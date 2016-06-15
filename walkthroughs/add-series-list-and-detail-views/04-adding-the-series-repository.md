
# Add Series List and Detail Views

## Adding the Series Repository

Now that we’ve centralized our data into our `Data` class, we’re ready to add our `SeriesRepository` class.

Right click on the “Data” folder, select the “Add > Class” menu item, set the class name to “SeriesRepository”, and press `ENTER`.

```
public class SeriesRepository
{
}
```

We can start by stubbing out the `GetSeries` and... `GetSeries` methods? Unfortunately the singular and plural versions of the word “series” are the same, so we can’t use the same naming convention that we used in the `ComicBookRepository` class.

Let’s go with `GetSeries`—to get an array of `Series` objects—and `GetSeriesDetail`—to get a single `Series` object. For now, just return `null` from both methods.

Don’t forget to add the `using` directive for the `ComicBookGallery.Models` namespace!

```
public Series[] GetSeries()
{
    return null;
}

public Series GetSeriesDetail()
{
    return null;
}
```

Okay—let’s work on implementing these methods. The `GetSeries` method is easy: just return the `Data.Series` property value.

```
public Series[] GetSeries()
{
    return Data.Series;
}
```

For the `GetSeriesDetail` method implementation, let’s open the `ComicBookRepository` class and review the `GetComicBook` method.

```
public ComicBook GetComicBook(int id)
{
    ComicBook comicBookToReturn = null;

    foreach (var comicBook in Data.ComicBooksData)
    {
        if (comicBook.Id == id)
        {
            comicBookToReturn = comicBook;

            // We found the comic book that we're looking for
            // so let's go ahead and short circuit the loop.
            break;
        }
    }

    return comicBookToReturn;
}
```

That’s right—we started with declaring a variable for our return value. Then we looped over the array of comic books, checking each to see if its `Id` property value matched our `id` argument. Let’s switch back to our `SeriesRepository` class and start implementing our method.

First, let’s add an `id` parameter—of type `int`. Then, declare a variable `seriesToReturn` for our return value, add a few blank lines, and return that variable. Now, let’s write our `foreach` loop—we can use `series` for the value variable name and the `Data.Series` property for the loop source.

```
public Series GetSeriesDetail(int id)
{
    Series seriesToReturn = null;

    foreach (var series in Data.Series)
    {
    }

    return seriesToReturn;
}
```

Inside of the loop, add an `if` statement that checks to see if the current series `Id` property value matches the `id` argument, and if so, assigns the `seriesToReturn` variable to the current series. Just after that, let’s add a `break` statement so that we exit the loop as soon as we find the series that we’re looking for.

```
public Series GetSeriesDetail(int id)
{
    Series seriesToReturn = null;

    foreach (var series in Data.Series)
    {
        if (series.Id == id)
        {
            seriesToReturn = series;
            break;
        }
    }

    return seriesToReturn;
}
```

Okay—our method is starting to shape up nicely, but there’s another requirement that need to work on. When retrieving a series, we need to populate the `Series.Issues` property with the comic books that belong to that series.

To start with, let’s add an `if` statement that checks to see if we found a `Series` object for the provided `id` argument value. This is important to do, as we don’t want to attempt to set the `Issues` property on the `seriesToReturn` object if it is null.

To find the comic books for a series, we’ll loop through the available comic books and check to see if the comic book has a series and its `Id` property equals our `id` argument value. When we find a matching comic book, we’ll add it to a local array variable.

Just inside of the `if` statement, declare a variable for our comic books array. Since we don’t know at this point how many comic books belong to this series, let’s set the size of the array to “0”. We’ll resize the array every time we find a matching comic book.

```
var comicBooks = new ComicBook[0];
```

Next, add a `foreach` loop—use `comicBook` for the value variable name and `Data.ComicBooks` for the loop source.

```
foreach (var comicBook in Data.ComicBooks)
{
}
```

Inside of the loop, let’s add an `if` statement that ensures that the `comicBook.Series` property is not null and if the `comicBook.Series.Id` property value equals the `id` argument value. Notice that I’m using the logical AND operator (see [https://msdn.microsoft.com/en-us/library/c6s3h5a7.aspx](https://msdn.microsoft.com/en-us/library/c6s3h5a7.aspx)). By using this operator, our second expression will only be evaluated if the first expression evaluates to “true”. This is important, because our second expression will throw an exception if the `comicBook.Series` property is null—that’s why we need to ensure that it’s not null in our first expression.

```
foreach (var comicBook in Data.ComicBooks)
{
    if (comicBook.Series != null && comicBook.Series.Id == id)
    {
    }
}
```

Inside of the `if` statement—when we find a matching comic book—we need to add an element to our `comicBooks` array and set that new element to the comic book that we just found. We can add an element to an array by using the static `Array.Resize` method (see [https://msdn.microsoft.com/en-us/library/bb348051(v=vs.110).aspx](https://msdn.microsoft.com/en-us/library/bb348051(v=vs.110).aspx)).

The `Array.Resize` method accepts two parameter values: the array to resize and size of the new array. The first parameter might look a little odd to you at first. When supplying that parameter, we need to include the `ref` keyword (see [https://msdn.microsoft.com/en-us/library/14akc2c7.aspx](https://msdn.microsoft.com/en-us/library/14akc2c7.aspx)) just before the parameter value.

The `ref` keyword causes the array to be passed by reference, not by value. Arguments by default are passed by value, which works in the vast majority of use cases.

To understand why using the `ref` keyword is necessary in this case, we need to understand how the `Array.Resize` method works internally. The `Resize` method creates a new array with the specified size, copies elements from the old array to the new one, and then replaces the old array with the new array. In order for the `Resize` method to be able to replace the array in our method, we need to pass the array by reference, not by value.

I realize that the concept of passing an argument by value or by reference can be a little difficult to understand at first. While you won’t often encounter situations where passing arguments by reference is necessary, it’s a concept that you’ll need to eventually understand. In later courses, we’ll continue to explore this topic, so don’t worry if things aren’t completely clear to you right now.

When calling `Array.Resize`, we need to provide the size of the new array. In this case, the new array needs to be one element larger than our current array. To get the size of an array, we can use the `Length` property. So, we can pass the result of adding “1” to the array’s `Length` property.

```
Array.Resize(ref comicBooks, comicBooks.Length + 1);
```

Next, we need to set the new array element to the current comic book. Again, we can use the `Length` property to determine the array index that we need to set.

Remember, arrays are what we refer to as being “zero” based, meaning that the first element in the array has an index of “0”, the second element has an index of “1”, and so on. So, to determine the index of the last element in the array—which is the element that we just added—we subtract “1” from the Length property value.

```
comicBooks[comicBooks.Length - 1] = comicBook;
```

Great! Now that we have our list of comic books, we just need to set the `Series.Issues` property value.

```
if (seriesToReturn != null)
{
    var comicBooks = new ComicBook[0];

    foreach (var comicBook in Data.ComicBooks)
    {
        if (comicBook.Series != null && comicBook.Series.Id == id)
        {
            Array.Resize(ref comicBooks, comicBooks.Length + 1);
            comicBooks[comicBooks.Length - 1] = comicBook;
        }
    }

    seriesToReturn.Issues = comicBooks;
}
```

And that completes our `GetSeriesDetail` method! Before we commit our changes, let’s build our project by pressing `CTRL+SHIFT+B`.

Visual Studio opens the Output window so that we can monitor the progress of the build and see the results upon its completion. You should see that one project succeeded and zero failed.

If you’re using GitHub, let’s commit our changes. Enter a commit message of “Added series repository” and click the “Commit” button.

In the next step, we’ll turn our attention to the Series List page.

[Return to Introduction](01-introduction.md)
