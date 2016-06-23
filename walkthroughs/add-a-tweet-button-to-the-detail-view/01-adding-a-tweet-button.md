
# Add a Tweet Button to the Detail View

## Adding a "Tweet" Button

In this step, we'll visit Twitter's website to get the HTML and JavaScript to add a "Tweet" button to our Comic Book Detail page.

To start, browse to [https://about.twitter.com/resources/buttons#tweet](https://about.twitter.com/resources/buttons#tweet).

In the "Choose a button" section, select the "Share a link" option. Under the "Button options" section, use the following values.

* For "Share URL" select "Use the page URL" option
* For "Tweet text" select "Use the title of the page" option
* For the "Via" value use your Twitter account name (for this walkthrough I'll be using my Twitter account name `SmashDev`)
* Leave "Recommend" blank
* For the "Hashtag" value use "comicbooks"
* Check the "Large button" check box

Then in the "Preview and code" section, select and copy the code in the field just below the preview "Tweet" button. At the time of this writing, the generated code looks like this.

```
<a href="https://twitter.com/share" class="twitter-share-button" 
   data-via="SmashDev" data-size="large" 
   data-hashtags="comicbooks">Tweet</a> 
<script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>
```

Now that we've got our "Tweet" button code, let's add it to our Comic Book Detail view! Open the "Views/ComicBooks/Detail.cshtml" view.

Let's add the "Tweet" button in the "well" section of the view. Notice that I'm also adding a `<p>` element, containing a non-breaking space HTML entity, just above the "Tweet" button in order to create some whitespace between the button and the content just above it.

```
<div class="well">
    <h5><label>Series Title:</label> @(Model.Series != null ? Model.Series.Title : "N/A")</h5>
    <h5><label>Issue #:</label> @Model.IssueNumber</h5>
    <h5><label>Favorite:</label> @(Model.Favorite ? "Yes" : "No")</h5>
    @if (Model.Artists.Length > 0)
    {
        <h5>Artists:</h5>
        <div>
            <ul>
                @foreach (var artist in Model.Artists)
                {
                    <li>@artist.Role: @artist.Name</li>
                }
            </ul>
        </div>
    }
    <p>&nbsp;</p>
    <p><a href="https://twitter.com/share" class="twitter-share-button" 
          data-via="SmashDev" data-size="large" 
          data-hashtags="comicbooks">Tweet</a></p>
    <script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>
</div>
```

Ready to test our "Tweet" button?

Press `F5` to run the website. If you started the website from the Comic Book Detail view, you'll initially get a `404` error. Go ahead and browse to the URL `http://localhost:50153/`.

Here's our Comic Books List page. Click on a comic book to browse to the Comic Book Detail page. And here's our Comic Book Detail page with our "Tweet" button! Click on the "Tweet" button. You should see the "Share a link on Twitter" dialog prompting you to login (if you're not already) and share the following text.

```
The Amazing Spider-Man #700 - Comic Book Gallery http://localhost:50153/ComicBooks/Detail/1 #comicbooks via @SmashDev
```

We can see our page title, hashtag, and Twitter account name.

_Important note: if you're running your Comic Book Gallery website locally on your PC (like I'm doing here), the URL in the Tweet text will only work on your PC and only when you're currently running the website using Visual Studio. You'd need to publish your website to a hosting provide in order to have URLs that would work for any user._

Close your browser and stop the website.

If you're using GitHub, let's commit our changes. Enter a commit message of "Added Tweet button to the Comic Book Detail view" and click the "Commit All" button.

In the [next step](02-using-app-configuration.md), we'll see how we can use ASP.NET app configuration to remove the hard-coded Twitter account name from our Comic Book Detail view.

[Return to Introduction](README.md)
