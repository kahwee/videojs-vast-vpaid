# Historical documentation

Original project notes. Commands, hosted demos, browser support, and external
services describe the original toolchain; they have not been revalidated today.

# videojs-vast-plugin
[![Build Status](https://travis-ci.org/MailOnline/videojs-vast-vpaid.svg?branch=master)](https://travis-ci.org/MailOnline/videojs-vast-vpaid)
[![Code Climate](https://codeclimate.com/github/MailOnline/videojs-vast-vpaid/badges/gpa.svg)](https://codeclimate.com/github/MailOnline/videojs-vast-vpaid)
[![Test Coverage](https://codeclimate.com/github/MailOnline/videojs-vast-vpaid/badges/coverage.svg)](https://codeclimate.com/github/MailOnline/videojs-vast-vpaid/coverage)

  This plugin allows videojs to monetise its videos. To do so, it implements the [VAST](http://www.iab.net/media/file/VASTv3.0.pdf) and [VPAID](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf) specifications from IAB.

  Currently we support VAST and VPAID [Flash](https://github.com/MailOnline/VPAIDFLASHClient) and [HTML5](https://github.com/MailOnline/VPAIDHTML5Client) preroll ads, we will add more VAST ad types as we need them.

  It is important to notice that **VPAID integration is still in beta** and we have not yet released a stable version.

###[DEMO HERE!!!](http://mailonline.github.io/videojs-vast-vpaid)

## Integration with video.js
  To integrate the plugin with videoJs you need to:

  1.- Add [all files from bin](https://github.com/MailOnline/videojs-vast-vpaid/tree/master/bin) to some path in your server

  2.- If don't have videoJs, add their scripts and stylesheet to your page
  ```
  <link href="http://vjs.zencdn.net/4.12/video-js.css" rel="stylesheet">
  <script src="http://vjs.zencdn.net/4.12/video.js"></script>
  ```

  3.- After videoJs add the plugin script stylesheet and swfobject (if you want to support flashvpaid) to your page
  ```
  <script src="/path/to/swfobject.js"></script>
  <link href="/path/to/videojs-vast-plugin.css" rel="stylesheet">
  <script src="/path/to/videojs-vast-plugin.min.js"></script>
  ```

  if you need to support older browsers that don't support ES5 add this to your page before the plugin script
  ```
  <script src="/path/to/es5-shim.js"></script>
  ```
  if you need to support ie8 add this after the es5-shim.js script
  ```
  <script src="/path/to/ie8fix.js"></script>
  ```

  4.- Create you own ads plugin to pass an add media tag to the plugin

  Below you have a simple ads-setup-plugin

  ```javascript
  vjs.plugin('ads-setup', function (opts) {
    var player = this;
    var adsCancelTimeout = 3000;

    var vastAd = player.vastClient({
      //Media tag URL
      url: "http://pubads.g.doubleclick.net/gampad/ads?env=....",
      playAdAlways: true,
      //Note: As requested we set the preroll timeout at the same place thant the adsCancelTimeout
      adCancelTimeout: adsCancelTimeout,
      adsEnabled: !!options.adsEnabled
    });
  });
  ```

  You can also configure the vast plugin using the data-setup attribute

```html
  <video id="example_video_1" class="video-js vjs-default-skin"
         controls preload="auto" width="640" height="264"
         poster="http://video-js.zencoder.com/oceans-clip.png"
         data-setup='{
                  "plugins": {
                          "vastClient":{
                              "url": "http://pubads.g.doubleclick.net/gampad/ads?env=....",
                              "adsCancelTimeout": 5000,
                              "adsEnabled": true
                          }
                  }
             }'>
      <source src="http://video-js.zencoder.com/oceans-clip.mp4" type='video/mp4'/>
      <source src="http://video-js.zencoder.com/oceans-clip.webm" type='video/webm'/>
      <source src="http://video-js.zencoder.com/oceans-clip.ogv" type='video/ogg'/>
      <p class="vjs-no-js">To view this video please enable JavaScript, and consider upgrading to a web browser that <a
              href="http://videojs.com/html5-video-support/" target="_blank">supports HTML5 video</a></p>
  </video>
```

## Options

### adTagUrl
  >Use it to pass the ad media tag, it can be a string containing the Media tag url
  >
  >##### Hardcoded Media Tag
  >
  > var vastAd = player.vastClient({
  >   adTagUrl: "http://pubads.g.doubleclick.net/gampad/ads?env=....",
  >  ...
  > });
  >
  >
  >or a function that will return the Media tag whenever called
  >
  >
  >#####  Dynamic Media Tag
  >```javascript
  >var vastAd = player.vastClient({
  >adTagUrl: getAdsUrl,
  > ...
  >});
  >
  >function getAdsUrl() {
  >      return "http://pubads.g.doubleclick.net/gampad/ads?env=....";
  >}
  >```
  >On initialization, the plugin well call the function and store the returned Media tag to request the VAST/VPAID ads.


### url (deprecated)
  >**This option is deprecated and you should use adTagUrl instead**
  >
  >Use it to pass the ad media tag, it can be a string containing the Media tag url
  >
  >##### Hardcoded Media Tag
  >
  > var vastAd = player.vastClient({
  >   url: "http://pubads.g.doubleclick.net/gampad/ads?env=....",
  >  ...
  > });
  >
  >
  >or a function that will return the Media tag whenever called
  >
  >
  >#####  Dynamic Media Tag
  >```javascript
  >var vastAd = player.vastClient({
  >url: getAdsUrl,
  > ...
  >});
  >
  >function getAdsUrl() {
  >      return "http://pubads.g.doubleclick.net/gampad/ads?env=....";
  >}
  >```
  >On initialization, the plugin well call the function and store the returned Media tag to request the VAST/VPAID ads.

### adTagXML
  >You can now do the VAST xml request on your own with our shinny new adTagXML option. 
  >
  >All you need to do is to pass the request fn as the adTagXML option when you initialize the plugin. See below for an example
  >
  >##### Using the adTagXML option
  >```javascript
  >var vastAd = player.vastClient({
  >adTagXML: requestVASTXML,
  > ...
  >});
  >
  >function requestVASTXML(callback) {
  >    //The setTimeout below is to simulate asynchrony
  >    setTimeout(function(){
  >      callback(null, '<VAST version="3.0"><Ad><Inline>...</Inline></Ad></VAST>');
  >    }, 0);
  >}
  >```
  >As you can see the requestVASTXML function above expects a node like error-first-callback that needs to be called whenever we are ready to serve the VAST XML.  
  >If you had any error executing the request, you need to pass it as the first argument of the callback 
  >and if there was no error pass null as the first argument and the VAST XML string as the second argument. 

### playAdAlways
  >Flag to indicate if we must play an ad whenever possible. If set to true the plugin will play an ad every time the user watches a new video or replays the actual video.
  >Defaults to false

## adCancelTimeout
 >Number of milliseconds for the ad to start before canceling it. Defaults to 3000

## adsEnabled
 >Flag to disable the ads. Defaults to false.

## autoResize
 >Flag to enable resize of the adUnit on window's `resize` and `orientationchange` events. This is useful for responsive players. Defaults to true

## vpaidFlashLoaderPath
 >Path to the vpaidFlashloader swf file. It defaults to '/VPAIDFlash.swf'

## Returned object
 An invocation to ```player.vastClient({...})``` returns and object that with some helper functions that allow you to dynamically enable or disable the vast plugin, or check if it is enabled.
  ```javascript
  var vastPlugin = player.vastClient({
     url: getAdsUrl,
     playAdAlways: true,
     //Note: As requested we set the preroll timeout at the same place thant the adsCancelTimeout
     adCancelTimeout: adsCancelTimeout,
     adsEnabled: !!options.adsEnabled
   });

   player.on('reset', function () {
       if (!vastPlugin.isEnabled()) {
         vastPlugin.enable();
       } else {
         vastPlugin.disable();
       }
   });
  ```

### isEnabled
  >This function returns true if the player is enabled and false otherwise.

### enable
  >Enables the VAST plugin

### disable
  >Disables the plugin

### adUnit
  >If there is an ad playing (after the vast.adStart event) it will contain an obj like the one below:
  ```
  player.vast.adUnit= {
     type: {string} <== The possible types are 'VAST' or 'VPAID'
     pauseAd: {function} <== Pauses the ad unit
     resumeAd: {function} <== Resumes the ad unit
     getSrc: {function} <== Returns the MediaFile instance used to play the ad
  }
  ```
  >Otherwise it will be null or undefined

## player.vast
  The returned object described above it is also published as a player property so that you can use it anywhere as long as you have access to the player instance.
  ```javascript
     player.vastClient({
       url: getAdsUrl,
       playAdAlways: true,
       //Note: As requested we set the preroll timeout at the same place thant the adsCancelTimeout
       adCancelTimeout: adsCancelTimeout,
       adsEnabled: !!options.adsEnabled
     });

     player.on('reset', function () {
         if (!player.vast.isEnabled()) {
           player.vast.enable();
         } else {
           player.vast.disable();
         }
     });
  ```
## Plugin events
  The plugin does trigger some events that can be used for tracking or debugging.

### 'vast.firstPlay' event
  Fired when the user first plays a video or if the playAdAlways option is set to true every time the user replays the same video

### 'vast.adStart' event
  Fired when the ad starts playing

### 'vast.adSkip' event
  Fired when the a vast ad gets skiped

### 'vast.adError' event
  Fired whenever there is an error with the ad. The error itself gets added to the event object in the property 'error'.

### 'vast.adsCancel' event
  Fired whenever the ads are canceled due to an error or because the plugin is not enabled.

### 'vast.contentStart' event
  Fired whenever the video content starts playing

### 'vast.contentEnded' event
  Fired when the video content ends.

### 'vast.reset' event
  Trigger the 'vast.reset' event whenever you want to reset the plugin. Beware that if an ad is playing it will be canceled.

## Original development commands

The original workflow uses npm, Bower, Gulp and Karma. See the
[current checkout summary](../README.md#development) for setup.

| Gulp task | Purpose |
| --- | --- |
| `start-dev` | Build and serve the local demo on port 8085 |
| `build` | Build plugin assets |
| `build-demo` | Build the demo |
| `watch` | Rebuild on source changes |
| `ci-test` | Run Karma tests with coverage |
| `test` | Run Karma with auto-watch |
| `deploy-demo` | Historical GitHub Pages deployment task |

## License
videojs-vast-plugin is licensed under the MIT License. [View the license file](../LICENSE)

Copyright (c) 2015 MailOnline
