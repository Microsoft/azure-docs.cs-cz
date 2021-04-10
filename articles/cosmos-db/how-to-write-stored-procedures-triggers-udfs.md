---
title: Zápis uložených procedur, triggerů a UDF v Azure Cosmos DB
description: Naučte se definovat uložené procedury, triggery a uživatelsky definované funkce v Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 7600d8aa2f78e06ea4046273635fdbba18042010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028858"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Postup zápisu uložených procedur, triggerů a uživatelsky definovaných funkcí v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB poskytuje jazykově integrované spouštění transakčního jazyka JavaScript, které vám umožní zapisovat **uložené procedury**, **triggery** a **uživatelsky definované funkce (UDF)**. Při použití rozhraní SQL API v Azure Cosmos DB můžete definovat uložené procedury, triggery a UDF v jazyce JavaScript. Logiku můžete napsat v JavaScriptu a spustit ji uvnitř databázového stroje. Můžete vytvářet a spouštět triggery, uložené procedury a UDF pomocí [Azure Portal](https://portal.azure.com/), [integrovaného rozhraní Query API jazyka JavaScript v Azure Cosmos DB](javascript-query-api.md) a [Cosmos DBch klientských sad SDK pro rozhraní SQL API](sql-api-dotnet-samples.md). 

Chcete-li volat uloženou proceduru, Trigger a uživatelsky definovanou funkci, je nutné ji zaregistrovat. Další informace najdete v tématu [jak pracovat s uloženými procedurami, triggery, uživatelsky definovanými funkcemi v Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md).

> [!NOTE]
> U dělených kontejnerů při provádění uložené procedury musí být v možnostech žádosti uvedena hodnota klíče oddílu. Uložené procedury jsou vždy vymezeny na klíč oddílu. Položky, které mají jinou hodnotu klíče oddílu, nebudou viditelné pro uloženou proceduru. To se také aplikuje i na triggery.
> [!Tip]
> Cosmos podporuje nasazování kontejnerů s uloženými procedurami, triggery a uživatelsky definovanými funkcemi. Další informace najdete v tématu [vytvoření kontejneru Azure Cosmos DB s využitím funkce na straně serveru.](./manage-with-templates.md#create-sproc)

## <a name="how-to-write-stored-procedures"></a><a id="stored-procedures"></a>Postup zápisu uložených procedur

Uložené procedury se napíší pomocí JavaScriptu, můžou vytvářet, aktualizovat, číst, dotazovat a odstraňovat položky v rámci kontejneru Azure Cosmos. Uložené procedury jsou registrovány na kolekci a mohou pracovat v jakémkoli dokumentu nebo v příloze v této kolekci.

Tady je jednoduchá uložená procedura, která vrací odpověď "Hello World".

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

Objekt Context poskytuje přístup ke všem operacím, které lze provádět v Azure Cosmos DB, a také přístup k objektům žádosti a odpovědi. V tomto případě použijete objekt odpovědi k nastavení těla odpovědi, která se má poslat zpátky klientovi.

Po zapsání musí být uložená procedura registrována s kolekcí. Další informace najdete v tématu [použití uložených procedur v Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures) článku.

### <a name="create-an-item-using-stored-procedure"></a><a id="create-an-item"></a>Vytvoření položky pomocí uložené procedury

Když vytvoříte položku pomocí uložené procedury, položka se vloží do kontejneru Azure Cosmos a vrátí se ID nově vytvořené položky. Vytvoření položky je asynchronní operace a závisí na funkcích zpětného volání JavaScriptu. Funkce zpětného volání má dva parametry – jeden pro objekt Error pro případ, že operace se nezdařila, a další pro návratovou hodnotu; v tomto případě vytvořený objekt. Uvnitř zpětného volání můžete buď zpracovat výjimku, nebo vyvolat chybu. V případě, že zpětné volání není k dispozici a dojde k chybě, Azure Cosmos DB runtime vyvolá chybu.

Uložená procedura také obsahuje parametr pro nastavení popisu, jedná se o logickou hodnotu. Pokud je parametr nastaven na hodnotu true a popis chybí, uložená procedura vyvolá výjimku. V opačném případě bude zbývající uložená procedura nadále běžet.

Následující příklad uložená procedura převezme pole nových položek Azure Cosmos jako vstup, vloží ho do kontejneru Azure Cosmos a vrátí počet vložených položek. V tomto příkladu používáme ukázku ToDoList z rychlého startu rozhraní [SQL API pro rychlé](create-sql-api-dotnet.md) zprovoznění.

```javascript
function createToDoItems(items) {
    var collection = getContext().getCollection();
    var collectionLink = collection.getSelfLink();
    var count = 0;

    if (!items) throw new Error("The array is undefined or null.");

    var numItems = items.length;

    if (numItems == 0) {
        getContext().getResponse().setBody(0);
        return;
    }

    tryCreate(items[count], callback);

    function tryCreate(item, callback) {
        var options = { disableAutomaticIdGeneration: false };

        var isAccepted = collection.createDocument(collectionLink, item, options, callback);

        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    function callback(err, item, options) {
        if (err) throw err;
        count++;
        if (count >= numItems) {
            getContext().getResponse().setBody(count);
        } else {
            tryCreate(items[count], callback);
        }
    }
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>Pole jako vstupní parametry pro uložené procedury

Při definování uložené procedury v Azure Portal jsou vstupní parametry vždy odeslány jako řetězec do uložené procedury. I v případě, že předáte pole řetězců jako vstup, pole je převedeno na řetězec a odesláno do uložené procedury. Chcete-li tento problém obejít, můžete definovat funkci v rámci uložené procedury k analýze řetězce jako pole. Následující kód ukazuje, jak analyzovat vstupní parametr řetězce jako pole:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a name="transactions-within-stored-procedures"></a><a id="transactions"></a>Transakce v uložených procedurách

Můžete implementovat transakce pro položky v rámci kontejneru pomocí uložené procedury. Následující příklad používá transakce v rámci herní aplikace v pohádkovém fotbalu do obchodních hráčů mezi dvěma týmy v rámci jedné operace. Uložená procedura se pokusí přečíst dvě položky Azure Cosmos, které odpovídají identifikátorům hráčů předaným jako argument. V případě nalezení obou hráčů pak uložená procedura aktualizuje položky jejich týmy. Pokud dojde k nějakým chybám, uložená procedura vyvolá výjimku jazyka JavaScript, která implicitně přerušuje transakci.

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery =
    {
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };

    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 =
            {
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}]
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a name="bounded-execution-within-stored-procedures"></a><a id="bounded-execution"></a>Vázané spuštění v uložených procedurách

Následuje příklad uložené procedury, která hromadně importuje položky do kontejneru Azure Cosmos. Uložená procedura zpracovává ohraničené spouštění kontrolou návratové hodnoty Boolean z `createDocument` a poté používá počet položek vložených do každého vyvolání uložené procedury ke sledování a obnovení průběhu napříč dávkami.

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

### <a name="async-await-with-stored-procedures"></a><a id="async-promises"></a>Asynchronní operátor await s uloženými procedurami

Následující příklad představuje uloženou proceduru, která používá Async-await s příslibů pomocí pomocné funkce. Dotazy na uloženou proceduru pro položku a nahradí ji.

```javascript
function async_sample() {
    const ERROR_CODE = {
        NotAccepted: 429
    };

    const asyncHelper = {
        queryDocuments(sqlQuery, options) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.queryDocuments(__.getSelfLink(), sqlQuery, options, (err, feed, options) => {
                    if (err) reject(err);
                    resolve({ feed, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        },

        replaceDocument(doc) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.replaceDocument(doc._self, doc, (err, result, options) => {
                    if (err) reject(err);
                    resolve({ result, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        }
    };

    async function main() {
        let continuation;
        do {
            let { feed, options } = await asyncHelper.queryDocuments("SELECT * from c", { continuation });

            for (let doc of feed) {
                doc.newProp = 1;
                await asyncHelper.replaceDocument(doc);
            }

            continuation = options.continuation;
        } while (continuation);
    }

    main().catch(err => getContext().abort(err));
}
```

## <a name="how-to-write-triggers"></a><a id="triggers"></a>Zápis triggerů

Azure Cosmos DB podporuje před triggery a po triggerech. Před změnou položky databáze se spustí předběžné triggery před úpravou položky databáze a následnými triggery. Triggery nejsou spouštěny automaticky, musí být zadány pro každou databázovou operaci, kterou chcete provést. Po definování triggeru byste měli [zaregistrovat a volat předběžnou Trigger](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) pomocí sad Azure Cosmos DB SDK.

### <a name="pre-triggers"></a><a id="pre-triggers"></a>Předběžné triggery

Následující příklad ukazuje, jak se používá předaktivační událost k ověření vlastností položky Azure Cosmos, která se vytváří. V tomto příkladu používáme ukázku ToDoList z rozhraní [SQL API pro rychlé](create-sql-api-dotnet.md)zprovoznění, abyste přidali vlastnost časového razítka k nově přidané položce, pokud neobsahuje jednu.

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

Triggery před akcí nesmí mít žádné vstupní parametry. Objekt Request v triggeru slouží k manipulaci s požadavkem na zprávu s žádostí přidruženou k operaci. V předchozím příkladu se předběžná Trigger spouští při vytváření položky Azure Cosmos a tělo zprávy požadavku obsahuje položku, která se má vytvořit ve formátu JSON.

Pokud jsou triggery registrovány, můžete zadat operace, se kterými může běžet. Tato aktivační událost by měla být vytvořena s `TriggerOperation` hodnotou `TriggerOperation.Create` , což znamená, že pomocí triggeru v operaci nahrazení, jak je znázorněno v následujícím kódu, není povoleno.

Příklady, jak registrovat a volat předběžnou aktivační událost, najdete v článcích [před triggerem](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) a [po triggerech](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) . 

### <a name="post-triggers"></a><a id="post-triggers"></a>Po triggerech

Následující příklad ukazuje po triggeru. Tato aktivační událost se dotazuje na položku metadat a aktualizuje ji s podrobnostmi o nově vytvořené položce.


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

Jedna z věcí, která je důležitá pro poznámení, je spuštění aktivačních událostí v Azure Cosmos DB. Aktivační událost se spouští jako součást stejné transakce pro vlastní podkladovou položku. Při spuštění po triggeru dojde k selhání celé transakce. Jakékoli potvrzené změny se vrátí zpět a vrátí se výjimka.

Příklady, jak registrovat a volat předběžnou aktivační událost, najdete v článcích [před triggerem](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) a [po triggerech](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) . 

## <a name="how-to-write-user-defined-functions"></a><a id="udfs"></a>Zápis uživatelsky definovaných funkcí

Následující ukázka vytvoří systém souborů UDF pro výpočet daně ze zisku pro různé příjmové závorky. Tato uživatelem definovaná funkce by se pak použila v dotazu. Pro účely tohoto příkladu předpokládáme, že existuje kontejner s názvem "příjmy" s vlastnostmi, jak je znázorněno níže:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

Následuje definice funkce pro výpočet daně z příjmu pro různé odpočítané závorky:

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

Příklady, jak registrovat a používat uživatelsky definovanou funkci, najdete [v tématu použití uživatelsky definovaných funkcí v Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs) článku.

## <a name="logging"></a>protokolování 

Při použití uložené procedury, triggerů nebo uživatelsky definovaných funkcí můžete pomocí příkazu zaprotokolovat kroky `console.log()` . Tento příkaz zachová řetězec pro ladění, pokud `EnableScriptLogging` je nastaven na hodnotu true, jak je znázorněno v následujícím příkladu:

```javascript
var response = await client.ExecuteStoredProcedureAsync(
document.SelfLink,
new RequestOptions { EnableScriptLogging = true } );
Console.WriteLine(response.ScriptLog);
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další koncepty a postupy psaní a používání uložených procedur, triggerů a uživatelsky definovaných funkcí v Azure Cosmos DB:

* [Postup registrace a používání uložených procedur, triggerů a funkcí definovaných uživatelem ve službě Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Jak zapisovat uložené procedury a triggery pomocí rozhraní API dotazů jazyka JavaScript v Azure Cosmos DB](how-to-write-javascript-query-api.md)

* [Práce s Azure Cosmos DB uloženými procedurami, triggery a uživatelsky definovanými funkcemi v Azure Cosmos DB](stored-procedures-triggers-udfs.md)

* [Práce s integrovaným rozhraním API pro integrované dotazy jazyka JavaScript v Azure Cosmos DB](javascript-query-api.md)
