# Angular2 Workshop (Tampa, FL 11/4-5)
*John Papa (http://www.johnpapa.net/)*  
&&  
*Dan Wahlin (https://blog.codewithdan.com/)*  
  
##### [Slides][slides]
[slides]:https://docs.google.com/presentation/d/1jvMY7bmtIgUSCKquCxCD6vH1635sYVMKtAmC0f6cKb4/edit
  
### Table of Contents
* Intro to ng2
* Typescript overview
* ng1 to ng2
* Modules (es6 & ng2), Components and Templates
* Binding and Directives
* Services / Providers, DI
* Http -> `Promise` and RxJS based
* Routing -> child routes, lazy loading etc..
* Forms
  
### AngularJS
* Not dead (yet).
* Still being actively developed and supported
* Shut off for support will happen when the metrics drop off significantly (not necessarily soon)

### Angular 2
* Actively developed
* Main ng team efforts
* Uses [TypeScript, ES6 ... or ES5/Dart...don't]

### High Level Angular 2
* Modules
  * es6 components encapsulation
  * ng2 containers single responsibility
* Components
  * building blocks (replaced controllers)
* Decorators
  * Provides ng metadata about the class (basically the exact same as Spring annotations)
  * ES6/TypeScript support (will support) decorators...browsers will eventually once it is formally added to ES6 spec
* Dependency Injection
  * Opportunities to override DI configuration at the component/provider level
* Services (simplified)
  * now just `Service` -> *`@Injectable`*
* Data Binding
  * Improved performance and simplified code (...about 3 things to know now...)
* Performance
  * Faster...wicked fast

### Demo Applications
* Dan's Demo link: [demo app][demoapp]
* John's Demo link: [demo app][johndemo]



[demoapp]:https://github.com/danwahlin/angular2-jumpstart
[johndemo]:https://github.com/johnpapa/event-view