---
title: AngularJS service events for state management
tags: [angularjs]
layout: post
---

Whilst beginning development on a new project, I was having trouble trying to find a clean way of managing service events, which caused difficulties in deciding where state change calls should go.

Initially I was using [angular-app](https://github.com/angular-app/angular-app) as a reference, but I didn't like how state changes were kept in services. Also, service events in angular-app (HTTP retry queue interceptor) were a bit different from what I required.

It had been a while since I developed an angular application, and after doing some digging, I couldn't find a general best practice for handling service events/callbacks cleanly. But, I was probably looking in the wrong places..

The requirements I had for service callbacks were:

- No magic between service/controller, events should be clear to follow.
- Avoid using $on/$broadcast anti-patterns
- Ability to easily add callbacks to service
- Service should declare which callbacks it supported

# Initial Attempt (Callback array)

Initially, I took a leaf out of the angular-app book, and a service would maintain a list of callbacks for specific events, a controller could then add a callback and they would be called appropriately.

This had a couple of issues:

- New service methods meant adding a new callback

A crude implementation of this looks a little like this:

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

# Final Solution (Messages)
