# izy-server-sql
Node.js server side plugin for handling sql calls from the client side

# Usage 

```
var cfgs = {
 production: {
  host: 'mydb.amazonaws.com',
  user: 'izyware',
  password: 'strongpassowrd',
  database: 'optional' 
 }
};
var izyServerSql = require('izy-server-sql').sp('cfgs', cfgs);
```

izyServerSql can be plugged into the izy-server framework. 

On the client side, in each query session, you can identify which db config to connect to by using db=. For example:

```
var qs = [];
qs.push('db=production');
qs.push('select firstname, lastname from customerdb.customertable');
node.runQuery2(qa, (rows) => {}, (outcome) => console.log(outcome.reason));
```

## Example 

Use this plug-in to add mySQL connectivity to your izy-server.

```
const port = 3000;
const https = require('https');
const fs = require('fs');

 var options = {
   key: fs.readFileSync('key.pem'),
   cert: fs.readFileSync('key-cert.pem')
 };

var izyServer = require('izy-server');
var izyServerSql = require('izy-server-sql').sp('cfgs', require('../private/config/sql.js'));

const requestHandler = (request, response) => {
   console.log('requestHandler', request.url);
   if (request.url == '/favicon.ico') {
     return response.end('');
   }
   var cfg = {};
   var plugins = [izyServerSql];
   var i = 0;
   while(i < plugins.length) {
     var plugin = plugins[i];
     if (plugin.canHandleRequest(request)) {
       cfg = { processReq: plugin.handleRequest };
       break;
     }
     i++;
   };
   return izyServer(cfg).handleRequest(request, response);
}

https.createServer(options, requestHandler).listen(port);
console.log('Listening on port: ', port);
```
