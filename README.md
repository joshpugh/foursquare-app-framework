Foursquare Apps Builder
=============
A framework for building foursquare apps on AppEngine.

System Config
-------

First update /app.yaml and /config.py with the details for your appengine
instance.

* app.yaml: You should only need to update the application name

* config.py: Read through the documentation and update fields as appropriate.

Implementing an app
-------

Create a new folder for your app's files. You can check out /chipotle and
/menulette as examples. Your main class should extend the Plugin class found
in plugin.py. There are 6 primary actions the app can support.

#### checkinTaskQueue(self, authenticated_client, checkin_json)
* A user who has authorized your app has checked in. The default
implementation no-ops and returns a 200.

#### pluginPost(self, client)
* A user is posting content to the app. For example, Menulette does this to
let users post the challenge to their check-in. The default
implementation returns a 404 to the user.

#### contentGet(self, client, content_info)
* A user is requesting to view content generated by the app. The content info
is passed along to the app so it can render the appropriate page. The
default implementation returns a 400 to the user.

#### pluginGet(self, client)
* A user is requesting some site in the app.  The default
implementation returns a 404 to the user.

The client object passed for these calls is a Foursquare object from
Mike Lewis's library: https://github.com/mLewisLogic/foursquare.
For checkinPost, the client includes the user's access token. For all other calls
you'll need to retrieve the access token you want to use via
utils.fetchAccessToken(user_id) and set it via
client.set_access_token(access_token).

Don't forget to specify a PATH variable, this is the path your app will be
served under from the appengine site.

There are a bunch of utility methods in both /utils.py and /plugins.py which
should make common actions much easier.


Setting up the app.
-------

Register your app at http://foursquare.com/oauth. Your application web site is

    http://APPENGINE_APPLICATION_NAME.appspot.com

And your callback url is

    https://APPENGINE_APPLICATION_NAME.appspot.com/oauth

Once you've registered, you'll get a client_id. You'll want to edit your
app's settings to fill out the client_id and set up your app for push
notifications / as a connected app.

Your push url is

    http://APPENGINE_APPLICATION_NAME.appspot.com/checkin

Next, make sure you've inherited from our Plugin class and set the correct
class in config.py

Finally, deploy your instance, and you should be all set!


Local development
-------

It's easy to develop your app locally. Go to config.py and
specify "local_dev": True and give the localhost url under 'local_server'.
Then use the AppEngineLauncher to run your app locally and hit it with a fake
check-in -- there's a sample payload in the testing directory. It's
url-encoded, so you'll need to decode and re-encode to make edits. I use
http://meyerweb.com/eric/tools/dencoder/ to do this.

You'll need to change the user ID field to your ID in order for the app
to find your auth token in the datastore.

Once you've done that, you can push the data into your app via:

    curl http://localhost:8080/checkin --data @test/test_checkin.txt


You probably don't want to make actual write requests from the app when in
local dev mode -- makeContentInfo skips and logs for your convenience.
