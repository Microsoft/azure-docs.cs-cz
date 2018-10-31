---
title: Vazby Azure Blob storage pro službu Azure Functions
description: Naučte se používat Azure Blob storage triggerů a vazeb ve službě Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: c9e6898d83e5bc1360bb5b1539b12bace8acdb3f
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251035"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Vazby Azure Blob storage pro službu Azure Functions

Tento článek vysvětluje, jak pracovat s vazby Azure Blob storage ve službě Azure Functions. Azure Functions podporuje aktivaci, vstupní a výstupní vazby pro objekty BLOB. Tento článek obsahuje oddíl pro každou vazbu:

* [Aktivační událost objektů BLOB](#trigger)
* [Vstupní vazby objektů BLOB](#input)
* [Výstupní vazby služby BLOB](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Pomocí aktivační události Event Grid místo aktivační událost objektů Blob storage pro účty Blob storage pro velké škálování nebo aby se vyhnuli prodlevám studený start. Další informace najdete v tématu [aktivační událost](#trigger) oddílu. 

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby služby Blob storage jsou k dispozici v [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) balíčku NuGet, verze 2.x. Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) úložiště GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Vazby služby Blob storage jsou k dispozici v [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) balíčku NuGet, verze 3.x. Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Aktivační událost objektů Blob storage se spustí funkce při zjištění nových nebo aktualizovaných objektu blob. Obsah objektu blob jsou k dispozici jako vstup do funkce.

[Trigger služby Event Grid](functions-bindings-event-grid.md) obsahuje integrovanou podporu [události služby blob](../storage/blobs/storage-blob-event-overview.md) a slouží také ke spuštění funkce při zjištění nových nebo aktualizovaných objektu blob. Příklad najdete v tématu [pomocí služby Event Grid – Změna velikosti obrázku](../event-grid/resize-images-on-storage-blob-upload-event.md) kurzu.

Pomocí služby Event Grid místo aktivační událost objektů Blob storage v následujících scénářích:

* Účty úložiště Blob
* Velké škály
* Minimalizace zpoždění studený start

### <a name="blob-storage-accounts"></a>Účty úložiště Blob

[Účty BLOB storage](../storage/common/storage-account-overview.md#types-of-storage-accounts) jsou podporovány pro objekt blob vstupní a výstupní vazby, ale nikoli pro triggery objektů blob. Triggery objektů BLOB storage se vyžaduje účet úložiště pro obecné účely.

### <a name="high-scale"></a>Velké škály

Velké škálování můžete volně definován jako kontejnery, které mají více než 100 000 objektů BLOB v nich nebo pro účty úložiště, které mají více než 100 aktualizace objektů blob za sekundu.

### <a name="cold-start-delay"></a>Studený start zpoždění

Pokud je vaše aplikace funkcí v plánu Consumption, může být až 10 minut zpoždění při zpracování nové objekty BLOB, pokud aplikace function app náramků RFID nečinnosti. Abyste zabránili tomuto zpoždění dochází studený start, můžete přepnout na plán služby App Service s povolenou funkci Always On nebo použít odlišný typ aktivační události.

### <a name="queue-storage-trigger"></a>Aktivační událost fronty úložiště

Kromě služby Event Grid Další možností pro zpracování objektů blob je aktivační událost fronty úložiště, ale nemá předdefinovanou podporu pro události služby blob. Je třeba vytvořit frontu zpráv při vytváření nebo aktualizaci objektů BLOB. Příklad, který předpokládá, že jste provedli, který najdete v tématu [blob vstupní vazby příkladu dále v tomto článku](#input---example).

## <a name="trigger---example"></a>Aktivační události – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Aktivační události – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) protokol, který zapíše, když se přidá nebo aktualizuje v objektu blob `samples-workitems` kontejneru.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Řetězec `{name}` v cestě aktivační událost objektů blob `samples-workitems/{name}` vytvoří [vazby výraz](functions-triggers-bindings.md#binding-expressions-and-patterns) , můžete použít v kódu funkce pro přístup k názvu souboru spouštěcí objekt blob. Další informace najdete v tématu [Blob vzory názvů](#trigger---blob-name-patterns) dále v tomto článku.

Další informace o `BlobTrigger` atributu naleznete v tématu [aktivační události – atributy](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Aktivační události – příklad skriptu jazyka C#

Následující příklad ukazuje vazby v aktivační událost objektů blob *function.json* souboru a [skript jazyka C# (.csx)](functions-reference-csharp.md) kód, který používá vazba. Tato funkce zapíše do protokolu při přidání nebo aktualizace v objektu blob `samples-workitems` kontejneru.

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

Řetězec `{name}` v cestě aktivační událost objektů blob `samples-workitems/{name}` vytvoří [vazby výraz](functions-triggers-bindings.md#binding-expressions-and-patterns) , můžete použít v kódu funkce pro přístup k názvu souboru spouštěcí objekt blob. Další informace najdete v tématu [Blob vzory názvů](#trigger---blob-name-patterns) dále v tomto článku.

Další informace o *function.json* vlastnosti souboru, najdete v článku [konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

Tady je C# kód skriptu, který vytvoří vazbu `Stream`:

```cs
public static void Run(Stream myBlob, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Tady je C# kód skriptu, který vytvoří vazbu `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Aktivační události – příklad v jazyce JavaScript

Následující příklad ukazuje vazby v aktivační událost objektů blob *function.json* souboru a [kódu jazyka JavaScript](functions-reference-node.md) , který používá vazba. Tato funkce zapíše do protokolu při přidání nebo aktualizace v objektu blob `samples-workitems` kontejneru.

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

Řetězec `{name}` v cestě aktivační událost objektů blob `samples-workitems/{name}` vytvoří [vazby výraz](functions-triggers-bindings.md#binding-expressions-and-patterns) , můžete použít v kódu funkce pro přístup k názvu souboru spouštěcí objekt blob. Další informace najdete v tématu [Blob vzory názvů](#trigger---blob-name-patterns) dále v tomto článku.

Další informace o *function.json* vlastnosti souboru, najdete v článku [konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

### <a name="trigger---java-example"></a>Aktivační události – příklad v jazyce Java

Následující příklad ukazuje aktivační událost objektů blob vazby ve *function.json* souboru a [kód v Javě](functions-reference-java.md) , který používá vazba. Tato funkce zapíše do protokolu při přidání nebo aktualizace v objektu blob `myblob` kontejneru.

Tady je *function.json* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Tady je kód Java:

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```


## <a name="trigger---attributes"></a>Aktivační události – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), můžete nakonfigurovat aktivační událost objektů blob následující atributy:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

  Konstruktor atributu přebírá řetězec cesty, která určuje kontejner, který chcete sledovat a volitelně [vzor názvu objektu blob](#trigger---blob-name-patterns). Tady je příklad:

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

  Kompletní příklad naleznete v tématu [Trigger – C# příklad](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje další způsob, jak zadat účet úložiště. Konstruktor přijme název nastavení aplikace, které obsahuje připojovací řetězec úložiště. Atribut je použít na parametr, metody nebo třídy úroveň. Následující příklad ukazuje úrovni třídy a metody:

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

Účet úložiště se určuje v tomto pořadí:

* `BlobTrigger` Atributu `Connection` vlastnost.
* `StorageAccount` Použije pro stejný parametr, jako `BlobTrigger` atribut.
* `StorageAccount` Použije pro funkci.
* `StorageAccount` Atribut aplikován třídu.
* Výchozí účet úložiště pro aplikaci function app (nastavení aplikace "AzureWebJobsStorage").

## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `BlobTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastaveno na `blobTrigger`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastaveno na `in`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. Výjimky jsou uvedeny v [využití](#trigger---usage) oddílu. |
|**Jméno** | neuvedeno | Název proměnné, která představuje objektů blob v kódu funkce. | 
|**Cesta** | **BlobPath** |Kontejner pro monitorování.  Může být [vzor názvu objektu blob](#trigger---blob-name-patterns). | 
|**připojení** | **připojení** | Název nastavení aplikace, které obsahuje připojovací řetězec úložiště má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat pouze zbytek název tady. Například pokud nastavíte `connection` na "MyStorage", modul runtime služby Functions vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdný, modul runtime služby Functions používá výchozí úložiště připojovací řetězec v nastavení aplikace, který je pojmenován `AzureWebJobsStorage`.<br><br>Připojovací řetězec nesmí být pro účet úložiště pro obecné účely [účtu úložiště objektů Blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační události – využití

V jazyce C# a skript jazyka C# můžete použít následující typy parametrů pro spouštěcí objekt blob:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* POCO serializovat jako JSON.
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> vyžaduje "vstup" vazbu `direction` v *function.json* nebo `FileAccess.ReadWrite` v knihovně tříd jazyka C#.

Pokud se pokusíte vytvořit vazbu na jeden z typů sadu SDK služby Storage a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](#azure-storage-sdk-version-in-functions-1x).

Vytvoření vazby na `string`, `Byte[]`, nebo POCO se doporučuje jenom, když je malá velikost objektu blob, jako celý objekt blob obsahu jsou načtena do paměti. Obecně je vhodnější použít `Stream` nebo `CloudBlockBlob` typu. Další informace najdete v tématu [souběžnosti a využití paměti](#trigger---concurrency-and-memory-usage) dále v tomto článku.

V jazyce JavaScript, přístup ke vstupnímu objektu blob data s využitím `context.bindings.<name from function.json>`.

## <a name="trigger---blob-name-patterns"></a>Aktivační události – vzory názvů objektů blob

Můžete zadat vzor názvu objektu blob v `path` vlastnost v *function.json* nebo `BlobTrigger` konstruktor atributu. Vzor názvů může být [vazbu filtr nebo výraz](functions-triggers-bindings.md#binding-expressions-and-patterns). Následující části obsahují příklady.

### <a name="get-file-name-and-extension"></a>Získání názvu souboru a přípony

Následující příklad ukazuje, jak vytvořit vazbu na název souboru objektu blob a příponu samostatně:

```json
"path": "input/{blobname}.{blobextension}",
```
Pokud je název objektu blob *původní Blob1.txt*, hodnoty `blobname` a `blobextension` proměnné v kódu funkce jsou *původní azureblob1* a *txt*.

### <a name="filter-on-blob-name"></a>Filtrovat podle názvu objektu blob

Následující příklad triggery pouze u objektů BLOB v `input` kontejneru, které začínají řetězcem "původní-":

```json
"path": "input/original-{name}",
```
 
Pokud je název objektu blob *původní Blob1.txt*, hodnota `name` proměnná ve funkci kódu je `Blob1`.

### <a name="filter-on-file-type"></a>Filtrovat podle typu souboru

Následující příklad spustí jenom v *.png* soubory:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrovat podle složené závorky v názvech souborů

Hledat složené závorky v názvech souborů, řídicí složené závorky pomocí dva složené závorky. Následující příklad filtry pro objekty BLOB, které mají v názvu složených závorek:

```json
"path": "images/{{20140101}}-{name}",
```

Pokud je název objektu blob  *{20140101}-soundfile.mp3*, `name` hodnotu proměnné v kódu funkce je *soundfile.mp3*. 

## <a name="trigger---metadata"></a>Aktivační události – metadat

Aktivační událost objektů blob poskytuje několik vlastností metadat. Tyto vlastnosti lze použít jako součást výrazy vazby v jiných vazbách nebo jako parametry v kódu. Tyto hodnoty mají stejnou sémantiku jako [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) typu.

|Vlastnost  |Typ  |Popis  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Cesta k spouštěcí objekt blob.|
|`Uri`|`System.Uri`|Identifikátor URI objektu blob pro primární umístění.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Systémové vlastnosti objektu blob. |
|`Metadata` |`IDictionary<string,string>`|Metadata definovaná uživatelem pro tento objekt blob.|

Protokolujte například, následující skript jazyka C# a JavaScript příklady cestu pro spouštěcí objekt blob, včetně kontejneru:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

## <a name="trigger---blob-receipts"></a>Aktivační události – potvrzení objektu blob

Modul runtime služby Azure Functions se zajistí, že žádné funkce pro aktivaci objektu blob volána více než jednou pro stejný objekt blob nové nebo aktualizované. Chcete-li zjistit, pokud verze daného objektu blob byla zpracována, udržuje *blob příjmy*.

Azure Functions úložiště objektů blob příjmy v kontejneru nazvaném *azure – webjobs – hostitelé* v účtu úložiště Azure pro vaši aplikaci function app (definované v nastavení aplikace `AzureWebJobsStorage`). Potvrzení objektu blob obsahuje následující informace:

* Aktivované – funkce ("*&lt;názvem aplikace function app >*. Funkce.  *&lt;název funkce >*", například:"MyFunctionApp.Functions.CopyBlob")
* Název kontejneru
* Typ objektu blob ("BlockBlob" nebo "PageBlob")
* Název objektu blob
* Značka ETag (identifikátor verze objektů blob, například: "0x8D1DC6E70A277EF")

Pokud chcete vynutit opětovné zpracování objektu blob, odstranit potvrzení tohoto objektu blob z objektu blob *azure – webjobs – hostitelé* kontejneru ručně.

## <a name="trigger---poison-blobs"></a>Aktivační události – počet poškozených objekty BLOB

Když selže funkce pro aktivaci objektu blob pro daný objekt blob, Azure Functions opakování této funkce celkem 5krát. ve výchozím nastavení. 

Pokud selžou i všechny 5 pokusech, Azure Functions přidá zprávu do fronty úložiště s názvem *webjobs. blobtrigger poison*. Pro objekty BLOB nezpracovatelná zpráva fronty je objekt JSON, který obsahuje následující vlastnosti:

* FunctionId (ve formátu  *&lt;názvem aplikace function app >*. Funkce.  *&lt;název funkce >*)
* BlobType ("BlockBlob" nebo "PageBlob")
* ContainerName
* BlobName
* Značka ETag (identifikátor verze objektů blob, například: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Aktivační události – souběžnosti a využití paměti

Aktivační událost objektů blob používá fronty interně, takže se řídí maximální počet souběžných funkce volání [konfigurace fronty v host.json](functions-host-json.md#queues). Výchozí nastavení omezení souběžnosti 24 volání. Toto omezení platí zvlášť pro každou funkci, která používá aktivační událost objektů blob.

[Plán consumption](functions-scale.md#how-the-consumption-plan-works) omezuje aplikace function app na jeden virtuální počítač (VM) až 1,5 GB paměti. Paměť se používá tak, že každá instance souběžně prováděným funkce a samotný modul runtime služby Functions. Pokud se objekt blob aktivuje funkci načte celý objekt blob do paměti, maximální velikost paměti používané, že fungují jenom pro objekty BLOB je 24 * velikost maximální objektu blob. Aplikace function app s tři funkce aktivované protokolem objektů blob a výchozí nastavení by mít třeba maximální jednotlivé virtuální počítače činí 3 * 24 = 72 funkce volání.

Funkce jazyka JavaScript načtěte celý objekt blob do paměti a funkcí jazyka C# provést, pokud svážete `string`, `Byte[]`, nebo POCO.

## <a name="trigger---polling"></a>Aktivovat - cyklického dotazování

Pokud kontejner objektů blob, který je monitorován obsahuje více než 10 000 objektů BLOB, soubory jak pro nové nebo změněné objekty BLOB protokolů kontroly runtime Functions. Tento proces může způsobit zpoždění. Funkce nemusí se aktivují až několik minut nebo i déle po vytvoření objektu blob. Kromě toho [protokoly úložiště se vytváří na "best effort"](/rest/api/storageservices/About-Storage-Analytics-Logging) základ. Není zaručeno, že se všechny události mají zachytávat. Za určitých podmínek mohou chybět protokoly. Pokud potřebujete rychlejší a spolehlivější blob zpracování, zvažte vytvoření [zpráva fronty](../storage/queues/storage-dotnet-how-to-use-queues.md) při vytváření objektu blob. Potom použijte [aktivační událost fronty](functions-bindings-storage-queue.md) místo aktivační událost objektů blob ke zpracování objektu blob. Další možností je použití služby Event Grid; Projděte si kurz [automatizace změny velikosti nahraných obrázků s využitím služby Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input"></a>Vstup

Čtení objektů BLOB pomocí vstupní vazby objektů Blob úložiště.

## <a name="input---example"></a>Input – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#input---c-example)
* [C# skript (.csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example"></a>(Vstup) – příklad v jazyce C#

V následujícím příkladu je [funkce jazyka C#](functions-dotnet-class-library.md) , která používá aktivační událost fronty a vazbu vstupnímu objektu blob. Queue zpráva obsahuje název objektu blob a protokoly funkce velikost objektu blob.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```        

### <a name="input---c-script-example"></a>(Vstup) – příklad skriptu jazyka C#

<!--Same example for input and output. -->

Následující příklad ukazuje blob vstupní a výstupní vazby *function.json* souboru a [skript jazyka C# (.csx)](functions-reference-csharp.md) kód, který používá vazby. Funkce vytvoří kopii tohoto objektu blob text. Funkce aktivované zpráv fronty, který obsahuje název objektu blob kopírování. Nový objekt blob má název *{originalblobname}-kopie*.

V *function.json* souboru `queueTrigger` vlastnost metadat se používá k určení názvu objektu blob v `path` vlastnosti:

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

[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>(Vstup) – příklad v jazyce JavaScript

<!--Same example for input and output. -->

Následující příklad ukazuje blob vstupní a výstupní vazby *function.json* souboru a [kódu jazyka JavaScript](functions-reference-node.md) vazby, který používá. Funkce vytvoří kopii tohoto objektu blob. Funkce aktivované zpráv fronty, který obsahuje název objektu blob kopírování. Nový objekt blob má název *{originalblobname}-kopie*.

V *function.json* souboru `queueTrigger` vlastnost metadat se používá k určení názvu objektu blob v `path` vlastnosti:

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

[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="input---java-example"></a>(Vstup) – příklad v jazyce Java

V následujícím příkladu je funkce Java, která používá aktivační událost fronty a vazbu vstupnímu objektu blob. Queue zpráva obsahuje název objektu blob a protokoly funkce velikost objektu blob.

```java
@FunctionName("getBlobSize")
@StorageAccount("AzureWebJobsStorage")
public void blobSize(@QueueTrigger(name = "filename",  queueName = "myqueue-items") String filename,
                    @BlobInput(name = "file", dataType = "binary", path = "samples-workitems/{queueTrigger") byte[] content,
       final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
 }
 ```

  V [Java funkce knihovny prostředí runtime](/java/api/overview/azure/functions/runtime), použijte `@BlobInput` poznámku o parametrech, jehož hodnota by pocházejí z objektu blob.  Tato poznámka je možné s nativní typy v jazyce Java, objektů Pojo nebo s povolenou hodnotou Null hodnoty pomocí `Optional<T>`. 


## <a name="input---attributes"></a>(Vstup) – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atributu přijímá cestu k objektu blob a `FileAccess` parametr určující pro čtení nebo zápisu, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Můžete nastavit `Connection` vlastnosti a určit účet úložiště, který chcete použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Můžete použít `StorageAccount` atribut k určení účtu úložiště na úrovni třídy, metody nebo parametr. Další informace najdete v tématu [aktivační události – atributy](#trigger---attributes).

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Blob` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastaveno na `blob`. |
|**direction** | neuvedeno | Musí být nastaveno na `in`. Výjimky jsou uvedeny v [využití](#input---usage) oddílu. |
|**Jméno** | neuvedeno | Název proměnné, která představuje objektů blob v kódu funkce.|
|**Cesta** |**BlobPath** | Cesta k objektu blob. | 
|**připojení** |**připojení**| Název nastavení aplikace, které obsahuje připojovací řetězec úložiště má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat pouze zbytek název tady. Například pokud nastavíte `connection` na "MyStorage", modul runtime služby Functions vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdný, modul runtime služby Functions používá výchozí úložiště připojovací řetězec v nastavení aplikace, který je pojmenován `AzureWebJobsStorage`.<br><br>Připojovací řetězec nesmí být pro účet úložiště pro obecné účely [účtu úložiště objektů Blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|neuvedeno | **Přístup** | Označuje, zda jste se čtení či zápis. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>(Vstup) – využití

V jazyce C# a skript jazyka C# můžete použít následující typy parametrů pro vstupní vazby objektů blob:

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

<sup>1</sup> vyžaduje "vstup" vazbu `direction` v *function.json* nebo `FileAccess.ReadWrite` v knihovně tříd jazyka C#.

Pokud se pokusíte vytvořit vazbu na jeden z typů sadu SDK služby Storage a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](#azure-storage-sdk-version-in-functions-1x).

Vytvoření vazby na `string` nebo `Byte[]` se doporučuje jen pokud je velikost objektu blob malé, protože obsah celý objekt blob se načtou do paměti. Obecně je vhodnější použít `Stream` nebo `CloudBlockBlob` typu. Další informace najdete v tématu [souběžnosti a využití paměti](#trigger---concurrency-and-memory-usage) dříve v tomto článku.

V jazyce JavaScript, přístup k data objektů blob s využitím `context.bindings.<name from function.json>`.

## <a name="output"></a>Výstup

Vazby výstupu úložiště objektů Blob použijte k zápisu objektů BLOB.

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Výstup – příklad v jazyce C#

V následujícím příkladu je [funkce jazyka C#](functions-dotnet-class-library.md) , který používá aktivační událost objektů blob a dvě výstupní vazby objektů blob. Funkce se aktivuje při vytvoření objektu blob obrázku ve *ukázkové obrázky* kontejneru. Vytvoří malé a střední zkopíruje objekt blob image. 

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

Následující příklad ukazuje blob vstupní a výstupní vazby *function.json* souboru a [skript jazyka C# (.csx)](functions-reference-csharp.md) kód, který používá vazby. Funkce vytvoří kopii tohoto objektu blob text. Funkce aktivované zpráv fronty, který obsahuje název objektu blob kopírování. Nový objekt blob má název *{originalblobname}-kopie*.

V *function.json* souboru `queueTrigger` vlastnost metadat se používá k určení názvu objektu blob v `path` vlastnosti:

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

[Konfigurace](#output---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Výstup – příklad v jazyce JavaScript

<!--Same example for input and output. -->

Následující příklad ukazuje blob vstupní a výstupní vazby *function.json* souboru a [kódu jazyka JavaScript](functions-reference-node.md) vazby, který používá. Funkce vytvoří kopii tohoto objektu blob. Funkce aktivované zpráv fronty, který obsahuje název objektu blob kopírování. Nový objekt blob má název *{originalblobname}-kopie*.

V *function.json* souboru `queueTrigger` vlastnost metadat se používá k určení názvu objektu blob v `path` vlastnosti:

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

[Konfigurace](#output---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="output---java-example"></a>Výstup – příklad v jazyce Java

Následující příklad ukazuje blob vstupní a výstupní vazby ve funkci jazyka Java. Funkce vytvoří kopii tohoto objektu blob text. Funkce aktivované zpráv fronty, který obsahuje název objektu blob kopírování. Nový objekt blob má název {originalblobname}-kopie

```java
@FunctionName("copyTextBlob")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "target", path = "samples-workitems/{queueTrigger}-Copy")
public String blobCopy(
    @QueueTrigger(name = "filename", queueName = "myqueue-items") String filename,
    @BlobInput(name = "source", path = "samples-workitems/{queueTrigger}") String content ) {
      return content;
 }
 ```

 V [Java funkce knihovny prostředí runtime](/java/api/overview/azure/functions/runtime), použijte `@BlobOutput` Poznámka k parametrům funkce, jehož hodnota bude zapsána do objektu v úložišti objektů blob.  Typ parametru by měl být `OutputBinding<T>`, kde T je libovolný Java nativní objekt POJO.


## <a name="output---attributes"></a>Výstup – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atributu přijímá cestu k objektu blob a `FileAccess` parametr určující pro čtení nebo zápisu, jak je znázorněno v následujícím příkladu:

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

Kompletní příklad naleznete v tématu [výstup – příklad v jazyce C#](#output---c-example).

Můžete použít `StorageAccount` atribut k určení účtu úložiště na úrovni třídy, metody nebo parametr. Další informace najdete v tématu [aktivační události – atributy](#trigger---attributes).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Blob` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastaveno na `blob`. |
|**direction** | neuvedeno | Musí být nastaveno na `out` pro výstupní vazbu. Výjimky jsou uvedeny v [využití](#output---usage) oddílu. |
|**Jméno** | neuvedeno | Název proměnné, která představuje objektů blob v kódu funkce.  Nastavte na `$return` tak, aby odkazovaly návratovou hodnotu funkce.|
|**Cesta** |**BlobPath** | Cesta k objektu blob. | 
|**připojení** |**připojení**| Název nastavení aplikace, které obsahuje připojovací řetězec úložiště má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat pouze zbytek název tady. Například pokud nastavíte `connection` na "MyStorage", modul runtime služby Functions vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdný, modul runtime služby Functions používá výchozí úložiště připojovací řetězec v nastavení aplikace, který je pojmenován `AzureWebJobsStorage`.<br><br>Připojovací řetězec nesmí být pro účet úložiště pro obecné účely [účtu úložiště objektů Blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|neuvedeno | **Přístup** | Označuje, zda jste se čtení či zápis. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

V jazyce C# a skript jazyka C# můžete vázat na následující typy k zápisu objektů blob:

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

<sup>1</sup> vyžaduje "v" vazby `direction` v *function.json* nebo `FileAccess.Read` v knihovně tříd jazyka C#. Můžete však použít objekt kontejneru, který modul runtime poskytuje k zápisu operace, jako je ukládání objektů BLOB do kontejneru.

<sup>2</sup> vyžaduje "vstup" vazbu `direction` v *function.json* nebo `FileAccess.ReadWrite` v knihovně tříd jazyka C#.

Pokud se pokusíte vytvořit vazbu na jeden z typů sadu SDK služby Storage a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](#azure-storage-sdk-version-in-functions-1x).

V asynchronních funkcí, použijte vrácenou hodnotu nebo `IAsyncCollector` místo `out` parametru.

Vytvoření vazby na `string` nebo `Byte[]` se doporučuje jen pokud je velikost objektu blob malé, protože obsah celý objekt blob se načtou do paměti. Obecně je vhodnější použít `Stream` nebo `CloudBlockBlob` typu. Další informace najdete v tématu [souběžnosti a využití paměti](#trigger---concurrency-and-memory-usage) dříve v tomto článku.


V jazyce JavaScript, přístup k data objektů blob s využitím `context.bindings.<name from function.json>`.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba |  Referenční informace |
|---|---|
| Objekt blob | [Kódy chyb objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Objekt BLOB, tabulky, fronty |  [Kódy chyb úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objekt BLOB, tabulky, fronty |  [Řešení potíží](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Další postup

* [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
