# Modules & Components
* Dan's Demo [https://github.com/danwahlin/angular2-barebones][barebones]
  * a couple of components and some routing

### Components Overview
* The basic building blocks of ng2 applications
  * import/export required modules
  * define component class
  * add `@Component` decorator to class -> metadata
  * create a template

### Modules Overview
* Seperate code into seperate "buckets"
* use import/export keywords
  * e.g. use code by importing it
  * make code available for use by exporting it
* Browsers need help with modules -> (require, browserfy...) use SystemJS or webpack

## 1: Exporting Modules
* Anything can be exported:
  
```js
// app/customers.component.ts
import { Component } from '@angular/core';
import { DataService } from '../services/data-service';

export class CustomersComponent {

}
```
  
* individual module import the functionality they need (self-responsibility)...this works around the problem of relying `<script src="">` to bring in dependencies in the correct order.
  
### Module Loader: SystemJS
* How to load ES Modules in the browser...(it's still kind of ugly because the spec hasn't decided how to handle this yet...)
  
* This example uses node for dependencies...could alternatively be sourced from the CDN

```html
<!-- change detector -->
<script src="node_modules/zone.js/dist/zone.js"></script>
<!-- ... -->
<script src="node_modules/reflect-metadata/Reflect.js"></script>
<!-- load SystemJS dependency -->
<script src="node_modules/systemjs/dist/system.js"></script>

<!-- SystemJS config file -->
<script src="system.config.js"></script>

<script>
  System.import('app') // start importing here (look for the main.ts in here)
    .catch(function(err) {
      console.err(err); // spit out the error if it fails
    });
</script>
```
  
* but what is 'app'?
  * It's mapped in the `system.config.js` file, and the referenced package property:

```js
packages : {
  app : {
    main: './main.ts'
  }
}
```
  
* For now, just grab an existing SystemJS config. **TODO** Read through the config and figure out what it is doing precisely
  
## 2: Components
* Components are reusable objects
  * it is composed of both angular (js) code, and an HTML template
  * reusable based on some element name (e.g. `<my-component></my-component>`)
* Building blocks of Angular apps
* A portion of the screen (a "view")
* Imports for modules
* `@Component` decorator
* Have a life cycle
* Delegate to services
* Accept input and handle events
  
```js
import { Component } from '@angular/core';

@Component({
  // ...
})
export class CustomersComponent { }
```
  
## 3: The `@Component` Decorator
* Decorators provide metadata for a component class
* @Component imported from `@angular/core` module
* These are the key properties:
  * selector : defines the selector that triggers instantiation of the component (e.g. html element)
  * template & templateUrl : the template to be used
  * providers : objects to be injected into the component
  
```js
import { Component } from '@angular/core';
import { DataService } from '../services/data.service';

@Component({
  selector : 'my-customers',
  providers : [DataService],
  templateUrl : 'customers.component.html'
})
export class CustomersComponent { 
  // providers get passed to the constructor when the component is initialized
  constructor(private dataService: DataService) { }
}
```
  
## 4: Creat a Template
* Components use uni-directional data flow, the Component communicates with the template and the template sends events back to the component.
  
```js
// customer.component.ts
import { Component } from '@angular/core';
import { DataService } from '../services/data.service';

@Component({
  selector : 'my-customers',
  providers : [DataService],
  templateUrl : 'customers.component.html'
})
export class CustomersComponent { 
  // providers get passed to the constructor when the component is initialized
  constructor(private dataService: DataService) { }

  isHidden: true;
  
  clickMe() {

  }
}
```
  
```html
<!-- customer.component.html -->
<div [hidden]="isHidden">
  <span (click)="clickMe()">
    Section 3
  </span>
</div>
```
  
### Bootstrapping
* Angular doesn't know to use a selector until it is registered (bootstrapped) through a module.
  
# `@NgModule` Decorator
* Requires declarations for loading Components.
* At first blush this seems like a hassle, but the reality is that it allows us to use lazy loading to bring in components based on application flow
* `main.ts` bootstraps the `AppComponent`
  
```js
// app.module.ts
@NgModule({
  declarations: [AppComponent]
})
export class AppModule { }

// app.component.ts
@Component({
  selector : 'app-component'
})
export class AppComponent { }
```
  
```html
<html>
  <head>
    <script>
      // Doing this...
      System.import('app')
    </script>
  </head>
  <body>
    <!-- ...means that Angular knows about this -->
    <app-component></app-component>
  </body>
</html>
```
  
* Modules can import additional modules
* `bootstrap : [AppComponent]` initializes the 'AppComponent' as the entry point for the application
  * **NOTE**: You don't declar modules, you only import them
  * **NOTE**: If you import a module that has already declared a component, you **do not** need to declare it again in the module it has been imported into
  * **NOTE**: *CHILD COMPONENTS MUST ALSO BE DECLARED*
* example:
  
```js
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';
import { HttpModule } from '@angular/http';

@NgModule({
  imports : [BrowserModule,FormsModule,HttpModule],
  declarations : [AppComponent],
  bootstrap : [AppComponent]
})
export class AppModule {}
```