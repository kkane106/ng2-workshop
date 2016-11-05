# Routing
* provides ability to load different component as users navigate through application
* Components can be changed/swapped with routing
* requires the `RouterModule` from `'@angular/router'`
* can define parent AND child routes
* no more `ng-view`... more powerful now, granting ability to load multiple templates on one page
* supports lazy-loading
  
### Setup
* uses HTML5 push state by default (you can give it the hash if you want)  

###1: Add a `<base>` element in index.html  
  
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <base href="/">
  <meta charset="UTF-8">
  <title>Index</title>
</head>
<body>
  
</body>
</html>
```
  
### 2: Define routes:  
  
```js
// app.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

const routes: Routes = [
  {
    path: '', 
    pathMatch: 'full', 
    redirectTo: 'characters' // pathMatch means match completely
  },
  {
    path: 'characters', 
    component: CharacterListComponent
  },
  {
    path: 'characters/:id', 
    component: CharacterComponent
  },
  {
    path: '**', 
    pathMatch: 'full', 
    component: PageNotFoundComponent // catch all...if you got here, do this
  }
]

@NgModule({

})
export class AppModule { }
```
  
* Order doesn't matter (unless you get a bug).
* If you define routes in their own module (e.g. app-routing.module.ts), the module that has the catch all will need to be imported last else you will never reach other routes imported after it.
  
### 3: (OPTIONAL) Define a routing module
```js
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

const routes: Routes = [
  {
    path: '', 
    pathMatch: 'full', 
    redirectTo: 'characters' // pathMatch means match completely
  },
  {
    path: 'characters', 
    component: CharacterListComponent
  },
  {
    path: 'characters/:id', 
    component: CharacterComponent
  },
  {
    path: '**', 
    pathMatch: 'full', 
    component: PageNotFoundComponent // catch all...if you got here, do this
  }
]

@NgModule({
  imports : [RouterModule.forRoot(routes)], // states that these are root routes (ie not child routes)
  exports : [RouterModule] // export the updated RouterModulewith the AppRoutingModule
})
export class AppRoutingModule { }
```
  
### 4: Import routes into root module
```js
// app.module.ts
//...other imports
import { AppRoutingModule } from './app-routing.module';

@NgModule({
  imports: [BrowserModule, AppRoutingModule],
  //... etc
})
export class AppModule { }
```
  
### 5: Add a Router Outler
* Where your components will appear when the route is selected
  
```js
@Component({
  selector: 'app-container',
  template: `<router-outlet></router-outlet>`
})
export class AppComponent { }
```
  
* Above, the root component has a `<router-outlet>` which is where content will appear
  
###6: RouterLink Directive
* Add links to routes, defines the route path and any route param data

```js
@Component({
  selector: 'app-container',
  template: `
    <!-- No params, not dynamically created -->
    <a routerLink="/customers">
      Customers
    </a>
    <!-- Params and dynamic value -->
    <a [routerLink]="['/customer', 'customer.id']">
      {{ customer.firstName }} 
    </a>
  `
})
export class AppComponent { }
```
  
# Child Routes
### Different approaches
* **DAN**
  * create a 'CoreModule' that stores all of the singletons (things you don't want to have be created more than once)
  * Export const of components and routes from each feature module and import just that module into the root module so that you don't need to do imports and declarations more than once
* **JOHN**
  * Use shared for non singletons that you want multi instances of throughout your application
  * Created an actual module, import, assign and then export RouterModule
  * Better for larger apps that need providers to secure/protect routes
  
### Define Child Routes
* declare routes as an array of routes in a const.
* To attach children to a route, create a nested array in a `children` property
* attach these to the `RouterModule` with `.forChild(routes)`
  
### Reloading part of a view
* use child routes to load multiple views of components from a route
  
```js
// app-routing.module.ts
import { RouterModule, Routes }  from '@angular/router';
// etc
const routes: Routes = [
  { 
    path: 'customers/:id',
    component: CustomerComponent,
    children : [ // each of these will load with the 'customers/:id' route into <router-outlet>s
      {
        path: 'details', 
        component: CustomerDetailsComponent
      },
      {
        path: 'edit',
        component: CustomerEditComponent
      }
    ]
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```
  
### Route Parameters
* Snapshot
  * easiest as long as parameter values do not change
  * most of the time, easy
  
```js
export class CustomerComponent implements OnInit {
  private id: any;

  constructor(private route: ActivatedRoute) { }

  ngOnInit() {
    this.id = parseInt(this.route.snapshot.params['id']);
    this.getCustomer(this.id);
  }
}
```

* Observable
  * Gets new valus (data) as parameters change when component is re-used
  * to clarify, this means the component loads once and can be updated each time data in the params change
  
```js
export class CustomerComponent implements OnInit {
  private id: any;

  constructor(private routes: ActivatedRoute) { }

  ngOnInit() {
    this.route.params
      .map(params => params['id'])
      .do(id => this.id = +id) // '+' here functions as a parseInt
      .subscribe(id => this.getCustomer(this.id));
  }
}
```
  
### Route Guards
* allow decision making at key points in the route lifecycle
  * allows for prehandling
  * continue, abort, or redirect
* All of these are UX, not security
* security needs to be on the server-side
  
* Five types:
  * resolve
    * get data before component loads (do you want the spinner before or after)
  * canActivate
    * precheck authent on user at parent level
  * canActivateChild
    * precheck authent on user protect all child routes
  * canLoad
    * do not even allow JS/HTML to load (literally cannot go there, will not touch the server to retrieve destination code)
    * test runs on client side (it's a handshake call to the server)
  * canDeactivate
    * if some condition is unsatisfied, they cannot navigate away from the page (or at least have to confirm that they want to...ex confirm navigation)
  
```js
@Injectable()
export class CanActivateAuthGuard implments CanActivate, CanActivateChild {

  constructor(private userProfileService: UserProfileService, private router: Router) { }

  canActivateChild(next: ActivatedRouteSnapshot, state: RouterStateSnapshot) {
    return this.canActivate(next,state);
  }

  canActivate(next: ActivatedRouteSnapshot, state: RouterStateSnapshot) {
    if (this.userProfileService.isLoggedIn) {
      return true;
    }
    this.router.navigate(['/login'], {queryParams: {redirectTo: state.url}});
    return false;
  }
}
```
  
* You can pass multiple guards to a route, all of which must pass

```js
const routes: Routes = [
  {
    path:'characters',
    component: CharactersComponent,
    canActivate: [CanActivateAuthGuard],
    canActivateChild: [CanActivateAuthGuard],
    children: [
      {
        path: '',
        component: CharacterListComponent
      },
      {
        path: ':id',
        component: CharacterComponent
      }
    ]
  }
]
```
  
### Loading Routes (Eager vs Lazy)
* Eager
  * at startup
* Lazy
  * on demand
  
```js
const routes: Routes = [
  {
    path: 'characters',
    loadChildren: 'app/characters/characters.module#CharactersModule' // lazily load this on demand
  },
  {
    path: 'vehicles',
    loadChildren: 'app/vehicles.modules#VehiclesModule', // also lazy
    data: { preload: true } // fetches lazy loaded content in the background to optimize UX
    // uses a background thread (web worker?) so the process shouldn't be noticeable
    // requires and import of 'PreLoadAllModules' from the RouterModule, and it's application
    // in the .forRoot()
    // Then implement a PreloadingStrategy
  }
]
```
  
