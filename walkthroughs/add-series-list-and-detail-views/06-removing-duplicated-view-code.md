
# Add Series List and Detail Views

## Removing Duplicated View Code

Review DRY principle

Introduce partial views

Refactor the "Comic Books" page markup into a partial view

Right-click on the Shared views folder and select the "Add > View..." menu item
    Change the name to "_ComicBooksList"
    Check the "Create as a partial view" check box

Copy and paste the comic book list markup from the "Series/Detail" view

Add "@model" directive to the top of the view
@model List<ComicBookGallery.Models.ComicBook>

Change the "foreach" loop statement to just reference "Model"
@foreach (var comicBook in Model.Issues)

Update the "Comic Books" and "Series Detail" views to use the partial view
    Replace the Comic Books List markup in the "Comics/Index" view with a call to "Html.Partial"
        @Html.Partial("_ComicBooksList", Model)
    Replace the Comic Books List markup in the "Series/Detail" view with a call to "Html.Partial"
        @Html.Partial("_ComicBooksList", Model.Issues)

Pass in "controller" and "action" parameters?

[Return to Introduction](README.md)
