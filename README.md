# feature-test

a proof of concept to use modernizr to test for browser functionality

## javascript & cookies (the straightforward bit)

HTML templates must include have the class `no-js` applied to head (e.g. `<html class="no-js">`), and load the custom build of modernizr `<script src="modernizr-custom.js"></script>` within the head of the document.

One of the first yep-nope things that [modernizr](https://modernizr.com) checks for is if there is javascript or not. If javascript is present, the class applied to the `html` element will be changed to `<html class="js">`.

Once that condition is mset, further tests can be made (since modernizr itself is javascript). I've added in a test for cookies - if the browser is not capable of setting cookies, the `html` element will have a `no-cookies` class set on it, and look something like `<html class="js no-cookies">`.

## browser detection (the other bit)

User Agent (UA) sniffing is generally considered a bad thing. Browsers supported by manufacturers are IE9 (on certain platforms) and in general the latest two versions of previous browsers (Chrome, Firefox, etc). Let's theorycraft how that would play out in a reasonable manner.

* Some script pings a third party service on page load, that tracks the latest version release of all major browsers, then subtracts 2 from most (3 from IE). This would require an external dependency (and may or may not exist).

* Something like the above is done in house.

* There is a build process, that ala postCSS will run every foo days/weeks and update variables as needed, then pushes to prod.

* Such lists are either manually updated, or more realistically fall behind given release schedules are in weeks rather than years for many browsers.

Those all, frankly, kinda suck. ^^;

A few more thoughts:

* User Agents can be easily spoofed, though this is probably less of an issue now than back in the day.

* There are less cross OS browser issues (foo feature works on OS X, but not not OS Y), but they still exist and version numbers may not match actual feature functionality.

The proper way to feature detect would be to run a modernizr test on any CSS value of dubious support, and allow those browsers that pass (excluding ones polyfilled or with css workarounds based on the modernizr class). However we don't want to support the most browsers we can, but ones that we want.

### proposed simple solution

An, imperfect, yet simple solution, is to just check for some CSS property that IE8 doesn't support (since Internet Explorer is generally the browser). Perusing caniuse brought up a few candidates, but I like [media queries](http://caniuse.com/#feat=css-mediaqueries) since they're actually something that doesn't gracefully degrade. :) By adding that test to the modernizr build, if a browser is older than IE8 (or for some reason doesn't support media queries) it will get pushed out. [rem](http://caniuse.com/#feat=rem) and [css3 colors](http://caniuse.com/#feat=css3-colors) were the runner up candidates, but the latter isn't critical.

Granted this doesn't solve for older non-IE browsers, but those tend to update on a more regular schedule. Some data for this would be nice.

If a browser passed the other two tests, and failed this one, the `html` element would look like `<html class="js cookies no-media-queries">`.

If this isn't good enough, one could add in a proper UA sniffing test on top of modernizr's js and cookies ones. Before gzipping the custom modernizr file is 2.7Kb, and it fires quickly, so it'd probably still be useful regardless.

## added bonus

It's easy to style bits of information on a static page people get sent to, as you can show/hide information based on simple CSS applied to specific children of those classes on the `html` element.

## tl;dr

Have something server side that checks for `no-js`, `no-cookies` or `no-media-queries` on the `html` element and send to an error page if any appear. How is would be done is up to someone else. :p
