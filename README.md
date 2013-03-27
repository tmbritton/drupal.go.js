drupal.go.js
------------------------

A node.js tool to automate and test Drupal using Zombie.js.

Introduction
========================
This node.js package provides an easy way to automate and test Drupal using
the <a href='http://zombie.labnotes.org'>Zombie.js</a> framework.  It provides some helpers that make working with
Drupal within Zombie.js easy and fun.

Installation & Usage
=====================
Since this is a node.js library, you can install it and include it in your node.js
package using ```npm install drupalgo```

Configuration
==============
The configuration for your automation and testing should be placed within a
configuration file in ```*.json``` form.  This file contains your configurations
for your Drupal installation as well as other configurations that you would like
to include in your automation and testing, such as content creation, etc.
```
{
  "host": "http://drupal.local/",
  "user": "admin",
  "nodes": [
    {
      "type": "article",
      "title": "Hello There",
      "body": "This is very cool!"
    },
    {
      "type": "article",
      "title": "This is another node",
      "body": "Nice!"
    }
  ]
}

```

Example
==============
You can now include this library in your test application and then do stuff
using the simple ```drupal.go``` command.  Since this library uses
<a href='http://zombie.labnotes.org'>Zombie.js</a> for its core browser, you can
pass along any Zombie.js command from it's <a href="http://zombie.labnotes.org/API">Zombie.js API</a>
to the ```drupal.go``` command like so...

```
drupal.go('pushButton', '#edit-submit')
```

This function returns a Promise to be used with the popular <a href="https://github.com/caolan/async">Async.js</a>
library making it very simple to build intuitive tests without falling into <a href="https://www.google.com/search?q=javascript+callback+hell">callback hell</a>.

Here is an example of how simple it is to write some automation with this library...

Login, then create some nodes....
```
var async =     require('async');
var drupal =    require('drupalgo');

// Load the config.json file... as seen above.
var browser = drupal.load('config.json');

// Login, then create some content...
async.series([
  drupal.go('login'),
  drupal.go('createMultipleContent', drupal.config.get('nodes'))
], function() {
  console.log('We are done!');
});
```

Extending
================
This library is super easy to extend and create your own Drupal processes.  You
can do so by simply attaching them to the Drupal object and then they can be
utilized with ```drupal.go``` like other processes.  Here is an example.

```
var async =     require('async');
var drupal =    require('drupalgo');

// Add a new task to edit the node.
drupal.editNode = function(done) {
  async.series([
    this.do('visit', 'node/1234/edit'),
    this.do('fill', '#edit-title', 'Something else!'),
    this.do('pressButton', '#edit-submit')
  ], done);
};

// Load the config.json file... as seen above.
var browser = drupal.load('config.json');

// Login, then create some content, then EDIT A NODE!...
async.series([
  drupal.go('login'),
  drupal.go('createMultipleContent', drupal.config.get('nodes')),
  drupal.go('editNode')
], function() {
  console.log('We are done!');
});
```

Happy Automating....
