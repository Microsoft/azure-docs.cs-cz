---
title: Výstupní vazba úložiště azure fronty pro funkce Azure
description: Naučte se vytvářet zprávy úložiště fronty Azure ve funkcích Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 76af5f398edd736874fa79095f2e80c02298eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277333"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Výstupní vazby úložiště azure fronty pro funkce Azure

Azure Functions můžete vytvořit nové zprávy úložiště Fronty Azure nastavením výstupní vazby.

Informace o nastavení a konfiguraci naleznete v [přehledu](./functions-bindings-storage-queue.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [funkci Jazyka C#,](functions-dotnet-class-library.md) která vytvoří zprávu fronty pro každý přijatý požadavek HTTP.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje vazbu aktivační události HTTP v souboru *function.json* a kód [skriptu Jazyka C# (.csx),](functions-reference-csharp.md) který používá vazbu. Funkce vytvoří položku fronty s datovou částobjektu **CustomQueueMessage** pro každý přijatý požadavek HTTP.

Zde je *soubor function.json:*

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód skriptu C#, který vytvoří jednu zprávu fronty:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Pomocí parametru `ICollector` nebo `IAsyncCollector` můžete odeslat více zpráv najednou. Zde je kód skriptu C#, který odesílá více zpráv, jeden s daty požadavku HTTP a jeden s pevně zakódované hodnoty:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje vazbu aktivační události HTTP v souboru *function.json* a [funkci JavaScriptu,](functions-reference-node.md) která vazbu používá. Funkce vytvoří položku fronty pro každý přijatý požadavek HTTP.

Zde je *soubor function.json:*

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Můžete odeslat více zpráv najednou definováním pole `myQueueItem` zpráv pro výstupní vazbu. Následující kód JavaScriptu odesílá dvě zprávy fronty s pevně zakódované hodnoty pro každý přijatý požadavek HTTP.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje, jak výstup jedné a více hodnot do front úložiště. Konfigurace potřebná pro *soubor function.json* je stejná v obou směrech.

Vazba fronty úložiště je definována v *souboru function.json,* kde je *typ* nastaven na `queue`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Chcete-li nastavit jednotlivé zprávy ve frontě, `set` předáte jedné hodnoty metody.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Chcete-li vytvořit více zpráv ve frontě, deklarujte parametr jako příslušný typ seznamu `set` a předajte metodě pole hodnot (které odpovídají typu seznamu).

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

 Následující příklad ukazuje funkci Java, která vytvoří zprávu fronty pro spuštění požadavkem HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

V [knihovně runtime funkcí](/java/api/overview/azure/functions/runtime) `@QueueOutput` jazyka Java použijte poznámku k parametrům, jejichž hodnota by byla zapsána do úložiště fronty.  Typ parametru `OutputBinding<T>`by `T` měl být , kde je libovolný nativní typ Java POJO.

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte [atribut QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

Atribut se vztahuje `out` na parametr nebo vrácenou hodnotu funkce. Konstruktor atributu přebírá název fronty, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Vlastnost můžete `Connection` nastavit tak, aby určila účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Úplný příklad naleznete v [příkladu Výstup](#example).

`StorageAccount` Atribut můžete použít k určení účtu úložiště na úrovni třídy, metody nebo parametru. Další informace naleznete v tématu Trigger - atributy.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Anotace `QueueOutput` umožňuje napsat zprávu jako výstup funkce. Následující příklad ukazuje funkci aktivovanou protokolem HTTP, která vytváří zprávu fronty.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Vlastnost    | Popis |
|-------------|-----------------------------|
|`name`       | Deklaruje název parametru v podpisu funkce. Při spuštění funkce má hodnota tohoto parametru obsah zprávy fronty. |
|`queueName`  | Deklaruje název fronty v účtu úložiště. |
|`connection` | Odkazuje na připojovací řetězec účtu úložiště. |

Parametr přidružený k `QueueOutput` anotaci je zadán jako instance [OutputBinding\<T.\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java)

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `Queue` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být `queue`nastavena na . Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal.|
|**direction** | neuvedeno | Musí být `out`nastavena na . Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje frontu v kódu funkce. Nastavte `$return` tak, aby odkazovalna vrácenou hodnotu funkce.|
|**název_fronty** |**Název fronty** | Název fronty. |
|**Připojení** | **Připojení** |Název nastavení aplikace, která obsahuje připojovací řetězec úložiště pro tuto vazbu. Pokud název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytek názvu zde. Například pokud nastavíte `connection` "MyStorage", funkce runtime hledá nastavení aplikace s názvem "MyStorage." Pokud necháte `connection` prázdné, spustí se s funkcí, která použije výchozí `AzureWebJobsStorage`připojovací řetězec úložiště v nastavení aplikace s názvem .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C #](#tab/csharp)

Napište jednu zprávu fronty pomocí `out T paramName`parametru metody, například . Můžete použít návratový typ metody `out` namísto `T` parametru a může být libovolný z následujících typů:

* Objekt serializovatelný jako JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Pokud se pokusíte `CloudQueueMessage` vytvořit vazbu a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

Ve skriptu C# a C# zapište více zpráv fronty pomocí jednoho z následujících typů: 

* `ICollector<T>` nebo `IAsyncCollector<T>`
* [CloudOvá fronta](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Napište jednu zprávu fronty pomocí `out T paramName`parametru metody, například . Je `paramName` hodnota zadaná `name` ve vlastnosti *function.json*. Můžete použít návratový typ metody `out` namísto `T` parametru a může být libovolný z následujících typů:

* Objekt serializovatelný jako JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Pokud se pokusíte `CloudQueueMessage` vytvořit vazbu a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

Ve skriptu C# a C# zapište více zpráv fronty pomocí jednoho z následujících typů: 

* `ICollector<T>` nebo `IAsyncCollector<T>`
* [CloudOvá fronta](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[Javascript](#tab/javascript)

Položka výstupní fronty je `context.bindings.<NAME>` `<NAME>` k dispozici prostřednictvím, kde odpovídá názvu definovanému v *souboru function.json*. Pro datovou část položky fronty můžete použít řetězec nebo objekt, který lze serializovat.

# <a name="python"></a>[Python](#tab/python)

Existují dvě možnosti pro vyprosit zprávu centra událostí z funkce:

- **Vrácená hodnota** `name` : Nastavte vlastnost *function.json* na `$return`. Při této konfiguraci je vrácená hodnota funkce zachována jako zpráva úložiště fronty.

- **Imperativní**: Předají hodnotu [metodě set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) parametru deklarovaného [Out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) jako out typ. Hodnota předaná je `set` trvalé jako zpráva úložiště fronty.

# <a name="java"></a>[Java](#tab/java)

Existují dvě možnosti pro výstup zprávy centra událostí z funkce pomocí anotace [QueueOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput)

- **Vrácená hodnota**: Použitím poznámky na samotnou funkci je vrácená hodnota funkce zachována jako zpráva centra událostí.

- **Imperativní**: Chcete-li explicitně nastavit hodnotu zprávy, použijte poznámku na konkrétní parametr typu [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), kde `T` je POJO nebo jakýkoli nativní typ Java. S touto konfigurací předávání `setValue` hodnoty metody zachová hodnotu jako zpráva centra událostí.

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba |  Odkaz |
|---|---|
| Fronta | [Kódy chyb fronty](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, Tabulka, Fronta | [Chybové kódy úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabulka, Fronta |  [Řešení potíží](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení host.json

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2.x a vyšších. Příklad souboru host.json níže obsahuje pouze nastavení verze 2.x+ pro tuto vazbu. Další informace o nastavení globální konfigurace ve verzích 2.x a dále naleznete [v odkazu host.json pro funkce Azure](functions-host-json.md).

> [!NOTE]
> Odkaz na host.json ve funkcích 1.x najdete v [tématu reference host.json pro Funkce Azure 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Maximální interval mezi dotazovánífronty. Minimální je 00:00:00.100 (100 ms) a přírůstky do 00:01:00 (1 min).  V 1.x datový typ je milisekund a v 2.x a vyšší je TimeSpan.|
|visibilityTimeout|00:00:00|Časový interval mezi opakováním při zpracování zprávy se nezdaří. |
|batchSize|16|Počet zpráv fronty, které načte čas běhu Functions současně a zpracovává paralelně. Když číslo, které jsou zpracovávány přejde do `newBatchThreshold`, runtime získá další dávku a začne zpracovávat tyto zprávy. Maximální počet souběžných zpráv zpracovávaných na `batchSize` funkci `newBatchThreshold`je tedy plus . Toto omezení platí samostatně pro každou funkci aktivovanou frontou. <br><br>Pokud se chcete vyhnout paralelnímu spuštění zpráv přijatých `batchSize` v jedné frontě, můžete nastavit na 1. Toto nastavení však eliminuje souběžnost pouze tak dlouho, dokud vaše aplikace funkce běží na jednom virtuálním počítači (VM). Pokud se aplikace funkce škáluje na více virtuálních počítačích, každý virtuální počítač může spustit jednu instanci každé funkce aktivované frontou.<br><br>Maximální `batchSize` hodnota je 32. |
|maxDequeueCount|5|Počet opakování zpracování zprávy před přesunutím do fronty jedovatý.|
|newBatchThreshold|dávkaVelikost/2|Vždy, když počet zpráv, které jsou zpracovávány současně dostane dolů na toto číslo, runtime načte jinou dávku.|

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce jako změny dat úložiště fronty (Trigger)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
