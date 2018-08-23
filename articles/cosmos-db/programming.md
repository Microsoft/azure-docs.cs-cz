---
title: Programování v jazyce JavaScript na straně serveru pro službu Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o použití služby Azure Cosmos DB k zápisu uložené procedury, aktivační události databáze a uživatelem definované funkce (UDF) v jazyce JavaScript. Získejte tipy k programování na databázi a mnoho dalšího.
keywords: Databáze aktivačních událostí, uložené procedury, uložené procedury, databázi, sproc, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: andrl
ms.openlocfilehash: 6296eb423f24762ed32a21ef40852dc1a9dd8f36
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42056845"
---
# <a name="azure-cosmos-db-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Programování na straně serveru služby Azure Cosmos DB: uložené procedury, aktivační události databáze a uživatelem definovanými funkcemi

Zjistěte, jak služby Azure Cosmos DB integrovaný jazyk, transakční provádění jazyka JavaScript a umožňuje vývojářům psát **uložených procedur komponentami TableAdapter**, **triggery**, a **uživatelem definované funkce (UDF)**  nativně v [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/) jazyka JavaScript. Integrace JavaScriptu umožňuje psát logiku programu, který může být dodána a spustit přímo v databázi oddílů pro úložiště. 

Doporučujeme začít následujícím videem, kde Andrew Liu poskytuje úvod do služby Azure Cosmos DB na straně serveru databáze programovací model. 

> [!VIDEO https://www.youtube.com/embed/s0cXdHNlVI0]
>
> 

Pak se vraťte k tomuto článku, kde se dozvíte odpovědi na následující otázky:  

* Jak se píše uložené procedury, aktivační události nebo pomocí jazyka JavaScript UDF?
* Jak služby Cosmos DB zajistit kyseliny?
* Jak fungují transakce v databázi Cosmos DB?
* Co jsou předem aktivuje a následného aktivuje a jak se píše jeden?
* Jak registrace a spouštění uložené procedury, aktivační události nebo systému souborů UDF v podobě RESTful pomocí protokolu HTTP?
* Co Cosmos DB SDK jsou k dispozici pro vytvoření a provedení uložené procedury, triggery a uživatelem definovanými funkcemi?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Úvod do programování UDF a uložené procedury
Tento přístup z *"JavaScript zde slouží jako moderní den T-SQL"* uvolní vývojářům aplikací z složitosti systému neshody typů a objektově relační mapování technologie. Obsahuje také řadu vnitřní výhody, které můžete využít k vytváření aplikací s bohatým obsahem:  

* **Procesní logiky:** JavaScript zde slouží jako programovací jazyk vysoké úrovně, poskytuje bohaté a známé rozhraní express obchodní logiku. Můžete provádět komplexní pořadí operací blíž k datům.
* **Atomické transakce:** Cosmos DB záruky, které databáze operací do jedné uložené procedury nebo aktivační události jsou atomické. Tato funkce atomic umožňuje kombinovat souvisejících operací v jedné dávce tak, aby všechny z nich úspěch nebo žádná z nich úspěšné aplikace. 
* **Výkon:** skutečnost, že JSON vnitřně mapován na systém typů jazyka Javascript a je také základní jednotky úložiště ve službě Cosmos DB umožňuje počet optimalizaci, jako je opožděné materializace dokumentů JSON ve fondu vyrovnávací paměti a provedení je k dispozici na vyžádání prováděním kódu. Existují další výhody výkonu související s přenosů obchodní logiky k databázi:
  
  * Dávkování – vývojáři mohli skupiny operací, jako je vložení a odešlete je hromadně. Výrazně se snižují latenci provoz sítě, náklady a režii úložiště k vytvoření samostatných transakcí. 
  * Předkompilace – Cosmos DB předkompiluje uložené procedury, triggery a uživatelem definované funkce (UDF), aby náklady kompilace jazyka JavaScript pro každé vyvolání. Minimální hodnota je amortizovaných režijní náklady na vytváření bajtový kód pro procedury logiku.
  * Sekvencování – mnoho operací potřeba vedlejší efekt ("aktivační události"), která potenciálně vyžaduje jeden nebo více operací sekundárního úložiště. Kromě atomicitu Toto je výkonnější při přesunu na server. 
* **Zapouzdření:** uložené procedury lze použít k seskupení obchodní logiky na jednom místě, která má dvě výhody:
  * Přidá abstraktní vrstvu nad nezpracovaná data, která umožňuje datoví architekti neustálý vývoj svých aplikací nezávisle na data. Tato vrstva abstrakce je výhodné, když jsou data bez schématu, protože křehká předpokladů, které možná bude nutné vloženými do aplikace, pokud mají vypořádat s daty přímo.  
  * Tato abstrakce vám umožní zabezpečit svoje data tak, že usnadňuje přístup ze skriptů velké podniky.  

Vytvoření a spuštění aktivační procedury databáze, uložených procedur a vlastní operátory dotazů je podporované prostřednictvím [webu Azure portal](https://portal.azure.com), [rozhraní REST API](/rest/api/cosmos-db/), [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases), a [klientské sady SDK](sql-api-sdk-dotnet.md) na spoustě platforem, včetně .NET, Node.js a JavaScript.

Tento kurz používá [sady Node.js SDK s Q příslibů](http://azure.github.io/azure-documentdb-node-q/) pro ilustraci syntaxi a použití uložené procedury, triggery a uživatelem definovanými funkcemi.   

## <a name="stored-procedures"></a>Uložené procedury
### <a name="example-write-a-stored-procedure"></a>Příklad: Zápis uložené procedury
Začněme jednoduchou uloženou proceduru, která vrací odpověď "Hello World".

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

Uložené procedury jsou registrované na kolekci a mohou pracovat v libovolné dokumentu a přílohy, které jsou k dispozici v dané kolekci. Následující fragment kódu ukazuje, jak zaregistrovat helloWorld uložené procedury s kolekcí. 


```javascript
// register the stored procedure
var createdStoredProcedure;
client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
    .then(function (response) {
        createdStoredProcedure = response.resource;
        console.log("Successfully created stored procedure");
    }, function (error) {
        console.log("Error", error);
    });
```


Po uložené procedury je zaregistrovaný, můžete ho spustit proti kolekci a přečíst výsledky zpět na klienta. 

```javascript
// execute the stored procedure
client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
    .then(function (response) {
        console.log(response.result); // "Hello, World"
    }, function (err) {
        console.log("Error", error);
    });
```

Objekt kontextu poskytuje přístup ke všem operacím, které lze provést u úložiště Cosmos DB, a také přístup k objektům žádostí a odpovědí. V tomto případě použijete objekt odpovědi nastavit text odpovědi, který byl odeslán zpět klientovi. Další informace najdete v tématu [server Azure Cosmos DB JavaScript dokumentace k sadě SDK](https://azure.github.io/azure-cosmosdb-js-server/).  

Dejte nám rozbalte v tomto příkladu a přidávají další funkce související s databáze uložené procedury. Uložené procedury lze vytvořit, aktualizovat, čtení, dotazování a odstranění dokumentů a příloh v kolekci.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Příklad: Zápis uloženou proceduru pro vytvoření dokumentu
Další fragment kódu ukazuje, jak používat objekt context k interakci s prostředky služby Cosmos DB.

```javascript
var createDocumentStoredProc = {
    id: "createMyDocument",
    serverScript: function createMyDocument(documentToCreate) {
        var context = getContext();
        var collection = context.getCollection();

        var accepted = collection.createDocument(collection.getSelfLink(),
              documentToCreate,
              function (err, documentCreated) {
                  if (err) throw new Error('Error' + err.message);
                  context.getResponse().setBody(documentCreated.id)
              });
        if (!accepted) return;
    }
}
```


Tuto uloženou proceduru přijímá jako vstupní documentToCreate textu dokumentu, který má být vytvořen v aktuální kolekci. Všechny tyto operace jsou asynchronní a závisí na zpětná volání funkce jazyka JavaScript. Funkce zpětného volání má dva parametry, jeden pro objekt error, v takovém případě se operace nezdaří a jeden pro vytvořený objekt. Uvnitř zpětného volání uživatelé můžete zpracovat výjimku nebo vyvolat chybu. V případě není k dispozici zpětné volání a dojde k chybě, modul runtime služby Azure Cosmos DB vyvolá chybu.   

V předchozím příkladu zpětného volání vrátí chybu, pokud se operace nezdařila. Jinak nastaví ID vytvořený dokument jako text odpovědi klientovi. Zde je, jak je tuto uloženou proceduru spustit se vstupními parametry.

```javascript
// register the stored procedure
client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
    .then(function (response) {
        var createdStoredProcedure = response.resource;

        // run stored procedure to create a document
        var docToCreate = {
            id: "DocFromSproc",
            book: "The Hitchhiker’s Guide to the Galaxy",
            author: "Douglas Adams"
        };

        return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
              docToCreate);
    }, function (error) {
        console.log("Error", error);
    })
.then(function (response) {
    console.log(response); // "DocFromSproc"
}, function (error) {
    console.log("Error", error);
});
```

Tuto uloženou proceduru lze upravit tak, aby pole těla dokumentu jako vstup přijmout a vytvořit je všechny na stejném spuštění uložené procedury namísto více požadavků pro každý z nich vytvářet jednotlivě. Tuto uloženou proceduru lze použít k implementaci efektivní hromadné programu pro import pro Cosmos DB (popsáno dále v tomto kurzu).   

Příkladu popisovaného ukázal, jak lze pomocí uložených procedur. Dále se dozvíte o aktivační události a uživatelsky definovaných funkcí (UDF) později v tomto kurzu.

### <a name="known-issues"></a>Známé problémy

Při definování uložené procedury pomocí webu Azure portal, vstupní parametry jsou vždy odesílat jako řetězec uloženou proceduru. I v případě, předejte pole řetězců jako vstupní pole je převést na řetězec a odesílat na uložené procedury. Chcete-li vyřešit tento problém, můžete definovat funkce v rámci uložené procedury k analýze řetězce jako pole. Následující kód je příklad k analýze řetězce jako pole: 

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);
    
    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

## <a name="database-program-transactions"></a>Transakce databáze programu
Transakce v databázi typické lze definovat jako posloupnost operací provedených jako jednu logickou jednotku práce. Obsahuje každou transakci **záruky "ACID"**. KYSELINY je známá zkratka, který zastupuje čtyři vlastnosti - atomicitu, konzistence, izolace a odolnost.  

Stručně řečeno, atomicitu zaručuje, že veškerou práci prováděnou v transakci se zpracovává jako jeden celek kde buď všechno je potvrzené nebo žádný. Konzistence zajišťuje, že data jsou vždy ve funkčním stavu interní napříč transakce. Izolace zaručuje, že žádné dvě transakce konfliktu mezi sebou – obecně, většina komerčních systémů poskytují více úrovní izolace, se dají podle potřeby aplikace. Odolnost zajišťuje, že každá změna, která je potvrzena v databázi budou vždy k dispozici.   

Ve službě Cosmos DB JavaScript hostována ve stejném paměťovém prostoru jako databáze. Proto požadavky provedené v rámci uložené procedury a triggery spuštění ve stejném oboru relace databázi. Tato funkce umožňuje zajistit kyseliny pro všechny operace, které jsou součástí jedné uložené procedury/trigger Cosmos DB. Vezměte v úvahu následující uložené procedury definice:

```javascript
// JavaScript source code
var exchangeItemsSproc = {
    id: "exchangeItems",
    serverScript: function (playerId1, playerId2) {
        var context = getContext();
        var collection = context.getCollection();
        var response = context.getResponse();

        var player1Document, player2Document;

        // query for players
        var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
            function (err, documents, responseOptions) {
                if (err) throw new Error("Error" + err.message);

                if (documents.length != 1) throw "Unable to find both names";
                player1Document = documents[0];

                var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                    function (err2, documents2, responseOptions2) {
                        if (err2) throw new Error("Error" + err2.message);
                        if (documents2.length != 1) throw "Unable to find both names";
                        player2Document = documents2[0];
                        swapItems(player1Document, player2Document);
                        return;
                    });
                if (!accept2) throw "Unable to read player details, abort ";
            });

        if (!accept) throw "Unable to read player details, abort ";

        // swap the two players’ items
        function swapItems(player1, player2) {
            var player1ItemSave = player1.item;
            player1.item = player2.item;
            player2.item = player1ItemSave;

            var accept = collection.replaceDocument(player1._self, player1,
                function (err, docReplaced) {
                    if (err) throw "Unable to update player 1, abort ";

                    var accept2 = collection.replaceDocument(player2._self, player2,
                        function (err2, docReplaced2) {
                            if (err) throw "Unable to update player 2, abort"
                        });

                    if (!accept2) throw "Unable to update player 2, abort";
                });

            if (!accept) throw "Unable to update player 1, abort";
        }
    }
}

// register the stored procedure in Node.js client
client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
    .then(function (response) {
        var createdStoredProcedure = response.resource;
    }
);
```

Tuto uloženou proceduru používá transakce v rámci herní aplikace obchodu položek mezi dva hráči v rámci jedné operace. Uložená procedura se pokusí přečíst dva dokumenty, které každý odpovídající ID player předanou jako argument. Pokud se najdou oba player dokumenty, uložené procedury aktualizuje dokumenty pomocí výměny jejich položky. Pokud nedojde k chybám na cestě, vyvolá výjimky jazyka JavaScript, která implicitně zruší transakce.

Pokud je kolekce uloženou proceduru zaregistrován proti je kolekce s jedním oddílem a potom transakce je vymezen na všechny dokumenty v kolekci. Pokud je kolekce rozdělená na oddíly, uložené procedury jsou provedeny v oboru transakce z jednoho klíče oddílu. Každý uložené procedury spuštění pak musí obsahovat hodnotu klíče oddílu, odpovídající oboru transakce musí být spuštěna pod. Další informace najdete v tématu [Azure Cosmos DB dělení](partition-data.md).

### <a name="commit-and-rollback"></a>Potvrdit změny a vrácení zpět
Transakce jsou hluboce a nativně integrováno programovací model Cosmos DB jazyka JavaScript. Uvnitř funkce JavaScriptu všechny operace jsou zabaleny automaticky v rámci jedné transakce. Pokud JavaScript se dokončí bez jakékoli výjimce, jsou potvrzeny operací do databáze. V důsledku toho příkazy "BEGIN TRANSACTION" a "Potvrzení transakce" v relačních databázích jsou implicitní ve službě Cosmos DB.  

Pokud je jakoukoliv výjimku, která se šíří ze skriptu, modulu runtime jazyka JavaScript služby Cosmos DB se vrátit zpět celá transakce. Jak je znázorněno v předchozím příkladu, došlo k výjimce odpovídá efektivně "Transakce vrácení zpět" ve službě Cosmos DB.

### <a name="data-consistency"></a>Konzistence dat
Uložené procedury a triggery jsou provedeny vždy na primární replice kontejneru Azure Cosmos DB. Tím se zajistí, že čtení záznamů z uvnitř ukládat silná konzistence nabídky postupy. Dotazy s využitím uživatelsky definovaných funkcí je možné provést v primární nebo sekundární repliku, ale zajistíte splnění úrovně konzistence požadovaný výběrem příslušné repliky.

## <a name="bounded-execution"></a>Ohraničené spuštění
Všechny operace služby Cosmos DB musí dokončit během zadaný server doba trvání časového limitu žádosti. Toto omezení platí i pro funkce jazyka JavaScript (uložené procedury, triggery a uživatelem definovaných funkcí). Pokud se tento časový limit operace dokončena, transakce je vrácena zpět. Funkce jazyka JavaScript musí dokončit během časového limitu nebo implementovat jako model založený na pokračování pro spuštění dávky a obnovování.  

Pro zjednodušení vývoje uložených procedur a aktivačních událostí ke zpracování časová omezení, všechny funkce v rámci objektu kolekce (vytvoření, čtení, nahraďte a odstranění dokumentů a příloh) návratová logická hodnota, které představuje Určuje, zda operace dokončí. Pokud je tato hodnota false, je jako ukazatel toho, že je časový limit vyprší a postup musí zalomit spuštění.  Operace zařazených do fronty před první operace nepřijatelného úložiště je zaručeno vyplnit, pokud uložená procedura dokončí včas a žádné další požadavky na frontu.  

Funkce jazyka JavaScript jsou taky omezená na spotřebu prostředků. Cosmos DB vyhrazuje propustnost na kolekci nebo pro skupinu kontejnerů. Propustnost se vyjadřuje jako normalizované jednotka procesoru, paměti a vstupně-výstupních operací využití jednotek žádosti nebo RU. Funkce jazyka JavaScript můžete využít potenciálně velkého počtu rezervovaných jednotek během krátké doby a může se zobrazit kurz časově omezené Pokud dosáhnete limitu kolekce. Uložené procedury náročné může také umístí do karantény a zajistit tak dostupnost primitivní databázových operací.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Příklad: Hromadný import dat do databáze programu
Níže je příklad uloženou proceduru, která jsou zapsána do hromadného importu dokumenty do kolekce. Všimněte si, jak uložená procedura zpracovává ohraničené provádění kontrolou datový typ Boolean návratová hodnota z createDocument a potom využívá ke sledování a pokračovat v průběhu napříč dávkami počet dokumentů vložen do každého volání uložené procedury.

```javascript
function bulkImport(docs) {
    var collection = getContext().getCollection();
    var collectionLink = collection.getSelfLink();

    // The count of imported docs, also used as current doc index.
    var count = 0;

    // Validate input.
    if (!docs) throw new Error("The array is undefined or null.");

    var docsLength = docs.length;
    if (docsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create a document.
    tryCreate(docs[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted. 
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called docs.length times.
    //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(doc, callback) {
        var isAccepted = collection.createDocument(collectionLink, doc, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client, 
        // which will call the script again with remaining set of docs.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when collection.createDocument is done in order to process the result.
    function callback(err, doc, options) {
        if (err) throw err;

        // One more document has been inserted, increment the count.
        count++;

        if (count >= docsLength) {
            // If we created all documents, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(docs[count], callback);
        }
    }
}
```

## <a id="trigger"></a> Aktivační procedury databáze
### <a name="database-pre-triggers"></a>Předběžné aktivační procedury databáze
Cosmos DB poskytuje triggery, které jsou provedeny nebo aktivované operací v dokumentu. Můžete například zadat předběžné aktivační událost při vytváření dokumentu – této předběžné trigger se spustí před vytvořením dokumentu. Následující příklad ukazuje, jak lze pomocí předběžné aktivační události ověření vlastnosti dokumentu, který se vytváří:

```javascript
var validateDocumentContentsTrigger = {
    id: "validateDocumentContents",
    serverScript: function validate() {
        var context = getContext();
        var request = context.getRequest();

        // document to be created in the current operation
        var documentToCreate = request.getBody();

        // validate properties
        if (!("timestamp" in documentToCreate)) {
            var ts = new Date();
            documentToCreate["my timestamp"] = ts.getTime();
        }

        // update the document that will be created
        request.setBody(documentToCreate);
    },
    triggerType: TriggerType.Pre,
    triggerOperation: TriggerOperation.Create
}
```

A odpovídající kód Node.js na straně klienta registrace pro aktivační událost:

```javascript
// register pre-trigger
client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
    .then(function (response) {
        console.log("Created", response.resource);
        var docToCreate = {
            id: "DocWithTrigger",
            event: "Error",
            source: "Network outage"
        };

        // run trigger while creating above document 
        var options = { preTriggerInclude: "validateDocumentContents" };

        return client.createDocumentAsync(collection.self,
              docToCreate, options);
    }, function (error) {
        console.log("Error", error);
    })
.then(function (response) {
    console.log(response.resource); // document with timestamp property added
}, function (error) {
    console.log("Error", error);
});
```

Předběžné aktivační události nemůže mít žádné vstupní parametry. Objekt žádosti slouží k manipulaci s zprávu s požadavkem asociovanou s operací. Tady, před aktivační události je spuštěn při vytváření dokumentu a tělo zprávy požadavku obsahuje dokumentu, který má být vytvořena ve formátu JSON.   

Když aktivační události jsou registrovány, uživatelé mohou zadat operace, které můžete spustit s. Tato aktivační událost pochází TriggerOperation.Create, což znamená, že pomocí triggeru v operaci nahrazení, jak je znázorněno v následujícím kódu se nepovoluje.

```javascript
var options = { preTriggerInclude: "validateDocumentContents" };

client.replaceDocumentAsync(docToReplace.self,
              newDocBody, options)
.then(function (response) {
    console.log(response.resource);
}, function (error) {
    console.log("Error", error);
});

// Fails, can’t use a create trigger in a replace operation
```
### <a name="database-post-triggers"></a>Po aktivační procedury databáze
Po aktivačních událostí, jako jsou předběžné aktivačních událostí, jsou spojeny s operací na dokument a nespoléhejte žádné vstupní parametry. Mohou být spuštěny **po** operace dokončí a mít přístup k zprávy s odpovědí, která je odeslána do klienta.   

Následující příklad ukazuje po aktivační události v akci:
```javascript
var updateMetadataTrigger = {
    id: "updateMetadata",
    serverScript: function updateMetadata() {
        var context = getContext();
        var collection = context.getCollection();
        var response = context.getResponse();

        // document that was created
        var createdDocument = response.getBody();

        // query for metadata document
        var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
            updateMetadataCallback);
        if(!accept) throw "Unable to update metadata, abort";

        function updateMetadataCallback(err, documents, responseOptions) {
            if(err) throw new Error("Error" + err.message);
                     if(documents.length != 1) throw 'Unable to find metadata document';

                     var metadataDocument = documents[0];

                     // update metadata
                     metadataDocument.createdDocuments += 1;
                     metadataDocument.createdNames += " " + createdDocument.id;
                     var accept = collection.replaceDocument(metadataDocument._self,
                           metadataDocument, function(err, docReplaced) {
                                  if(err) throw "Unable to update metadata, abort";
                           });
                     if(!accept) throw "Unable to update metadata, abort";
                     return;                    
        }                                                                                            
    },
    triggerType: TriggerType.Post,
    triggerOperation: TriggerOperation.All
}

```
Aktivační událost lze zaregistrovat, jak je znázorněno v následujícím příkladu.
```javascript
// register post-trigger
client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
    .then(function(createdTrigger) { 
        var docToCreate = { 
            name: "artist_profile_1023",
            artist: "The Band",
            albums: ["Hellujah", "Rotators", "Spinning Top"]
        };

        // run trigger while creating above document 
        var options = { postTriggerInclude: "updateMetadata" };

        return client.createDocumentAsync(collection.self,
              docToCreate, options);
    }, function(error) {
        console.log("Error" , error);
    })
.then(function(response) {
    console.log(response.resource); 
}, function(error) {
    console.log("Error" , error);
});
```

Tato aktivační událost dotazuje na dokument metadat a aktualizuje s podrobnostmi o nově vytvořený dokument.  

Je jednou z věcí, je důležité si uvědomit, **transakční** spuštění aktivačních událostí ve službě Cosmos DB. Tento po trigger se spustí v rámci stejné transakce jako vytvoření původního dokumentu. Proto pokud vyvoláte výjimku z po triggeru (například pokud se nemůžete aktualizovat dokument metadat), celá transakce nezdaří a vrácena zpět. Žádný dokument se vytvoří a vrátí se výjimka.  

## <a id="udf"></a>Uživatelem definované funkce
Uživatelem definované funkce (UDF) slouží k rozšíření gramatiku jazyka SQL služby Azure Cosmos DB dotazu a implementovat vlastní obchodní logiku. Jejich lze volat pouze z uvnitř dotazů. Tyto nemají přístup k objektu kontextu a jsou určené pro použití jako jen pro výpočty jazyka JavaScript. Proto uživatelem definovanými funkcemi, může běžet na sekundárních replikách služby Cosmos DB.  

Následující příklad vytvoří UDF k výpočtu daně z příjmu podle sazeb pro různé příjem hranaté závorky pro a používá ho uvnitř dotazu k vyhledání všech uživatelů v daně placené více než 20 000.

```javascript
var taxUdf = {
    id: "tax",
    serverScript: function tax(income) {

        if(income == undefined) 
            throw 'no input';

        if (income < 1000) 
            return income * 0.1;
        else if (income < 10000) 
            return income * 0.2;
        else
            return income * 0.4;
    }
}
```

UDF lze následně použít v dotazech stejně jako v následujícím příkladu:

```javascript
// register UDF
client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
    .then(function(response) { 
        console.log("Created", response.resource);

        var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
        return client.queryDocuments('dbs/testdb/colls/testColl',
               query).toArrayAsync();
    }, function(error) {
        console.log("Error" , error);
    })
.then(function(response) {
    var documents = response.feed;
    console.log(response.resource); 
}, function(error) {
    console.log("Error" , error);
});
```

## <a name="javascript-language-integrated-query-api"></a>Dotaz integrovaný jazyk JavaScript API
Kromě vydávat dotazy pomocí gramatika SQL služby Azure Cosmos DB, sady SDK na straně serveru vám umožní provést optimalizované dotazy pomocí rozhraní fluent JavaScript bez znalosti jazyka SQL. Dotaz jazyka JavaScript, který rozhraní API můžete programově vytvářet dotazy předáním predikátu funkce do chainable funkce se volá s syntaxi na předdefinované pole a Oblíbené knihovny jazyka JavaScript, jako je Lodash ECMAScript5 společnosti. Dotazy jsou analyzovány pomocí modulu runtime jazyka JavaScript, který se spustí efektivně pomocí služby Azure Cosmos DB indexy.

> [!NOTE]
> `__` (dvojité podtržítko) je alias `getContext().getCollection()`.
> <br/>
> Jinými slovy, můžete použít `__` nebo `getContext().getCollection()` pro přístup k rozhraní API jazyka JavaScript dotazu.
> 
> 

Podporované funkce patří:

<ul>
<li>
<b>chain().... hodnota ([zpětného volání] [, možnosti])</b>
<ul>
<li>
Začíná value() zřetězené volání, které musí být ukončen.
</li>
</ul>
</li>
<li>
<b>Filter (predicateFunction [, možnosti] [, zpětné volání])</b>
<ul>
<li>
Filtruje vstup pomocí funkce predikátu, který vrací hodnotu true nebo false, chcete-li filtrovat vstupních dokumentů vstup/výstup do výsledné sady. Tato funkce se chová podobně jako v klauzuli WHERE v SQL.
</li>
</ul>
</li>
<li>
<b>Mapa (transformationFunction [, možnosti] [, zpětné volání])</b>
<ul>
<li>
Projekce zadané funkce transformace, která se mapuje na objekt jazyka JavaScript nebo hodnotu každé vstupní položky se vztahuje. Tato funkce se chová podobně jako v klauzuli SELECT v SQL.
</li>
</ul>
</li>
<li>
<b>pluck ([propertyName] [, možnosti] [, zpětné volání])</b>
<ul>
<li>
Tato funkce je zkratka pro mapu, která extrahuje hodnotu jedinou vlastnost z každé vstupní položky.
</li>
</ul>
</li>
<li>
<b>Sloučit ([isShallow] [, možnosti] [, zpětné volání])</b>
<ul>
<li>
Kombinuje a sloučí pole z každé vstupní položky do jednoho pole. Tato funkce se chová podobně jako operátor SelectMany v technologii LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy ([predicate] [, možnosti] [, zpětné volání])</b>
<ul>
<li>
Vytvořit novou sadu dokumentů, že je seřadíte dokumentů ve službě stream vstupní dokument ve vzestupném pořadí pomocí daného predikátu. Tato funkce se chová podobně jako klauzuli ORDER BY v SQL.
</li>
</ul>
</li>
<li>
<b>sortbydescending – ([predicate] [, možnosti] [, zpětné volání])</b>
<ul>
<li>
Vytvořit novou sadu dokumentů, že je seřadíte dokumentů ve službě stream vstupní dokument v sestupném pořadí pomocí daného predikátu. Tato funkce se chová podobně jako klauzuli ORDER BY x DESC v SQL.
</li>
</ul>
</li>
</ul>


Pokud zahrnutý do predikátu a/nebo výběr funkcí, následující konstrukce jazyka JavaScript automaticky optimalizovat spouštět přímo na indexy Azure Cosmos DB:

* Jednoduché operátory: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Literály, včetně literálu objektu: {}
* var, vratky

Následující konstrukce jazyka JavaScript není optimalizovat pro indexy Azure Cosmos DB:

* Řízení toku (třeba pokud, pro, zatímco)
* Volání funkcí

Další informace najdete v tématu [serverové JSDocs](https://azure.github.io/azure-cosmosdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Příklad: Zápis uložené procedury pomocí dotazu jazyka JavaScript API
Následující ukázka kódu je příkladem použití rozhraní API pro dotazy jazyka JavaScript v rámci uložené procedury. Uložená procedura vloží dokumentu, poskytnuté vstupního parametru a aktualizuje metadat dokumentů pomocí `__.filter()` metodou minSize maxSize a totalSize na základě vlastnosti velikost vstupní dokument.

```javascript
/**
 * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
 */
function insertDocumentAndUpdateMetadata(doc) {
  // HTTP error codes sent to our callback funciton by DocDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
    if (err) throw err;

    // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
    if (!doc.isMetadata && doc.size > 0) {
      // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata doc was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

        // The metadata document.
        var metaDoc = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
        else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

        // Update metaDoc.maxSize.
        metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

        // Update metaDoc.totalSize.
        metaDoc.totalSize += doc.size;

        // Update/replace the metadata document in the store.
        var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
}
```

## <a name="sql-to-javascript-cheat-sheet"></a>SQL pro jazyk Javascript tahák
Následující tabulka uvádí různé dotazy SQL a odpovídající dotazy jazyka JavaScript.

Jak pomocí dotazů SQL, zdokumentujte vlastnosti klíče (například `doc.id`) jsou malá a velká písmena.

|SQL| Dotaz jazyka JavaScript API|Popis níže|
|---|---|---|
|VYBERTE *<br>Z dokumentace| __.map(Function(DOC) { <br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc;<br>});|1|
|Vyberte docs.id, docs.message jako msg, docs.actions <br>Z dokumentace|__.map(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;vrátit {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg –: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|2|
|VYBERTE *<br>Z dokumentace<br>WHERE docs.id="X998_Y998"|__.Filter(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc.id === "X998_Y998";<br>});|3|
|VYBERTE *<br>Z dokumentace<br>KDE ARRAY_CONTAINS (dokumentace. Značky, 123)|__.Filter(Function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí x.Tags & & x.Tags.indexOf(123) > -1;<br>});|4|
|Vyberte docs.id, docs.message jako zpráva<br>Z dokumentace<br>WHERE docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Filter(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc.id === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vrátit {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg –: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|5|
|VYBRAT hodnotu značky<br>Z dokumentace<br>Připojte se k značky v docs. Značky<br>Klauzule ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Filter(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vrátí dokumentu. Značky & & Array.IsArray – (doc. Značky);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Value()|6|

V následujících popisech popisují každý dotaz v tabulce výše.
1. Výsledky ve všech dokumentech (stránkované se token pro pokračování), jako je.
2. Projekty id, zprávy (s aliasem se přiřadila msg) a akce ze všech dokumentů.
3. Dotazy na dokumenty s predikát: id = "X998_Y998".
4. Dotazy na dokumenty, které mají značky a vlastnost Tags je pole, který obsahuje hodnotu 123.
5. Dotazy na dokumenty s predikátem, id = "X998_Y998" a potom projekty id a zprávy (alias pro zprávy).
6. Filtry pro dokumenty, které mají vlastnost typu pole, značky a seřadí výsledný dokumenty podle _ts vlastnosti časového razítka systému a potom projekty + sloučí pole značky.


## <a name="runtime-support"></a>Podpora modulu CLR
Azure Cosmos DB [JavaScript API na straně serveru](https://azure.github.io/azure-cosmosdb-js-server/) poskytuje podporu pro většinu hlavní fáze technické funkce jazyka JavaScript jako standardizované podle [ECMA 262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Zabezpečení
JavaScript uložené procedury a triggery jsou v izolovaném prostoru tak, aby účinky jeden skript není pronikly na druhou bez nutnosti kontaktovat transakci izolace snímku na úrovni databáze. Běhová prostředí jsou ve fondu, ale vyčistit kontextu po každém spuštění. Proto je zaručeno bezpečné z jakékoli nezamýšlenými vedlejšími účinky od sebe navzájem.

### <a name="pre-compilation"></a>Předkompilace
Uložené procedury, triggery a uživatelem definovanými funkcemi jsou implicitně předkompilované na formát kódu byte předejdete tak náklady na kompilace při každém vyvolání skriptu. Předkompilace zajišťuje volání uložené procedury je rychlé a nízkým nárokům.

## <a name="client-sdk-support"></a>Podpora sady SDK klienta
Kromě služby Azure Cosmos DB [Node.js](sql-api-sdk-node.md) rozhraní API, služby Azure Cosmos DB má [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [jazyka JavaScript ](sql-api-sdk-node.md), a [sady SDK pro Python](sql-api-sdk-python.md) pro rozhraní SQL API. Uložené procedury, triggery a uživatelem definovanými funkcemi je možné vytvořit a spustit pomocí kteréhokoli z těchto sad SDK také. Následující příklad ukazuje, jak vytvořit a spustit uloženou proceduru z klienta .NET. Všimněte si, jak jsou typy .NET předaný do uložené procedury jako dokumenty JSON a číst zpět.

```javascript
var markAntiquesSproc = new StoredProcedure
{
    Id = "ValidateDocumentAge",
    Body = @"
            function(docToCreate, antiqueYear) {
                var collection = getContext().getCollection();    
                var response = getContext().getResponse();    

                if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                    docToCreate.antique = true;
                }

                collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                    function(err, docCreated, options) { 
                        if(err) throw new Error('Error while creating document: ' + err.message);                              
                        if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                        response.setBody(docCreated);
                });
         }"
};

// register stored procedure
StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
dynamic document = new Document() { Id = "Borges_112" };
document.Title = "Aleph";
document.Year = 1949;

// execute stored procedure
Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "ValidateDocumentAge"), document, 1920);
```

Tento příklad ukazuje způsob použití [SQL .NET API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) vytvoření předběžné aktivační události a vytvořit dokument s triggerem povolena. 

```javascript
Trigger preTrigger = new Trigger()
{
    Id = "CapitalizeName",
    Body = @"function() {
        var item = getContext().getRequest().getBody();
        item.id = item.id.toUpperCase();
        getContext().getRequest().setBody(item);
    }",
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};

Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
    new RequestOptions
    {
        PreTriggerInclude = new List<string> { "CapitalizeName" },
    });
```

A následující příklad ukazuje, jak vytvořit uživatelem definované funkce (UDF) a použít jej v [dotaz SQL](sql-api-sql-query.md).

```javascript
UserDefinedFunction function = new UserDefinedFunction()
{
    Id = "LOWER",
    Body = @"function(input) 
    {
        return input.toLowerCase();
    }"
};

foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
    "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
{
    Console.WriteLine("Read {0} from query", book);
}
```

## <a name="rest-api"></a>REST API
Všechny operace služby Azure Cosmos DB můžete provést RESTful způsobem. Uložené procedury, triggery a uživatelem definovaných funkcí lze zaregistrovat v rámci kolekce pomocí HTTP POST. Následující příklad ukazuje, jak zaregistrovat uložené procedury:

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT


    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


Uložená procedura je registrovaný pomocí provádí požadavek POST na identifikátor URI databází/testdb/colls/testColl/sprocs spolu s textem obsahující uloženou proceduru pro vytvoření. Triggery a uživatelem definovanými funkcemi lze registrovat podobně vydáním příspěvek proti/aktivační události a /udfs v uvedeném pořadí.
Tato uložená procedura může pak provést pomocí požadavku POST pro odkaz na prostředek:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Tady je předán vstup pro uloženou proceduru v textu požadavku. Vstup je předán jako pole JSON vstupních parametrů. Uložené procedury přijímá prvního vstupu jako dokument, který je text odpovědi. Odpověď, který jste dostali, vypadá takto:

    HTTP/1.1 200 OK

    { 
      name: 'TestDocument',
      book: 'Autumn of the Patriarch',
      id: 'V7tQANV3rAkDAAAAAAAAAA==',
      ts: 1407830727,
      self: 'dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/',
      etag: '6c006596-0000-0000-0000-53e9cac70000',
      attachments: 'attachments/',
      Price: 200
    }


Na rozdíl od uložené procedury, aktivační události nelze spustit přímo. Místo toho jsou spouštěny jako součást operace s dokumentem. Můžete zadat triggery spuštění s požadavkem pomocí hlavičky protokolu HTTP. Následující kód ukazuje požadavek na vytvoření dokumentu.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger

    {
       "name": "newDocument",
       "title": "The Wizard of Oz",
       "author": "Frank Baum",
       "pages": 92
    }


Tady se před aktivační událost pro spuštění s požadavkem je zadat v hlavičce x-ms-documentdb-pre-trigger-include. Žádné aktivační události po odpovídajícím způsobem, jsou uvedeny v hlavičce x-ms-documentdb-post-trigger-include. Před a po aktivačních událostí může být zadané pro daný požadavek.

## <a name="sample-code"></a>Ukázka kódu
Můžete najít další příklady kódu na straně serveru (včetně [hromadné odstranění](https://github.com/Azure/azure-cosmosdb-js-server/blob/master/samples/stored-procedures/bulkDelete.js), a [aktualizovat](https://github.com/Azure/azure-cosmosdb-js-server/blob/master/samples/stored-procedures/update.js)) v [úložiště GitHub](https://github.com/Azure/azure-cosmosdb-js-server/tree/master/samples).

Chcete sdílet vaše Super uložené procedury? přispívat do úložiště a vytvořte žádost o přijetí změn. 

## <a name="next-steps"></a>Další postup
Až budete mít jeden nebo více uložené procedury, triggery a uživatelem definovaných funkcí, které vytvořili, můžete je načíst a zobrazit je na webu Azure Portal pomocí Průzkumníku dat.

Můžete také zjistit následující odkazy a prostředky užitečné ve své cestě Další informace o programování na straně serveru v Azure Cosmos dB:

* [Sady SDK služby Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
* [JSON](http://www.json.org/) 
* [JavaScript ECMA 262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Rozšiřitelnost zabezpečení a přenosné databáze](http://dl.acm.org/citation.cfm?id=276339) 
* [Služba orientované architektuře databáze](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
* [Hostování modulu Runtime .NET v systému Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)
