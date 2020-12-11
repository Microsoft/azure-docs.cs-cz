---
title: Azure Event Grid výstupní vazba pro Azure Functions
description: Naučte se odeslat událost Event Grid v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 888afdc2764fed9f0b2c8b548c3e2b1c48e9a31e
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094672"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Event Grid výstupní vazba pro Azure Functions

Použijte výstupní vazbu Event Grid k zápisu událostí do vlastního tématu. Musíte mít platný [přístupový klíč pro vlastní téma](../event-grid/security-authenticate-publishing-clients.md).

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](./functions-bindings-event-grid.md).

> [!NOTE]
> Vazba Event Grid Output nepodporuje signatury sdíleného přístupu (tokeny SAS). Je nutné použít přístupový klíč tématu.

> [!IMPORTANT]
> Výstupní vazba Event Grid je k dispozici pouze pro funkce 2. x a vyšší.

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která zapisuje zprávu do vlastního tématu Event Grid pomocí návratové hodnoty metody jako výstup:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Následující příklad ukazuje, jak použít `IAsyncCollector` rozhraní k odeslání dávky zpráv.

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

Následující příklad ukazuje Event Grid výstupní data vazby v *function.jsv* souboru.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Zde je kód skriptu jazyka C#, který vytváří jednu událost:

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

Zde je kód skriptu jazyka C#, který vytváří více událostí:

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

# <a name="java"></a>[Java](#tab/java)

Vazba Event Grid Output není pro jazyk Java k dispozici.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje Event Grid výstupní data vazby v *function.jsv* souboru.

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Následující příklad ukazuje, jak nakonfigurovat funkci pro výstup zprávy události Event Grid. Oddíl, ve kterém `type` je nastavena `eventGrid` Konfigurace hodnot potřebných pro vytvoření výstupní vazby Event Grid.

```powershell
{
  "bindings": [
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    },
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

Ve své funkci použijte `Push-OutputBinding` k odeslání události do vlastního tématu prostřednictvím výstupní vazby Event Grid.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message

Push-OutputBinding -Name outputEvent -Value  @{
    id = "1"
    EventType = "testEvent"
    Subject = "testapp/testPublish"
    EventTime = "2020-08-27T21:03:07+00:00"
    Data = @{
        Message = $message
    }
    DataVersion = "1.0"
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje aktivační vazbu v *function.js* souboru a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Pak pošle v události do vlastního tématu, jak je uvedeno v `topicEndpointUri` .

Tady jsou data vazby v *function.js* souboru:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Zde je ukázka Pythonu pro odeslání události do vlastního tématu nastavením `EventGridOutputEvent` :

```python
import logging
import azure.functions as func
import datetime

def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

Pro [knihovny tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

Konstruktor atributu přebírá název nastavení aplikace, které obsahuje název vlastního tématu, a název nastavení aplikace, které obsahuje klíč tématu. Další informace o těchto nastaveních naleznete v tématu [Output-Configuration](#configuration). Tady je `EventGrid` příklad atributu:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v tématu [příklad](#example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="java"></a>[Java](#tab/java)

Vazba Event Grid Output není pro jazyk Java k dispozici.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell nepodporuje atributy.

# <a name="python"></a>[Python](#tab/python)

Vazba Event Grid Output není pro Python k dispozici.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `EventGrid` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**textový** | neuvedeno | Musí být nastavené na "eventGrid". |
|**směr** | neuvedeno | Musí být nastavené na "out". Tento parametr je nastaven automaticky při vytváření vazby v Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné použitý v kódu funkce, který představuje událost. |
|**topicEndpointUri** |**TopicEndpointUri** | Název nastavení aplikace, které obsahuje identifikátor URI vlastního tématu, například `MyTopicEndpointUri` . |
|**topicKeySetting** |**TopicKeySetting** | Název nastavení aplikace, které obsahuje přístupový klíč pro vlastní téma. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Ujistěte se, že jste nastavili hodnotu `TopicEndpointUri` Vlastnosti konfigurace na název nastavení aplikace, které obsahuje identifikátor URI vlastního tématu. Nezadávejte identifikátor URI vlastního tématu přímo v této vlastnosti.

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

Odesílat zprávy pomocí parametru metody, jako je například `out EventGridEvent paramName` . Chcete-li zapsat více zpráv, můžete použít `ICollector<EventGridEvent>` nebo `IAsyncCollector<EventGridEvent>` místo `out EventGridEvent` .

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Odesílat zprávy pomocí parametru metody, jako je například `out EventGridEvent paramName` . Ve skriptu jazyka C# `paramName` je hodnota zadaná ve `name` vlastnosti *function.jsv*. Chcete-li zapsat více zpráv, můžete použít `ICollector<EventGridEvent>` nebo `IAsyncCollector<EventGridEvent>` místo `out EventGridEvent` .

# <a name="java"></a>[Java](#tab/java)

Vazba Event Grid Output není pro jazyk Java k dispozici.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup k události výstupu pomocí `context.bindings.<name>` Where `<name>` je hodnota zadaná ve `name` vlastnosti *function.json*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Přístup k události výstupu pomocí `Push-OutputBinding` rutiny k odeslání události do výstupní vazby Event Grid.

# <a name="python"></a>[Python](#tab/python)

Vazba Event Grid Output není pro Python k dispozici.

---

## <a name="next-steps"></a>Další kroky

* [Odeslání události Event Grid](./functions-bindings-event-grid-trigger.md)
