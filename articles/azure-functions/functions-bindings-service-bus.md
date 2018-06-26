---
title: Azure Service Bus vazby pro Azure Functions
description: Pochopit, jak používat Azure Service Bus triggerů a vazeb v Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Funkce Azure, funkce zpracování událostí, dynamické výpočetní architektura bez serveru
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: tdykstra
ms.openlocfilehash: 0e9e7dcab208d1ffd8410a02a7c1cd713d11b277
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753485"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus vazby pro Azure Functions

Tento článek vysvětluje, jak pracovat s vazeb Azure Service Bus v Azure Functions. Azure Functions podporuje aktivaci a výstupní vazby pro fronty sběrnice a témata.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby služby Service Bus jsou součástí [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) balíček NuGet verze 2.x. Zdrojový kód pro balíček je v [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Vazby služby Service Bus jsou součástí [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) balíček NuGet verze 3.x. Zdrojový kód pro balíček je v [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Použijte aktivační událost Service Bus reagovat na zprávy z fronty sběrnice nebo téma. 

## <a name="trigger---example"></a>Aktivační událost – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Aktivační událost – příklad jazyka C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který čte [zpráva metadata](#trigger---message-metadata) a do protokolu zaznamená zprávu fronty sběrnice:

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

V tomto příkladu je pro verzi Azure Functions 1.x; pro 2.x [vynechejte parametr práva přístupu](#trigger---configuration).
 
### <a name="trigger---c-script-example"></a>Aktivační událost – příklad skriptu jazyka C#

Následující příklad ukazuje vazby v aktivační události služby Service Bus *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce přečte [zpráva metadata](#trigger---message-metadata) a do protokolu zaznamená zprávu fronty Service Bus.

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

### <a name="trigger---f-example"></a>Aktivační událost – příklad F #

Následující příklad ukazuje vazby v aktivační události služby Service Bus *function.json* souboru a [funkce F #](functions-reference-fsharp.md) používající vazby. Funkce zaznamená zprávu fronty Service Bus. 

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

### <a name="trigger---javascript-example"></a>Aktivační událost – příklad v jazyce JavaScript

Následující příklad ukazuje vazby v aktivační události služby Service Bus *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce přečte [zpráva metadata](#trigger---message-metadata) a do protokolu zaznamená zprávu fronty Service Bus. 

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

Tady je kód JavaScript skriptu:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

## <a name="trigger---attributes"></a>Aktivační událost – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte následující atributy ke konfiguraci služby Service Bus aktivační událost:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusTriggerAttribute.cs)

  Konstruktoru atributu převezme název fronta nebo téma a odběr. Ve verzi Azure Functions 1.x, můžete také zadat připojení přístupová práva. Pokud nezadáte přístupová práva, výchozí hodnota je `Manage`. Další informace najdete v tématu [aktivační událost - konfigurace](#trigger---configuration) části.

  Tady je příklad, který ukazuje atribut používaný s parametrem řetězec:

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

  Úplný příklad najdete v tématu [aktivační událost - C# příklad](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs)

  Poskytuje další možnost zadat účet služby Service Bus, který chcete použít. Konstruktor přebírá název nastavení aplikace, který obsahuje připojovací řetězec sběrnice služeb. Atribut lze použít na parametr, metoda nebo na úrovni třídy. Následující příklad ukazuje úroveň třídy a metody:

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

Účet služby Service Bus, který chcete použít, je určen v následujícím pořadí:

* `ServiceBusTrigger` Atributu `Connection` vlastnost.
* `ServiceBusAccount` Atribut použitý pro parametr stejné jako `ServiceBusTrigger` atribut.
* `ServiceBusAccount` Atribut použitý k funkci.
* `ServiceBusAccount` Atribut použitý k třídě.
* Nastavení aplikace "AzureWebJobsServiceBus".

## <a name="trigger---configuration"></a>Aktivační událost - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `ServiceBusTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavena na "serviceBusTrigger". Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure.|
|**direction** | neuvedeno | Musí být nastavena na "v". Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure. |
|**Jméno** | neuvedeno | Název proměnné, která představuje zprávu fronta nebo téma v kódu funkce. Chcete-li funkce návratovou hodnotu nastavte na "$return". | 
|**queueName**|**QueueName**|Název fronty k monitorování.  Nastavte jenom v případě, že monitorování fronty, ne pro téma.
|**topicName**|**TopicName**|Název tématu, které chcete monitorovat. Nastavte jenom v případě, že monitorování téma, ne pro frontu.|
|**subscriptionName**|**Název_předplatného**|Název odběru k monitorování. Nastavte jenom v případě, že monitorování téma, ne pro frontu.|
|**Připojení**|**Připojení**|Název nastavení aplikace, který obsahuje připojovací řetězec sběrnice služeb, který chcete použít pro tuto vazbu. Název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytku názvu. Například pokud nastavíte `connection` na "MyServiceBus" Functions runtime vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyServiceBus." Pokud necháte `connection` prázdná, připojovací řetězec sběrnice služeb výchozí modul runtime funkce používá v nastavení aplikace, s názvem "AzureWebJobsServiceBus".<br><br>Pokud chcete získat připojovací řetězec, postupujte podle kroků, zobrazuje se v [získat přihlašovací údaje správu](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). Připojovací řetězec musí být v případě oboru názvů Service Bus, bez omezení konkrétní fronty nebo téma. |
|**accessRights**|**Přístup**|Přístupová práva pro připojovací řetězec. Dostupné jsou hodnoty `manage` a `listen`. Výchozí hodnota je `manage`, což znamená, že `connection` má **spravovat** oprávnění. Pokud použijete připojovací řetězec, který nemá **spravovat** nastavit oprávnění, `accessRights` "naslouchat". Funkce modulu runtime může selhat pokouší o provedení operace, které vyžadují, jinak hodnota spravovat práva. V Azure Functions verze 2.x, tato vlastnost není k dispozici nejnovější verzi sady SDK úložiště nepodporuje spravovat operace.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační událost - využití

V jazyce C# a C# skript můžete použít následující typy parametrů pro zprávu fronta nebo téma:

* `string` – Pokud zpráva je text.
* `byte[]` -Vhodné pro binární data.
* Vlastní typ – Pokud zpráva obsahuje formát JSON, Azure Functions se pokusí rekonstruovat JSON data.
* `BrokeredMessage` -Vám deserializovat zprávu s [BrokeredMessage.GetBody<T>()](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metoda.

Tyto parametry jsou určené pro Azure Functions verze 1.x; pro 2.x, použijte [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) místo `BrokeredMessage`.

V jazyce JavaScript, přístup k fronta nebo téma zprávy pomocí `context.bindings.<name from function.json>`. Zprávy služby Service Bus je předán do funkce jako řetězec nebo objekt JSON.

## <a name="trigger---poison-messages"></a>Aktivační událost - poškozených zpráv

Zpracování poškozených zpráv nelze řídí nebo nakonfigurované v Azure Functions. Service Bus zpracovává poškozených zpráv sám sebe.

## <a name="trigger---peeklock-behavior"></a>Aktivační událost - PeekLock chování

Modul runtime funkce obdrží zprávu v [PeekLock režimu](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Zavolá `Complete` na zprávu, pokud funkci skončí úspěšně, nebo volání `Abandon` Pokud funkce selže. Pokud je funkce spuštěná déle, než `PeekLock` vypršel časový limit, zámek je automaticky obnovují vždy tak dlouho, dokud funkce běží. 

Funkce 1.x umožňuje nakonfigurovat `autoRenewTimeout` v *host.json*, která se mapuje na [OnMessageOptions.AutoRenewTimeout](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout?view=azure-dotnet#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout). Maximální povolená pro toto nastavení je 5 minut podle dokumentace služby Service Bus, zatímco můžete zvýšit časový limit funkce z výchozí hodnoty 5 minut do 10 minut. Pro funkce Service Bus není vhodné k tomu pak, protože by překročila limit obnovení Service Bus.

## <a name="trigger---message-metadata"></a>Aktivační událost – zpráva metadat

Aktivační události služby Service Bus nabízí několik [metadata vlastnosti](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Tyto vlastnosti lze použít jako součást vazby výrazy v jiných vazby nebo jako parametry v kódu. Tyto vlastnosti jsou [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) třídy.

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Počet doručení.|
|`DeadLetterSource`|`string`|Zdroj nedoručených zpráv.|
|`ExpiresAtUtc`|`DateTime`|Čas vypršení platnosti ve standardu UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Zařazených do fronty čas v UTC.|
|`MessageId`|`string`|Hodnotu definovanou uživatelem, který sběrnice můžete použít k identifikaci duplicitní zprávy, pokud je povoleno.|
|`ContentType`|`string`|Identifikátor typu obsahu využívaných odesílatele a příjemce pro určitou logiku aplikace.|
|`ReplyTo`|`string`|Odpověď na adresu fronty.|
|`SequenceNumber`|`Int64`|Jedinečné číslo přiřazené zprávu pomocí sběrnice služby.|
|`To`|`string`|Odeslání na adresu.|
|`Label`|`string`|Popisek konkrétní aplikace.|
|`CorrelationId`|`string`|ID korelace.|
|`Properties`|`IDictionary<String,Object>`|Vlastnosti zprávy konkrétní aplikace.|

V tématu [příklady kódu pro](#trigger---example) , použijte tyto vlastnosti dříve v tomto článku.

## <a name="trigger---hostjson-properties"></a>Aktivační událost - host.json vlastnosti

[Host.json](functions-host-json.md#servicebus) soubor obsahuje nastavení, které řídí chování aktivační události služby Service Bus.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-service-bus.md)]

## <a name="output"></a>Výstup

Použití Azure Service Bus Výstupní vazba odesílat zprávy fronty nebo téma.

## <a name="output---example"></a>Výstup – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Výstup – příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) , odešle zprávu fronty sběrnice:

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

Následující příklad ukazuje výstup Service Bus vazby ve *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce používá aktivaci časovačem odeslat zprávu fronty každých 15 sekund.

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

Tady je C# kód skriptu, který vytvoří do jedné zprávy:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Zde uvádíme C# kód skriptu, který vytvoří více zpráv:

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

Následující příklad ukazuje výstup Service Bus vazby ve *function.json* souboru a [F # skript funkce](functions-reference-fsharp.md) používající vazby. Funkce používá aktivaci časovačem odeslat zprávu fronty každých 15 sekund.

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

Tady je F # kód skriptu, který vytvoří do jedné zprávy:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

### <a name="output---javascript-example"></a>Výstup – příklad v jazyce JavaScript

Následující příklad ukazuje výstup Service Bus vazby ve *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce používá aktivaci časovačem odeslat zprávu fronty každých 15 sekund.

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

Zde je kód JavaScript, který vytvoří do jedné zprávy:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Zde je kód skriptu jazyka JavaScript, který vytváří více zpráv:

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

## <a name="output---attributes"></a>Výstup – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs).

Konstruktoru atributu převezme název fronta nebo téma a odběr. Můžete také zadat připojení přístupová práva. Jak vybrat přístupová práva, nastavení je podrobně popsaný [výstup - konfigurace](#output---configuration) části. Tady je příklad, který ukazuje atribut návratové hodnoty funkce:

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

Úplný příklad najdete v tématu [výstup - C# příklad](#output---c-example).

Můžete použít `ServiceBusAccount` atribut zadat účet služby Service Bus, který chcete použít na úrovni třídy, metoda nebo parametr.  Další informace najdete v tématu [aktivační událost – atributy](#trigger---attributes).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `ServiceBus` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavena na "sběrnice". Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure.|
|**direction** | neuvedeno | Musí být nastavena na "out". Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure. |
|**Jméno** | neuvedeno | Název proměnné, která představuje fronta nebo téma v kódu funkce. Chcete-li funkce návratovou hodnotu nastavte na "$return". | 
|**queueName**|**QueueName**|Název fronty.  Nastavte jenom v případě, že odeslání zprávy do fronty, není pro téma.
|**topicName**|**TopicName**|Název tématu, které chcete monitorovat. Nastavte jenom v případě, že odesílání zpráv tématu, ne pro frontu.|
|**Připojení**|**Připojení**|Název nastavení aplikace, který obsahuje připojovací řetězec sběrnice služeb, který chcete použít pro tuto vazbu. Název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytku názvu. Například pokud nastavíte `connection` na "MyServiceBus" Functions runtime vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyServiceBus." Pokud necháte `connection` prázdná, připojovací řetězec sběrnice služeb výchozí modul runtime funkce používá v nastavení aplikace, s názvem "AzureWebJobsServiceBus".<br><br>Pokud chcete získat připojovací řetězec, postupujte podle kroků, zobrazuje se v [získat přihlašovací údaje správu](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). Připojovací řetězec musí být v případě oboru názvů Service Bus, bez omezení konkrétní fronty nebo téma.|
|**accessRights**|**Přístup**|Přístupová práva pro připojovací řetězec. Dostupné jsou hodnoty `manage` a `listen`. Výchozí hodnota je `manage`, což znamená, že `connection` má **spravovat** oprávnění. Pokud použijete připojovací řetězec, který nemá **spravovat** nastavit oprávnění, `accessRights` "naslouchat". Funkce modulu runtime může selhat pokouší o provedení operace, které vyžadují, jinak hodnota spravovat práva. V Azure Functions verze 2.x, tato vlastnost není k dispozici nejnovější verzi sady SDK úložiště nepodporuje spravovat operace.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – použití

V Azure Functions 1.x, modul runtime vytvoří frontu Pokud neexistuje, a nastavili jste `accessRights` k `manage`. Funkce verze 2.x, fronta nebo téma již musí existovat; Pokud jste určili, se fronta nebo téma, který neexistuje, funkce se nezdaří. 

V jazyce C# a C# skript můžete použít následující typy parametrů pro vazbu výstup:

* `out T paramName` - `T` mohou být jakéhokoli typu JSON serializable. Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce vytvoří zprávu s objektem hodnotu null.
* `out string` – Pokud je hodnota parametru má hodnotu null při ukončení funkce, funkce nevytváří zprávu.
* `out byte[]` – Pokud je hodnota parametru má hodnotu null při ukončení funkce, funkce nevytváří zprávu.
* `out BrokeredMessage` – Pokud je hodnota parametru má hodnotu null při ukončení funkce, funkce nevytváří zprávu.
* `ICollector<T>` nebo `IAsyncCollector<T>` – pro vytvoření více zpráv. Zprávu se vytvoří při volání `Add` metoda.

Asynchronní funkcí, použití návratovou hodnotu nebo `IAsyncCollector` místo `out` parametr.

Tyto parametry jsou určené pro Azure Functions verze 1.x; pro 2.x, použijte [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) místo `BrokeredMessage`.

V jazyce JavaScript, přístup k fronta nebo téma pomocí `context.bindings.<name from function.json>`. Řetězec, bajtové pole nebo objekt jazyka Javascript (deserializovat do formátu JSON) můžete přiřadit k `context.binding.<name>`.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Service Bus | [Kódy chyb služby Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Omezení služby Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)
