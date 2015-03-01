---
title: AngularJS channels
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

Based on the work of [Eric Burley](https://eburley.github.io/2013/01/31/angularjs-watch-pub-sub-best-practices.html) and [Jim Lavin](http://codingsmackdown.tv/blog/2013/04/29/hailing-all-frequencies-communicating-in-angularjs-with-the-pubsub-design-pattern/) I created a reusable service to handle inter-component communication with AngularJS.

## Pub/Sub Channel Factory

Below is a rough example of the channel factory, it needs improvements, but allows for quick channel creation.

```javascript

angular.module('ng.channel', [])

.factory('EventChannel', function($rootScope, $log) {

    var createChannel = function(name) {

      var getEventKey = function(eventName) {
        return "CHANNEL:"+ name + ":" + eventName;
      }

      var emit = function(event, data) {
        $rootScope.$broadcast(getEventKey(event), data);
      };

      var register = function(scope, event, handler) {

        return scope.$on(getEventKey(event), function(event, args){
          try {
            handler(args);
          } catch(e) {
            $log.error('Channel:' + name +' event threw an error' + e);
          }
        });
      };

      return {
        emit: emit,
        register: register
      };
    };

    return {
      create: createChannel
    }
});

```

# Create your own channel service

```javascript

angular.module('myApp.security', ['ng.channel'])
  .service('SecurityChannel', function($rootScope, EventChannel) {

    var serviceChannel = EventChannel.create("SecurityChannel");
    var _LOGOUT_EVENT_ = 'logout';

    return {
      logout: function (data) {
        serviceChannel.emit(_LOGOUT_EVENT_, data);
      },
      onLogout: function ($scope, handler) {
        return serviceChannel.register($scope, _LOGOUT_EVENT_, handler);
      }
    }
  });

angular.module('myApp.security').service('SecurityService', function(SecurityChannel) {
    return {
      logout: function() {
        // Do actual logout call
        // Notify
        SecurityChannel.logout();
      }
    };
  });

```

# Using the channel

```javascript

angular.module('myApp.view1', ['ngRoute', 'myApp.security'])

.controller('View1Ctrl', function($scope, $log, SecurityChannel, SecurityService) {

    $scope.logout = function() {
      SecurityService.logout();
    };

    var onLogoutCleanup = SecurityChannel.onLogout($scope, function(item){
      $log.info("Logged out!");
    });

    $scope.$on('$destroy', function() {
      $log.debug("Removing handler");
      onLogoutCleanup();
    });
});

```

# Summary and Future improvements

So this implementation is a little messy, and could do with some improvements. I would like to move more of the setup into createChannel, but I wonder if this could lead us back to the initial problem and create a confusing flow.

I imagine the channel setup could eventually look like this:

```javascript

angular.module('myApp.security', ['ng.channel'])
  .service('SecurityChannel', function($rootScope, EventChannel) {

    return EventChannel.create("SecurityChannel", ["onLogout"]);
});
```

That's it for now, please [submit an issue](https://github.com/eddie/eddie.github.io/issues) if you see any problems with this post or can offer any suggestions.  I have also created a package for this [angular-channel](https://github.com/eddie/angular-channel), it shouldn't be used in this state- but I hope to build upon this soon. All contributions welcome of course. 



