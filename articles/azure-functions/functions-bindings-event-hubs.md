---
title: Azure Event Hubs vazby pro Azure Functions
description: Pochopit, jak používat Azure Event Hubs vazby v Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Funkce Azure, funkce zpracování událostí, dynamické výpočetní architektura bez serveru
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: tdykstra
ms.openlocfilehash: 64914a1b3efe81a152f5463f74c70c22f01ec0c1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724040"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Event Hubs vazby pro Azure Functions

Tento článek vysvětluje, jak pracovat s [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) vazby pro Azure Functions. Azure Functions podporuje aktivaci a výstupní vazeb pro služby Event Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Pro verzi Azure Functions 1.x, vazby centra událostí jsou součástí [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) balíček NuGet verze 2.x.
Zdrojový kód pro balíček je v [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) úložiště GitHub.


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Pro funkce 2.x, použijte [Microsoft.Azure.WebJobs.Extensions.EventHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) balíčku, verzi 3.x.
Zdrojový kód pro balíček je v [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Použijte aktivační událost Event Hubs reagovat na událost odeslaná datového proudu událostí centra událostí. Musíte mít přístup pro čtení do centra událostí vytvořit aktivační událost.

Když se aktivuje funkce aktivační událost Event Hubs, zprávu, která ji spouští je předán do funkce jako řetězec.

## <a name="trigger---scaling"></a>Aktivovat - škálování

Každá instance funkce Event Hub-Triggered je zajištěna pouze 1 instancí třídy EventProcessorHost (EPH). Služba Event Hubs zajišťuje, že pouze 1 EPH můžete získat zapůjčení v daném oddílu.

Předpokládejme například, že jsme začínat následující nastavení a předpoklady pro centra událostí:

1. 10 oddíly.
1. události 1000 rovnoměrně mezi všechny oddíly = > 100 zprávy v každém oddílu.

Když je funkce nejdřív povolené, je pouze 1 instancí funkce. Umožňuje volání této funkce instance Function_0. Function_0 bude mít 1 EPH, která spravuje získat zapůjčení na všechny oddíly 10. Zahájí čtení událostí z oddílů 0 – 9. Z tohoto bodu se stane jednu z těchto možností:

* **Je potřeba pouze 1 funkce instance** -Function_0 je schopna zpracovat všechny 1000 předtím, než dojde k logiku škálování Azure Functions. Proto Function_0 zpracovává všechny zprávy 1000.

* **Přidat 1 další instanci funkce** -logika škálování Azure Functions Určuje, že Function_0 má více zpráv, než může zpracovat, takže je vytvořena nová instance, Function_1,. Event Hubs zjistí, že novou instanci EPH se pokouší číst zprávy typu. Event Hubs se spustí vyrovnávání oddíly napříč instancemi EPH zatížení, například oddíly 0-4 jsou přiřazeny k Function_0 a oddíly 5-9 jsou přiřazeny k Function_1. 

* **Přidat N funkce více instancí** -logiku škálování Azure Functions zjistí, že Function_0 a Function_1 více zpráv, než se může zpracovat. Změní měřítko znovu n Function_2..., kde N je větší než oddíly centra událostí. Služba Event Hubs načte vyrovnávat oddíly ve Function_0... 9 instancí.

Pro aktuální Azure Functions jedinečné škálování logiku je skutečnost, že N je větší než počet oddílů. To slouží k zajistěte, aby vždy instancí EPH snadno dostupné, jakmile budou k dispozici od dalších instancí rychle získat zámek na oddíly. Uživatelům se účtují poplatky za prostředky používá, když instance funkce provede a pro tento předimenzování poplatky neúčtujeme.

Pokud všechny funkce spuštěních úspěšné bez chyb, kontrolní body se přidají do přidruženého účtu úložiště. Pokud odkazující kontrola proběhne úspěšně, všechny zprávy 1000 by nikdy načíst znovu.

## <a name="trigger---example"></a>Aktivační událost – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Aktivační událost – příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) , protokoly tělo zprávy aktivační události rozbočovače.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Získat přístup k [metadata události](#trigger---event-metadata) v kódu funkce vytvořit vazbu k [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektu (vyžaduje, pomocí příkazu pro `Microsoft.ServiceBus.Messaging`). Stejné vlastnosti také přistupujete pomocí vazby výrazů v podpis metody.  Následující příklad ukazuje, jak způsoby, jak získat stejná data:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage, 
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Chcete-li přijímat události v dávce, ujistěte se, `string` nebo `EventData` pole:

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>Aktivační událost – příklad skriptu jazyka C#

Následující příklad ukazuje vazby v aktivační signál události rozbočovače *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce zaznamená tělo zprávy aktivační události rozbočovače.

Následující příklady ukazují, vytvoření vazby dat služby Event Hubs v *function.json* souboru. V prvním příkladu se pro funkce 1.x a druhá je pro funkce 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Tady je kód skriptu jazyka C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Získat přístup k [metadata události](#trigger---event-metadata) v kódu funkce vytvořit vazbu k [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektu (vyžaduje, pomocí příkazu pro `Microsoft.ServiceBus.Messaging`). Stejné vlastnosti také přistupujete pomocí vazby výrazů v podpis metody.  Následující příklad ukazuje, jak způsoby, jak získat stejná data:

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Chcete-li přijímat události v dávce, ujistěte se, `string` nebo `EventData` pole:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Aktivační událost – příklad F #

Následující příklad ukazuje vazby v aktivační signál události rozbočovače *function.json* souboru a [funkce F #](functions-reference-fsharp.md) používající vazby. Funkce zaznamená tělo zprávy aktivační události rozbočovače.

Následující příklady ukazují, vytvoření vazby dat služby Event Hubs v *function.json* souboru. V prvním příkladu se pro funkce 1.x a druhá je pro funkce 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Tady je kód F #:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Aktivační událost – příklad v jazyce JavaScript

Následující příklad ukazuje vazby v aktivační signál události rozbočovače *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce přečte [metadata události](#trigger---event-metadata) a zaprotokoluje zprávu.

Následující příklady ukazují, vytvoření vazby dat služby Event Hubs v *function.json* souboru. V prvním příkladu se pro funkce 1.x a druhá je pro funkce 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Event Hubs trigger function processed message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);
     
    context.done();
};
```

Chcete-li přijímat události v dávce, nastavte `cardinality` k `many` v *function.json* souboru, jak je znázorněno v následujících příkladech. V prvním příkladu se pro funkce 1.x a druhá je pro funkce 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);
    
    eventHubMessages.forEach(message => {
        context.log(`Processed message ${message}`);
    });

    context.done();
};
```

## <a name="trigger---attributes"></a>Aktivační událost – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs) atribut.

Konstruktoru atributu přebírá název centra událostí, název skupiny uživatelů a název nastavení aplikace, který obsahuje připojovací řetězec. Další informace o těchto nastaveních najdete v tématu [aktivovat konfigurační oddíl](#trigger---configuration). Tady je `EventHubTriggerAttribute` atribut příklad:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Úplný příklad najdete v tématu [aktivační událost - C# příklad](#trigger---c-example).

## <a name="trigger---configuration"></a>Aktivační událost - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `EventHubTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | musí být nastavena na `eventHubTrigger`. Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure.|
|**direction** | neuvedeno | musí být nastavena na `in`. Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure. |
|**Jméno** | neuvedeno | Název proměnné, která představuje položku událostí v kódu funkce. | 
|**Cesta** |**EventHubName** | Funguje pouze 1.x. Název centra událostí.  | 
|**EventHubName** |**EventHubName** | Funguje pouze 2.x. Název centra událostí.  |
|**consumerGroup** |**ConsumerGroup** | Volitelná vlastnost, která nastavuje [skupiny příjemců](../event-hubs/event-hubs-features.md#event-consumers) používá přihlásit k odběru událostí v centru. Pokud tento parametr vynechán, `$Default` skupina uživatelů slouží. | 
|**Mohutnost** | neuvedeno | Pro jazyk Javascript. Nastavte na `many` Chcete-li povolit dávkování.  Pokud tento parametr vynechán, nebo nastavte `one`, funkci byl předán jedné zprávy. | 
|**Připojení** |**Připojení** | Název nastavení aplikace, který obsahuje připojovací řetězec k Centru událostí oboru názvů. Zkopírujte tento připojovací řetězec kliknutím **informace o připojení** tlačítko pro [obor názvů](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), ne samotný centra událostí. Tento připojovací řetězec musí mít alespoň oprávnění ke čtení pro aktivační událost.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Aktivační událost - metadata události

Aktivační událost Event Hubs poskytuje několik [metadata vlastnosti](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Tyto vlastnosti lze použít jako součást vazby výrazy v jiných vazby nebo jako parametry v kódu. Tyto vlastnosti jsou [EventData](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.eventdata) třídy.

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` Instance.|
|`EnqueuedTimeUtc`|`DateTime`|Zařazených do fronty čas v UTC.|
|`Offset`|`string`|Posun dat relativně k datový proud oddílu centra událostí. Posun je značka nebo identifikátor události v rámci služby Event Hubs datového proudu. Identifikátor je jedinečný v rámci oddílu centra událostí datového proudu.|
|`PartitionKey`|`string`|Oddíl, která událost data se mají posílat.|
|`Properties`|`IDictionary<String,Object>`|Vlastnosti uživatele data události.|
|`SequenceNumber`|`Int64`|Logické pořadové číslo události.|
|`SystemProperties`|`IDictionary<String,Object>`|Vlastnosti systému, včetně data události.|

V tématu [příklady kódu pro](#trigger---example) , použijte tyto vlastnosti dříve v tomto článku.

## <a name="trigger---hostjson-properties"></a>Aktivační událost - host.json vlastnosti

[Host.json](functions-host-json.md#eventhub) soubor obsahuje nastavení, které řídí chování aktivační událost Event Hubs.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Výstup

Použijte službu Event Hubs výstup vytvoření vazby na zapsat události do datového proudu událostí. Musíte mít oprávnění odesílat do centra událostí zapsat události do ní.

## <a name="output---example"></a>Výstup – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Výstup – příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) , zapíše zprávu do centra událostí, pomocí návratovou hodnotu metody jako výstup:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Výstup – příklad skriptu jazyka C#

Následující příklad ukazuje vazby v aktivační signál události rozbočovače *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují, vytvoření vazby dat služby Event Hubs v *function.json* souboru. V prvním příkladu se pro funkce 1.x a druhá je pro funkce 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Tady je C# kód skriptu, který vytvoří jednu zprávu:

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Zde uvádíme C# kód skriptu, který vytvoří více zpráv:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Výstup – příklad F #

Následující příklad ukazuje vazby v aktivační signál události rozbočovače *function.json* souboru a [funkce F #](functions-reference-fsharp.md) používající vazby. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují, vytvoření vazby dat služby Event Hubs v *function.json* souboru. V prvním příkladu se pro funkce 1.x a druhá je pro funkce 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Tady je kód F #:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Výstup – příklad v jazyce JavaScript

Následující příklad ukazuje vazby v aktivační signál události rozbočovače *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují, vytvoření vazby dat služby Event Hubs v *function.json* souboru. V prvním příkladu se pro funkce 1.x a druhá je pro funkce 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Zde uvádíme kódu jazyka JavaScript, který odesílá do jedné zprávy:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Zde uvádíme kódu jazyka JavaScript, který odesílá více zpráv:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="output---attributes"></a>Výstup – atributy

Pro [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) atribut.

Konstruktoru atributu přebírá název centra událostí a název nastavení aplikace, který obsahuje připojovací řetězec. Další informace o těchto nastaveních najdete v tématu [výstup - konfigurace](#output---configuration). Tady je `EventHub` atribut příklad:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Úplný příklad najdete v tématu [výstup - C# příklad](#output---c-example).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `EventHub` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavena na "eventHub". |
|**direction** | neuvedeno | Musí být nastavena na "out". Tento parametr je nastaven automaticky při vytvoření vazby na portálu Azure. |
|**Jméno** | neuvedeno | Název proměnné používá v kódu funkce, která představuje událost. | 
|**Cesta** |**EventHubName** | Funguje pouze 1.x. Název centra událostí.  | 
|**EventHubName** |**EventHubName** | Funguje pouze 2.x. Název centra událostí.  |
|**Připojení** |**Připojení** | Název nastavení aplikace, který obsahuje připojovací řetězec k Centru událostí oboru názvů. Zkopírujte tento připojovací řetězec kliknutím **informace o připojení** tlačítko pro *obor názvů*, ne samotný centra událostí. Tento připojovací řetězec musí mít oprávnění pro odesílání k odeslání zprávy do datového proudu událostí.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – použití

V jazyce C# a C# skript, odesílání zpráv pomocí parametru metody `out string paramName`. V jazyce C# skript `paramName` je hodnota zadaná v `name` vlastnost *function.json*. Zápis více zpráv, můžete použít `ICollector<string>` nebo `IAsyncCollector<string>` místě `out string`.

V jazyce JavaScript, přístup k výstupu událostí pomocí `context.bindings.<name>`. `<name>` Hodnota zadaná v `name` vlastnost *function.json*.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Centrum událostí | [Provozní příručka](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)
