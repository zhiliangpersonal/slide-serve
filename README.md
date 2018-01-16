# slide-serve

i really like the idea behind [slide-html](https://github.com/trikita/slide-html) and the [Takahashi method](https://en.wikipedia.org/wiki/Takahashi_method)

for really, really fast slide-development i created this small server

## features

* just write plaintext files, no need to think about html or indents
  * you can even write more than one plaintext file!
* auto reload the browser on that slide you just edited
  * this is just awesome!
* add styles and background images next to your content
  * no hardcoded slide-numbers in css
  * don't count your slides, i'll do it for you ;)
* syntax highlighter with [chroma](https://github.com/alecthomas/chroma)
* use local images

## usage

quickstart with a pre-build binary for linux, windows or mac: on the [release page](https://github.com/cdreier/slide-serve/releases)

put the file in a place you really like - and start it!  
the `-dir` flag should point to the folder with your presentation

``` bash
./slide-serve -dir how-to-overengineer-3000
```

### your presentation

* your slide-files should end with `.md` 
* `styles.css` is automaticly added to your presentation
* your presentation directory is routed to `http://localhost:8080/static/` so you can access local images or fonts - this is important for your custom stylesheets!

### syntax additions

`@img/imagename.gif`  
`@css/stylesheet.css`  
`@js/jsfile.js`  
`@code/language`  

the path is relative to your presentation directory, if you like to create an image folder it would change to `@img/images/imagename.gif`

with die `@code` annotation, the code-formatting is set for this slide 

note: instead of writing the harcoded slide number, you should use the SLIDENUMBER placeholder

``` css
@code/css
.slide-SLIDENUMBER h1 { 
  color: #fff; 
  text-shadow: 1px 1px 3px #333; 
}
```

example slide with background image and stylesheet:

``` md
# AWESOME
.
@img/example_bg.jpg
@css/whiteHeadline.css
```

with the `@js` annotation, you can add javascript to your slide - the code will run in the moment you enter the slide.

``` md
# javascript!
@js/jsdemo.js
```

2 variables are available: `_slide` is your current slide-div, and `_slideIndex` - is your slide index... obviously

the jsdemo looks like

```js
_slide.style.backgroundColor = "red"
setTimeout(() => {
  _slide.style.backgroundColor = "inherit"
}, 1000)
```


example presentation: https://github.com/cdreier/slide-serve/tree/master/example

> to see the example presentation, just run slide-serve without any flags


### syntax highlighter

i used [chroma](https://github.com/alecthomas/chroma) for syntax highlightning. 

just tell me what language you are using on your slide (with `@code/lang`) and you are good to go.

with the `-syntaxhl` start flag you can set the highlighter theme (there is a [list](https://github.com/alecthomas/chroma/tree/master/styles) with all the themes). the default is monokai

example (the position of the `@code` annotation does not matter)

``` md
# CSS backgrounds
@code/css
  .slide-12 {
    background: url("icon.png"),
                url("bg.png");
    background-repeat: no-repeat, repeat;
    background-size: 40%, auto;
    background-position: bottom right;
  }
```

### build your own

i used [packr](https://github.com/gobuffalo/packr) to embedd the html template in the binary

all the rest is basic go code, with dependencies managed by [dep](https://github.com/golang/dep)

## useful snippets

### restarting gifs

as gifs are looping in the background, this script reloads the gif every time you enter the slide

```js
// restartGif.js
function getBackgroundImageUrl(element){
  var styles = getComputedStyle(element)
  return styles.backgroundImage.replace("url(\"", "").replace("\")", "")
}
var url = getBackgroundImageUrl(_slide)
var reloadUrl = url + "?" + Math.random()
_slide.style.backgroundImage = "url('" + reloadUrl + "')"
```

and in an example slide

```
.
@img/funnyCat.gif
@js/restartGif.js
```

### no image scaling

in the past, i needed a down-scaling of the images - if you want to reset this behaviour, add this to your `styles.css`

```css
.slide {
  background-size: unset !important;
}
```

### add a logo on every slide

you can add more background images - so we add one default image in the `styles.css`

```css
body {
  font-family: Vollkorn;
  background-color: #e2e4e6;
  background-image: url("/static/images/your_logo.png");
  background-repeat: no-repeat;
  background-position: right 10px top 10px;
  background-size: 15%, auto;
}
```

## cli flags

-dev  
* start with `-dev` or `-dev true` to enable auto-reloading (default false)
* still much awesome feature! 

-dir  
* this is the directory of your presentation (default "example")

-port 
* http port the server is starting on (default "8080")

-syntaxhl
* string with the highlighter theme for your code (default "monokai")

-title 
* html title in the browser (default "Slide")