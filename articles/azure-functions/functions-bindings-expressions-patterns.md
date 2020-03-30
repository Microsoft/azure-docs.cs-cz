---
title: Azure Functions vazby výrazy a vzory
description: Naučte se vytvářet různé výrazy vazby Azure Functions na základě běžných vzorů.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 2d0cf18de09932c5d66e269a85919f4d85383c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277645"
---
# <a name="azure-functions-binding-expression-patterns"></a>Vzory výrazů vazby Azure Functions

Jedním z nejvýkonnějších funkcí [aktivačních událostí a vazeb](./functions-triggers-bindings.md) je *vazby výrazy*. V souboru *function.json* a v parametrech funkce a kódu můžete použít výrazy, které se přemisťují na hodnoty z různých zdrojů.

Většina výrazů je označena a tím, že je zabalena ve složených závorkách. Například ve funkci aktivační `{queueTrigger}` události fronty se překládá na text zprávy fronty. Pokud `path` je `container/{queueTrigger}` vlastnost pro vazbu výstupu objektu blob `HelloWorld`a funkce je `HelloWorld` spuštěna zprávou fronty , je vytvořen objekt blob s názvem.

Typy vazbových výrazů

* [Nastavení aplikace](#binding-expressions---app-settings)
* [Název souboru aktivační události](#trigger-file-name)
* [Metadata aktivační události](#trigger-metadata)
* [Datové části JSON](#json-payloads)
* [Nový GUID](#create-guids)
* [Aktuální datum a čas](#current-time)

## <a name="binding-expressions---app-settings"></a>Výrazy vazby – nastavení aplikace

Jako osvědčený postup by měly být tajné klíče a připojovací řetězce spravovány pomocí nastavení aplikace, nikoli pomocí konfiguračních souborů. To omezuje přístup k těmto tajným kódům a umožňuje bezpečné ukládání souborů, jako je *function.json* v úložištích správy veřejných zdrojů.

Nastavení aplikace jsou také užitečné, kdykoli chcete změnit konfiguraci na základě prostředí. Například v testovacím prostředí můžete chtít sledovat jinou frontu nebo kontejner úložiště objektů blob.

Výrazy vazby nastavení aplikace jsou identifikovány odlišně od jiných výrazů vazby: jsou zabaleny do procenta znamének spíše než složené závorky. Například `%Environment%/newblob.txt` pokud je cesta výstupní vazby objektu blob a hodnota nastavení `Environment` aplikace je `Development`, objekt blob se vytvoří v kontejneru. `Development`

Pokud je funkce spuštěna místně, hodnoty nastavení aplikace pocházejí ze souboru *local.settings.json.*

Všimněte `connection` si, že vlastnost aktivačníudálosti a vazby je zvláštní případ a automaticky řeší hodnoty jako nastavení aplikace, bez procenta znamének. 

Následující příklad je aktivační událost úložiště fronty `%input-queue-name%` Azure, která používá nastavení aplikace k definování fronty, na kterou se má aktivovat.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Stejný přístup můžete použít v knihovnách tříd:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Název souboru aktivační události

Pro `path` aktivační událost objektu blob může být vzorek, který umožňuje odkazovat na název aktivační objekt blob v jiných vazeb a kódu funkce. Vzorek může také obsahovat kritéria filtrování, která určují, které objekty BLOB mohou vyvolat vyvolání funkce.

Například v následující vazbě aktivační `path` události `sample-images/{filename}`objektu Blob je `filename`vzorek , který vytvoří výraz vazby s názvem :

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

Výraz `filename` pak lze použít ve výstupní vazbě k určení názvu vytvářeného objektu blob:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Kód funkce má přístup k `filename` této stejné hodnotě pomocí jako název parametru:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Stejná možnost použití vazebných výrazů a vzorů platí pro atributy v knihovnách tříd. V následujícím příkladu jsou parametry konstruktoru atributů stejné `path` hodnoty jako předchozí příklady *function.json:* 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

Můžete také vytvořit výrazy pro části názvu souboru. V následujícím příkladu je funkce spuštěna pouze u názvů souborů, které odpovídají vzoru:`anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

Další informace o použití výrazů a vzorků v řetězci cesty k blob najdete v tématu [odkaz na vazbu objektu blob úložiště](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Metadata aktivační události

Kromě datové části poskytované aktivační událost (například obsah zprávy fronty, která spustila funkci), mnoho aktivačních událostí poskytují další hodnoty metadat. Tyto hodnoty lze použít jako vstupní parametry v jazyce `context.bindings` C# a F# nebo vlastnosti na objekt v jazyce JavaScript. 

Aktivační událost úložiště fronty Azure například podporuje následující vlastnosti:

* QueueTrigger - aktivace obsahu zprávy, pokud je platný řetězec
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* Další visibletime
* PopReceipt

Tyto hodnoty metadat jsou přístupné ve vlastnostech souboru *function.json.* Předpokládejme například, že používáte aktivační událost fronty a zpráva fronty obsahuje název objektu blob, který chcete číst. V souboru *function.json* můžete `queueTrigger` použít vlastnost metadat `path` a vlastnost ve vlastnosti objektu blob, jak je znázorněno v následujícím příkladu:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Podrobnosti o vlastnostech metadat pro každou aktivační událost jsou popsány v odpovídajícím referenčním článku. Příklad naleznete v tématu [metadata aktivační události fronty](functions-bindings-storage-queue-trigger.md#message-metadata). Dokumentace je také k dispozici na **kartě Integrace** portálu v části **Dokumentace** pod oblastí konfigurace vazby.  

## <a name="json-payloads"></a>Datové části JSON

Pokud je spouštěcí datová část JSON, můžete odkazovat na jeho vlastnosti v konfiguraci pro jiné vazby ve stejné funkci a v kódu funkce.

Následující příklad ukazuje soubor *function.json* pro funkci webhooku, která přijímá název `{"BlobName":"HelloWorld.txt"}`objektu blob v JSON: . Vstupní vazba objektu blob přečte objekt blob a výstupní vazba HTTP vrátí obsah objektu blob v odpovědi HTTP. Všimněte si, že objekt blob vstupní vazby `BlobName` získá`"path": "strings/{BlobName}"`název objektu blob odkazem přímo na vlastnost ( )

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Aby to fungovalo v C# a F#, potřebujete třídu, která definuje pole, která mají být rekonstruována, jako v následujícím příkladu:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

V JavaScriptu se automaticky provádí deserializace JSON.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Tečka zápis

Pokud některé vlastnosti v datové části JSON jsou objekty s vlastnostmi, můžete odkazovat na ty přímo pomocí tečka zápisu. Předpokládejme například, že váš JSON vypadá takto:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Můžete odkazovat `FileName` přímo `BlobName.FileName`na jako . S tímto formátem JSON, `path` tady je to, co vlastnost v předchozím příkladu bude vypadat takto:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

V c#, budete potřebovat dvě třídy:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>Vytvoření identifikátorů GUID

Výraz `{rand-guid}` vazby vytvoří identifikátor GUID. Následující cesta objektu `function.json` blob v souboru vytvoří objekt blob s názvem *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Aktuální čas

Výraz `DateTime` vazby se `DateTime.UtcNow`překládá na . Následující cesta objektu `function.json` blob v souboru vytvoří objekt blob s názvem jako *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Vazba za běhu

V jazycích C# a dalších jazycích .NET můžete použít imperativní vzor vazby, na rozdíl od deklarativních vazeb v *function.json* a atributy. Imperativní vazba je užitečná, když je třeba vypočítat parametry vazby za běhu, nikoli za čas návrhu. Další informace naleznete v [odkazu pro vývojáře jazyka C#](functions-dotnet-class-library.md#binding-at-runtime) nebo [na odkaz na vývojáře skriptu jazyka C#](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Použití vrácené hodnoty funkce Azure](./functions-bindings-return-value.md)
