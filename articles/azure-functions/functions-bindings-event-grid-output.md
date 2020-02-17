---
title: Azure Event Grid výstupní vazba pro Azure Functions
description: Naučte se odeslat událost Event Grid v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368945"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Event Grid výstupní vazba pro Azure Functions

Použijte výstupní vazbu Event Grid k zápisu událostí do vlastního tématu. Musíte mít platný [přístupový klíč pro vlastní téma](../event-grid/security-authentication.md#custom-topic-publishing).

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](./functions-bindings-event-grid.md).

> [!NOTE]
> Vazba Event Grid Output nepodporuje signatury sdíleného přístupu (tokeny SAS). Je nutné použít přístupový klíč tématu.

> [!IMPORTANT]
> Výstupní vazba Event Grid je k dispozici pouze pro funkce 2. x a vyšší.

## <a name="example"></a>Příklad

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která zapisuje zprávu do vlastního tématu Event Grid pomocí návratové hodnoty metody jako výstup:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Následující příklad ukazuje, jak použít rozhraní `IAsyncCollector` k odeslání dávky zpráv.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Následující příklad ukazuje výstupní data vazby Event Grid v souboru *Function. JSON* .

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Zde je C# kód skriptu, který vytváří jednu událost:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Zde je C# kód skriptu, který vytváří více událostí:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje výstupní data vazby Event Grid v souboru *Function. JSON* .

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Tady je kód JavaScriptu, který vytváří jednu událost:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Tady je kód JavaScriptu, který vytváří několik událostí:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Vazba Event Grid Output není pro Python k dispozici.

# <a name="javatabjava"></a>[Java](#tab/java)

Vazba Event Grid Output není pro jazyk Java k dispozici.

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Pro [ C# knihovny tříd](functions-dotnet-class-library.md)použijte atribut [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

Konstruktor atributu přebírá název nastavení aplikace, které obsahuje název vlastního tématu, a název nastavení aplikace, které obsahuje klíč tématu. Další informace o těchto nastaveních naleznete v tématu [Output-Configuration](#configuration). Tady je příklad atributu `EventGrid`:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v tématu [příklad](#example).

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Vazba Event Grid Output není pro Python k dispozici.

# <a name="javatabjava"></a>[Java](#tab/java)

Vazba Event Grid Output není pro jazyk Java k dispozici.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `EventGrid`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavené na "eventGrid". |
|**direction** | neuvedeno | Musí být nastavena na "out". Tento parametr je nastaven automaticky při vytváření vazby v Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné použitý v kódu funkce, který představuje událost. |
|**topicEndpointUri** |**TopicEndpointUri** | Název nastavení aplikace, které obsahuje identifikátor URI vlastního tématu, například `MyTopicEndpointUri`. |
|**topicKeySetting** |**TopicKeySetting** | Název nastavení aplikace, které obsahuje přístupový klíč pro vlastní téma. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Ujistěte se, že jste nastavili hodnotu vlastnosti konfigurace `TopicEndpointUri` na název nastavení aplikace, které obsahuje identifikátor URI vlastního tématu. Nezadávejte identifikátor URI vlastního tématu přímo v této vlastnosti.

## <a name="usage"></a>Využití

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Odesílat zprávy pomocí parametru metody, jako je například `out EventGridEvent paramName`. Chcete-li zapsat více zpráv, můžete místo `out EventGridEvent`použít `ICollector<EventGridEvent>` nebo `IAsyncCollector<EventGridEvent>`.

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Odesílat zprávy pomocí parametru metody, jako je například `out EventGridEvent paramName`. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *Function. JSON*. Chcete-li zapsat více zpráv, můžete místo `out EventGridEvent`použít `ICollector<EventGridEvent>` nebo `IAsyncCollector<EventGridEvent>`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Přístup k události výstupu pomocí `context.bindings.<name>`, kde `<name>` je hodnota zadaná ve vlastnosti `name` *funkce Function. JSON*.

# <a name="pythontabpython"></a>[Python](#tab/python)

Vazba Event Grid Output není pro Python k dispozici.

# <a name="javatabjava"></a>[Java](#tab/java)

Vazba Event Grid Output není pro jazyk Java k dispozici.

---

## <a name="next-steps"></a>Další kroky

* [Odeslání události Event Grid](./functions-bindings-event-grid-trigger.md)
