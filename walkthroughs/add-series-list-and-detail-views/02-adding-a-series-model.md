
# Add Series List and Detail Views

## Adding a Series Model

In this step, we’ll extend our data model to include a `Series` model. Let’s start by taking a look at the relationship that exists between our `ComicBook` and `Series` models.

Each of our comic books will be associated with a single series, but each series can be associated with one or more comic books. For example “The Amazing Spider-Man #700” comic book is part of the “The Amazing Spider-Man” comic book series and the “The Amazing Spider-Man” comic book series is associated with two of our comics, issues #657 and #700. The relationship between the `Series` and `ComicBook` data models is called a “one-to-many” or “1:many” relationship.

The one-to-many relationship is just one of the available relationships that we can use when data modeling. In future Treehouse courses, we’ll take a closer look at data relationships.

Let’s add our new `Series` model.

Right click on the “Models” folder, select the “Add > Class” menu item, set the class name to “Series”, and press `ENTER`.

```
public class Series
{
}
```

For the model attributes, let’s add properties to our class for `Id`, of type `int`, and `Title` and `DescriptionHtml`, both of type `string`.

```
public class Series
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string DescriptionHtml { get; set; }
}
```

As we just discussed, each series can be associated with one or more comic books, so we need a property for those comic book references. Let’s add an `Issues` property that will be an array of `ComicBook` objects.

```
public class Series
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string DescriptionHtml { get; set; }
    public ComicBook[] Issues { get; set; }
}
```

Now that we have our `Series` model, let’s turn our attention to the `ComicBook` model. To start with, we can replace the `SeriesTitle` property with a `Series` property, that is of type `Series`.

```
public class ComicBook
{
    public int Id { get; set; }
    public Series Series { get; set; }
    public int IssueNumber { get; set; }
    public string DescriptionHtml { get; set; }
    public Artist[] Artists { get; set; }
    public bool Favorite { get; set; }

    public string DisplayText
    {
        get
        {
            return SeriesTitle + " #" + IssueNumber;
        }
    }

    public string CoverImageFileName
    {
        get
        {
            return SeriesTitle.Replace(" ", "-").ToLower() + 
                "-" + IssueNumber + ".jpg";
        }
    }
}
```

As soon as we made that change, Visual Studio added a red squiggle underneath the `SeriesTitle` property references in our readonly `DisplayText` and `CoverImageFileName` property getters, to politely remind us that the `SeriesTitle` property doesn’t exist anymore.

To resolve the issue, we could easily just replace `SeriesTitle` with the `Series.Title` property.

```
public string DisplayText
{
    get
    {
        return Series.Title + " #" + IssueNumber;
    }
}

public string CoverImageFileName
{
    get
    {
        return Series.Title.Replace(" ", "-").ToLower() + 
            "-" + IssueNumber + ".jpg";
    }
}
```

This will work, under ideal circumstances, but it has a notable flaw: if the `Series` property is null—meaning that it doesn’t have a value—then we’ll receive an exception when trying to get the `Title` property value.

To prevent this error, we need to ensure that the `Series` property is not null, *before* trying to access any properties on the `Series` object. Sounds like we need an `if` statement!

Let’s start by capturing the value of the `Series` property into a `series` variable, then use an `if` statement to check if the `series` variable is not null. If the `series` variable is not null, then we can return the original statement, else we can return null. In the return statement, be sure to replace the `Series` property with our `series` variable.

```
public string DisplayText
{
    get
    {
        var series = Series;
        if (series != null)
        {
            return series.Title + " #" + IssueNumber;
        }
        else
        {
            return null;
        }
    }
}
```

We can use the same approach to improve our `CoverImageFileName` property. Add the `series` variable, the `if` statement, copy and paste the original statement inside of the `if` statement, and return `null` in the `else` block.

```
public string CoverImageFileName
{
    get
    {
        var series = Series;
        if (series != null)
        {
            return series.Title.Replace(" ", "-").ToLower() + 
                "-" + IssueNumber + ".jpg";
        }
        else
        {
            return null;
        }
    }
}
```

Before we go any further, let’s build our project. Press `CTRL+SHIFT+B` to build the solution.

Visual Studio opens the Output window so that we can monitor the progress of the build and see the results upon its completion. Oops! Looks like we have three errors, all with the same description.

```
'ComicBook' does not contain a definition for 'SeriesTitle'
```

If we look here on the right hand side of the Error List window, we can also see that all three errors are in the `ComicBookRepository.cs` file. Double click the first error to navigate to the offending line of code.

Now it’s easy to see what the problem is: we’re still trying to set the `ComicBook.SeriesTitle` property in our repository.

```
...
SeriesTitle = "The Amazing Spider-Man",
...
```

We can fix this issue by changing `SeriesTitle` to `Series`, and replacing the string literal “The Amazing Spider-Man” with instantiating an inline `Series` model object. We can then use object initializer syntax to set the `Series.Title` property to our original string literal “The Amazing Spider-Man”.

```
...
Series = new Series()
{
    Title = "The Amazing Spider-Man"
},
...
```

Copy and paste this change down to our other comic books, changing “The Amazing Spider-Man” to “Bone” for the last comic book.

Let’s press press `CTRL+SHIFT+B` to try to build our solution again. This time the build succeeds!

We fixed our error, but the solution that we used is not ideal. First, we don’t have an easy way to get a list of the available series, which we’ll need for our Series List view. Second, we’re using two different `Series` object instances for our first two comics, when they could be referencing the same `Series` object instance. In the next video, we’ll take a closer look at our design and improve it.

Now that we’ve verified that our project is successfully building, let’s update our Comic Book Detail view.

When we were updating our `ComicBook` model readonly properties, I remembered that our detail view has a reference to our deprecated `SeriesTitle` property.

```
<h5><label>Series Title:</label> @Model.SeriesTitle</h5>
```

Just like with our readonly properties, we can fix this issue by replacing `SeriesTitle` with `Series.Title`.

```
<h5><label>Series Title:</label> @Model.Series.Title</h5>
```

But again, just like with our readonly properties, if the `Series` property is null, this code will throw an exception. If we wrap this code in parentheses, we can use the ternary operator to ensure that the `Series` property is not null before we access it. If it is null, then let’s render the string literal “N/A”.

```
<h5><label>Series Title:</label> @(Model.Series != null ? 
    Model.Series.Title : "N/A")</h5>
```

Go ahead and save the view and start the website to test our changes.

Visual Studio starts by displaying the view that we have open in the editor, which in this case, isn’t very helpful. Remove the path in the URL to browse to the root of our website at `http://localhost:50153/`.

Here’s our list page, looking like it was before. Click on the first comic book to view the detail page. This looks okay too. Nice job with refactoring our website to use our new `Series` model!

Go ahead and stop the website. 

If you’re using GitHub, let’s commit our changes. Enter a commit message of “Added Series model” and click the “Commit All” button.

In the next step, let’s prepare for adding our `SeriesRepository` class by revisiting where our data lives.

[Return to Introduction](01-introduction.md)
