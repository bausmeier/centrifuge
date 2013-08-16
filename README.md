CENTRIFUGE
==========

Simple platform for real-time message broadcasting in web applications.

The main goal of Centrifuge is the same as in [Pusher](http://pusher.com/) or
[Pubnub](http://www.pubnub.com/) services. The main difference is that Centrifuge is
open-source and requires installation (it is worth noting that installation is rather simple).

It is built on top of [Tornado](http://www.tornadoweb.org/en/stable/) -
extremely fast and mature Python's async web server.

Centrifuge uses [ZeroMQ](http://www.zeromq.org/) steroid sockets for internal
communication and publish/subscribe operations.

For presence and history data Centrifuge utilizes [Redis](http://redis.io/) - advanced and super fast
in memory key-value store.

To connect to Centrifuge from browser pure [Websockets](http://en.wikipedia.org/wiki/WebSocket)
or [SockJS](https://github.com/sockjs/sockjs-client) library can be
used.

Centrifuge comes with administrative web interface to manage project/category structure and monitor important
messages.

Persistent data (projects, categories) by default stored in [SQLite](http://www.sqlite.org/) database.
But when running Centrifuge instance processes on different machines you should use [MongoDB](http://www.mongodb.org/)
or [PostgreSQL](http://www.postgresql.org/) backends instead of SQLite for structure management.

Please, read the [documentation](https://centrifuge.readthedocs.org/en/latest/), watch [screencast](http://www.youtube.com/watch?v=RCLnCexzfOk)
and look at [examples](https://github.com/FZambia/centrifuge/tree/master/examples) for more information.


Basic usage from browser
```javascript
var centrifuge = new Centrifuge();

centrifuge.configure({
    url: 'http://{{centrifuge_address}}/connection',  // use SockJS endpoint (SockJS library must be imported)
    token: '{{auth_data["token"]}}', // token based on project's ID and user ID
    project: '{{auth_data["project"]}}', // project ID from Centrifuge admin interface
    user: '{{auth_data["user"]}}', // your application user ID (can be empty for anonymous access)
});

centrifuge.on('connect', function(){

    console.log('connected');

    var subscription = centrifuge.subscribe('/python/django', function(message) {
        console.log(message);
    });

    subscription.on('subscribe:success', function(){
        subscription.presence(function(message) {
            var count = 0;
            for (var key in message){
                count++;
            }
            console.log('Now in this room: ' + count + ' clients');
        });
    });

});

centrifuge.on('disconnect', function(){
    console.log('disconnected');
});

centrifuge.connect();
```


Architecture diagram
--------------------

![centrifuge](https://raw.github.com/FZambia/centrifuge/master/docs/main/centrifuge_architecture.png "centrifuge")

Admin web interface
-------------------

![admin_web_interface](https://raw.github.com/FZambia/centrifuge/master/docs/main/main.png "admin web interface")


To run tests type the following from `tests` directory (`centrifuge` must be in PYTHONPATH):

```python
# IMPORTANT! Tests clear Redis database on every running. Be aware of this.
python -m unittest discover -p 'test_*.py'
```

Contributing
------------

Pull requests are welcome! But, please, follow next principles:

* keep things as simple as possible
* pep8
* python 2.6, 2.7 and 3.3 compatible

P.S. If BSD license of Centrifuge does not allow you to use it, tell me and I'll consider to change license.
