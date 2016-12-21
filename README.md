# mongox

Synchronous and Asynchronous access to the Mongo Database.

Chris Munt <cmunt@mgateway.com>  
19 December 2016, M/Gateway Developments Ltd [http://www.mgateway.com](http://www.mgateway.com)

## Pre-requisites 

**mongox** is a Node.js addon written in C++.  It is distributed as C++ source code and the NPM installation procedure will expect a C++ compiler to be present on the target system.

Linux systems can use the freely available GNU C++ compiler (g++) which can be installed as follows.

Ubuntu:

       apt-get install g++

Red Hat and CentOS:

       yum install gcc-c++

Apple OS X can use the freely available **Xcode** development environment.

There are two options for Windows, both of which are free:

* Microsoft Visual Studio Community: [https://www.visualstudio.com/vs/community/](https://www.visualstudio.com/vs/community/)
* MinGW: [http://www.mingw.org/](http://www.mingw.org/)

If the Windows machine is not set up for systems development, building native Addon modules for this platform from C++ source can be quite arduous.  There is some helpful advice available at:

* [Compiling native Addon modules for Windows](https://github.com/Microsoft/nodejs-guidelines/blob/master/windows-environment.md#compiling-native-addon-modules)

Alternatively there are built Windows x64 binaries available from:

* [https://github.com/chrisemunt/mongox/blob/master/bin/winx64](https://github.com/chrisemunt/mongox/blob/master/bin/winx64)

## Installing mongox

Assuming that Node.js is already installed and a C++ compiler is available to the installation process:

       npm install mongox

This command will create the **mongox** addon (*mongox.node*).

## Documentation

Most **mongox** methods are capable of operating either synchronously or asynchronously. For an operation to complete asynchronously, simply supply a suitable callback as the last argument in the call.

The first step is to add **mongox** to your Node.js script

       var mongodb = require('mongox');

#### Create a Server Object

       var db = new mongodb.server();

#### Open a connection to the Server

Synchronous:

       var result = db.open({address: <address>, port: <port>});

Asynchronous:

       db.open({address: <address>, port: <port>}, callback(<error>, <result>));

Result Object:

       {
          ok: <ok flag>
          [, ErrorMessage: <message>]
          [, ErrorCode: <code>]
       }
     
If the operation is successful, the *ok flag* will be set to *true*. Otherwise, the *ok flag* will be set to *false* and error information will be returned in the *ErrorMessage* and *ErrorCode* fields.

For example, create a server connection object to a local MongoDB installation listening on the *default* port (27017).

       var result = db.open({address: "localhost", port: 27017});

#### Close the connection to the Server

Synchronous:

       var result = db.close();

Asynchronous:

       db.close(callback(<error>, <result>));

Result Object:

       {
          ok: <ok flag>
          [, ErrorMessage: <message>]
          [, ErrorCode: <code>]
       }
     
If the operation is successful, the *ok flag* will be set to *true*. Otherwise, the *ok flag* will be set to *false* and error information will be returned in the *ErrorMessage* and *ErrorCode* fields.

#### Insert a new Document

Synchronous:

       var result = db.insert(<database>.<collection>, <document>);

Asynchronous:

       db.insert(<database>.<collection>, <document>, callback(<error>, <result>));
      
Result Object:

       {
          ok: <ok flag>,
          _id: <document id>
          [, ErrorMessage: <message>]
          [, ErrorCode: <code>]
       }
     
If the operation is successful, the *ok flag* will be set to *true* and the document's unique ID returned. Otherwise, the *ok flag* will be set to *false* and error information will be returned in the *ErrorMessage* and *ErrorCode* fields.

Example:

       var result = db.insert("company.employee", {emp_no: 1, name: "Chris Munt"});

#### Insert a set of new Documents

This method is the same as *insert* except that it will take an array of Documents for inserting.

Synchronous:

       var result = db.insert_batch(<database>.<collection>, [<document>, ...]);

Asynchronous:

       db.insert_batch(<database>.<collection>, [<document>, ...], callback(<error>, <result>));
      
Result Object (*an array of document IDs or notification of errors*):

       [
          {
             ok: <ok flag>,
             _id: <document id>
             [, ErrorMessage: <message>]
             [, ErrorCode: <code>]
          },
          ...
       ]
     
For each Document, if the operation is successful, the *ok flag* will be set to *true* and the document's unique ID returned. Otherwise, the *ok flag* will be set to *false* and error information will be returned in the *ErrorMessage* and *ErrorCode* fields.

Example:

       var result = db.insert_batch("company.employee", [{emp_no: 2, name: "Rob Tweed"},
                                                         {emp_no: 3, name: "John Smith"}]);
   
#### Update an existing Document


Synchronous:

       var result = db.update(<database>.<collection>, <old document>, <new document>);

Asynchronous:

       db.update(<database>.<collection>, <old document>, <new document>, callback(<error>, <result>));
      
Result Object:

       {
          ok: <ok flag>,
          _id: <document id>
          [, ErrorMessage: <message>]
          [, ErrorCode: <code>]
       }
     
If the operation is successful, the *ok flag* will be set to *true* and the document's unique ID returned. Otherwise, the *ok flag* will be set to *false* and error information will be returned in the *ErrorMessage* and *ErrorCode* fields.

Example:

       var result = db.update("company.employee", {emp_no: 3}, {emp_no: 3, name: "Jane Doe"});
       

#### Remove Document(s)

Synchronous:

       var result = db.remove(<database>.<collection>, <document>);

Asynchronous:

       db.remove(<database>.<collection>, <document>, callback(<error>, <result>));
      
Result Object:

       {
          ok: <ok flag>
          [, ErrorMessage: <message>]
          [, ErrorCode: <code>]
       }
     
If the operation is successful, the *ok flag* will be set to *true*. Otherwise, the *ok flag* will be set to *false* and error information will be returned in the *ErrorMessage* and *ErrorCode* fields.

Example (*remove a specific Document*):

       var result = db.remove("company.employee", {emp_no: 3});

Example (*remove all Documents from the Collection*):

       var result = db.remove("company.employee", {});


#### Retrieve Document(s)

Synchronous:

       var result = db.find(<database>.<collection>, <query>);

Asynchronous:

       db.find(<database>.<collection>, <query>, callback(<error>, <result>));
      
Result Object:

       {
          ok: <ok flag>,
          data: <results>
          [, ErrorMessage: <message>]
          [, ErrorCode: <code>]
       }
     
If the operation is successful, the *ok flag* will be set to *true*. Otherwise, the *ok flag* will be set to *false* and error information will be returned in the *ErrorMessage* and *ErrorCode* fields.

The results will be held in the *data* field.

Example (*retrieve all Documents in the Collection*):

       var result = db.find("company.employee", {});

Example (*retrieve a specific Document from the Collection*):

       var result = db.find("company.employee", {$query: {emp_no: 1}});

Example (*return optimization information for a query*):

       var result = db.find("company.employee", {$query: {emp_no: 1}, $explain: 1});

#### Create an Index for a Collection

Synchronous:

       var result = db.create_index(<database>.<collection>, <index atributes>,
                                    <index name>, <index options>);

Asynchronous:

       db.create_index(<database>.<collection>, <index atributes>,
                       <index name>, <index options>, callback(<error>, <result>));
      
Result Object:

       {
          ok: <ok flag>
          [, ErrorMessage: <message>]
          [, ErrorCode: <code>]
       }
     
If the operation is successful, the *ok flag* will be set to *true*. Otherwise, the *ok flag* will be set to *false* and error information will be returned in the *ErrorMessage* and *ErrorCode* fields.

Example (*create an index on the 'emp_no' field*):

       var result = db.create_index("company.employee", {emp_no: 1}, "emp_no_index",
                                    "MONGO_INDEX_UNIQUE, MONGO_INDEX_DROP_DUPS");


#### Invoke a MongoDB Command

Synchronous:

       var result = db.command(<database>, <command>);

Asynchronous:

       db.command(<database>, <command>, callback(<error>, <result>));
      
The Result Object will depend on the nature of the Command invoked.  The result is always expressed as a JSON Document.

Example (*Obtain a list of all Commands*):

       var result = db.command("company", {listCommands : ""});

Example (*Get the build information for this MongoDB installation*):

       var result = db.command("company", {buildInfo : ""});

Example (*Get information about the host*):

       var result = db.command("company", {hostInfo : ""});

Example (*Get a list of databases*):

       var result = db.command("admin", {listDatabases : 1});

Example: (*Get statistics for a the 'company' Database*):

       var result = db.command("company", {dbStats : 1});

Example (*Get statistics for the 'admin' Database*):

       var result = db.command("admin", {dbStats : 1});

Example (*Return the number of documents in the 'employee' Collection*):

       var result = db.command("company", {count: "employee"});

Example (*Get statistics for the 'person' Collection*):

       var result = db.command("company", {collStats : "employee"});

## License

Copyright (c) 2013-2017 M/Gateway Developments Ltd,
Surrey UK.                                                      
All rights reserved.
 
http://www.mgateway.com                                                  
Email: cmunt@mgateway.com
 
 
Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.      
