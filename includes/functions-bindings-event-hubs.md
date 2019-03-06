---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 1957fa4310a22a162ee2a621d1e0349e253badb3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456563"
---
## <a name="trigger"></a>Trigger

Použití aktivační procedury funkce reagovat na událost odeslaná do datového proudu událostí centra událostí. Musíte mít přístup pro čtení k podkladové centra událostí, a nastavit aktivační událost. Když se aktivuje funkci, je zpráva předaná funkci typu řetězec.

## <a name="trigger---scaling"></a>Aktivovat – škálování

Každá instance funkce byla aktivována událost se zálohuje pomocí jediného [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instance. Aktivační události (s využitím služby Event Hubs) zajišťuje, že pouze jedna [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instance můžete získat zapůjčení v daném oddílu.

Představte si třeba v Centru událostí následujícím způsobem:

* 10 oddíly
* 1 000 událostí rovnoměrně mezi všechny oddíly s 100 zprávy v každém oddílu

Když vaše funkce je nejprve povolené, existuje pouze jedna instance funkce. Pojmenujme první instance funkce `Function_0`. `Function_0` Funkce má jednu instanci [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) , který drží zapůjčení na všechny oddíly deset. Tato instance je čtení události ze oddíly 0 – 9. Od této chvíle se stane něco z tohoto toto:

* **Nové instance funkce nejsou potřeba**: `Function_0` je schopná zpracovat všechny 1 000 událostí funkce škálování logiky projeví. V tomto případě jsou zpracovány všechny 1 000 zpráv `Function_0`.

* **Přidat další funkce instance**: Pokud funkce škálování logiku, která určuje `Function_0` má víc zpráv, než dokáže zpracovat, nová instance aplikace – funkce (`Function_1`) se vytvoří. Tato nová funkce má také přidruženou instanci [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Protože základní Event Hubs zjistit, že nové instance hostitele se pokouší číst zprávy, vyrovnává zatížení oddílů mezi její instance hostitele. Například oddíly 0-4 přiřazeni k `Function_0` a oddíly 5 až 9 k `Function_1`.

* **N přidají další instance funkce**: Pokud funkce škálování logiku, která určuje obě `Function_0` a `Function_1` mít víc zpráv, než se může zpracovat, nové `Functions_N` se vytvářejí instance aplikace funkce.  Aplikace se vytvářejí na bod kam `N` je větší než počet oddílů centra událostí. V našem příkladu Event Hubs znovu vyrovnává zatížení oddílů, v tomto případě mezi instancemi `Function_0`... `Functions_9`.

Když funkce škálování, `N` instance je číslo větší než počet oddílů centra událostí. To slouží k zajištění [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instance je možné získat zámky u oddílů, jakmile budou dostupné z jiných instancí. Pouze bude vám účtována prostředky používané při spustí instanci funkce. Jinými slovy se vám neúčtují poplatky pro toto bylo potřeba zřizovat.

Po dokončení všech spuštění funkce (s nebo bez chyb) kontrolní body se přidají do přidruženého účtu úložiště. Pokud bodového proběhne úspěšně, všechny 1 000 zpráv se nikdy načíst znovu.

## <a name="trigger---example"></a>Aktivační události – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Aktivační události – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](../articles/azure-functions/functions-dotnet-class-library.md) , která protokoluje těla aktivační událost centra událostí.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Získat přístup k [metadat události](#trigger---event-metadata) v kódu funkce svázat [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektu (vyžaduje sadu pomocí příkazu pro `Microsoft.Azure.EventHubs`). Můžete také přistupovat k stejné vlastnosti pomocí vazbové výrazy v podpisu metody.  Následující příklad ukazuje oba způsoby, jak získat stejná data:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Ujistěte se, pokud chcete přijímat události v dávce, `string` nebo `EventData` pole.  

> [!NOTE]
> Při přijetí v dávce nelze vytvořit vazbu k parametrům metody jako ve výše uvedeném příkladu s `DateTime enqueuedTimeUtc` a musí přijímat z každé `EventData` objektu  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>Aktivační události – příklad skriptu jazyka C#

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [funkce skriptu jazyka C#](../articles/azure-functions/functions-reference-csharp.md) , který používá vazba. Funkce protokoly těla aktivační událost centra událostí.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru.

#### <a name="version-2x"></a>Verze 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Verzi 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Tady je kód skriptu jazyka C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Získat přístup k [metadat události](#trigger---event-metadata) v kódu funkce svázat [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektu (vyžaduje sadu pomocí příkazu pro `Microsoft.Azure.EventHubs`). Můžete také přistupovat k stejné vlastnosti pomocí vazbové výrazy v podpisu metody.  Následující příklad ukazuje oba způsoby, jak získat stejná data:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

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
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Aktivační události – F# příklad

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [ F# funkce](../articles/azure-functions/functions-reference-fsharp.md) , který používá vazba. Funkce protokoly těla aktivační událost centra událostí.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru. 

#### <a name="version-2x"></a>Verze 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Verzi 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Tady je F# kódu:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Aktivační události – příklad v jazyce JavaScript

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [funkce jazyka JavaScript](../articles/azure-functions/functions-reference-node.md) , který používá vazba. Funkce přečte [metadat události](#trigger---event-metadata) a zaprotokoluje zprávu.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru.

#### <a name="version-2x"></a>Verze 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Verzi 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Chcete-li přijímat události v dávce, nastavte `cardinality` k `many` v *function.json* souboru, jak je znázorněno v následujícím příkladu.

#### <a name="version-2x"></a>Verze 2.x

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

#### <a name="version-1x"></a>Verzi 1.x

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

### <a name="trigger---python-example"></a>Aktivační události – příklad v Pythonu

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [funkce Pythonu](../articles/azure-functions/functions-reference-python.md) , který používá vazba. Funkce přečte [metadat události](#trigger---event-metadata) a zaprotokoluje zprávu.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Tady je kód Pythonu:

```python
import logging
import azure.functions as func

def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>Aktivační události – příklad v jazyce Java

Následující příklad ukazuje vazby v aktivační událost centra událostí *function.json* souboru a [Java funkce](../articles/azure-functions/functions-reference-java.md) , který používá vazba. Funkce protokoly těla triggeru centra událostí.

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

V [knihoven tříd C#](../articles/azure-functions/functions-dotnet-class-library.md), použijte [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) atribut.

Konstruktor atributu má název centra událostí, název skupiny uživatelů a název nastavení aplikace, které obsahuje připojovací řetězec. Další informace o těchto nastaveních najdete v tématu [aktivovat konfigurační oddíl](#trigger---configuration). Tady je `EventHubTriggerAttribute` příkladu atribut:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
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
|**consumerGroup** |**ConsumerGroup** | Volitelná vlastnost, která nastavuje [skupinu příjemců](../articles/event-hubs/event-hubs-features.md)#-příjemci event) používá k přihlášení k odběru událostí v centru. Pokud tento parametr vynechán, `$Default` skupina uživatelů se používá. |
|**Kardinalita** | neuvedeno | Pro jazyk Javascript. Nastavte na `many` Chcete-li povolit dávkové zpracování.  Pokud tento parametr vynechán, nebo nastavte `one`, funkci byl předán jedné zprávy. |
|**připojení** |**připojení** | Název nastavení aplikace, které obsahuje připojovací řetězec pro obor názvů centra událostí. Zkopírovat tento připojovací řetězec kliknutím **informace o připojení** tlačítko pro [obor názvů](../articles/event-hubs/event-hubs-create.md)#create--hubs-obor názvů služby event), nikoli samotného centra událostí. Tento připojovací řetězec musí mít alespoň oprávnění ke čtení pro aktivaci triggeru.|
|**Cesta**|**EventHubName**|Název centra událostí. Může být odkazováno prostřednictvím nastavení aplikace `%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Aktivační události – metadat události

Trigger služby Event Hubs nabízí několik [vlastnosti metadat](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Tyto vlastnosti lze použít jako součást výrazy vazby v jiných vazbách nebo jako parametry v kódu. Toto jsou vlastnosti [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) třídy.

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

[Host.json](../articles/azure-functions/functions-host-json.md#eventhub) soubor obsahuje nastavení, která řídí chování trigger služby Event Hubs.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Výstup

Pomocí služby Event Hubs výstupní vazbu zapsat události do datového proudu událostí. Musíte mít oprávnění Odeslat do centra událostí zapsat události do něj.

Ujistěte se, odkazy na požadované balíčky jsou na místě: Funkce 1.x a 2.x funkce

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Výstup – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](../articles/azure-functions/functions-dotnet-class-library.md) , který zapíše zprávu do centra událostí pomocí návratová hodnota metody jako výstup:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Výstup – příklad skriptu jazyka C#

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [funkce skriptu jazyka C#](../articles/azure-functions/functions-reference-csharp.md) , který používá vazba. Funkce zapíše zprávu do centra událostí.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru. První příklad je určený pro funkce 2.x, a druhý je pro službu Functions 1.x. 

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

Tady je kód jazyka C# skript, který vytváří jednu zprávu:

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

Tady je kód jazyka C# skript, který vytváří více zpráv:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Výstup – F# příklad

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [ F# funkce](../articles/azure-functions/functions-reference-fsharp.md) , který používá vazba. Funkce zapíše zprávu do centra událostí.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru. První příklad je určený pro funkce 2.x, a druhý je pro službu Functions 1.x. 

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

Tady je F# kódu:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Výstup – příklad v jazyce JavaScript

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [funkce jazyka JavaScript](../articles/azure-functions/functions-reference-node.md) , který používá vazba. Funkce zapíše zprávu do centra událostí.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru. První příklad je určený pro funkce 2.x, a druhý je pro službu Functions 1.x. 

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

Tady je kód jazyka JavaScript, která odesílá do jedné zprávy:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Tady je kód jazyka JavaScript, která odesílá zprávy více:

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

### <a name="output---python-example"></a>Výstup – příklad v Pythonu

Následující příklad ukazuje vazby v aktivační procedura událostí centra *function.json* souboru a [funkce Pythonu](../articles/azure-functions/functions-reference-python.md) , který používá vazba. Funkce zapíše zprávu do centra událostí.

Následující příklady znázorňují data vazby služby Event Hubs v *function.json* souboru.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Tady je kód Pythonu, která odesílá do jedné zprávy:

```python
import datetime
import logging
import azure.functions as func

def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp);   
    return 'Message created at: {}'.format(timestamp)
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

V [Java funkce knihovny prostředí runtime](/java/api/overview/azure/functions/runtime), použijte `@EventHubOutput` poznámku o parametrech, jehož hodnota se budou publikovat do centra událostí.  Parametr musí být typu `OutputBinding<T>` , kde T je objekt POJO nebo nativní typ jazyka Java.

## <a name="output---attributes"></a>Výstup – atributy

Pro [knihoven tříd C#](../articles/azure-functions/functions-dotnet-class-library.md), použijte [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) atribut.

Konstruktor atributu má název centra událostí a název nastavení aplikace, které obsahuje připojovací řetězec. Další informace o těchto nastaveních najdete v tématu [výstup - konfigurace](#output---configuration). Tady je `EventHub` příkladu atribut:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
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

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

V jazyce C# a skript jazyka C#, odesílání zpráv s použitím parametru metody, jako `out string paramName`. Ve skriptu jazyka C# `paramName` je zadaná hodnota v `name` vlastnost *function.json*. Pro zápis zpráv více, můžete použít `ICollector<string>` nebo `IAsyncCollector<string>` místo `out string`.

V jazyce JavaScript, k výstupní událost s využitím `context.bindings.<name>`. `<name>` je zadaná hodnota v `name` vlastnost *function.json*.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Centrum událostí | [Provozní příručka](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení Host.JSON

Tato část popisuje globální konfiguraci nastavení k dispozici pro tuto vazbu ve verzi 2.x. Příklad souboru host.json níže obsahuje pouze verzi 2.x nastavení pro tuto vazbu. Další informace o globální nastavení konfigurace ve verzi 2.x, naleznete v tématu [referenční materiály k host.json pro Azure Functions verze 2.x](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Pro odkaz host.json ve funkcích 1.x, najdete v článku [referenční materiály k host.json pro Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md).

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
|maxBatchSize|64|Maximální počet přijatých událostí za smyčka příjmu.|
|prefetchCount|neuvedeno|Výchozí PrefetchCount, který se použije základní třídy EventProcessorHost.|
|batchCheckpointFrequency|1|Počet událostí zpracovávaných dávek před vytvořením kontrolního bodu EventHub kurzoru.|