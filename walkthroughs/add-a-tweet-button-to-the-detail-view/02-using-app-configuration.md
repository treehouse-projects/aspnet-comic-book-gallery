
# Add a Tweet Button to the Detail View

## Using App Configuration

The "Tweet" button on our Comic Book Detail page currently has the Twitter account name hard-coded into the code that we copied and pasted from Twitter's website. In this step, we'll see how we can use ASP.NET app configuration to remove that hard-code value from our view.

Open the "Web.config" file that's located in the root of the project. "Web.config" is the main settings and configuration file for ASP.NET web applications. Within this file, there is an `appSettings` section that we can use to define global application settings as "key/value" pairs.

Let's add a new app setting for our Twitter account. For the key, use "TwitterAccount" and for the value, use the Twitter account name that you're using for your "Tweet" button.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0"/>
    <add key="webpages:Enabled" value="false"/>
    <add key="ClientValidationEnabled" value="true"/>
    <add key="UnobtrusiveJavaScriptEnabled" value="true"/>
    <add key="TwitterAccount" value="SmashDev"/>
</appSettings>
```

Now we need to update our website to use this app settings value for our "Tweet" button's Twitter account. We'll start by updating our `ComicBooksController.Detail` action method. Open the "Controllers/ComicBooksController.cs" file.

In the `Detail` action method, we'll use the `ConfigurationManager` class to retrieve the app setting value. To use the `ConfigurationManager` class, you'll need to add a `using` directive for the `System.Configuration` namespace.

You can retrieve app setting values using the `ConfigurationManager` class' `AppSettings` property like this.

```
string twitterAccount = ConfigurationManager.AppSettings["TwitterAccount"];
```

The string literal `TwitterAccount` inside of the brackets is similar to indexing into an array (i.e. `myArray[0]`) except in this case, we're indexing into a collection of app setting values using the app setting key name.

App setting values are always represented as strings. If an app setting value is actually another data type, such as a number or boolean, you can parse or convert the string values to their "correct" data types (for an example of this, see the `Boolean.TryParse` method [https://msdn.microsoft.com/en-us/library/system.boolean.tryparse(v=vs.110).aspx](https://msdn.microsoft.com/en-us/library/system.boolean.tryparse(v=vs.110).aspx)). In a future course, we'll discuss string parsing in more detail.

Now that we have our Twitter account app setting value, let's pass it to our view using a `ViewBag` object property.

```
ViewBag.TwitterAccount = twitterAccount;
```

Our updated action method now looks like this.

```
public ActionResult Detail(int? id)
{
    if (id == null)
    {
        return HttpNotFound();
    }

    string twitterAccount = ConfigurationManager.AppSettings["TwitterAccount"];
    ViewBag.TwitterAccount = twitterAccount;

    var comicBook = _comicBookRepository.GetComicBook((int)id);

    return View(comicBook);
}
```

To finish things up, let's update our Comic Book Detail view. Open the "Views/ComicBooks/Detail.cshtml" file.

In the "Tweet" button code, we need to update the `<a>` element's `data-via` attribute value to use the `ViewBag.TwitterAccount` property value.

```
<p><a href="https://twitter.com/share" class="twitter-share-button" 
      data-via="@ViewBag.TwitterAccount" data-size="large" 
      data-hashtags="comicbooks">Tweet</a></p>
```

Let's test our updated "Tweet" button!

Press `F5` to run the website. If you started the website from the Comic Book Detail view, you'll initially get a `404` error. Go ahead and browse to the URL `http://localhost:50153/`.

Here's our Comic Books List page. Click on a comic book to browse to the Comic Book Detail page. Click on the "Tweet" button. You should see the "Share a link on Twitter" dialog with the Twitter account name that you specified in the "Web.config" app settings section.

```
The Amazing Spider-Man #700 - Comic Book Gallery http://localhost:50153/ComicBooks/Detail/1 #comicbooks via @SmashDev
```

_Important note: if you're running your Comic Book Gallery website locally on your PC (like I'm doing here), the URL in the Tweet text will only work on your PC and only when you're currently running the website using Visual Studio. You'd need to publish your website to a hosting provide in order to have URLs that would work for any user._

Close your browser and stop the website.

If you're using GitHub, let's commit our changes. Enter a commit message of "Updated Tweet button to use app configuration" and click the "Commit All" button.

This concludes our walkthrough of adding a "Tweet" button to our website's Comic Book Detail page. I hope you had fun and learned something along the way!

If you find any errors or mistakes with this walkthrough, feel free to fork this repo, fix the error, and issue a pull request back to this repo. Reach out to me on Twitter [@SmashDev](https://twitter.com/SmashDev) and give me your feedback and suggestions on how I can make this walkthrough better.

See you next time!

[Return to Introduction](README.md)
