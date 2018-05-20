---
title: Azure Cosmos DB vazby pro funkce
description: Pochopit, jak používat Azure Cosmos DB triggerů a vazeb v Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Funkce Azure, funkce zpracování událostí, dynamické výpočetní architektura bez serveru
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: ffb18ef65bc0d901fe237ec9c4f97fdae43dc472
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="azure-cosmos-db-bindings-for-azure-functions"></a>Azure Cosmos DB vazby pro Azure Functions

Tento článek vysvětluje, jak pracovat s [Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) vazeb v Azure Functions. Azure Functions podporuje aktivaci, vstup a výstup vazby pro Azure Cosmos DB.

> [!NOTE]
> Tato vazba byl původně pojmenován DocumentDB. Ve verzi funkce 1.x, pouze aktivační událost byla přejmenována Cosmos DB; Vstupní vazby, výstup vazby a balíček NuGet zachovat název DocumentDB. V [funkce verze 2.x](functions-versions.md), vazby a balíčku byly také přejmenovat DB Cosmos. Tento článek používá 1.x názvy.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Balíčky

Vazby Cosmos DB verze funkcí jsou součástí 1.x [Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) balíček NuGet. Pro funkce 2.x, je balíček [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB). Zdrojový kód pro vazby je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

## <a name="trigger"></a>Trigger

Aktivační událost DB Cosmos Azure používá [Azure Cosmos DB změnu kanálu](../cosmos-db/change-feed.md) tak, aby naslouchala na změny napříč oddíly. Informační kanál změnu publikuje vložení a aktualizace, není odstranění. Pro každý vložení nebo aktualizace provedené v kolekci monitorovaných je volána aktivační událost. 

## <a name="trigger---example"></a>Aktivační událost – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Aktivační událost – příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) , aktivuje z konkrétní databáze a kolekce.

```cs
    using System.Collections.Generic;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
            log.Info("Documents modified " + documents.Count);
            log.Info("First document Id " + documents[0].Id);
    }
```

### <a name="trigger---c-script-example"></a>Aktivační událost – příklad skriptu jazyka C#

Následující příklad ukazuje, aktivační událost Cosmos DB vazby ve *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce zapíše zprávy protokolu při změně záznamy Cosmos DB.

Zde je vazba dat v *function.json* souboru:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Tady je kód skriptu jazyka C#:
 
```cs 
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
      log.Verbose("Documents modified " + documents.Count);
      log.Verbose("First document Id " + documents[0].Id);
    }
```

### <a name="trigger---javascript-example"></a>Aktivační událost – příklad v jazyce JavaScript

Následující příklad ukazuje, aktivační událost Cosmos DB vazby ve *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce zapíše zprávy protokolu při změně záznamy Cosmos DB.

Zde je vazba dat v *function.json* souboru:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Tady je kód jazyka JavaScript:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

## <a name="trigger---attributes"></a>Aktivační událost – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) atribut.

Konstruktoru atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a další vlastnosti, které můžete konfigurovat, najdete v článku [aktivační událost - konfigurace](#trigger---configuration). Tady je `CosmosDBTrigger` atribut příkladu podpis metody:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Úplný příklad najdete v tématu [aktivační událost - C# příklad](#trigger---c-example).

## <a name="trigger---configuration"></a>Aktivační událost - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `CosmosDBTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** || musí být nastavena na `cosmosDBTrigger`. |
|**direction** || musí být nastavena na `in`. Tento parametr je nastaven automaticky při vytváření aktivační události na portálu Azure. |
|**Jméno** || Název proměnné používá v kódu funkce, která představuje seznam dokumentů se změnami. | 
|**ConnectionStringSetting**|**ConnectionStringSetting** | Název nastavení aplikace, který obsahuje připojovací řetězec použitý pro připojení k účtu Azure Cosmos DB monitorovány. |
|**databaseName**|**DatabaseName**  | Název databáze Azure Cosmos DB s kolekcí monitorovány. |
|**Název_kolekce** |**Název_kolekce** | Název kolekce, který je monitorován. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Volitelné) Název nastavení aplikace, který obsahuje připojovací řetězec k službě, která kolekci zapůjčení. Pokud není nastavena, `connectionStringSetting` hodnota se používá. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. Připojovací řetězec pro kolekci zapůjčení musí mít oprávnění k zápisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Volitelné) Název databáze, který obsahuje kolekci používá k ukládání zapůjčení. Pokud není nastavena hodnota `databaseName` nastavení se používá. Tento parametr je automaticky nastaven při vytvoření vazby na portálu. |
|**leaseCollectionName** | **LeaseCollectionName** | (Volitelné) Název kolekce používá k ukládání zapůjčení. Pokud není nastavena hodnota `leases` se používá. |
|**CreateLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Volitelné) Pokud nastavíte hodnotu `true`, kolekci zapůjčení se automaticky vytvoří, pokud ještě neexistuje. Výchozí hodnota je `false`. |
|**LeasesCollectionThroughput**| **LeasesCollectionThroughput**| (Volitelné) Definuje množství jednotky žádosti přiřadit při vytvoření kolekce zapůjčení. Toto nastavení je pouze použité při `createLeaseCollectionIfNotExists` je nastaven na `true`. Tento parametr je automaticky nastaven při vytvoření vazby na portálu.
|**LeaseCollectionPrefix**| **LeaseCollectionPrefix**| (Volitelné) Pokud nastavíte, přidá předponu zapůjčení vytvořené v kolekci zapůjčení pro tuto funkci umožňuje efektivně dvě samostatné funkce Azure sdílet stejné zapůjčení kolekci pomocí jiné předpony.
|**FeedPollDelay**| **FeedPollDelay**| (Volitelné) Pokud sadu, definuje, v milisekundách, zpoždění mezi dotazování oddíl pro nové změny v informačním kanálu se nečekaně po všechny aktuální změny. Výchozí hodnota je 5000 (5 sekund).
|**LeaseAcquireInterval**| **LeaseAcquireInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval, který ji úlohu Vypočítat, pokud oddíly jsou rovnoměrně rozdělené mezi instancí známé hostitele. Výchozí hodnota je 13000 (13 sekund).
|**LeaseExpirationInterval**| **LeaseExpirationInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval, pro kterou je zapůjčení pořízené zapůjčení představující oddílu. Pokud během tohoto intervalu neobnovíte zapůjčení, může to způsobit vypršení platnosti, a vlastnictví oddílu se přesune do jiné instance. Výchozí hodnota je 60000 (60 sekund).
|**LeaseRenewInterval**| **LeaseRenewInterval**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval obnovení pro všechny zapůjčení pro oddíly, které jsou aktuálně uchovávat instance. Výchozí hodnota je 17000 (17 sekund).
|**CheckpointFrequency**| **CheckpointFrequency**| (Volitelné) Pokud nastavíte, definuje, v milisekundách, interval mezi body obnovení zapůjčení. Výchozí hodnota je vždy po úspěšném volání funkce.
|**MaxItemsPerInvocation**| **MaxItemsPerInvocation**| (Volitelné) Pokud nastavíte, přizpůsobuje maximální množství přijatých položek pro volání funkce.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační událost - využití

Aktivační událost vyžaduje druhé kolekci, která se používá k ukládání _zapůjčení_ přes oddíly. Kolekce, který je monitorován a kolekce, která obsahuje zapůjčení musí být k dispozici pro aktivační událost pro práci.

 >[!IMPORTANT]
 > Pokud víc funkcí nakonfigurovány pro používání Cosmos DB aktivační událost pro stejné kolekci, každá z těchto funkcí by měl použít kolekci s vyhrazenou zapůjčení. Jinak se spustí jenom jedna z funkcí. 

Aktivační událost není označuje, že zda dokument byl aktualizaci nebo vložení, pouze poskytuje samotného dokumentu. Pokud potřebujete pro zpracování aktualizace a vloží odlišně, můžete to udělat implementací pole časového razítka pro vložení nebo aktualizace.

## <a name="input"></a>Vstup

Načte jeden nebo více dokumentů Azure Cosmos DB a předává je do vstupní parametr funkce vstupní vazeb Azure Cosmos DB. Parametry dotazu nebo ID dokumentu lze určit podle aktivační událost, která volá funkci. 

>[!NOTE]
> Nechcete používat Azure Cosmos DB vstup nebo výstup vazby, pokud používáte rozhraní API MongoDB na účet Cosmos DB. Poškození dat je možné.

## <a name="input---example-1"></a>(Vstup) – Příklad 1

Podívejte se na příklad pro specifický jazyk, který čte jednoho dokumentu:

* [C#](#input---c-example)
* [C# skript (.csx)](#input---c-script-example)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>(Vstup) – příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) který jednoho dokumentu načte z konkrétní databáze a kolekce. 

První, `Id` a `Maker` hodnoty `CarReview` instanci předávána do fronty. DB Cosmos vazba používá `Id` a `Maker` ze zprávy ve frontě pro načtení dokumentu z databáze.

```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    namespace CosmosDB
    {
        public static class SingleEntry
        {
            [FunctionName("SingleEntry")]
            public static void Run(
                [QueueTrigger("car-reviews", Connection = "StorageConnectionString")] CarReview carReview,
                [DocumentDB("cars", "car-reviews", PartitionKey = "{maker}", Id= "{id}", ConnectionStringSetting = "CarReviewsConnectionString")] CarReview document,
                TraceWriter log)
            {
                log.Info( $"Selected Review - {document?.Review}"); 
            }
        }
    }
```

Tady je `CarReview` objektů POCO:

 ```cs
    public class CarReview
    {
        public string Id { get; set; }
        public string Maker { get; set; }
        public string Description { get; set; }
        public string Model { get; set; }
        public string Image { get; set; }
        public string Review { get; set; }
    }
 ```

### <a name="input---c-script-example"></a>(Vstup) – příklad skriptu jazyka C#

Následující příklad ukazuje vstupní vazbu Cosmos DB v *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce přečte jednotlivý dokument a aktualizuje dokumentu textovou hodnotu.

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```
[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

<a name="infsharp"></a>

### <a name="input---f-example"></a>(Vstup) – příklad F #

Následující příklad ukazuje vstupní vazbu Cosmos DB v *function.json* souboru a [F # funkce](functions-reference-fsharp.md) používající vazby. Funkce přečte jednotlivý dokument a aktualizuje dokumentu textovou hodnotu.

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```

[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód F #:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Tento příklad vyžaduje, `project.json` soubor, který určuje `FSharp.Interop.Dynamic` a `Dynamitey` NuGet závislosti:

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

Chcete-li přidat `project.json` souborů najdete v tématu [správy balíčků F #](functions-reference-fsharp.md#package).

### <a name="input---javascript-example"></a>(Vstup) – příklad v jazyce JavaScript

Následující příklad ukazuje vstupní vazbu Cosmos DB v *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce přečte jednotlivý dokument a aktualizuje dokumentu textovou hodnotu.

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "inputDocumentIn",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

## <a name="input---example-2"></a>(Vstup) – příklad 2

Podívejte se na příklad pro specifický jazyk, který čte více dokumentů:

* [C#](#input---c-example-2)
* [C# skript (.csx)](#input---c-script-example-2)
* [JavaScript](#input---javascript-example-2)

### <a name="input---c-example-2"></a>Vstup - C# příklad 2

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) , provede dotaz SQL. Použít `SqlQuery` parametr, musíte nainstalovat nejnovější verze beta `Microsoft.Azure.WebJobs.Extensions.DocumentDB` balíček NuGet.

```csharp
    using System.Net;
    using System.Net.Http;
    using System.Collections.Generic;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;

    [FunctionName("CosmosDBSample")]
    public static HttpResponseMessage Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get")] HttpRequestMessage req,
        [DocumentDB("test", "test", ConnectionStringSetting = "CosmosDB", SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")] IEnumerable<object> documents)
    {
        return req.CreateResponse(HttpStatusCode.OK, documents);
    }
```

### <a name="input---c-script-example-2"></a>(Vstup) – příklad skriptu jazyka C# 2

Následující příklad ukazuje vazbu vstupní v Azure Cosmos DB *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce načte více dokumentů určeného dotaz SQL, chcete-li přizpůsobit parametry dotazu pomocí aktivační procedury fronty.

Aktivační událost fronty poskytuje parametr `departmentId`. Fronty zpráv z `{ "departmentId" : "Finance" }` by vrátit všechny záznamy pro finančního oddělení. 

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {   
        foreach (var doc in documents)
        {
            // operate on each document
        }    
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

### <a name="input---javascript-example-2"></a>(Vstup) – příklad v jazyce JavaScript 2

Následující příklad ukazuje vazbu vstupní v Azure Cosmos DB *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce načte více dokumentů určeného dotaz SQL, chcete-li přizpůsobit parametry dotazu pomocí aktivační procedury fronty.

Aktivační událost fronty poskytuje parametr `departmentId`. Fronty zpráv z `{ "departmentId" : "Finance" }` by vrátit všechny záznamy pro finančního oddělení. 

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
    module.exports = function (context, input) {    
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }       
        context.done();
    };
```

## <a name="input---attributes"></a>(Vstup) – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) atribut.

Konstruktoru atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a další vlastnosti, které můžete konfigurovat, najdete v článku [následující konfigurační oddíl](#input---configuration). 

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `DocumentDB` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**     || musí být nastavena na `documentdb`.        |
|**direction**     || musí být nastavena na `in`.         |
|**Jméno**     || Název parametru vazby, který představuje dokumentu ve funkci.  |
|**databaseName** |**DatabaseName** |Databáze obsahující dokumentu.        |
|**Název_kolekce** |**Název_kolekce** | Název kolekce, která obsahuje dokument. |
|**ID**    | **ID** | ID dokumentu pro načtení. Tato vlastnost podporuje [vazby výrazy](functions-triggers-bindings.md#binding-expressions-and-patterns). Obě není nastavený **id** a **sqlQuery** vlastnosti. Pokud není nastavený buď jednu, je načíst celou kolekci. |
|**sqlQuery**  |**SqlQuery**  | Dotaz služby Azure Cosmos DB SQL použitý k načtení více dokumentů. Vlastnost podporuje runtime vazby, jako v následujícím příkladě: `SELECT * FROM c where c.departmentId = {departmentId}`. Obě není nastavený **id** a **sqlQuery** vlastnosti. Pokud není nastavený buď jednu, je načíst celou kolekci.|
|**Připojení**     |**ConnectionStringSetting**|Název nastavení aplikace obsahující připojovacího řetězce Azure Cosmos DB.        |
|**Klíč oddílu**|**Klíč oddílu**|Určuje hodnotu klíče oddílu pro vyhledávání. Může zahrnovat vázané parametry.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>(Vstup) – použití

V jazyce C# a F # funkce při ukončení funkce úspěšně, některé změny provedené vstupní dokument prostřednictvím pojmenované vstupní parametry jsou automaticky nastavené jako trvalé. 

V funkce jazyka JavaScript nejsou automaticky provedeny aktualizace po ukončení funkce. Místo toho použijte `context.bindings.<documentName>In` a `context.bindings.<documentName>Out` pro nastavení aktualizací. Najdete v článku [příklad v jazyce JavaScript](#input---javascript-example).

## <a name="output"></a>Výstup

Azure Cosmos DB výstup vazby umožňuje zapsat nový textový dokument k databázi Azure Cosmos DB. 

>[!NOTE]
> Nechcete používat Azure Cosmos DB vstup nebo výstup vazby, pokud používáte rozhraní API MongoDB na účet Cosmos DB. Poškození dat je možné.

## <a name="output---example"></a>Výstup – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Výstup – příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) doplňuje dokument k databázi, pomocí data zadaná v zpráv z fronty úložiště.

```cs
    using System;
    using Microsoft.Azure.WebJobs;

    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        document = new { Text = myQueueItem, id = Guid.NewGuid() };
    }
```

### <a name="output---c-script-example"></a>Výstup – příklad skriptu jazyka C#

Následující příklad ukazuje výstup Azure Cosmos DB vazby ve *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce, která používá vazbu vstupní fronty pro frontu, která přijímá JSON v následujícím formátu:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkce vytvoří Azure Cosmos DB dokumenty ve formátu pro každý záznam:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

[Konfigurace](#output---configuration) část vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
      log.Info($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

Pokud chcete vytvořit více dokumentů, můžete vázat na `ICollector<T>` nebo `IAsyncCollector<T>` kde `T` je jedním z podporovaných typů.

### <a name="output---f-example"></a>Výstup – příklad F #

Následující příklad ukazuje výstup Azure Cosmos DB vazby ve *function.json* souboru a [F # funkce](functions-reference-fsharp.md) používající vazby. Funkce, která používá vazbu vstupní fronty pro frontu, která přijímá JSON v následujícím formátu:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkce vytvoří Azure Cosmos DB dokumenty ve formátu pro každý záznam:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```
[Konfigurace](#output---configuration) část vysvětluje tyto vlastnosti.

Tady je kód F #:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
      log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Tento příklad vyžaduje, `project.json` soubor, který určuje `FSharp.Interop.Dynamic` a `Dynamitey` NuGet závislosti:

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

Chcete-li přidat `project.json` souborů najdete v tématu [správy balíčků F #](functions-reference-fsharp.md#package).

### <a name="output---javascript-example"></a>Výstup – příklad v jazyce JavaScript

Následující příklad ukazuje výstup Azure Cosmos DB vazby ve *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce, která používá vazbu vstupní fronty pro frontu, která přijímá JSON v následujícím formátu:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkce vytvoří Azure Cosmos DB dokumenty ve formátu pro každý záznam:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Zde je vazba dat v *function.json* souboru:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

[Konfigurace](#output---configuration) část vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({ 
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

## <a name="output---attributes"></a>Výstup – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) atribut.

Konstruktoru atributu přebírá název databáze a název kolekce. Informace o těchto nastaveních a další vlastnosti, které můžete konfigurovat, najdete v článku [výstup - konfigurace](#output---configuration). Tady je `DocumentDB` atribut příkladu podpis metody:

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Úplný příklad najdete v tématu [výstup - C# příklad](#output---c-example).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `DocumentDB` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**     || musí být nastavena na `documentdb`.        |
|**direction**     || musí být nastavena na `out`.         |
|**Jméno**     || Název parametru vazby, který představuje dokumentu ve funkci.  |
|**databaseName** | **DatabaseName**|Databáze obsahující kolekci, kde se má vytvořit dokumentu.     |
|**Název_kolekce** |**Název_kolekce**  | Název kolekce, kde se má vytvořit dokumentu. |
|**CreateIfNotExists**  |**CreateIfNotExists**    | Logická hodnota označující, zda kolekce se vytvoří při neexistuje. Výchozí hodnota je *false* s vyhrazenou propustností, který obsahuje náklady důsledky se vytváří nové kolekce. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**Klíč oddílu**|**Klíč oddílu** |Když `CreateIfNotExists` hodnotu true, definuje cestu ke klíči oddílu pro vytvořenou kolekci.|
|**CollectionThroughput**|**CollectionThroughput**| Když `CreateIfNotExists` hodnotu true, definuje [propustnost](../cosmos-db/set-throughput.md) vytvořené kolekce.|
|**Připojení**    |**ConnectionStringSetting** |Název nastavení aplikace obsahující připojovacího řetězce Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – použití

Ve výchozím nastavení když zapíšete do výstupního parametru ve funkci, se dokumentu vytvoří v databázi. Tento dokument má automaticky vytvářenému identifikátoru GUID jako ID dokumentu. Můžete zadat ID dokumentu výstupního dokumentu zadáním `id` vlastnost v objektu JSON předaný výstupní parametr. 

> [!Note]  
> Pokud zadáte ID stávající dokument, získá přepsány nový dokument výstup. 

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| CosmosDB | [Kódy chyb CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejděte na rychlé spuštění, používající aktivační událost Cosmos DB](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [Další informace o výpočetní s Cosmos DB bez serveru databáze](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)
