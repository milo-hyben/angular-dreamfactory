angular-dreamfactory
====================


The AngularJS DreamFactory module seamlessy integrates the DreamFactory Services Platform (DSP) SDK with an AngularJS application.  Because it is dynamically generated when the application loads your methods and services will always be in sync with your DSP.  For more information about DreamFactory or the DreamFactory Services Platform please visit the [DreamFactory website](https://www.dreamfactory.com).


The angular-dreamfactory module can be installed by either:

* Cloning or downloading this repo and copying to your project
* using Bower


## Cloning or downloading the repo
This should be self-explanatory.


## Using Bower
First, make sure you have bower installed by opening a command line tool and typing 'bower' at the prompt.
If Bower is installed help text will pop up and you may continue.  If bower is not installed please refer to the [Bower](http://bower.io) website for help on installing this wonderful tool.

If you have bower or have just finished installing it navigate to your project root and type 'bower install angular-dreamfactory'.  The install should start.  **Pro-tip:** _use the option --save to update your main bower.json file with the angular-dreamfactory dependency._

After the install finishes you are ready to go.


## Including the angular-dreamfactory module

Now that you've installed the module we'll need to include it in the app.  If you have used bower the script should already have been included at the bottom of your index.html file.  If you have not used bower or the script was not included for some reason simply add the following line to wherever you are including your scripts for angular.

```
<script src="_PATH_TO_ANGULAR_DREAMFACTORY_/angular-dreamfactory.js"></script>
```

Now inject angular-dreamfactory into your app (usually in the app.js file).

```javascript
angular.module('MyApp',['ngDreamFactory'])
// The rest of your app
```

The angular-dreamfactory module requires two parameters that we suggest you store as constants in your app definition.  They are your DSP url and your DSP api key (provided by your DSP).  They should be set like so:

```javascript
.constant('DSP_URL', _YOUR_DSP_URL_HERE_)
.constant('DSP_API_KEY', _YOUR_DSP_API_KEY_HERE_)
```

The first parameters for the constants have to be called exactly 'DSP_URL' and 'DSP_API_KEY' as the angular-dreamfactory module is looking for these to be injected. All together your app definition should resemble the code below:

```javascript
angular.module('MyApp', ['ngDreamFactory'])
  .constant('DSP_URL', _YOUR_DSP_URL_HERE_)
  .constant('DSP_API_KEY', _YOUR_DSP_API_KEY_HERE_)
// Rest of your app
```

## Injecting and Using angular-dreamfactory

Inject the 'DreamFactory' service from the angular-dreamfactory module as you would any service.  Calls to the DreamFactory SDK are made by calling the api object in the DreamFactory service followed by the service name and method you wish to call.  For example:

```javascript
DreamFactory.api._SERVICE_NAME_HERE_._SERVICE_METHOD_HERE_();
```

You can find a list of the services and methods available to you under the API/SDK tab in your DSP Admin Console. All the methods adhere to an ajax like structure with data as the first parameter followed by success and error methods.  Because angular-dreamfactory is built upon $http there is no need to use $apply.  Angular is aware of the SDK call.


Here's an example of injecting the DreamFactory service into a controller and executing the login function.

```javascript
// Define a Controller
.controller('MyCtrl', ['DreamFactory', function(DreamFactory) {
  
  
  // model for login credentials
  $scope.creds = {
      "body": {
          "email": '',
          "password": ''
      }
  }

  // Login function
  $scope.loginFunc = function() {
  
    // Call to the DreamFactory user service using provided login method
    // Here was have passed in our success/error callbacks
    DreamFactory.api.user.login($scope.creds,
    
      // Success function
      function(result) {

        // returned data will not be wrapped in a 'data' object

        // Handle login success
        console.log(result)
      },
      
      // Error function
      function(error) {
      
        // Handle Login failure
      });
  }
}]);
```


Using DreamFactory with promises works the same as using promises with $http.  This time we'll demonstrate an AngularJS service built using the DreamFactory service to request a record set from a database and return that with a promise.  We'll also add an extra param to limit the number of records retrieved.

```javascript
// Define a Controller
.controller('MyCtrl', ['MyService', function(MyService) {

  // Params for call
  scope.callParams = {
    table_name: '_YOUR_TABLE_NAME_',
    params: {
        limit: 10
    }
  }


  // Function to call custom service
  $scope.getRecords = function() {

     // call custom service built using DreamFactory that returns a promise
     MyService.getRecords(callParams).then(

     // Success function
      function(result) {

      // We have a returned promise here.
      // your data will be wrapped in a data object.
      // result.data.record

      // Do something with the record set
      console.log(result.data.record)
     },

     // Error function
     function(reject) {

      // Handle error
     });
  }
}]);



// Define a custom service that returns a promise from a call

.service('MyService', ['DreamFactory', function(DreamFactory) {

  return {

    // Define custom getRecords service
    getRecords: function(tableNameStr) {


      // Create request obj
      var request = {
            table_name: tableNameStr
          };

      // Call DreamFactory database service with request obj
      // As long as we don't specify callback/error functions
      // angular-dreamfactory will return promises which we are passing
      // back as the result of the call to MyService.getRecords() in the controller

      return DreamFactory.api.db.getRecords(request);

    }
  }
}]);



```

That's all there is to it!



