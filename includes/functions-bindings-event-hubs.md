---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 0880d60f9cc7ca989194a98d96f9d5f118f028d0
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71692026"
---
## <a name="trigger"></a>Trigger

Pomocí triggeru funkce můžete reagovat na událost odeslanou do datového proudu událostí centra událostí. K nastavení triggeru musíte mít přístup pro čtení k základnímu centru událostí. Když je funkce aktivována, zpráva předaná funkci je zapsána jako řetězec.

## <a name="trigger---scaling"></a>Aktivační událost – škálování

Každá instance funkce aktivované událostmi je zajištěna jednou instancí [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) . Aktivační událost (s podporou Event Hubs) zajišťuje, že v daném oddílu může získat zapůjčení jenom jedna instance [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) .

Můžete například zvážit centrum událostí následujícím způsobem:

* 10 oddílů
* 1 000 událostí distribuovaně napříč všemi oddíly a 100 zprávy v každém oddílu

Je-li funkce nejprve povolena, je k dispozici pouze jedna instance funkce. Pojďme zavolat první instanci funkce `Function_0`. Funkce `Function_0` má jednu instanci [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) , která má zapůjčení na všech deseti oddílech. Tato instance čte události z oddílů 0-9. Od této chvíle nastane jedna z následujících možností:

* **Nové instance funkcí nejsou potřeba**: `Function_0` je možné zpracovat všechny události 1 000 předtím, než se logika škálování funkcí projeví. V tomto případě jsou všechny zprávy 1 000 zpracovávány pomocí `Function_0`.

* **Přidala se další instance funkce**: Pokud logika škálování funkcí určuje, že `Function_0` obsahuje více zpráv, než je možné zpracovat, vytvoří se nová instance aplikace Function app (`Function_1`). Tato nová funkce má také přidruženou instanci [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Vzhledem k tomu, že základní Event Hubs zjišťují, že se nová instance hostitele pokouší číst zprávy, zatížení vyrovnává všechny oddíly v rámci svých instancí hostitelů. Například oddíly 0-4 mohou být přiřazeny `Function_0` a oddílům 5-9 až `Function_1`.

* **N jsou přidané další instance funkcí**: Pokud logika škálování funkcí určuje, že `Function_0` i `Function_1` mají více zpráv, než je možné zpracovat, vytvoří se nové instance aplikace funkce `Functions_N`.  Aplikace jsou vytvořeny do bodu, kde `N` je větší než počet oddílů centra událostí. V našem příkladu Event Hubs znovu načítat oddíly, v tomto případě u instancí `Function_0`... `Functions_9`.

Když se funkce škálují, `N` instancí je číslo větší než počet oddílů centra událostí. K tomu je potřeba zajistit, aby instance [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) byly k dispozici pro získání zámků na oddílech, jakmile budou k dispozici z jiných instancí. Účtují se vám jenom prostředky používané při spuštění instance funkce. Jinými slovy, toto nadměrné zřizování se vám neúčtují.

Po dokončení všech spuštění funkce (s chybami nebo bez nich) se do přidruženého účtu úložiště přidají kontrolní body. Po úspěšném vrácení se změnami se nebudou všechny zprávy 1 000 znovu načíst.

## <a name="trigger---example"></a>Aktivační procedura – příklad

Podívejte se na příklad konkrétního jazyka:

* [C#](#trigger---c-example)
* [C#skript (. csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Aktivační procedura C# – příklad

Následující příklad ukazuje [ C# funkci](../articles/azure-functions/functions-dotnet-class-library.md) , která zaznamená tělo zprávy triggeru centra událostí.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Chcete-li získat přístup k [metadatům události](#trigger---event-metadata) v kódu funkce, připojte se k objektu [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (vyžaduje příkaz using pro `Microsoft.Azure.EventHubs`). Ke stejným vlastnostem můžete přistupovat také pomocí výrazů vazeb v signatuře metody.  Následující příklad ukazuje dva způsoby, jak získat stejná data:

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

Chcete-li přijímat události v dávce, nastavte `string` nebo `EventData` pole.  

> [!NOTE]
> Při příjmu v dávce nelze vytvořit vazby k parametrům metody, jako v předchozím příkladu, pomocí `DateTime enqueuedTimeUtc` a získat je z každého objektu `EventData`.  

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

### <a name="trigger---c-script-example"></a>Aktivační procedura C# – příklad skriptu

Následující příklad ukazuje aktivační vazbu centra událostí v souboru *Function. JSON* a [ C# funkci skriptu](../articles/azure-functions/functions-reference-csharp.md) , která používá vazbu. Funkce zaznamená text zprávy triggeru centra událostí.

Následující příklady ukazují Event Hubs vázání dat v souboru *Function. JSON* .

#### <a name="version-2x"></a>Verze 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Verze 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Tady je kód C# skriptu:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Chcete-li získat přístup k [metadatům události](#trigger---event-metadata) v kódu funkce, připojte se k objektu [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (vyžaduje příkaz using pro `Microsoft.Azure.EventHubs`). Ke stejným vlastnostem můžete přistupovat také pomocí výrazů vazeb v signatuře metody.  Následující příklad ukazuje dva způsoby, jak získat stejná data:

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

Chcete-li přijímat události v dávce, nastavte `string` nebo `EventData` pole:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Aktivační procedura F# – příklad

Následující příklad ukazuje aktivační vazbu centra událostí v souboru *Function. JSON* a [ F# funkci](../articles/azure-functions/functions-reference-fsharp.md) , která používá vazbu. Funkce zaznamená text zprávy triggeru centra událostí.

Následující příklady ukazují Event Hubs vázání dat v souboru *Function. JSON* . 

#### <a name="version-2x"></a>Verze 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Verze 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Zde je F# kód:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Trigger – příklad JavaScriptu

Následující příklad ukazuje aktivační vazbu centra událostí v souboru *Function. JSON* a [funkci JavaScriptu](../articles/azure-functions/functions-reference-node.md) , která používá vazbu. Funkce přečte [metadata události](#trigger---event-metadata) a zaprotokoluje zprávu.

Následující příklady ukazují Event Hubs vázání dat v souboru *Function. JSON* .

#### <a name="version-2x"></a>Verze 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Verze 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Tady je kód JavaScriptu:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Chcete-li přijímat události v dávce, nastavte `cardinality` na `many` v souboru *Function. JSON* , jak je znázorněno v následujících příkladech.

#### <a name="version-2x"></a>Verze 2. x

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

#### <a name="version-1x"></a>Verze 1. x

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

Tady je kód JavaScriptu:

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

### <a name="trigger---python-example"></a>Trigger – příklad Pythonu

Následující příklad ukazuje aktivační vazbu centra událostí v souboru *Function. JSON* a [funkci Pythonu](../articles/azure-functions/functions-reference-python.md) , která používá vazbu. Funkce přečte [metadata události](#trigger---event-metadata) a zaprotokoluje zprávu.

Následující příklady ukazují Event Hubs vázání dat v souboru *Function. JSON* .

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

### <a name="trigger---java-example"></a>Trigger – příklad Java

Následující příklad ukazuje aktivační vazbu centra událostí v souboru *Function. JSON* a [funkci jazyka Java](../articles/azure-functions/functions-reference-java.md) , která používá vazbu. Funkce zaznamená text zprávy triggeru centra událostí.

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

 V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte anotaci `EventHubTrigger` u parametrů, jejichž hodnota by pocházela z centra událostí. Parametry s těmito poznámkami způsobí, že se funkce spustí při přijetí události.  Tuto poznámku lze použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí volitelného @ no__t-0T >.

## <a name="trigger---attributes"></a>Aktivační atributy

V [ C# knihovnách tříd](../articles/azure-functions/functions-dotnet-class-library.md)použijte atribut [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) .

Konstruktor atributu přebírá název centra událostí, název skupiny příjemců a název nastavení aplikace, které obsahuje připojovací řetězec. Další informace o těchto nastaveních najdete v [části Konfigurace aktivační události](#trigger---configuration). Tady je příklad atributu `EventHubTriggerAttribute`:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Úplný příklad najdete v tématu [Trigger – C# příklad](#trigger---c-example).

## <a name="trigger---configuration"></a>Aktivační událost – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `EventHubTrigger`.

|Function. JSON – vlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | – | Musí být nastavené na `eventHubTrigger`. Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal.|
|**direction** | – | Musí být nastavené na `in`. Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal. |
|**Jméno** | – | Název proměnné, která představuje položku události v kódu funkce. |
|**dílčí** |**EventHubName** | Pouze funkce 1. x. Název centra událostí Pokud je v připojovacím řetězci přítomen i název centra událostí, tato hodnota tuto vlastnost Přepisuje za běhu. |
|**eventHubName** |**EventHubName** | Pouze funkce 2. x. Název centra událostí Pokud je v připojovacím řetězci přítomen i název centra událostí, tato hodnota tuto vlastnost Přepisuje za běhu. |
|**Klientská organizace** |**Klientská organizace** | Volitelná vlastnost, která nastaví [skupinu uživatelů](../articles/event-hubs/event-hubs-features.md#event-consumers) použitou k přihlášení k odběru událostí v centru. Je-li tento parametr vynechán, je použita skupina uživatelů `$Default`. |
|**kardinalita** | – | Pro JavaScript. Pokud chcete povolit dávkování, nastavte `many`.  Je-li tento parametr vynechán, nebo je nastaven na hodnotu `one`, jedna zpráva byla předána funkci. |
|**vázán** |**Vázán** | Název nastavení aplikace, které obsahuje připojovací řetězec k oboru názvů centra událostí. Zkopírujte tento připojovací řetězec kliknutím na tlačítko **informace o připojení** pro [obor názvů](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), nikoli v samotném centru událostí. Tento připojovací řetězec musí mít aspoň oprávnění ke čtení pro aktivaci triggeru.|
|**dílčí**|**EventHubName**|Název centra událostí Dá se odkazovat pomocí nastavení aplikace `%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Trigger – metadata události

Aktivační událost Event Hubs poskytuje několik [vlastností metadat](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Tyto vlastnosti lze použít jako součást výrazů vazby v jiných vazbách nebo jako parametry v kódu. Jedná se o vlastnosti třídy [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) .

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Instance `PartitionContext`|
|`EnqueuedTimeUtc`|`DateTime`|Čas zařazení do fronty ve standardu UTC.|
|`Offset`|`string`|Posun dat vzhledem ke streamu oddílu centra událostí. Posun je značka nebo identifikátor události v rámci Event Hubsho datového proudu. Identifikátor je jedinečný v rámci oddílu Event Hubsho datového proudu.|
|`PartitionKey`|`string`|Oddíl, do kterého mají být odeslána data událostí.|
|`Properties`|`IDictionary<String,Object>`|Vlastnosti uživatele dat události.|
|`SequenceNumber`|`Int64`|Číslo logické sekvence události|
|`SystemProperties`|`IDictionary<String,Object>`|Vlastnosti systému, včetně dat události.|

Podívejte se na [Příklady kódu](#trigger---example) , které používají tyto vlastnosti dříve v tomto článku.

## <a name="trigger---hostjson-properties"></a>Trigger – vlastnosti Host. JSON

Soubor [Host. JSON](../articles/azure-functions/functions-host-json.md#eventhub) obsahuje nastavení, která řídí chování triggeru Event Hubs.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Výstup

Použijte výstupní vazbu Event Hubs k zápisu událostí do datového proudu událostí. K zápisu událostí do centra událostí musíte mít oprávnění Odeslat.

Ujistěte se, že jsou na místě požadované odkazy na balíčky: Functions 1. x nebo functions 2. x.

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad konkrétního jazyka:

* [C#](#output---c-example)
* [C#skript (. csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Výstup – C# příklad

Následující příklad ukazuje [ C# funkci](../articles/azure-functions/functions-dotnet-class-library.md) , která zapisuje zprávu do centra událostí pomocí návratové hodnoty metody jako výstup:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Následující příklad ukazuje, jak pomocí rozhraní @no__t 0 odeslat dávku zpráv. Tento scénář je běžný při zpracování zpráv přicházejících z jednoho centra událostí a odeslání výsledku do jiného centra událostí.

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

### <a name="output---c-script-example"></a>Ukázka výstupního C# skriptu

Následující příklad ukazuje aktivační vazbu centra událostí v souboru *Function. JSON* a [ C# funkci skriptu](../articles/azure-functions/functions-reference-csharp.md) , která používá vazbu. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují Event Hubs vázání dat v souboru *Function. JSON* . První příklad je pro Functions 2. x a druhý je pro Functions 1. x. 

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

Zde je C# kód skriptu, který vytváří jednu zprávu:

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

Zde je C# kód skriptu, který vytváří více zpráv:

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

Následující příklad ukazuje aktivační vazbu centra událostí v souboru *Function. JSON* a [ F# funkci](../articles/azure-functions/functions-reference-fsharp.md) , která používá vazbu. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují Event Hubs vázání dat v souboru *Function. JSON* . První příklad je pro Functions 2. x a druhý je pro Functions 1. x. 

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

Zde je F# kód:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Výstup – příklad JavaScriptu

Následující příklad ukazuje aktivační vazbu centra událostí v souboru *Function. JSON* a [funkci JavaScriptu](../articles/azure-functions/functions-reference-node.md) , která používá vazbu. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují Event Hubs vázání dat v souboru *Function. JSON* . První příklad je pro Functions 2. x a druhý je pro Functions 1. x. 

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

### <a name="output---python-example"></a>Výstup – příklad Pythonu

Následující příklad ukazuje aktivační vazbu centra událostí v souboru *Function. JSON* a [funkci Pythonu](../articles/azure-functions/functions-reference-python.md) , která používá vazbu. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují Event Hubs vázání dat v souboru *Function. JSON* .

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

### <a name="output---java-example"></a>Výstup – příklad Java

Následující příklad ukazuje funkci jazyka Java, která zapisuje zprávu contianing aktuálním časem do centra událostí.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte pro parametry, jejichž hodnota by byla publikována do centra událostí, anotaci `@EventHubOutput`.  Parametr by měl být typu `OutputBinding<T>`, kde T je POJO nebo jakýkoli nativní typ Java.

## <a name="output---attributes"></a>Výstupní atributy

Pro [ C# knihovny tříd](../articles/azure-functions/functions-dotnet-class-library.md)použijte atribut [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) .

Konstruktor atributu přebírá název centra událostí a název nastavení aplikace, které obsahuje připojovací řetězec. Další informace o těchto nastaveních naleznete v tématu [Output-Configuration](#output---configuration). Tady je příklad atributu `EventHub`:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v tématu [Output- C# example](#output---c-example).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `EventHub`.

|Function. JSON – vlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | – | Musí být nastavené na eventHub. |
|**direction** | – | Musí být nastavené na "out". Tento parametr je nastaven automaticky při vytváření vazby v Azure Portal. |
|**Jméno** | – | Název proměnné použitý v kódu funkce, který představuje událost. |
|**dílčí** |**EventHubName** | Pouze funkce 1. x. Název centra událostí Pokud je v připojovacím řetězci přítomen i název centra událostí, tato hodnota tuto vlastnost Přepisuje za běhu. |
|**eventHubName** |**EventHubName** | Pouze funkce 2. x. Název centra událostí Pokud je v připojovacím řetězci přítomen i název centra událostí, tato hodnota tuto vlastnost Přepisuje za běhu. |
|**vázán** |**Vázán** | Název nastavení aplikace, které obsahuje připojovací řetězec k oboru názvů centra událostí. Zkopírujte tento připojovací řetězec kliknutím na tlačítko **informace o připojení** pro *obor názvů*, nikoli v samotném centru událostí. Tento připojovací řetězec musí mít oprávnění Odeslat pro odeslání zprávy do datového proudu událostí.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

Do C# skriptu C# a zasílejte zprávy pomocí parametru metody, jako je například `out string paramName`. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *funkce Function. JSON*. Chcete-li zapsat více zpráv, můžete místo `out string` použít `ICollector<string>` nebo `IAsyncCollector<string>`.

V jazyce JavaScript přejděte k události výstup pomocí `context.bindings.<name>`. `<name>` je hodnota zadaná ve vlastnosti `name` *funkce Function. JSON*.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Ovládacího | Referenční informace |
|---|---|
| Centrum událostí | [Provozní příručka](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení Host. JSON

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzi 2. x. Ukázkový soubor host. JSON níže obsahuje pouze nastavení verze 2. x pro tuto vazbu. Další informace o globálních nastaveních konfigurace verze 2. x naleznete v tématu [reference Host. JSON pro Azure Functions verze 2. x](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Odkaz na Host. JSON ve funkcích 1. x najdete v [referenčních informacích k host. JSON pro Azure Functions 1. x](../articles/azure-functions/functions-host-json-v1.md).

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
|maxBatchSize|64|Maximální počet událostí přijatých pro jednu smyčku příjmu.|
|prefetchCount|–|Výchozí PrefetchCount, které bude používat základní EventProcessorHost.|
|batchCheckpointFrequency|1\. místo|Počet dávek události, které mají být zpracovány před vytvořením kontrolního bodu centra EventHub.|
