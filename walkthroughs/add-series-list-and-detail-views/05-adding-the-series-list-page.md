
# Add Series List and Detail Views

## Adding the Series List Page

Now that we’ve updated our models, centralized our data, and created our repository, we’re ready to add the controller and view for our series list page. Let’s start by adding the `SeriesController` class.

Instead of adding a class using the “Add > Class” menu item, let’s right click on the “Controllers” folder and select the “Add > Controller” menu item.



Add “SeriesController”
Use the right click “Add Controller” workflow
Add “_seriesRepository” private field
Add default constructor that instantiates the repository

Add “Index” method
Call the “SeriesRepository.GetSeries” method
Pass the result into the View method call

Add “Index” view
Right-click on the “SeriesController.Index” method’s View method call and select the “Add View…” menu item
Accept all of the default values and click “OK”
Use the “Empty (without model)” template
Add model view directive to the top of the view in order to make our view a strongly typed view
Needs to be a collection of Series models!
Set the ViewBag.Title property to the string literal “Series”
Update the <h2> to display the ViewBag.Title property value
Loop over the items in the model and render the Series.Title in a Bootstrap grid row
Update the series title to be clickable using <a> tags

Add menu items to the layout page

<li>@Html.ActionLink("Comic Books", "Index", "ComicBooks")</li>
<li>@Html.ActionLink("Series", "Index", "Series")</li>

In the [next step](06-adding-the-series-detail-page.md), we’ll add the Series Detail page.

[Return to Introduction](01-introduction.md)
