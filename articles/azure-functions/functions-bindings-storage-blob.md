---
title: Azure Blob storage vazby pro Azure Functions
description: Pochopit, jak používat Azure Blob storage triggerů a vazeb v Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Funkce Azure, funkce zpracování událostí, dynamické výpočetní architektura bez serveru
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/12/2018
ms.author: tdykstra
ms.openlocfilehash: 1d1cdee7373b949456032aff3c808cc233aaa1cc
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Blob storage vazby pro Azure Functions

Tento článek vysvětluje, jak pro práci s vazbami úložiště objektů Azure Blob v Azure Functions. Azure Functions podporuje aktivaci, vstup a výstup vazby pro objekty BLOB. Článek obsahuje oddíl pro každou vazbu:

* [Aktivační události objektu BLOB](#trigger)
* [Vstupní vazby objektů BLOB](#input)
* [BLOB výstup vazby](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Použijte aktivační událost mřížky INSTEAD úložiště objektů Blob pro účty úložiště pouze objekt blob, pro vysoce Škálované, nebo aby se zabránilo studený start zpožděním. Další informace najdete v tématu [aktivační událost](#trigger) části. 

## <a name="packages"></a>Balíčky

Vazby úložiště objektů Blob jsou součástí [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) balíček NuGet. Zdrojový kód pro balíček je v [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src) úložiště GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="trigger"></a>Trigger

Aktivační událost úložiště objektů Blob spustí funkce při zjištění do nové nebo aktualizované objektu blob. Obsahu objektu blob jsou uvedeny jako vstup do funkce.

[Aktivační událost mřížky](functions-bindings-event-grid.md) integrovanou podporu pro [blob události](../storage/blobs/storage-blob-event-overview.md) a lze také spustit funkci, při zjištění do nové nebo aktualizované objektu blob. Příklad, naleznete v části [změnit velikost bitové kopie s událostí mřížky](../event-grid/resize-images-on-storage-blob-upload-event.md) kurzu.

Použijte mřížky události INSTEAD úložiště objektů Blob pro následující scénáře:

* Účty úložiště jen objektů BLOB
* Měřítko
* Zpoždění studený start

### <a name="blob-only-storage-accounts"></a>Účty úložiště jen objektů BLOB

[Účty úložiště BLOB jen](../storage/common/storage-create-storage-account.md#blob-storage-accounts) jsou podporovány pro objekt blob vstup a výstup vazby, ale nikoli pro aktivační události objektu blob. Aktivační události objektu BLOB úložiště se vyžaduje účet úložiště pro obecné účely.

### <a name="high-scale"></a>Měřítko

Měřítko můžete volně definované jako kontejnery, které mají více než 100 000 objektů BLOB v nich nebo účty úložiště, které mají více než 100 aktualizace objektu blob za sekundu.

### <a name="cold-start-delay"></a>Zpoždění studený start

Pokud je vaše aplikace funkce plánu spotřeby, může být až 10 minut zpoždění při zpracování nové objekty BLOB, pokud aplikaci funkce přešel nečinnosti. Abyste se vyhnuli Tato prodleva studený start, můžete přepnout na plán služby App Service s povolenou funkci Always On nebo používat typ, který různých aktivační události.

### <a name="queue-storage-trigger"></a>Aktivace fronty úložiště

Kromě události mřížky Další alternativou k zpracování objektů blob je aktivační událost fronty úložiště, ale nemá žádné integrovanou podporu pro události se objektu blob. Je třeba vytvořit zprávy do fronty při vytváření nebo aktualizaci objekty BLOB. Pro příklad, který předpokládá můžete tento krok najdete [blob vstupní vazby příklad později v tomto článku](#input---example).

## <a name="trigger---example"></a>Aktivační událost – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Aktivační událost – příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) , zapíše do protokolu v případě, že objekt blob je přidán nebo aktualizován v `samples-workitems` kontejneru.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Řetězec `{name}` v cestě aktivační události objektu blob `samples-workitems/{name}` vytvoří [vazby výraz](functions-triggers-bindings.md#binding-expressions-and-patterns) používané v kódu funkce pro přístup k názvu souboru spouštěcí objekt blob. Další informace najdete v tématu [Blob vzory názvů](#trigger---blob-name-patterns) dále v tomto článku.

Další informace o `BlobTrigger` atributů najdete v tématu [aktivační událost – atributy](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Aktivační událost – příklad skriptu jazyka C#

Následující příklad ukazuje vazby v aktivační události objektu blob *function.json* souboru a [C# skript (.csx)](functions-reference-csharp.md) kód, který používá vazby. Funkce zapíše do protokolu v případě, že objekt blob je přidán nebo aktualizován v `samples-workitems` kontejneru.

Zde je vazba dat v *function.json* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Řetězec `{name}` v cestě aktivační události objektu blob `samples-workitems/{name}` vytvoří [vazby výraz](functions-triggers-bindings.md#binding-expressions-and-patterns) používané v kódu funkce pro přístup k názvu souboru spouštěcí objekt blob. Další informace najdete v tématu [Blob vzory názvů](#trigger---blob-name-patterns) dále v tomto článku.

Další informace o *function.json* vlastnosti souboru, najdete v článku [konfigurace](#trigger---configuration) část vysvětluje tyto vlastnosti.

Tady je C# kód skriptu, který vytvoří vazbu `Stream`:

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Tady je C# kód skriptu, který vytvoří vazbu `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Aktivační událost – příklad v jazyce JavaScript

Následující příklad ukazuje vazby v aktivační události objektu blob *function.json* souboru a [kódu jazyka JavaScript](functions-reference-node.md) používající vazby. Funkce zapíše do protokolu v případě, že objekt blob je přidán nebo aktualizován v `samples-workitems` kontejneru.

Tady je *function.json* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Řetězec `{name}` v cestě aktivační události objektu blob `samples-workitems/{name}` vytvoří [vazby výraz](functions-triggers-bindings.md#binding-expressions-and-patterns) používané v kódu funkce pro přístup k názvu souboru spouštěcí objekt blob. Další informace najdete v tématu [Blob vzory názvů](#trigger---blob-name-patterns) dále v tomto článku.

Další informace o *function.json* vlastnosti souboru, najdete v článku [konfigurace](#trigger---configuration) část vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes"></a>Aktivační událost – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte následující atributy ke konfiguraci aktivační události objektu blob:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

  Cesta řetězec, který označuje kontejner si chcete přehrát trvá konstruktoru atributu a volitelně [vzor názvu objektu blob](#trigger---blob-name-patterns). Tady je příklad:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Můžete nastavit `Connection` vlastnosti a určit účet úložiště, který chcete použít, jak je znázorněno v následujícím příkladu:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Úplný příklad najdete v tématu [aktivační událost - C# příklad](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje další možnost zadejte účet úložiště, který chcete použít. Konstruktor přebírá název nastavení aplikace, který obsahuje připojovací řetězec úložiště. Atribut lze použít na parametr, metoda nebo na úrovni třídy. Následující příklad ukazuje úroveň třídy a metody:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

Účet úložiště, který chcete použít, je určen v následujícím pořadí:

* `BlobTrigger` Atributu `Connection` vlastnost.
* `StorageAccount` Atribut použitý pro parametr stejné jako `BlobTrigger` atribut.
* `StorageAccount` Atribut použitý k funkci.
* `StorageAccount` Atribut použitý k třídě.
* Výchozí účet úložiště pro funkce aplikace (nastavení aplikace "AzureWebJobsStorage").

## <a name="trigger---configuration"></a>Aktivační událost - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `BlobTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | musí být nastavena na `blobTrigger`. Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure.|
|**direction** | neuvedeno | musí být nastavena na `in`. Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure. Výjimky jsou uvedeny v [využití](#trigger---usage) části. |
|**Jméno** | neuvedeno | Název proměnné, která představuje objektů blob v kódu funkce. | 
|**Cesta** | **BlobPath** |Kontejner pro monitorování.  Může být [vzor názvu objektu blob](#trigger-blob-name-patterns). | 
|**Připojení** | **Připojení** | Název nastavení aplikace, který obsahuje připojovací řetězec úložiště k použití pro tuto vazbu. Název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytku názvu sem. Například pokud nastavíte `connection` na "MyStorage" Functions runtime vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdný, funkce používá modul runtime výchozí úložiště připojovací řetězec v nastavení aplikace, který je pojmenován `AzureWebJobsStorage`.<br><br>Připojovací řetězec nesmí být pro účet úložiště pro obecné účely [účet pouze objekt blob úložiště](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační událost - využití

V jazyce C# a C# skript můžete použít následující typy parametrů pro spouštěcí objekt blob:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Serializovatelné jako JSON objektů POCO
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> vyžaduje "inout" vazba `direction` v *function.json* nebo `FileAccess.ReadWrite` v knihovně tříd jazyka C#.

Vytvoření vazby na `string`, `Byte[]`, nebo objektů POCO se doporučuje, pouze pokud má malou velikost objektu blob, jako celý objekt blob se obsah načten do paměti. Obecně platí, je vhodnější použít `Stream` nebo `CloudBlockBlob` typu. Další informace najdete v tématu [souběžnosti a použití paměti](#trigger---concurrency-and-memory-usage) dále v tomto článku.

V jazyce JavaScript, přístup k datům vstupního objektu blob pomocí `context.bindings.<name from function.json>`.

## <a name="trigger---blob-name-patterns"></a>Aktivační událost - vzory názvů objektů blob

Můžete zadat vzor názvu objektu blob v `path` vlastnost *function.json* nebo `BlobTrigger` konstruktoru atributu. Může být vzor názvů [výraz filtru nebo vazby](functions-triggers-bindings.md#binding-expressions-and-patterns). Následující části obsahují příklady.

### <a name="get-file-name-and-extension"></a>Získat název souboru a rozšíření

Následující příklad ukazuje, jak vytvořit vazbu na název souboru objektů blob a příponu samostatně:

```json
"path": "input/{blobname}.{blobextension}",
```
Pokud je název objektu blob *původní Blob1.txt*, hodnoty `blobname` a `blobextension` proměnné v kódu funkce jsou *původní Blob1* a *txt*.

### <a name="filter-on-blob-name"></a>Filtrování na název objektu blob

Následující příklad aktivace pouze na objekty BLOB v `input` kontejneru, která začínají řetězcem "původní-":

```json
"path": "input/original-{name}",
```
 
Pokud je název objektu blob *původní Blob1.txt*, hodnota `name` proměnná v kódu funkce `Blob1`.

### <a name="filter-on-file-type"></a>Filtrovat podle typu souboru

Následující příklad spustí jenom v *.png* soubory:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrovat složené závorky v názvech souborů

Hledání složené závorky v názvech souborů, vyhnuli složené závorky pomocí dvou složené závorky. Následující příklad filtry pro objekty BLOB, které mají složené závorky v názvu:

```json
"path": "images/{{20140101}}-{name}",
```

Pokud je název objektu blob  *{20140101}-soundfile.mp3*, `name` hodnotu proměnné v kód funkce je *soundfile.mp3*. 

## <a name="trigger---metadata"></a>Aktivační událost - metadat

Aktivační události objektu blob nabízí několik vlastností metadat. Tyto vlastnosti lze použít jako součást vazby výrazy v jiných vazby nebo jako parametry v kódu. Tyto hodnoty mají stejnou sémantiku jako [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) typu.

|Vlastnost  |Typ  |Popis  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Cesta k spouštěcí objekt blob.|
|`Uri`|`System.Uri`|Identifikátor URI objektu blob pro primární umístění.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Vlastnosti systému objektu blob. |
|`Metadata` |`IDictionary<string,string>`|Metadata definovaná uživatelem pro tento objekt blob.|

Například následující skript jazyka C# a JavaScript příklady protokolu cestu na spouštěcí objekt blob, včetně kontejneru:

```csharp
public static void Run(string myBlob, string blobTrigger, TraceWriter log)
{
    log.Info($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

## <a name="trigger---blob-receipts"></a>Aktivační událost – objekt blob potvrzení

Modulu runtime Azure Functions zajistí, že žádná funkce aktivační události objektu blob volala více než jednou pro stejný objekt blob nové nebo aktualizované. Chcete-li zjistit, pokud byla zpracována na verzi daného objektu blob, udržuje *blob potvrzení*.

Azure funkce úložiště objektů blob v kontejneru nazvaném potvrzení *azure. webové úlohy hostitelů* v účtu úložiště Azure pro aplikaci funkce (definované nastavení aplikace `AzureWebJobsStorage`). Potvrzení o objektu blob obsahuje následující informace:

* Funkci spouštěná ("*&lt;funkce název aplikace >*. Funkce.  *&lt;název funkce >*", například:"MyFunctionApp.Functions.CopyBlob")
* Název kontejneru
* Typ objektu blob ("BlockBlob" nebo "PageBlob")
* Název objektu blob
* Značky ETag (identifikátor objektu blob verze, například: "0x8D1DC6E70A277EF")

Pokud chcete vynutit opětovné zpracování objektu blob, odstranit objekt blob příjmu pro tento objekt blob z *azure. webové úlohy hostitelů* kontejneru ručně.

## <a name="trigger---poison-blobs"></a>Aktivační událost - poškozených objektů BLOB

Pokud funkci aktivační události objektu blob se nezdaří pro daný objekt blob Azure Functions opakovaných pokusů, které funkce celkem 5krát ve výchozím nastavení. 

Pokud selžou všechny 5 pokusů, Azure Functions přidá zprávu do fronty úložiště s názvem *webjobs. blobtrigger poison*. Zprávy ve frontě pro poškozených objekty BLOB je objekt JSON, který obsahuje následující vlastnosti:

* FunctionId (ve formátu  *&lt;funkce název aplikace >*. Funkce.  *&lt;název funkce >*)
* BlobType ("BlockBlob" nebo "PageBlob")
* ContainerName
* BlobName
* Značka ETag (identifikátor objektu blob verze, například: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Aktivační událost - souběžnosti a využití paměti

Aktivační události objektu blob používá fronty interně, takže se řídí maximální počet souběžných funkce volání [konfigurace fronty v host.json](functions-host-json.md#queues). Výchozí nastavení omezit souběžnosti 24 volání. Toto omezení platí samostatně pro každou funkci, která používá aktivační události objektu blob.

[Plán spotřeby](functions-scale.md#how-the-consumption-plan-works) omezuje funkce aplikace na jeden virtuální počítač (VM) k 1,5 GB paměti. Paměť se používá každá souběžně spuštěných instance funkce a funkce modulu runtime sám sebe. Pokud funkce aktivované blob načte celý objekt blob do paměti, maximální velikost paměti, použije jenom pro objekty BLOB tato funkce je 24 * velikost maximální objektu blob. Například funkce aplikace tři funkce aktivované objektů blob a výchozí nastavení podléhaly souběžných maximální jednotlivé virtuální počítače 3 * 24 = 72 funkce volání.

Funkce jazyka JavaScript načíst celý objekt blob do paměti a C# funkce udělat Pokud vytvořit vazbu na `string`, `Byte[]`, nebo objektů POCO.

## <a name="trigger---polling"></a>Aktivovat - dotazování

Pokud kontejner objektů blob, který je monitorován obsahuje více než 10 000 objektů BLOB, přihlaste se kontroly runtime funkce soubory, které chcete sledovat pro nové nebo změněné objekty BLOB. Tento proces může způsobit zpoždění. Funkce nemusí získat aktivuje až několik minut nebo déle po vytvoření objektu blob. Kromě toho [protokol úložiště jsou vytvořené na "best effort"](/rest/api/storageservices/About-Storage-Analytics-Logging) intervalech. Není zaručeno, že jsou zachyceny všechny události. Za určitých podmínek může být načteni protokoly. Pokud budete potřebovat rychlejší a spolehlivější blob zpracování, zvažte vytvoření [zprávu fronty](../storage/queues/storage-dotnet-how-to-use-queues.md) při vytváření objektu blob. Potom pomocí [aktivační událost fronty](functions-bindings-storage-queue.md) místo aktivační událost objektu blob ke zpracování objektu blob. Další možností je použít událost mřížky; Zobrazit kurz [automatické změně velikosti nahrán obrázky pomocí mřížky událostí](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input"></a>Vstup

Ke čtení objektů BLOB použijte vazbu vstupní úložiště objektů Blob.

## <a name="input---example"></a>Vstup – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#input---c-example)
* [C# skript (.csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>(Vstup) – příklad jazyka C#

Následující příklad je [C# funkce](functions-dotnet-class-library.md) používající aktivační procedury fronty a vazbu vstupního objektu blob. Zprávy ve frontě obsahuje název objektu blob a funkce protokoly velikost objektu blob.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```        

### <a name="input---c-script-example"></a>(Vstup) – příklad skriptu jazyka C#

<!--Same example for input and output. -->

Následující příklad ukazuje, objektů blob vstup a výstup vazeb v *function.json* souboru a [C# skript (.csx)](functions-reference-csharp.md) kód, který používá vazby. Funkce vytvoří kopii text objektu blob. Funkce se aktivuje zpráva fronty, který obsahuje název objektu blob pro kopírování. Nový objekt blob je s názvem *{originalblobname}-kopie*.

V *function.json* souboru `queueTrigger` vlastnost metadat slouží k určení názvu objektu blob v `path` vlastnosti:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>(Vstup) – příklad v jazyce JavaScript

<!--Same example for input and output. -->

Následující příklad ukazuje, objektů blob vstup a výstup vazeb v *function.json* souboru a [kódu jazyka JavaScript](functions-reference-node.md) používající vazby. Funkce vytvoří kopii objektu blob. Funkce se aktivuje zpráva fronty, který obsahuje název objektu blob pro kopírování. Nový objekt blob je s názvem *{originalblobname}-kopie*.

V *function.json* souboru `queueTrigger` vlastnost metadat slouží k určení názvu objektu blob v `path` vlastnosti:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input---attributes"></a>(Vstup) – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs).

Konstruktoru atributu má cestu k objektu blob a `FileAccess` parametr určující číst nebo zapisovat, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Můžete nastavit `Connection` vlastnosti a určit účet úložiště, který chcete použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Můžete použít `StorageAccount` atribut určete účet úložiště na úrovni třídy, metoda nebo parametr. Další informace najdete v tématu [aktivační událost – atributy](#trigger---attributes).

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Blob` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | musí být nastavena na `blob`. |
|**direction** | neuvedeno | musí být nastavena na `in`. Výjimky jsou uvedeny v [využití](#input---usage) části. |
|**Jméno** | neuvedeno | Název proměnné, která představuje objektů blob v kódu funkce.|
|**Cesta** |**BlobPath** | Cesta k objektu blob. | 
|**Připojení** |**Připojení**| Název nastavení aplikace, který obsahuje připojovací řetězec úložiště k použití pro tuto vazbu. Název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytku názvu sem. Například pokud nastavíte `connection` na "MyStorage" Functions runtime vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdný, funkce používá modul runtime výchozí úložiště připojovací řetězec v nastavení aplikace, který je pojmenován `AzureWebJobsStorage`.<br><br>Připojovací řetězec nesmí být pro účet úložiště pro obecné účely [účet pouze objekt blob úložiště](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|neuvedeno | **Přístup** | Určuje, zda jste se čtení nebo zápis. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>(Vstup) – použití

V jazyce C# a C# skript můžete použít následující typy parametrů pro vstupní vazbu objektu blob:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> vyžaduje "inout" vazba `direction` v *function.json* nebo `FileAccess.ReadWrite` v knihovně tříd jazyka C#.

Vytvoření vazby na `string` nebo `Byte[]` se doporučuje jenom když je velikost objektu blob malé, protože obsah celý objekt blob jsou načtena do paměti. Obecně platí, je vhodnější použít `Stream` nebo `CloudBlockBlob` typu. Další informace najdete v tématu [souběžnosti a použití paměti](#trigger---concurrency-and-memory-usage) výše v tomto článku.

V jazyce JavaScript, přístup k objektu blob dat pomocí `context.bindings.<name from function.json>`.

## <a name="output"></a>Výstup

Použijte vazby výstup úložiště objektů Blob k zápisu objektů BLOB.

## <a name="output---example"></a>Výstup – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Výstup – příklad jazyka C#

Následující příklad je [C# funkce](functions-dotnet-class-library.md) používající aktivační události objektu blob a dva výstupní vazby objektů blob. Funkce se aktivuje při vytváření objektu blob bitové kopie v *ukázkové obrázky* kontejneru. Vytvoří se kopie malé a střední velikost objektu blob bitové kopie. 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

### <a name="output---c-script-example"></a>Výstup – příklad skriptu jazyka C#

<!--Same example for input and output. -->

Následující příklad ukazuje, objektů blob vstup a výstup vazeb v *function.json* souboru a [C# skript (.csx)](functions-reference-csharp.md) kód, který používá vazby. Funkce vytvoří kopii text objektu blob. Funkce se aktivuje zpráva fronty, který obsahuje název objektu blob pro kopírování. Nový objekt blob je s názvem *{originalblobname}-kopie*.

V *function.json* souboru `queueTrigger` vlastnost metadat slouží k určení názvu objektu blob v `path` vlastnosti:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[Konfigurace](#output---configuration) část vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Výstup – příklad v jazyce JavaScript

<!--Same example for input and output. -->

Následující příklad ukazuje, objektů blob vstup a výstup vazeb v *function.json* soubor a [kód JavaScript] (funkce node.md odkaz), který používá vazby. Funkce vytvoří kopii objektu blob. Funkce se aktivuje zpráva fronty, který obsahuje název objektu blob pro kopírování. Nový objekt blob je s názvem *{originalblobname}-kopie*.

V *function.json* souboru `queueTrigger` vlastnost metadat slouží k určení názvu objektu blob v `path` vlastnosti:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[Konfigurace](#output---configuration) část vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="output---attributes"></a>Výstup – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs).

Konstruktoru atributu má cestu k objektu blob a `FileAccess` parametr určující číst nebo zapisovat, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Můžete nastavit `Connection` vlastnosti a určit účet úložiště, který chcete použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

Úplný příklad najdete v tématu [výstup - C# příklad](#output---c-example).

Můžete použít `StorageAccount` atribut určete účet úložiště na úrovni třídy, metoda nebo parametr. Další informace najdete v tématu [aktivační událost – atributy](#trigger---attributes).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Blob` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | musí být nastavena na `blob`. |
|**direction** | neuvedeno | Musí být nastavena na `out` pro vazbu výstup. Výjimky jsou uvedeny v [využití](#output---usage) části. |
|**Jméno** | neuvedeno | Název proměnné, která představuje objektů blob v kódu funkce.  Nastavte na `$return` Chcete-li funkce návratovou hodnotu.|
|**Cesta** |**BlobPath** | Cesta k objektu blob. | 
|**Připojení** |**Připojení**| Název nastavení aplikace, který obsahuje připojovací řetězec úložiště k použití pro tuto vazbu. Název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytku názvu sem. Například pokud nastavíte `connection` na "MyStorage" Functions runtime vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdný, funkce používá modul runtime výchozí úložiště připojovací řetězec v nastavení aplikace, který je pojmenován `AzureWebJobsStorage`.<br><br>Připojovací řetězec nesmí být pro účet úložiště pro obecné účely [účet pouze objekt blob úložiště](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|neuvedeno | **Přístup** | Určuje, zda jste se čtení nebo zápis. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – použití

V jazyce C# a C# skript můžete vázat na následující typy k zápisu objektů blob:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> vyžaduje "v" vazba `direction` v *function.json* nebo `FileAccess.Read` v knihovně tříd jazyka C#.

<sup>2</sup> vyžaduje "inout" vazba `direction` v *function.json* nebo `FileAccess.ReadWrite` v knihovně tříd jazyka C#.

Asynchronní funkcí, použití návratovou hodnotu nebo `IAsyncCollector` místo `out` parametr.

Vytvoření vazby na `string` nebo `Byte[]` se doporučuje jenom když je velikost objektu blob malé, protože obsah celý objekt blob jsou načtena do paměti. Obecně platí, je vhodnější použít `Stream` nebo `CloudBlockBlob` typu. Další informace najdete v tématu [souběžnosti a použití paměti](#trigger---concurrency-and-memory-usage) výše v tomto článku.


V jazyce JavaScript, přístup k objektu blob dat pomocí `context.bindings.<name from function.json>`.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba |  Referenční informace |
|---|---|
| Objekt blob | [Kódy chyb objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Objekt BLOB, tabulky, fronty |  [Kódy chyb úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objekt BLOB, tabulky, fronty |  [Řešení potíží](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejděte na rychlé spuštění, který používá aktivační událost úložiště objektů Blob](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)
