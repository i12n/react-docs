# Server-Send Events

服务器推送事件（Server-sent Events）是 HTML 5 规范中的一个组成部分，可以用来从服务端实时推送数据到浏览器端。它的主要特点是，数据传送是单向的，只能从服务端向浏览器推送数据，浏览器可以订阅服务端推送来的数据。

```
var http = require('http');
var sys = require('sys');
var fs = require('fs');

http.createServer(function(req, res) {
  //debugHeaders(req);

  if (req.headers.accept && req.headers.accept == 'text/event-stream') {
    if (req.url == '/events') {
      sendSSE(req, res);
    } else {
      res.writeHead(404);
      res.end();
    }
  } else {
    res.writeHead(200, {'Content-Type': 'text/html'});
    res.write(fs.readFileSync(__dirname + '/sse-node.html'));
    res.end();
  }
}).listen(8000);

function sendSSE(req, res) {
  res.writeHead(200, {
    'Content-Type': 'text/event-stream',
    'Cache-Control': 'no-cache',
    'Connection': 'keep-alive'
  });

  var id = (new Date()).toLocaleTimeString();

  // Sends a SSE every 5 seconds on a single connection.
  setInterval(function() {
    constructSSE(res, id, (new Date()).toLocaleTimeString());
  }, 5000);

  constructSSE(res, id, (new Date()).toLocaleTimeString());
}

function constructSSE(res, id, data) {
  res.write('id: ' + id + '\n');
  res.write("data: " + data + '\n\n');
}

function debugHeaders(req) {
  sys.puts('URL: ' + req.url);
  for (var key in req.headers) {
    sys.puts(key + ': ' + req.headers[key]);
  }
  sys.puts('\n\n');
}
```
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
</head>
<body>
    <script>
        var source = new EventSource('/events');
        source.onmessage = function(e) {
            document.body.innerHTML += e.data + '<br>';
        };
    </script>
</body>
</html>
~
```

[https://www.html5rocks.com/en/tutorials/eventsource/basics/](https://www.html5rocks.com/en/tutorials/eventsource/basics/)