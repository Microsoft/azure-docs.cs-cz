---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1e25656b58fe675cfbe87fef75af4fcb174b7f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589737"
---
Pomocí výstupní vazby centra událostí zapisujte události do datového proudu událostí. Musíte mít oprávnění k odesílání do centra událostí, abyste do něj události mohli zapisovat.

Ujistěte se, že požadované odkazy na balíček jsou na místě před pokusem o implementaci výstupní vazby.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [funkci C#,](../articles/azure-functions/functions-dotnet-class-library.md) která zapisuje zprávu do centra událostí pomocí vrácené hodnoty metody jako výstupu:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Následující příklad ukazuje, jak `IAsyncCollector` pomocí rozhraní odeslat dávku zpráv. Tento scénář je běžné, když zpracováváte zprávy přicházející z jednoho centra událostí a odesíláte výsledek do jiného centra událostí.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje aktivační událost centra událostí v souboru *function.json* a [funkci skriptu Jazyka C#,](../articles/azure-functions/functions-reference-csharp.md) která používá vazbu. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují data vazby centra událostí v souboru *function.json.* První příklad je pro funkce 2.x a vyšší a druhý je pro funkce 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Zde je c# skript kód, který vytvoří jednu zprávu:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Zde je c# skript kód, který vytváří více zpráv:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje vazby aktivační události centra událostí v souboru *function.json* a [funkci JavaScriptu,](../articles/azure-functions/functions-reference-node.md) která vazbu používá. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují data vazby centra událostí v souboru *function.json.* První příklad je pro funkce 2.x a vyšší a druhý je pro funkce 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Zde je JavaScript kód, který odešle jednu zprávu:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Zde je JavaScript kód, který odesílá více zpráv:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje vazby aktivační události centra událostí v souboru *function.json* a [funkci Pythonu,](../articles/azure-functions/functions-reference-python.md) která používá vazbu. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují data vazby centra událostí v souboru *function.json.*

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Zde je kód Pythonu, který odešle jednu zprávu:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje funkci Java, která zapisuje zprávu obsahující aktuální čas do centra událostí.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

V [knihovně runtime funkcí javy](/java/api/overview/azure/functions/runtime)použijte poznámku `@EventHubOutput` k parametrům, jejichž hodnota by byla publikována v centru událostí.  Parametr by měl `OutputBinding<T>` být typu , kde T je POJO nebo jakýkoli nativní typ Java.

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C #](#tab/csharp)

Pro [knihovny tříd Jazyka C#](../articles/azure-functions/functions-dotnet-class-library.md)použijte atribut [EventHubAttribute.](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs)

Konstruktor atributu přebírá název centra událostí a název nastavení aplikace, která obsahuje připojovací řetězec. Další informace o těchto nastaveních naleznete v [tématu Output - configuration](#configuration). Zde je `EventHub` příklad atributu:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v [tématu Output - C# example](#example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

V [knihovně runtime funkcí Jazyka Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)použijte anotaci [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) u parametrů, jejichž hodnota by byla publikována v centru událostí. Parametr by měl `OutputBinding<T>` být `T` typu , kde je POJO nebo jakýkoli nativní typ Java.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `EventHub` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být nastavena na "eventHub". |
|**direction** | neuvedeno | Musí být nastavena na "out". Tento parametr se nastaví automaticky při vytváření vazby na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné použitý v kódu funkce, který představuje událost. |
|**Cestu** |**Název EventHubName** | Funkce pouze 1.x. Název centra událostí. Pokud je název centra událostí také přítomen v připojovacím řetězci, tato hodnota přepíše tuto vlastnost za běhu. |
|**eventHubName** |**Název EventHubName** | Funkce 2.x a vyšší. Název centra událostí. Pokud je název centra událostí také přítomen v připojovacím řetězci, tato hodnota přepíše tuto vlastnost za běhu. |
|**Připojení** |**Připojení** | Název nastavení aplikace, která obsahuje připojovací řetězec k oboru názvů centra událostí. Zkopírujte tento připojovací řetězec klepnutím na tlačítko **Informace o připojení** pro obor *názvů*, nikoli na samotné centrum událostí. Tento připojovací řetězec musí mít oprávnění k odeslání zprávy do datového proudu událostí.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C #](#tab/csharp)

Odesílat zprávy pomocí parametru `out string paramName`metody, například . Ve skriptu `paramName` jazyka C# je `name` hodnota zadaná ve vlastnosti *function.json*. Chcete-li napsat více `ICollector<string>` zpráv, můžete použít nebo `IAsyncCollector<string>` místo `out string`.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Odesílat zprávy pomocí parametru `out string paramName`metody, například . Ve skriptu `paramName` jazyka C# je `name` hodnota zadaná ve vlastnosti *function.json*. Chcete-li napsat více `ICollector<string>` zpráv, můžete použít nebo `IAsyncCollector<string>` místo `out string`.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Přístup k výstupní `context.bindings.<name>` události `<name>` pomocí kde je `name` hodnota zadaná ve vlastnosti *function.json*.

# <a name="python"></a>[Python](#tab/python)

Existují dvě možnosti pro vyprosit zprávu centra událostí z funkce:

- **Vrácená hodnota** `name` : Nastavte vlastnost *function.json* na `$return`. Při této konfiguraci je vrácená hodnota funkce zachována jako zpráva centra událostí.

- **Imperativní**: Předají hodnotu [metodě set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) parametru deklarovaného [Out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) jako out typ. Hodnota předaná je `set` trvalé jako zpráva centra událostí.

# <a name="java"></a>[Java](#tab/java)

Existují dvě možnosti pro výstup zprávy centra událostí z funkce pomocí anotace [EventHubOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)

- **Vrácená hodnota**: Použitím poznámky na samotnou funkci je vrácená hodnota funkce zachována jako zpráva centra událostí.

- **Imperativní**: Chcete-li explicitně nastavit hodnotu zprávy, použijte poznámku na konkrétní parametr typu [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), kde `T` je POJO nebo jakýkoli nativní typ Java. S touto konfigurací předávání `setValue` hodnoty metody zachová hodnotu jako zpráva centra událostí.

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Odkaz |
|---|---|
| Centrum událostí | [Průvodce provozem](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení host.json

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2.x a vyšších. Příklad souboru host.json níže obsahuje pouze nastavení verze 2.x+ pro tuto vazbu. Další informace o nastavení globální konfigurace ve verzích 2.x a dále naleznete [v odkazu host.json pro funkce Azure](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Odkaz na host.json ve funkcích 1.x najdete v [tématu reference host.json pro Funkce Azure 1.x](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|`maxBatchSize`|10|Maximální počet událostí přijatých za příjem smyčky.|
|`prefetchCount`|300|Výchozí počet předběžného načtení `EventProcessorHost`používaný podkladovým objektem .|
|`batchCheckpointFrequency`|1|Počet dávek událostí ke zpracování před vytvořením kontrolního bodu kurzoru EventHub.|
