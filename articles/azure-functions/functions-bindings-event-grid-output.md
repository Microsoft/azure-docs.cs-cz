---
title: Výstupní vazba Azure Event Grid pro funkce Azure
description: Naučte se odesílat událost Grid událostí v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368945"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Výstupní vazba Azure Event Grid pro funkce Azure

Pomocí výstupní vazby Event Grid zapisovat události do vlastního tématu. Musíte mít platný [přístupový klíč pro vlastní téma](../event-grid/security-authentication.md#custom-topic-publishing).

Informace o nastavení a konfiguraci naleznete v [přehledu](./functions-bindings-event-grid.md).

> [!NOTE]
> Výstupní vazba Event Grid nepodporuje sdílené přístupové podpisy (tokeny SAS). Je nutné použít přístupový klíč tématu.

> [!IMPORTANT]
> Výstupní vazba Event Grid je k dispozici pouze pro funkce 2.x a vyšší.

## <a name="example"></a>Příklad

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [funkci C#,](functions-dotnet-class-library.md) která zapisuje zprávu do vlastního tématu Event Grid, pomocí vrácené hodnoty metody jako výstupu:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Následující příklad ukazuje, jak `IAsyncCollector` pomocí rozhraní odeslat dávku zpráv.

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

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje data výstupní vazby Event Grid v souboru *function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Zde je c# kód skriptu, který vytvoří jednu událost:

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

Zde je c# kód skriptu, který vytváří více událostí:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje data výstupní vazby Event Grid v souboru *function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Zde je JavaScript kód, který vytváří jednu událost:

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

Zde je JavaScript kód, který vytváří více událostí:

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

# <a name="python"></a>[Python](#tab/python)

Výstupní vazba Event Grid není pro Python k dispozici.

# <a name="java"></a>[Java](#tab/java)

Výstupní vazba Event Grid není k dispozici pro Jazyk Java.

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C #](#tab/csharp)

Pro [knihovny tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [EventGridAttribute.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs)

Konstruktor atributu přebírá název nastavení aplikace, které obsahuje název vlastního tématu a název nastavení aplikace, která obsahuje klíč tématu. Další informace o těchto nastaveních naleznete v [tématu Output - configuration](#configuration). Zde je `EventGrid` příklad atributu:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v [příkladu](#example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Výstupní vazba Event Grid není pro Python k dispozici.

# <a name="java"></a>[Java](#tab/java)

Výstupní vazba Event Grid není k dispozici pro Jazyk Java.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `EventGrid` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být nastavena na "eventGrid". |
|**direction** | neuvedeno | Musí být nastavena na "out". Tento parametr se nastaví automaticky při vytváření vazby na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné použitý v kódu funkce, který představuje událost. |
|**témaEndpointUri** |**TopicEndpointUri** | Název nastavení aplikace, která obsahuje identifikátor URI pro `MyTopicEndpointUri`vlastní téma, například . |
|**topicKeySetting** |**Nastavení kláves topickey** | Název nastavení aplikace, která obsahuje přístupový klíč pro vlastní téma. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Ujistěte se, že `TopicEndpointUri` jste nastavili hodnotu vlastnosti konfigurace na název nastavení aplikace, které obsahuje identifikátor URI vlastního tématu. Nezadávejte identifikátor URI vlastního tématu přímo v této vlastnosti.

## <a name="usage"></a>Využití

# <a name="c"></a>[C #](#tab/csharp)

Odesílat zprávy pomocí parametru `out EventGridEvent paramName`metody, například . Chcete-li napsat více `ICollector<EventGridEvent>` zpráv, můžete použít nebo `IAsyncCollector<EventGridEvent>` místo `out EventGridEvent`.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Odesílat zprávy pomocí parametru `out EventGridEvent paramName`metody, například . Ve skriptu `paramName` jazyka C# je `name` hodnota zadaná ve vlastnosti *function.json*. Chcete-li napsat více `ICollector<EventGridEvent>` zpráv, můžete použít nebo `IAsyncCollector<EventGridEvent>` místo `out EventGridEvent`.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Přístup k výstupní `context.bindings.<name>` události `<name>` pomocí kde je `name` hodnota zadaná ve vlastnosti *function.json*.

# <a name="python"></a>[Python](#tab/python)

Výstupní vazba Event Grid není pro Python k dispozici.

# <a name="java"></a>[Java](#tab/java)

Výstupní vazba Event Grid není k dispozici pro Jazyk Java.

---

## <a name="next-steps"></a>Další kroky

* [Odeslání události Mřížky událostí](./functions-bindings-event-grid-trigger.md)
