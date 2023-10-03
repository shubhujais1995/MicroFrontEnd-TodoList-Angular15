
Microfrontend monorepo

1) new new my-workSpace --createApplication="false" //create common workspace
2) cd my-workSpace
3) ng g application host-app --routing  --style=scss //create app1
4) ng g application mfe --routing --style=scss // create app2
5) ng s host-app -o || ng s mfe-app -o
6) install webpack 5 for module fedaration feature -> npm i webpack webpack-cli --save-dev
install module fedration for both the projects and set default port 
7) ng add @angular-architects/module-federation --project host-app --port 4200 // required configratio will be added to angular.json and webpack files will be added
8) ng add @angular-architects/module-federation --project mfe-app --port 4300 // required configratio will be added to angular.json and webpack files will be added
9) ng g c home --project host-app // ng g c todo --project host-app // and setup basic routing in app component and modules for this components
10)ng g m todo-list --project mfe-app 
11) ng g s todo-list --project mfe-app
12) add this todolist module into app module and add routing in app-routing for empty route to todolist and run mfe project
13) open webpack config of hostapp add scriptType: "text/javascrip" in output, and in plugin , replace this changes
    // For remotes (please adjust)
        name: "hostApp",
        // filename: "remoteEntry.js",
        // exposes: {
        //     './Component': './projects/host-app/src/app/app.component.ts',
        // },        
        
        // For hosts (please adjust)
        remotes: {
            "mfeApp": "mfeApp@http://localhost:4300/remoteEntry.js",
        },


14) restart server
15) open webpack config of mfe app and add scriptType: "text/javascrip" in output, and in plugin , replace this changes 
    // For remotes (please adjust)
        name: "mfeApp",
        filename: "remoteEntry.js",
        exposes: {
            './TodoListModule': './projects/mfe-app/src/app/todo-list/todo-list.module.ts',
        },        
        
        // For hosts (please adjust)
        // remotes: {
        //     "hostApp": "http://localhost:4200/remoteEntry.js",

        // },

16) in app routing file of host app- do change below code   
    import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { loadRemoteModule } from '@angular-architects/module-federation';

const MFE_APP_URL = "http://localhost:4300/remoteEntry.js";
const routes: Routes = [
  { path: '', redirectTo: '/home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
  { path: 'todo-list',
    loadChildren: () => {
      return loadRemoteModule({
        remoteEntry: MFE_APP_URL,
        remoteName: "mfeApp",
        exposedModule: "./TodoListModule"
      }).then((m) => m.TodoListModule).catch((err) => console.log(err));
    }
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }

export const routesComponentsArray = [HomeComponent];

17) in app routing file of mfe app, do change below code

    import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { TodoListModule } from './todo-list/todo-list.module';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    TodoListModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }


18) in todoListModule app, do change below code
    import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { TodoListComponent } from './todo-list.component';
import { BrowserModule } from '@angular/platform-browser';
import { RouterModule } from '@angular/router';

@NgModule({
  declarations: [
    TodoListComponent
  ],
  imports: [
    CommonModule,
    BrowserModule,
    RouterModule.forChild([
      {
        path: '', component: TodoListComponent
      }
    ]),
  ]
})
export class TodoListModule { }


restart both the servers and check both the functioanalities are working or not.
when you visit todo list link, remoteEntryfile.js would be loaded and it loads once only and if you change in mfe app todo component it automatically updates, witout loading remoteEntryfile.js file again.

Thank you. :-)

# MircofrontendWorkspacen

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 15.0.3.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The application will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via a platform of your choice. To use this command, you need to first add a package that implements end-to-end testing capabilities.

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI Overview and Command Reference](https://angular.io/cli) page.
