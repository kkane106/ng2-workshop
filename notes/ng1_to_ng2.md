# Angular 1 to Angular 2
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
  