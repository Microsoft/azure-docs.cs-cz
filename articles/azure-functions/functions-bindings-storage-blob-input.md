---
title: Vstupní vazba úložiště Azure blob pro funkce Azure
description: Zjistěte, jak poskytovat data úložiště objektů blob Azure do funkce Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 33db9a8d86e02db2076cdb85170d466697930b96
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633884"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Vstupní vazba úložiště Azure blob pro funkce Azure

Vstupní vazba umožňuje číst data úložiště objektů blob jako vstup do funkce Azure.

Informace o nastavení a konfiguraci naleznete v [přehledu](./functions-bindings-storage-blob.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad je [c# funkce,](functions-dotnet-class-library.md) která používá fronta aktivační událost a vstupní vazby objektu blob. Zpráva fronty obsahuje název objektu blob a funkce protokoluje velikost objektu blob.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
      "dataType": "binary",
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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód Pythonu:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="java"></a>[Java](#tab/java)

Tato část obsahuje následující příklady:

* [Aktivační událost HTTP, vyhledat název objektu blob z řetězce dotazu](#http-trigger-look-up-blob-name-from-query-string)
* [Aktivační událost fronty, příjem názvu objektu blob ze zprávy fronty](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>Aktivační událost HTTP, vyhledat název objektu blob z řetězce dotazu

 Následující příklad ukazuje funkci Java, `HttpTrigger` která používá poznámku k přijetí parametru obsahujícího název souboru v kontejneru úložiště objektů blob. Anotace `BlobInput` pak přečte soubor a předá `byte[]`jeho obsah funkci jako .

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Aktivační událost fronty, příjem názvu objektu blob ze zprávy fronty

 Následující příklad ukazuje funkci Java, `QueueTrigger` která používá poznámku k přijetí zprávy obsahující název souboru v kontejneru úložiště objektů blob. Anotace `BlobInput` pak přečte soubor a předá `byte[]`jeho obsah funkci jako .

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

V [knihovně runtime funkcí](/java/api/overview/azure/functions/runtime) `@BlobInput` Jazyka Java použijte poznámku k parametrům, jejichž hodnota by pocházela z objektu blob.  Tuto poznámku lze použít s nativními typy Jazyka Java, `Optional<T>`POJOnebo hodnotami s možnou hodnotou s hodnotou s nulou pomocí .

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte [atribut BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atributu přebírá cestu k objektu `FileAccess` blob a parametr označující čtení nebo zápis, jak je znázorněno v následujícím příkladu:

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

Vlastnost můžete `Connection` nastavit tak, aby určila účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

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

`StorageAccount` Atribut můžete použít k určení účtu úložiště na úrovni třídy, metody nebo parametru. Další informace naleznete v [tématu Trigger - atributy a poznámky](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Atribut `@BlobInput` umožňuje přístup k objektu blob, který spustil funkci. Pokud používáte bajtové pole s `dataType` `binary`atributem, nastavte na . Podrobnosti naleznete v [příkladu vstupu.](#example)

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `Blob` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být `blob`nastavena na . |
|**direction** | neuvedeno | Musí být `in`nastavena na . Výjimky jsou uvedeny v části [použití.](#usage) |
|**Jméno** | neuvedeno | Název proměnné, která představuje objekt blob v kódu funkce.|
|**Cestu** |**BlobPath** | Cesta k objektu blob. |
|**Připojení** |**Připojení**| Název nastavení aplikace, která obsahuje [připojovací řetězec úložiště](../storage/common/storage-configure-connection-string.md) pro tuto vazbu. Pokud název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytek názvu zde. Například pokud nastavíte `connection` na "MyStorage", funkce runtime hledá nastavení aplikace s názvem "AzureWebJobsMyStorage". Pokud necháte `connection` prázdné, spustí se s funkcí, která použije výchozí `AzureWebJobsStorage`připojovací řetězec úložiště v nastavení aplikace s názvem .<br><br>Připojovací řetězec musí být pro účet úložiště pro obecné účely, nikoli účet úložiště pouze pro [objekt y objektů .](../storage/common/storage-account-overview.md#types-of-storage-accounts)|
|neuvedeno | **Přístup** | Označuje, zda budete číst nebo psát. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup k datům objektu blob pomocí `context.bindings.<NAME>` where where `<NAME>` odpovídá hodnotě definované v *souboru function.json*.

# <a name="python"></a>[Python](#tab/python)

Přístup k datům objektu blob prostřednictvím parametru zadaného jako [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Podrobnosti naleznete v [příkladu vstupu.](#example)

# <a name="java"></a>[Java](#tab/java)

Atribut `@BlobInput` umožňuje přístup k objektu blob, který spustil funkci. Pokud používáte bajtové pole s `dataType` `binary`atributem, nastavte na . Podrobnosti naleznete v [příkladu vstupu.](#example)

---

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce při změně dat úložiště objektů blob](./functions-bindings-storage-blob-trigger.md)
- [Zápis dat úložiště objektů blob z funkce](./functions-bindings-storage-blob-output.md)
