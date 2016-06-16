
# Add Series List and Detail Views

## Centralizing Our Data

We're about to add a second repository to our project: the `SeriesRepository` class. Currently, our website's data resides inside of our `ComicBookRepository` class. The `SeriesRepository` class is going to need access too, so we need to centralize our data into its own class.

Right click on the "Data" folder, select the "Add > Class" menu item, set the class name to "Data", and press `ENTER`.

```
public class Data
{
}
```

Switch back to the `ComicBookRepository` class, select the `_comicBooks` private field—including all of the related initialization code—and cut it to the clipboard. Visual Studio will immediately let us know that the `_comicBooks` variable references in the `ComicBookRepository` class are broken. It's okay to leave them alone for now; we'll come back in a bit and fix them.

Switch to the `Data` class, and paste the contents of the clipboard inside of our class. All of our model references are broken, which can be fixed by adding a `using` directive to the `ComicBookGallery.Models` namespace.

```
public class Data
{
    private static ComicBook[] _comicBooks = new ComicBook[]
    {
        new ComicBook()
        {
            ...
        },
        new ComicBook()
        {
            ...
        },
        new ComicBook()
        {
            ...
        }
    };
}
```

As I mentioned in the previous step, we want to make it easier to get a list of the available series. We also want to reuse `Series` object instances instead of creating new instances for each `ComicBook` as we are currently doing.

To accomplish these goals, let's refactor our code so that we are creating our `Series` model objects separately from our `ComicBook` model objects. Unfortunately, we can't easily do that with our current approach of initializing the comic book data inline with the `_comicBooks` variable declaration. We'll need to put our data initialization code into its own method.

Let's add a private `InitData` method to our class. We can make this method private because it's an internal implementation detail of the `Data` class and we don't want the rest of our website to know about it or have access to it.

```
private void InitData()
{
}
```

Now let's move our comic book data into this method. This time, we don't want the entire private field, so start the selection just after the `_comicBooks` field name, and cut everything to the right and down—just up to the ending semicolon—to the clipboard. That leaves us with just a bit of code—at least for now.

```
public class Data
{
    private static ComicBook[] _comicBooks;

    private void InitData()
    {
    }
}
```

Then add a `comicBooks` variable inside of the `InitData` method and paste the contents of the clipboard to the right of the variable name. Don't forget to add a semicolon to the end of the statement!

```
private void InitData()
{
    var comicBooks = new ComicBook[]
    {
        new ComicBook()
        {
            ...
        },
        new ComicBook()
        {
            ...
        },
        new ComicBook()
        {
            ...
        }
    };
}
```

Next, add a `series` variable for the series data just above the `comicBooks` variable. It will hold an array of `Series` objects.

```
var series = new Series[]
{
};
```

Now we can add our series data code.

```
var series = new Series[]
{
    new Series()
    {
        Id = 1,
        Title = "The Amazing Spider-Man",
        DescriptionHtml = "The Amazing Spider-Man (abbreviated as ASM) is an American comic book series published by Marvel Comics, featuring the adventures of the fictional superhero Spider-Man. Being the mainstream continuity of the franchise, it began publication in 1963 as a monthly periodical and was published continuously, with a brief interruption in 1995, until its relaunch with a new numbering order in 1999. In 2003 the series reverted to the numbering order of the first volume. The title has occasionally been published biweekly, and was published three times a month from 2008 to 2010. A film named after the comic was released July 3, 2012."
    },
    new Series()
    {
        Id = 2,
        Title = "Bone",
        DescriptionHtml = "Bone is an independently published comic book series, written and illustrated by Jeff Smith, originally serialized in 55 irregularly released issues from 1991 to 2004."
    }
};
```

With our series data now in place, we can update the comic books data to use the `Series` object instances. In the first comic book, go ahead and remove the code that is instantiating the `Series` object. Then we can set the `Series` property to the first element in the `series` array.

We can index into the array by typing the `series` variable name followed by a set of brackets containing the numeral "0". Remember, array elements are numbered starting with zero, so the first element is index "0", the second is index "1", and so on.

The second comic book `Series` property also needs to be set to the `series` array's first element. The third comic book `Series` property needs to be set to the second element, so use the numeral "1" in the brackets following the `series` variable.

```
var comicBooks = new ComicBook[]
{
    new ComicBook()
    {
        Id = 1,
        Series = series[0],
        ...
    },
    new ComicBook()
    {
        Id = 2,
        Series = series[0],
        ...
    },
    new ComicBook()
    {
        Id = 3,
        Series = series[1],
        ...
    }
};
```

Nice! Now we have our series data separated from our comic books data and the `Series` object instances are not unnecessarily duplicated across our `ComicBook` objects.

Let's turn our attention to how we're going to expose our data outside of this class.

We currently have a private `_comicBooks` field. To start with, let's make this field visible to the rest of our website by changing the `private` access modifier to `public`. Now that our field is no longer private, let's change the name to `ComicBooks`. We want to ensure that we're only starting field names with an underscore and lowercase letter if they are private fields.

```
public static ComicBook[] ComicBooks;
```

We *could* leave this line of code as it is, but let's make one more improvement by converting the field to a property by adding a set of curly braces containing the `get` and `set` keywords. As you might have seen in a previous C# course, this is the syntax for implementing auto-properties.

```
public static ComicBook[] ComicBooks { get; set; }
```

If we add a private access modifier just before the set keyword, we can allow code outside of this class to get the property value, without being able to set the value. In general, we only want to provide the access that is necessary.

```
public static ComicBook[] ComicBooks { get; private set; }
```

While we're here, let's add a property for our series data. This property can also be `public` and `static`. Set its type to be an array of `Series` objects, use "Series" for the name, and add a public getter and a private setter.

```
public static Series[] Series { get; private set; }
```

Now that we have our data properties, we need to update the `InitData` method to set their values. At the bottom of the method, set the `ComicBooks` property to the `comicBooks` variable and the `Series` property to the `series` variable.

```
Series = series;
ComicBooks = comicBooks;
```

We're getting close to being done with our `Data` class, but our implementation has a major shortcoming right now: we're initializing our data in the `InitData` method but we're currently not calling that method.

Ideally, we only want to call the `InitData` method once. We can accomplish this by adding a static constructor to our class that calls the `InitData` method. Static constructors (see [https://msdn.microsoft.com/en-us/library/k9x6w0hc.aspx](https://msdn.microsoft.com/en-us/library/k9x6w0hc.aspx)) are called just before any instances of our class are created or any static members are accessed. They specifically exist to initialize class members, which fits our needs exactly.

```
static Data()
{
    InitData();
}
```

Visual Studio is complaining about our `InitData` method call. We're getting this error because we're attempting to call an instance method from a static method. This is just an oversight on our part—we can easily make the `InitData` method static so that it can be called from our static constructor. Just add the `static` keyword right before the return type of the `InitData` method.

```
private static void InitData()
{
    ...
}
```

One last thing before we move on: all four of our `Data` class members are static. When a class just contains static members, we can make the class itself static by adding the `static` keyword just before the `class` keyword.

Doing this adds additional clarity to our code and prevents our class from being instantiated—which wouldn't make any sense given that our class doesn't contain any instance members. It also prevents us from adding any instance members to our class, so it helps ensure that we keep our `Data` class design consistent.

```
public static class Data
{
    ...
}
```

Just like we did with the `ComicBookRepository` class, by making our data class static, we're ensuring that we only have a single copy of our data in memory at any given time.

Now that we're *finally* done with our `Data` class, let's switch to the `ComicBookRepository` class and fix the broken references to the non-existent `_comicBooks` field.

We just need to replace each broken field reference with a reference to the our new `Data.ComicBooks` property. Notice that since the `Data.ComicBooks` property is static, we can access it with just the class name—no object instance required.

```
public class ComicBookRepository
{
    public ComicBook[] GetComicBooks()
    {
        return Data.ComicBooks;
    }

    public ComicBook GetComicBook(int id)
    {
        ComicBook comicBookToReturn = null;

        foreach (var comicBook in Data.ComicBooks)
        {
            ...
        }

        return comicBookToReturn;
    }
}
```

Before we test our changes, let's set two breakpoints: one inside of the constructor for the  `ComicBooksController` class and another inside of the static constructor for the `Data` class. By setting those breakpoints, we can see how often these constructors get called.

Go ahead and start the website.

The first breakpoint we hit is the constructor for the `ComicBooksController` class. This is MVC instantiating our controller in order to process the request for our "Home" page. Press `F5` to continue execution of our website.

Next, we hit the breakpoint inside of the `Data` class static constructor. If we press `SHIFT+F11`, we can step out of the constructor and see what caused it to be called—the `ComicBooks` property getter. Press `SHIFT+F11` again to step out of the property getter. Now we're in the `ComicBookRepository.GetComicBooks` method. If we press `SHIFT+F11` one more time, we'll step out to the `ComicBooksController.Index` action method.

All of this makes sense. MVC routed the request for our "Home" page to the controller's `Index` action method, which in turn called the repository's `GetComicBooks` method, which accessed the `Data.ComicBooks` static property, which caused the `Data.InitData` method to get called.

Press `F5` to continue execution. You should now see the list page with everything intact.

Click on the first comic book to browse to the Comic Book Detail page. And again, we hit the breakpoint in the `ComicBooksController` class constructor. This tells us that MVC instantiates a controller instance for each new request.

Press `F5` to continue execution. You should now see the detail page.

Notice that we didn't hit our breakpoint in the `Data` class static constructor. This confirms that the static constructor will only get called once.

Go ahead and stop the website. 

If you're using GitHub, let's commit our changes. Enter a commit message of "Added Data class" and click the "Commit All" button.

In the [next step](03-adding-the-series-repository.md), we'll add the series repository.

[Return to Introduction](README.md)
