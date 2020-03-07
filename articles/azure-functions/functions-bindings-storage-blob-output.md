---
title: Výstupní vazba Azure Blob Storage pro Azure Functions
description: Přečtěte si, jak poskytnout data služby Azure Blob Storage do funkce Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: c6e15c9a99a78f0f3637f718b35462fe49fd5ee6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370822"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Výstupní vazba Azure Blob Storage pro Azure Functions

Výstupní vazba umožňuje upravit a odstranit data služby Blob Storage ve funkci Azure Functions.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](./functions-bindings-storage-blob.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód skriptu jazyka C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód jazyka JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód Pythonu:

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

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

Atribut `@BlobOutput` poskytuje přístup k objektu blob, který funkci aktivoval. Použijete-li pole bajtů s atributem, nastavte `dataType` na `binary`. Podrobnosti najdete v [příkladu výstupu](#example) .

---

Úplný příklad naleznete v tématu [výstup – příklad](#example).

Atribut `StorageAccount` lze použít k určení účtu úložiště na úrovni třídy, metody nebo parametru. Další informace najdete v tématu [Trigger – atributy](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `Blob`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavené na `blob`. |
|**direction** | neuvedeno | Musí být nastavené na `out` pro výstupní vazbu. Výjimky jsou uvedeny v části [použití](#usage) . |
|**Jméno** | neuvedeno | Název proměnné, která představuje objekt BLOB v kódu funkce.  Nastavte na `$return` pro odkaz na návratovou hodnotu funkce.|
|**dílčí** |**Blobpath cestou** | Cesta k kontejneru objektů BLOB. |
|**vázán** |**Vázán**| Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "AzureWebJobsMyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage`.<br><br>Připojovací řetězec musí být pro účet úložiště pro obecné účely, nikoli [účet úložiště jen pro objekty blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|neuvedeno | **Přístup** | Určuje, zda budete číst nebo zapisovat. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

V JavaScriptu přístup k datům objektu BLOB pomocí `context.bindings.<name from function.json>`.

# <a name="python"></a>[Python](#tab/python)

Parametry funkce můžete deklarovat jako následující typy pro zápis do úložiště objektů BLOB:

* Řetězce jako `func.Out(str)`
* Streamy jako `func.Out(func.InputStream)`

Podrobnosti najdete v [příkladu výstupu](#example) .

# <a name="java"></a>[Java](#tab/java)

Atribut `@BlobOutput` poskytuje přístup k objektu blob, který funkci aktivoval. Použijete-li pole bajtů s atributem, nastavte `dataType` na `binary`. Podrobnosti najdete v [příkladu výstupu](#example) .

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba |  Referenční informace |
|---|---|
| Objekt blob | [Kódy chyb objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Objekt blob, tabulka, fronta |  [Kódy chyb úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objekt blob, tabulka, fronta |  [Řešení potíží](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Další kroky

- [Spustit funkci při změně dat služby Blob Storage](./functions-bindings-storage-blob-trigger.md)
- [Při spuštění funkce číst data služby Blob Storage](./functions-bindings-storage-blob-input.md)
