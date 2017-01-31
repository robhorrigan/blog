
### Blog of Rob Horrigan
A hugo blog site hosted on Github pages. Updates deployed via Wercker CI.

#### Build
```
hugo
```

#### Run server with Ghostwriter theme
```
hugo server -t ghostwriter
```

#### Create a new post
```
hugo new post/hello-world.md
```

#### Create a new page
```
hugo new page/about.md
```

#### Create a new project
```
hugo new project/todo.md
```

#### Code Snippets with [Google Code-prettify](https://github.com/google/code-prettify)
To embed a code snippet, just wrap code in a `<pre>` tag with `class="prettyprint"`
```
<pre class="prettyprint">
module.exports = function () {
  var element = document.createElement('h1');

  element.innerHTML = 'Hello world!';

  return element;
};
</pre>
```
