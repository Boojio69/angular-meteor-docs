{{#template name="tutorials.socially.angular1.step_23.md"}}
{{> downloadPreviousStep stepName="step_22"}}

In this chapter we will be upgrading Socially from Angular1 to Angular2 using Angular's [Upgrade Adapter](https://angular.io/docs/ts/latest/guide/upgrade.html). Why upgrading? Because Angular2 is the next major version of the framework and it will surely be the version we want to go with when building web apps in the future, and we can enjoy many of its benefits like better performance, server-side rendering, more powerful templating, better ecosystem and more. We will also be transforming our JavaScript code into [TypeScript](https://www.typescriptlang.org/) inspired by Angular2 team's recommendation.

## Switching to TypeScript

First, we will remove the JavaScript compiler, whos belonging Meteor package is `pbastowski:angular-babel`:

    meteor remove pbastowski:angular-babel

Although it is possible to just replace the old JavaScript compiler with a TypeScript compiler, we would like to reserve `ng-annotate`'s compiler as well, since the updating process is gonna be done gradually and our app is gonna be a hybrid of Angular1 and Angular2. Thus, we're gonna install the following package:

    meteor add mys:typescript-ng-annotate

Once our app is fully migrated to Angular2, we will replace `mys:typescript-ng-annotate` with `barbatus:typescript`, a package which will provide you with a pure TypeScript compiler. TypeScript compiler also relies on [declerations](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html), which can be installed via package manager called `typings`. Further informations about `typings` and how to install it can be found [here](https://github.com/typings/typings). This step is very long and its purpose is only to provide you with the right tools on how to upgrade your app, hence we're not going to focus on TypeScript deceleration files any further, but know that you might get related warnings during compilation time!

Now that the compiler is ready we will start by switching our entry file into TypeScript:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.3"}}

Not only we changed the extension of the file, but we also made some adjustments to its content. All Angular1 modules registrations are now manually invoked, due to dependency on the upgrade who's gonna take part further in this tutorial. In addition, the way we import the `angular` library have changed.

Before:

```js
import angular from 'angular';
```

After:

```js
import * as angular from 'angular';
```

Now we gonna go through a component transformation. We will start with the Socially component since it is the root component of our app. We will first change the way we import Angular-related libraries, just like demonstrated above:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.4"}}

Second, we will specify the exported module as an Angular1 module, since we're dealing with two module systems which come both from Angular1 and Angular2, and we will implement a registration which is called from the entry file like we showed earlier:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.5"}}

Now we gonna simply switch **all** the file extensions from `.js` to `.ts` and repeat the recent process for each component. This process is a pain so if you don't wanna deal with it just git-checkout the next step of this tutorial.

Once your'e done we shall change the way we import the `underscore` library otherwise our app might break:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.7" filename="imports/api/parties/methods.ts"}}

## Upgrading to Angular2

Since we wanna use Angular2 we will have to install its essential packages:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.8"}}

Raw commands are listed below:

    $ npm install --save @angular/common
    $ npm install --save @angular/compiler
    $ npm install --save @angular/core
    $ npm install --save @angular/forms
    $ npm install --save @angular/platform-browser
    $ npm install --save @angular/platform-browser-dynamic
    $ npm install --save @angular/upgrade
    $ npm install --save angular2-meteor
    $ npm install --save angular2-meteor-polyfills
    $ npm install --save es6-shim
    $ npm install --save reflect-metadata
    $ npm install --save rxjs
    $ npm install --save underscore
    $ npm install --save zone.js

> `angular2-meteor-polyfills` will load `rxjs`, `reflect-metadata` and `zone.js` in their chronological order.

Now will start replacing our Angular1 components with Angular2 components, and it would be helpful if we could just do it gradually and not all at once, one component at a time. This is where the [Upgrade Adapter]([Upgrade Adapter](https://angular.io/docs/ts/latest/guide/upgrade.html) kicks in. The adapter will give us the ability to downgrade Angular2 so they can be registered to Angular1 modules, and upgrade Angular1 components so they can be registered to Angular2 modules, thereby we can have a hybrid app. Let's get into business by initializing a new instance of the `UpgradeAdapter` and passing it as an argument to each module registration:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.9"}}

**Note that you have to share the same instance of the adapter otherwise the application might not work as expected**. Once we have our adapter we will create an Angular2 module which will represent our Angular2 app's module, and bootstrap our application using the adapter:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.10"}}

## Filters to Pipes

Angular1 filters have the same functionality as Angular2 pipes. Further details about the relation between filters pipes can be found in [Angular's docs](https://angular.io/docs/ts/latest/cookbook/a1-a2-quick-reference.html#!#filters-pipes). Filters should be registered to Angular1 modules as normal and pipes should be registered to Angular2 modules, so even though they are almost identical they will have to be duplicated for now.

We will start with the `displayName` pipe. This is how it should look like:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.11" filename="imports/ui/filters/displayNamePipe.ts"}}

And after we create a pipe it should be declared our app's Angular2 module:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.11" filename="client/main.ts"}}

The key concepts of this convertion are:

* import Pipe decorator.
* change name to *displayName*.
* move a function to be a class with `transform` method.
* remove everything related to Angular1.

The same process should be applied for `uninvited` pipe as well:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.12" filename="imports/ui/filters/uninvitedPipe.ts"}}

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.12" filename="client/main.ts"}}

We can't return `false` inside an `ngFor` directive so let's replace it with an empty array:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.13"}}

## Preparing Angular1 component for migration

We will focus on the `PartyUninvited` to start with. Let's decorate it with a [Component decorator](https://angular.io/docs/ts/latest/cookbook/a1-a2-quick-reference.html#!#controllers-components):

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.14" filename="imports/ui/components/partyUninvited.ts"}}

And like the pipe creation process we will also have to declare a component whenever we create it:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.14" filename="client/main.ts"}}

Since this is an Angular2 component it relies on pipes rather than filters, so we can remove the filters' importations:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.15"}}

## Switching to angular2-meteor

Switching to `angular2-meteor` would be an integral part of the migration. We will extend our component by `MeteorComponent` so angular2-meteor's API would be available for use

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.16"}}

Once it's done we will replace angular-meteor's API with angular2-meteor's.

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.17"}}

Now that we have our Angular2 component ready, we need to use it in our Angular1 module. The UpgradeAdapter's prototype contains a function called `downgradeNg2Component` which will transform an Angular2 component into an Angular1 directive, once it is transformed we can declare it in our Angular1 module:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.18"}}

**Note that a component is a actually a directive so registering a directive achieves the same result**.

The bindings of our Angular1 component can be translated to Angular2 using the [Input](https://angular.io/docs/ts/latest/cookbook/component-communication.html) decorator.

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.19"}}

We will no longer use prefixed variables inside our template since Angular2's template engine doesn't support it:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.20"}}

Our template uses Pipes instead of Filters, so we will make the following adjustments:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.21"}}

The `ngFor` directive of Angular2 is equivalent to Angular1's `ng-for`:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.22"}}

And `ng-if` directive is now `ngIf`:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.23"}}

Events are registered using (parenthesis) rather than an `ng` prefix:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.24"}}

Since PartyUninvited is a Angular2 component we have to change the way we're passing a value:

Bound attributes should be notated with \[square brackets\]:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.25"}}

Now let's do the same for `PartyDetails`. First we will turn it into a component:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.26" filename="imports/ui/components/partyDetails.ts"}}

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.26" filename="main/client.ts"}}

Since `PartyDetails` component is dependent on an Angular1 component which outside our project's scope, we will have to upgrade it using the `upgradeNg1Component` method:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.27"}}

Then again, we will extend the `MeteorComponent`:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.28"}}

And we will downgrade our newly created component so it can be loaded with Angular1's module system:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.29"}}

Our router might be implemented using Angular1's router, but the template can be Angular2's:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.30"}}

Let's take care of the properties binding in the `PartyDetails` component:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.31"}}

Further information about the `Output` decorator can be found [here](https://angular.io/docs/ts/latest/cookbook/component-communication.html).

## Removing Angular1 support

Once a component is fully integrated with Angular2, everything related to Angular1 can be removed, in this case, the `PartyUninvited` component:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.32" filename="imports/ui/components/partyUninvited/partyUninvited.ts"}}

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.32" filename="imports/ui/components/partyUninvited/partyDetails.ts"}}

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.32" filename="client/main.ts"}}

## Using Material2

Indeed, We can also move our app's design stracture to Angular2 and reserve the API. Let's install the necessary packages

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.33"}}

Raw commands are listed below:

    $ npm install --save @angular2-material/core
    $ npm install --save @angular2-material/checkbox
    $ npm install --save @angular2-material/button
    $ npm install --save @angular2-material/input

We now have `md-checkbox`, `md-button` and `md-input` available for us. But will first need to declare those:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.34"}}

Now that material2 is fully registered we can safely replace `PartyDetails` template with Angular2's:

{{> DiffBox tutorialName="meteor-angular1-socially" step="23.35"}}

The upgrading process for Socially might take a while and can be done in many different ways, but hopefully you got the concept. If your'e not familiar with Angular2-Metoer the following [tutorial](/tutorials/socially/angular2/bootstrapping) might come in handy.

{{/template}}
