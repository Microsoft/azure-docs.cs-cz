---
title: Azure Event Hubs vazby pro službu Azure Functions
description: Vysvětlení použití služby Azure Event Hubs vazby ve službě Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: glenga
ms.openlocfilehash: 610771e659a80e330fbb1c9d6fd97c15ff832386
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42054255"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Event Hubs vazby pro službu Azure Functions

Tento článek vysvětluje, jak pracovat s [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) vazby pro službu Azure Functions. Azure Functions podporuje aktivaci a výstupní vazby služby Event hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Pro Azure Functions verzi 1.x, vazby služby Event Hubs jsou součástí [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) balíčku NuGet, verze 2.x.
Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) úložiště GitHub.


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Pro funkce 2.x, použijte [Microsoft.Azure.WebJobs.Extensions.EventHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) balíčku, verzi 3.x.
Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Pomocí aktivační události Event Hubs reagovat na událost odeslaná do datového proudu událostí centra událostí. Musíte mít přístup pro čtení do centra událostí, a nastavit aktivační událost.

Když se aktivuje funkci triggeru Event Hubs, zprávu, která se aktivuje je předán do funkce jako řetězec.

## <a name="trigger---scaling"></a>Aktivovat – škálování

Každá instance funkce Event Hub-Triggered zálohovaný jenom 1 instance třídy EventProcessorHost (EPH). Služba Event Hubs zajišťuje, že pouze 1 EPH můžete získat zapůjčení v daném oddílu.

Předpokládejme například, že Začneme s následující předpoklady pro Centrum událostí a nastavení:

1. 10 oddíly.
1. události 1000 rovnoměrně mezi všechny oddíly = > 100 zprávy v každém oddílu.

Pokud funkce je nejprve povolena, je jenom 1 instance funkce. Pojmenujme tuto instanci funkce Function_0. Function_0 bude mít 1 EPH, který spravuje získat zapůjčení na všechny oddíly 10. Spustí načítání událostí z oddíly 0 – 9. Od této chvíle se stane něco z tohoto:

* **Je potřeba jenom 1 funkce instance** -Function_0 je schopná zpracovat všechny 1000 dříve, než se aktivuje, logiku škálování Azure Functions. Proto Function_0 zpracovává všechny zprávy 1000.

* **Přidat 1 další instanci funkce** – logika škálování Azure Functions Určuje, že Function_0 má víc zpráv, než dokáže zpracovat, takže je vytvořena nová instance Function_1,. Event Hubs zjistí, že nové instance EPH se pokouší číst zprávy. Event Hubs se spustí vyrovnávání oddílů napříč instancemi EPH zatížení, například oddíly 0-4 jsou přiřazeny k Function_0 a oddíly 5 až 9 jsou přiřazeny k Function_1. 

* **Přidat N funkce více instancí** – škálování Azure Functions logiky zjistí, že Function_0 a Function_1 víc zpráv, než dokáže zpracovat. Provede se škálování znovu pro Function_2... N, kde N je větší než má téma oddílů centra událostí. Event Hubs se načte oddíly vyrovnávat Function_0... 9 instancí.

Azure Functions aktuální škálování logiky je skutečnost, že N je větší než počet oddílů. To slouží k zajištění, že vždy existuje instancí EPH snadno k dispozici rychle získat zámek na oddíly, jakmile budou dostupné z jiných instancí. Uživatelům se účtuje pouze pro prostředky používané při instanci funkce provede a za toto bylo potřeba zřizovat žádné poplatky neúčtujeme.

Pokud všechny prováděné funkce uspět bez chyb, kontrolní body se přidají do přidruženého účtu úložiště. Pokud bodového proběhne úspěšně, všechny zprávy 1000 by nikdy být znovu načteny.

## <a name="trigger---example"></a>Aktivační události – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Aktivační události – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , která protokoluje těla aktivační událost centra událostí.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Získat přístup k [metadat události](#trigger---event-metadata) v kódu funkce svázat [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektu (vyžaduje sadu pomocí příkazu pro `Microsoft.ServiceBus.Messaging`). Můžete také přistupovat k stejné vlastnosti pomocí vazbové výrazy v podpisu metody.  Následující příklad ukazuje oba způsoby, jak získat stejná data:

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

Ujistěte se, pokud chcete přijímat události v dávce, `string` nebo `EventData` pole:

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

### <a name="trigger---c-script-example"></a>Aktivační události – příklad skriptu jazyka C#

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce protokoly těla aktivační událost centra událostí.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru. První příklad je určený pro funkce 1.x a druhý je pro službu Functions 2.x. 

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

Získat přístup k [metadat události](#trigger---event-metadata) v kódu funkce svázat [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektu (vyžaduje sadu pomocí příkazu pro `Microsoft.ServiceBus.Messaging`). Můžete také přistupovat k stejné vlastnosti pomocí vazbové výrazy v podpisu metody.  Následující příklad ukazuje oba způsoby, jak získat stejná data:

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

Ujistěte se, pokud chcete přijímat události v dávce, `string` nebo `EventData` pole:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Aktivační události – příklad F #

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [funkce jazyka F #](functions-reference-fsharp.md) , který používá vazba. Funkce protokoly těla aktivační událost centra událostí.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru. První příklad je určený pro funkce 1.x a druhý je pro službu Functions 2.x. 

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

### <a name="trigger---javascript-example"></a>Aktivační události – příklad v jazyce JavaScript

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce přečte [metadat události](#trigger---event-metadata) a zaprotokoluje zprávu.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru. První příklad je určený pro funkce 1.x a druhý je pro službu Functions 2.x. 

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

Chcete-li přijímat události v dávce, nastavte `cardinality` k `many` v *function.json* souboru, jak je znázorněno v následujícím příkladu. První příklad je určený pro funkce 1.x a druhý je pro službu Functions 2.x. 

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
    
    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---java-example"></a>Aktivační události – příklad v jazyce Java

Následující příklad ukazuje vazby v aktivační událost centra událostí *function.json* souboru a [Java funkce](functions-reference-java.md) , který používá vazba. Funkce protokoly těla triggeru centra událostí.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context ) 
       {
          context.getLogger().info(message);
 }
 ```

 V [Java funkce knihovny prostředí runtime](/java/api/overview/azure/functions/runtime), použijte `EventHubTrigger` poznámku o parametrech, jehož hodnota bude pocházet z centra událostí. Parametry těchto poznámek způsobit funkce spustit, když dorazí událost.  Tato poznámka je možné s nativní typy v jazyce Java, objektů Pojo nebo s povolenou hodnotou Null hodnoty pomocí nepovinné<T>. 

## <a name="trigger---attributes"></a>Aktivační události – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs) atribut.

Konstruktor atributu má název centra událostí, název skupiny uživatelů a název nastavení aplikace, které obsahuje připojovací řetězec. Další informace o těchto nastaveních najdete v tématu [aktivovat konfigurační oddíl](#trigger---configuration). Tady je `EventHubTriggerAttribute` příkladu atribut:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Kompletní příklad naleznete v tématu [Trigger – C# příklad](#trigger---c-example).

## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `EventHubTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastaveno na `eventHubTrigger`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastaveno na `in`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje položku událost v kódu funkce. | 
|**Cesta** |**EventHubName** | Funguje pouze 1.x. Název centra událostí. Pokud název centra událostí je také k dispozici v připojovacím řetězci, přepíše tuto hodnotu této vlastnosti v době běhu. | 
|**eventHubName** |**EventHubName** | Funguje pouze 2.x. Název centra událostí. Pokud název centra událostí je také k dispozici v připojovacím řetězci, přepíše tuto hodnotu této vlastnosti v době běhu. |
|**consumerGroup** |**ConsumerGroup** | Volitelná vlastnost, která nastavuje [skupinu příjemců](../event-hubs/event-hubs-features.md#event-consumers) používá k přihlášení k odběru událostí v centru. Pokud tento parametr vynechán, `$Default` skupina uživatelů se používá. | 
|**Kardinalita** | neuvedeno | Pro jazyk Javascript. Nastavte na `many` Chcete-li povolit dávkové zpracování.  Pokud tento parametr vynechán, nebo nastavte `one`, funkci byl předán jedné zprávy. | 
|**připojení** |**připojení** | Název nastavení aplikace, které obsahuje připojovací řetězec pro obor názvů centra událostí. Zkopírovat tento připojovací řetězec kliknutím **informace o připojení** tlačítko pro [obor názvů](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), nikoli samotného centra událostí. Tento připojovací řetězec musí mít alespoň oprávnění ke čtení pro aktivaci triggeru.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Aktivační události – metadat události

Trigger služby Event Hubs nabízí několik [vlastnosti metadat](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Tyto vlastnosti lze použít jako součást výrazy vazby v jiných vazbách nebo jako parametry v kódu. Toto jsou vlastnosti [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) třídy.

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` Instance.|
|`EnqueuedTimeUtc`|`DateTime`|Čas zařazení do fronty ve standardu UTC.|
|`Offset`|`string`|Posun dat vzhledem ke streamu oddílu centra událostí. Posun je značky nebo identifikátor pro události v rámci datového proudu Event Hubs. Identifikátor je jedinečný v rámci oddílu datového proudu Event Hubs.|
|`PartitionKey`|`string`|Oddíl, na které události mají být odesílána data.|
|`Properties`|`IDictionary<String,Object>`|Vlastnosti uživatele dat událostí.|
|`SequenceNumber`|`Int64`|Logické pořadové číslo události.|
|`SystemProperties`|`IDictionary<String,Object>`|Vlastnosti systému, včetně dat událostí.|

Zobrazit [příklady kódu](#trigger---example) , které používají tyto vlastnosti dříve v tomto článku.

## <a name="trigger---hostjson-properties"></a>Aktivační události – vlastnosti host.json

[Host.json](functions-host-json.md#eventhub) soubor obsahuje nastavení, která řídí chování trigger služby Event Hubs.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Výstup

Pomocí služby Event Hubs výstupní vazbu zapsat události do datového proudu událostí. Musíte mít oprávnění Odeslat do centra událostí zapsat události do něj.

Ujistěte se odkazy na požadované balíčky jsou na místě: [funkce 1.x](#packages---functions-1.x) nebo [funkce 2.x](#packages---functions-2.x) 

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Výstup – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který zapíše zprávu do centra událostí pomocí návratová hodnota metody jako výstup:

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

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce zapíše zprávu do centra událostí.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru. První příklad je určený pro funkce 1.x a druhý je pro službu Functions 2.x. 

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

Tady je kód jazyka C# skript, který vytváří jednu zprávu:

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Tady je kód jazyka C# skript, který vytváří více zpráv:

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

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [funkce jazyka F #](functions-reference-fsharp.md) , který používá vazba. Funkce zapíše zprávu do centra událostí.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru. První příklad je určený pro funkce 1.x a druhý je pro službu Functions 2.x. 

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

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce zapíše zprávu do centra událostí.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru. První příklad je určený pro funkce 1.x a druhý je pro službu Functions 2.x. 

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

Tady je kód jazyka JavaScript, která odesílá do jedné zprávy:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Tady je kód jazyka JavaScript, která odesílá zprávy více:

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

### <a name="output---java-example"></a>Výstup – příklad v jazyce Java

Následující příklad ukazuje funkci Java, která zapisuje zprávy umístí aktuální čas do centra událostí.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
 ```

V [Java funkce knihovny prostředí runtime](/java/api/overview/azure/functions/runtime), použijte `@EventHubOutput` poznámku o parametrech, jehož hodnota bude poublished do centra událostí.  Parametr musí být typu `OutputBinding<T>` , kde T je objekt POJO nebo nativní typ jazyka Java. 

## <a name="output---attributes"></a>Výstup – atributy

Pro [knihoven tříd C#](functions-dotnet-class-library.md), použijte [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) atribut.

Konstruktor atributu má název centra událostí a název nastavení aplikace, které obsahuje připojovací řetězec. Další informace o těchto nastaveních najdete v tématu [výstup - konfigurace](#output---configuration). Tady je `EventHub` příkladu atribut:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Kompletní příklad naleznete v tématu [výstup – příklad v jazyce C#](#output---c-example).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `EventHub` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavena na "eventHub". |
|**direction** | neuvedeno | Musí být nastavena na "out". Tento parametr je nastavena automaticky při vytváření vazby na webu Azure Portal. |
|**Jméno** | neuvedeno | Název této proměnné v kódu funkce, která představuje událost. | 
|**Cesta** |**EventHubName** | Funguje pouze 1.x. Název centra událostí. Pokud název centra událostí je také k dispozici v připojovacím řetězci, přepíše tuto hodnotu této vlastnosti v době běhu. | 
|**eventHubName** |**EventHubName** | Funguje pouze 2.x. Název centra událostí. Pokud název centra událostí je také k dispozici v připojovacím řetězci, přepíše tuto hodnotu této vlastnosti v době běhu. |
|**připojení** |**připojení** | Název nastavení aplikace, které obsahuje připojovací řetězec pro obor názvů centra událostí. Zkopírovat tento připojovací řetězec kliknutím **informace o připojení** tlačítko pro *obor názvů*, nikoli samotného centra událostí. Tento připojovací řetězec musí mít oprávnění k odesílání k odeslání zprávy do datového proudu událostí.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

V jazyce C# a skript jazyka C#, odesílání zpráv s použitím parametru metody, jako `out string paramName`. Ve skriptu jazyka C# `paramName` je zadaná hodnota v `name` vlastnost *function.json*. Pro zápis zpráv více, můžete použít `ICollector<string>` nebo `IAsyncCollector<string>` místo `out string`.

V jazyce JavaScript, k výstupní událost s využitím `context.bindings.<name>`. `<name>` je zadaná hodnota v `name` vlastnost *function.json*.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Centrum událostí | [Provozní příručka](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
