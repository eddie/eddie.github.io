---
title: AngularJS service events for state management
tags: [angularjs]
layout: post
---

Whilst beginning development on a new project, I was having trouble trying to find a clean way of managing service events, which caused difficulties in deciding where state change calls should go.

Initially I was using [angular-app](https://github.com/angular-app/angular-app) as a reference, but I didn't like how state changes were kept in services. Also, service events in angular-app (HTTP retry queue interceptor) were a bit different from what I required.

It had been a while since I developed an angular application, and after doing some digging, I couldn't find a general best practice for handling service events/callbacks cleanly. But, I was probably looking in the wrong places..

The requirements I had for service callbacks were:

- Reduce coupling between components
- Avoid using angular event anti-patterns
- Ability to easily add callbacks to service
- Service should declare which callbacks it supported

# Initial Attempt (Callback array)

Initially, I took a leaf out of the angular-app book, and a service would maintain a list of callbacks for specific events, a controller could then add a callback and they would be called appropriately.

This worked well, but had a couple issues:

- New service methods meant adding a new callback
- Callback code coupled with service

A crude implementation of this looks a little like this:

service.js

```javascript
angular.module('myapp.security').factory('SecurityService', function($log){
var service = {

  callbacks: {
    onLogout: []
  },

  logout: function() {
    service.runCallback('onLogout');
  },

  runCallback: function(name) {
    if(!service.callbacks.hasOwnProperty(name)){
      $log.error('SecurityService:' + name +' callback does not exist');
      return;
    }
    angular.forEach(service.callbacks[name], function(cb) {
      try {
        cb();
      } catch(e) {
        $log.error('SecurityService:' + name +' callback threw an error' + e);
      }
    });
    }
  };
  return service;
});

```

This could then be used like so:

```javascript
// Add account token from localStorage
SecurityService.callbacks.onLogout.push(function(){
  delete $localStorage['token'];
  $state.go('login');
});
```

# Publ/Sub Approach

I liked the previous approach, but I wanted something a little more managable. I thought about using $on and $broadcast, but knew things could get pretty messy if used incorrectly. So after some research, it seemed using channels would be an ideal approach to meet my requirements.

It would allow me to keep a strict API for the events exposed by a service and help move the event implementation out of the service iteself. 



## Unsubscribe on $destroy

# Further reading

https://eburley.github.io/2013/01/31/angularjs-watch-pub-sub-best-practices.html
http://codingsmackdown.tv/blog/2013/04/29/hailing-all-frequencies-communicating-in-angularjs-with-the-pubsub-design-pattern/

