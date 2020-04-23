---
title: Aktivační událost úložiště objektů blob Azure pro funkce Azure
description: Zjistěte, jak spustit funkci Azure jako změny dat úložiště objektů blob Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 61fbaf37577efdab0b147d437ae78fc4df0764cb
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084953"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Aktivační událost úložiště objektů blob Azure pro funkce Azure

Aktivační událost úložiště objektů blob spustí funkci při zjištění nového nebo aktualizovaného objektu blob. Obsah objektu blob jsou k dispozici jako [vstup do funkce](./functions-bindings-storage-blob-input.md).

Aktivační událost úložiště objektů blob Azure vyžaduje účet úložiště pro obecné účely. Chcete-li použít účet pouze pro objekt blob, nebo pokud vaše aplikace má specializované potřeby, zkontrolujte alternativy k použití této aktivační události.

Informace o nastavení a konfiguraci naleznete v [přehledu](./functions-bindings-storage-blob.md).

## <a name="alternatives"></a>Alternativy

### <a name="event-grid-trigger"></a>Aktivační událost Mřížka událostí

[Aktivační událost mřížky událostí má](functions-bindings-event-grid.md) také integrovanou podporu pro události objektů [blob](../storage/blobs/storage-blob-event-overview.md). Místo aktivační události úložiště objektů blob použijte místo aktivační události úložiště objektů blob pro následující scénáře:

- **Účty úložiště pouze pro objekt blob:** [Účty úložiště pouze pro objekt y objektů blob](../storage/common/storage-account-overview.md#types-of-storage-accounts) jsou podporované pro vkládání a výstupy objektů blob, ale ne pro aktivační události objektů blob.

- **Ve vysokém měřítku**: Vysoké škálování lze volně definovat jako kontejnery, které mají více než 100 000 objektů BLOB v nich nebo účty úložiště, které mají více než 100 aktualizací objektů blob za sekundu.

- **Minimalizace latence**: Pokud je vaše aplikace funkce v plánu Spotřeba, může být až 10minutové zpoždění při zpracování nových objektů BLOB, pokud aplikace funkce odešla nečinnosti. Chcete-li se této latence vyhnout, můžete přepnout na plán služby App Service s povolenou funkcí Always On. Aktivační [událost Event Grid](functions-bindings-event-grid.md) můžete použít také s účtem úložiště objektů Blob. Příklad naleznete v [kurzu Mřížka událostí](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Podívejte se na příklad [u změny velikosti obrázku pomocí mřížky událostí](../event-grid/resize-images-on-storage-blob-upload-event.md) v příkladu mřížky událostí.

### <a name="queue-storage-trigger"></a>Trigger služby Queue Storage

Dalším přístupem ke zpracování objektů BLOB je zápis zpráv fronty, které odpovídají objektům BLOB, které jsou vytvářeny nebo upravovány, a potom k zahájení zpracování použijte [aktivační událost úložiště fronty.](./functions-bindings-storage-queue.md)

## <a name="example"></a>Příklad

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která zapíše protokol při přidání `samples-workitems` nebo aktualizaci objektu blob v kontejneru.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Řetězec `{name}` v cestě `samples-workitems/{name}` aktivační události objektu blob vytvoří výraz [vazby,](./functions-bindings-expressions-patterns.md) který můžete použít v kódu funkce pro přístup k názvu souboru aktivačního objektu blob. Další informace naleznete v tématu [vzory názvů objektů blob](#blob-name-patterns) dále v tomto článku.

Další informace o `BlobTrigger` atributu naleznete v [tématu atributy a poznámky](#attributes-and-annotations).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje vazbu aktivační události objektu blob v souboru *function.json* a kódu, který používá vazbu. Funkce zapíše protokol při přidání nebo aktualizaci `samples-workitems` objektu blob v [kontejneru](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Zde jsou data vazby v souboru *function.json:*

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

Řetězec `{name}` v cestě `samples-workitems/{name}` aktivační události objektu blob vytvoří výraz [vazby,](./functions-bindings-expressions-patterns.md) který můžete použít v kódu funkce pro přístup k názvu souboru aktivačního objektu blob. Další informace naleznete v tématu [vzory názvů objektů blob](#blob-name-patterns) dále v tomto článku.

Další informace o vlastnostech souboru *function.json* naleznete v části [Konfigurace](#configuration) vysvětluje tyto vlastnosti.

Zde je C# skript kód, `Stream`který váže na :

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Zde je C# skript kód, `CloudBlockBlob`který váže na :

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vazbu aktivační události objektu blob v souboru *function.json* a [kód Jazyka JavaScript,](functions-reference-node.md) který vazbu používá. Funkce zapíše protokol při přidání nebo aktualizaci `samples-workitems` objektu blob v kontejneru.

Zde je *soubor function.json:*

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

Řetězec `{name}` v cestě `samples-workitems/{name}` aktivační události objektu blob vytvoří výraz [vazby,](./functions-bindings-expressions-patterns.md) který můžete použít v kódu funkce pro přístup k názvu souboru aktivačního objektu blob. Další informace naleznete v tématu [vzory názvů objektů blob](#blob-name-patterns) dále v tomto článku.

Další informace o vlastnostech souboru *function.json* naleznete v části [Konfigurace](#configuration) vysvětluje tyto vlastnosti.

Zde je kód JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje vazbu aktivační události objektu blob v souboru *function.json* a [kódu Pythonu,](functions-reference-python.md) který používá vazbu. Funkce zapíše protokol při přidání nebo aktualizaci `samples-workitems` objektu blob v [kontejneru](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Zde je *soubor function.json:*

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

Řetězec `{name}` v cestě `samples-workitems/{name}` aktivační události objektu blob vytvoří výraz [vazby,](./functions-bindings-expressions-patterns.md) který můžete použít v kódu funkce pro přístup k názvu souboru aktivačního objektu blob. Další informace naleznete v tématu [vzory názvů objektů blob](#blob-name-patterns) dále v tomto článku.

Další informace o vlastnostech souboru *function.json* naleznete v části [Konfigurace](#configuration) vysvětluje tyto vlastnosti.

Zde je kód Pythonu:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

Tato funkce zapíše protokol při přidání nebo `myblob` aktualizaci objektu blob v kontejneru.

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

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd Jazyka C#](functions-dotnet-class-library.md)použijte ke konfiguraci aktivační události objektu blob následující atributy:

* [Atribut blobTrigger](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Konstruktor atributu trvá řetězec cesty, který označuje kontejner sledovat a volitelně [vzor názvu objektu blob](#blob-name-patterns). Tady je příklad:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Vlastnost můžete `Connection` nastavit tak, aby určila účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Úplný příklad naleznete v [příkladu Trigger](#example).

* [Atribut účtu úložiště](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje další způsob, jak zadat účet úložiště, který se má použít. Konstruktor přebírá název nastavení aplikace, která obsahuje připojovací řetězec úložiště. Atribut lze použít na úrovni parametru, metody nebo třídy. Následující příklad ukazuje úroveň třídy a úroveň metody:

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

Účet úložiště, který se má použít, se určuje v následujícím pořadí:

* Vlastnost `BlobTrigger` atributu. `Connection`
* Atribut `StorageAccount` použitý na stejný parametr `BlobTrigger` jako atribut.
* Atribut `StorageAccount` použitý pro funkci.
* Atribut `StorageAccount` použitý pro třídu.
* Výchozí účet úložiště pro aplikaci funkce ("AzureWebJobsStorage" nastavení aplikace).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Atribut `@BlobTrigger` se používá k poskytnutí přístupu k objektu blob, který spustil funkci. Podrobnosti naleznete v [příkladu aktivační události.](#example)

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `BlobTrigger` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být `blobTrigger`nastavena na . Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal.|
|**direction** | neuvedeno | Musí být `in`nastavena na . Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal. Výjimky jsou uvedeny v části [použití.](#usage) |
|**Jméno** | neuvedeno | Název proměnné, která představuje objekt blob v kódu funkce. |
|**Cestu** | **BlobPath** |[Kontejner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) ke sledování.  Může se na mít [vzor názvu objektu blob](#blob-name-patterns). |
|**Připojení** | **Připojení** | Název nastavení aplikace, která obsahuje připojovací řetězec úložiště pro tuto vazbu. Pokud název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytek názvu zde. Například pokud nastavíte `connection` na "MyStorage", funkce runtime hledá nastavení aplikace s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdné, spustí se s funkcí, která použije výchozí `AzureWebJobsStorage`připojovací řetězec úložiště v nastavení aplikace s názvem .<br><br>Připojovací řetězec musí být pro účet úložiště pro obecné účely, nikoli [účet úložiště objektů Blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup k datům objektu blob pomocí `context.bindings.<NAME>` where where `<NAME>` odpovídá hodnotě definované v *souboru function.json*.

# <a name="python"></a>[Python](#tab/python)

Přístup k datům objektu blob prostřednictvím parametru zadaného jako [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Podrobnosti naleznete v [příkladu aktivační události.](#example)

# <a name="java"></a>[Java](#tab/java)

Atribut `@BlobTrigger` se používá k poskytnutí přístupu k objektu blob, který spustil funkci. Podrobnosti naleznete v [příkladu aktivační události.](#example)

---

## <a name="blob-name-patterns"></a>Vzory názvů objektů blob

Vzorek názvu objektu blob `path` můžete zadat ve vlastnosti *function.json* nebo v konstruktoru atributu. `BlobTrigger` Vzor názvu může být [výraz filtru nebo vazby](./functions-bindings-expressions-patterns.md). V následujících částech jsou uvedeny příklady.

### <a name="get-file-name-and-extension"></a>Získání názvu souboru a rozšíření

Následující příklad ukazuje, jak vázat na název souboru objektů blob a příponu samostatně:

```json
"path": "input/{blobname}.{blobextension}",
```

Pokud je objekt blob pojmenován *original-Blob1.txt* `blobname` `blobextension` , hodnoty a proměnné v kódu funkce jsou *původní Blob1* a *txt*.

### <a name="filter-on-blob-name"></a>Filtrování podle názvu objektu blob

Následující příklad se aktivuje pouze na `input` objektech BLOB v kontejneru, které začínají řetězcem "original-":

```json
"path": "input/original-{name}",
```

Pokud je název objektu blob *původní-Blob1.txt*, `name` hodnota `Blob1`proměnné v kódu funkce je .

### <a name="filter-on-file-type"></a>Filtrování podle typu souboru

Následující příklad se aktivuje pouze u souborů *PNG:*

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrování složených závorek v názvech souborů

Chcete-li hledat složené závorky v názvech souborů, utečte závorky pomocí dvou závorek. Následující příklad filtruje objekty BLOB, které mají v názvu složené závorky:

```json
"path": "images/{{20140101}}-{name}",
```

Pokud je objekt blob pojmenován `name` * {20140101}-soundfile.mp3*, hodnota proměnné v kódu funkce je *soundfile.mp3*.

## <a name="metadata"></a>Metadata

# <a name="c"></a>[C #](#tab/csharp)

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

Metadata nejsou v Pythonu dostupná.

# <a name="java"></a>[Java](#tab/java)

Metadata nejsou v jazyce Java k dispozici.

---

## <a name="blob-receipts"></a>Příjmy objektu blob

Runtime Azure Functions zajišťuje, že žádná funkce aktivační události objektu blob se volá více než jednou pro stejný nový nebo aktualizovaný objekt blob. Chcete-li zjistit, zda byla zpracována daná verze objektu blob, udržuje *příjemky objektů blob*.

Azure Functions ukládá příjmy z objektů blob v kontejneru s názvem *azure-webjobs-hosts* v `AzureWebJobsStorage`účtu úložiště Azure pro vaši funkční aplikaci (definovanou nastavením aplikace). Příjem k blob má následující informace:

* Spouštěná funkce*&lt; *(" název aplikace funkce>. Funkce. název>", například: "MyFunctionApp.Functions.CopyBlob") * &lt; *
* Název kontejneru
* Typ objektu blob ("BlockBlob" nebo "PageBlob")
* Název objektu blob
* ETag (identifikátor verze objektu blob, například: "0x8D1DC6E70A277EF")

Chcete-li vynutit opětovné zpracování objektu blob, odstraňte příjemka objektu blob pro tento objekt blob z kontejneru *azure-webjobs-hosts* ručně. Při opětovném zpracování nemusí dojít okamžitě, je zaručeno, že dojde později v čase. Chcete-li znovu okamžitě, *scaninfo* objekt blob v *azure-webjobs-hosts/blobscaninfo* lze aktualizovat. Všechny objekty BLOB s posledním `LatestScan` upraveným časovým razítkem po vlastnosti budou znovu zkontrolovány.

## <a name="poison-blobs"></a>Jedovaté kuličky

Když funkce aktivační události objektu blob selže pro daný objekt blob, Azure Functions opakuje, že funkce celkem 5 krát ve výchozím nastavení.

Pokud se nezdaří všech 5 pokusů, Azure Functions přidá zprávu do fronty úložiště s názvem *webjobs-blobtrigger-poison*. Maximální počet opakování je konfigurovatelný. Stejné nastavení MaxDequeueCount se používá pro zpracování objektu blob poison a zpracování zpráv fronty poison. Zpráva fronty pro poškozená objekty BLOB je objekt JSON, který obsahuje následující vlastnosti:

* FunctionId (v * &lt;názvu aplikace funkce *formátu>. Funkce. název funkce>) * &lt; *
* BlobType ("BlockBlob" nebo "PageBlob")
* ContainerName
* Název objektu BlobName
* ETag (identifikátor verze objektu blob, například "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>Souběžnost a využití paměti

Aktivační událost objektu blob používá frontu interně, takže maximální počet souběžných vyvolání funkcí je řízen [konfigurací front v souboru host.json](functions-host-json.md#queues). Výchozí nastavení omezit souběžnost na 24 vyvolání. Toto omezení platí samostatně pro každou funkci, která používá aktivační událost objektu blob.

[Plán spotřeba](functions-scale.md#how-the-consumption-and-premium-plans-work) omezuje aplikaci funkcí na jednom virtuálním počítači (VM) na 1,5 GB paměti. Paměť je používána každou souběžně spuštěnou instancí funkce a samotným runtime funkce. Pokud funkce spouštěná objektem blob načte celý objekt blob do paměti, maximální paměť používaná tuto funkcí pouze pro objekty BLOB je 24 * maximální velikost objektu blob. Například aplikace funkce se třemi funkcemi aktivovanými objektem blob a výchozím nastavením by měla maximální souběžnost na virtuální počítače 3*24 = 72 vyvolání funkce.

Funkce JavaScriptu a Jazyka Java načítají celý objekt blob do `string` `Byte[]`paměti a funkce jazyka C# to dělají, pokud se vážete na , , nebo POCO.

## <a name="polling"></a>Dotazování

Dotazování funguje jako hybrid mezi kontrolou protokolů a spuštěním periodických kontrol kontejnerů. Objekty BLOB jsou kontrolovány ve skupinách 10 000 najednou s tokenpokračování používané mezi intervaly.

> [!WARNING]
> Kromě toho [protokoly úložiště jsou vytvořeny na základě "nejlepší úsilí".](/rest/api/storageservices/About-Storage-Analytics-Logging) Neexistuje žádná záruka, že všechny události jsou zachyceny. Za určitých podmínek mohou být protokoly vynechány.
> 
> Pokud požadujete rychlejší nebo spolehlivější zpracování objektů blob, zvažte vytvoření [zprávy fronty](../storage/queues/storage-dotnet-how-to-use-queues.md) při vytváření objektu blob. Potom použijte [aktivační událost fronty](functions-bindings-storage-queue.md) namísto aktivační události objektu blob ke zpracování objektu blob. Další možností je použití event gridu; viz výukový program [Automatizovat velikost nahraných obrázků pomocí event gridu](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="next-steps"></a>Další kroky

- [Čtení dat úložiště objektů blob při spuštění funkce](./functions-bindings-storage-blob-input.md)
- [Zápis dat úložiště objektů blob z funkce](./functions-bindings-storage-blob-output.md)
