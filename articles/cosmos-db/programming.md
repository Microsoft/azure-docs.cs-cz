---
title: Programování v jazyce JavaScript na straně serveru pro databázi Azure Cosmos | Microsoft Docs
description: Naučte se používat Azure Cosmos DB zápis uložené procedury, triggery databáze a uživatelem definované funkce (UDF) v jazyce JavaScript. Získáte tipy programing databáze a další.
keywords: Databáze aktivační události, uložené procedury, uložené procedury, program databáze, sproc, azure, Microsoft azure
services: cosmos-db
documentationcenter: ''
author: aliuy
manager: kfile
ms.assetid: 0fba7ebd-a4fc-4253-a786-97f1354fbf17
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: andrl
ms.openlocfilehash: e6fd51cb2550549e14934c3f4774a40d42281247
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Azure programování na straně serveru Cosmos DB: uložené procedury, triggery databáze a UDF

Zjistěte, jak Azure Cosmos DB language-integrated, transakční provádění jazyka JavaScript umožňuje vývojářům zápisu **uložené procedury**, **aktivační události**, a **uživatelsky definované funkce (UDF)**  nativně v [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/) JavaScript. Integrace JavaScriptu umožňuje psát logiku programu, která může být dodána a spustit přímo v databázi oddílů pro úložiště. 

Doporučujeme začít následujícím videem, kde Andrew Liu obsahuje úvod do Azure Cosmos DB serverové databáze programovací model. 

> [!VIDEO https://www.youtube.com/embed/s0cXdHNlVI0]
>
> 

Pak se vraťte k tomuto článku, kde se dozvíte odpovědi na tyto otázky:  

* Jak psát uložené procedury, aktivační události nebo pomocí jazyka JavaScript UDF?
* Jak Cosmos DB zaručit kyseliny?
* Jak fungují transakce v databázi Cosmos?
* Co se aktivuje před a po aktivuje a jak jeden zápis?
* Jak registrovat a spustit uloženou proceduru, aktivační události nebo UDF RESTful způsobem pomocí protokolu HTTP?
* Jaké Cosmos DB sady SDK jsou k dispozici pro vytvoření a provedení uložené procedury, triggery a UDF?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Úvod do uložené procedury a UDF programování
Tento přístup z *"JavaScript jako moderní den T-SQL"* uvolní vývojáři aplikací z složitosti neshody typu systému a relační objekt mapování technologie. Má také počet vnitřní výhody, které můžete použít k vytvoření bohaté aplikace:  

* **Procedurální logika:** JavaScript zde slouží jako základní programovací jazyk, poskytuje bohatou a známá rozhraní pro express obchodní logiku. Můžete provádět komplexní pořadí operací blíže k datům.
* **Jednotlivé transakce:** Cosmos DB záruky, které databáze operací provést v jedné uložené procedury nebo aktivační události jsou atomic. Tato funkce atomic umožní aplikaci kombinovat související operace v jedné dávkové tak, aby všechny úspěšné nebo žádná z nich není úspěšné. 
* **Výkon:** skutečnost, že JSON je vnitřně namapovaný na systém typů jazyka Javascript a také je základní jednotkou úložiště v systému Cosmos DB umožňuje počet optimalizace jako opožděné materialization dokumentů JSON ve fondu vyrovnávací paměti a přitom k dispozici na vyžádání provádění kódu. Existují další výhody výkonu související s přenosů obchodní logiky k databázi:
  
  * Dávkování – vývojáři můžou skupiny operací, jako je vložení a odesílat je hromadně. Latence provoz sítě, náklady a nároky na úložiště k vytvoření samostatné transakce jsou výrazně snížit. 
  * Předběžné kompilace – Cosmos DB překompiluje uložené procedury, triggery a uživatelem definovaných funkcí (UDF) předejdete náklady kompilace jazyka JavaScript pro každé vyvolání. Nároky na vytváření kód bajtů pro Procedurální logika je amortizovaný na minimální hodnotu.
  * Sekvencování – mnoho operations nutné vedlejším účinkem (dále jen "aktivační události"), potenciálně zahrnuje jednu nebo více provádění operací sekundárního úložiště. Kromě zajištění dostatečného nedělitelnost to je další původce při přesunu na server. 
* **Zapouzdření:** uložené procedury lze použít k seskupení obchodní logiky v jednom místě, která má dvě výhody:
  * Přidá abstraktní vrstvu nad nezpracovaná data, která umožňuje data architekty vyvíjí svých aplikací nezávisle data. Tato abstraktní vrstvu, je výhodné, když jsou data bez schémat, z důvodu křehká předpokladů, které možná muset zaručená do aplikace, pokud mají jak nakládat s daty přímo.  
  * Tato abstrakce umožňuje podnikům zabezpečit svá data pomocí zjednodušení přístup z skripty.  

Vytvoření a spuštění databáze aktivační události, uložené procedury a operátory vlastního dotazu jsou podporovány prostřednictvím [portál Azure](https://portal.azure.com), [REST API](/rest/api/cosmos-db/), [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases), a [klientskou sadu SDK](sql-api-sdk-dotnet.md) na spoustě platforem včetně .NET, Node.js a JavaScript.

Tento kurz používá [Node.js SDK Q lišící](http://azure.github.io/azure-documentdb-node-q/) pro ilustraci syntaxi a použití uložené procedury, triggery a UDF.   

## <a name="stored-procedures"></a>Uložené procedury
### <a name="example-write-a-stored-procedure"></a>Příklad: Zápis uložené procedury
Začněme jednoduché uložené procedury, jež vrátí odpověď "Hello World".

    var helloWorldStoredProc = {
        id: "helloWorld",
        serverScript: function () {
            var context = getContext();
            var response = context.getResponse();

            response.setBody("Hello, World");
        }
    }


Uložené procedury jsou registrované na kolekci a mohou pracovat na všechny dokumentů a příloh, které se nachází v dané kolekci. Následující fragment kódu ukazuje, jak zaregistrovat helloWorld uložené procedury s kolekcí. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


Po registraci uloženou proceduru můžete spustit proti kolekci a přečtěte si výsledky zpět na straně klienta. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


Objekt context poskytuje přístup ke všem operacím, které lze provést na úložiště Cosmos databáze, a také přístup k objektům požadavku a odpovědi. V takovém případě použijete objekt odpovědi nastavit text odpovědi, která byla odeslána zpět klientovi. Další informace najdete v tématu [server Azure Cosmos DB JavaScript dokumentaci k sadě SDK](http://azure.github.io/azure-documentdb-js-server/).  

Dejte nám rozbalte v tomto příkladu a přidání dalších funkcí vztahující se k databázi uložené procedury. Uložené procedury můžete vytvořit, aktualizovat, číst, dotazování a odstraňovat dokumenty a přílohy v kolekci.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Příklad: Zápis uložené procedury k vytvoření dokumentu
Další fragment kódu ukazuje, jak pomocí objektu context můžete pracovat s prostředky Cosmos DB.

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


Tuto uloženou proceduru vezme jako vstupní documentToCreate textu dokumentu má být vytvořen v aktuální kolekci. Všechny tyto operace jsou asynchronní a závisí na zpětná volání funkce jazyka JavaScript. Funkce zpětného volání má dva parametry, jeden pro objekt chyby v případě, kdy se operace nezdaří a jeden pro vytvořený objekt. Uvnitř zpětné volání uživatelé mohou zpracovat výjimku nebo vyvolána chyba. V případě, že není k dispozici zpětné volání a dojde k chybě, modul runtime Azure Cosmos DB vyvolá chybu.   

V předchozím příkladu zpětné volání vrátí chybu, pokud operace se nezdařila. Jinak nastaví ID vytvořený dokumentu jako text odpovědi klientovi. Zde je, jak se spustí tuto uloženou proceduru s vstupní parametry.

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


Tuto uloženou proceduru lze upravit tak, aby pole těla dokumentu jako vstup a k jejich vytvoření všechno ve stejném spuštění uložené procedury místo více požadavků pro každý z nich vytvoření jednotlivě. Tuto uloženou proceduru lze použít k implementaci efektivní hromadné – Importér pro DB Cosmos (popsané později v tomto kurzu).   

Popisuje příklad ukázal, jak lze pomocí uložených procedur. Dále se dozvíte o triggery a uživatelem definovaných funkcí (UDF) později v tomto kurzu.

## <a name="database-program-transactions"></a>Databáze programu transakce
Transakce v typické databáze může být definováno jako posloupnost operací provést jako jednu logickou jednotku práce. Poskytuje každou transakci **ACID záruky**. Je kyselina dobře známé zkratku, který zastupuje čtyři vlastnosti - nedělitelnost, konzistence, izolace a odolnost.  

Stručně řečeno, nedělitelnost zaručuje, že všechny práci v transakci je považován za jednu jednotku kde buď všechny klade nebo hodnotu none. Konzistence zajišťuje, že data jsou vždycky ve funkčním stavu interní napříč transakce. Izolace zaručuje, že žádné dvě transakce konfliktu s jinými – obecně, většina komerčních systémy poskytují více úrovní izolace, které lze použít podle potřeb aplikace. Odolnost zajistí, že všechny změny, která je potvrzena v databázi vždy bude k dispozici.   

V Cosmos databáze JavaScript je hostován ve stejném prostoru paměti jako databáze. Proto požadavky provedené v rámci uložené procedury a triggery provést ve stejném oboru relace databáze. Tato funkce umožňuje Cosmos DB zaručit kyseliny pro všechny operace, které jsou součástí jedné uložené procedury nebo aktivační událost. Vezměte v úvahu následující uložené procedury definice:

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

Tuto uloženou proceduru používá transakce v rámci aplikace herní položkám obchodu mezi dvěma přehrávače v rámci jedné operace. Uložená procedura se pokusí přečíst dva dokumenty, které každý odpovídající ID player v předat jako argument. Pokud jsou oba player dokumenty, uložené procedury aktualizuje dokumenty pomocí odkládací jejich položky. Pokud na cestě došlo k chybám, vyvolá výjimku JavaScript, která implicitně zruší transakce.

Pokud je registrován kolekce uložené procedury proti je jedním oddílem kolekce a potom transakce je omezená na všechny dokumenty v rámci kolekce. Pokud je kolekce rozdělena na oddíly, uložené procedury jsou spouštěny v oboru transakce klíče jeden oddíl. Každý uložené provádění procedury pak musí obsahovat hodnotu klíče oddílu odpovídající transakce musí běžet v rámci oboru. Další informace najdete v tématu [Azure Cosmos DB dělení](partition-data.md).

### <a name="commit-and-rollback"></a>Potvrzení a vrácení zpět
Transakce je úzce a nativně integrováno programovací model Cosmos DB jazyka JavaScript. Uvnitř funkce jazyka JavaScript jsou automaticky zabalená všechny operace v rámci jedné transakce. Pokud JavaScript se dokončí bez jakékoli výjimky, se potvrdí operací do databáze. V důsledku toho jsou implicitní v Cosmos DB "BEGIN TRANSACTION" a "Potvrzení transakcí" příkazy v relačních databází.  

Pokud žádné výjimka, která je rozšířena ze skriptu, bude Cosmos DB JavaScript runtime vrátit zpět celou transakci. Jak ukazuje předchozí příklad, došlo k výjimce je efektivně ekvivalentní "Transakce vrácení zpět" v Cosmos DB.

### <a name="data-consistency"></a>Konzistence dat
Uložené procedury a triggery jsou vždy provést u primární repliky kontejneru Azure Cosmos DB. Tím se zajistí, že čtení z uvnitř uložené procedury nabídka silnou konzistenci. Dotazy pomocí uživatelsky definované funkce mohou být provedeny u primární nebo sekundární repliku, ale zajistěte pro splnění úrovně konzistence požadovaný výběrem příslušné repliky.

## <a name="bounded-execution"></a>Ohraničené provádění
Všechny operace Cosmos DB musí dokončit v rámci serveru zadané trvání časového limitu požadavku. Toto omezení platí i pro funkce jazyka JavaScript (uložené procedury, triggery a uživatelem definované funkce). Pokud se tento časový limit operace nebude dokončena, transakce je vrácena zpět. Funkce jazyka JavaScript musí dokončit v časovém limitu nebo implementovat modelu na základě pokračování pro spuštění dávky nebo obnovení.  

Aby bylo možné zjednodušit vývoj uložených procedur a aktivačních událostí ke zpracování časových limitů, všechny funkce v rámci kolekce objektu (vytvořit, číst, nahraďte a odstranění dokumentů a přílohy) vrátí logickou hodnotu hodnotu této představuje jestli tuto operaci dokončí. Pokud je tato hodnota false, je to znamenat, že je časový limit vyprší a, musíte si provádění zabalit postupu.  Operace zařazených do fronty před první operace nepřijatelného úložiště je zaručeno dokončení, pokud uložená procedura se dokončí za dobu a fronty nejsou žádné další žádosti.  

Funkce jazyka JavaScript jsou také vázaný na spotřeby prostředků. Cosmos DB si vyhrazuje propustnosti na kolekci nebo pro sadu kontejnery. Propustnost se vyjadřuje jako normalizované jednotka procesoru, paměti a jednotek žádosti nebo RUs spotřeba vstupně-výstupní operace. Funkce jazyka JavaScript může potenciálně spotřebovávat velký počet RUs během krátké doby a může získat míra limited, pokud bude dosažen limit kolekce. Uložené procedury náročná může také umístí do karantény k zajištění dostupnosti primitivní databázových operací.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Příklad: Hromadný import dat do databáze programu
Dole je příklad uložené procedury, jež je zapsán do hromadného importu dokumenty do kolekce. Všimněte si, jak uloženou proceduru zpracovává ohraničené provádění kontrolou logickou hodnotu návratová hodnota z createDocument a pak používá k sledování a pokračovat v průběhu napříč dávek počet dokumentů vložen do každé volání uložené procedury.

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

## <a id="trigger"></a> Aktivační události databáze
### <a name="database-pre-triggers"></a>Databáze před aktivační události
Cosmos DB poskytuje triggery, které jsou provedeny nebo aktivovány operace v dokumentu. Například můžete zadat před aktivační událost, když vytváříte dokumentu – Tato předběžná aktivační událost se spustí před vytvořením dokumentu. Následující příklad ukazuje, jak lze pomocí předběžné aktivační události ověření vlastností dokumentu, který je právě vytvářena:

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


A odpovídající kód Node.js registrace klienta pro aktivační událost:

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


Předběžné aktivační události nemůže mít žádné vstupní parametry. Objekt požadavku můžete použít k manipulaci zprávu požadavku přidruženou operaci. Zde předběžné aktivační události je spuštěn s vytvoření dokumentu a tělo zprávy požadavku obsahuje dokumentu, který má být vytvořen ve formátu JSON.   

Když jsou registrované aktivačních událostí, mohou uživatelé zadat operace, které můžete spustit s. Této aktivační události byl vytvořen s TriggerOperation.Create, což znamená, že v operaci nahrazení, jak je znázorněno v následujícím kódu pomocí aktivační událost není povoleno.

    var options = { preTriggerInclude: "validateDocumentContents" };

    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });

    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Databáze po aktivační události
Po aktivační události, například před aktivační události, jsou spojena s operací na dokument a nechcete provést žádné vstupní parametry. Mohou být spuštěny **po** operace byla dokončena a mít přístup k zprávu odpovědi, která je odeslána do klienta.   

Následující příklad ukazuje po aktivační události v akci:

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


Aktivační událost lze zaregistrovat, jak znázorňuje následující ukázka.

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


Této aktivační události dotazuje na dokument metadat a aktualizuje s podrobnostmi o nově vytvořený dokumentu.  

Je jednou z věcí, je důležité si uvědomit **transakcí** provádění aktivační události do databáze. Cosmos. Této aktivační události po spuštění v rámci stejné transakci jako vytváření původního dokumentu. Pokud tedy můžete vyvolat výjimku z po aktivační událost (indikované nejde aktualizovat dokument metadat), je celá transakce se nezdaří a vrácena zpět. Žádné dokumentu se vytvoří a bude vrácen výjimku.  

## <a id="udf"></a>Uživatelem definované funkce
Uživatelem definované funkce (UDF) slouží k rozšíření gramatiky jazyka dotazů Azure Cosmos DB SQL a implementovat vlastní obchodní logiku. Je možné volat jedině z uvnitř dotazy. Tyto nemají přístup k objektu kontextu a jsou určené pro použití jako jen výpočetní JavaScript. Proto UDF lze spustit na sekundárních replikách služby Cosmos DB.  

Následující příklad vytvoří UDF k výpočtu daně z příjmu podle sazby za různé příjem závorky a používá je uvnitř dotazu najít všechny lidí, kteří v daně placené více než 20 000 $.

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


UDF lze následně použít v dotazech jako následující ukázka:

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

## <a name="javascript-language-integrated-query-api"></a>JavaScript language-integrated query rozhraní API
Kromě vydávat dotazy pomocí gramatiky SQL Azure Cosmos DB, SDK na straně serveru umožňuje provádět optimalizované dotazy pomocí rozhraní fluent JavaScript bez znalostí SQL. Dotaz jazyka JavaScript, který rozhraní API umožňuje prostřednictvím kódu programu vytvořit dotazy předáním predikátem funkce do chainable funkce volá s pro pole built-ins a Oblíbené knihovny JavaScript jako Lodash ECMAScript5 na syntaxi. Dotazy jsou analyzovat pomocí prostředí JavaScript runtime spouštění efektivně pomocí Azure Cosmos DB indexy.

> [!NOTE]
> `__` (dvojité podtržítko) je alias `getContext().getCollection()`.
> <br/>
> Jinými slovy, můžete použít `__` nebo `getContext().getCollection()` pro přístup k rozhraní API dotazu jazyka JavaScript.
> 
> 

Podporované funkce patří:

<ul>
<li>
<b>chain().... hodnota ([zpětného volání] [, možnosti])</b>
<ul>
<li>
Začíná value() zřetězené volání, které musí být ukončena.
</li>
</ul>
</li>
<li>
<b>Filtr (predicateFunction [, možnosti] [, zpětného volání])</b>
<ul>
<li>
Filtry vstupu pomocí funkce predikátu, která vrátí hodnotu true nebo false, chcete-li filtrovat a konec vstupu dokumenty do výsledné sady. Tato funkce se chová podobně jako klauzule WHERE v příkazu SQL.
</li>
</ul>
</li>
<li>
<b>mapy (transformationFunction [, možnosti] [, zpětného volání])</b>
<ul>
<li>
Platí projekci zadané transformační funkce, který se mapuje na objekt jazyka JavaScript nebo hodnota každou vstupní položku. Tato funkce se chová podobně jako v klauzuli SELECT v systému SQL.
</li>
</ul>
</li>
<li>
<b>pluck ([propertyName] [, možnosti] [, zpětného volání])</b>
<ul>
<li>
Tato funkce je zkratka pro mapu, která extrahuje hodnotu jedinou vlastnost z každé vstupní položky.
</li>
</ul>
</li>
<li>
<b>vyrovnání ([isShallow] [, možnosti] [, zpětného volání])</b>
<ul>
<li>
Kombinuje a vyrovná pole z každé vstupní položky do jednoho pole. Tato funkce se chová podobně jako označit více v technologii LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy ([predikát] [, možnosti] [, zpětného volání])</b>
<ul>
<li>
Vytvořit novou sadu dokumentů podle řazení dokumenty v datovém proudu vstupní dokument ve vzestupném pořadí pomocí daného predikátu. Tato funkce se chová podobně jako klauzuli ORDER BY v systému SQL.
</li>
</ul>
</li>
<li>
<b>sortbydescending – ([predikát] [, možnosti] [, zpětného volání])</b>
<ul>
<li>
Vytvořit novou sadu dokumentů podle řazení dokumenty v datovém proudu vstupní dokument v sestupném pořadí pomocí daného predikátu. Tato funkce se chová podobně jako klauzuli ORDER BY x DESC v systému SQL.
</li>
</ul>
</li>
</ul>


Pokud je součástí uvnitř predikátu nebo pro výběr funkcí, následující konstrukce JavaScript získat automaticky optimalizovaná tak, aby spustit přímo v Azure Cosmos DB indexů:

* Jednoduché operátory: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Literály, včetně objekt literálu: {}
* var, návratový

Následující konstrukce JavaScript získat není optimalizovaný pro Azure Cosmos DB indexů:

* Řízení toku (například pokud, zatímco)
* Volání funkcí

Další informace najdete v tématu [serverové JSDocs](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Příklad: Zápis uložené procedury pomocí rozhraní API dotazu jazyka JavaScript
Následující ukázka kódu je příklad použití rozhraní API dotazu jazyka JavaScript v rámci uložené procedury. Uložená procedura vloží dokumentu, poskytují vstupní parametr a metadat aktualizací dokumentů, pomocí `__.filter()` metoda s minSize, maxSize a totalSize podle vlastnosti velikosti vstupní dokument.

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

## <a name="sql-to-javascript-cheat-sheet"></a>Server SQL k Javascript – tahák
Následující tabulka uvádí různé dotazy SQL a odpovídající dotazy jazyka JavaScript.

Jako s dotazy SQL dokumentu vlastnosti klíče (například `doc.id`) malých a velkých písmen.

|SQL| Rozhraní API dotazu jazyka JavaScript|Popis dole|
|---|---|---|
|VYBERTE *<br>Z dokumentace| __.map(Function(DOC) { <br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc;<br>});|1|
|Vyberte docs.id, docs.message jako msg, docs.actions <br>Z dokumentace|__.map(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|2|
|VYBERTE *<br>Z dokumentace<br>WHERE docs.id="X998_Y998"|__.Filter(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc.id === "X998_Y998";<br>});|3|
|VYBERTE *<br>Z dokumentace<br>KDE ARRAY_CONTAINS (dokumentace. Značky, 123)|__.Filter(Function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí x.Tags & & x.Tags.indexOf(123) > -1;<br>});|4|
|Vyberte docs.id, docs.message jako msg<br>Z dokumentace<br>WHERE docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Filter(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc.id === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vrátí {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|5|
|SELECT VALUE – značka<br>Z dokumentace<br>Připojte značky v dokumentaci. Značky<br>Docs._ts ORDER BY|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Filter(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vrátí dokumentů. Značky & & Array.IsArray – (dokumentů. Značky);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Value()|6|

V následujících popisech popisují každý dotaz v předchozí tabulce.
1. Výsledkem všechny dokumenty (čísla stránek vložena s token pokračování), jako je.
2. Projekty id, zprávy (alias pro msg) a akce z všechny dokumenty.
3. Dotazy na dokumenty s predikát: id = "X998_Y998".
4. Dotazy pro dokumenty, které mají značky a vlastnost Tags je pole obsahující hodnotu 123.
5. Dotazy na dokumenty s predikátem, id = "X998_Y998" a pak projekty id a zprávy (alias pro msg).
6. Filtry pro dokumenty, které mít vlastnost pole, značky a seřadí výsledné dokumenty vlastností _ts časové razítko systému a pak projekty + vyrovná pole značky.


## <a name="runtime-support"></a>Podpora modulu runtime
Azure Cosmos DB [JavaScript serveru straně API](http://azure.github.io/azure-documentdb-js-server/) poskytuje podporu pro většinu všeobecně funkce jazyka JavaScript jako standardizované podle [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Zabezpečení
JavaScript uložené procedury a triggery jsou v izolovaném prostoru tak, aby důsledky jeden skript není pronikly na druhý bez průchodu přes transakci izolace snímku na úrovni databáze. Prostředí runtime jsou ve fondu, ale čištění kontextu po každé spuštění. Proto jsou zaručeno bezpečné z jakékoli nezamýšleným vedlejší účinky od sebe navzájem.

### <a name="pre-compilation"></a>Předkompilace
Uložené procedury, triggery a UDF jsou implicitně předkompilovaných na formát kódu byte předejdete tak náklady na kompilace v době každé vyvolání skriptu. Předběžné kompilace zajišťuje volání uložené procedury je rychlá a nízkým nárokům mít.

## <a name="client-sdk-support"></a>Podpora klienta SDK
Kromě Azure Cosmos DB [Node.js](sql-api-sdk-node.md) rozhraní API, Azure Cosmos DB má [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript ](http://azure.github.io/azure-documentdb-js/), a [Python SDK](sql-api-sdk-python.md) pro rozhraní SQL API. Uložené procedury, triggery a UDF lze vytvořit a spustit některé z těchto sad SDK také používá. Následující příklad ukazuje postup vytvoření a provedení uložené procedury pomocí klienta rozhraní .NET. Všimněte si, jak jsou typy .NET předaný do uložené procedury jako JSON a čtení zpět.

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


Tento příklad ukazuje způsob použití [SQL .NET API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) vytvořit před aktivační události a vytvořit dokument s aktivační událost povolena. 

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


A následující příklad ukazuje, jak vytvořit uživatelem definované funkce (UDF) a použít ho [dotazu SQL](sql-api-sql-query.md).

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

## <a name="rest-api"></a>REST API
Všechny operace Azure Cosmos databáze lze provést RESTful způsobem. Uložené procedury, triggery a uživatelem definované funkce může být registrováno v rámci kolekce pomocí HTTP POST. Následující příklad ukazuje, jak zaregistrovat uložené procedury:

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


Uložená procedura je zaregistrován spuštěním požadavek POST URI databází nebo testdb/colls/testColl/sprocs spolu s textem obsahující uložené procedury k vytvoření. Aktivační události a funkcí UDF lze registrovat podobně vydáním POST proti/aktivační události a /udfs v uvedeném pořadí.
Tato uložená procedura může poté provést po vydání požadavku POST s jeho odkazu prostředku:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Zde vstup uložené procedury je předán v textu požadavku. Vstup je předán jako pole JSON vstupních parametrů. Uložená procedura přijímá první vstup jako dokument, který je text odpovědi. Odpověď, který obdržíte vypadá takto:

    HTTP/1.1 200 OK

    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Na rozdíl od uložené procedury, aktivační události nelze spustit přímo. Místo toho jsou spouštěny jako součást operace v dokumentu. Můžete zadat aktivačních událostí ke spuštění s žádostí pomocí hlaviček protokolu HTTP. Následující kód ukazuje požadavek na vytvoření dokumentu.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger


    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


Předběžné aktivační událost ke spuštění s požadavkem je zde uvedená v hlavičce x-ms-documentdb-pre-trigger-include. Žádné aktivační události po odpovídajícím způsobem, jsou uvedeny v hlavičce x-ms-documentdb-post-trigger-include. Obě před a po aktivační události lze zadat pro daný požadavek.

## <a name="sample-code"></a>Ukázka kódu
Můžete najít další příklady kódu na straně serveru (včetně [hromadné odstranění](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js), a [aktualizace](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) v [úložiště GitHub](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

Chcete sdílet vaše Super uložené procedury? přispívat do úložiště a vytvořte žádost o přijetí změn! 

## <a name="next-steps"></a>Další postup
Až budete mít jeden nebo více uložené procedury, triggery a uživatelem definované funkce vytvoření, můžete je načíst a zobrazit je na portálu Azure pomocí Průzkumníku dat.

Můžete také zjistit následující odkazy a prostředky užitečné ve své cestě Další informace o programování na straně serveru dB Azure Cosmos:

* [Sady SDK služby Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
* [JSON](http://www.json.org/) 
* [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Rozšiřitelnost zabezpečení a přenosné databáze](http://dl.acm.org/citation.cfm?id=276339) 
* [Služba zaměřené na konkrétní architektura databáze](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
* [Hostování prostředí .NET Runtime v systému Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)

