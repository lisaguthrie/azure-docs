---
title: Use MongoDB APIs to build an Azure Cosmos DB app | Microsoft Docs
description: A tutorial that creates an online database using the Azure Cosmos DB APIs for MongoDB.
keywords: mongodb examples
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
documentationcenter: ''

ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: sngun

---
# Build an Azure Cosmos DB: API for MongoDB app using Node.js
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js for MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
>

This example shows you how to build an Azure Cosmos DB: API for MongoDB console app using Node.js.

To use this example, you must:

* [Create](create-mongodb-dotnet.md#create-account) an Azure Cosmos DB: API for MongoDB account.
* Retrieve your MongoDB [connection string](connect-mongodb-account.md) information.

## Create the app

1. Create a *app.js* file and copy & paste the code below.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Optional**: If you are using the **MongoDB Node.js 2.2 driver**, please replace the following code snippet:

    Original:

    ```nodejs
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Should be replaced with:

    ```nodejs
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Modify the following variables in the *app.js* file per your account settings (Learn how to find your [connection string](connect-mongodb-account.md)):

    > [!IMPORTANT]
    > The **MongoDB Node.js 3.0 driver** requires encoding special characters in the Cosmos DB password. Make sure to encode '=' characters as %3D
    >
    > Example: The password *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* encodes to *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > The **MongoDB Node.js 2.2 driver** does not require encoding special characters in the Cosmos DB password.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Open your favorite terminal, run **npm install mongodb --save**, then run your app with **node app.js**

## Next steps
* Learn how to [use MongoChef](mongodb-mongochef.md) with your Azure Cosmos DB: API for MongoDB account.
