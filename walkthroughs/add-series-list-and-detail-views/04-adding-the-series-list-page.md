
# Add Series List and Detail Views

## Adding the Series List Page

Now that we've updated our models, centralized our data, and created our repository, we're ready to add the controller and view for our series list page. Let's start by adding the `SeriesController` class.

Instead of adding a class using the "Add > Class" menu item, let's right click on the "Controllers" folder and select the "Add > Controller" menu item.

Visual Studio will open the "Add Scaffold" dialog and present us with a list of the available controller templates. Select the "MVC 5 Controller - Empty" template and click the "Add" button. Next, we'll be prompted by the "Add Controller" dialog for the name of the controller. Replace the "Default" text with "Series" and press `ENTER`. Remember, MVC controllers need to end with the suffix "Controller", so it's important to not remove the "Controller" text when naming your controller.

Here's our controller.

```
public class SeriesController : Controller
{
    // GET: Series
    public ActionResult Index()
    {
        return View();
    }
}
```

By using scaffolding to add our controller, Visual Studio has saved us a couple of steps by inheriting from the MVC `Controller` base class and adding the `Index` action method. The `// GET: Series` code comment doesn't add much value, so I'll remove it in order to keep our code clean.

Next, let's add `_seriesRepository` private field of type `SeriesRepository` and a default constructor that instantiates the repository. Don't forget to add the `using` directive for the `ComicBookGallery.Data` namespace.

```
public class SeriesController : Controller
{
    private SeriesRepository _seriesRepository = null;

    public SeriesController()
    {
        _seriesRepository = new SeriesRepository();
    }

    public ActionResult Index()
    {
        return View();
    }
}
```

Update the `Index` method to make a call into the `_seriesRepository.GetSeries` method to get the available series. Store the result of the `GetSeries` method call into a `series` variable so we can pass the result into the `View` method call.

```
public ActionResult Index()
{
    var series = _seriesRepository.GetSeries();

    return View(series);
}
```

That's it for our controller—at least for now. Let's turn our attention to the view. Right click on the `Index` method's `View` method call and select the "Add View" menu item. Visual Studio will present us with the "Add View" dialog.

The "View name" field should default to "Index", which follows the convention of the view name matching the action name. Select the "Empty (without model)" template (if it's not already selected). Make sure that the "Create as a partial view" check box is not checked and the "Use a layout page" check box is checked. Click the "Add" button to finishing adding the view.

Here's our view.

```
@{
    ViewBag.Title = "Index";
}

<h2>Index</h2>
```

Let's start by adding a `model` view directive to the top of the view in order to make it strongly typed. For the type, provide the fully qualified name for the `Series` data model. Also, remember that the controller is passing an array of `Series` objects to our view, so be sure to include a set of brackets `[]` at the end of the model type to indicate that we're expecting an array of that type.

```
@model ComicBookGallery.Models.Series[]
```

Next, set the `ViewBag.Title` property to the string literal "Series". Then, update the `<h2>` element content to display the `ViewBag.Title` property value.

```
@model ComicBookGallery.Models.Series[]

@{
    ViewBag.Title = "Series";
}

<h2>@ViewBag.Title</h2>
```

Now we're ready to add the markup for our series list. Here's the HTML that our designer provided to us for this view.

```
<div class="row">
    <div class="col-md-12">
        <ul class="series-list">
            <li>
                <h4><a href="/Series/Detail/1">The Amazing Spider-Man</a></h4>
            </li>
            ...
        </ul>
    </div>
</div>
```

That looks simple enough! Looks like we need to add a `foreach` loop inside of the `<ul>` element to loop over the available `Series` objects. For now let's add the `foreach` loop just above the `<li>` element instead of replacing it, so that we can reference it as we construct our loop. Name the loop value variable `series` and set the loop source to the view's `Model` property.

```
<div class="row">
    <div class="col-md-12">
        <ul class="series-list">
            @foreach (var series in Model)
            {
            }
            <li>
                <h4><a href="/Series/Detail/1">The Amazing Spider-Man</a></h4>
            </li>
        </ul>
    </div>
</div>
``` 

Inside of the loop, start by adding an `<li>` element followed by an `<h4>` element.

```
@foreach (var series in Model)
{
    <li>
        <h4></h4>
    </li>
}
```

For the `<h4>` element content, we need to render the series title as a hyperlink to our currently non-existent series detail page (don't worry, we'll work on the series detail page in the next step!)

To create the hyperlink, we can use the `ActionLink` HTML helper method, passing the `series.Title` property value for the `linkText` parameter and the string literal "Detail" for the `actionName` parameter. Just like we did when linking from our comic books list page to the comic books detail page, we need to pass an anonymous object for the `routeValues` parameter that has as `id` property set to the `series.Id` property value.

```
<h4>@Html.ActionLink(series.Title, "Detail", new { id = series.Id })</h4>
```

Don't forget to remove the `<li>` element below our `foreach` loop. And that finishes our series list view!

```
@model ComicBookGallery.Models.Series[]

@{
    ViewBag.Title = "Series";
}

<h2>@ViewBag.Title</h2>

<div class="row">
    <div class="col-md-12">
        <ul class="series-list">
            @foreach (var series in Model)
            {
                <li>
                    <h4>@Html.ActionLink(@series.Title, "Detail", new { id = series.Id })</h4>
                </li>
            }
        </ul>
    </div>
</div>
```

Before we run and test our website, let's add a couple of menu items to the layout page, so users have a way to browse between the comic books and series list pages.

Open the "_Layout.cshtml" file in the "Views/Shared" folder. Scroll down to the line that contains the code comment `<!-- Add menu items here -->`. Replace that code comment with two `<li>` elements.

```
<div class="navbar-collapse collapse">
    <ul class="nav navbar-nav">
        <li></li>
        <li></li>
    </ul>
</div>
```

Then, in the first `<li>` element, add a call to the `ActionLink` HTML helper method. We'll need to use the 4th overload in the list of method overloads, as it accepts a `controllerName` parameter along with the `linkText` and `actionName` parameters. Supplying the controller name is necessary as our layout page will be used across controllers, so we can't rely upon the ambient or current controller name. So, our values will be the string literals "Comic Books", "Index", and "ComicBooks" respectively for the `linkText`, `actionName`, and `controllerName` parameters.

```
<li>@Html.ActionLink("Comic Books", "Index", "ComicBooks")</li>
```

For the second `<li>` element, we'll do the same thing, only this time we'll supply the string literals "Series", "Index", and "Series" for the parameter values.

```
<li>@Html.ActionLink("Series", "Index", "Series")</li>
```

Let's test our changes! Save all of the open files and press `F5` to run our website.

You should first see the comic books list page. The list of comic books should look as it did before, as we didn't make any changes to it. Notice at the top of the page that we now have two menu items: "Comic Books" and "Series". Click on the "Series" menu item to browse to the "Series" list page. And here's our new series list page!

There's no need to test the links to the series detail page yet, as we haven't built it yet. Go ahead and close Chrome and stop the website.

If you're using GitHub, let's commit our changes. Enter a commit message of "Added Series List view" and click the "Commit All" button.

In the [next step](05-adding-the-series-detail-page.md), we'll add the series detail page.

[Return to Introduction](README.md)
