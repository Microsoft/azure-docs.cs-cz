---
title: Azure Service Bus vazby pro službu Azure Functions
description: Naučte se používat Azure Service Bus triggerů a vazeb ve službě Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 04/01/2017
ms.author: glenga
ms.openlocfilehash: 8728533171ec8c8754aabf1a3e32c5ab7630db77
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637962"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus vazby pro službu Azure Functions

Tento článek vysvětluje, jak pracovat s Azure Service Bus vazby ve službě Azure Functions. Azure Functions podporuje aktivaci a výstupní vazby pro fronty a témata Service Bus.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby služby Service Bus jsou součástí [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) balíčku NuGet, verze 2.x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Vazby služby Service Bus jsou součástí [Microsoft.Azure.WebJobs.Extensions.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) balíčku NuGet, verze 3.x. Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Pomocí aktivační události služby Service Bus můžete reagovat na zprávy z fronty služby Service Bus nebo téma. 

## <a name="trigger---example"></a>Aktivační události – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Aktivační události – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který čte [zpráva metadat](#trigger---message-metadata) a zaznamená zprávu fronty služby Service Bus:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

V tomto příkladu je pro Azure Functions verzi 1.x; pro 2.x [vynechejte parametr práva přístup](#trigger---configuration).
 
### <a name="trigger---c-script-example"></a>Aktivační události – příklad skriptu jazyka C#

Následující příklad ukazuje vazby v aktivační události služby Service Bus *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce přečte [zpráva metadat](#trigger---message-metadata) a zaznamená zprávu fronty služby Service Bus.

Zde je vazba dat v *function.json* souboru:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Tady je kód skriptu jazyka C#:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

### <a name="trigger---f-example"></a>Aktivační události – příklad F #

Následující příklad ukazuje vazby v aktivační události služby Service Bus *function.json* souboru a [funkce jazyka F #](functions-reference-fsharp.md) , který používá vazba. Funkce zaznamená zprávu fronty služby Service Bus. 

Zde je vazba dat v *function.json* souboru:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Tady je kód skriptu F #:

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---javascript-example"></a>Aktivační události – příklad v jazyce JavaScript

Následující příklad ukazuje vazby v aktivační události služby Service Bus *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce přečte [zpráva metadat](#trigger---message-metadata) a zaznamená zprávu fronty služby Service Bus. 

Zde je vazba dat v *function.json* souboru:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Tady je kód skriptu jazyka JavaScript:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

### <a name="trigger---java-example"></a>Aktivační události – příklad v jazyce Java

Následující příklad ukazuje vazby v aktivační události služby Service Bus *function.json* souboru a [Java funkce](functions-reference-java.md) , který používá vazba. Aktivuje funkci umístit do fronty služby Service Bus zprávu, a funkce zaznamená zprávu fronty.

Zde je vazba dat v *function.json* souboru:

```json
{
"bindings": [
    {
    "queueName": "myqueuename",
    "connection": "MyServiceBusConnection",
    "name": "msg",
    "type": "ServiceBusQueueTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Tady je kód Java:

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

## <a name="trigger---attributes"></a>Aktivační události – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), můžete nakonfigurovat aktivační událost služby Service Bus následující atributy:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Konstruktor atributu přebírá název fronty nebo tématu a odběru. V Azure Functions verzi 1.x, můžete také zadat připojení přístupová práva. Pokud nezadáte přístupová práva, výchozí hodnota je `Manage`. Další informace najdete v tématu [aktivační události – konfigurace](#trigger---configuration) oddílu.

  Tady je příklad ukazující atribut používaný s parametrem řetězce:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Můžete nastavit `Connection` vlastnosti a určit účet služby Service Bus, který chcete použít, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Kompletní příklad naleznete v tématu [Trigger – C# příklad](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Poskytuje další způsob, jak zadat účet služby Service Bus. Konstruktor přijme název nastavení aplikace, které obsahuje připojovací řetězec služby Service Bus. Atribut je použít na parametr, metody nebo třídy úroveň. Následující příklad ukazuje úrovni třídy a metody:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

Účet služby Service Bus, který chcete použít, je určena v následujícím pořadí:

* `ServiceBusTrigger` Atributu `Connection` vlastnost.
* `ServiceBusAccount` Použije pro stejný parametr, jako `ServiceBusTrigger` atribut.
* `ServiceBusAccount` Použije pro funkci.
* `ServiceBusAccount` Atribut aplikován třídu.
* Nastavení aplikace "AzureWebJobsServiceBus".

## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `ServiceBusTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavena na "serviceBusTrigger". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastavena na "in". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje zprávu fronty nebo tématu v kódu funkce. Nastavte na "$return" tak, aby odkazovaly návratovou hodnotu funkce. | 
|**queueName**|**queueName**|Název fronty k monitorování.  Nastaví jenom v případě, že monitorování frontu, ne pro téma.
|**topicName**|**topicName**|Název tématu, které chcete monitorovat. Nastaví jenom v případě, že monitorování tématu, ne pro frontu.|
|**subscriptionName**|**subscriptionName**|Název odběru, který chcete monitorovat. Nastaví jenom v případě, že monitorování tématu, ne pro frontu.|
|**připojení**|**připojení**|Název nastavení aplikace, které obsahuje připojovací řetězec služby Service Bus má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat jenom zbývající část názvu. Například pokud nastavíte `connection` na "MyServiceBus", modul runtime služby Functions vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyServiceBus." Necháte-li `connection` prázdný, připojovací řetězec služby Service Bus výchozí modul runtime služby Functions používá v nastavení aplikace, který je pojmenován "AzureWebJobsServiceBus".<br><br>K získání připojovacího řetězce, postupujte podle kroků v [získání přihlašovacích údajů pro správu](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). Připojovací řetězec musí být pro obor názvů služby Service Bus, do konkrétní fronty nebo tématu není omezený. |
|**accessRights**|**Přístup**|Přístupová práva připojovacího řetězce. Dostupné jsou hodnoty `manage` a `listen`. Výchozí hodnota je `manage`, což znamená, že `connection` má **spravovat** oprávnění. Pokud používáte připojovací řetězec, který nemá **spravovat** sadu oprávnění, `accessRights` poslouchat "". V opačném případě funkce modulu runtime může selhat pokouší o provedení operace, které vyžadují spravovat práva. V Azure Functions verzi 2.x, tato vlastnost není k dispozici vzhledem k tomu, že nejnovější verze sady SDK úložiště nepodporuje spravovat operace.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační události – využití

V C# a skript jazyka C# můžete použít následující typy parametrů pro zprávu fronty nebo tématu:

* `string` – Pokud má zpráva text.
* `byte[]` -Vhodné pro binární data.
* Vlastní typ – Pokud zpráva obsahuje JSON, Azure Functions se pokusí rekonstruovat JSON data.
* `BrokeredMessage` -Poskytuje deserializovaný zprávu s [BrokeredMessage.GetBody<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metody.

Tyto parametry jsou pro Azure Functions verzi 1.x; 2.x, použijte [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) místo `BrokeredMessage`.

V jazyce JavaScript, přístup ke zprávě fronty nebo tématu pomocí `context.bindings.<name from function.json>`. Zprávy služby Service Bus je předán do funkce jako řetězec nebo objekt JSON.

## <a name="trigger---poison-messages"></a>Aktivační události – počet nezpracovatelných zpráv

Manipulaci s nezpracovatelnými zprávami nelze řídit nebo nakonfigurovat ve službě Azure Functions. Service Bus zpracovává nezpracovatelných zpráv samotný.

## <a name="trigger---peeklock-behavior"></a>Aktivační události – PeekLock chování

Modul runtime služby Functions přijme zprávu v [PeekLock režimu](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Volá `Complete` na zprávu, pokud funkci dokončí úspěšně, nebo volání `Abandon` Pokud funkce selže. Pokud je funkce spuštěná déle, než `PeekLock` vypršení časového limitu, zámek se obnovuje automaticky za předpokladu, že funkce běží. 

Funkce 1.x umožňuje nakonfigurovat `autoRenewTimeout` v *host.json*, která se mapuje na [OnMessageOptions.AutoRenewTimeout](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout?view=azure-dotnet#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout). Maximální hodnotu povolenou pro toto nastavení je 5 minut podle dokumentace k Service Bus, že můžete zvýšit časový limit funkce z výchozí hodnoty 5 minut až 10 minut. Pro funkce služby Service Bus by má k tomu pak, protože by překročilo limit obnovení služby Service Bus.

## <a name="trigger---message-metadata"></a>Aktivační události – zpráva metadat

Aktivační události služby Service Bus nabízí několik [vlastnosti metadat](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Tyto vlastnosti lze použít jako součást výrazy vazby v jiných vazbách nebo jako parametry v kódu. Toto jsou vlastnosti [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) třídy.

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Počet doručení.|
|`DeadLetterSource`|`string`|Zdroj nedoručených zpráv.|
|`ExpiresAtUtc`|`DateTime`|Čas vypršení platnosti ve standardu UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Čas zařazení do fronty ve standardu UTC.|
|`MessageId`|`string`|Uživatelem definovanou hodnotu, která služby Service Bus můžete použít k identifikaci duplicitních zpráv, pokud je povoleno.|
|`ContentType`|`string`|Identifikátor typu obsahu využívaných odesílatele a příjemce pro konkrétní aplikaci logiky.|
|`ReplyTo`|`string`|Odpověď na adresa fronty.|
|`SequenceNumber`|`Int64`|Jedinečné číslo přiřazené zprávy ve službě Service Bus.|
|`To`|`string`|Odeslání na adresu.|
|`Label`|`string`|Popisek konkrétní aplikace.|
|`CorrelationId`|`string`|ID korelace.|
|`UserProperties`|`IDictionary<String,Object>`|Vlastnosti konkrétní zprávy aplikace.|

Zobrazit [příklady kódu](#trigger---example) , které používají tyto vlastnosti dříve v tomto článku.

## <a name="trigger---hostjson-properties"></a>Aktivační události – vlastnosti host.json

[Host.json](functions-host-json.md#servicebus) soubor obsahuje nastavení, která řídí chování aktivační události služby Service Bus.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-service-bus.md)]

## <a name="output"></a>Výstup

Použití Azure Service Bus výstupní vazby pro odesílání zpráv fronty nebo tématu.

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)
* [Java](#output--java-example)

### <a name="output---c-example"></a>Výstup – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , která odešle zprávu fronty služby Service Bus:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Výstup – příklad skriptu jazyka C#

Následující příklad ukazuje, Service Bus výstupní vazby ve *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce používá aktivaci časovačem odeslat zprávu fronty každých 15 sekund.

Zde je vazba dat v *function.json* souboru:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Tady je kód jazyka C# skript, který vytvoří jedna zpráva:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Tady je kód jazyka C# skript, který vytváří více zpráv:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Výstup – příklad F #

Následující příklad ukazuje, Service Bus výstupní vazby ve *function.json* souboru a [funkce skriptu jazyka F #](functions-reference-fsharp.md) , který používá vazba. Funkce používá aktivaci časovačem odeslat zprávu fronty každých 15 sekund.

Zde je vazba dat v *function.json* souboru:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Tady je F # kód skriptu, který vytvoří jedna zpráva:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

### <a name="output---javascript-example"></a>Výstup – příklad v jazyce JavaScript

Následující příklad ukazuje, Service Bus výstupní vazby ve *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce používá aktivaci časovačem odeslat zprávu fronty každých 15 sekund.

Zde je vazba dat v *function.json* souboru:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Tady je kód skriptu jazyka JavaScript, který vytvoří jedna zpráva:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Tady je kód skriptu jazyka JavaScript, který vytvoří více zpráv:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```


### <a name="output---java-example"></a>Výstup – příklad v jazyce Java

Následující příklad ukazuje funkci Java, která odešle zprávu do fronty služby Service Bus `myqueue` aktivovaného požadavku HTTP.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
 ```

 V [Java funkce knihovny prostředí runtime](/java/api/overview/azure/functions/runtime), použijte `@QueueOutput` Poznámka k parametrům funkcí, jehož hodnota by byla zapsána do fronty služby Service Bus.  Typ parametru by měl být `OutputBinding<T>`, kde T je libovolný Java nativní objekt POJO.

## <a name="output---attributes"></a>Výstup – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Konstruktor atributu přebírá název fronty nebo tématu a odběru. Můžete také zadat připojení přístupová práva. Jak zvolit přístupová práva, nastavení je podrobně [výstup - konfigurace](#output---configuration) oddílu. Tady je příklad ukazující použije návratovou hodnotu funkce:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Můžete nastavit `Connection` vlastnosti a určit účet služby Service Bus, který chcete použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Kompletní příklad naleznete v tématu [výstup – příklad v jazyce C#](#output---c-example).

Můžete použít `ServiceBusAccount` atribut zadat účet služby Service Bus na úrovni třídy, metody nebo parametr.  Další informace najdete v tématu [aktivační události – atributy](#trigger---attributes).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `ServiceBus` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavena na "služby"Service Bus. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastavena na "out". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje fronty nebo tématu v kódu funkce. Nastavte na "$return" tak, aby odkazovaly návratovou hodnotu funkce. | 
|**queueName**|**queueName**|Název fronty.  Nastaví jenom v případě, že odesílá zprávy do fronty, ne pro téma.
|**topicName**|**topicName**|Název tématu, které chcete monitorovat. Nastaví jenom v případě, že odesílání zpráv tématu, ne pro frontu.|
|**připojení**|**připojení**|Název nastavení aplikace, které obsahuje připojovací řetězec služby Service Bus má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat jenom zbývající část názvu. Například pokud nastavíte `connection` na "MyServiceBus", modul runtime služby Functions vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyServiceBus." Necháte-li `connection` prázdný, připojovací řetězec služby Service Bus výchozí modul runtime služby Functions používá v nastavení aplikace, který je pojmenován "AzureWebJobsServiceBus".<br><br>K získání připojovacího řetězce, postupujte podle kroků v [získání přihlašovacích údajů pro správu](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). Připojovací řetězec musí být pro obor názvů služby Service Bus, do konkrétní fronty nebo tématu není omezený.|
|**accessRights**|**Přístup**|Přístupová práva připojovacího řetězce. Dostupné jsou hodnoty `manage` a `listen`. Výchozí hodnota je `manage`, což znamená, že `connection` má **spravovat** oprávnění. Pokud používáte připojovací řetězec, který nemá **spravovat** sadu oprávnění, `accessRights` poslouchat "". V opačném případě funkce modulu runtime může selhat pokouší o provedení operace, které vyžadují spravovat práva. V Azure Functions verzi 2.x, tato vlastnost není k dispozici vzhledem k tomu, že nejnovější verze sady SDK úložiště nepodporuje spravovat operace.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

Ve službě Azure Functions 1.x, modul runtime vytvoří frontu Pokud neexistuje a nastavili jste `accessRights` k `manage`. Funkce verze 2.x, fronty nebo tématu již musí existovat; Pokud chcete zadat do fronty nebo tématu, která neexistuje, funkce selže. 

V jazyce C# a skript jazyka C# můžete použít následující typy parametrů pro výstupní vazbu:

* `out T paramName` - `T` může být libovolný typ serializovat na JSON. Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce vytvoří zprávu s objekt s hodnotou null.
* `out string` – Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce nevytváří zprávu.
* `out byte[]` – Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce nevytváří zprávu.
* `out BrokeredMessage` – Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce nevytváří zprávu.
* `ICollector<T>` nebo `IAsyncCollector<T>` – pro vytváření více zpráv. Zpráva se vytvoří při volání `Add` metody.

V asynchronních funkcí, použijte vrácenou hodnotu nebo `IAsyncCollector` místo `out` parametru.

Tyto parametry jsou pro Azure Functions verzi 1.x; 2.x, použijte [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) místo `BrokeredMessage`.

V jazyce JavaScript, přístup k frontě nebo tématu pomocí `context.bindings.<name from function.json>`. Řetězec, bajtové pole nebo objekt jazyka Javascript (deserializovat do formátu JSON) můžete přiřadit k `context.binding.<name>`.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Service Bus | [Kódy chyb služby Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Omezení služby Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
