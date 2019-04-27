---
title: Výrazy vazeb Azure Functions a vzory
description: Zjistěte, jak vytvořit různé výrazy vazeb Azure Functions založené na běžných vzorů.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 03/20/2019
ms.author: v-junlch
ms.openlocfilehash: 0c1dbbae5e4be965f195b5ea4fc88b1bc5fb4f87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437867"
---
# <a name="azure-functions-binding-expression-patterns"></a>Služba Azure Functions vazby vzory výrazů

Jednou z nejúčinnějších funkcí [aktivačními událostmi a vazbami](./functions-triggers-bindings.md) je *výrazy vazeb*. V *function.json* soubor a parametry funkce a kód, můžete použít výrazy, které odkazují na hodnoty z různých zdrojů.

Většina výrazy jsou označeny obtékání ve složených závorkách. Například ve funkci aktivační událost fronty `{queueTrigger}` přeloží text zprávy fronty. Pokud `path` vlastností pro objekt blob Výstupní vazba je `container/{queueTrigger}` a aktivuje funkci zprávy fronty `HelloWorld`, objekt blob s názvem `HelloWorld` se vytvoří.

Typy výrazů vazby

* [Nastavení aplikace](#binding-expressions---app-settings)
* [Název souboru aktivační události](#trigger-file-name)
* [Aktivační událost metadat](#trigger-metadata)
* [Datové části JSON](#json-payloads)
* [Nový GUID](#create-guids)
* [Aktuální datum a čas](#current-time)

## <a name="binding-expressions---app-settings"></a>Výrazy vazby – nastavení aplikace

Jako osvědčený postup tajné klíče a připojovací řetězce by měla spravovat pomocí nastavení aplikace, nikoli konfigurační soubory. To omezuje přístup k těmto tajným kódům a umožňuje bezpečně ukládat soubory, jako *function.json* v úložištích veřejné zdrojového ovládacího prvku.

Nastavení aplikace jsou také užitečné, kdykoli budete chtít změnit konfiguraci na základě prostředí. Například v testovacím prostředí můžete monitorovat různé fronty nebo objektů blob v kontejneru úložiště.

Aplikace nastavení vazbové výrazy jsou označeny odlišně od jiných výrazech vazby: jsou zabaleny v procenta spíše než složených závorek. Například pokud je výstupní cesta vazby objektů blob `%Environment%/newblob.txt` a `Environment` hodnotu nastavení aplikace je `Development`, vytvoří se objekt blob v `Development` kontejneru.

Když funkce běží místně, hodnoty nastavení aplikace pocházet z *local.settings.json* souboru.

Všimněte si, že `connection` vlastnost triggerů a vazeb je zvláštní případ a automaticky vyřeší hodnoty jako nastavení aplikace, aniž by procenta. 

V následujícím příkladu je aktivační událost Azure Queue Storage, který používá nastavení aplikace `%input-queue-name%` k definování fronty pro aktivaci.

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

`path` Pro objekt Blob může být aktivační událost vzor, který umožňuje odkazovat na název spouštěcí objekt blob v jiných vazbách a funkce kódu. Vzor může také obsahovat kritéria filtrování, které určují, jaké objekty BLOB můžete aktivovat volání funkce.

V následující aktivační událost objektů Blob vazby, například `path` vzor je `sample-images/{filename}`, která vytvoří vazbu výraz s názvem `filename`:

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

Výraz `filename` pak lze v výstupní vazbu k určení názvu objektu blob vytváří:

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

Kód funkce má přístup k této stejnou hodnotu s použitím `filename` jako název parametru:

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

Stejné umožňuje používat výrazy vazeb a vzory se vztahuje na atributy v knihovnách tříd. V následujícím příkladu, parametry konstruktoru atributu jsou stejné `path` hodnoty jako u předchozí *function.json* příklady: 

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

Můžete také vytvořit výrazy částí názvu souboru, jako je například rozšíření. Další informace o tom, jak používat výrazy a vzorů v řetězci cesty objektu Blob najdete v tématu [odkazy vazby objektů blob úložiště](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Aktivační událost metadat

Řada triggerů kromě datová část poskytované aktivační události (jako je třeba obsah zprávy fronty, která aktivuje funkce), poskytují další metadata hodnoty. Tyto hodnoty slouží jako vstupní parametry v C# a F# nebo vlastnosti `context.bindings` objektu v jazyce JavaScript. 

Například aktivační událost Azure Queue storage podporuje následující vlastnosti:

* QueueTrigger - aktivací obsah zprávy, pokud se platný řetězec
* DequeueCount
* expirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

Tyto hodnoty metadat jsou k dispozici v *function.json* vlastnosti souboru. Předpokládejme například, můžete použít aktivační událost fronty a zpráva fronty obsahuje název objektu blob, který chcete číst. V *function.json* soubor, můžete použít `queueTrigger` metadat vlastnost v objektu blob `path` vlastnost, jak je znázorněno v následujícím příkladu:

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

Podrobnosti vlastnosti metadat pro jednotlivé aktivační události jsou popsané v příslušném článku odkaz. Příklad najdete v tématu [metadat aktivační událost fronty](functions-bindings-storage-queue.md#trigger---message-metadata). Je také k dispozici v dokumentaci **integrace** kartu na portálu v **dokumentaci** části oblasti Konfigurace vazby.  

## <a name="json-payloads"></a>Datové části JSON

Po triggeru datovou část JSON, můžete odkazovat na její vlastnosti v konfiguraci u jiných vazeb ve stejné funkci a v kódu funkce.

Následující příklad ukazuje *function.json* soubor funkce webhooku, která přijímá název objektu blob ve formátu JSON: `{"BlobName":"HelloWorld.txt"}`. Přečte objekt blob vstupní vazby objektů Blob a HTTP výstupní vazbu vrátí obsah objektů blob v odpovědi HTTP. Všimněte si, že vstupní vazby objektů Blob získá rekapitulací přímo na název objektu blob `BlobName` vlastnosti (`"path": "strings/{BlobName}"`)

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

Aby to fungovalo C# a F#, je třeba třídu, která definuje pole k deserializaci jako v následujícím příkladu:

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

V jazyce JavaScript probíhá automaticky deserializace JSON.

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

### <a name="dot-notation"></a>Zápisu s tečkou

Pokud některé vlastnosti ve vaší datové části JSON jsou objekty s vlastnostmi, mohou odkazovat na ty přímo pomocí zápisu s tečkou. Předpokládejme například, že vaše struktury JSON vypadá takto:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Je možné odkazovat přímo na `FileName` jako `BlobName.FileName`. V tomto formátu JSON, tady je co `path` vlastnost v předchozím příkladu vypadat nějak takto:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

V jazyce C# je třeba dvě třídy:

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

## <a name="create-guids"></a>Vytvořit GUID

`{rand-guid}` Vazby výraz vytvoří identifikátor GUID. Následující cesta objektu blob v `function.json` soubor vytvoří objekt blob s názvem jako *50710cb5 84b9 - 4d 87 9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

## <a name="current-time"></a>Aktuální čas

Vazbový výraz `DateTime` přeloží na `DateTime.UtcNow`. Následující cesta objektu blob v `function.json` soubor vytvoří objekt blob s názvem jako *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```
## <a name="binding-at-runtime"></a>Vazbu za běhu

V jazyce C# a jinými jazyky rozhraní .NET, můžete použít vzoru imperativní vazby, na rozdíl od deklarativní vazby v *function.json* a atributy. Imperativní vazby je užitečné, když parametry vazby musí vypočítat v době běhu spíše než návrhu. Další informace najdete v tématu [jazyka C# – reference pro vývojáře](functions-dotnet-class-library.md#binding-at-runtime) nebo [referenční informace pro vývojáře skriptů jazyka C#](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Používat návratovou hodnotu funkce Azure](./functions-bindings-return-value.md)

<!-- Update_Description: link update -->