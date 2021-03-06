# Intro to VueDart

<div id="welcome"></div>

## Welcome

Welcome to the VueDart docs!

First off, note that this documentation assumes you already know how Vue works. If you
don't, then first check out the [official Vue guide](https://vuejs.org/v2/guide/), then
come back once you're done.

Also, the examples shown in this documentation are available
[on GitHub](https://github.com/kirbyfan64/vuedart/tree/master/example).

<div id="first-steps"></div>

## First steps

Let's define a basic app with VueDart. Create a new directory:

```bash
$ mkdir my-project
$ cd my-project
```

Now create a new file `web/index.dart` and add this:

```dart
import 'package:vue/vue.dart';


@VueApp(el: '#app')
class App extends VueAppBase {
}

App app;

void main() {
  app = App();
  app.create();
}
```

For those familiar with Vue, this should be rather self-explanatory. Here, *App* is the
equivalent of a global *Vue* instance, and *app.create* connects . The *VueApp* annotation is
where you declare which element is your root.

Also, add this to `web/index.html`:

```html
<!DOCTYPE html>

<head>
  <title>VueDart first example</title>

  <script src="https://unpkg.com/vue"></script>

  <script defer src="index.vue.dart.js"></script>
  <script defer src="packages/browser/dart.js"></script>
</head>

<body vuedart>
  <div id="app">
    <p>My first Vue app!</p>
  </div>
</body>
```

Again, this should be pretty self-explanatory. Interesting things to note:

- The filename is `index.vue.dart.js`, not `index.dart`. The VueDart transformer will compile
  `index.dart` into `index.vue.dart`, and then the compiler will compile it to
  `index.vue.dart.js`.
- The `defer` tag is to prevent your Dart code from running before the page has finished
  loading.

Now's to build it! Make a `pubspec.yml` that looks like this:

```yaml
name: vuedart_example
version: 0.1.0
description: VueDart example app
author: Foo Bar
environment:
  sdk: ">=2.0.0 <3.0.0"
dependencies:
  vue: any
dev_dependencies:
  build_runner: any
  build_web_compilers: any
```

The `dev_dependencies` section

Before running our new VueDart web app, we need to install the `webdev` package:

```bash
$ pub global activate webdev
```

Now that everything's been put together, run:

```bash
$ pub get
$ webdev serve
```

and open your browser to `localhost:8080`. Congratulations, you just made your first
VueDart app!

### Addendum #1: Why dartdevc?

As a quick side note, Dart has two different Dart-to-JavaScript compilers. dart2js, the
default, is designed for production use; it has powerful, awesome optimizations, but it's
also really slow. DDC, the Dart Dev Compiler, is designed for development like we're
doing; it has faster compile times and compiles down to readable ES6 code to make it
easier to debug.

### Addendum #2: Building for production

If you want to deploy a VueDart app in production, you'll want to run `webdev build`; while
`webdev serve` defaults to debug mode builds, `webdev build` defaults to release mode.
<div id="data"></div>

## Declaring data

This app is cool and all...but it's not actually *doing* anything. Let's read in a name
and show it to the screen.

Modify your app class to look like so:

```dart
@VueApp(el: '#app')
class App extends VueAppBase {
  @data
  String name = '';
}
```

This is how you declare data in VueDart: the *data* annotation. Just put it on an instance
variable, and VueDart will compile it into Vue data accesses. Now it's time to read in
a name in our HTML file:

```html
<body>
  <div id="app">
    <input v-model="name">
    <p>Your name is: {{name}}</p>
  </div>
</body>
```

Now run your app, enter a name, and watch the fireworks. (Not really, but actual fireworks
take far too much effort.)

<div id="methods"></div>

## Declaring methods

What if we want to, for instance, capitalize the name? One approach to this is to
use methods:

```dart
@VueApp(el: '#app')
class App extends VueAppBase {
  @data
  String name;

  @method
  void capitalize(String str) => str.toUpperCase();
}
```

```html
<body>
  <div id="app">
    <input v-model="name">
    <p>Your name is: {{capitalize(name)}}</p>
  </div>
</body>
```

As you can see, declaring Vue methods is the same as declaring a normal method, except
for the `@method` decorator.


<div id="computed"></div>

## Declaring computed data

Of course, a better approach in this case would be to use computed data. Here's how
that's done in VueDart:

```dart
@VueApp(el: '#app')
class App extends VueAppBase {
  @data
  String name = '';

  // The computed data
  @computed
  String get capitalizedName => name.toUpperCase();
}
```

```html
<body>
  <div id="app">
    <input v-model="name">
    <p>Your name is: {{capitalizedName}}</p>
  </div>
</body>
```

You can also define setters on computed properties using the normal Dart setter syntax:

```dart
@computed
void set capitalizedName(String newValue) { /* ... */ }
```

<div id="watchers"></div>

## Watchers

Just like everything else here, watchers work in similar manner to Vue, except with
a more Dart-ified syntax.

```dart
@VueApp(el: '#app')
class App extends VueAppBase {
  @data
  int value1 = 0, value2 = 0, value3 = 0;

  @watch('value1')
  void watchValue1() => print('Watching value1');
  @watch('value2')
  void watchValue2(int newValue) => print('Watching value2');
  @watch('value3', deep: true)
  void watchValue3(int newValue, int oldValue) => print('Watching value3');
}
```

<div id="cli"></div>

## Using the VueDart CLI to create your projects

VueDart also as a CLI you can use to generate projects from templates. To get started,
install it:

```
$ pub global activate vue_cli
```

and then create your project:

```
$ vuedart create my-project
```

This will create a directory `my-project` with everything you need for a basic VueDart
project.
