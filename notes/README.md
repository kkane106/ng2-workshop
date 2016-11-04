# Angular2 Workshop (Tampa, FL 11/4-5)
*John Papa (http://www.johnpapa.net/)*  
&&  
*Dan Wahlin (https://blog.codewithdan.com/)*  
  
##### [Slides][slides]

  
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

### TypeScript
* Docs: [http://www.typescriptlang.org/][ts]
* Superset of JS (duh) -> all JS is valid TS
* ES6 is the future (eventually everything will be there...ish)
  * major update
  * lots of syntactic sugar to mirror classical inheritance, add generators etc
  * modules, import/export functionality etc
  * ability to compile down to legacy ES* means that backwards compatability will be simplified
* ES Modules, TS Modules (Namespaces), Ng Modules...these are all different things
  * transpiled to conform to specification
* ES Modules are solving the problem that created things like RequireJS etc as work arounds
* Hardest things about Ng2 are modern JS (not Ng2 itself)
* Transpiles src map files that will trace errors from transpiled es5 back to src .ts files (e.g. errors map to the code you wrote, not the crap you transpiled)
* TypeScript is gaining interest consistently over time.

### ES6 Features
* Classes (syntactic sugar...still prototypal inheritance)
* Block scope (`let`)
* Destructuring
* Arrow Functions (`log = (val) => console.(val);`)
  * Help with `this`
* Modules
* Rest/Spread operators
* Default parameters
  
* [ES6 Demo][ES6demo]

### ES6 + TypeScript
* Start with SystemJS (webpack/gulp are bigger)
* `tsconfig.json` : Essentially a build file

```json
// tsconfig.json
{
  "compilerOptions" : {
    "target" : "es5" // compile to es5
    ,"module" : "system" // use systemjs
    ,"moduleResolution" : "node" // use node
    ,"sourceMap" : true // create source map --> helps with debugging
    ,"emitDecoratorMetadata" : true // leverage new features
    ,"experimentalDecorators" : true // ''
    ,"removeComments" : false // when transpiled, do not add comments to js
    ,"noImplicitAny" : true // requires type definitions
    ,"suppressImplicitAnyIndexErrors" : true
    ,"allowJs" : true
  }
}
```
  
* Example TS with ES6:
  
```js
class Person {
    constructor(private name:string) {
    }

    public getName(): string {
        return this.name;
    }
}

class Employee extends Person {
    constructor(n: string, private salary: number) {
        super(n);
    }

    public getSalary(): number {
        return this.salary;
    }

    getDetails(): void {
        console.log(`
            name : ${this.getName()}
            salary : \$${this.getSalary()}
        `);
    }
}

class Student extends Person {
    constructor(n: string, private gpa: number) {
        super(n);
    }

    public getGpa(): number {
        return this.gpa;
    }

    getDetails(): void {
        console.log(`
            name : ${this.getName()}
            gpa : ${this.getGpa()}
        `);
    }
}

const PEOPLE = [
    new Employee('John', 50000),
    new Employee('Dan', 50000),
    new Employee('Joe', 50000),
    new Student('Kris', 4.0)
];

PEOPLE.forEach((person) => person.getDetails());

PEOPLE.forEach((person) => console.log(person.getName()));
```
  
### Destructuring with TypeScript
* Shortcut syntax to create multiple variables and assign values to them.
  
```js
var [red, blue, green] = ['red', 'blue', 'green'];
alert(red)
```
  
* Also possible to destructure objects and store the property values into variables in the scope with some namespace assigned
  
```js
var {fname, city} = {fname : 'Jim', city: 'Denver'};
alert(city)
```
  
* Will produce TypeError if the value attempting to be destructured is `undefined`: e.g.
  
```js
var test = function() {
  return undefined;
}

var {fname, city} = test();
// TypeError
```
  
### Arrow Functions
* example:
  
```js
// TypeScript
class Timer {
    counter: number = 0;

    startTimer() {
        window.setInterval(() => {
            console.log(`Fired: ${this.counter}`);
            this.counter += 1;
        }, 1000)
    }
}

var timer = new Timer();
timer.startTimer();
```
  
* Transpiled ES5
  
```js
var Timer = (function () {
    function Timer() {
        this.counter = 0;
    }
    Timer.prototype.startTimer = function () {
      // Automatically assigns this!!!
        var _this = this;
        window.setInterval(function () {
            console.log("Fired: " + _this.counter);
            _this.counter += 1;
        }, 1000);
    };
    return Timer;
}());
var timer = new Timer();
timer.startTimer();

```
  
* Single line arrow functions automatically return the value to the right of the fat arrow. Multi-line (in a block) arrow functions do not have the implicit return for the line (they default to `undefined` like normal js functions).
  
### Ng1 -> Ng2
* Controllers to Components
  * Components are now the only way to display content...no more controllers/ng-include
* Structural (Built-in) Directives

```html
<ul>
  <!-- BLOCK scope!!!! -->
  <li *ngFor="let vehicle of vehicles">
    {{vehicle.name}}
  </li>
</ul>
```
  
* Data Binding
  * Digest Cycle is gone...huge performance jump
  * interpolation/one-way binding (Component to DOM)  
    * ***ANY HTML element property***

```html
<!-- Interpolation -->
<h3>{{ story.name }}</h3>

<!-- One Way Binding -->
<h3 [innerText]="story.name"></h3>
<div [style.color]="color">{{ story.name }}</div>
<input [value]="story.name" />
```
  
  * event binding (DOM to component)
  
```html
<button 
  (click)="log('click')" 
  (blur)="log('blur')">OK</button>
```
  
  * two way data binding ... faked ... (DOM to Component and back)
    * both a property binding *[]* and an event binding *()* => *[()]*
  
```html
<input [(ngModel)]="story.name" />
```
  
* **OBJECTIVE**: In removing so many directives was to use HTML instead of trying to side step HTML
  * Removed ~43 directives by binding to the actual events and properties in HTML
  
* Services are services are services...Ng2 simplified this! They are all Classes.
  
```js
export class MyService { }
```
  
### [LABS!][labs]
* Core Setup (uses cdn for dependencies): [Lab1][lab1]
  


[ts]:http://www.typescriptlang.org/docs/tutorial.html
[ES6demo]:https://github.com/DanWahlin/ES6Samples
[slides]:https://docs.google.com/presentation/d/1jvMY7bmtIgUSCKquCxCD6vH1635sYVMKtAmC0f6cKb4/edit

[demoapp]:https://github.com/danwahlin/angular2-jumpstart
[johndemo]:https://github.com/johnpapa/event-view
[labs]:http://codewithdan.me/ng2workshoplabs
[lab1]:http://plnkr.co/edit/zbNBH3dUWSpsWSNGKwWN