---
title: Výstupní vazba úložiště azure blob pro funkce Azure
description: Zjistěte, jak poskytovat data úložiště objektů blob Azure do funkce Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: c6e15c9a99a78f0f3637f718b35462fe49fd5ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277242"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Výstupní vazba úložiště azure blob pro funkce Azure

Výstupní vazba umožňuje upravit a odstranit data úložiště objektů blob ve funkci Azure.

Informace o nastavení a konfiguraci naleznete v [přehledu](./functions-bindings-storage-blob.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad je [c# funkce,](functions-dotnet-class-library.md) která používá aktivační událost objektu blob a dvě výstupní vazby objektu blob. Funkce je vyvolána vytvořením objektu blob bitové kopie v kontejneru *ukázkových bitových kopií.* Vytvoří malé a střední kopie objektu blob obrázku.

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

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

<!--Same example for input and output. -->

Následující příklad ukazuje vkládání objektů blob input and output bindings in a *function.json* file and [C# script (.csx)](functions-reference-csharp.md) code that uses the vazby. Funkce vytvoří kopii textového objektu blob. Funkce je spuštěna zprávou fronty, která obsahuje název objektu blob ke kopírování. Nový objekt blob má název *{originalblobname}-Copy*.

V souboru *function.json* se vlastnost `queueTrigger` metadat používá k určení `path` názvu objektu blob ve vlastnostech:

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód skriptu C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

<!--Same example for input and output. -->

Následující příklad ukazuje vkládání vazby vstupu a výstupu objektu blob v souboru *function.json* a [kód jazyka JavaScript,](functions-reference-node.md) který používá vazby. Funkce vytvoří kopii objektu blob. Funkce je spuštěna zprávou fronty, která obsahuje název objektu blob ke kopírování. Nový objekt blob má název *{originalblobname}-Copy*.

V souboru *function.json* se vlastnost `queueTrigger` metadat používá k určení `path` názvu objektu blob ve vlastnostech:

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

Následující příklad ukazuje vkládání datových blob a výstup vazby v *souboru function.json* a [python kód,](functions-reference-python.md) který používá vazby. Funkce vytvoří kopii objektu blob. Funkce je spuštěna zprávou fronty, která obsahuje název objektu blob ke kopírování. Nový objekt blob má název *{originalblobname}-Copy*.

V souboru *function.json* se vlastnost `queueTrigger` metadat používá k určení `path` názvu objektu blob ve vlastnostech:

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód Pythonu:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="java"></a>[Java](#tab/java)

Tato část obsahuje následující příklady:

* [Aktivační událost HTTP s použitím funkce OutputBinding](#http-trigger-using-outputbinding-java)
* [Aktivační událost fronty s použitím vrácené hodnoty funkce](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Aktivační událost HTTP pomocí outputbinding (Java)

 Následující příklad ukazuje funkci Java, `HttpTrigger` která používá poznámku k přijetí parametru obsahujícího název souboru v kontejneru úložiště objektů blob. Anotace `BlobInput` pak přečte soubor a předá `byte[]`jeho obsah funkci jako . Anotace `BlobOutput` se `OutputBinding outputItem`váže na , který je pak používán funkcí zapsat obsah vstupního objektu blob do nakonfigurovaného kontejneru úložiště.

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

#### <a name="queue-trigger-using-function-return-value-java"></a>Aktivační událost fronty, pomocí vrácené hodnoty funkce (Java)

 Následující příklad ukazuje funkci Java, `QueueTrigger` která používá poznámku k přijetí zprávy obsahující název souboru v kontejneru úložiště objektů blob. Anotace `BlobInput` pak přečte soubor a předá `byte[]`jeho obsah funkci jako . Anotace `BlobOutput` se váže na vrácenou hodnotu funkce, která se pak používá v době runtime k zápisu obsahu vstupního objektu blob do kontejneru nakonfigurovaného úložiště.

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

 V [knihovně runtime funkcí](/java/api/overview/azure/functions/runtime) `@BlobOutput` Jazyka Java použijte poznámku o parametrech funkce, jejichž hodnota by byla zapsána do objektu v úložišti objektů blob.  Typ parametru `OutputBinding<T>`by měl být , kde T je jakýkoli nativní typ Java nebo POJO.

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte [atribut BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atributu přebírá cestu k objektu `FileAccess` blob a parametr označující čtení nebo zápis, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Vlastnost můžete `Connection` nastavit tak, aby určila účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Atribut `@BlobOutput` umožňuje přístup k objektu blob, který spustil funkci. Pokud používáte bajtové pole s `dataType` `binary`atributem, nastavte na . Podrobnosti naleznete v [příkladu výstupu.](#example)

---

Úplný příklad naleznete v [příkladu Výstup](#example).

`StorageAccount` Atribut můžete použít k určení účtu úložiště na úrovni třídy, metody nebo parametru. Další informace naleznete v tématu [Trigger - atributy](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `Blob` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být `blob`nastavena na . |
|**direction** | neuvedeno | Musí být `out` nastavena na pro výstupní vazbu. Výjimky jsou uvedeny v části [použití.](#usage) |
|**Jméno** | neuvedeno | Název proměnné, která představuje objekt blob v kódu funkce.  Nastavte `$return` tak, aby odkazovalna vrácenou hodnotu funkce.|
|**Cestu** |**BlobPath** | Cesta k kontejneru objektů blob. |
|**Připojení** |**Připojení**| Název nastavení aplikace, která obsahuje připojovací řetězec úložiště pro tuto vazbu. Pokud název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytek názvu zde. Například pokud nastavíte `connection` na "MyStorage", funkce runtime hledá nastavení aplikace s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdné, spustí se s funkcí, která použije výchozí `AzureWebJobsStorage`připojovací řetězec úložiště v nastavení aplikace s názvem .<br><br>Připojovací řetězec musí být pro účet úložiště pro obecné účely, nikoli účet úložiště pouze pro [objekt y objektů .](../storage/common/storage-account-overview.md#types-of-storage-accounts)|
|neuvedeno | **Přístup** | Označuje, zda budete číst nebo psát. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

V Jazyce JavaScript přistupujete k datům objektů blob pomocí `context.bindings.<name from function.json>`.

# <a name="python"></a>[Python](#tab/python)

Parametry funkce můžete deklarovat jako následující typy pro zápis do úložiště objektů blob:

* Řetězce jako`func.Out(str)`
* Streamy jako`func.Out(func.InputStream)`

Podrobnosti naleznete v [příkladu výstupu.](#example)

# <a name="java"></a>[Java](#tab/java)

Atribut `@BlobOutput` umožňuje přístup k objektu blob, který spustil funkci. Pokud používáte bajtové pole s `dataType` `binary`atributem, nastavte na . Podrobnosti naleznete v [příkladu výstupu.](#example)

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba |  Odkaz |
|---|---|
| Objekt blob | [Kódy chyb objektů blob](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Tabulka, Fronta |  [Chybové kódy úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabulka, Fronta |  [Řešení potíží](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce při změně dat úložiště objektů blob](./functions-bindings-storage-blob-trigger.md)
- [Čtení dat úložiště objektů blob při spuštění funkce](./functions-bindings-storage-blob-input.md)
