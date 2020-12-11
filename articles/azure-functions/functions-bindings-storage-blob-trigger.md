---
title: Aktivační událost Azure Blob Storage pro Azure Functions
description: Naučte se, jak spustit funkci Azure Functions jako změny dat služby Azure Blob Storage.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: fd33ca4c5d637e31230d8c124fdb9ec7c71d2ba7
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094841"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Aktivační událost Azure Blob Storage pro Azure Functions

Trigger služby Blob Storage spustí funkci při zjištění nového nebo aktualizovaného objektu BLOB. Obsah objektu BLOB je uveden jako [vstup do funkce](./functions-bindings-storage-blob-input.md).

Aktivační událost Azure Blob Storage vyžaduje účet úložiště pro obecné účely. Jsou podporovány také účty úložiště verze 2 s [oborem názvů hierarchická](../storage/blobs/data-lake-storage-namespace.md) . Pokud chcete použít účet jen pro objekt blob, nebo pokud vaše aplikace má specializované potřeby, přečtěte si alternativy k použití této aktivační události.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](./functions-bindings-storage-blob.md).

## <a name="polling"></a>Cyklické dotazování

Cyklické dotazování funguje jako hybrid mezi kontrolou protokolů a spouštěním pravidelného prohledávání kontejnerů. Objekty BLOB se prohledávají ve skupinách po 10 000 s tokenem pokračování použitým mezi intervaly.

> [!WARNING]
> [Protokoly úložiště se navíc vytvářejí na základě nejlepšího úsilí](/rest/api/storageservices/About-Storage-Analytics-Logging) . Není zaručeno, že budou zachyceny všechny události. Za určitých podmínek můžou být protokoly zmeškané.
> 
> Pokud potřebujete rychlejší nebo spolehlivější zpracování objektů blob, zvažte vytvoření [zprávy fronty](../storage/queues/storage-dotnet-how-to-use-queues.md) při vytváření objektu BLOB. Pak použijte [aktivační událost Queue](functions-bindings-storage-queue.md) namísto triggeru objektu BLOB ke zpracování objektu BLOB. Další možností je použít Event Grid; Podívejte se na kurz [Automatizace změny velikosti nahraných imagí pomocí Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="alternatives"></a>Alternativy

### <a name="event-grid-trigger"></a>Aktivační událost Event Grid

[Aktivační událost Event Grid](functions-bindings-event-grid.md) také obsahuje integrovanou podporu pro [události objektů BLOB](../storage/blobs/storage-blob-event-overview.md). Místo triggeru služby Blob Storage použijte Event Grid pro následující scénáře:

- **Účty úložiště jen pro objekty blob**: [účty úložiště jen](../storage/common/storage-account-overview.md#types-of-storage-accounts) pro objekty BLOB se podporují pro vstupní a výstupní vazby objektů blob, ale ne pro triggery objektů BLOB.

- **Vysoké měřítko**: vysoké měřítko je možné volně definovat jako kontejnery, které mají v nich více než 100 000 objektů blob, nebo účty úložiště, které mají více než 100 aktualizací objektů blob za sekundu.

- **Minimalizace latence**: Pokud se vaše aplikace Function App nachází v plánu spotřeby, může při zpracování nových objektů BLOB trvat až 10 minut, pokud se aplikace Function App nečinný. Chcete-li se této latenci vyhnout, můžete přepnout na plán App Service s povolenou možnost vždy zapnuto. Můžete také použít [aktivační událost Event Grid](functions-bindings-event-grid.md) s vaším účtem služby Blob Storage. Příklad najdete v [kurzu Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Další informace o Event Grid příkladech najdete v tématu o [změně velikosti obrázku pomocí Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) kurzu.

### <a name="queue-storage-trigger"></a>Trigger služby Queue Storage

Dalším přístupem ke zpracování objektů BLOB je zápis zpráv ve frontách, které odpovídají vytvářeným nebo měněným objektům blob, a následnému spuštění zpracování pomocí [triggeru úložiště fronty](./functions-bindings-storage-queue.md) .

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která zapisuje protokol při přidání nebo aktualizaci objektu BLOB v `samples-workitems` kontejneru.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Řetězec `{name}` v cestě triggeru objektu BLOB `samples-workitems/{name}` vytvoří [výraz vazby](./functions-bindings-expressions-patterns.md) , který můžete použít v kódu funkce pro přístup k názvu souboru triggerového objektu BLOB. Další informace najdete v tématu [vzory názvů objektů BLOB](#blob-name-patterns) dále v tomto článku.

Další informace o atributu naleznete `BlobTrigger` v tématu [atributy a poznámky](#attributes-and-annotations).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje vazbu triggeru objektu BLOB v *function.js* souboru a kódu, který používá vazbu. Funkce zapisuje protokol, když se v kontejneru přidá nebo aktualizuje objekt BLOB `samples-workitems` [](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Tady jsou data vazby v *function.js* souboru:

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

Řetězec `{name}` v cestě triggeru objektu BLOB `samples-workitems/{name}` vytvoří [výraz vazby](./functions-bindings-expressions-patterns.md) , který můžete použít v kódu funkce pro přístup k názvu souboru triggerového objektu BLOB. Další informace najdete v tématu [vzory názvů objektů BLOB](#blob-name-patterns) dále v tomto článku.

Další informace o *function.js* vlastností souboru naleznete v části [Konfigurace](#configuration) vysvětlení těchto vlastností.

Tady je kód skriptu C#, který se váže k `Stream` :

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Tady je kód skriptu C#, který se váže k `CloudBlockBlob` :

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vazbu triggeru objektu BLOB v *function.js* souboru a [kódu JavaScriptu](functions-reference-node.md) , který používá vazbu. Funkce zapisuje protokol, když se v kontejneru přidá nebo aktualizuje objekt BLOB `samples-workitems` .

Tady je *function.js* souboru:

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

Řetězec `{name}` v cestě triggeru objektu BLOB `samples-workitems/{name}` vytvoří [výraz vazby](./functions-bindings-expressions-patterns.md) , který můžete použít v kódu funkce pro přístup k názvu souboru triggerového objektu BLOB. Další informace najdete v tématu [vzory názvů objektů BLOB](#blob-name-patterns) dále v tomto článku.

Další informace o *function.js* vlastností souboru naleznete v části [Konfigurace](#configuration) vysvětlení těchto vlastností.

Tady je kód JavaScriptu:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje vazbu triggeru objektu BLOB v *function.js* souboru a [kódu Pythonu](functions-reference-python.md) , který používá vazbu. Funkce zapisuje protokol, když se v kontejneru přidá nebo aktualizuje objekt BLOB `samples-workitems` [](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Tady je *function.js* souboru:

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

Řetězec `{name}` v cestě triggeru objektu BLOB `samples-workitems/{name}` vytvoří [výraz vazby](./functions-bindings-expressions-patterns.md) , který můžete použít v kódu funkce pro přístup k názvu souboru triggerového objektu BLOB. Další informace najdete v tématu [vzory názvů objektů BLOB](#blob-name-patterns) dále v tomto článku.

Další informace o *function.js* vlastností souboru naleznete v části [Konfigurace](#configuration) vysvětlení těchto vlastností.

Tady je kód Pythonu:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

Tato funkce zapisuje protokol, když se do kontejneru přidá nebo aktualizuje objekt BLOB `myblob` .

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

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte následující atributy ke konfiguraci triggeru objektu BLOB:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Konstruktor atributu přebírá řetězec cesty, který označuje, který kontejner se má sledovat, a volitelně také [vzor názvu objektu BLOB](#blob-name-patterns). Tady je příklad:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Vlastnost můžete nastavit `Connection` tak, aby určovala účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Úplný příklad najdete v části [příklad triggeru](#example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje jiný způsob určení účtu úložiště, který se má použít. Konstruktor převezme název nastavení aplikace, které obsahuje připojovací řetězec úložiště. Atribut lze použít na úrovni parametru, metody nebo třídy. Následující příklad ukazuje úroveň třídy a úroveň metody:

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

* `BlobTrigger` `Connection` Vlastnost atributu
* `StorageAccount`Atribut aplikovaný na stejný parametr jako `BlobTrigger` atribut.
* `StorageAccount`Atribut aplikovaný na funkci.
* `StorageAccount`Atribut aplikovaný na třídu.
* Výchozí účet úložiště pro aplikaci Function App (nastavení aplikace "AzureWebJobsStorage").

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

`@BlobTrigger`Atribut slouží k poskytnutí přístupu k objektu blob, který funkci aktivoval. Podrobnosti najdete v [příkladu triggeru](#example) .

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `BlobTrigger` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**textový** | neuvedeno | Musí být nastaven na hodnotu `blobTrigger` . Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal.|
|**směr** | neuvedeno | Musí být nastaven na hodnotu `in` . Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal. Výjimky jsou uvedeny v části [použití](#usage) . |
|**Jméno** | neuvedeno | Název proměnné, která představuje objekt BLOB v kódu funkce. |
|**dílčí** | **Blobpath cestou** |[Kontejner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) , který se má monitorovat  Může se jednat o [vzor názvu objektu BLOB](#blob-name-patterns). |
|**vázán** | **Připojení** | Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "AzureWebJobsMyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage` .<br><br>Připojovací řetězec musí být pro účet úložiště pro obecné účely, nikoli [účet Blob Storage](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup k datům objektů BLOB pomocí `context.bindings.<NAME>` , kde `<NAME>` se shoduje s hodnotou definovanou v *function.js*.

# <a name="python"></a>[Python](#tab/python)

Přístup k datům objektů BLOB prostřednictvím parametru zadaného jako [InputStream](/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Podrobnosti najdete v [příkladu triggeru](#example) .

# <a name="java"></a>[Java](#tab/java)

`@BlobTrigger`Atribut slouží k poskytnutí přístupu k objektu blob, který funkci aktivoval. Podrobnosti najdete v [příkladu triggeru](#example) .

---

## <a name="blob-name-patterns"></a>Vzory názvů objektů BLOB

Můžete určit vzor názvu objektu BLOB ve `path` vlastnosti v *function.jsna* nebo v `BlobTrigger` konstruktoru atributu. Vzor názvu může být [filtr nebo výraz vazby](./functions-bindings-expressions-patterns.md). V následujících částech jsou uvedeny příklady.

> [!TIP]
> Název kontejneru nemůže obsahovat překladač ve vzoru názvu.

### <a name="get-file-name-and-extension"></a>Získat název a příponu souboru

Následující příklad ukazuje, jak vytvořit nezávisle s názvem a příponou souboru objektů BLOB:

```json
"path": "input/{blobname}.{blobextension}",
```

Pokud je objekt BLOB pojmenovaný *original-Blob1.txt*, hodnoty `blobname` `blobextension` proměnných a v kódu funkce jsou *původní – Blob1* a *txt*.

### <a name="filter-on-blob-name"></a>Filtrovat podle názvu objektu BLOB

Následující příklad aktivuje pouze objekty BLOB v `input` kontejneru, které začínají řetězcem "původní-":

```json
"path": "input/original-{name}",
```

Pokud je název objektu BLOB *original-Blob1.txt*, hodnota `name` proměnné v kódu funkce je `Blob1.txt` .

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

Pokud je objekt BLOB pojmenovaný *{20140101}-soundfile.mp3*, `name` hodnota proměnné v kódu funkce je *soundfile.mp3*.

## <a name="metadata"></a>Metadata

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

V Pythonu nejsou metadata k dispozici.

# <a name="java"></a>[Java](#tab/java)

Metadata nejsou v jazyce Java k dispozici.

---

## <a name="blob-receipts"></a>Příjem objektů BLOB

Modul runtime Azure Functions zajišťuje, aby se žádná funkce triggeru objektu BLOB nevolala více než jednou pro stejný nový nebo aktualizovaný objekt BLOB. Aby bylo možné zjistit, zda daná verze objektu BLOB byla zpracována, uchovává *příjem objektů BLOB*.

Azure Functions ukládá příjem objektů BLOB v kontejneru s názvem *Azure-WebJobs – hostitelé* v účtu úložiště Azure pro vaši aplikaci Function App (definované nastavením aplikace `AzureWebJobsStorage` ). Příjem objektů BLOB obsahuje následující informace:

* Aktivovaná funkce (*&lt; název aplikace funkce>*. POZVYHLEDAT. *&lt; název funkce>*", například:" MyFunctionApp. Functions. CopyBlob ")
* Název kontejneru
* Typ objektu BLOB ("BlockBlob" nebo "PageBlob")
* Název objektu BLOB
* ETag (identifikátor verze objektu blob, například: "0x8D1DC6E70A277EF")

Pokud chcete vynutit opětovné zpracování objektu blob, odstraňte příjem objektů BLOB pro tento objekt BLOB z kontejneru *Azure-WebJobs – hostuje* kontejner ručně. I když se nemůžete znovu zajímat, je zaručeno, že dojde k pozdějšímu časovému okamžiku. Pro okamžité zpracování je možné aktualizovat *scaninfo* objekt BLOB v *Azure – WebJobs – Hosts/blobscaninfo* . Všechny objekty BLOB s časovým razítkem poslední změny, po jejichž uplynutí bude `LatestScan` vlastnost prohledávána znovu.

## <a name="poison-blobs"></a>Poškozené objekty blob

Pokud se funkce triggeru objektu BLOB pro daný objekt BLOB nezdařila, Azure Functions se ve výchozím nastavení funkce pokusí o celkem 5 časů.

Pokud dojde k selhání všech 5 pokusů, Azure Functions přidá zprávu do fronty úložiště s názvem *WebJobs-blobtrigger-otrav*. Maximální počet opakovaných pokusů lze konfigurovat. Stejné nastavení MaxDequeueCount se používá pro zpracování poškozeného objektu BLOB a zpracování zpráv z fronty otrav. Zpráva fronty pro poškozené objekty BLOB je objekt JSON, který obsahuje následující vlastnosti:

* FunctionId (v *&lt; názvu aplikace funkce Format>*. POZVYHLEDAT. *&lt; název funkce>*)
* BlobType ("BlockBlob" nebo "PageBlob")
* ContainerName
* BlobName
* ETag (identifikátor verze objektu blob, například: "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>Souběžnost a využití paměti

Trigger objektu BLOB používá interně frontu, takže maximální počet souběžných volání funkcí je řízený [konfigurací front v host.jszapnuté](functions-host-json.md#queues). Výchozí nastavení omezují souběžné navýšení na 24 volání. Toto omezení se vztahuje odděleně na jednotlivé funkce, které používají Trigger objektu BLOB.

[Plán spotřeby](functions-scale.md#how-the-consumption-and-premium-plans-work) omezuje aplikaci funkcí na jednom virtuálním počítači na 1,5 GB paměti. Paměť je používána každou souběžně spuštěnou instancí funkce a samotným modulem runtime Functions. Pokud funkce aktivovaná objektem BLOB načte celý objekt blob do paměti, maximální velikost paměti, kterou tato funkce používá jenom pro objekty blob, je 24 * maximální velikost objektu BLOB. Například aplikace funkcí se třemi funkcemi aktivovanými pro objekty BLOB a výchozími nastaveními může být maximální souběžnost na virtuálním počítači 3 * 24 = 72 volání funkcí.

Funkce JavaScriptu a Java načtou celý objekt blob do paměti a funkce jazyka C# to uděláte, když vytváříte vazby na `string` , nebo `Byte[]` .

## <a name="next-steps"></a>Další kroky

- [Při spuštění funkce číst data služby Blob Storage](./functions-bindings-storage-blob-input.md)
- [Zápis dat služby Blob Storage z funkce](./functions-bindings-storage-blob-output.md)
