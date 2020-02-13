---
title: Vazby úložiště objektů BLOB v Azure pro Azure Functions
description: Seznamte se s použitím aktivačních událostí a vazeb služby Azure Blob Storage v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: 5113b59f314a40feb02485b3fdc99fe4df406d88
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168095"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Vazby úložiště objektů BLOB v Azure pro Azure Functions

Tento článek vysvětluje, jak pracovat s vazbami služby Azure Blob Storage v Azure Functions. Azure Functions podporuje vazby triggeru, vstupu a výstupu pro objekty blob. Článek obsahuje oddíl pro každou vazbu:

* [Trigger objektu BLOB](#trigger)
* [Vstupní vazba objektu BLOB](#input)
* [Výstupní vazba objektu BLOB](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Místo triggeru služby Blob Storage pro účty úložiště jen pro objekty blob, pro vysoké škálování nebo snížení latence použijte Trigger Event Grid. Další informace najdete v části [Trigger](#trigger) .

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby úložiště objektů BLOB jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

Vazby úložiště objektů BLOB jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) , verze 3. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Trigger služby Blob Storage spustí funkci při zjištění nového nebo aktualizovaného objektu BLOB. Obsah objektu BLOB je uveden jako vstup do funkce.

[Aktivační událost Event Grid](functions-bindings-event-grid.md) obsahuje integrovanou podporu pro [události objektů BLOB](../storage/blobs/storage-blob-event-overview.md) a můžete ji také použít ke spuštění funkce při zjištění nového nebo aktualizovaného objektu BLOB. Příklad najdete v kurzu [Změna velikosti obrázku pomocí Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) .

Místo triggeru služby Blob Storage použijte Event Grid pro následující scénáře:

* Účty úložiště Blob
* Vysoké měřítko
* Minimalizace latence

### <a name="blob-storage-accounts"></a>Účty úložiště Blob

[Účty BLOB Storage](../storage/common/storage-account-overview.md#types-of-storage-accounts) jsou podporované pro vstupní a výstupní vazby objektů blob, ale ne pro triggery objektů BLOB. Triggery služby Blob Storage vyžadují účet úložiště pro obecné účely.

### <a name="high-scale"></a>Vysoké měřítko

Vysoké měřítko je možné volně definovat jako kontejnery, které mají v nich více než 100 000 objektů blob, nebo účty úložiště, které mají více než 100 aktualizací objektů blob za sekundu.

### <a name="latency-issues"></a>Problémy s latencí

Pokud je vaše aplikace Functions v plánu spotřeby, může při zpracování nových objektů BLOB trvat až 10 minut, než se aplikace Function App nečinný. Chcete-li se této latenci vyhnout, můžete přepnout na plán App Service s povolenou možnost vždy zapnuto. Můžete také použít [aktivační událost Event Grid](functions-bindings-event-grid.md) s vaším účtem služby Blob Storage. Příklad najdete v [kurzu Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Trigger služby Queue Storage

Kromě Event Grid je další alternativa pro zpracování objektů BLOB ve frontě aktivační událost, ale nemá žádnou integrovanou podporu pro události objektů BLOB. Při vytváření nebo aktualizaci objektů BLOB byste museli vytvářet zprávy ve frontě. Příklad, který předpokládá, že jste to provedli, najdete v [příkladu vstupní vazby objektu BLOB dále v tomto článku](#input---example).

## <a name="trigger---example"></a>Aktivační události – příklad

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která zapisuje protokol při přidání nebo aktualizaci objektu BLOB v kontejneru `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Řetězec `{name}` v cestě triggeru objektu BLOB `samples-workitems/{name}` vytvoří [výraz vazby](./functions-bindings-expressions-patterns.md) , který můžete použít v kódu funkce pro přístup k názvu souboru triggerového objektu BLOB. Další informace najdete v tématu [vzory názvů objektů BLOB](#trigger---blob-name-patterns) dále v tomto článku.

Další informace o atributu `BlobTrigger` naleznete v tématu [Trigger-Attributes](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Následující příklad ukazuje vazbu triggeru objektu BLOB v souboru *Function. JSON* a kódu, který používá vazbu. Funkce zapíše protokol, když se do [kontejneru](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)`samples-workitems` přidá nebo aktualizuje objekt BLOB.

Tady jsou data vazby v souboru *Function. JSON* :

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

Řetězec `{name}` v cestě triggeru objektu BLOB `samples-workitems/{name}` vytvoří [výraz vazby](./functions-bindings-expressions-patterns.md) , který můžete použít v kódu funkce pro přístup k názvu souboru triggerového objektu BLOB. Další informace najdete v tématu [vzory názvů objektů BLOB](#trigger---blob-name-patterns) dále v tomto článku.

Další informace o vlastnostech souboru *Function. JSON* najdete v části [Konfigurace](#trigger---configuration) vysvětlení těchto vlastností.

Zde je C# kód skriptu, který se váže k `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Zde je C# kód skriptu, který se váže k `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vazbu triggeru objektu BLOB v souboru *Function. JSON* a [kódu JavaScriptu](functions-reference-node.md) , který používá vazbu. Funkce zapíše protokol, když se do kontejneru `samples-workitems` přidá nebo aktualizuje objekt BLOB.

Tady je soubor *Function. JSON* :

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

Řetězec `{name}` v cestě triggeru objektu BLOB `samples-workitems/{name}` vytvoří [výraz vazby](./functions-bindings-expressions-patterns.md) , který můžete použít v kódu funkce pro přístup k názvu souboru triggerového objektu BLOB. Další informace najdete v tématu [vzory názvů objektů BLOB](#trigger---blob-name-patterns) dále v tomto článku.

Další informace o vlastnostech souboru *Function. JSON* najdete v části [Konfigurace](#trigger---configuration) vysvětlení těchto vlastností.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Následující příklad ukazuje vazbu triggeru objektu BLOB v souboru *Function. JSON* a [kódu Pythonu](functions-reference-python.md) , který používá vazbu. Funkce zapíše protokol, když se do [kontejneru](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)`samples-workitems` přidá nebo aktualizuje objekt BLOB.

Tady je soubor *Function. JSON* :

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Řetězec `{name}` v cestě triggeru objektu BLOB `samples-workitems/{name}` vytvoří [výraz vazby](./functions-bindings-expressions-patterns.md) , který můžete použít v kódu funkce pro přístup k názvu souboru triggerového objektu BLOB. Další informace najdete v tématu [vzory názvů objektů BLOB](#trigger---blob-name-patterns) dále v tomto článku.

Další informace o vlastnostech souboru *Function. JSON* najdete v části [Konfigurace](#trigger---configuration) vysvětlení těchto vlastností.

Tady je kód Pythonu:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Tato funkce zapisuje protokol, když se do kontejneru `myblob` přidá nebo aktualizuje objekt BLOB.

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

---

## <a name="trigger---attributes"></a>Aktivační události – atributy

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte následující atributy ke konfiguraci triggeru objektu BLOB:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Konstruktor atributu přebírá řetězec cesty, který označuje, který kontejner se má sledovat, a volitelně také [vzor názvu objektu BLOB](#trigger---blob-name-patterns). Tady je příklad:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Vlastnost `Connection` můžete nastavit tak, aby určovala účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Úplný příklad najdete v části [příklad triggeru](#trigger---example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje jiný způsob určení účtu úložiště, který se má použít. Konstruktor převezme název nastavení aplikace, které obsahuje připojovací řetězec úložiště. Atribut je použít na parametr, metody nebo třídy úroveň. Následující příklad ukazuje úrovni třídy a metody:

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

Účet úložiště, který se má použít, se určuje v tomto pořadí:

* Vlastnost `Connection` atributu `BlobTrigger`.
* Atribut `StorageAccount` aplikovaný na stejný parametr jako atribut `BlobTrigger`.
* Atribut `StorageAccount` aplikovaný na funkci.
* Atribut `StorageAccount` aplikovaný na třídu.
* Výchozí účet úložiště pro aplikaci Function App (nastavení aplikace "AzureWebJobsStorage").

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="javatabjava"></a>[Java](#tab/java)

Atribut `@BlobTrigger` slouží k poskytnutí přístupu k objektu blob, který funkci aktivoval. Podrobnosti najdete v [příkladu triggeru](#trigger---example) .

---

## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `BlobTrigger`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | musí být nastavené na `blobTrigger`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | neuvedeno | musí být nastavené na `in`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. Výjimky jsou uvedeny v části [použití](#trigger---usage) . |
|**Jméno** | neuvedeno | Název proměnné, která představuje objekt BLOB v kódu funkce. |
|**dílčí** | **Blobpath cestou** |[Kontejner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) , který se má monitorovat  Může se jednat o [vzor názvu objektu BLOB](#trigger---blob-name-patterns). |
|**vázán** | **Vázán** | Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "MyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage`.<br><br>Připojovací řetězec musí být pro účet úložiště pro obecné účely, nikoli [účet Blob Storage](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační události – využití

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Přístup k datům objektů BLOB pomocí `context.bindings.<NAME>`, kde `<NAME>` odpovídá hodnotě definované v *Function. JSON*.

# <a name="pythontabpython"></a>[Python](#tab/python)

Přístup k datům objektů BLOB prostřednictvím parametru zadaného jako [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Podrobnosti najdete v [příkladu triggeru](#trigger---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

Atribut `@BlobTrigger` slouží k poskytnutí přístupu k objektu blob, který funkci aktivoval. Podrobnosti najdete v [příkladu triggeru](#trigger---example) .

---

## <a name="trigger---blob-name-patterns"></a>Trigger – vzory názvů objektů BLOB

Můžete zadat vzor názvu objektu BLOB ve vlastnosti `path` v *Function. JSON* nebo v konstruktoru `BlobTrigger` atributu. Vzor názvu může být [filtr nebo výraz vazby](./functions-bindings-expressions-patterns.md). V následujících částech jsou uvedeny příklady.

### <a name="get-file-name-and-extension"></a>Získat název a příponu souboru

Následující příklad ukazuje, jak vytvořit nezávisle s názvem a příponou souboru objektů BLOB:

```json
"path": "input/{blobname}.{blobextension}",
```

Pokud má objekt BLOB název *Original-Blob1. txt*, hodnoty `blobname` a `blobextension` proměnných v kódu funkce jsou *původní – Blob1* a *txt*.

### <a name="filter-on-blob-name"></a>Filtrovat podle názvu objektu BLOB

Následující příklad aktivuje pouze objekty BLOB v kontejneru `input`, který začíná řetězcem "původní-":

```json
"path": "input/original-{name}",
```

Pokud je název objektu BLOB *Original-Blob1. txt*, hodnota proměnné `name` v kódu funkce je `Blob1`.

### <a name="filter-on-file-type"></a>Filtrovat podle typu souboru

Následující příklad triggeruje pouze soubory *. png* :

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrovat složené závorky v názvech souborů

Chcete-li vyhledat složené závorky v názvech souborů, vydejte závorky pomocí dvou složených závorek. Následující příklad filtruje objekty blob, které mají složené závorky v názvu:

```json
"path": "images/{{20140101}}-{name}",
```

Pokud je objekt BLOB pojmenovaný *{20140101}-soundfile. mp3*, hodnota proměnné `name` v kódu funkce je *soundfile. mp3*.

## <a name="trigger---metadata"></a>Aktivační událost – metadata

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

V Pythonu nejsou metadata k dispozici.

# <a name="javatabjava"></a>[Java](#tab/java)

Metadata nejsou v jazyce Java k dispozici.

---

## <a name="trigger---blob-receipts"></a>Trigger – příjemy objektů BLOB

Modul runtime Azure Functions zajišťuje, aby se žádná funkce triggeru objektu BLOB nevolala více než jednou pro stejný nový nebo aktualizovaný objekt BLOB. Aby bylo možné zjistit, zda daná verze objektu BLOB byla zpracována, uchovává *příjem objektů BLOB*.

Azure Functions ukládá příjem objektů BLOB v kontejneru s názvem *Azure-WebJobs – hostitelé* v účtu úložiště Azure pro vaši aplikaci Function App (definované nastavením aplikace `AzureWebJobsStorage`). Příjem objektů BLOB obsahuje následující informace:

* Aktivovaná funkce ( *&lt;název aplikace Function app >* . POZVYHLEDAT. *&lt;název funkce >* ", například:" MyFunctionApp. Functions. CopyBlob ")
* Název kontejneru
* Typ objektu BLOB ("BlockBlob" nebo "PageBlob")
* Název objektu BLOB
* ETag (identifikátor verze objektu blob, například: "0x8D1DC6E70A277EF")

Pokud chcete vynutit opětovné zpracování objektu blob, odstraňte příjem objektů BLOB pro tento objekt BLOB z kontejneru *Azure-WebJobs – hostuje* kontejner ručně. I když se nemůžete znovu zajímat, je zaručeno, že dojde k pozdějšímu časovému okamžiku.

## <a name="trigger---poison-blobs"></a>Triggery – poškozené objekty blob

Pokud se funkce triggeru objektu BLOB pro daný objekt BLOB nezdařila, Azure Functions se ve výchozím nastavení funkce pokusí o celkem 5 časů.

Pokud dojde k selhání všech 5 pokusů, Azure Functions přidá zprávu do fronty úložiště s názvem *WebJobs-blobtrigger-otrav*. Zpráva fronty pro poškozené objekty BLOB je objekt JSON, který obsahuje následující vlastnosti:

* FunctionId (ve formátu *&lt;Function app > název aplikace*. POZVYHLEDAT. *název funkce&lt;>* )
* BlobType ("BlockBlob" nebo "PageBlob")
* ContainerName
* BlobName
* ETag (identifikátor verze objektu blob, například: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Trigger-Concurrency a využití paměti

Trigger objektu BLOB používá interně frontu, takže maximální počet souběžných volání funkcí je řízený [konfigurací front v Host. JSON](functions-host-json.md#queues). Výchozí nastavení omezují souběžné navýšení na 24 volání. Toto omezení se vztahuje odděleně na jednotlivé funkce, které používají Trigger objektu BLOB.

[Plán spotřeby](functions-scale.md#how-the-consumption-and-premium-plans-work) omezuje aplikaci funkcí na jednom virtuálním počítači na 1,5 GB paměti. Paměť je používána každou souběžně spuštěnou instancí funkce a samotným modulem runtime Functions. Pokud funkce aktivovaná objektem BLOB načte celý objekt blob do paměti, maximální velikost paměti, kterou tato funkce používá jenom pro objekty blob, je 24 * maximální velikost objektu BLOB. Například aplikace funkcí se třemi funkcemi aktivovanými pro objekty BLOB a výchozími nastaveními může být maximální souběžnost na virtuálním počítači 3 * 24 = 72 volání funkcí.

Funkce JavaScriptu a Java načtou celý objekt blob do paměti a C# funkce to uděláte, když vytvoříte vazby na `string`, `Byte[]`nebo POCO.

## <a name="trigger---polling"></a>Trigger – cyklické dotazování

Cyklické dotazování funguje jako hybrid mezi kontrolou protokolů a spouštěním pravidelného prohledávání kontejnerů. Objekty BLOB se prohledávají ve skupinách po 10 000 s tokenem pokračování použitým mezi intervaly.

> [!WARNING]
> [Protokoly úložiště se navíc vytvářejí na základě nejlepšího úsilí](/rest/api/storageservices/About-Storage-Analytics-Logging) . Není zaručeno, že budou zachyceny všechny události. Za určitých podmínek můžou být protokoly zmeškané.
> 
> Pokud potřebujete rychlejší nebo spolehlivější zpracování objektů blob, zvažte vytvoření [zprávy fronty](../storage/queues/storage-dotnet-how-to-use-queues.md) při vytváření objektu BLOB. Pak použijte [aktivační událost Queue](functions-bindings-storage-queue.md) namísto triggeru objektu BLOB ke zpracování objektu BLOB. Další možností je použít Event Grid; Podívejte se na kurz [Automatizace změny velikosti nahraných imagí pomocí Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Vstup

Pro čtení objektů BLOB použijte vstupní vazbu služby Blob Storage.

## <a name="input---example"></a>Vstupní příklad

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

V následujícím příkladu je [ C# funkce](functions-dotnet-class-library.md) , která používá Trigger fronty a vstupní vazbu objektu BLOB. Zpráva fronty obsahuje název objektu BLOB a funkce protokoluje velikost objektu BLOB.

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

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

<!--Same example for input and output. -->

Následující příklad ukazuje vstupní a výstupní vazby objektu BLOB v souboru *Function. JSON* a [ C# skriptu (. csx)](functions-reference-csharp.md) , který používá vazby. Funkce vytvoří kopii textového objektu BLOB. Funkce je aktivována zprávou fronty obsahující název objektu BLOB ke zkopírování. Nový objekt BLOB má název *{originalblobname} – kopírování*.

V souboru *Function. JSON* se vlastnost `queueTrigger` metadata používá k určení názvu objektu BLOB ve vlastnostech `path`:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

Tady je kód skriptu jazyka C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

Následující příklad ukazuje vstupní a výstupní vazby objektů BLOB v souboru *Function. JSON* a [kódu JavaScriptu](functions-reference-node.md) , který používá vazby. Funkce vytvoří kopii objektu BLOB. Funkce je aktivována zprávou fronty obsahující název objektu BLOB ke zkopírování. Nový objekt BLOB má název *{originalblobname} – kopírování*.

V souboru *Function. JSON* se vlastnost `queueTrigger` metadata používá k určení názvu objektu BLOB ve vlastnostech `path`:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

Tady je kód jazyka JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

Následující příklad ukazuje vstupní a výstupní vazby objektů BLOB v souboru *Function. JSON* a [kódu Pythonu](functions-reference-python.md) , který používá vazby. Funkce vytvoří kopii objektu BLOB. Funkce je aktivována zprávou fronty obsahující název objektu BLOB ke zkopírování. Nový objekt BLOB má název *{originalblobname} – kopírování*.

V souboru *Function. JSON* se vlastnost `queueTrigger` metadata používá k určení názvu objektu BLOB ve vlastnostech `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

Tady je kód Pythonu:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="javatabjava"></a>[Java](#tab/java)

Tato část obsahuje následující příklady:

* [Aktivační událost HTTP, vyhledat název objektu BLOB z řetězce dotazu](#http-trigger-look-up-blob-name-from-query-string)
* [Aktivační událost fronty, příjem názvu objektu BLOB ze zprávy ve frontě](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>Aktivační událost HTTP, vyhledat název objektu BLOB z řetězce dotazu

 Následující příklad ukazuje funkci jazyka Java, která používá anotaci `HttpTrigger` pro příjem parametru obsahujícího název souboru v kontejneru úložiště objektů BLOB. `BlobInput` anotace pak přečte soubor a předá jeho obsah funkci jako `byte[]`.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Aktivační událost fronty, příjem názvu objektu BLOB ze zprávy ve frontě

 Následující příklad ukazuje funkci jazyka Java, která používá anotaci `QueueTrigger` k přijetí zprávy obsahující název souboru v kontejneru úložiště objektů BLOB. `BlobInput` anotace pak přečte soubor a předá jeho obsah funkci jako `byte[]`.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@BlobInput` anotaci u parametrů, jejichž hodnota by pocházela z objektu BLOB.  Tuto poznámku lze použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí `Optional<T>`.

---

## <a name="input---attributes"></a>(Vstup) – atributy

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

V [ C# knihovně tříd](functions-dotnet-class-library.md)použijte [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atributu přebírá cestu k objektu BLOB a parametr `FileAccess`, který označuje čtení nebo zápis, jak je znázorněno v následujícím příkladu:

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

Vlastnost `Connection` můžete nastavit tak, aby určovala účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

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

Atribut `StorageAccount` lze použít k určení účtu úložiště na úrovni třídy, metody nebo parametru. Další informace najdete v tématu [Trigger – atributy](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="javatabjava"></a>[Java](#tab/java)

Atribut `@BlobInput` poskytuje přístup k objektu blob, který funkci aktivoval. Použijete-li pole bajtů s atributem, nastavte `dataType` na `binary`. Podrobnosti najdete v [příkladu vstupu](#input---example) .

---

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `Blob`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | musí být nastavené na `blob`. |
|**direction** | neuvedeno | musí být nastavené na `in`. Výjimky jsou uvedeny v části [použití](#input---usage) . |
|**Jméno** | neuvedeno | Název proměnné, která představuje objekt BLOB v kódu funkce.|
|**dílčí** |**Blobpath cestou** | Cesta k objektu BLOB |
|**vázán** |**Vázán**| Název nastavení aplikace, které obsahuje [připojovací řetězec úložiště](../storage/common/storage-configure-connection-string.md) , který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "MyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage`.<br><br>Připojovací řetězec musí být pro účet úložiště pro obecné účely, nikoli [účet úložiště jen pro objekty blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|neuvedeno | **Přístup** | Určuje, zda budete číst nebo zapisovat. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>(Vstup) – využití

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Přístup k datům objektů BLOB pomocí `context.bindings.<NAME>`, kde `<NAME>` odpovídá hodnotě definované v *Function. JSON*.

# <a name="pythontabpython"></a>[Python](#tab/python)

Přístup k datům objektů BLOB prostřednictvím parametru zadaného jako [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Podrobnosti najdete v [příkladu vstupu](#input---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

Atribut `@BlobInput` poskytuje přístup k objektu blob, který funkci aktivoval. Použijete-li pole bajtů s atributem, nastavte `dataType` na `binary`. Podrobnosti najdete v [příkladu vstupu](#input---example) .

---

## <a name="output"></a>Výstup

Použijte výstupní vazby služby Blob Storage k zápisu objektů BLOB.

## <a name="output---example"></a>Výstup – příklad

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

V následujícím příkladu je [ C# funkce](functions-dotnet-class-library.md) , která používá Trigger objektu BLOB a dvě výstupní vazby objektů BLOB. Funkce se aktivuje vytvořením objektu BLOB s obrázkem v kontejneru *Sample-images* . Vytvoří malé a střední velikosti kopií objektu BLOB imagí.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

}
```

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

<!--Same example for input and output. -->

Následující příklad ukazuje vstupní a výstupní vazby objektu BLOB v souboru *Function. JSON* a [ C# skriptu (. csx)](functions-reference-csharp.md) , který používá vazby. Funkce vytvoří kopii textového objektu BLOB. Funkce je aktivována zprávou fronty obsahující název objektu BLOB ke zkopírování. Nový objekt BLOB má název *{originalblobname} – kopírování*.

V souboru *Function. JSON* se vlastnost `queueTrigger` metadata používá k určení názvu objektu BLOB ve vlastnostech `path`:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#output---configuration) .

Tady je kód skriptu jazyka C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

Následující příklad ukazuje vstupní a výstupní vazby objektů BLOB v souboru *Function. JSON* a [kódu JavaScriptu](functions-reference-node.md) , který používá vazby. Funkce vytvoří kopii objektu BLOB. Funkce je aktivována zprávou fronty obsahující název objektu BLOB ke zkopírování. Nový objekt BLOB má název *{originalblobname} – kopírování*.

V souboru *Function. JSON* se vlastnost `queueTrigger` metadata používá k určení názvu objektu BLOB ve vlastnostech `path`:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#output---configuration) .

Tady je kód jazyka JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

Následující příklad ukazuje vstupní a výstupní vazby objektů BLOB v souboru *Function. JSON* a [kódu Pythonu](functions-reference-python.md) , který používá vazby. Funkce vytvoří kopii objektu BLOB. Funkce je aktivována zprávou fronty obsahující název objektu BLOB ke zkopírování. Nový objekt BLOB má název *{originalblobname} – kopírování*.

V souboru *Function. JSON* se vlastnost `queueTrigger` metadata používá k určení názvu objektu BLOB ve vlastnostech `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#output---configuration) .

Tady je kód Pythonu:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Tato část obsahuje následující příklady:

* [Aktivační událost HTTP s použitím OutputBinding](#http-trigger-using-outputbinding-java)
* [Aktivační událost fronty pomocí návratové hodnoty funkce](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Aktivační událost HTTP s použitím OutputBinding (Java)

 Následující příklad ukazuje funkci jazyka Java, která používá anotaci `HttpTrigger` pro příjem parametru obsahujícího název souboru v kontejneru úložiště objektů BLOB. `BlobInput` anotace pak přečte soubor a předá jeho obsah funkci jako `byte[]`. Poznámka `BlobOutput` se váže k `OutputBinding outputItem`, která je pak používána funkcí k zápisu obsahu vstupního objektu blob do konfigurovaného kontejneru úložiště.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Aktivační událost fronty pomocí návratové hodnoty funkce (Java)

 Následující příklad ukazuje funkci jazyka Java, která používá anotaci `QueueTrigger` k přijetí zprávy obsahující název souboru v kontejneru úložiště objektů BLOB. `BlobInput` anotace pak přečte soubor a předá jeho obsah funkci jako `byte[]`. Poznámka `BlobOutput` se váže k návratové hodnotě funkce, kterou modul runtime používá k zápisu obsahu vstupního objektu blob do nakonfigurovaného kontejneru úložiště.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 V [běhové knihovně funkcí jazyka Java](/java/api/overview/azure/functions/runtime)použijte u parametrů funkce anotaci `@BlobOutput`, jejichž hodnota by byla zapsána do objektu v úložišti objektů BLOB.  Typ parametru by měl být `OutputBinding<T>`, kde T je jakýkoliv nativní typ Java nebo POJO.

---

## <a name="output---attributes"></a>Výstup – atributy

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

V [ C# knihovně tříd](functions-dotnet-class-library.md)použijte [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atributu přebírá cestu k objektu BLOB a parametr `FileAccess`, který označuje čtení nebo zápis, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Vlastnost `Connection` můžete nastavit tak, aby určovala účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="javatabjava"></a>[Java](#tab/java)

Atribut `@BlobOutput` poskytuje přístup k objektu blob, který funkci aktivoval. Použijete-li pole bajtů s atributem, nastavte `dataType` na `binary`. Podrobnosti najdete v [příkladu výstupu](#output---example) .

---

Úplný příklad naleznete v tématu [výstup – příklad](#output---example).

Atribut `StorageAccount` lze použít k určení účtu úložiště na úrovni třídy, metody nebo parametru. Další informace najdete v tématu [Trigger – atributy](#trigger---attributes).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `Blob`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | musí být nastavené na `blob`. |
|**direction** | neuvedeno | Musí být nastavené na `out` pro výstupní vazbu. Výjimky jsou uvedeny v části [použití](#output---usage) . |
|**Jméno** | neuvedeno | Název proměnné, která představuje objekt BLOB v kódu funkce.  Nastavte na `$return` pro odkaz na návratovou hodnotu funkce.|
|**dílčí** |**Blobpath cestou** | Cesta k kontejneru objektů BLOB. |
|**vázán** |**Vázán**| Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "MyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage`.<br><br>Připojovací řetězec musí být pro účet úložiště pro obecné účely, nikoli [účet úložiště jen pro objekty blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|neuvedeno | **Přístup** | Určuje, zda budete číst nebo zapisovat. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

V JavaScriptu přístup k datům objektu BLOB pomocí `context.bindings.<name from function.json>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Parametry funkce můžete deklarovat jako následující typy pro zápis do úložiště objektů BLOB:

* Řetězce jako `func.Out(str)`
* Streamy jako `func.Out(func.InputStream)`

Podrobnosti najdete v [příkladu výstupu](#output---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

Atribut `@BlobOutput` poskytuje přístup k objektu blob, který funkci aktivoval. Použijete-li pole bajtů s atributem, nastavte `dataType` na `binary`. Podrobnosti najdete v [příkladu výstupu](#output---example) .

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba |  Referenční informace |
|---|---|
| Objekt blob | [Kódy chyb objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Objekt blob, tabulka, fronta |  [Kódy chyb úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objekt blob, tabulka, fronta |  [Řešení potíží](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Další kroky

* [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
