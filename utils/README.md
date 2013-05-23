# Rdio Utils

Some helper functions for the Rdio JavaScript API. 

## Documentation

To use, include `rdio-utils.js` and `rdio-utils.css` in your project. It adds a single object to the global space: `rdioUtils`.  See below for methods.

### rdioUtils.startupChecks()

Does various standard health checks, and notifies the user if there are issues. The notification is a simple dialog that you can style via the `.rdio-utils-dialog` CSS class. It's not terribly deluxe, so if you want something more advanced, you might just use the code as a reference for what to check.

It checks:

* If the Rdio API loaded at all.
* If the user has cookies blocked.
* If the user needs Flash but has it blocked or not installed.

It returns `true` if the API exists on the page and false if it doesn't (i.e. Rdio is down).

```
if (!rdioUtils.startupChecks()) {
  return;
}
``` 

### rdioUtils.collectionAlbums( config )

Allows you to access all the albums in the user's collection, and notifies you when albums are added or removed.

The first time it's run (or every time if you don't use the `localStorage` option), it loads all the albums from the user's collection out of the Rdio database. This may take as long as a minute for large collections. Thereafter (assuming you're using the `localStorage` option), it'll load immediately.

When the user adds or removes albums from their collection, you'll be notified shortly thereafter. If they've added or removed albums since you last ran, you'll be notified of those right after the initial load.

```
var collection = rdioUtils.collectionAlbums({
  localStorage: true, // Highly recommended.
  onAlbumsLoaded: function(albums) {
    // Called during the initial load with an array of albums. When loading
    // from the Rdio database, you'll get a number of these; when loading
    // from localStorage, it'll come in one bunch.
  },
  onLoadComplete: function() {
    // Called when the initial load is complete.

    for (var i = 0; i < collection.length; i++) {
      console.log(collection.at(i).name);
    }
  },
  onError: function(message) {
    // Called with any loading errors.
  },
  onAdded: function(albums) {
    // Called after the initial load when the user adds additional albums to 
    // their collection.
  },
  onRemoved: function(albums) {
    // Called after the initial load when the user removes albums from their 
    // collection.
  }
});
```

### rdioUtils.authWidget( element )

Turns the given element into a small Rdio authentication widget: if the user is authenticated, it shows their name. Otherwise it shows a "Sign In With Rdio" button which, when clicked, initiates the authentication sequence. If the authentication is successfull, the widget updates to show the user's name.

`element` can be either a DOM element or a jQuery object. Its contents will be replaced with the widget.

```
rdioUtils.authWidget($('.authenticate'));
```

### rdioUtils.addToTopOfQueue( sourceKey )

Adds the given source to the top of the queue (rather than the bottom, which is where `R.player.queue.add()` puts it).

```
rdioUtils.addToTopOfQueue('a171827');
```

## TODO

* Playback UI
* Album UI
* Unit tests for collectionAlbums()
 * Interrupting a load with another load
 * If library changes during initial load
* Track the collection of any user?
* Sort collectionAlbums by play count, date added, etc?