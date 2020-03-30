---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 438e3166e27511780dd871b5076a7b28ebade052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589711"
---
Aktivační událost funkce slouží k reakci na událost odeslanou do datového proudu událostí centra událostí centra událostí. Chcete-li nastavit aktivační událost, musíte mít přístup pro čtení do podkladového centra událostí. Při spuštění funkce je zpráva předaná funkci zadána jako řetězec.

## <a name="scaling"></a>Škálování

Každá instance funkce spouštěné události je podpořena jedinou instancí [EventProcessorHost.](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) Aktivační událost (poháněná event huby) zajišťuje, že pouze jedna instance [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) může získat zapůjčení na daný oddíl.

Centrum událostí zvažte například takto:

* 10 oddílů
* 1 000 událostí rovnoměrně rozmístěných ve všech oddílech se 100 zprávami v každém oddílu

Pokud je funkce poprvé povolena, existuje pouze jedna instance funkce. Zavolejte první instanci `Function_0`funkce . Funkce `Function_0` má jednu instanci [EventProcessorHost,](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) která obsahuje zapůjčení na všech deseti oddílů. Tato instance je čtení událostí z oddílů 0-9. Od tohoto okamžiku se stane jedna z následujících věcí:

* **Nové instance funkcí nejsou** `Function_0` potřeba : je schopen zpracovat všech 1 000 událostí, než se projeví logika škálování funkcí. V tomto případě jsou všechny 1 000 `Function_0`zpráv zpracovány .

* **Je přidána další instance funkce**: Pokud logika škálování funkcí určuje, že `Function_0` má`Function_1`více zpráv, než může zpracovat, vytvoří se nová instance aplikace funkce ( ). Tato nová funkce má také přidruženou instanci [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Jako základní event huby zjistí, že nová instance hostitele se snaží číst zprávy, vyrovnává oddíly mezi instancemi hostitele. Například oddíly 0-4 mohou `Function_0` být přiřazeny a `Function_1`oddíly 5-9 do .

* **N další instance funkce jsou přidány**: Pokud funkce `Function_0` škálování logiky určuje, že oba a `Function_1` mají více zpráv, než mohou zpracovat, jsou vytvořeny nové `Functions_N` instance aplikace funkce.  Aplikace se vytvářejí do `N` bodu, kde je větší než počet oddílů centra událostí. V našem příkladu centra událostí opět vyrovnává zatížení oddíly, v `Function_0`tomto případě napříč instancemi ... `Functions_9`.

Při škálování dojde, `N` instance je číslo větší než počet oddílů centra událostí. Tento vzor se používá k zajištění [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instance jsou k dispozici získat zámky na oddíly, jakmile budou k dispozici z jiných instancí. Poplatky se vám budou účtovat pouze za prostředky použité při spuštění instance funkce. Jinými slovy, za toto nadměrné zřizování se vám neúčtuje.

Po dokončení spuštění všech funkcí (s chybami nebo bez něj) jsou kontrolní body přidány do přidruženého účtu úložiště. Při směrování zaškrtávací mačká teceúspěšná, všech 1 000 zpráv jsou nikdy načíst znovu.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [c# funkce,](../articles/azure-functions/functions-dotnet-class-library.md) která protokoluje text zprávy aktivační události centra událostí.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Chcete-li získat přístup k [metadatům události](#event-metadata) v kódu funkce, spojte se s objektem [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (vyžaduje příkaz using pro). `Microsoft.Azure.EventHubs` Můžete také přistupovat ke stejným vlastnostem pomocí výrazů vazby v podpisu metody.  Následující příklad ukazuje oba způsoby, jak získat stejná data:

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

Chcete-li přijímat události `string` v `EventData` dávce, vytvořte nebo pole.  

> [!NOTE]
> Při příjmu v dávce nelze vázat na parametry `DateTime enqueuedTimeUtc` metody, jako ve `EventData` výše uvedeném příkladu s a musí přijímat tyto z každého objektu  

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

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje aktivační událost centra událostí v souboru *function.json* a [funkci skriptu Jazyka C#,](../articles/azure-functions/functions-reference-csharp.md) která používá vazbu. Funkce protokoluje text zprávy aktivační události centra událostí.

Následující příklady ukazují data vazby centra událostí v souboru *function.json.*

### <a name="version-2x-and-higher"></a>Verze 2.x a vyšší

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Verze 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Zde je kód skriptu C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Chcete-li získat přístup k [metadatům události](#event-metadata) v kódu funkce, spojte se s objektem [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (vyžaduje příkaz using pro). `Microsoft.Azure.EventHubs` Můžete také přistupovat ke stejným vlastnostem pomocí výrazů vazby v podpisu metody.  Následující příklad ukazuje oba způsoby, jak získat stejná data:

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

Chcete-li přijímat události `string` v `EventData` dávce, vytvořte nebo vytvořte pole:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje vazby aktivační události centra událostí v souboru *function.json* a [funkci JavaScriptu,](../articles/azure-functions/functions-reference-node.md) která vazbu používá. Funkce čte [metadata události](#event-metadata) a protokoluje zprávu.

Následující příklady ukazují data vazby centra událostí v souboru *function.json.*

### <a name="version-2x-and-higher"></a>Verze 2.x a vyšší

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Verze 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Zde je kód JavaScript:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Chcete-li přijímat události `cardinality` v `many` dávce, nastavte v souboru *function.json,* jak je znázorněno v následujících příkladech.

### <a name="version-2x-and-higher"></a>Verze 2.x a vyšší

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

### <a name="version-1x"></a>Verze 1.x

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

Zde je kód JavaScript:

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

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje vazby aktivační události centra událostí v souboru *function.json* a [funkci Pythonu,](../articles/azure-functions/functions-reference-python.md) která používá vazbu. Funkce čte [metadata události](#event-metadata) a protokoluje zprávu.

Následující příklady ukazují data vazby centra událostí v souboru *function.json.*

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Zde je kód Pythonu:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje aktivační událost centra událostí, která zaznamenává text zprávy aktivační události Event Hub.

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

 V [knihovně runtime funkcí javy](/java/api/overview/azure/functions/runtime)použijte poznámku `EventHubTrigger` k parametrům, jejichž hodnota by pocházela z Centra událostí. Parametry s těmito poznámkami způsobit spuštění funkce při příchodu události.  Tuto poznámku lze použít s nativními typy Jazyka Java, `Optional<T>`POJOnebo hodnotami s možnou hodnotou s hodnotou s nulou pomocí .

 ---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd Jazyka C#](../articles/azure-functions/functions-dotnet-class-library.md)použijte atribut [EventHubTriggerAttribute.](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs)

Konstruktor atributu přebírá název centra událostí, název skupiny příjemce a název nastavení aplikace, která obsahuje připojovací řetězec. Další informace o těchto nastaveních naleznete v [části konfigurace aktivační události](#configuration). Zde je `EventHubTriggerAttribute` příklad atributu:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v tématu [Trigger - C# příklad](#example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Z [knihovny runtime funkcí](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)Java použijte anotaci [EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) na parametry, jejichž hodnota by pocházet z Event Hub. Parametry s těmito poznámkami způsobit spuštění funkce při příchodu události. Tuto poznámku lze použít s nativními typy Jazyka Java, `Optional<T>`POJOnebo hodnotami s možnou hodnotou s hodnotou s nulou pomocí .

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `EventHubTrigger` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být `eventHubTrigger`nastavena na . Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal.|
|**direction** | neuvedeno | Musí být `in`nastavena na . Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje položku události v kódu funkce. |
|**Cestu** |**Název EventHubName** | Funkce pouze 1.x. Název centra událostí. Pokud je název centra událostí také přítomen v připojovacím řetězci, tato hodnota přepíše tuto vlastnost za běhu. |
|**eventHubName** |**Název EventHubName** | Funkce 2.x a vyšší. Název centra událostí. Pokud je název centra událostí také přítomen v připojovacím řetězci, tato hodnota přepíše tuto vlastnost za běhu. Lze odkazovat pomocí nastavení aplikace %eventHubName% |
|**consumerGroup** |**Skupina spotřebitelů** | Volitelná vlastnost, která nastavuje [skupinu spotřebitelů,](../articles/event-hubs/event-hubs-features.md#event-consumers) která slouží k přihlášení k odběru událostí v centru. Pokud je vynechána, skupina `$Default` příjemce se používá. |
|**Mohutnost** | neuvedeno | Pro Javascript. Nastavte `many` na chcete-li povolit dávkování.  Pokud je vynechána `one`nebo nastavena na , je funkci předána jedna zpráva. |
|**Připojení** |**Připojení** | Název nastavení aplikace, která obsahuje připojovací řetězec k oboru názvů centra událostí. Zkopírujte tento připojovací řetězec klepnutím na tlačítko **Informace o připojení** pro obor [názvů](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), nikoli na samotné centrum událostí. Tento připojovací řetězec musí mít alespoň oprávnění ke čtení, aby aktivoval aktivační událost.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="event-metadata"></a>Metadata události

Aktivační událost Event Hubs poskytuje několik [vlastností metadat](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Vlastnosti metadat lze použít jako součást výrazy vazby v jiných vazeb nebo jako parametry v kódu. Vlastnosti pocházejí z [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) třídy.

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Instance `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|Doba zařazená do fronty v čase UTC.|
|`Offset`|`string`|Posun dat vzhledem k datovému proudu oddílu centra událostí. Posun je značka nebo identifikátor pro událost v rámci datového proudu Centra událostí. Identifikátor je jedinečný v rámci oddílu datového proudu centra událostí.|
|`PartitionKey`|`string`|Oddíl, do kterého by měla být odeslána data události.|
|`Properties`|`IDictionary<String,Object>`|Vlastnosti uživatele dat události.|
|`SequenceNumber`|`Int64`|Logické pořadové číslo události.|
|`SystemProperties`|`IDictionary<String,Object>`|Vlastnosti systému, včetně dat událostí.|

Viz [příklady kódu,](#example) které používají tyto vlastnosti dříve v tomto článku.

## <a name="hostjson-properties"></a>vlastnosti host.json

Soubor [host.json](../articles/azure-functions/functions-host-json.md#eventhub) obsahuje nastavení, která řídí chování centra událostí.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]