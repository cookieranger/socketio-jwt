Access [Passport.js](http://passportjs.org) user information from [socket.io](http://socket.io) connection.


Installation
============

```
npm install passport.socketio
```

Usage 
=====


```javascript

  //configure passport and express

  var socketIo = require("socket.io"),
    passportSocketIo = require("passport.socketio");

  var sio = socketIo.listen(webServer);


  //except for the optional fail and success the parameter object has the 
  //same attribute than the session middleware http://www.senchalabs.org/connect/middleware-session.html

  sio.set("authorization", passportSocketIo.authorize({
    key:    'express.sid',       //the cookie where express (or connect) stores its session id.
    secret: 'my session secret', //the session secret to parse the cookie
    store:   mySessionStore,     //the session store that express uses
    fail: function(data, accept) {     // *optional* callbacks on success or fail
      accept(null, false);             // second param takes boolean on whether or not to allow handshake
    },
    success: function(data, accept) {
      accept(null, true);
    }
  }));

  sio.sockets.on("connection", function(socket){
    console.log("user connected: ", socket.handshake.user.name);
    
    //filter sockets by user...
    var userGender = socket.handshake.user.gender, 
        opposite = userGender === "male" ? "female" : "male";

    passportSocketIo.filterSocketsByUser(sio, function (user) {
      return user.gender === opposite;
    }).forEach(function(s){
      s.send("a " + userGender + " has arrived!");
    });

  });

```

Develop
=======

  npm install
  npm test


License
========

MIT - José F. Romaniello 2012.
