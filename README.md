# TikZJax

TikZJax converts `<script>` tags (containing TikZ code) into SVGs.

See a live demo at [tikzjax.com](http://tikzjax.com/).

Note that the demo above is not the same as what you will get from what this branch of my fork. However, it does show
the general concept.

This is a fork of Glenn Rice's ([drgrice1](https://github.com/drgrice1)) work with fixes and improvements to enhance compatibility and functionality.

Thanks to Jim Fowler for doing all of the hard work. See [kisonecat/tikzjax](https://github.com/kisonecat/tikzjax),
[kisonecat/web2js](https://github.com/kisonecat/web2js), and [kisonecat/dvi2html](https://github.com/kisonecat/dvi2html)
for his original work.

Also see [jhoobergs/web2js](https://github.com/jhoobergs/web2js) for additional changes that were made by Jesse Hoobergs
that were used in this work.

## Installation

In the `<head>` of your HTML, include the following CDN links:

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@planktimerr/tikzjax@1.0.7/dist/fonts.css">
<script src="https://cdn.jsdelivr.net/npm/@planktimerr/tikzjax@1.0.7/dist/tikzjax.js"></script>
```

## Example

Then in the `<body>`, include TikZ code such as

```html
<script type="text/tikz">
 \begin{tikzpicture}
 \draw (0,0) circle (1in);
 \end{tikzpicture}
</script>
```

The TikZ code will be compiled into an SVG image, and the `<script>` element will be replaced with the generated SVG
image.

## Options

There are several data attributes that can be set for a "text/tikz" `<script>` tag that affect the generation of the
resulting SVG image, or change the way the TikzJax javascript behaves.

The values of the `data-width` and `data-height` attributes set on the `<script>` tag will be used for the width and
height of a loader image. This is an svg image that is displayed while TeX is being run to generate the svg image, and
contains a spinner to indicate to the user that work is being done. These dimensions are in points.

Use `data-tex-packages` to load and use TeX packages. The value of this attribute must be a string that will parse to a
valid javascript object via the javascript JSON.parse method. The keys of the object should be the TeX package names,
and the value of each key should be the package options to set. For example:

```html
<script type="text/tikz" data-tex-packages='{ "array": "", "pgfplots": "", "custom-package": "option=special" }'>
```

will add

```tex
\usepackage{array}
\usepackage{pgfplots}
\usepackage[option=special]{custom-package}
```

to the preamble of the TeX input. Note that TeX packages must be loaded in this way. This will ensure that the needed
TeX system files are made available to the TeX WebAssembly for successful compilation. Note that the TeX packages that
are available at this time are `amsbsy`, `amsfonts`, `amsgen`, `amsmath`, `amsopn`, `amssymb`, `amstext`, `array`,
`etoolbox`, `hf-tikz`, `pgfplots`, `tikz-3dplot`, `tikz-cd`, and `xparse`. Additional packages can be made available by
adding the gzipped TeX files used by the package to the `tex_files` directory.

Use `data-tikz-libraries` to load and use TikZ libraries. For example:

```html
<script type="text/tikz" data-tikz-libraries="arrows.meta,calc">
```

will result in

```tex
\usetikzlibrary{arrows.meta,calc}
```

being added to the preamble of the TeX input. As with TeX packages, TikZ libraries must be loaded in this way to ensure
that the needed TeX system files are made available to the TeX WebAssembly for successful compilation. Note that all
known TikZ libraries are available (with the exception of some that don't make sense in this context, like the external
library).

Use `data-add-to-preamble="..."` to add to the TeX preamble.

An SVG title can be added for screen reader users by setting `data-aria-label`.

The `data-disable-cache` attribute can be set to `true` to disable caching of an image in the indexed database.

Use `data-show-console="true"` to enable the output of TeX in the console. By default, console output is disabled and
nothing is shown in the browser console.

## CSS Classes

For your convenience, some css classes are provided that will apply common styles to the svg image. To use these classes
place the "text/tikz" script tags inside an html element with one of the following classes.

If you add the css class `tikzjax-container` to the containing element, then `overflow:visible` will be added to the
style of the generated `<svg>` image.

If you add the css class `tikzjax-scaled-container` to the containing element, then `overflow:visible`, `width:100%`,
and `height:100%` will be added to the style of the generated `<svg>` image.

## Other JavaScript Interactions

Note that once tikzjax completes the generation of an SVG image, the generated `<svg>` image will emit the
`tikzjax-load-finished` event. You can use this event to do something with the generated SVG image in javascript.

For example:

```javascript
document.addEventListener('tikzjax-load-finished', function(e) {
    var svg = e.srcElement;
    ...
});
```