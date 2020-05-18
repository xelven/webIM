# webIM

## Overview

Easy & quicky to setup a web IM plugin base RongCloud services.



## Setup
#### Env setup
Step 1. install related env tools grunt: https://gruntjs.com/getting-started
```
npm install -g grunt-cli
npm install grunt --save-dev
npm install grunt-contrib-jshint --save-dev

```

#### project setup
Step 2. install all project related lib.
```
cd *project root folder*
npm install
```

## Run IM web in your local
#### All command line with grunt
* Compressed source code for deploy
```
grunt dist

```

* run your local server
```
grunt connect:server:keepalive
```
after running local static server succeed, then u can go to https://localhost:9001/im.html



## project struct:
```

|-dist (release-merge compressed code)
|
|-templates (HTML templates)
|
|-im.css (Style sheet)
|
|-im.html (Development test page)
|
|-im.js  (im core code)
|
|-emoji.js (Expression library call package)
|
|-libs/qiniu-upload.js  (Upload in chat plugin)
|
|-libs/utils.js (Tools)
```


## config file:
can modify grunt command line & hostname/port,etc by config (Gruntfile.js)
```
'use strict';
var glob = require('glob');
var fs = require('fs');
module.exports = function(grunt) {
    grunt.initConfig({
        connect: {
            server: {
              options: {
                port: 9001,
                hostname: '*',
                base: './',
                protocol: 'http2'
              }
            }
        },
        concat: {
            dist: {
                src: [
                    'libs/RongEmoji.js',
                    'libs/utils.js',
                    'libs/qiniu-upload.js',
                    'template.js',
                    'emoji.js',
                    'im.js'
                ],
                dest: 'temp/im.js'
            }
        },
        uglify: {
            dist: {
                src: [
                    'temp/im.js',
                    'temp/template.js'
                ],
                dest: 'dist/im.min.js'
            }
        },
        cssmin: {
            dist: {
                src: 'im.css',
                dest: 'dist/im.min.css'
            }
        },
        clean: {
            dist: {
                src: 'temp'
            }
        }
    });

    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    grunt.loadNpmTasks('grunt-contrib-cssmin');
    grunt.loadNpmTasks('grunt-contrib-clean');
    grunt.loadNpmTasks('grunt-contrib-connect');

    grunt.registerTask('html-to-js',function () {
        var template = {};
        glob.sync('./templates/**/*.html').forEach(filePath => {
            var content = fs.readFileSync(filePath, {
                encoding: 'utf8'
            });
            var key = filePath.replace(/^\.\//, '');
            template[key] = content;
        });
        var dest = './temp/template.js';
        var outputContent = 'RCS.templateCache=' + JSON.stringify(template, null, 4) + ';';
        fs.writeFileSync(dest, outputContent);
    });

    grunt.registerTask('dist', ['concat', 'html-to-js', 'uglify', 'cssmin', 'clean']);
}

```

