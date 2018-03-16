# Notes On: Routing

The Angular Router allows navigation through an app either through more traditional anchor elements, or other user interactions like button presses, or effectively anything Angular can track. It also allows for passing data with some routing action, which can then be used to decide which view or information a user will see.

Any app that makes uses of the Angular Router must have a `base` element. This is typically set to `\`.

```
<base href="/">
```

## RouterModule

The Angular Router can be imported via the `RouterModule` module from the `@angular/router` package. When `RouterModule` is imported, it should use the `forRoot` method, meaning a single instance is shared across the entire app.

## Route Checking

When some routing event occurs i.e. the browser's URL changes, the Router will look for a matching route to determine what component needs to be displayed. The `RouterModule` has an `enableTracing` option which when set to `true` will log this process to console.

![Router events][router_events]

## Router Events

Router events are fired once for any given navigation.

|  Start Event  |   End Event   |  Triggered when...  |
| ------------- | ------------- | ------------- |
| `NavigationStart`  | `NavigationEnd`  | Navigation starts. |
| `RoutesRecognized` | none             | The browser's URL is parsed and recognised. |
| `GuardsCheckStart` | `GuardsCheckEnd` | The checking of route guards starts. |
| `ChildActivationStart` | `ChildActivationEnd` | A child route is activated. |
| `ActivationStart` | `ActivationEnd` | A route is activated. |
| `ResolveStart` | `ResolveEnd` | The resolving of the url starts. |

## Router Directives

The Angular Router provides `RouterOutlet`, `RouterLink` and `RouterLinkActive` directives.

### Router Outlet

This directive is used in your main app template, and tells the Angular Router where to swap in and out the views as the user navigates through the application.

The router will add the `<router-outlet>` to the rendered DOM, and the view required immediately after it, **not** within it.

![Router outlet][router_outlet]

### RouterLink

The `RouterLink` directive is effectively Angular's own `href` in that it can navigate a user to a different part of the app when clicked. The key difference is that `RouterLink` won't refresh the page on navigation, and it can be given additional data to be used as parameters.

```html
// Basic
<a routerLink="/dashboard">Dashboard</a>

// Passing parameters
// assume the route is '/dashboard/:userId'
<a routerLink="['/dashboard', user.id]">Dashboard</a>

// Passing optional route parameters
// assume the route is '/dashboard/:userRole
<a routerLink="['/dashboard', {userRole: user.role}]>Dashboard</a>
```

### RouterLinkActive

Another benefit to using `RouterLink` is the accompanying `RouterLinkActive` directive. Because Angular can track `RouterLink` navigation, you can easily set CSS classes on a nav element using `RouterLinkActive`.

```html
<nav>
    <a routerLink="/dashboard" routerLinkActive="active">Dashboard</a>
    <a routerLink="/profile" routerLinkActive="active">Profile</a>
</nav>
```

Now when either of those routes are the active route, the element will be given an `active` class.

## Sources/Further Reading

- [Angular Router Events](https://github.com/angular/angular/blob/5.2.9/packages/router/src/events.ts)
- [Angular Guide - Routing & Navigation](https://angular.io/guide/router)

[router_events]: ../images/angular_router_events.jpg "Angular Router Events"
[router_outlet]: ../images/angular_router_outlet.jpg "Angular Router Outlet"
