<h1 align="center">Frameworks code comparison</h1>

Comparison of different approaches in writing web applications. Based on React, Angular, AngularJS. Useful when migrating between frameworks or switching between projects often.

### :warning: **Work in progress** :warning:

### :exclamation: PRs and Feedback are welcome :exclamation:

> Note regarding framework naming:
> - AngularJS refers to Angular v1.x
> - Angular refers to Angular v2+
>
> See: http://angularjs.blogspot.com/2017/01/branding-guidelines-for-angular-and.html

All examples intend to follow the current best practises and conventions inside the given framework community.
Angular code is written in TypeScript.

<h1 align="center">Table of contents</h1>

  * [Table of contents](#table-of-contents)
  * [Simple component](#simple-component)
  * [Default inputs](#default-inputs)
  * [Dependency injection](#dependency-injection)
  * [Templates](#templates)
  * [Interpolation](#interpolation)
  * [Inputs and Outputs](#inputs-and-outputs)
  * [Forms](#forms)
  * [Handling Events](#handling-events)
  * [Lifecycle methods](#lifecycle-methods)
  * [Lists](#lists)


# Simple component

### AngularJS
```js
class ChangePasswordController {
    constructor($log, Auth, Notification) {
        'ngInject';

        this.$log = $log;
        this.Auth = Auth;
        this.Notification = Notification;
    }

    $onInit() {
        this.password = '';
    }

    changePassword() {
        this.Auth.changePassword(this.password).then(() => {
            this.Notification.info('Password has been changed successfully.');
        }).catch(error => {
            this.$log.error(error);
            this.Notification.error('There was an error. Please try again.');
        });
    }
}

// Object describing the component
const component = {
    bindings: {},
    template: changePasswordTemplate,
    controller: ChangePasswordController,
};

// Initialize the component in Angular's module
const module = angular.module('app.changePassword', [])
.component('changePassword', component);

```

### Angular
```js
import { Component, OnInit } from '@angular/core';
import { NGXLogger } from 'ngx-logger';
import { Auth } from 'actions/auth';
import { Notification } from 'utils/notification';

@Component({
    selector: 'app-login',
    templateUrl: './change-password.component.html',
    styleUrls: ['./change-password.component.scss'],
})
export class ChangePasswordComponent implements OnInit {
    password: string = '';

    constructor(
        private logger: NGXLogger,
        private auth: Auth,
        private notification: Notification,
    ) {}

    ngOnInit() { /* runs on component initialization */ }

    changePassword() {
        this.auth.changePassword(this.password).subscribe(() => {
          this.notification.info('Password has been changes successfully');
        }).catch(error => {
          this.logger.error(error);
          this.notification.error('There was an error. Please try again');
        });
    }
}


/*
Module is a 'holder' for your components:
every service, redux store, effects, actions, external modules (bootstrap, etc)
have to be declared here, in order to be used within this module's components.
Keep in the separate file
*/
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

import { ChangePasswordComponent } from './change-password.component';

@NgModule({
  imports: [CommonModule],
  declarations: [ChangePasswordComponent],
})
export class ChangePasswordModule {}

```

### React
```js
import Logger from 'utils/logger'
import Notification from 'utils/notification'
import Auth from 'actions/auth'

class ChangePassword {
  state = {
    password: ''
  };

  changePassword() {
    Auth.changePassword(this.state,password).then(() => {
      Notification.info('Password has been changed successfully.');
    }).catch(error => {
      Logger.error(error);
      Notification.error('There was an error. Please try again.');
    });
  }

  render() {
    return <div>{ /* template */ }</div>;
  }
});
```

# Default inputs

### AngularJS
```js
class CoursesListController {
    $onChanges(bindings) {
        if (typeof bindings.displayPurchased.currentValue === 'undefined') {
            this.displayPurchased = true;
        }
        if (typeof bindings.displayAvailable.currentValue === 'undefined') {
            this.displayAvailable = true;
        }
    }
}

const component = {
    bindings: {
        displayPurchased: '<',
        displayAvailable: '<',
    },
    template,
    controller: CoursesListController,
};
```

### Angular

> TODO

### React
```jsx
class CoursesListController {
    static propTypes = {
        displayPurchased: PropTypes.bool,
        displayAvailable: PropTypes.bool,
    };

    static defaultProps = {
        displayPurchased: true,
        displayAvailable: true,
    };

    render() {
        return <div></div>
    }
}
```

# Dependency injection

### AngularJS
Constructor is used to inject dependencies, what is done implicitly by the [$inject](https://docs.angularjs.org/api/auto/service/$injector) service.

`'ngInject'` annotation has been used which allows automatic method annotation by the ng-annotate plugin (e.g. [ng-annotate-loader](https://www.npmjs.com/package/ng-annotate-loader) for Webpack). That's essentially needed to counter minification problems.

```js
class ChangePasswordController {
  constructor($log, Auth, Notification) {
    'ngInject';

    this.$log = $log;
    this.Auth = Auth;
    this.Notification = Notification;
  }

  handleEvent() {
    this.Notification.info('Event handled successfully');
  }
}
```

### React
There's no special injection mechanism. For dependency managment, ES2015 modules are used.
```js
import Logger from 'utils/logger'
import Notification from 'utils/notification'
import Auth from 'actions/auth'

class ChangePassword extends React.Component {
  handleEvent = () => {
    Notification.info('Event handled successfully');
  }

  render() {
    return (
      <button onClick={this.handleEvent}>
        Hello world!
      </button>
    );
  }
}
```

# Templates

### AngularJS
Values on custom components must be expressions which are interpolated.

```html
<primary-button size="'big'"
                disabled="true"
                click="$ctrl.saveContent()">
  Save
</primary-button>
```

### Angular
There are three kinds of possible attributes being passed:
- text binding, e.g. size="string"
- property binding, e.g. [disabled]="value"
- event binding, e.g. (click)="eventHandler()"

```html
<primary-button size="big"
                [disabled]="true"
                (click)="saveContent()">
  Save
</primary-button>
```

### React
Templates in React are written inside the JavaScript file using the [JSX language](https://facebook.github.io/react/docs/jsx-in-depth.html). This allows us to utilize the full JavaScript capabilities. JSX uses the uppercase vs. lowercase convention to distinguish between the user-defined components and DOM tags.

```jsx
<PrimaryButton
  size="big"
  disabled
  onClick={this.saveContent}
>
  Save
</PrimaryButton>
```

# Interpolation

> TODO

# Inputs and Outputs

### AngularJS

AngularJs has introduced a new syntax for inputs/outputs which was back ported from Angular. The main purpose of this new syntax is to enforce the One-way dataflow pattern. Read more
on the [official documentation](https://docs.angularjs.org/guide/component#component-based-application-architecture):

```js
class UserPreviewComponent { }

const component = {
  bindings: {
    user: '<',
    onEdit: '&',
  },
  template: `
  <form ng-submit="$ctrl.onEdit();">
    <input type="text" ng-model="$ctrl.user.name">
    <input type="text" ng-model="$ctrl.user.email">
    <button type="submit">Submit</button>
  </form>
  `,
  controller: UserPreviewComponent,
};

export default module.component('user-preview', component);
```

In a parent component, ie `SettingsComponent`:

```js
class SettingsComponent {
  user: User;
  constructor() {
    this.user = {
      name: 'Foo Bar',
      email: 'foobar@example.com'
    }
  }
  editedUser(user: User){
    console.log('Name of the edited user is', user.name);
  }
}

const component = {
  template: `<user-preview user="user" on-edit="editedUser()"></user-preview>`,
  controller: SettingsComponent,
};

export default module.component('app-settings', component);
```

### Angular

Angular introduced a new pattern for Component interactions, this pattern follows the [Flux](https://facebook.github.io/flux/) architecture. Read more on [the official documentation](https://angular.io/guide/component-interaction).

```js
@Component({
  selector: 'user-preview',
  template: `
  <form (ngSubmit)="emitEditedUser();">
    <input type="text" value="{{user.name}}">
    <input type="text" value="{{user.email}}">
    <button type="submit">Submit</button>
  </form>
  `
})
export class UserPreviewComponent {
  @Input() user: User;
  @Output() onEdit: EventEmitter = new EventEmitter<User>();

  emitEditedUser() {
    this.onEdit.emit(this.user);
  }
}

```

In a parent component, ie `SettingsComponent`:

```js
@Component({
  selector: 'app-settings',
  template: `
    <user-preview [user]="user" (onEdit)="editedUser($event)"></user-preview>
  `
})
export class SettingsComponent {
  user: User;
  constructor() {
    this.user = {
      name: 'Foo Bar',
      email: 'foobar@example.com'
    }
  }
  editedUser(user: User){
    console.log('Name of the edited user is', user.name);
  }
}
```

### React

```jsx
import React from 'react';
import PropTypes from 'prop-types';

class UserPreviewComponent extends React.Component {
  render() {
    return (
      <form  onSubmit={this.props.onEdit}>
        <input type="text" value={this.props.user.email} >
        <input type="text" value={this.props.user.name} >
        <button type="submit">Submit</button>
      </form>
    );
  }
}

UserPreviewComponent.propTypes = {
  user: PropTypes.instanceOf(User)
};
```

In a parent component, ie `SettingsComponent`:

```js
import React from 'react';

class SettingsComponent extends React.Component {
  constructor() {
    this.state = {
        user: {
          name: 'Foo Bar',
          email: 'foobar@example.com'
        }
    };
  }
  editedUser(user: User){
    console.log('Name of the edited user is', user.name);
  }
  render() {
    return (
      <UserPreviewComponent user={this.state.user} onEdit={(user) => this.editedUser(user)} />
    );
  }
}
```

Read more about React's [PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html).

For communication between two components that don't have a parent-child relationship, you can set up your own global event system. Subscribe to events in componentDidMount(), unsubscribe in componentWillUnmount(), and call setState() when you receive an event. [Flux](https://facebook.github.io/flux/) pattern is one of the possible ways to arrange this.

# Forms

> TODO

## Validation

## model-options

## submit

# Handling Events

> TODO

# Lifecycle methods

### React

#### Mounting

`constructor(props)` - the first method called in the lifecycle before mounting. If used, it must include super(props) as first call:

```js
constructor(props) {
  super(props);
  this.state = {
    time: new Date();
  }
}
```

`componentWillMount()` - is invoked just before render. Modifying the state here won't trigger a re-render.

`componentDidMount()` - is invoked after render. Useful for initialisations that require DOM nodes.

#### Updating

`componentWillReceiveProps(nextProps)` - is only called after rendering, but before receiving new props. Because React may call this method although props stay the same. It is recommended to manually implement a check to see if there's a difference.

`shouldComponentUpdate(nextProps, nextState)` - the method is called before receiving new props or state. By default, it returns true which means render is triggered by any change. Modifying this method allows you to only re-render in intended scenarios.

`componentWillUpdate(nextProps, nextState)` - is invoked if shouldComponentUpdate returns true before render. Note: You can't use this.setState() here.

`componentDidUpdate(prevProps, prevState)` - is invoked after render, but not after the initial one. This method is useful for manipulating the DOM when updated.

#### Unmounting

`componentWillUnmount()` - is invoked immediately before a component is unmounted and destroyed. Useful for resource cleanup.

# Data flow between components

> TODO

# Lists

### AngularJS
[ngRepeat](https://docs.angularjs.org/api/ng/directive/ngRepeat)

```js
export class BookListComponentCtrl {
  constructor() {
    this.books = [
      {
        id: 1,
        title: "Eloquent JavaScript",
        author: "Marijn Haverbeke"
      },
      {
        id: 2,
        title: "JavaScript: The Good Parts",
        author: "Douglas Crockford"
      },
      {
        id: 3,
        title: "JavaScript: The Definitive Guide",
        author: "David Flanagan"
      }
    ];
  }
}
```

```html
<ul>
  <li ng-repeat="book in $ctrl.books track by book.id">
    {{ book.title }} by {{ book.author }}
  </li>
</ul>
```

### Angular
[ngFor](https://angular.io/guide/template-syntax#ngfor)

```js
export interface Book {
  id: number;
  title: string;
  author: string;
}
```

```js
import { Component } from '@angular/core';
import { Book } from './book.interface';

@Component({
  selector: 'book-list',
  template: `
    <ul>
      <li *ngFor="let book of books; trackBy: trackById">
        {{ book.title }} by {{ book.author }}
      </li>
    </ul>
  `
})
export class BookListComponent {
  this.books: Book[] = [
    {
      id: 1,
      title: "Eloquent JavaScript",
      author: "Marijn Haverbeke"
    },
    {
      id: 2,
      title: "JavaScript: The Good Parts",
      author: "Douglas Crockford"
    },
    {
      id: 3,
      title: "JavaScript: The Definitive Guide",
      author: "David Flanagan"
    }
  ];

  trackById(book) {
    return book.id;
  }
}
```

### React
[Lists and Keys](https://reactjs.org/docs/lists-and-keys.html)

```js
class BookList extends React.Component {
  constructor() {
    this.state = {
      books: [
        {
          id: 1,
          title: "Eloquent JavaScript",
          author: "Marijn Haverbeke"
        },
        {
          id: 2,
          title: "JavaScript: The Good Parts",
          author: "Douglas Crockford"
        },
        {
          id: 3,
          title: "JavaScript: The Definitive Guide",
          author: "David Flanagan"
        }
      ]
    };
  }

  render() {
    const { books } = this.state;

    return (
      <ul>
        { books.map(book => {
          return (
            <li key="{ book.id }">
              { book.title } by { book.author }
            </li>
          );
        }) }
      </ul>
    );
  }
}
```

# Child nodes

> TODO
> ViewChild vs refs
> ContentChild vs this.props.children

# Transclusion / Containment

## Basic

### AngularJS

```js
angular.module('app.layout', [])
.component('layout', {
  bindings: {
    theme: '@',
  },
  controller: LayoutController,
  transclude: true,
  template: `
    <div ng-class="'theme-' + $ctrl.theme">
      <ng-transclude></ng-transclude>
    </div>
  `,
}).component('pageContent', {
  template: `<div>Some content</div>`,
}).component('pageFooter', {
  template: `<footer>Some content</footer>`,
});
```
```html
<layout theme="dark">
  <page-content></page-content>
  <page-footer></page-footer>
</layout>
```

### Angular

```js
@Component({
  selector: 'layout',
  template: `
    <div>
      <ng-content></ng-content>
    </div>
  `
})
export class Layout {}

@Component({
  selector: 'page-content',
  template: `<div>Some content</div>`,
})
export class PageContent {}

@Component({
  selector: 'page-footer',
  template: `<footer>Some content</footer>`,
})
export class PageFooter {}
```
```html
<layout>
  <page-content></page-content>
  <page-footer></page-footer>
</layout>
```

### React

```jsx
const Layout = ({ children, theme }) => (
  <div className={`theme-${theme}`}>
    {children}
  </div>
);

const PageContent = () => (
  <div>Some content</div>
);

const PageFooter = () => (
  <div>Some content</div>
);

<Layout theme='dark'>
  <PageContent />
  <PageFooter />
</Layout>
```

## Multiple slots

### AngularJS

```js
angular.module('app.layout', [])
.component('landingSection', {
  bindings: {},
  controller: LandingSectionController,
  transclude: {
    contentSlot: '?content', // '?' indicates an optional slot
    iconSlot: '?icon'
  },
  template: `
    <div>
      <span ng-transclude="contentSlot"></span>
      <div>
        <span ng-transclude="iconSlot">This is the default value</span>
      </dev>
    </div>
  `,
}).component('pageContent', {
  template: `<div>Some content</div>`,
});
```
```html
<div>
  <h1>Page title</h1>
  <landing-section>
    <page-content></page-content>
  </landing-section>
</div>
```

### Angular

> TODO

### React

```jsx
const Layout = ({ children, theme }) => (
  <div className={`theme-${theme}`}>
    <header>{children.header}</header>
    <main>{children.content}</main>
    <footer>{children.footer}</footer>
  </div>
);

const Header = () => (
  <h1>My Header</h1>
);

const Footer = () => (
  <div>My Footer</div>
);

const Content = () => (
  <div>Some fancy content</div>
);

<Layout theme='dark'>
{{
  header: <Header />,
  content: <Content />,
  footer: <Footer />
}}
</Layout>
```

# Styling

> TODO

# Inject HTML template (aka. innerHTML)

### AngularJS
By default, the HTML content will be sanitized using the [$sanitize](https://docs.angularjs.org/api/ngSanitize/service/$sanitize) service. To utilize this functionality, you need to include `ngSanitize` in your module's dependencies. [Read more](https://docs.angularjs.org/api/ng/directive/ngBindHtml)

```html
<p ng-bind-html="$ctrl.article.content"></p>
```

### Angular
It automatically sanitizes the values before displaying them using [DomSanitizer](https://angular.io/docs/ts/latest/api/platform-browser/index/DomSanitizer-class.html)

```html
<p [innerHTML]="article.content"></p>
```

### React
All string values are sanitized before being inserted into the DOM. No more details are currently available.
You need to pass an object containing `__html` property with desired template content.

```jsx
<p dangerouslySetInnerHTML={{__html: article.content}} />
```
