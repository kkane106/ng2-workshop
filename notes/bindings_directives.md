# Binding and Directives
* `ngOnInit` : Technically an abstract class, but works like an interface
  * initialization method
  * gets called when component is created

```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-component',
  template: `
    <h1>{{greeting}}</h1>
  `
})
export class AppComponent implements OnInit {
  
  greeting = "Hello World";
  
  ngOnInit() {
    // initialization code goes here
  }
}
```
  
* By implementing `ngOnInit` you will avoid syntax errors relating from incorrect camel-casing
  
* Constructors are also available to Components, but it is difficult to test them, as such it is preferable to use the `ngOnInit` to do things like async operations (as `ngOnInit` is testable). Constructors are really only used for DI.
  
### Data Binding
##### Interpolations
```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-component',
  template: `
    <h3>{{title}}</h3> 
  `
})
export class AppComponent implements OnInit {
  // This value will be interpolated between the mustache
  title = "My Best Component";
  
  ngOnInit() {

  }
  
}
```
##### One Way Binding
* Bind a propety to an attribute with a one-way binding
  
```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-component',
  template: `
  <div [title]="title">
    <h3 [style.background]="mycolor">{{title}}</h3>
  </div>
  `
})
export class AppComponent implements OnInit {
  
  title = 'My Best Component';
  mycolor = 'yellow';
  
  ngOnInit() {

  }
}
```
  
##### Event Binding
* Use parentheses to select an event and assign it a function:
  
```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-component',
  template: `
  <div [title]="title">
    <h3 [style.background]="mycolor">{{title}}</h3>
  </div>
  <button (click)="flipColor()">Flip Color</button>
  `
})
export class AppComponent implements OnInit {
  
  title = 'My Best Component';
  mycolor = 'yellow';
  
  flipColor() {
    this.mycolor = (this.mycolor === 'yellow') ? 'blue' : 'yellow';
  }
  
  ngOnInit() {

  }
}
```
  
##### Two Way Data Binding with `ngModel`
* `ngModel` lives in the '@angular/forms' `FormsModule`
  
* Two way binding uses `[()]`
  
  * square brackets bind the value
  * the parentheses bind to `ngModelChanged` (behind the scenes) which actually updates the value of the model

```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-component',
  template: `
  <h2>{{name}}</h2>
  <div [title]="title">
    <h3 [style.background]="mycolor">{{title}}</h3>
  </div>
  <label>Name: </label><input type="text" [(ngModel)]="name" />
  <button (click)="flipColor()">Flip Color</button>
  `
})
export class AppComponent implements OnInit {
  name:string = "John";
  title = 'My Best Component';
  mycolor = 'yellow';
  
  flipColor() {
    this.mycolor = (this.mycolor === 'yellow') ? 'blue' : 'yellow';
  }
  
  ngOnInit() {

  }
}
```
  
##### Pathing to a `templateUrl` property
```js
@Component({
  // this creates an absolute reference to the module
  // which makes it easier to path to
  moduleId : module.id,
  selector: 'app-component',
  // now (assuming it's in the same directory) we know just where the html is
  templateUrl: 'app.component.html'
})
```
  
##### Repeating Over Arrays (`*ngFor`)
* Use the `*ngFor` directive.
* `*ngFor` is a structural directive which comes from the `CommonModule`. The `CommonModule` comes from the `BrowserModule`...

```js
import { Component, OnInit } from '@angular/core';

@Component({
  // this creates an absolute reference to the module
  // which makes it easier to path to
  moduleId : module.id,
  selector: 'app-component',
  templateUrl: 'app.component.html'
})
export class AppComponent implements OnInit {
  name = "John";
  title = 'My Best Component';
  mycolor = 'yellow';
  data = [1,2,3,4];
  
  flipColor() {
    this.mycolor = (this.mycolor === 'yellow') ? 'blue' : 'yellow';
  }
  
  ngOnInit() {

  }
}
```
  
* Remember to put the `*ngFor` on the element to be repeated
  
```html
<h2>{{name}}</h2>
<div [title]="title">
  <h3 [style.background]="mycolor">{{title}}</h3>
</div>
<label>Name: </label>
<input type="text" [(ngModel)]="name" />
<button (click)="flipColor()">Flip Color</button>
<ul>
  <li *ngFor="let d of data">{{d}}</li>
</ul>
```
  
* What's the deal with the `*`?
  * Comes with structural directives that manipulate the DOM
  * The `*` is a shortcut from having to type a template tag (html5 goodness). TLDR; don't forget the `*`
  
##### Using Conditionals (`*ngIf`)
* About as easy as you'd think
* Also a structural directive
* More simplified than `ng-hide`/`ng-show` from Angular 1.*
  
```html
<h2>{{name}}</h2>
<div [title]="title">
  <h3 [style.background]="mycolor">{{title}}</h3>
</div>
<label>Name: </label>
<input type="text" [(ngModel)]="name" />
<button (click)="flipColor()">Flip Color</button>
<ul *ngIf="data && data.length">
  <li *ngFor="let d of data">{{d}}</li>
</ul>
```
  
* To replicate that functionality you can use a binding on the html 'hidden' property
  * `<div [hidden]="data"></div>`

##### Pipes (used to be filters)
* Pipes are wildly more efficient then filters were.
  
```js
//...
  person = {name : "John"}
//...
```
  
```html
<div>{{ person | json }}</div>
```
  
* The above displays the object as 'json'...as opposed to the toString default
* Also things like uppercase and lowercase...etc etc

##### Custom Pipes (`@Pipe`)
```js
import { Pipe , PipeTransform } from '@angular/core';

@Pipe({
  name: 'capitalize'
})
export class CapitalizePipe implements PipeTransform {
  transform(value : any) {
    if (value) {
      return value.charAt(0).toUpperCase() + value.slice(1);
    }
    return value;
  }
}
```
  
* Don't forget to import it in the Module

```js
import { CapitalizePipe } from '../pipes/capitalize.pipe';

@NgModule({
  declarations : [CapitalizePipe /*, ...*/]
})
export class MyModule { }
```
