phantomas
=========

![GitHub Logo](http://upload.wikimedia.org/wikipedia/en/a/a5/Fantomas.jpg)

PhantomJS-based modular web performance metrics collector. And why phantomas? Well, [because](http://en.wikipedia.org/wiki/Fantômas) :)

[![NPM version](https://badge.fury.io/js/phantomas.png)](http://badge.fury.io/js/phantomas)
[![Build Status](https://api.travis-ci.org/macbre/phantomas.png)](http://travis-ci.org/macbre/phantomas)

## Requirements

* [NodeJS](http://nodejs.org)
* [PhantomJS 1.9+](http://phantomjs.org/)

## Installation

```
npm install --global phantomas
```

> This will install the latest version of PhantomJS and add a symlink called ``phantomas`` (pointing to ``./bin/phantomas.js``) to your system's ``PATH``

## Features

* Modular approach - each metric is generated by a separate "module"
* phantomas "core" acts as an [events emitter](https://github.com/macbre/phantomas/wiki/Events) that each module can hook into
* in-depth metrics such as: number of events bound via jQuery, calls to ``window.write``or [complex and duplicated CSS selectors (via analyze-css)](https://github.com/macbre/analyze-css)
* JSON and CSV as available output formats for easy integration with automated reporting / monitoring tools
* easy integration with Continuous Integration tools via TAP format and assertions handling
* metrics can be sent via StatsD or stored in elasticsearch
* easy integration with other nodejs projects via CommonJS module ([see API docs](https://github.com/macbre/phantomas/wiki/npm-module))
* metrics can be emitted from JavaScript code of the page phantomas is run against (thanks to [helper functions available in window.__phantomas](https://github.com/macbre/phantomas/wiki/Phantomas-scope))
* device profiles allow phantomas to emulate mobile or tablet (by setting a proper user agent and viewport)

## Contributors

* [macbre](https://github.com/macbre)
* [jmervine](https://github.com/jmervine)
* [jmosney](https://github.com/jmosney)
* [umaar](https://github.com/umaar)
* [sjhcockrell](https://github.com/sjhcockrell)
* [cphoover](https://github.com/cphoover)
* [LaurentGoderre](https://github.com/LaurentGoderre)
* [kennydee](https://github.com/kennydee)
* [iNem0o](https://github.com/iNem0o)
* [stefanjudis](https://github.com/stefanjudis)
* [vgangan](https://github.com/vgangan)
* [william-p](https://github.com/william-p)
* [cvan](https://github.com/cvan)
* [gomezd](https://github.com/gomezd)
* All the [contributors](https://github.com/macbre/phantomas/graphs/contributors)

## Usage

> phantomas comes as both command line tool and CommonJS module ([see API docs](https://github.com/macbre/phantomas/wiki/npm-module)) that you can use in your nodejs projects.

### Single run

``` bash
phantomas https://github.com/macbre/phantomas --verbose
```

You can measure the performance of your site without requests to 3rd party domains (but allowing CDN that serves your static assets):

```bash
phantomas https://github.com/macbre/phantomas --verbose --no-externals --allow-domain .fastly.net
```

#### Parameters

* `--reporter=[json|csv|tap|plain|statsd|elasticsearch]` results reporter aka format (``plain`` is the default one)
* `--timeout=[seconds]` timeout for phantomas run (defaults to 15 seconds)
* `--viewport=[width]x[height]` phantomJS viewport dimensions (1280x1024 is the default)
* `--verbose` writes debug messages to the console
* `--debug` run PhantomJS in debug mode
* `--silent` don't write anything to the console
* `--progress` shows page loading progress bar (disables verbose mode)
* `--log=[log file]` log to a given file
* `--modules=[moduleOne],[moduleTwo]` run only selected modules
* `--include-dirs=[dirOne],[dirTwo]` load modules from specified directories
* `--skip-modules=[moduleOne],[moduleTwo]` skip selected modules
* `--user-agent='Custom user agent'` provide a custom user agent (will default to something similar to ``phantomas/0.6.0 (PhantomJS/1.9.0; linux 64bit)``)
* `--config=[JSON config file]` uses JSON-formatted config file to set parameters
* `--cookie='bar=foo;domain=url'` document.cookie formatted string for setting a single cookie
* `--cookies-file=[JAR file]` specifies the file name to store the persistent Cookies
* `--no-externals` block requests to 3rd party domains
* `--allow-domain=[domain],[domain]` allow requests to given domain(s) - aka whitelist
* `--block-domain=[domain],[domain]` disallow requests to given domain(s) - aka blacklist
* `--auth-user` sets the user name used for HTTP authentication
* `--auth-pass` sets the password used for HTTP authentication
* `--disable-js` disable JavaScript on the page that will be loaded
* `--analyze-css` emit in-depth CSS metrics **experimental**
* `--film-strip` register film strip when page is loading **experimental**
* `--film-strip-dir=[dir path]` folder path to output film strip (default is ``./filmstrip`` directory)
* `--film-strip-prefix` film strip files name prefix (defaults to ``screenshot``)
* `--page-source` save page source to file **experimental**
* `--page-source-dir=[dir path]` folder path to output page source (default is ``./html`` directory) **experimental**
* `--assert-[metric-name]=value` assert that given metric should be less or equal the value
* `--screenshot=[file name]` render fully loaded page to a given file
* `--har=[file name]` save HAR to a given file
* `--wait-for-selector=[CSS selector]` wait for an element matching given CSS selector before generating a report, timeout setting still applies (e.g. ``--wait-for-selector "body.loaded"``)
* `--post-load-delay=[seconds]` wait X seconds before generating a report, timeout setting still applies
* `--ignore-ssl-errors` ignores SSL errors, such as expired or self-signed certificate errors
* `--proxy=[host:port]` specifies the proxy server to use
* `--proxy-auth=[username:password]` specifies the authentication information for the proxy
* `--proxy-type=[http|socks5|none]` specifies the type of the proxy server (default is http)
* `--phone` force viewport and user agent of a mobile phone
* `--tablet` force viewport and user agent of a tablet

### Multiple runs

Simply provide ``--runs`` option:

``` bash
phantomas https://github.com/macbre/phantomas --verbose --runs 5
```

Only ``plain`` (the default one) and ``json`` reporters are currently supported in multiple runs mode.

## Metrics

_Current number of metrics: 104_

Units:

* ms for time
* bytes for size

### Requests monitor (core module)

> Due to [PhantomJS issue #10156](https://github.com/ariya/phantomjs/issues/10156) **body size related metrics are not reliable**

* requests: total number of HTTP requests made
* gzipRequests: number of gzipped HTTP responses
* postRequests: number of POST requests
* httpsRequests: number of HTTPS requests
* notFound: number of HTTP 404 responses
* timeToFirstByte: time it took to receive the first byte of the first response (that was not a redirect)
* timeToLastByte: time it took to receive the last byte of the first response (that was not a redirect)
* bodySize: size of the content of all responses
* contentLength: size of the content of all responses (based on ``Content-Length`` header)
* httpTrafficCompleted: time it took to receive the last byte of the last HTTP response

### AJAX requests

* ajaxRequests: number of AJAX requests

### Assets types

> Due to [PhantomJS issue #10156](https://github.com/ariya/phantomjs/issues/10156) **body size related metrics are not reliable**

* htmlCount: number of HTML responses
* htmlSize: size of HTML responses
* cssCount: number of CSS responses
* cssSize: size of CSS responses
* jsCount: number of JS responses
* jsSize: size of JS responses
* jsonCount: number of JSON responses
* jsonSize: size of JSON responses
* imageCount: number of image responses
* imageSize: size of image responses
* webfontCount: number of web font responses
* webfontSize: size of web font responses
* base64Count: number of base64 encoded "responses" (no HTTP request was actually made)
* base64Size: size of base64 encoded "responses"
* otherCount: number of other responses
* otherSize: size of other responses

### Cache Hits

> Metrics are calculated based on ``Age`` and ``X-Cache`` headers added by Varnish  / Squid servers

* cacheHits: number of cache hits
* cacheMisses: number of cache misses
* cachePasses: number of cache passes

### Headers

* headersCount: number of requests and responses headers
* headersSentCount: number of headers sent in requests
* headersRecvCount: number of headers received in responses
* headersSize: size of all headers
* headersSentSize: size of sent headers
* headersRecvSize: size of received headers
* headersBiggerThanContent: number of responses with headers part bigger than the response body

### Domains

* domains: number of domains used to fetch the page
* maxRequestsPerDomain: maximum number of requests fetched from a single domain
* medianRequestsPerDomain: median of requests fetched from each domain

### Cookies

* cookiesSent: length of cookies sent in HTTP requests
* cookiesRecv: length of cookies received in HTTP responses
* domainsWithCookies: number of domains with cookies set
* documentCookiesLength: length of `document.cookie`
* documentCookiesCount: number of cookies in `document.cookie`

### DOM complexity

> Metrics listed below are generated after the full page load

* globalVariables: number of JS globals variables
* globalVariablesFalsy: number of JS global variables that cast to false
* bodyHTMLSize: the size of body tag content (``document.body.innerHTML.length``)
* commentsSize: the size of HTML comments on the page
* hiddenContentSize: the size of content of hidden elements on the page (with CSS ``display: none``)
* whiteSpacesSize: the size of text nodes with whitespaces only
* DOMelementsCount: total number of HTML element nodes
* DOMelementMaxDepth: maximum level on nesting of HTML element node
* iframesCount: number of iframe nodes
* nodesWithInlineCSS: number of nodes with inline CSS styling (with `style` attribute)
* imagesWithoutDimensions: number of ``<img>`` nodes without both ``width`` and ``height`` attribute

### DOM queries

* DOMqueries: the sum of all four metrics below
* DOMqueriesById: number of `document.getElementById` calls
* DOMqueriesByClassName: number of `document.getElementsByClassName` calls
* DOMqueriesByTagName: number of `document.getElementsByTagName` calls
* DOMqueriesByQuerySelectorAll: number of `document.querySelectorAll` calls
* DOMinserts: number of DOM nodes inserts
* DOMqueriesDuplicated: number of duplicated DOM queries

### Event listeners

* eventsBound: number of ``EventTarget.addEventListener`` calls

### Window performance

> Times below are relative to ``responseEnd`` entry in NavigationTiming (represented by ``timeToLastByte`` metric). See [NavigationTiming spec](http://w3c-test.org/webperf/specs/NavigationTiming/) for more information.

* onDOMReadyTime: time it took to fire onDOMready event
* onDOMReadyTimeEnd: time it took to finish processing onDOMready event **experimental**
* windowOnLoadTime: time it took to fire window.load event
* windowOnLoadTimeEnd: time it took to finish processing window.load event **experimental**
* timeBackend: time to the first byte compared to the total loading time (in %)
* timeFrontend: time to window on load compared to the total loading time (in %)

### Requests statistics

> **Time** is total duration, from the start of the request to the receipt of the final byte in the response. **Latency** is the time to load the first byte in the response.
> https://developers.google.com/chrome-developer-tools/docs/network
>
> Includes ``HTTP 200`` responses only

* smallestResponse: the size of the smallest response
* biggestResponse: the size of the biggest response
* fastestResponse: the time to the last byte of the fastest response
* slowestResponse: the time to the last byte of the slowest response
* smallestLatency: the time to the first byte of the fastest response
* biggestLatency: the time to the first byte of the slowest response
* medianResponse: median value of time to the last byte for all responses
* medianLatency: median value of time to the first byte for all responses

### localStorage

* localStorageEntries: number of entries in local storage

### jQuery

> Requires jQuery 1.8.0+

* jQueryVersion: version of jQuery framework (if loaded)
* jQueryOnDOMReadyFunctions: number of functions bound to onDOMReady event
* jQuerySizzleCalls: number of calls to [Sizzle](http://sizzlejs.com/) (including those that will be resolved using ``querySelectorAll``)

### Static assets

* assetsNotGzipped: static assets that were not gzipped
* assetsWithQueryString: static assets requested with query string (e.g. ?foo) in URL
* assetsWithCookies: number of static assets requested from domains with cookie set
* smallImages: images smaller than 2 kB that can be base64 encoded
* multipleRequests: number of static assets that are requested more than once

### Caching

* cachingNotSpecified: responses with no caching header sent (either `Cache-Control` or `Expires`)
* cachingTooShort: responses with too short (less than a week) caching time
* cachingDisabled: responses with caching disabled (`max-age=0`)
* oldCachingHeaders: responses with old, HTTP 1.0 caching headers (``Expires`` and ``Pragma``)

### Time to first asset

* timeToFirstCss: time it took to receive the last byte of the first CSS
* timeToFirstJs: time it took to receive the last byte of the first JS
* timeToFirstImage: time it took to receive the last byte of the first image

### Redirects

* redirects: number of HTTP redirects (either 301, 302 or 303)
* redirectsTime: time it took to send and receive redirects

### JavaScript bottlenecks

* documentWriteCalls: number of calls to either ``document.write`` or ``document.writeln``
* evalCalls: number of calls to ``eval`` (either direct or via ``setTimeout`` / ``setInterval``)

### JavaScript errors

> Error message and backtrace will be emitted as offenders

* jsErrors: number of JavaScript errors

### JavaScript console and alert

* windowAlerts: number of calls to ``alert``
* windowConfirms: number of calls to ``confirm``
* windowPrompts: number of calls to ``prompt``
* consoleMessages: number of calls to ``console.*`` functions

### Main request

> Analyzes bits of data pertaining to the main request only

* statusCodesTrail: comma-separated list of HTTP status codes that main request followed through (could contain a single element if the main request is a terminal one)

### Optional metrics

> The following metrics are emitted only when certain options are passed to phantomas

* blockedRequests: number of requests blocked due to domain filtering (emitted only when in `--no-externals` / `--block-domain` mode)

### CSS metrics

> This is an experimental feature. Use `--analyze-css` option to enable it.

Take a look at [analyze-css README](https://github.com/macbre/analyze-css) for the full list of metrics.

### Reporters

phantomas provides a number of reporters that can format the run results and send them to various tools. Use ``--reporter`` (or ``-R`` shortcut) option to use one.

#### Formatters

Results can be emitted as TAP, CSV and JSON. ``plain`` format is most useful for human beings :)

#### Parameters

Formatters can be provided with colon separated list of options:

```
$ phantomas http://foo.net -R csv:no-header:timestamp
```

This will omit CSV headers row and add current timestamp as the first column, so you can append the results line to a growing file.

##### CSV
* ``no-header`` - omit CSV header
* ``timestamp`` - add the current timestamp as the first column
* ``url`` - add the URL as the first column

##### JSON
* ``pretty`` - emits pretty printed JSON

#### StatsD integration

Metrics from phantomas run can be sent directly to [StatsD](http://codeascraft.com/2011/02/15/measure-anything-measure-everything/) and then graphed using [graphite](http://graphite.wikidot.com/), [graphene](http://jondot.github.io/graphene/) or any other tool of your choice. For instance:

```
$ phantomas http://app.net/start -R statsd --statsd-host stats.app.net --statsd-port 8125 --statsd-prefix 'myApp.mainPage.'
```

will sent metrics to StatsD running on ``stats.app.net:8125`` and prefix them with 'myApp.mainPage'.

#### Save metrics to ElasticSearch

Metrics from phantomas run can be outputted directly in ElasticSearch :

##### Parameters

* `--elasticsearch-host=[ip]` ElasticSearch instance ip (default : 127.0.0.1)
* `--elasticsearch-port=[port]` ElasticSearch instance port (default : 9200)
* `--elasticsearch-index=[index_name]` Name of the index to use
* `--elasticsearch-type=[type_name]` Name of the document type to use



## For developers

* [Project's wiki](https://github.com/macbre/phantomas/wiki)
* Description of [events fired by phantomas core](https://github.com/macbre/phantomas/wiki/Events)
* Description of [helper functions available to the browser in window.__phantomas](https://github.com/macbre/phantomas/wiki/Phantomas-scope)

## Let's make Web a bit faster!

* [Best Practices for Speeding Up Your Web Site](http://developer.yahoo.com/performance/rules.html) (by Yahoo!)
* [Web Performance Best Practices](https://developers.google.com/speed/docs/best-practices/rules_intro) (by Google)
* [Writing Efficient CSS](http://developer.mozilla.org/en/Writing_Efficient_CSS) (by Mozilla)
* [Planet Performance](http://www.perfplanet.com/) - news and views from the web performance blogosphere
* [Performance of 3rd Party Content](http://stevesouders.com/p3pc/) (by Steve Souders)
* [Profiling CSS for fun and profit. Optimization notes.](http://perfectionkills.com/profiling-css-for-fun-and-profit-optimization-notes/)
* [phantomas – PhantomJS based, modular web performance metrics generator](http://calendar.perfplanet.com/2013/phantomas/) (an article for Performance Calendar)
* [How to Measure Frontend Performance With Grunt](http://4waisenkinder.de/blog/2013/12/22/how-to-measure-frontend-performance-with-phantomas-and-grunt/)
* [MaxCDN Blog - Behind The Scenes: tools.maxcdn.com](http://blog.maxcdn.com/behind-scenes-tools-maxcdn-com/)

### Slides

* [Know Your Engines: How to Make Your JavaScript Fast](http://cdn.oreillystatic.com/en/assets/1/event/60/Know%20Your%20Engines_%20How%20to%20Make%20Your%20JavaScript%20Fast%20Presentation%201.pdf) (by David Mandelin from Mozilla)
* [Velocity Conf 2013 Workshop: Avoiding Web Performance Regression](http://www.slideshare.net/marcelduran/velocity-conf-2013) (by Marcel Duran from Twitter)

## Videos
* [Sebastian Golasch: The Glitch in the Game (Front End Ops Conference 2014)](http://youtu.be/B_kwo5V6m_w?t=12m25s)

## Utilities

Use [grunt](http://gruntjs.com/) to automate daily dev tasks, including your's application web performance, via these great tools:

* [grunt-phantomas](https://github.com/stefanjudis/grunt-phantomas)
* [juve](https://github.com/jared-stilwell/juve)
* [grunt-devperf](https://github.com/gmetais/grunt-devperf)
