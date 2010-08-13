# connect-assetmanager-handlers

Post and pre hooks for [connect-assetmanager](http://github.com/mape/connect-assetmanager)

## Installation

Via [npm](http://github.com/isaacs/npm):

    $ npm install connect-assetmanager-handlers
## Usage
    var assetManager = require('connect-assetmanager');
    var assetHandler = require('connect-assetmanager-handlers');
    var assets = assetManager({
        'css': {
            'route': /\/static\/css\/[0-9]+\/.*\.css/
            , 'path': './public/css/'
            , 'dataType': 'css'
            , 'files': [
                'reset.css'
                , 'client.css'
            ]
            , 'preManipulate': {
                , '^': [
                    assetHandler.yuiCssOptimize
                    , assetHandler.fixVendorPrefixes
                    , assetHandler.fixGradients
                    , assetHandler.replaceImageRefToBase64(root)
                ]
            }
        }
    });
## Handlers
### yuiJsOptimize
Uses YUI Compressor to compress the given javascript files.
### yuiCssOptimize
Uses YUI Compressor to compress the given CSS files.
### fixVendorPrefixes
Replaces -vendor with multiple versions of the same line for most vendor prefixes.

#### Example
    -vendor-border-radius: 5px;

Turns into: 

    border-radius: 5px;
    -moz-border-radius: 5px;
    -webkit-border-radius: 5px;
    -o-border-radius: 5px;
    -ms-border-radius: 5px;

### fixGradients
Enables easy use of top to bottom gradients cross browser.

#### Example
    gradient:rgba(10,10,10,0.5)_rgba(10,10,10,0.2);

Turns into:

    background-image: -webkit-gradient(linear,0% 0,0% 100%,from(rgba(10,10,10,0.5)),to(rgba(10,10,10,0.2)));
    background-image: -moz-linear-gradient(top,rgba(10,10,10,0.5),rgba(10,10,10,0.2));
    filter: progid:DXImageTransform.Microsoft.gradient(startColorStr='#7faaa',EndColorStr='#33aaa');
    -ms-filter: "progid:DXImageTransform.Microsoft.gradient(startColorStr='#7faaa', EndColorStr='#33aaa')";

### replaceImageRefToBase64
Looks for data-url(filepath/file.png) in the CSS and replaces those with the contents of the image, base64 encoded.
#### Setup
    assetHandler.replaceImageRefToBase64(__dirname + '/public')

### stripDataUrlsPrefix
Simply replaces `data-url` with `url`. Used as a complement with replaceImageRefToBase64 if you want to serve a css to IE6-7.

### fixFloatDoubleMargin
Finds all blocks containing floats and add a display: inline; (unless there is another display set in that block) to fix double margin bugs. 