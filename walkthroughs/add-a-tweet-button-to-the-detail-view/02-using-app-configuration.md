
# Add a Tweet Button to the Detail View

## Using App Configuration

Our "Tweet" button has our Twitter account hard-coded into the HTML

Add “TwitterAccount” web.config app setting
<add key="TwitterAccount" value="SmashDev"/>

Update the ComicBooksController Detail method to retrieve the “TwitterAccount” value from the app settings
var twitterAccount = ConfigurationManager.AppSettings["TwitterAccount"];
ViewBag.TwitterAccount = twitterAccount;

Update the “ComicBooks/Detail” view
Replace the “SmashDev” literal value with the “ViewBag.TwitterAccount” property value
<p><a href="https://twitter.com/share" class="twitter-share-button" data-via="@ViewBag.TwitterAccount" data-size="large" data-hashtags="comicbooks">Tweet</a></p>

[Return to Introduction](README.md)
