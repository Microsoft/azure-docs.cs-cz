---
title: Zápis uložených procedur, aktivačních událostí a ufl v Azure Cosmos DB
description: Zjistěte, jak definovat uložené procedury, aktivační události a uživatelem definované funkce v Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 4dee017323bda5fc08598a9b24cadd11516807cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441738"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Jak psát uložené procedury, aktivační události a uživatelem definované funkce v Azure Cosmos DB

Azure Cosmos DB poskytuje jazykově integrované transakční provádění JavaScriptu, které umožňuje psát **uložené procedury**, **aktivační události**a **uživatelem definované funkce (UD).** Při použití rozhraní SQL API v Azure Cosmos DB můžete definovat uložené procedury, aktivační události a UDV v jazyce JavaScript. Logiku můžete napsat do Jazyka JavaScript a spustit ji uvnitř databázového stroje. Aktivační události, uložené procedury a ufls můžete vytvářet a spouštět pomocí [portálu Azure Portal](https://portal.azure.com/), [rozhraní API integrovaného dotazu v jazyce JavaScript v azure cosmos DB](javascript-query-api.md) a [sadách SDK klienta Cosmos DB SQL API](sql-api-dotnet-samples.md). 

Chcete-li volat uloženou proceduru, aktivační událost a uživatelem definovanou funkci, musíte ji zaregistrovat. Další informace najdete v [tématu Jak pracovat s uložené procedury, aktivační události, uživatelem definované funkce v Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md).

> [!NOTE]
> Pro rozdělené kontejnery při provádění uložené procedury musí být v možnostech požadavku uvedena hodnota klíče oddílu. Uložené procedury jsou vždy vymezeny na klíč oddílu. Položky, které mají jinou hodnotu klíče oddílu nebude viditelná pro uloženou proceduru. To platí i pro aktivační události stejně.

> [!Tip]
> Cosmos podporuje nasazení kontejnerů s uloženými procedurami, aktivačními událostmi a uživatelem definovanými funkcemi. Další informace najdete [v tématu Vytvoření kontejneru Azure Cosmos DB s funkcemi na straně serveru.](manage-sql-with-resource-manager.md#create-sproc)

## <a name="how-to-write-stored-procedures"></a><a id="stored-procedures"></a>Jak psát uložené procedury

Uložené procedury se zapisují pomocí JavaScriptu, můžou vytvářet, aktualizovat, číst, dotazovat a odstraňovat položky uvnitř kontejneru Azure Cosmos. Uložené procedury jsou registrovány na kolekci a mohou pracovat s libovolným dokumentem nebo přílohou, která je v této kolekci přítomna.

**Příklad**

Zde je jednoduchý uložený postup, který vrací odpověď "Hello World".

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

Objekt kontextu poskytuje přístup ke všem operacím, které lze provést v Azure Cosmos DB, stejně jako přístup k objektům požadavku a odpovědi. V takovém případě použijete objekt odpovědi k nastavení těla odpovědi, která má být odeslána zpět klientovi.

Po zapsání musí být uložená procedura registrována v kolekci. Další informace najdete v tématu Jak používat uložené procedury v článku [Azure Cosmos DB.](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

### <a name="create-an-item-using-stored-procedure"></a><a id="create-an-item"></a>Vytvoření položky pomocí uložené procedury

Při vytvoření položky pomocí uložené procedury je položka vložena do kontejneru Azure Cosmos a id pro nově vytvořenou položku je vrácena. Vytvoření položky je asynchronní operace a závisí na funkcích zpětného volání Jazyka JavaScript. Funkce zpětného volání má dva parametry - jeden pro objekt chyby v případě selhání operace a druhý pro vrácenou hodnotu; v tomto případě vytvořený objekt. Uvnitř zpětného volání můžete buď zpracovat výjimku nebo vyvolat chybu. V případě, že zpětné volání není k dispozici a dojde k chybě, runtime Azure Cosmos DB vyvolá chybu. 

Uložená procedura také obsahuje parametr pro nastavení popisu, je to logická hodnota. Pokud je parametr nastaven na hodnotu true a popis chybí, uložená procedura vyvolá výjimku. V opačném případě bude nadále spuštěn a bude spuštěn a bude spuštěn a bude spuštěn.

Následující příklad uložené procedury přebírá novou položku Azure Cosmos jako vstup, vloží ji do kontejneru Azure Cosmos a vrátí ID pro nově vytvořenou položku. V tomto příkladu využíváme ukázku Seznamu ToDoList z [rozhraní API JAZYKA SQL .NET .Start.](create-sql-api-dotnet.md)

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

Při definování uložené procedury na webu Azure Portal jsou vstupní parametry vždy odeslány jako řetězec do uložené procedury. I v případě, že předáte pole řetězců jako vstup, pole je převeden na řetězec a odeslána do uložené procedury. Chcete-li tento problém vyřešit, můžete definovat funkci v rámci uložené procedury analyzovat řetězec jako pole. Následující kód ukazuje, jak analyzovat vstupní parametr řetězce jako pole:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a name="transactions-within-stored-procedures"></a><a id="transactions"></a>Transakce v rámci uložených procedur

Můžete implementovat transakce na položky v rámci kontejneru pomocí uložené procedury. Následující příklad používá transakce v rámci fantasy fotbalové herní aplikace k obchodování hráčů mezi dvěma týmy v jedné operaci. Uložená procedura se pokusí přečíst dvě položky Azure Cosmos, z nichž každá odpovídá ID přehrávače předaným jako argument. Pokud jsou nalezeni oba hráči, pak uložená procedura aktualizuje položky výměnou jejich týmů. Pokud jsou na cestě zjištěny nějaké chyby, uložená procedura vyvolá výjimku javascriptu, která implicitně přeruší transakci.

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

### <a name="bounded-execution-within-stored-procedures"></a><a id="bounded-execution"></a>Ohraničené spuštění v rámci uložených procedur

Následuje příklad uložené procedury, která hromadně importuje položky do kontejneru Azure Cosmos. Uložená procedura zpracovává ohraničené spuštění kontrolou logické `createDocument`vrácené hodnoty z programu a potom použije počet položek vložených do každého vyvolání uložené procedury ke sledování a obnovení průběhu v příčce.

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

## <a name="how-to-write-triggers"></a><a id="triggers"></a>Jak psát aktivační události

Azure Cosmos DB podporuje předběžné aktivační události a následné aktivační události. Předaktivační události jsou spouštěny před úpravou položky databáze a následné aktivační události jsou provedeny po úpravě položky databáze.

### <a name="pre-triggers"></a><a id="pre-triggers"></a>Triggery před akcí

Následující příklad ukazuje, jak se předaktivační událost používá k ověření vlastností položky Azure Cosmos, která se vytváří. V tomto příkladu využíváme ukázku ToDoList z [rozhraní API Rychléspuštění .NET SQL](create-sql-api-dotnet.md), abychom přidali vlastnost časového razítka do nově přidané položky, pokud ji neobsahuje.

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

Triggery před akcí nesmí mít žádné vstupní parametry. Objekt požadavku v aktivační události se používá k manipulaci se zprávou požadavku přidruženou k operaci. V předchozím příkladu pre-trigger se spustí při vytváření položky Azure Cosmos a text zprávy požadavku obsahuje položku, která má být vytvořena ve formátu JSON.

Když jsou registrovány aktivační události, můžete určit operace, které lze spustit s. Tato aktivační událost by `TriggerOperation` měla `TriggerOperation.Create`být vytvořena s hodnotou , což znamená, že použití aktivační události v operaci nahrazení, jak je znázorněno v následujícím kódu, není povoleno.

Příklady, jak zaregistrovat a volat pre-trigger, naleznete [v tématu pre-triggers](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) a [post-triggers](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) články. 

### <a name="post-triggers"></a><a id="post-triggers"></a>Triggery po akci

Následující příklad ukazuje post-trigger. Tato aktivační událost se dotazuje na položku metadat a aktualizuje ji podrobnostmi o nově vytvořené položce.


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

Jedna věc, kterou je důležité si uvědomit, je transakční spuštění aktivačních událostí v Azure Cosmos DB. Post-trigger spustí jako součást stejné transakce pro podkladové položky samotné. Výjimka během spuštění po aktivační události se nezdaří celou transakci. Vše potvrzené bude vrácena zpět a vrácena výjimka.

Příklady, jak zaregistrovat a volat pre-trigger, naleznete [v tématu pre-triggers](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) a [post-triggers](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) články. 

## <a name="how-to-write-user-defined-functions"></a><a id="udfs"></a>Jak psát uživatelem definované funkce

Následující ukázka vytvoří UDF pro výpočet daně z příjmu pro různé příjmové třídy. Tato uživatelem definovaná funkce by pak byla použita uvnitř dotazu. Pro účely tohoto příkladu předpokládejme, že je kontejner s názvem "Příjmy" s vlastnostmi takto:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

Následuje definice funkce pro výpočet daně z příjmu pro různé příjmové třídy:

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

Příklady registrace a použití uživatelem definované funkce najdete v tématu Jak používat uživatelem definované funkce v článku [Azure Cosmos DB.](how-to-use-stored-procedures-triggers-udfs.md#udfs)

## <a name="logging"></a>protokolování 

Při použití uložené procedury, aktivačních událostí nebo uživatelem `console.log()` definovaných funkcí můžete protokolovat kroky pomocí příkazu. Tento příkaz bude soustředit řetězec pro `EnableScriptLogging` ladění, pokud je nastavena na hodnotu true, jak je znázorněno v následujícím příkladu:

```javascript
var response = await client.ExecuteStoredProcedureAsync(
document.SelfLink,
new RequestOptions { EnableScriptLogging = true } );
Console.WriteLine(response.ScriptLog);
```

## <a name="next-steps"></a>Další kroky

Další koncepty a postupy pro zápis nebo použití uložených procedur, aktivačních událostí a uživatelem definovaných funkcí v Azure Cosmos DB:

* [Postup registrace a používání uložených procedur, triggerů a funkcí definovaných uživatelem ve službě Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Jak psát uložené procedury a aktivační události pomocí rozhraní Javascript Query API v Azure Cosmos DB](how-to-write-javascript-query-api.md)

* [Práce s uloženými procedurami, aktivačními událostmi a uživateli definovanými funkcemi Azure Cosmos DB](stored-procedures-triggers-udfs.md)

* [Práce s rozhraním API pro integrované dotazy jazyka JavaScript v Azure Cosmos DB](javascript-query-api.md)
