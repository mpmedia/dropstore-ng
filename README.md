dropstore-ng
============

dropstore-ng is a set of [AngularJS](http://angularjs.org/) bindings for [Dropbox Datastore](https://www.dropbox.com/developers/datastore).
With Drobox Datastore, AngularJS and dropstore-ng you can easily create completely serverless client-side applications with syncing across multiple devices !

The bindings wrap the first three tiers (Client, DatastoreManager, Datastore) of the Dropbox Datastore API within
[Angular Promises](http://docs.angularjs.org/api/ng.$q). This is because only the first three tiers use callbacks and because
 it creates a easy to follow chain of async function calls rather than the hell usually associated with callback heavy libraries.
 The Dropbox Datastore API is also wrapped with promises to create a
simple way to ensure that the AngularJS scope is updated properly. dropstore-ng also provides AngularJS publish/subscribe functionality
allowing applications to listen for local and/or remote updates to the datastores.


### Live Demo: <a target="_blank" href="http://analogj.github.com/dropstore-ng/example/todo/">Real-time TODO app</a>.

Usage
-----
Include [drobox javascript sdk](https://www.dropbox.com/developers/datastore/sdks/js), angular.js and dropstore-ng.js in your application.

```html
<script src="https://www.dropbox.com/static/api/1/dropbox-datastores-0.1.0-b2.js" type="text/javascript"></script>
<script src="//ajax.googleapis.com/ajax/libs/angularjs/1.0.7/angular.min.js"></script>
<script src="dropstore-ng.js"></script>
```

Add the module `dropstore-ng` as a dependency to your app module:

```js
var myapp = angular.module('myapp', ['dropstore-ng']);
```

Quick Start
----------------------------------

Set `dropstoreService` as a service dependency in your controller:

```js
myapp.controller('MyCtrl', ['$scope', 'dropstoreService',
  function MyCtrl($scope, dropstoreService) {
    ...
  }
]);
```

Create an `authentication` handler that will be called when the controller first loads.
drobstore-ng just wraps the standard [authenticate](https://www.dropbox.com/developers/datastore/docs/js#Dropbox.Client.authenticate)
 method inside of a promise. The success callback of the authenticate method is a modified [datastoreManager](https://www.dropbox.com/developers/datastore/docs/js#Dropbox.Datastore.DatastoreManager) object.

```js
$scope.tasks = [];
dropstoreClient.authenticate({interactive: true})
    .then(function(datastoreManager){
        console.log('completed authentication');
        return datastoreManager.openDefaultDatastore();
    });
```

the datastoreManager and the datastore objects has been modified such that their instance methods are wrapped in promises:

```js
    ...
    .then(function(datastore){
        return datastore.getTable('tasks');
    })
    .then(function(taskTable){
        $scope.tasks =  taskTable.query();
    });
```

You can display the datastore report items using the standard Dropbox Datastore API commands:

```html
<ul>
    <li ng-repeat="task in tasks">
       {{task.get('taskname')}}
    </li>
</ul>
```
See the source for the
[controller behind the todo app](http://analogj.github.com/dropstore-ng/example/todo/controllers.js)
for a working example including syncing changes.

Dropbox Datastore API Tiers and Promises
-----------
As I stated above, only the first three tiers of the Dropbox Datastore API are wrapped in AngularJS Promises.
Only methods that make use of callbacks are wrapped in promises, all other instance methods are passed through like usual.
I've included the full documentation on the modified instance methods, and how to use them, below:

###Dropbox Client
All unmentioned instance methods for the standard Dropbox.Client are transparently aliased. Only wrapped/changed functionality  methods are documented here. Refer to
[Dropbox SDK Documentation for Dropbox.Client](https://www.dropbox.com/developers/datastore/docs/js#Dropbox.Client) for more information.

####dropstoreClient.client
alias for [Dropbox.client.constructor](https://www.dropbox.com/developers/datastore/docs/js#Dropbox.Client)

####dropstoreClient.authenticate
[Dropbox.Client.authenticate](https://www.dropbox.com/developers/datastore/docs/js#Dropbox.Client.authenticate) has been wrapped in a promise, such that the callback parameter is not necessary.
On success, a [dropstoreDatastoreManager] object is returned, which wraps the [Dropbox.Datastore.DatastoreManager](https://www.dropbox.com/developers/datastore/docs/js#Dropbox.Datastore.DatastoreManager)
```js
    dropstoreClient.authenticate({interactive: true})
        .then(function(datastoreManager){
            console.log('auth successful');
            ..
        }, function(error){
            console.log('auth failure');
            ..
        };
```

####dropstoreClient.getDatastoreManager
[Dropbox.Client.getDatastoreManager](https://www.dropbox.com/developers/datastore/docs/js#Dropbox.Client.getDatastoreManager) has been modified to return a [dropstoreDatastoreManager], which wraps the standard [Dropbox.Datastore.DatastoreManager](https://www.dropbox.com/developers/datastore/docs/js#Dropbox.Datastore.DatastoreManager) in promises.

####dropstoreClient.signOut
[Dropbox.Client.signOut](https://www.dropbox.com/developers/datastore/docs/js#Dropbox.Client.signOut) has been wrapped in a promise, such that the callback parameter is not necessary.
On success nothing is returned, however failures will passthrough a [Dropbox.ApiError](https://www.dropbox.com/developers/datastore/docs/js#Dropbox.ApiError) object
```js
    dropstoreClient.signOut({mustInvalidate: true})
        .then(function(){
            console.log('signout successful');
            ..
        }, function(error){
            console.log('signout failure');
            ..
        };
```

####dropstoreClient.getAccountInfo
[Dropbox.Client.getAccountInfo](https://www.dropbox.com/developers/datastore/docs/js#Dropbox.Client.getAccountInfo) has been wrapped in a promise, such that the callback parameter is not necessary.
On success the Dropbox.AccountInfo object will be passed through and failures will passthrough a [Dropbox.ApiError](https://www.dropbox.com/developers/datastore/docs/js#Dropbox.ApiError) object
```js
    dropstoreClient.getAccountInfo({httpCache: true})
        .then(function(accountInfo){
            console.log('getAccountIno successful');
            ..
        }, function(error){
            console.log('getAccountInfo failure');
            ..
        };
```


Pull Requests
-----------
To make a pull request, please do the following:

Mention what specific version of dropbox.js and dropstore-ng.js you were using when you encountered the issue/added the feature. This can be accessed by looking at the dropstore-ng.js file header.
Provide a pastie or gist that demonstrates the bug/feature
Do not modify the version header. I will modify that manually when merging the request


License
-------
Copyright (c) 2013 Jason Kulatunga, released under the MIT license