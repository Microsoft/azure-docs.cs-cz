---
title: Jak napsat uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB
description: Další informace o definování uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/11/2018
ms.author: mjbrown
ms.openlocfilehash: 7f11579988d965723fedb3d7900f12979ad0feb1
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043902"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Jak napsat uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB

Azure Cosmos DB poskytuje integrovaný jazyk, transakční provádění jazyka JavaScript, který umožňuje zapisovat **uložených procedur komponentami TableAdapter**, **triggery**, a **uživatelem definované funkce (UDF)**. Když ve službě Azure Cosmos DB pomocí rozhraní SQL API, můžete definovat uložené procedury, triggery a uživatelem definovanými funkcemi v jazyce JavaScript. Můžete napsat svoji logiku v jazyce JavaScript a spustit ho v databázovém stroji. Můžete vytvářet a spouštět aktivační události, uložených procedur a uživatelem definovanými funkcemi pomocí [webu Azure portal](https://portal.azure.com/), [jazyka JavaScript integrovaná rozhraní API pro dotazy ve službě Azure Cosmos DB](javascript-query-api.md) a [klienta SQL API služby Cosmos DB Sady SDK](sql-api-dotnet-samples.md). 

Volání uložené procedury, aktivační události a uživatelsky definované funkce, musíte ho zaregistrovat. Další informace najdete v tématu [jak pracovat s uložené procedury, aktivační události, uživatelem definovaných funkcí ve službě Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md).

## <a id="stored-procedures"></a>Jak napsat uložených procedur

Uložené procedury jsou zapsány pomocí jazyka JavaScript, mohou vytvořit, aktualizovat, čtení, dotazování a odstraňování položek uvnitř kontejneru Azure Cosmos. Uložené procedury jsou registrované na kolekci a mohou pracovat v libovolné dokumentu nebo přílohy k dispozici v dané kolekci.

**Příklad**

Tady je jednoduchou uloženou proceduru, která vrací odpověď "Hello World".

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

Objekt kontextu poskytuje přístup ke všem operacím, které lze provést ve službě Azure Cosmos DB, a také přístup k objektům žádostí a odpovědí. V tomto případě použijete objekt odpovědi nastavit text odpovědi k odeslání zpět do klienta.

Jakmile zapsána, uložené procedury musí být zaregistrovaná s kolekcí. Další informace najdete v tématu [jak ve službě Azure Cosmos DB pomocí uložené procedury](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures) článku.

### <a id="create-an-item"></a>Vytvořte položku pomocí uložené procedury

Když budete vytvářet položku pomocí uložené procedury, položka je vložen do kontejneru Azure Cosmos DB a id pro nově vytvořenou položku, kterou vrátil. Vytvoření položky je asynchronní operace a závisí na zpětné volání funkce jazyka JavaScript. Funkce zpětného volání má dva parametry – jeden pro objekt error, v takovém případě se operace nezdaří a druhý pro návratovou hodnotu; v tomto případě vytvořený objekt. Uvnitř zpětného volání můžete zpracovat výjimku nebo vyvolat chybu. V případě není k dispozici zpětné volání a dojde k chybě, modul runtime služby Azure Cosmos DB vyvolá chybu. 

Uložená procedura také obsahuje parametr nastavit popis, je logická hodnota. Pokud parametr je nastaven na hodnotu true a popis chybí, uložené procedury vyvolá výjimku. V opačném případě zbývající uložené procedury zůstane spuštěný.

Následující příklad uložené postupu přijímá jako vstup novou položku služby Azure Cosmos DB, vloží do kontejneru Azure Cosmos DB a vrátí id pro nově vytvořenou položku. V tomto příkladu jsme využívají vzorová z [rozhraní SQL API služby .NET rychlý start](create-sql-api-dotnet.md)

```javascript
function createToDoItem(itemToCreate) {

    var context = getContext();
    var container = context.getCollection();

    var accepted = container.createDocument(container.getSelfLink(),
        itemToCreate,
        function (err, itemCreated) {
            if (err) throw new Error('Error' + err.message);
            context.getResponse().setBody(itemCreated.id)
        });
    if (!accepted) return;
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>Pole jako vstupní parametry pro uložené procedury 

Při definování uložené procedury na webu Azure portal, vstupní parametry jsou vždy odesílat jako řetězec uloženou proceduru. I v případě, předejte pole řetězců jako vstupní pole je převést na řetězec a odesílat na uložené procedury. Chcete-li tento problém obejít, můžete definovat funkce v rámci uložené procedury k analýze řetězce jako pole. Následující kód ukazuje, jak analyzovat parametr vstupní řetězec jako pole:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a id="transactions"></a>Transakce v rámci uložené procedury

Transakce na položky v kontejneru můžete implementovat pomocí uložené procedury. Následující příklad používá transakce v rámci herní aplikace fantasy fotbalu přehrávačům kompromisy mezi dvěma týmy v rámci jedné operace. Uložená procedura se pokusí přečíst dvě položky služby Azure Cosmos DB, které každý odpovídající ID player předanou jako argument. Pokud se najdou oba hráči, uložená procedura aktualizuje položky pomocí výměny týmů. Pokud nedojde k chybám na cestě, uložené procedury výjimku jazyka JavaScript, která se implicitně zruší transakce.

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
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

### <a id="bounded-execution"></a>Ohraničené provádění v rámci uložené procedury

Následuje příklad uloženou proceduru, která hromadného importu položky do kontejneru Azure Cosmos. Uložená procedura zpracovává ohraničené spouštění tak, že zkontrolujete logická hodnota vrácená z `createDocument`a potom využívá ke sledování a pokračovat v průběhu napříč dávkami počet položek vložených do každého volání uložené procedury.

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

## <a id="triggers"></a>Jak napsat aktivační události

Azure Cosmos DB podporuje aktivačních událostí před a po aktivační události. Předběžné aktivační události jsou spouštěny před úpravou položky databáze a po aktivační události provádějí po změně položky databáze.

### <a id="pre-triggers"></a>Předběžné aktivační události

Následující příklad ukazuje, jak předběžné trigger slouží k ověření vlastnosti položky služby Azure Cosmos DB, která se vytváří. V tomto příkladu jsme využívají vzorová z [rozhraní SQL API služby .NET rychlý Start](create-sql-api-dotnet.md), přidejte vlastnost časového razítka nově přidané položky, pokud ho neobsahuje.

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

Předběžné aktivační události nemůže mít žádné vstupní parametry. Objekt žádosti v aktivační událost se používá k manipulaci s zprávu s požadavkem asociovanou s operací. V předchozím příkladu předběžné aktivační událost se spustí při vytvoření položky služby Azure Cosmos DB a tělo zprávy požadavku obsahuje položky, která má být vytvořena ve formátu JSON.

Když aktivační události jsou zaregistrované, můžete zadat operace, které můžete spustit s. Tato aktivační událost by měl být vytvořen s `TriggerOperation` hodnotu `TriggerOperation.Create`, což znamená, že pomocí triggeru v operaci nahrazení, jak je znázorněno v následujícím kódu se nepovoluje.

Příklady toho, jak zaregistrovat a volat před aktivační událost, naleznete v tématu [předběžné triggery](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) a [po triggery](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) článků. 

### <a id="post-triggers"></a>Po aktivační události

Následující příklad ukazuje po triggeru. Tato aktivační událost dotazy pro položku metadat a aktualizuje s podrobnostmi o nově vytvořenou položku.


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

Jednou z věcí, je důležité si uvědomit je transakční spuštění aktivačních událostí ve službě Azure Cosmos DB. Tento po trigger se spustí v rámci stejné transakce, který používá vytvořit položku služby Azure Cosmos DB. Proto pokud dojde k výjimce během zpracování po triggeru, například pokud nemůžete aktualizovat položku metadat, celá transakce nezdaří a je vrácena zpět. Proto je vytvořena položka služby Azure Cosmos DB a vrátí se výjimka.

Příklady toho, jak zaregistrovat a volat před aktivační událost, naleznete v tématu [předběžné triggery](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) a [po triggery](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) článků. 

## <a id="udfs"></a>Jak napsat uživatelsky definovaných funkcí

Následující příklad vytvoří UDF k výpočtu daně z příjmu pro různé příjem hranaté závorky. Tato funkce definované uživatelem se pak použije uvnitř dotazu. Pro účely tohoto příkladu předpokládají se kontejner volá "Příjmů" s vlastnostmi následujícím způsobem:

```json
{
   name = "Satya Nadella",
   country = "USA",
   income = 70000
}
```

Toto je definice funkce k výpočtu daně z příjmu pro různé hranaté závorky příjmu:

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

Příklady toho, jak registrace a použití uživatelem definovanou funkci najdete v tématu [použití uživatelem definovaných funkcí ve službě Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs) článku.

## <a name="next-steps"></a>Další postup

Přečtěte si další koncepty a postupy: zápis nebo pomocí uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB:

* [Postup registrace a použití uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Jak napsat uložených procedur a aktivačních událostí ve službě Azure Cosmos DB pomocí rozhraní API pro dotazy jazyka Javascript](how-to-write-javascript-query-api.md)

* [Práce s Azure Cosmos DB uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB](stored-procedures-triggers-udfs.md)

* [Práce s jazykem JavaScript integrovaná rozhraní API pro dotazy ve službě Azure Cosmos DB](javascript-query-api.md)
