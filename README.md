ConciseStaticHttpServer
=================

node Concise Static Http Server

based on an answer by @Jeff Ward for

http://stackoverflow.com/questions/7268033/basic-static-file-server-in-nodejs

**Simple Usage:**

    httpServer(absoluteDir).listen(port);
    
**with Callback:**
```

path = require('path');

var HTTPserver =
			httpServer(path
				.join(__dirname, 'www'))
			.listen(9999, function()
			{
				console.log('HTTP listening 9999');
			});
``` 
----

**httpServer**

```
var httpServer = function(dir)
		{
			var http = require('http');
			var fs = require('fs');
			var path = require("path");
			var url = require('url');

			var mimeTypes = {
				"html": "text/html",
				"jpeg": "image/jpeg",
				"jpg": "image/jpeg",
				"png": "image/png",
				"js": "text/javascript",
				"css": "text/css"
			};

			return http.createServer(function(req, res)
			{
				var uri = url.parse(req.url)
					.pathname;
				var filename = path.join(dir, unescape(uri));
				var indexFilename = path.join(dir, unescape('index.html'));
				var stats;

				console.log(filename);

				try
				{
					stats = fs.lstatSync(filename); // throws if path doesn't exist
				}
				catch (e)
				{
					res.writeHead(404,
					{
						'Content-Type': 'text/plain'
					});
					res.write('404 Not Found\n');
					res.end();
					return;
				}


				if (stats.isFile())
				{
					// path exists, is a file
					var mimeType = mimeTypes[path.extname(filename)
						.split(".")[1]];
					res.writeHead(200,
					{
						'Content-Type': mimeType
					});

					var fileStream =
						fs.createReadStream(filename)
						.pipe(res);
				}
				else if (stats.isDirectory())
				{
					// path exists, is a directory
					res.writeHead(200,
					{
						'Content-Type': "text/html"
					});
					var fileStream =
						fs.createReadStream(indexFilename)
						.pipe(res);
				}
				else
				{
					// Symbolic link, other?
					// TODO: follow symlinks?  security?
					res.writeHead(500,
					{
						'Content-Type': 'text/plain'
					});
					res.write('500 Internal server error\n');
					res.end();
				}

			});
		};
```
