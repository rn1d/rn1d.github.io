---
layout: post
title:  "Overflows can be elusive"
date:   2014-05-21 22:15:00
categories: code
excerpt: Recently while debugging some .NET code I encountered an interesting oddity - while processing some data from a text file exported from a SQL Server database, I was getting negative values for fields that shouldn't have been negative. 
---
Recently while debugging some .NET code I encountered an interesting oddity - while processing some data from a text file exported from a SQL Server database, I was getting negative values for fields that shouldn't have been negative. The data in the database all came from one source, and in this source the particular fields were defined as unsigned integers.

{% highlight c# %}
    public class AppMessage {
        public int Id { get; set; }
        // fields ...
        public uint Data1 { get; set; }
        public uint Data2 { get; set; }
        // more fields
    }
{% endhighlight %}

So where could have the negative values come from? 

As mentioned earlier, the DBMS in question is SQL Server, which doesn't have an unsigned integer data type. This table is defined as follows:

{% highlight sql %}
    CREATE TABLE [dbo].[AppMessage] (
        [Id] int PRIMARY KEY CLUSTERED,
        -- fields,
        [Data1] int NULL,
        [Data2] int NULL,
        -- more fields
    )
{% endhighlight %}

It might be obvious already, but it took a moment before it hit me - the culprit is integer overflows.

## Integer overflows

Overflows, i.e. the [integer overflow](http://en.wikipedia.org/wiki/Integer_overflow) kind, are not exactly a mysterious thing. You try to assign a value to a variable that is out of range of its' data type you get a result that is not what mathematics says it should be. Let's say you add 4321 to 30000 and try to assign it to a 16 bit integer, instead of 34321, you get -31215. Again, this is not very surprising, and is sometimes even desired. 

One of my favourite books on .NET, [CLR via C#](http://www.amazon.com/CLR-via-Edition-Developer-Reference/dp/0735667454) by J. Richter (a book I highly recommend), devotes a section to it, warns to be wary and gives general guidelines on how to keep yourself out of trouble, but naturally doesn't go into detail on every possible case.

For many people, myself included, overflows are not something that you have to think about every day. Also, .NET is platform that places much emphasis on safety. And, I must admit, .NET has spoiled me with all kinds of safety features, for example, array bound checking - I have a feeling that .NET will protect me from such errors.

And the fact is that the C# compiler *does* provide some checks for some cases of unintended overflow.

Let's look at some examples:

	{% highlight c# %}
    // COMPILE ERROR: Cannot implicitly convert type 'uint' to 'int'. 
    // An explicit conversion exists (are you missing a cast?)
    //// int i0 = uint.MaxValue; 
    //// Console.WriteLine(i0);

    // COMPILE ERROR: Constant value '4294967295' cannot be converted to a 'int' (use 'unchecked' syntax to override)
    //// int i1 = (int)uint.MaxValue; 
    //// Console.WriteLine(i1);
         
    // COMPILE ERROR: Constant value '4294967294' cannot be converted to a 'int' (use 'unchecked' syntax to override)
    //// int i2 = (int)(uint.MaxValue - 1); 
    //// Console.WriteLine(i2);
    
    // No error - silent overflow
    uint ui3b = 0;
    int i3 = (int)(uint.MaxValue - ui3b);
    Console.WriteLine(i3); // prints '-1'

    // COMPILE ERROR: Cannot implicitly convert type 'uint' to 'int'. 
    // An explicit conversion exists (are you missing a cast?)
    //// uint ui4 = uint.MaxValue;
    //// int i4 = ui4; 
    //// Console.WriteLine(i4);
    
    uint ui5 = uint.MaxValue;
    int i5 = (int)ui5;  // overflows
    Console.WriteLine(i5); // prints '-1'
	{% endhighlight %}

It seems that the C# compiler will detect an overflow and output a compile error whenever the expression can be evaluated at compile time. However, when there's an expression with a variable involved, no errors are generated.

You might notice that even in the cases when values overflow, the user still has to be explicit and provide an explicit cast. So how can this go unnoticed? Take the following example.
    
	{% highlight c# %}
    table.Insert(
        evt.Id,
        // ...
        (int?)evt.Data1,
        (int?)evt.Data2,
        // ...
	{% endhighlight %}

In my case .Insert(...) is a generated method, and it gets generated from the SQL Server types. Without the cast to `int?` the code would not compile. Apparently, the programmer who wrote this assumed the conversion to be safe, as `uint` 'can hold only non-negative values', while `int` 'can hold negative as well'. In the end it comes down to programmer error.

In our case this problem had existed and gone unnoticed for several years, and the database contained several hundred million records with this problem.

## Cleaning up

Luckily, no data was ever lost - the signed integer contains as much bits as its' unsigned counterpart, the values can be reinterpreted/converted back to the original value.

If you need to do this in C#, by using the [unchecked](http://msdn.microsoft.com/en-us/library/a569z7k8.aspx) keyword the conversion can be as simple as this:

	{% highlight c# %}
    int signed = -1;
    unchecked
    {
        var unsigned = (uint) signed;
        Console.WriteLine(unsigned);
    }

    uint unsigned2 = unchecked((uint)signed);
    Console.WriteLine(unsigned2);
	{% endhighlight %}

If you're in SQL Server, this can be done by a temporary conversion to BIGINT and some [modular arithmetic](http://en.wikipedia.org/wiki/Modular_arithmetic) (or possibly some other clever trick).

Of course, one might say that the correct way to deal with this is to change to column data type to BIGINT and update all code. If you have an non-trivial amount of data, say several hundred million records, and an active database, this may take a while. In addition to that, the savings in space may matter. In fact, storing the value as INT is the most efficient storage data type. In our case, we decided to leave the data in the INT columns and change data retrieval logic to convert the data dynamically as needed.

## Ways to protect yourself

There are a couple of ways to catch such errors early. 

One way - be disciplined and explicitly use the [`checked`](http://msdn.microsoft.com/en-us/library/74b4xzyw.aspx) and [`unchecked`](http://msdn.microsoft.com/en-us/library/a569z7k8.aspx) keywords when an overflow might occur. In addition to making the program safer, the intent is more clear. But this may be impractical in case of existing code bases.

Another way - use the [`/checked` compiler option](http://msdn.microsoft.com/en-us/library/h25wtyxf.aspx) - this sets the default behaviour outside of `checked`/`unchecked` blocks. 

One thing to keep in mind is that `checked` integer arithmetic may negatively impact performance. In CLR via C#, J. Richter suggests turning on `checked` mode by default in `Debug` builds and leaving it off for `Release` builds. That way you get the best of both worlds - catch errors more easily while debugging and testing, and run at full speed when in production.