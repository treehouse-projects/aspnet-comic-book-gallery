
# Add Series List and Detail Views

## Adding the Series Detail Page

In this step, we'll build out our Series Detail page. The Series Detail page will not only display the series title and description, but also the list of comic books that belong to that series.

Let's start by stubbing out the `Detail` action method in the `SeriesController` class.

```
public ActionResult Detail()
{
    return View();
}
```

Before we go any further, let's review how we implemented the comic books controller's `Detail` action method.

```
public ActionResult Detail(int? id)
{
    if (id == null)
    {
        return HttpNotFound();
    }

    var comicBook = _comicBookRepository.GetComicBook((int)id);

    return View(comicBook);
}
```

If we follow that example, we have four changes to make:

* Add an `id` parameter of type `int?` (remember that the `?` after the `int` type makes that data type nullable)
* Add an `if` statement that checks if the `id` parameter is null, and if so returns a call to the `HttpNotFound` method
* Make a call to the `_seriesRepository.GetSeriesDetail` method
* Pass the repository method result into the `View` method call

Here's our updated `Detail` action method.

```
public ActionResult Detail(int? id)
{
    if (id == null)
    {
        return HttpNotFound();
    }

    var series = _seriesRepository.GetSeriesDetail((int)id);

    return View(series);
}
```

Now let's turn our attention to the view. Right click on the `Detail` method's `View` method call and select the "Add View" menu item. Visual Studio will present us with the "Add View" dialog.

The "View name" field should default to "Detail", which follows the convention of the view name matching the action name. Select the "Empty (without model)" template (if it's not already selected). Make sure that the "Create as a partial view" check box is not checked and the "Use a layout page" check box is checked. Click the "Add" button to finishing adding the view.

Here's our view.

```
@{
    ViewBag.Title = "Detail";
}

<h2>Detail</h2>
```

Let's start by adding a `model` view directive to the top of the view. For the type, provide the fully qualified name for the `Series` data model.

```
@model ComicBookGallery.Models.Series
```

Next, set the `ViewBag.Title` property to the `Model.Title` property value. Then, update the `<h2>` element content to display the `ViewBag.Title` property value. We also want to display the "Return to List" button on this page, so let's set the `ViewBag.ShowBackBar` property to `true`.

```
@model ComicBookGallery.Models.Series

@{
    ViewBag.Title = Model.Title;
    ViewBag.ShowBackBar = true;
}

<h2>@ViewBag.Title</h2>
```

Now we're ready to add the series detail markup to our view. Here's the HTML that our designer provided.

```
<div class="well">
    <h5>Description</h5>
    <div>
        <p>The Amazing Spider-Man (abbreviated as ASM) is an American comic book series published by Marvel Comics, featuring the adventures of the fictional superhero Spider-Man. Being the mainstream continuity of the franchise, it began publication in 1963 as a monthly periodical and was published continuously, with a brief interruption in 1995, until its relaunch with a new numbering order in 1999. In 2003 the series reverted to the numbering order of the first volume. The title has occasionally been published biweekly, and was published three times a month from 2008 to 2010. A film named after the comic was released July 3, 2012.</p>
    </div>
</div>

<h4>Comic Books Available in This Series</h4>

<div class="row">
    <div class="col-md-3">
        <h4><a href="/ComicBooks/Detail/1">The Amazing Spider-Man #700</a></h4>
        <a href="/ComicBooks/Detail/1">
            <img src="/Images/the-amazing-spider-man-700.jpg" 
                alt="The Amazing Spider-Man #700" class="img-responsive" />
        </a>
    </div>
    ...
</div>
```

Let's select, copy, and paste this HTML into our view and incrementally update it to make it dynamic.

To start with, we can replace the series description with the `Model.DescriptionHtml` property value. Just like we did on the Comic Book Detail view, we need to use the `Raw` HTML helper method to render the property value so that MVC won't encode any of the HTML tags in the description.

```
<div class="well">
    <h5>Description</h5>
    <div>
        @Html.Raw(Model.DescriptionHtml)
    </div>
</div>
```

Next, we need to update the list of comic books. The comic books list HTML looks familiar. Let's compare it to the markup in the "Views/ComicBooks/Index.cshtml" file.

```
<div class="row">
    @foreach (var comicBook in Model)
    {
        <div class="col-md-3">
            <h4>@Html.ActionLink(comicBook.DisplayText, "Detail", 
               new { id = comicBook.Id })</h4>
            <a href="@Url.Action("Detail", new { id = comicBook.Id })">
                <img src="/Images/@comicBook.CoverImageFileName"
                     alt="@comicBook.DisplayText"
                     class="img-responsive" />
            </a>
        </div>
    }
</div>
```

Yes, it's the same! Given that, we can copy and paste this markup from the "Views/ComicBooks/Index.cshtml" file to our Series Detail view. After pasting the markup into the view, you'll notice that Visual Studio will complain about the `foreach` loop source, which is currently set to the `Model` property.

```
foreach statement cannot operate on variables of type 'ComicBookGallery.Models.Series' because 'ComicBookGallery.Models.Series' does not contain a public definition for 'GetEnumerator'
```

In the Comic Books List view, the `Model` property was an array of `ComicBook` objects, whereas here in our Series Detail view, the `Model` property is an instance of the `Series` data model. We can't enumerate a `Series` object, but we can enumerate the `Series.Issues` array of `ComicBook` objects. That's an easy change to make, just change `Model` to `Model.Issues`.

```
<div class="row">
    @foreach (var comicBook in Model.Issues)
    {
        <div class="col-md-3">
            <h4>
                @Html.ActionLink(comicBook.DisplayText, "Detail",
                    new { id = comicBook.Id })
            </h4>
            <a href="@Url.Action("Detail", new { id = comicBook.Id })">
                <img src="/Images/@comicBook.CoverImageFileName"
                    alt="@comicBook.DisplayText"
                    class="img-responsive" />
            </a>
        </div>
    }
</div>
```

With that error resolved, let's run our website by pressing `F5`. If you started the website from the Series Detail view, you'll initially get a `404` error. Go ahead and browse to the URL `http://localhost:50153/Series`.

Here's our Series List page. Click on a series title to browse to the Series Detail page. And here's our Series Detail page! Looks good so far. Let's try clicking on one of the series' comic books. Clicking on the first comic book left us on the same page. If we click on the second comic book we're taken to the Series Detail page for our second series "Bone".

If you hover your mouse pointer over the comic book cover, you'll notice (in the lower left hand corner) that the cover's hyperlink is `/Series/Detail/3`. That's not correct; it should be `/ComicBooks/Detail/3`.

Let's review the `Html.ActionLink` and `Url.Action` method calls in the Series Detail view.

```
<div class="col-md-3">
    <h4>
        @Html.ActionLink(comicBook.DisplayText, "Detail",
            new { id = comicBook.Id })
    </h4>
    <a href="@Url.Action("Detail", new { id = comicBook.Id })">
        <img src="/Images/@comicBook.CoverImageFileName"
            alt="@comicBook.DisplayText"
            class="img-responsive" />
    </a>
</div>
```

Do you see it? We're supplying the action name but not the controller name, so the current controller's name, "Series", will get used by default. We need to switch to a method overload that will allow us to pass the controller name.

Be careful which overload you are calling! It's easy to think you're calling the correct method overload but actually be targeting the wrong one. For the `Html.ActionLink` method, it's the 7th method overload that accepts `linkText`, `actionName`, `controllerName`, `routeValues`, and `htmlAttributes` parameter values. For the `Url.Action` method, it's the 6th method overload that accepts `actionName`, `controllerName`, and `routeValues` parameter values.

```
<div class="col-md-3">
    <h4>
        @Html.ActionLink(comicBook.DisplayText, "Detail", "ComicBooks",
            new { id = comicBook.Id }, null)
    </h4>
    <a href="@Url.Action("Detail", "ComicBooks", new { id = comicBook.Id })">
        <img src="/Images/@comicBook.CoverImageFileName"
              alt="@comicBook.DisplayText"
              class="img-responsive" />
    </a>
</div>
```

Let's test our changes by pressing `F5` to run our website. When the browser finishes loading, browse to the Series List page. Click on the first series title to browse to the Series Detail page, and then click on the first comic book in the list. Excellent! We've fixed our comic book hyperlinks.

Click the "Return to List" button to return to the Series Detail page. Now we're on the Comic Books List page, which wasn't what we expected.

Let's review the "Return to List" button markup in the "/Views/Shared/_Layout.cshtml" layout page.

```
@if (ViewBag.ShowBackBar != null && ViewBag.ShowBackBar)
{
    <div class="backbar">
        <div class="container">
            @Html.ActionLink("Return to List", "Index",
                null, new { @class = "btn btn-default" })
        </div>
    </div>
}
```

This error is similar to the previous error: we're not supplying the controller name so the current controller's name is used by default. When we're viewing the Comic Book Detail page, the current controller is the "ComicBooks" controller so the "Return to List" button will send the user to the Comic Books List page instead of back to the Series Detail page.

There are a variety of ways that we could resolve this issue. Instead of using the `ActionLink` HTML helper method to create the "Return to List" button, we could switch to manually rendering the `<a>` element and setting the `href` attribute to the referrer's URL. Or we could use a JavaScript based approach that leverages the brower's `window.history` object's `back` method.

What method do you think would work best? Share your thoughts on the [Treehouse Community](https://teamtreehouse.com/community).

If you're using GitHub, let's commit our changes. Enter a commit message of "Added Series Detail view" and click the "Commit All" button.

In the [next step](06-removing-duplicated-view-code.md), we'll review the DRY design principle and remove some duplicated code in our views.

[Return to Introduction](README.md)
