# node-pptx [![Build Status](https://travis-ci.org/heavysixer/node-pptx.svg?branch=master)](https://travis-ci.org/heavysixer/node-pptx)

## Under Development (Do not use)

Generate PPTX files on the server-side with JavaScript.

## Features

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Getting Started](#getting-started)
  - [General Conventions](#general-conventions)
- [Usage](#usage)
  - [Presentation Object](#presentation-object)
    - [Creating a Presentation From Scratch](#creating-a-presentation-from-scratch)
    - [Modifying an existing Presentation](#modifying-an-existing-presentation)
    - [Saving A Presentation](#saving-a-presentation)
    - [Setting Properties](#setting-properties)
    - [Setting Layouts](#setting-layouts)
    - [Setting Text Direction](#setting-text-direction)
  - [Slides](#slides)
      - [Adding Slides](#adding-slides)
      - [Removing Slides](#removing-slides)
      - [Reordering Slides](#reordering-slides)
      - [Formatting Options](#formatting-options)
      - [Applying Master Slides](#applying-master-slides)
      - [Adding Slide Numbers](#adding-slide-numbers)
    - [Adding Content to Slides](#adding-content-to-slides)
      - [Charts](#charts)
        - [Bar Charts](#bar-charts)
      - [Images](#images)
      - [Media Objects](#media-objects)
      - [Text Boxes](#text-boxes)
      - [Shapes](#shapes)
- [Contributing](#contributing)
- [Special Thanks](#special-thanks)
- [License](#license)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Getting Started

```bash
$ npm install node-pptx
```

Let's create a very simple presentation with one slide.

```javascript
const pptx = require('node-pptx');

new pptx.Presentation()
  .compose(pres => {
    pres.title('My Presentation').addSlide(slide => {
      slide
      .addText(text => {
        text
        .value('Hello world')
        .x(10)
        .y(0);
      })
      .addShape(shape => {
        shape.type('circle');
      })
      .addImage(image => {
        image.src('http://www.someurl.com/some-image.jpg');
      });
    });
  })
  .save('/tmp/my-presentation.pptx');
```
### General Conventions
`node-pptx` has a friendly declarative DSL to quickly design a pptx file. This makes your JavaScript code very readable because, it allows you to visually segment and compartmentalize your code to the presentation element you are trying to edit. Here is a simple example of adding a text box to a slide:

```javascript
slide.addText(text => {
  text
  .value('Hello world')
  .x(10)
  .y(0);
})
```
You can also achieve the same result using the more terse object-only format by supplying a configuration object instead of a function.

*Note*: Not all presentation elements support the object-only format.

```javascript
slide.addText({ value:'Hello world', x: 10, y: 0 })
```

## Usage
The following sections gives a detailed run through of the core features of this library as it relates to creating pptx files.

### Presentation Element
The following sections defines the various ways to read, compose, and write pptx files. `node-pptx` allows you to either create a brand new file, or modify an existing pptx file.

#### Creating a Presentation From Scratch
```javascript
new pptx.Presentation()
  .compose(pres => {
    pres.title('My Presentation')
  })
  .save('/tmp/my-presentation.pptx');
```

#### Modifying an existing Presentation
If you would like to use an modify an existing pptx file, simply load it first.
```javascript
new pptx.Presentation()
  .load('/tmp/fixtures/basic.pptx')
  .compose(pres => {
    pres.title('My Presentation')
  })
  .save('/tmp/my-presentation.pptx');
```

#### Saving A Presentation
```javascript
new pptx.Presentation()
.save(`${tmpDir}/some-file.pptx`);
```

#### Setting Properties
```javascript
new pptx.Presentation()
  .compose(pres => {
    pres
      .title('My Presentation')
      .author('Mark Daggett')
      .company('Humansized Inc.')
      .revision('20')
      .subject('My Presentation')
  })
```

#### Setting Layouts
TODO

#### Setting Text Direction
TODO

### Slides
Slides are are by far the most complex feature of this library because they are the backbone for all presentations.

##### Adding Slides
```javascript
new pptx.Presentation()
  .compose(pres => {
    pres
    .title('My Presentation')
    .addSlide(slide => {
      // design your slide here.
    })
  })
```

##### Removing Slides
If you've assigned a `name` which is a special internal property to your slide
you can reference it by name otherwise you will need to remove it based on index.

```javascript
new pptx.Presentation()
  .compose(pres => {
    pres
    .addSlide(slide => {
      slide.name('my-slide')
    })
    .removeSlide('my-slide')
    .addSlide()
    .removeSlide(0) // Remove the first slide (zero-based index)
  })
```
##### Reordering Slides
TODO

##### Formatting Options

Applying Background Colors

```javascript
pres
.addSlide(slide => {
  slide.backgroundColor('my-slide')
})
```

##### Applying Master Slides
TODO

##### Adding Slide Numbers
TODO

#### Adding Content to Slides
This library supports charts, images, text boxes, and shapes. The following section describes the ways in which you can add these elements. to a slide.

Objects are layered on top of one another in the order in which they are added. Therefore you'll want to add background items first and gradually work your way towards the top of the composition.

##### Charts
Charts have very minimal support right now, think of it mostly as a proof of concept at this point.

###### Bar Charts

```javascript
let barChartData = [
    {
        name: 'Series 1',
        labels: ['Category 1', 'Category 2', 'Category 3', 'Category 4'],
        values: [4.3, 2.5, 3.5, 4.5],
    },
    {
        name: 'Series 2',
        labels: ['Category 1', 'Category 2', 'Category 3', 'Category 4'],
        values: [2.4, 4.4, 1.8, 2.8],
    },
    {
        name: 'Series 3',
        labels: ['Category 1', 'Category 2', 'Category 3', 'Category 4'],
        values: [2.0, 2.0, 3.0, 5.0],
    },
];

slide.addChart(chart => {
  chart
    .type('bar')
    .data(barChartData)
    .x(100)
    .y(100)
    .cx(400)
    .cy(300)
});
```

##### Images
```javascript
// You can add an image by specifying a remote source
slide.addImage(image => {
  image.src(`${__dirname}/images/pizza.jpg`)
})

// Images can also be created using base64 encoded strings.
slide.addImage(image => {
  image.data('iVBORw0KGgoAAAANSUhEUgAAACAAAA[...]iVBORw0K')
});
```
##### Media Objects
The pptx spec calls for support of media objects (video & audio) however presently `node-pptx` doesn't support these objects.

##### Text Boxes
As the name suggests text can be added to the slide using `addText`.  The text box element also supports the creation of external links (which open a web browser) and internal linking (which link to another slide in the same presentation).

To create an external link specify the full URI path as the value for the `url` key.
```javascript
defaultSlide.addText({ value: 'This is a hyperlink!', x: 0, y: 25, cx: 400, url: 'http://www.google.com' });
```

To link to another slide specify the slide number preceded with a hash like so:
```javascript
defaultSlide.addText({value: 'This go to slide 3', x: 0, y: 50, url: '#3' });
```
##### Shapes
For a full list of the supported shapes check the
[shape-types](https://github.com/heavysixer/node-pptx/blob/master/lib/shape-types.js) file.

```javascript
//Creating a shape using the object-only syntax.
slide.addShape({ type: PPTX.ShapeTypes.TRIANGLE, x: 50, y: 50, cx: 50, cy: 50 });

//Creating a shape using the composable DSL.
slide.addShape(shape=> {
  shape
    .type(PPTX.ShapeTypes.TRIANGLE)
    .x(150)
    .y(50);
});
```

## Testing
To run the unit tests for `node-pptx` simply type this into your commandline:

`yarn test`

You can also see the code coverage metrics as well which will be written to the `coverage/` directory.

## Contributing

Send issues and pull requests with your ideas.

[Good First Issue](https://github.com/heavysixer/node-pptx/labels/Good%20First%20Issue) is a great starting point for PRs.

## Special Thanks

This project builds off the hard work of several other PPTX libraries namely:

* <https://github.com/gitbrent/PptxGenJS>
* <https://github.com/Ziv-Barber/officegen>
* <https://github.com/won21kr/js-pptx>

## License

MIT © [Mark Daggett](https://github.com/heavysixer) & Greg Dolley
