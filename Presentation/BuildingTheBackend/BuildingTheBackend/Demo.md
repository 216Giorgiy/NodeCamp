﻿<a name="title" />
# Building the Backend#

---
<a name="Overview" />
## Overview ##

A Websocket is a protocol designed to allow web applications to create a full-duplex channel over TCP between the web browser and a web server. It is fully compatible with HTTP and uses TCP port number 80. 
WebSockets allowed web applications to become real-time and support advanced interactions between the client and the server. 

Socket.IO is a simple JavaScript library and Node.js module that allows you to create real-time bidirectional event-based communication apps simply and quickly. It simplifies the process of using WebSockets significantly.

Azure DocumentDB is a NoSQL document database service designed from the ground up to natively support JSON and JavaScript directly inside the database engine. It’s the right solution for applications that run in the cloud when predictable throughput, low latency, and flexible query are key.

This demo introduces the use of the Socket.IO module that allows to create real-time bidirectional communication. Here we see how to connect, broadcast and receive messages in a chat app. It also shows how to use DocumentDB, a NoSQL database to save and retrieve messages.

<a id="goals" />
### Goals ###
In this demo, you will see how to:

1. Create a bidirectional communication between client and server by using Socket.IO module.

1. Add DocumentDB to your app for retrieving and saving messages.

<a name="technologies" />
### Key Technologies ###

- [Node.js][1]
- [Socket.IO][2]
- [DocumentDB][3]
- [Node.js Tools for Visual Studio][4]
- [Visual Studio Community 2013][5]

[1]: https://nodejs.org/
[2]: http://socket.io/
[3]: http://azure.microsoft.com/en-us/services/documentdb/
[4]: https://www.visualstudio.com/en-us/features/node-js-vs.aspx
[5]: https://www.visualstudio.com/en-us/features/node-js-vs.aspx

<a name="Setup" />
### Setup and Configuration ###
Follow these steps to set up your environment for the demo.

1. Install [Visual Studio Community 2013](https://go.microsoft.com/fwlink/?LinkId=517284).
1. Install [Node.js](https://nodejs.org/download/)
1. Install [Node.js Tools for Visual Studio](http://aka.ms/getntvs).

1. Open Windows Explorer and browse to the **source** folder.

1. Right-click on **Setup.cmd** and select **Run as administrator** to launch the setup process that will configure your environment and install the Visual Studio code snippets for this demo.

1. If the User Account Control dialog box is shown, confirm the action to proceed.

1. Open Visual Studio.

1. Open the **Chatroom.sln** solution located under **source\Begin**.

1. Install the missing npm packages by right-clicking on the **npm** node of the project and selecting **Install Missing npm Packages...**.

	![Installing Missing npm Packages](Images/installing-missing-npm-packages.png?raw=true "Installing Missing npm Packages")

	_Installing Missing npm Packages_

<a name="CodeSnippets" />
### Using the Code Snippets ###

Throughout the demo document, you will be instructed to insert code blocks. For your convenience, most of this code is provided as Visual Studio Code Snippets, which you can access from within Visual Studio to avoid having to add it manually.

> **Note:** This demo is accompanied by a starting solution located in the **Begin** folder that allows you to follow the demo. Inside the source code you will also find an **End** folder containing a Visual Studio solution with the code that results from completing the steps in the demo. You can use these solutions as guidance if you need additional help as you work through this demo.

---

<a name="Demo" />
## Demo ##
This demo is composed of the following segments:

1. [Creating a chat server with Socket.IO](#segment1)
1. [Saving messages into a DocumentDB database](#segment2)
1. [Appendix: Saving messages into a MongoDB database](#appendix1)

<a name="segment1" />
### Creating a chat server with Socket.IO ###

1. Right-click on the **npm** node of the project and select **Install New npm Packages...**.

	![Installing Node packages](images/installing-node-packages.png?raw=true "Installing Node packages")

	_Installing new Node package_

1. Search for **Socket.IO**. Select the top result and make sure the **Add to package.json** checkbox is checked before installing the package. 

	> **Note:** This will install socket.io into your project and add it to the package.json file. You can achieve the same by running the `npm install --save socket.io` command.

	![Installing Socket.IO package](images/installing-socket-io-package.png?raw=true "Installing Socket.IO package")

	_Installing Socket.IO package_

1. Show the **package.json** file located at the root folder of the project.

	![Showing the package.json file](images/showing-the-package-json-file.png?raw=true "Showing the package.json file")

	_Showing the package.json file_

1. Right-click on the project and select **Add | New Item...**.

	![Creating a new item](images/creating-a-new-item.png?raw=true "Creating a new item")

	_Creating a new item_

1. In the **Add New Item** dialog box, select **JavaScript file**, name it **socketio.js** and click **Add**.

	![Creating a new JavaScript file](images/creating-a-new-js-file.png?raw=true "Creating a new JavaScript file")

	_Creating a new JavaScript file_

1. Copy the following code snippet in the **socketio.js** file you created.

	> **Speaking point:** We will log each user entering the chatroom by hooking a callback function to be executed on every single “connection” event via WebSocket to our HTTP server. 

	(Code Snippet - _BuildingTheBackend-SocketioOnConnection_)

	````JavaScript
	module.exports = function (io, config) {
		 io.on('connection', function (socket) {
			  console.log('a user connected');
		 });
	};
	````

1. Add the following code snippet inside the callback of the **connection** event.

	> **Speaking point:** To do the same for when a user leaves, we have to hook up to the “disconnect” event for each socket.

	(Code Snippet - _BuildingTheBackend-SocketioOnDisconnect_)

	````JavaScript
	socket.on('disconnect', function () {
		console.log('user disconnected');
	});
	````

1. Add the following code snippet inside the callback of the **connection** event below the **disconnect** event handler.

	> **Speaking point:** Socket.IO gives us a function called **emit** that we can use to send an event. We will use it to broadcast any message received on the “chat” channel to all the other connections on this socket.

	(Code Snippet - _BuildingTheBackend-SocketioOnChat_)

	````JavaScript
	socket.on('chat', function (msg) {
		socket.broadcast.emit('chat', msg);
	});
	````

1. Open the **www** file located at the **bin** folder and insert the following code snippet at the end of the file.

	> **Speaking point:** Here we capture the HTTP server created by express in a variable called **server** and pass it to the socket.io module so it can attach to it.
We then pass the socket.io instance to the module we created.

	(Code Snippet - _BuildingTheBackend-SocketioInitialization_)

	````JavaScript
	var io = require('socket.io')(server);
	require('../socketio')(io);
	````

<a name="segment2" />
### Saving messages into a DocumentDB database ###

1. Install **documentdb** package as you did with **socket.IO** package in previous segment.

	![Installing documentdb package](images/installing-documentdb-package.png?raw=true "Installing documentdb package")

	_Installing documentdb package_

1. Add a new JavaScript file named **docdbUtils.js** to the project.

1. Add the following code snippet in the **docdbUtils.js** file you created.

	(Code Snippet - _BuildingTheBackend-DocDBUtilsStructure_)

	````JavaScript
	var DocDBUtils = {
	};

	module.exports = DocDBUtils;
	````

1. Add the following function inside the DocDBUtils object definition to get or create a database.

	(Code Snippet - _BuildingTheBackend-DocDBUtilsGetOrCreateDatabase_)

	````JavaScript
    getOrCreateDatabase: function (client, databaseId, callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                    name: '@id',
                    value: databaseId
                }]
        };
        
        client.queryDatabases(querySpec).toArray(function (err, results) {
            if (err) {
                callback(err);

            } else {
                if (results.length === 0) {
                    var databaseSpec = {
                        id: databaseId
                    };
                    
                    client.createDatabase(databaseSpec, function (err, created) {
                        callback(null, created);
                    });

                } else {
                    callback(null, results[0]);
                }
            }
        });
    },
	````

1. Add the following function inside the DocDBUtils object definition to get or create a collection below the previous function.

	(Code Snippet - _BuildingTheBackend-DocDBUtilsGetOrCreateCollection_)

	````JavaScript
	getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                    name: '@id',
                    value: collectionId
                }]
        };
        
        client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
            if (err) {
                callback(err);

            } else {
                if (results.length === 0) {
                    var collectionSpec = {
                        id: collectionId
                    };
                    
                    var requestOptions = {
                        offerType: 'S1'
                    };
                    
                    client.createCollection(databaseLink, collectionSpec, requestOptions, function (err, created) {
                        callback(null, created);
                    });

                } else {
                    callback(null, results[0]);
                }
            }
        });
    },
	````

1. Then, add the following function at the end of the DocDBUtils object definition to simplify the initialization of a collection.

	(Code Snippet - _BuildingTheBackend-DocDBUtilsInitCollection_)

	````JavaScript
    initCollection: function (client, databaseId, collectionId, callback) {
        var self = this;
        
        DocDBUtils.getOrCreateDatabase(client, databaseId, function (err, db) {
            if (err) {
                callback(err);
            } else {
                DocDBUtils.getOrCreateCollection(client, db._self, collectionId, callback);
            }
        });
    }
	````

1. Now, open the **socketio.js** file you created in the previous section and add the following code snippet at the beggining of the file.

	(Code Snippet - _BuildingTheBackend-DocDBInitialization_)

	````JavaScript
	var DocumentDBClient = require('documentdb').DocumentClient;
	var docdbUtils = require('./docdbUtils');
	````

1. Add the following code snippet below the `console.log('a user connected');`.

	````JavaScript
	var docDbClient = new DocumentDBClient(process.env.DOCUMENT_DB_HOST, {
		 masterKey: process.env.DOCUMENT_DB_AUTH_KEY
	});

	docdbUtils.initCollection(docDbClient, 'chatroom', 'messages', function (err, collection) {
		 if (err) {
			  console.warn(err.message);
		 } else {
			  // TODO: Add code here.
		 }
	});
	````

1. Add the following code snippet inside the chat event handler and before the **socket.broadcast.emit** function call.

	````JavaScript
	docdbUtils.initCollection(docDbClient, 'chatroom', 'messages', function (err, collection) {
		 if (err) {
			  console.warn(err.message);
		 } else {
			  var documentDefinition = { content: msg };
			  docDbClient.createDocument(collection._self, documentDefinition, function (err, o) {
					if (err) {
						 console.warn(err.message);
					} else {
						 console.log("chat message inserted into db: " + msg);
					}
			  });
		 }
	});
	````

---

<a name="appendix1" />
### Appendix: Saving messages into a MongoDB database ###

1. Install **MongoDB** package as you did with **socket.IO** package in previous segment.

1. Add the following code before `var app = express();` line in **app.js** file:

	`var mongo = require('mongodb').MongoClient;`

1. Add the next code snippet inside the "connection" event and after the `console.log('a user connected');` line:

	> **Speaking point:** We want to give our users the last 10 messages from the chatroom so they have some context when they just joined. To do that, we need to connect to Mongo. We use the **limit** function to limit the results to only 10 messages. We will stream the results from Mongo so that we emit them as soon as they arrive to the chatroom.
	
	(Code Snippet - _NodeJsSocketIO-mongoDB-retrieve-messages_)

	````JavaScript
    mongo.connect(process.env.CUSTOMCONNSTR_MONGOLAB_URI, function (err, db) {
        if(err){
            console.warn(err.message);
        } else {
            var collection = db.collection('chat messages')
            var stream = collection.find().sort().limit(10).stream();
            stream.on('data', function (chat) { console.log('emitting chat'); socket.emit('chat', chat.content); });
        }
    });
	````

1. Add the next code snippet inside the "chat" event and before the `socket.broadcast.emit('chat', msg);` line:

	> **Speaking point:** We want to connect to the database using the URI we have in the **CUSTOMCONNSTR_MONGOLAB_URI** environment variable and insert the chat message received in the socket connection.

	(Code Snippet - _NodeJsSocketIO-mongoDB-save-message_)

	````JavaScript
	mongo.connect(process.env.CUSTOMCONNSTR_MONGOLAB_URI, function (err, db) {
		if (err) {
			console.warn(err.message);
		} else {
			var collection = db.collection('chat messages');
			collection.insert({ content: msg }, function (err, o) {
				if (err) { console.warn(err.message); }
				else { console.log("chat message inserted into db: " + msg); }
			});
		}
	});
	````

---

<a name="summary" />
## Summary ##

By completing this demo, you have learned how to create a simple chat application where communictaion between users and server is implemented using Socket.IO module. You have also seen how to save/retrieve messages to/from a NoSQL database like MongoDB.

---