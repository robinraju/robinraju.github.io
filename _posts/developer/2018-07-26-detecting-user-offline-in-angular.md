---
layout: single
title: Detecting lost internet connection / offline status in Angular
read_time: true
category: developer
tags: [Angular]
header:
    teaser: /assets/images/posts/developer/angular/angular_broken.jpg
comments: true
toc: true
toc_label: "Contents at a glance"
toc_icon: "sitemap"
author_profile: false
share: true
related: true
permalink: /:categories/:year-:month-:day-:title/
excerpt: A simple way to detect whether the user is disconnected from the Internet or not inside an Angular application. 
published: true
---

Angular has become the defacto tool for developing single page web applications. Its underlying architecture helps anyone
in crafting powerful web applications. To provide the best user experience, has become a huge concern over the time. Sometimes, a user might find
himself disconnected from the web after using your application for a while. 
The situation might not be very obvious to the user at first and he might end up thinking that the “site doesn’t work”.

Here we are going to discuss how we can introduce a cool feature that let our users know if he/she is offline.
{% include figure image_path="/assets/images/posts/developer/angular/angular_online_status.gif" alt="user offline/online detection using angular" caption="Detecting user's connectivity status" %}

### Start with a new Angular project

I assume you have installed Angular CLI to get started, if no find it from [here](https://angular.io/guide/quickstart){:target="_blank"}
I am using Angular CLI: 6.0.8

```sh
$ ng new online-status-demo

$ cd online-status-demo

$ ng serve
``` 

After following these commands you will be able to bring up the default angular application on *http://localhost:4200*

### Detect connectivity status

Add the following code to app.component.ts [You can place it in any other component that is accessible by the whole application]

```typescript

import {Component, OnDestroy, OnInit} from '@angular/core';
import {fromEvent, Observable, Subscription} from 'rxjs';

@Component({
  .
  .
})
export class AppComponent implements OnInit, OnDestroy {
  onlineEvent: Observable<Event>;
  offlineEvent: Observable<Event>;

  subscriptions: Subscription[] = [];

  connectionStatusMessage: string;
  connectionStatus: string;

  constructor() {}

  ngOnInit(): void {
    /**
    * Get the online/offline status from browser window
    */
    this.onlineEvent = fromEvent(window, 'online');
    this.offlineEvent = fromEvent(window, 'offline');

    this.subscriptions.push(this.onlineEvent.subscribe(e => {
      this.connectionStatusMessage = 'Back to online';
      this.connectionStatus = 'online';
      console.log('Online...');
    }));

    this.subscriptions.push(this.offlineEvent.subscribe(e => {
      this.connectionStatusMessage = 'Connection lost! You are not connected to internet';
      this.connectionStatus = 'offline';
      console.log('Offline...');
    }));
  }

  ngOnDestroy(): void {
    /**
    * Unsubscribe all subscriptions to avoid memory leak
    */
    this.subscriptions.forEach(subscription => subscription.unsubscribe());
  }
}

```
### For Angular v5.x
If you are using Angular 5 with RxJS v5.x, use the following way to get the connectivity status.
```typescript
import {Observable} from 'rxjs/Observable';
import 'rxjs/add/observable/fromEvent';

onlineStatus: Observable<boolean>;
offlineStatus: Observable<boolean>;

this.onlineStatus = Observable.fromEvent(window, 'online');
this.offlineStatus = Observable.fromEvent(window, 'offline');

```

### Create new component to display connection status
Now lets add a new component to show the message/status 

```sh
$ ng g c online-status
```
Add the following into **online-status.component.ts**

```typescript
  import {Component, Input, OnInit} from '@angular/core';
  
  @Component({
    selector: 'app-online-status',
    templateUrl: './online-status.component.html',
    styleUrls: ['./online-status.component.css']
  })
  export class OnlineStatusComponent implements OnInit {
  
    @Input() onlineStatusMessage: string;
    @Input() onlineStatus: string;
    constructor() { }
  
    ngOnInit() {
    }
  }
```

And here is the **online-status.component.html**
```html
{% raw %}
<div *ngIf="onlineStatus === 'online'" class="online">
  <span>{{onlineStatusMessage}}</span>
</div>
<div *ngIf="onlineStatus === 'offline'" class="offline">
    <span>{{onlineStatusMessage}}</span>  
</div>
{% endraw %}

```

Add some CSS to make it work. **online-status.component.css**

```css
{% raw %}
.online {
  background-color: green;
  color: #ffffff;
  padding: 10px;
  text-align: center;
  height: 100%;
}
.offline {
  background-color: red;
  color: #ffffff;
  padding: 10px;
  text-align: center;
  height: 100%;
}
{% endraw %}
```

We can now include the newly created component to our **app-component.html** as follows.

{% raw %}
```html
<app-online-status 
  [onlineStatusMessage]="connectionStatusMessage" 
  [onlineStatus]="connectionStatus">
</app-online-status>
```
{% endraw %}

### Testing application
Once our application is up and running, we can test it by disconnecting network connection or can simulte the action
from Chrome's developer Tools.
Navigate to Network Tab and toggle the offline checkbox. 

See it in action [https://stackblitz.com/edit/angular-offline-online-detection](https://stackblitz.com/edit/angular-offline-online-detection){:target="_blank"}