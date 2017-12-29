---
layout: single
title: "Angular 5: External configuration"
read_time: true
category: developer
tags: [angular, typescript]
header:
    teaser: /assets/images/posts/developer/angular/angular-configuration.png
comments: true
toc: true
toc_label: "Contents at a glance"
toc_icon: "sitemap"
author_profile: false
share: true
related: true
permalink: /:categories/:year-:month-:day-:title/
excerpt: An experimental approach to load external configuration files in Angular 5 before the application startup.
published: true
---

If you are experienced with Java or any other backend development ecosystems, there are chances that you've worked with 
loading data from an external configuration file (properties, YAML, JSON ...), where we use environment specific settings like URLs, credentials etc.. 
And you may wonder how the same can be done in an angular app. The Angular CLI has created `environment.ts` for DEV and 
`environment.prod.ts` for PROD and will choose the appropriate one during the build process. The usual way of doing this is

```sh
  $ ng build
```
 and for production environment,
 
```sh
  $ ng build --env=prod
```
This approach has a limitation that we need to issue new builds everytime we change the environment. I was searching for a solution where I can 
isolate the configuration from the build process and load before the application startup.

### Creating the configuration files.

***env.json*** - This file specifies the environment namely development,production or any others.
```json
  {
      "env": "development"
  }
``` 

***env.development.json*** - We can have the following file for each different environments. And the filename should follow the pattern 
`env.<Environment name>.json` eg: env.development.json env.production.json
```json
  {
    "api_root": "http://localhost:9000",
    "users_api" : "/users",
    .
    .
    .
  }
```

### app.config.ts

Create an angular class `AppConfig` where we can read the JSON files and store it for later use inside our application.

```typescript
  
import { Inject, Injectable } from '@angular/core';
import { Http } from '@angular/http';
import { Observable } from 'rxjs/Rx';

@Injectable()
export class AppConfig {

  private config: Object = null;
  private env:    Object = null;

  constructor(private http: Http) {
  }

  /**
   * Use to get the data found in the second file (config file)
   */
  public getConfig(key: any) {
    return this.config[key];
  }

  /**
   * Use to get the data found in the first file (env file)
   */
  public getEnv(key: any) {
    return this.env[key];
  }

  /**
   * This method:
   *   a) Loads "env.json" to get the current working environment (e.g.: 'production', 'development')
   *   b) Loads "config.[env].json" to get all env's variables (e.g.: 'config.development.json')
   */
  public load() {
    return new Promise((resolve, reject) => {
      this.http.get('./assets/env/env.json').map( res => res.json() ).catch((error: any): any => {
        console.log('Configuration file "env.json" could not be read');
        resolve(true);
        return Observable.throw(error.json().error || 'Server error');
      }).subscribe( (envResponse) => {
        this.env = envResponse;
        let request: any = null;

        switch (envResponse.env) {
          case 'production': {
            request = this.http.get('./assets/env/env.' + envResponse.env + '.json');
          } break;

          case 'development': {
            request = this.http.get('./assets/env/env.' + envResponse.env + '.json');
          } break;

          case 'default': {
            console.error('Environment file is not set or invalid');
            resolve(true);
          } break;
        }

        if (request) {
          request
            .map( res => res.json() )
            .catch((error: any) => {
              console.error('Error reading ' + envResponse.env + ' configuration file');
              resolve(error);
              return Observable.throw(error.json().error || 'Server error');
            })
            .subscribe((responseData: any) => {
              this.config = responseData;
              resolve(true);
            });
        } else {
          console.error('Env config file "env.json" is not valid');
          resolve(true);
        }
      });

    });
  }
}
```

### Application module

We need to load the configuration before the application startup. For that lets make use of the `APP_INITIALIZER` from angular.

```typescript
  import { APP_INITIALIZER } from '@angular/core';
  import {AppConfig} from './config/app.config';
  import {HttpModule} from '@angular/http';
  
  export function initConfig(config: AppConfig) {
    return () => config.load();
  }
  @NgModule({
      imports: [
          ...
          HttpModule
      ],
      ...
      providers: [
          ...
          AppConfig,
          { 
            provide: APP_INITIALIZER,
            useFactory: initConfig,
            deps: [AppConfig],
            multi: true 
          }
      ],
      ...
  })
  export class AppModule { }
```

### Accessing configuration values from any angular classes

We can use the `getConfig()` method from AppConfig class to read values from the configuration file.

```typescript
  import {Http} from '@angular/http';
  import {Injectable} from '@angular/core';
  import {AppConfig} from '../config/app.config';

  import {Response} from '@angular/http';  
  
  @Injectable()
  export class UserService {
    api_root: string;        
  
    constructor(private http: Http, private config: AppConfig) {
      this.api_root = this.config.getConfig('api_root');
    }
  
    getAllUsers() {
    return this.http.get(this.api_root + this.config.getConfig('users_api')).subscribe(
        (response: Response) => {
          console.log(response.json());
        },
        (error) => console.log(error)
      );
    }     
  }
```

With this approach, we can load and modify configuration without rebuilding the code.