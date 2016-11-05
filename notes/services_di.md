# Services
* Use services to get data for your application
* Can be used to hold state in an application (e.g. user information)
* Exception Handlers / Logging / HTTP Interceptors / HTTP Services
* Shared data/logic/provides something of value
  
```js
@Injectable()
export class VehicleService {
  getVehicles() {
    return [
      new Vehicle(10, 'Millenium Falcon')
    ];
  }
}
```
  
* `@Injectable()` : always put this on your services and you'll never run into a problem
  * **why**? If the service has a constructor with DI, you must have `@Injectable()` on the class to tell Angular that it needs to go find resources (e.g. this services constructor has injected resources)
  * It's essentially a do nothing decorator, it just needs a decorator so that it can emit decorator meta data (basically makes the JS aware that it's available to receive resources from DI)
  
### Dependency Injection
* How we provide an instance of a class to another Angular feature
* Inject a service into a component through the `@Component` classes constructor
  
```js
import { VehicleService } from '../services/vehicle.service';

@Component({
  selector : 'my-component',
  template : '<h1>My Component</h1>'
  providers : [ VehicleService ]
})
export class MyComponent {
  vehicles:Vehicle[];
  // injecting the service into the component's constructor
  // by typing the vehicleService DI, it tells Ng how (and what) to go and find
  // retrieves the service as a singleton (if it's already been instantiated, you'll get)
  // the one that exists...if not, it will create and store the single instance
  constructor(private vehicleService : VehicleService) {
    this.vehicleService.getVehicles()
      .subscribe((vehicles) => this.vehicles = vehicles);
  }
}
```
  
### How do you inject a service into a service?
* ... basically the same way as you inject one into a Component...
  
```js
@Injectable()
export class SpeakerService {
  constructor(private http: Http) { }

  getSpeakers() {
    // ...
  }
}
```
  
* Angular uses the 'root injector' (one singular root injector) to register the services (a bucket at root of app with a ton of singletons)
  
### Using Services:
1: Inject the service into the constructor of the class where you want to use it (**DO NOT FORGET TO IMPORT IT!!!!**):
  
```js
//*** TODO: Import the Component and OnInit objects from @angular/core
import { Component, OnInit } from '@angular/core';
import { CharacterService } from './character.service';

@Component({ 
  selector: 'app-component',
  templateUrl: 'app/app.component.html',
  providers : [ CharacterService ]
})
export class AppComponent implements OnInit {
  
  constructor(private characterService: CharacterService) {  }
  
}
```
  
2: In order to use a service you **must include** it in the list of providers at the module level.  
  
* It is possible to add a provider at the Component level. If this is done that service does not need to be included at the module level and will have isolated availability (that component and it's children)
  
* Be cautious about including services in multiple places (e.g. both the module and in a component), this will instantiate multiple singletons. If you are relying on the service to maintain state you will need to differentiate which instance is being referenced...
  
* What happens if two modules both use the same service...you probably end up with multiple instances of a service.

# HTTP
* Import Http module
* Inject Http in a service
* Call Http.get and return the result
* subscriptions with services
  
```js
import { BrowserModule } from '@angular/platform-browser';
import { HttpModule } from '@angular/http';

@NgModule({
  imports : [BrowserModule,HttpModule],
  // ...
})
export class AppModule { }
```
  
* You don't need to include the `HttpModule` as a provider because the module already adds all of the contained services as providers.
  
* Use DI to include `Http` in a services constructor:
  * **NOTE**: ALWAYS HANDLE EXCEPTIONS IN THE SERVICE...easier implementation
  
```js
@Injectable()
export class VehicleService {
  constructor(private http: Http) { }

  getVehicles() : Observable<Vehicle[]> {
    return this.http
      // these operators will need to be imported (they are not part of Http, they are part of observable)
      .get('api/vehicles.json') // gets data
      .map((response : Response) => response.json().data) // .map : operator to extract data
      .do(data => console.log(data)) // .do : operator to perform an operation without modifying the return
      .catch(this.handleError)
  }

  private handleError(error : Response) {
    console.error(error);
    return Observable.throw(error.json().error || 'Server error');
  }
}
```
  
* This Http service uses Observables
  * promises retrieve individual data points in single round trips.
  * with pub/subs over sockets, observables can handle notifications for many items continuosly with less overhead

* RxJS is overwhelming when you first look at it. It is much more powerful, you don't have to learn all of it to be effective, and it's definitely worth a look.
  * [http://reactivex.io/][rxjs]



[rxjs]:http://reactivex.io/
  
### Http interceptors:
* Not built-in in ng2
* could simulate it by creating a child class of Http and intercepting everything...maybe not the best
* Ward just submitted a PR to add a cookbook to the docs around how to do this

# RxJS
* Reactive JS, implements the observer pattern for Ng2 ([http://reactivex.io/][rxjs])
* Subscribing to observables
  * component is handed an observable ... it is then subscribed to:
  * subject exposes the data, subscriber receives notification
  * Angular will automatically close the subscriptions for it's services (e.g. Http), custom observables you create will need to be closed manually.
  
```js
@Component({
  selector : 'vehicle-component',
  template : '<h1></h1>',
  providers : [ VehicleService ]
})
export class VehicleComponent {

  constructor(private vehicleService: VehicleService) { }

  getVehicles() {
    this.vehicles = [];

    this.vehicleService.getVehicles()
      .subscribe(
        vehicles => this.vehicles = vehicles, // success handler function
        error => this.errorMessage = <any>error // error handler function
        );
  }
}
```
  
### Returning from Http
* do not return the response
* service handles the work
  
```js

```
  

### Async Pipe
* Async Pipe receives a `Promise` or `Observable` as input and subscribes to the input, eventually emitting the value(s) as changes arrive.
  * Async Pipes can be applied to stream data to a decorated value in the template
  
```js
@Component({
  selector : 'vehicle-component',
  template : '<h1></h1>',
  providers : [ VehicleService ]
})
export class VehicleComponent implements OnInit{

  constructor(private vehicleService: VehicleService) { }

  vehicles: Observable<Vehicle[]>

  getVehicles() {
    this.vehicles = this.vehicleService.getVehicles()
  }

  ngOnInit () {
    getVehicles();
  }
}
```
  
```html
<ul>
<!-- the '| async' subscribes to the 'vehicles' observable -->
  <li *ngFor="let v of vehicles | async"> 
    {{vehicle.name}}
  </li>
</ul>
```
  
* This can be hard to debug, generally it is probably a better idea to use `subscribe`...highly dependent on the situation
  
* [John's app is a really good resource for some best practices][johnsapp]

[johnsapp]:https://github.com/johnpapa/event-view