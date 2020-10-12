---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: d8c6b79dca97de3dd46eb9c677f2c94191f276b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89304021"
---
Pomocí triggeru funkce můžete reagovat na událost odeslanou do datového proudu událostí centra událostí. K nastavení triggeru musíte mít přístup pro čtení k základnímu centru událostí. Když je funkce aktivována, zpráva předaná funkci je zapsána jako řetězec.

## <a name="scaling"></a>Škálování

Každá instance funkce aktivované událostmi je zajištěna jednou instancí [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) . Aktivační událost (s podporou Event Hubs) zajišťuje, že v daném oddílu může získat zapůjčení jenom jedna instance [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) .

Můžete například zvážit centrum událostí následujícím způsobem:

* 10 oddílů
* 1 000 událostí distribuovaně napříč všemi oddíly a 100 zprávy v každém oddílu

Je-li funkce nejprve povolena, je k dispozici pouze jedna instance funkce. Pojďme zavolat první instanci funkce `Function_0` . `Function_0`Funkce má jednu instanci [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) , která má zapůjčení na všech deseti oddílech. Tato instance čte události z oddílů 0-9. Od této chvíle nastane jedna z následujících možností:

* **Nové instance funkcí nejsou potřeba**: `Function_0` je možné zpracovat všechny události 1 000 předtím, než se logika škálování funkcí projeví. V tomto případě jsou všechny zprávy 1 000 zpracovávány pomocí `Function_0` .

* **Přidala se další instance funkce**: Pokud logika škálování funkcí určuje, že `Function_0` má víc zpráv, než je možné zpracovat, vytvoří se nová instance aplikace Function App ( `Function_1` ). Tato nová funkce má také přidruženou instanci [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Vzhledem k tomu, že základní Event Hubs zjišťují, že se nová instance hostitele pokouší číst zprávy, načítá vyrovnávání zatížení oddílů v rámci instancí hostitelů. Například oddíly 0-4 mohou být přiřazeny k `Function_0` a oddíly 5-9 až `Function_1` .

* **N je přidaných víc instancí funkcí**: Pokud logika škálování funkcí určuje, že má obojí `Function_0` a `Function_1` má víc zpráv, než můžou zpracovat, `Functions_N` vytvoří se nové instance aplikace Function App.  Aplikace se vytvoří v místě, kde `N` je větší než počet oddílů centra událostí. V našem příkladu Event Hubs znovu vyrovnávání zatížení oddílů v tomto případě napříč instancemi `Function_0` ... `Functions_9` .

V případě, že dojde ke škálování, `N` jsou instance větší než počet oddílů centra událostí. Tento model se používá k zajištění dostupnosti instancí [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) k získání zámků na oddílech, jakmile budou k dispozici z jiných instancí. Účtují se vám jenom prostředky používané při spuštění instance funkce. Jinými slovy, toto nadměrné zřizování se vám neúčtují.

Po dokončení všech spuštění funkce (s chybami nebo bez nich) se do přidruženého účtu úložiště přidají kontrolní body. Po úspěšném vrácení se změnami se nebudou všechny zprávy 1 000 znovu načíst.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](../articles/azure-functions/functions-dotnet-class-library.md) , která zapisuje text zprávy triggeru centra událostí.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Chcete-li získat přístup k [metadatům události](#event-metadata) v kódu funkce, připojte se k objektu [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (vyžaduje příkaz using pro `Microsoft.Azure.EventHubs` ). Ke stejným vlastnostem můžete přistupovat také pomocí výrazů vazeb v signatuře metody.  Následující příklad ukazuje dva způsoby, jak získat stejná data:

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

Chcete-li přijímat události v dávce, udělejte `string` nebo `EventData` Array.  

> [!NOTE]
> Při příjmu v dávce nejde vytvořit vazby k parametrům metody, jako v předchozím příkladu, `DateTime enqueuedTimeUtc` a získat je z každého `EventData` objektu.  

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

Následující příklad ukazuje aktivační vazbu centra událostí v *function.js* souboru a [funkci skriptu jazyka C#](../articles/azure-functions/functions-reference-csharp.md) , která používá vazbu. Funkce zaznamená text zprávy triggeru centra událostí.

Následující příklady ukazují Event Hubs vázání dat v *function.js* souboru.

### <a name="version-2x-and-higher"></a>Verze 2. x a vyšší

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Verze 1. x

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

Chcete-li získat přístup k [metadatům události](#event-metadata) v kódu funkce, připojte se k objektu [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (vyžaduje příkaz using pro `Microsoft.Azure.EventHubs` ). Ke stejným vlastnostem můžete přistupovat také pomocí výrazů vazeb v signatuře metody.  Následující příklad ukazuje dva způsoby, jak získat stejná data:

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

Chcete-li přijímat události v dávce, udělejte `string` nebo `EventData` pole:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje aktivační vazbu centra událostí v *function.js* souboru a [funkci JavaScriptu](../articles/azure-functions/functions-reference-node.md) , která používá vazbu. Funkce přečte [metadata události](#event-metadata) a zaprotokoluje zprávu.

Následující příklady ukazují Event Hubs vázání dat v *function.js* souboru.

### <a name="version-2x-and-higher"></a>Verze 2. x a vyšší

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Verze 1. x

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

Chcete-li přijímat události v dávce, nastavte `cardinality` na `many` *function.js* v souboru, jak je znázorněno v následujících příkladech.

### <a name="version-2x-and-higher"></a>Verze 2. x a vyšší

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

### <a name="version-1x"></a>Verze 1. x

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

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje aktivační vazbu centra událostí v *function.js* souboru a [funkci Pythonu](../articles/azure-functions/functions-reference-python.md) , která používá vazbu. Funkce přečte [metadata události](#event-metadata) a zaprotokoluje zprávu.

Následující příklady ukazují Event Hubs vázání dat v *function.js* souboru.

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

    # Metadata
    for key in event.metadata:
        logging.info(f'Metadata: {key} = ', event.metadata[key])
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje aktivační událost centra událostí, která zapisuje text zprávy triggeru centra událostí.

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

 V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `EventHubTrigger` anotaci pro parametry, jejichž hodnota by pocházela z centra událostí. Parametry s těmito poznámkami způsobí, že se funkce spustí při přijetí události.  Tato poznámka se dá použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí `Optional<T>` .

 ---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](../articles/azure-functions/functions-dotnet-class-library.md)použijte atribut [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) .

Konstruktor atributu přebírá název centra událostí, název skupiny příjemců a název nastavení aplikace, které obsahuje připojovací řetězec. Další informace o těchto nastaveních najdete v [části Konfigurace aktivační události](#configuration). Tady je `EventHubTriggerAttribute` příklad atributu:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Úplný příklad najdete v tématu [Trigger-C# – příklad](#example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

Z [běhové knihovny Functions](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)jazyka Java použijte anotaci [EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) u parametrů, jejichž hodnota by pocházela z centra událostí. Parametry s těmito poznámkami způsobí, že se funkce spustí při přijetí události. Tato poznámka se dá použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí `Optional<T>` .

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `EventHubTrigger` atribut.

|function.jsvlastnost | Vlastnost atributu |Description|
|---------|---------|----------------------|
|**textový** | neuvedeno | Musí být nastaven na hodnotu `eventHubTrigger` . Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal.|
|**směr** | neuvedeno | Musí být nastaven na hodnotu `in` . Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje položku události v kódu funkce. |
|**dílčí** |**EventHubName** | Pouze funkce 1. x. Název centra událostí Pokud je v připojovacím řetězci přítomen i název centra událostí, tato hodnota tuto vlastnost Přepisuje za běhu. |
|**eventHubName** |**EventHubName** | Functions 2. x a vyšší. Název centra událostí Pokud je v připojovacím řetězci přítomen i název centra událostí, tato hodnota tuto vlastnost Přepisuje za běhu. Dá se odkazovat prostřednictvím [nastavení aplikace](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings) . `%eventHubName%` |
|**Klientská organizace** |**Klientská organizace** | Volitelná vlastnost, která nastaví [skupinu uživatelů](../articles/event-hubs/event-hubs-features.md#event-consumers) použitou k přihlášení k odběru událostí v centru. Je-li tento parametr vynechán, `$Default` je použita skupina uživatelů. |
|**kardinalita** | neuvedeno | Používá se pro všechny jazyky jiné než C #. Nastavte na, aby `many` bylo možné dávkování povolit.  Je-li tento parametr vynechán nebo je nastaven na hodnotu `one` , je do funkce předána jedna zpráva.<br><br>V jazyce C# je tato vlastnost automaticky přiřazena vždy, když Trigger obsahuje pole pro daný typ.|
|**vázán** |**Připojení** | Název nastavení aplikace, které obsahuje připojovací řetězec k oboru názvů centra událostí. Zkopírujte tento připojovací řetězec kliknutím na tlačítko **informace o připojení** pro [obor názvů](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), nikoli v samotném centru událostí. Tento připojovací řetězec musí mít aspoň oprávnění ke čtení pro aktivaci triggeru.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="event-metadata"></a>Metadata události

Aktivační událost Event Hubs poskytuje několik [vlastností metadat](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Vlastnosti metadat lze použít jako součást výrazů vazby v jiných vazbách nebo jako parametry v kódu. Vlastnosti pocházejí ze třídy [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) .

|Vlastnost|Typ|Description|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Instance `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|Čas zařazení do fronty ve standardu UTC.|
|`Offset`|`string`|Posun dat vzhledem ke streamu oddílu centra událostí. Posun je značka nebo identifikátor události v rámci Event Hubsho datového proudu. Identifikátor je jedinečný v rámci oddílu Event Hubsho datového proudu.|
|`PartitionKey`|`string`|Oddíl, do kterého mají být odeslána data událostí.|
|`Properties`|`IDictionary<String,Object>`|Vlastnosti uživatele dat události.|
|`SequenceNumber`|`Int64`|Číslo logické sekvence události|
|`SystemProperties`|`IDictionary<String,Object>`|Vlastnosti systému, včetně dat události.|

Podívejte se na [Příklady kódu](#example) , které používají tyto vlastnosti dříve v tomto článku.

## <a name="hostjson-properties"></a>host.jsvlastností
<a name="host-json"></a>

[host.jsv](../articles/azure-functions/functions-host-json.md#eventhub) souboru obsahuje nastavení, která řídí chování triggeru Event Hubs. Konfigurace se liší v závislosti na verzi Azure Functions.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]