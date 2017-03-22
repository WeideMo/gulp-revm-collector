
# [gulp](https://github.com/WeideMo/gulp-revm-collector)-revm-collector

## About

>  gulp-revm-collector is a modified version of gulp-rev-collector based on the main processing for appending the hash to filename with gulp-revm, in the plugin you can use all API as in gulp-rev-collector.gulp-revm through the gulp-revm can easily solve the page loading process cache with file-generated md5.

## Install

```sh
$ npm install --save gulp-revm-collector
```

## Usage

We can use [gulp-revm](https://github.com/WeideMo/gulp-revm) to cache-bust several assets and generate manifest files for them. Then using gulp-revm-collector we can collect data from several manifest files and replace lincks to assets in html templates.

```js
var gulp = require('gulp');
var rev = require('gulp-revm');

gulp.task('css', function () {
    return gulp.src('src/css/*.css')
        .pipe(rev())
        .pipe(gulp.dest('dist/css'))
        .pipe( rev.manifest() )
        .pipe( gulp.dest( 'rev/css' ) );
});

gulp.task('scripts', function () {
    return gulp.src('src/js/*.js')
        .pipe(rev())
        .pipe(gulp.dest('dist/js'))
        .pipe( rev.manifest() )
        .pipe( gulp.dest( 'rev/js' ) );
});

...

var revCollector = require('gulp-revm-collector');
var minifyHTML   = require('gulp-minify-html');

gulp.task('rev', function () {
    return gulp.src(['rev/**/*.json', 'templates/**/*.html'])
        .pipe( revCollector({
            replaceReved: true,
            dirReplacements: {
                'css': '/dist/css',
                '/js/': '/dist/js/',
                'cdn/': function(manifest_value) {
                    return '//cdn' + (Math.floor(Math.random() * 9) + 1) + '.' + 'exsample.dot' + '/img/' + manifest_value;
                }
            }
        }) )
        .pipe( minifyHTML({
                empty:true,
                spare:true
            }) )
        .pipe( gulp.dest('dist') );
});
```

### Options

#### replaceReved

Type : `Boolean`

You set a flag, replaceReved, which will replace alredy replaced links in template's files. Default value is `false`.

#### dirReplacements

Specifies a directories replacement set. [gulp-revm](https://github.com/WeideMo/gulp-revm) creates manifest files without any info about directories. E.c. if you use dirReplacements param from [Usage](#usage) example, you get next replacement:

```
"/css/style.css" => "/dist/css/style.css?1d87bebe"
"/js/script1.js" => "/dist/script1.js?61e0be79"
"cdn/image.gif"  => "//cdn8.example.dot/img/image.gif?35c3af8134"
```

#### revSuffix

Type : `String`

It is pattern for define reved files suffixes. Default value is '-[0-9a-f]{8,10}-?'. This is necessary in case of e.c. [gulp-rename](https://github.com/hparra/gulp-rename) usage. If reved filenames had different from default mask.


### Works with gulp-revm-collector

- [gulp-revm](https://github.com/WeideMo/gulp-revm)