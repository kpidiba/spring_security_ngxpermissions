# ngx-permissions Example Project

This project demonstrates how to use `ngx-permissions` in an Angular application to manage user permissions and roles effectively. `ngx-permissions` is a powerful library for controlling access to various parts of your application based on user-defined permissions and roles.

## Installation

To use `ngx-permissions` in your Angular project, you need to install it via npm:

bash

```bash
npm install ngx-permissions
```

## Setup

### 1. Import the Module

Import `NgxPermissionsModule` in your application module.

```ts

import { NgxPermissionsModule } from 'ngx-permissions';

@NgModule({
  declarations: [
    // Your components
  ],
  imports: [
    BrowserModule,
    NgxPermissionsModule.forRoot(),
    // Other modules
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }

```

### 2. Define and Assign Permissions and Roles

Create a service to manage permissions and roles.

```ts
import { Injectable } from '@angular/core';
import { NgxPermissionsService, NgxRolesService } from 'ngx-permissions';

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  constructor(private permissionsService: NgxPermissionsService, private rolesService: NgxRolesService) {}

  setPermissions() {
    const permissions = ['canAdd', 'canDelete', 'canEdit', 'canRead'];
    this.permissionsService.loadPermissions(permissions);
  }

  setRoles() {
    const roles = {
      ADMIN: ['canAdd', 'canDelete', 'canEdit', 'canRead'],
      USER: ['canRead']
    };
    this.rolesService.addRoles(roles);
  }
}

```

### 3. Use Permissions in Templates

Use `ngxPermissionsOnly` and `ngxPermissionsExcept` directives in your templates to show or hide elements based on permissions.

```html
<!-- Add Button -->
<button *ngxPermissionsOnly="'canAdd'" (click)="addItem()">Add Item</button>

<!-- Edit Button -->
<button *ngxPermissionsOnly="'canEdit'" (click)="editItem()">Edit Item</button>

<!-- Delete Button -->
<button *ngxPermissionsOnly="'canDelete'" (click)="deleteItem()">Delete Item</button>

<!-- Read Content -->
<div *ngxPermissionsOnly="'canRead'">
  <p>This content is visible to users with read permission.</p>
</div>

```

### 4. Implement Component Logic

Implement the methods used in the template in your component.

```ts
import { Component, OnInit } from '@angular/core';
import { AuthService } from './auth.service'; // Adjust the path as necessary

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  constructor(private authService: AuthService) {}

  ngOnInit() {
    this.authService.setPermissions();
    this.authService.setRoles();
  }

  addItem() {
    console.log('Add item');
  }

  editItem() {
    console.log('Edit item');
  }

  deleteItem() {
    console.log('Delete item');
  }
}

```

### 5. Guard Routes Based on Permissions

Protect routes based on permissions using `NgxPermissionsGuard`.

```ts
import { NgxPermissionsGuard } from 'ngx-permissions';

const routes: Routes = [
  {
    path: 'admin',
    component: AdminComponent,
    canActivate: [NgxPermissionsGuard],
    data: {
      permissions: {
        only: 'ADMIN',
        redirectTo: '/no-access'
      }
    }
  },
  {
    path: 'user',
    component: UserComponent,
    canActivate: [NgxPermissionsGuard],
    data: {
      permissions: {
        only: 'USER',
        redirectTo: '/no-access'
      }
    }
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }

```

### 6. Handle Permission Changes Dynamically

Update permissions and roles dynamically during a session if needed.

typescript

```ts
import { Component, OnInit } from '@angular/core';
import { NgxPermissionsService, NgxRolesService } from 'ngx-permissions';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  constructor(private permissionsService: NgxPermissionsService, private rolesService: NgxRolesService) {}

  ngOnInit() {
    // Load initial permissions and roles
    this.permissionsService.loadPermissions(['canRead']);
    this.rolesService.addRole('USER', ['canRead']);
  }

  upgradeToAdmin() {
    // Dynamically change permissions and roles
    this.permissionsService.addPermission('canAdd');
    this.permissionsService.addPermission('canEdit');
    this.permissionsService.addPermission('canDelete');
    this.rolesService.addRole('ADMIN', ['canAdd', 'canDelete', 'canEdit', 'canRead']);
  }
}

```



In your template, you can have a button to trigger the role upgrade:

```html
<button (click)="upgradeToAdmin()">Upgrade to Admin</button>
```

## Summary

By using the `ngx-permissions` directives like `ngxPermissionsOnly` and `ngxPermissionsExcept`, you can control the visibility of UI elements based on the permissions assigned to the user. This approach ensures that users only see the parts of the application they are authorized to access, enhancing both security and user experience.
