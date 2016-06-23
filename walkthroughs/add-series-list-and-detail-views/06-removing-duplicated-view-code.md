
# Add Series List and Detail Views

## Removing Duplicated View Code

In the previous step, we copied and pasted the comic books list markup from the "Views/ComicBooks/Index.cshtml" file to our Series Detail view. When we did that, we violated the DRY (Don't Repeat Yourself) design principle.

ASP.NET MVC provides a feature, known as "partial views", that gives us a convenient way to avoid repeating that markup in two views. Instead, we can create a partial view that contains the comic book list markup, then render that partial view in both the Comic Books List and Series Detail views.

Let's do that now!

Right click on the "Views/Shared" folder and select the "Add > View" menu item. Change the "View name" field to "_ComicBooksList", select the "Empty (without model)" template, and check the "Create as a partial view" check box. Click the "Add" button to finishing adding the view.

This time, Visual Studio won't provide any us with any starting code or markup for our partial view.

Let's start by adding a `model` view directive to the top of the view in order to make it strongly typed. For the type, provide the fully qualified name for the `ComicBook` data model. Be sure to include a set of brackets `[]` at the end of the model type to indicate that we're expecting an array of that type.

```
@model ComicBookGallery.Models.ComicBook[]
```

Now let's copy and paste the comic book list markup from the "Series/Detail" view into our partial view.

```
<div class="row">
    @foreach (var comicBook in Model.Issues)
    {
        <div class="col-md-3">
            <h4>
                @Html.ActionLink(comicBook.DisplayText, "Detail", "ComicBooks",
                    new { id = comicBook.Id }, null)
            </h4>
            <a href="@Url.Action("Detail", "ComicBooks",
                    new { id = comicBook.Id })">
                <img src="/Images/@comicBook.CoverImageFileName"
                     alt="@comicBook.DisplayText"
                     class="img-responsive" />
            </a>
        </div>
    }
</div>
```

Update the `foreach` loop to use `Model` as its source instead of `Model.Issues`.

```
@foreach (var comicBook in Model)
```

That completes our partial view! Next, we can update the Comic Books List and Series Detail views to use our new partial view.

Open the "Views/ComicBooks/Index.cshtml" file and replace the comic books list markup with a call to the `Partial` HTML helper method. After that change, the view should look like this.

```
@model ComicBookGallery.Models.ComicBook[]

@{
    ViewBag.Title = "Comic Books";
}

<h2>@ViewBag.Title</h2>

@Html.Partial("_ComicBooksList", Model)
```

Then open the "Views/Series/Detail.cshtml" file and replace the comic books list markup with a call to the `Partial` HTML helper method. After that change, the view should look like this.

```
@model ComicBookGallery.Models.Series

@{
    ViewBag.Title = Model.Title;
    ViewBag.ShowBackBar = true;
}

<h2>@ViewBag.Title</h2>

<div class="well">
    <h5>Description</h5>
    <div>
        @Html.Raw(Model.DescriptionHtml)
    </div>
</div>

<h4>Comic Books Available in This Series</h4>

@Html.Partial("_ComicBooksList", Model.Issues)
```

Let's run our website by pressing `F5`. If you started the website from the Series Detail view, you'll initially get a `404` error. Go ahead and browse to the URL `http://localhost:50153/Series`.

Here's our Series List page. Click on a series title to browse to the Series Detail page. And here's our Series Detail page! Looks good so far. Let's try clicking on one of the series' comic books. Clicking on the first comic book takes us to the Comic Book Detail page for that comic book.

Click on the "Comic Books" item in the top menu to browse to the Comic Books List page. And here's our Comic Books List page, which also looks good. To finish, click on the first comic book in the list to browse to its Comic Book Detail page. Looks great!

Close your browser and stop the website.

If you're using GitHub, let's commit our changes. Enter a commit message of "Added comic books list partial view" and click the "Commit All" button.

This concludes our walkthrough of adding Series List and Detail views to our website. I hope you had fun and learned something along the way!

If you find any errors or mistakes with this walkthrough, feel free to fork this repo, fix the error, and issue a pull request back to this repo. Reach out to me on Twitter [@SmashDev](https://twitter.com/SmashDev) and give me your feedback and suggestions on how I can make this walkthrough better.

See you next time!

[Return to Introduction](README.md)
