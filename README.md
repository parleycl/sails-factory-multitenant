![logo](https://raw.githubusercontent.com/parleycl/sails-factory-multitenancy/master/assets/logo.png)

Sails Multitenant Factory is a simple model factory for Sails.js 1.X with multitenancy features. This project is based on [sails-factory](https://github.com/zand3rs/sails-factory). This project use [sails-hook-multitenancy](https://github.com/parleycl/sails-hook-multitenant) to provide with multitenancy features.

This project is inspired on Factory-girl and other factory testing tools.

## Installation

```bash    
npm install sails-factory-multitenant --save
```

## Usage

### Defining factories

Define a factory by giving it a name and an optional model name. The factory name will be the default model name if model name is not provided.

```js
  var factory = require("sails-factory-multitenant");

  factory.define("user")
    .attr("first_name", "First Name")
    .attr("last_name", "Last Name")
    .attr("random_id", function() { return Math.random(); });

  factory.define("active_user").parent("user")
    .attr("active", true);

  factory.define("admin_user", "Admin").parent("user");
```

### Using factories

```js
  var active_user = factory.build("active_user");
  // active_user: non-persistent "active_user" instance
  // {
  //    first_name: "First Name",
  //    last_name: "Last Name",
  //    random_id: <number>,
  //    active: true
  // }

  var user = factory.build("user", {first_name: "Hello", last_name: function() { return "World"; }});
  // user: non-persistent "user" instance
  // {
  //    first_name: "Hello",
  //    last_name: "World",
  //    random_id: <number>
  // }

  //-- asynchronous
  factory.build("active_user", function(active_user) {
    // active_user: non-persistent "active_user" instance
    // {
    //    first_name: "First Name",
    //    last_name: "Last Name",
    //    random_id: <number>,
    //    active: true
    // }
  });

  factory.build("user", {first_name: "Hello", last_name: function() { return "World"; }}, function(user) {
    // user: non-persistent "user" instance
    // {
    //    first_name: "Hello",
    //    last_name: "World",
    //    random_id: <number>
    // }
  });

  factory.create("active_user", function(active_user) {
    // active_user: sails User model instance
    // {
    //    id: <id>,
    //    first_name: "First Name",
    //    last_name: "Last Name",
    //    random_id: <number>,
    //    active: true,
    //    createdAt: <date>,
    //    updatedAt: <date>
    // }
  });
```

### Auto increment attributes

Attributes can have an auto_increment option. By default, sequence will increment by 1, otherwise it will increment by whatever value the auto_increment option is set to. Counting starts at the initial value given. Sequence is shared among parent and children.

```js
  factory.define("user")
    .attr("id", 0, {auto_increment: true})
    .attr("first_name", "First Name - ", {auto_increment: 5});

  // OR with %d number character template.

  factory.define("user")
    .attr("id", 0, {auto_increment: true})
    .attr("first_name", "First Name - %d", {auto_increment: 5});


  factory.define("other_user").parent("user");

  factory.build("user", function(user) {
    // user:
    // {
    //    id: 1,
    //    first_name: "First Name - 5",
    //    ...
    // }
  });

  factory.create("user", function(user) {
    // user:
    // {
    //    id: 2,
    //    first_name: "First Name - 10",
    //    ...
    // }
  });

  factory.build("other_user", function(other_user) {
    // other_user:
    // {
    //    id: 3,
    //    first_name: "First Name - 15",
    //    ...
    // }
  });
```

### Loading factories

Calling .load() without parameter will try to load factory definitions from test/factories folder. By default, the model name will be set to factory file name if not provided on define parameters.

```js
  // api/models/User.js
  module.exports = {
    attributes: {
      first_name: "string",
      last_name: "string",
      random_id: "integer",
      active: "boolean"
    }
  };

  // test/factories/User.js
  module.exports = function(factory) {
    factory.define("user")
      .attr("first_name", "First Name")
      .attr("last_name", "Last Name")
      .attr("random_id", function() { return Math.random(); });

    factory.define("active_user").parent("user")
      .attr("active", true);
  };

  // test/bootstrap.js
  before(function(done) {
    require("sails").lift({
      log: {
        level: "error"
      }
    }, function(err, sails) {
      if (sails) {
        //-- load factory definition files from test/factories
        require("sails-factory-multitenant").load();
      }
      done(err);
    });
  });
```

To load factory files from different folder:

```js
  factory.load("/path/to/factories");
```

To get the total number of loaded factory files:

```js
  factory.load(function(count) {
    // count is the total number of loaded files
  });
```

### Test

To run test of this library, please follow next command

```bash
npm test
...
33 passing
```

## 9. Contributors

This project is based on [sails-factory](https://github.com/zand3rs/sails-factory) project wrote by Alexander Magtipon.

**Knownledge is power, share the Knownledge.**

## 10. License
This project is develop by Parley for free use by the community, under MIT license. 

Made with ❤ in Chile
