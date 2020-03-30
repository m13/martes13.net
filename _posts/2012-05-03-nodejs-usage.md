---
layout: post
title: nodejs ~ Usage?
post_id: 174
categories: 
- challenges
- cluster
- dbus
- eyeos
- fun
- multiserver
- mutlcore
- nodejs
- redis
- socket
- system bus
- tcp
- tools
- zeromq
---

Hi people,

I would like ask you what is the purpose of nodejs...

Is it only a long polling server with events, or is it anything more? (Some of you said me that it is a server to do parallel tasks...).

I have been developing a long polling Comer server... an HTTP bruteforce (hehe, it is a hacker blog!! I built evil tools too!!)... and a system bus :). So I want to share this last piece because I think that it is quite cool. It is MULTI-CORE and MULTI-SERVER (thanks to Redis) with TCP sockets. If you read it, you will see that it uses 8 bytes + 8 bytes to identify the sender and the listener. I didn't know how to do it better ^^"

Any comment? :) I want to do cool projects with nodejs (& mongodb) but I don't have any idea... give me some please! :)

```javascript
/**
*  NodeJS - Service Cluster BUS using REDIS
http://www.opensource.org/licenses/gpl-2.0.php

$ nodejs sbus.js
(1) $ nc 127.0.0.1 3055; AAAAAAAA
(2) $ echo -e BBBBBBBBAAAAAAAAThisIsSparta | nc 127.0.0.1 3055
*/

process.on('uncaughtException', function (err) {
console.log('Caught exception: ' + err);
});

var redis = require('redis');
var net = require('net');
var cluster = require('cluster');
var numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
console.log('[CPU_MASTER] ' + process.pid);
for (var i = 0; i < numCPUs; i++) {
var worker = cluster.fork();
console.log('[CPU_WORKER] ' + worker.pid);
cluster.on('death', function(worker) {
console.log('[DEATH] worker ' + worker.pid + ' died. restart...');
cluster.fork();
});
}
}
else { // else ?

var listen = redis.createClient();
var client = redis.createClient();

listen.on('subscribe', function (channel, count) {
console.log('[' + process.pid + ']' + ' (subscribe) channel:(' + channel + ') , count:(' + count + ')');
});

listen.on('message', function (to, data) {
var from = data.substr(0,8);
var to = data.substr(8,8);
var msg = data.substr(16);
console.log('[' + process.pid + ']' + ' (message) from:(' + from + ') , to:(' + to + ') , msg.length:(' + msg.length + ')');
process.emit(to, msg);
});

var server = net.createServer(function (socket) {
socket.setTimeout(0);

var firstTime = true;
var from = null;
var to = null;

socket.on('close', function(count) {
console.log('[' + process.pid + ']' + ' (close) socket');
listen.unsubscribe(from);
console.log('[' + process.pid + ']' + ' (unsubscribe) from:(' + from + ') , count:(' + count + ')');
});

socket.setEncoding('utf8');
socket.on('data', function(data){

from = data.substr(0,8);
to = data.substr(8,8);

process.on(from, function(msg) {
console.log('[' + process.pid + ']' + ' (socket.write) from:(' + from + ') , msg.length:(' + msg.length + ')');
socket.write(msg);
});

if (firstTime) {
listen.subscribe(from);
firstTime = false;
}

if (to.length != 8) {
console.log('[' + process.pid + ']' + ' (to.length<8) => registering service');
return;
}

console.log('[' + process.pid + ']' + ' (data) from:(' + from + ') , to:(' + to + ')');
client.publish(to, data);
});
}).listen(3055);
}
```