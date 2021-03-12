---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 9fca69804220021ca7935e562f2026c11749515a
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623250"
---
Použijte výstupní vazbu Event Hubs k zápisu událostí do datového proudu událostí. Musíte mít oprávnění k odesílání do centra událostí, abyste do něj události mohli zapisovat.

Před pokusem o implementaci výstupní vazby se ujistěte, že jsou na místě požadované odkazy na balíčky.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](../articles/azure-functions/functions-dotnet-class-library.md) , která zapisuje zprávu do centra událostí pomocí návratové hodnoty metody jako výstup:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Následující příklad ukazuje, jak použít `IAsyncCollector` rozhraní k odeslání dávky zpráv. Tento scénář je běžný při zpracování zpráv přicházejících z jednoho centra událostí a odeslání výsledku do jiného centra událostí.

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

Následující příklad ukazuje aktivační vazbu centra událostí v *function.js* souboru a [funkci skriptu jazyka C#](../articles/azure-functions/functions-reference-csharp.md) , která používá vazbu. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují Event Hubs vázání dat v *function.js* souboru. První příklad je pro Functions 2. x a vyšší a druhý je pro Functions 1. x. 

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

Zde je kód skriptu jazyka C#, který vytváří jednu zprávu:

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

Zde je kód skriptu jazyka C#, který vytváří více zpráv:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje aktivační vazbu centra událostí v *function.js* souboru a [funkci JavaScriptu](../articles/azure-functions/functions-reference-node.md) , která používá vazbu. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují Event Hubs vázání dat v *function.js* souboru. První příklad je pro Functions 2. x a vyšší a druhý je pro Functions 1. x. 

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

Tady je kód JavaScriptu, který pošle jednu zprávu:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Zde je kód JavaScriptu, který odesílá více zpráv:

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

Následující příklad ukazuje aktivační vazbu centra událostí v *function.js* souboru a [funkci Pythonu](../articles/azure-functions/functions-reference-python.md) , která používá vazbu. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují Event Hubs vázání dat v *function.js* souboru.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Tady je kód Pythonu, který pošle jednu zprávu:

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

Následující příklad ukazuje funkci jazyka Java, která zapisuje zprávu obsahující aktuální čas do centra událostí.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@EventHubOutput` poznámku k parametrům, jejichž hodnota by byla publikována do centra událostí.  Parametr by měl být typu `OutputBinding<T>` , kde T je Pojo nebo jakýkoli nativní typ Java.

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

Pro [knihovny tříd jazyka C#](../articles/azure-functions/functions-dotnet-class-library.md)použijte atribut [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) .

Konstruktor atributu přebírá název centra událostí a název nastavení aplikace, které obsahuje připojovací řetězec. Další informace o těchto nastaveních naleznete v tématu [Output-Configuration](#configuration). Tady je `EventHub` příklad atributu:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v tématu [Output-C# příklad](#example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte anotaci [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) u parametrů, jejichž hodnota by byla publikována do centra událostí. Parametr by měl být typu `OutputBinding<T>` , kde `T` je Pojo nebo jakýkoli nativní typ Java.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `EventHub` atribut.

|function.jsvlastnost | Vlastnost atributu |Description|
|---------|---------|----------------------|
|**textový** | Není k dispozici | Musí být nastavené na eventHub. |
|**směr** | Není k dispozici | Musí být nastavené na "out". Tento parametr je nastaven automaticky při vytváření vazby v Azure Portal. |
|**Jméno** | Není k dispozici | Název proměnné použitý v kódu funkce, který představuje událost. |
|**dílčí** |**EventHubName** | Pouze funkce 1. x. Název centra událostí Pokud je v připojovacím řetězci přítomen i název centra událostí, tato hodnota tuto vlastnost Přepisuje za běhu. |
|**eventHubName** |**EventHubName** | Functions 2. x a vyšší. Název centra událostí Pokud je v připojovacím řetězci přítomen i název centra událostí, tato hodnota tuto vlastnost Přepisuje za běhu. |
|**vázán** |**Připojení** | Název nastavení aplikace, které obsahuje připojovací řetězec k oboru názvů centra událostí. Zkopírujte tento připojovací řetězec kliknutím na tlačítko **informace o připojení** pro [obor názvů](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), nikoli v samotném centru událostí. Tento připojovací řetězec musí mít oprávnění Odeslat pro odeslání zprávy do datového proudu událostí. <br><br>Pokud používáte [verzi 5. x nebo vyšší z rozšíření](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)místo připojovacího řetězce, můžete zadat odkaz na oddíl konfigurace, který definuje připojení. Viz [připojení](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Výchozí

Pro výstupní vazbu centra událostí můžete použít následující typy parametrů:

* `string`
* `byte[]`
* `POCO`
* `EventData` – Výchozí vlastnosti EventData jsou k dispozici v [oboru názvů Microsoft. Azure. EventHubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

Odesílat zprávy pomocí parametru metody, jako je například `out string paramName` . Ve skriptu jazyka C# `paramName` je hodnota zadaná ve `name` vlastnosti *function.jsv*. Chcete-li zapsat více zpráv, můžete použít `ICollector<string>` nebo `IAsyncCollector<string>` místo `out string` .

### <a name="additional-types"></a>Další typy 
Aplikace používající 5.0.0 nebo vyšší verze rozšíření centra událostí používají `EventData` typ v [Azure. Messaging. EventHubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) , nikoli v [oboru názvů Microsoft. Azure. EventHubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Tato verze vyřazuje podporu pro starší `Body` typ, a to ve prospěch následujících typů:

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

### <a name="default"></a>Výchozí

Pro výstupní vazbu centra událostí můžete použít následující typy parametrů:

* `string`
* `byte[]`
* `POCO`
* `EventData` – Výchozí vlastnosti EventData jsou k dispozici v [oboru názvů Microsoft. Azure. EventHubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

Odesílat zprávy pomocí parametru metody, jako je například `out string paramName` . Ve skriptu jazyka C# `paramName` je hodnota zadaná ve `name` vlastnosti *function.jsv*. Chcete-li zapsat více zpráv, můžete použít `ICollector<string>` nebo `IAsyncCollector<string>` místo `out string` .

### <a name="additional-types"></a>Další typy 
Aplikace používající 5.0.0 nebo vyšší verze rozšíření centra událostí používají `EventData` typ v [Azure. Messaging. EventHubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) , nikoli v [oboru názvů Microsoft. Azure. EventHubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Tato verze vyřazuje podporu pro starší `Body` typ, a to ve prospěch následujících typů:

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup k události výstupu pomocí `context.bindings.<name>` Where `<name>` je hodnota zadaná ve `name` vlastnosti *function.json*.

# <a name="python"></a>[Python](#tab/python)

K dispozici jsou dvě možnosti pro výstup zprávy centra událostí z funkce:

- **Návratová hodnota**: nastavte `name` vlastnost v *function.jsna* `$return` . V této konfiguraci je návratová hodnota funkce trvalá jako zpráva centra událostí.

- **Imperativní**: předejte hodnotu metodě [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) parametru deklarovaného jako typ [out](/python/api/azure-functions/azure.functions.out) . Předaná hodnota `set` je trvalá jako zpráva centra událostí.

# <a name="java"></a>[Java](#tab/java)

K dispozici jsou dvě možnosti pro výstup zprávy centra událostí z funkce pomocí anotace [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) :

- **Návratová hodnota**: použitím poznámky k samotné funkci je vrácená hodnota funkce trvalá jako zpráva centra událostí.

- **Imperativní**: Chcete-li explicitně nastavit hodnotu zprávy, použijte poznámku na konkrétní parametr typu [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.OutputBinding) , kde `T` je Pojo nebo jakýkoli nativní typ Java. Při této konfiguraci předává hodnota metodě předávání hodnoty `setValue` jako zprávy centra událostí.

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Reference |
|---|---|
| Centrum událostí | [Provozní příručka](/rest/api/eventhub/publisher-policy-operations) |