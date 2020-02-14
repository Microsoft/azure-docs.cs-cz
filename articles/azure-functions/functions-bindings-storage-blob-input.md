---
title: Vstupní vazba Azure Blob Storage pro Azure Functions
description: Přečtěte si, jak poskytnout data služby Azure Blob Storage do funkce Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: e074d7d74c0c5f020cb8086124634b25012927db
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202148"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Vstupní vazba Azure Blob Storage pro Azure Functions

Vstupní vazba umožňuje číst data služby Blob Storage jako vstup do funkce Azure Functions.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](./functions-bindings-storage-blob.md).

## <a name="example"></a>Příklad

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

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

## <a name="attributes-and-annotations"></a>Atributy a poznámky

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

Atribut `StorageAccount` lze použít k určení účtu úložiště na úrovni třídy, metody nebo parametru. Další informace najdete v tématu [Trigger – atributy a poznámky](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="javatabjava"></a>[Java](#tab/java)

Atribut `@BlobInput` poskytuje přístup k objektu blob, který funkci aktivoval. Použijete-li pole bajtů s atributem, nastavte `dataType` na `binary`. Podrobnosti najdete v [příkladu vstupu](#example) .

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `Blob`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | musí být nastavené na `blob`. |
|**direction** | neuvedeno | musí být nastavené na `in`. Výjimky jsou uvedeny v části [použití](#usage) . |
|**Jméno** | neuvedeno | Název proměnné, která představuje objekt BLOB v kódu funkce.|
|**dílčí** |**Blobpath cestou** | Cesta k objektu BLOB |
|**vázán** |**Vázán**| Název nastavení aplikace, které obsahuje [připojovací řetězec úložiště](../storage/common/storage-configure-connection-string.md) , který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "AzureWebJobsMyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage`.<br><br>Připojovací řetězec musí být pro účet úložiště pro obecné účely, nikoli [účet úložiště jen pro objekty blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|neuvedeno | **Přístup** | Určuje, zda budete číst nebo zapisovat. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Přístup k datům objektů BLOB pomocí `context.bindings.<NAME>`, kde `<NAME>` odpovídá hodnotě definované v *Function. JSON*.

# <a name="pythontabpython"></a>[Python](#tab/python)

Přístup k datům objektů BLOB prostřednictvím parametru zadaného jako [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Podrobnosti najdete v [příkladu vstupu](#example) .

# <a name="javatabjava"></a>[Java](#tab/java)

Atribut `@BlobInput` poskytuje přístup k objektu blob, který funkci aktivoval. Použijete-li pole bajtů s atributem, nastavte `dataType` na `binary`. Podrobnosti najdete v [příkladu vstupu](#example) .

---

## <a name="next-steps"></a>Další kroky

- [Spustit funkci při změně dat služby Blob Storage](./functions-bindings-storage-blob-trigger.md)
- [Zápis dat služby Blob Storage z funkce](./functions-bindings-storage-blob-output.md)
