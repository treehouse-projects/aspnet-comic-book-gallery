
# Add Series List and Detail Views

## Adding the Series Detail Page

Add "Detail" action method

Call the "SeriesRepository.GetSeriesDetail" method
Pass the result into the View method call

Add "Detail" view

Includes a list of the series' issues

Right-click on the "SeriesController.Detail" method's View method call and select the "Add View…" menu item
  Accept all of the default values and click "OK"
  Use the "Empty (without model)" template

Add model view directive to the top of the view in order to make our view a strongly typed view

Set the ViewBag.Title property to the Model.Title property value
Update the <h2> to display the Model.Title property value

Output the Series.Description property value

Display the list of issues in the series
  Start by copying and pasting Comic Books list page markup into this view
  Add <h4> heading with the text "Comic Books Available in This Series"

Oops! The links to the Comic Book Detail page are broken!
  Why? We aren't explicitly naming the "ComicBooks" controller
  Replace "ambient" controller names with explicit names when calling Html.ActionLink
  Update the "Html.ActionLink" and "Url.Action" method calls to explicitly name the "ComicBooks" controller
    Be careful which overload you are calling! It's easy to think you're calling the correct method overload but actually be targeting the wrong one.

In the [next step](07-removing-duplicated-view-code.md), we'll review the DRY design principle and remove some duplicated code in our views.

[Return to Introduction](01-introduction.md)
