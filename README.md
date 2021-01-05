# kohai.js

A miniature, primarily SOC/encapsulation web design paradigm for personal use. Model-oriented, but generally light and less opinionated: all components are defined as singleton objects that merely encapsulate our JS/HTML/CSS with a rudimentary css-in-js parser. Syntax highlighting is achieved in our JS using tagged template literals.

Currently compiles TypeScript using Babel.


### gulp "build-site" to build site to dist folder

build-site builds the following from an src folder to dist:

* Asset files, e.g. images, videos, music, etc.
  
* A root CSS folder, which is minified, cleaned and prefixed.
  
* A typescript folder, which is compiled into a singular AMD module currently to generate component blueprints and helper functions.
  
* A javascript folder, which supports hosting and loading libraries.
  
* HTML structure copying, e.g. from index and nested pages.


## A component might look like this:

```
COMPONENTS.example = {
  styles: {
    container: /*css*/ `
            display: block;
        `,

    stuff: /*css*/ `
            width: 200px;
            height: 200px;
            background-color: orange;

            :hover {
              background-color: blue;
            }

            :after {
              content: 'abc';
            }

            :before {
              content: 'abc';
            }

            @media (max-width: 1000px) {
              background-color: green !important;
            }
        `,
  },

  get get() {
    return /*html*/ `
      <div class=${this.styles.container}>
        <div class=${this.styles.stuff}>
        </div>
        ${COMPONENTS.hr.get({color: 'orange', width: '50%', height: '1px'})}
      </div>
      `;
  },
  
  get write() {
     document.body.insertAdjacentHTML('beforeend', this.get);
    return;
  },
};
```
Components are written sequentially into an HTML page in the body tag via their "write" function. We can also run arbitrary encapsulated code via this function, as it will be called in the root HTML.

Our root compiled js is loaded into every html page in the head, to prevent pop-in. Our CSS is also loaded explicitly into the html file for the same reason.

Herein we define styles with our CSS-in-JS (media queries, after, before, and hover currently supported), define what is returned when the component get function is called (the HTML), and what is returned when the component write function is called.

You can nest components by calling a component's get function within another component. For example,

```
COMPONENTS.example = {
  get get() {
    return  /*html*/`
        ${COMPONENTS.example2.get}
        ${COMPONENTS.example2.get}
      `
  },

  get write() {
    document.body.insertAdjacentHTML('beforeend', COMPONENTS.example.get);
    return;
  }
};
```

Your HTML page writing our components might look like this:

```
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <link href="/./CSS/root.min.css" type="text/css" rel="stylesheet" />
    <!-- render root script in head to prevent pop-in -->
    <script src="./js/root.min.js"></script>
</head>

<body>
    <script>
    // components to load
        COMPONENTS.head.write;
    // this produces two copies of the example component
        COMPONENTS.example.write;
        COMPONENTS.example.write;
    </script>
    <script src="/./dist/js/Library/example-API.js"></script>
</body>

</html>
```
