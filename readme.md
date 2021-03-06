# ng-falcor: Use Falcor in Angular 1.x Projects

## Installation

```
npm install ng-falcor
```

Alternatively, copy the [UMD](https://github.com/umdjs/umd) file `dist/ng-falcor.js` in this repo and put it wherever you want.

## How does it work?

See the [Falcor website](https://netflix.github.io/falcor/) for how Falcor works.
This lib provides an Angular factory that wraps a Falcor model and exposes it to your logic and templates.
Subsequently, Angular bindings operate against a single source of truth consisting of a Falcor model containing all your application data.
From then on, it's simply a matter of manipulating the JSON graph via the model.

**Note:** this lib is pre-1.0.
Pending any feedback and/or lessons learned it may change substantially before hitting 1.0.

## API

### `ngFalcor.create(opts)`

The main export of `ng-falcor` has but one method `create()` which returns an Angular factory function.
Pass it an options hash:

 * **router** (string) If provided, a new [`falcor.HttpDataSource`](https://netflix.github.io/falcor/documentation/datasources.html) is created using this and added to the model.
 * **headers** (object) An object containing HTTP request headers. If provided, the falcor HttpDatasource will send these headers along with every request.
 * **timeout** (number) If provided, falcor HttpDatasource requests will timeout after this many milliseconds.
 * **cache** (object) Pre-populate the model cache. Useful for bootstrapping data for example.

### `ngf`

This is the singleton object that gets injected into your controllers by the factory.
You can name it whatever you want, but `ngf` is nice and short.
It has several methods:

 * `ngf('path.to.something')` or `ngf('path', 'to', 'something')` - Synchronous getter. Will trigger a router request as a side effect if the value isn't found in the model. As usual with Falcor, the provided path must point to a leaf node in the graph.
 * `ngf.twoWay(path)` - Returns a function that serves as an `ng-model` in a two-way binding scenario. Must be used in conjunction with `ng-model-options="{getterSetter:true}"`. Be aware this will send `set` requests to your Falcor model as the user interacts with the bound element.
 * `ngf.get(...args)` - Alias to [`get(...args)`](https://netflix.github.io/falcor/doc/Model.html#get) on the internal Falcor model.
 * `ngf.getValue(...args)` - Alias to [`getValue(...args)`](https://netflix.github.io/falcor/doc/Model.html) on the internal Falcor model.
 * `ngf.set(...args)` - Alias to [`set(...args)`](https://netflix.github.io/falcor/doc/Model.html#set) on the internal Falcor model.
 * `ngf.callModel(...args)` - Alias to [`call(...args)`](https://netflix.github.io/falcor/doc/Model.html#call) on the internal Falcor model (named `callModel` to avoid colliding with JS's `Function#call`).
 * `ngf.invalidate(...args)` - Alias to [`invalidate(...args)`](https://netflix.github.io/falcor/doc/Model.html#invalidate) on the internal Falcor model.
 * `ngf.configure(opts)` - Reconfigure your `ngf` object. Accepts same options as `ngFalcor.create(options)`. Warning: this has the side effect of deleting all cached Falcor data.

## Example

```js
import ngFalcor from 'ng-falcor';

angular.module('foo', [])
.factory('ngf', ngFalcor.create({
  router: '/model.json',
  cache: { ... }
}))
.controller('myCtrl', function($scope, ngf) {
  $scope.ngf = ngf;
});
```

```html
<img src="{{ ngf('users.u12345.avatar.src') }}"/>
<button ng-click="ngf.set('users.u12345.isOnline', true)"/>
<button ng-click="ngf.callModel('users.create')"/>
<checkbox ng-model="ngf.twoWay('users.u12345.isOnline')" ng-model-options="{ getterSetter: true }"/>
```

## Credit

Credit due to [@rolaveric](https://github.com/rolaveric/angular-falcor) for inspiration and providing some useful pieces to this puzzle. And of course the [Falcor](https://netflix.github.io/falcor/) and [Angular](https://angularjs.org/) teams.
