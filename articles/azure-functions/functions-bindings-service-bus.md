---
title: Azure Service Bus vazby pro službu Azure Functions
description: Naučte se používat Azure Service Bus triggerů a vazeb ve službě Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: d27058ed0ff3044d98d8428b3065b02e2c24c451
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231047"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus vazby pro službu Azure Functions

Tento článek vysvětluje, jak pracovat s Azure Service Bus vazby ve službě Azure Functions. Azure Functions podporuje aktivaci a výstupní vazby pro fronty a témata Service Bus.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby Service Bus jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) , verze 2. x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Vazby Service Bus jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) , verze 3. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-Functions-ServiceBus-Extension](https://github.com/Azure/azure-functions-servicebus-extension) .

> [!NOTE]
> Verze 2. x nevytváří téma ani předplatné nakonfigurované v instanci `ServiceBusTrigger`. Verze 2. x je založená na [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) a nezpracovává správu fronty.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Pomocí aktivační události služby Service Bus můžete reagovat na zprávy z fronty služby Service Bus nebo téma. 

## <a name="trigger---example"></a>Aktivační události – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#trigger---c-example)
* [C#skript (. csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Aktivační události – příklad v jazyce C#

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která čte [metadata zprávy](#trigger---message-metadata) a protokoluje zprávu fronty Service Bus:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

### <a name="trigger---c-script-example"></a>Aktivační události – příklad skriptu jazyka C#

Následující příklad ukazuje Service Bus aktivační vazby v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce přečte [metadata zprávy](#trigger---message-metadata) a zaprotokoluje zprávu fronty Service Bus.

Tady jsou data vazby v souboru *Function. JSON* :

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

### <a name="trigger---f-example"></a>Aktivační události – F# příklad

Následující příklad ukazuje Service Bus aktivační vazby v souboru *Function. JSON* a [ F# funkci](functions-reference-fsharp.md) , která používá vazbu. Funkce zaznamená zprávu fronty služby Service Bus. 

Tady jsou data vazby v souboru *Function. JSON* :

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

Tady je F# kód skriptu:

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>Aktivační události – příklad v jazyce Java

Následující funkce jazyka Java používá `@ServiceBusQueueTrigger` anotace z [knihovny běhového prostředí Java Functions](/java/api/overview/azure/functions/runtime) k popisu konfigurace pro aktivační událost Service Bus Queue. Funkce přiřadí zprávu umístěnou ve frontě a přidá ji do protokolů.

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

Funkce Java se můžou aktivovat i při přidání zprávy do tématu Service Bus. Následující příklad používá anotaci `@ServiceBusTopicTrigger` k popisu konfigurace triggeru.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

### <a name="trigger---javascript-example"></a>Aktivační události – příklad v jazyce JavaScript

Následující příklad ukazuje Service Bus aktivační vazby v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce přečte [metadata zprávy](#trigger---message-metadata) a zaprotokoluje zprávu fronty Service Bus. 

Tady jsou data vazby v souboru *Function. JSON* :

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

### <a name="trigger---python-example"></a>Trigger – příklad Pythonu

Následující příklad ukazuje, jak číst zprávu fronty ServiceBus prostřednictvím triggeru.

Vazba ServiceBus je definovaná v *Function. JSON* , kde *Type* je nastavená na `serviceBusTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Kód v  *_\_init_\_. py* deklaruje parametr jako `func.ServiceBusMessage` což umožňuje číst zprávu fronty ve funkci.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

## <a name="trigger---attributes"></a>Aktivační události – atributy

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte následující atributy ke konfiguraci aktivační události Service Bus:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Konstruktor atributu přebírá název fronty nebo tématu a odběru. V Azure Functions verzi 1.x, můžete také zadat připojení přístupová práva. Pokud nezadáte přístupová práva, výchozí hodnota je `Manage`. Další informace najdete v části věnované [konfiguraci triggeru](#trigger---configuration) .

  Tady je příklad ukazující atribut používaný s parametrem řetězce:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Vlastnost `Connection` můžete nastavit tak, aby určovala Service Bus účet, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Úplný příklad najdete v tématu [Trigger – C# příklad](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Poskytuje další způsob, jak zadat účet služby Service Bus. Konstruktor přijme název nastavení aplikace, které obsahuje připojovací řetězec služby Service Bus. Atribut je použít na parametr, metody nebo třídy úroveň. Následující příklad ukazuje úrovni třídy a metody:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

Účet služby Service Bus, který chcete použít, je určena v následujícím pořadí:

* Vlastnost `Connection` atributu `ServiceBusTrigger`.
* Atribut `ServiceBusAccount` aplikovaný na stejný parametr jako atribut `ServiceBusTrigger`.
* Atribut `ServiceBusAccount` aplikovaný na funkci.
* Atribut `ServiceBusAccount` aplikovaný na třídu.
* Nastavení aplikace "AzureWebJobsServiceBus".

## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `ServiceBusTrigger`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavena na "serviceBusTrigger". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastavena na "in". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje zprávu fronty nebo tématu v kódu funkce. Nastavte na "$return" tak, aby odkazovaly návratovou hodnotu funkce. |
|**Proměnné QueueName**|**Proměnné QueueName**|Název fronty k monitorování.  Nastaví jenom v případě, že monitorování frontu, ne pro téma.
|**téma**|**Téma**|Název tématu, které chcete monitorovat. Nastaví jenom v případě, že monitorování tématu, ne pro frontu.|
|**subscriptionName**|**SubscriptionName**|Název odběru, který chcete monitorovat. Nastaví jenom v případě, že monitorování tématu, ne pro frontu.|
|**vázán**|**Vázán**|Název nastavení aplikace, které obsahuje připojovací řetězec služby Service Bus má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat jenom zbývající část názvu. Například pokud nastavíte `connection` na "MyServiceBus", modul runtime Functions vyhledá nastavení aplikace s názvem "AzureWebJobsMyServiceBus". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec Service Bus v nastavení aplikace s názvem "AzureWebJobsServiceBus".<br><br>Pokud chcete získat připojovací řetězec, postupujte podle kroků uvedených v části [získání přihlašovacích údajů pro správu](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Připojovací řetězec musí být pro obor názvů služby Service Bus, do konkrétní fronty nebo tématu není omezený. |
|**accessRights**|**Přístup**|Přístupová práva připojovacího řetězce. Dostupné hodnoty jsou `manage` a `listen`. Výchozí hodnota je `manage`, což znamená, že `connection` má oprávnění **Spravovat** . Pokud použijete připojovací řetězec, který nemá oprávnění **Spravovat** , nastavte `accessRights` na "naslouchání". V opačném případě funkce modulu runtime může selhat pokouší o provedení operace, které vyžadují spravovat práva. V Azure Functions verzi 2.x, tato vlastnost není k dispozici vzhledem k tomu, že nejnovější verze sady SDK úložiště nepodporuje spravovat operace.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační události – využití

V C# a skript jazyka C# můžete použít následující typy parametrů pro zprávu fronty nebo tématu:

* `string` – Pokud se jedná o text zprávy
* `byte[]` – užitečné pro binární data.
* Vlastní typ – Pokud zpráva obsahuje JSON, Azure Functions se pokusí rekonstruovat JSON data.
* `BrokeredMessage` – poskytuje deserializovanou zprávu s metodou [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Tyto parametry jsou pro Azure Functions verze 1. x; pro 2. x použijte [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) místo `BrokeredMessage`.

V jazyce JavaScript přihlaste ke zprávě fronty nebo tématu pomocí `context.bindings.<name from function.json>`. Zprávy služby Service Bus je předán do funkce jako řetězec nebo objekt JSON.

## <a name="trigger---poison-messages"></a>Aktivační události – počet nezpracovatelných zpráv

Manipulaci s nezpracovatelnými zprávami nelze řídit nebo nakonfigurovat ve službě Azure Functions. Service Bus zpracovává nezpracovatelných zpráv samotný.

## <a name="trigger---peeklock-behavior"></a>Aktivační události – PeekLock chování

Modul runtime Functions obdrží zprávu v [režimu PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Volá `Complete` ve zprávě, pokud se funkce dokončí úspěšně, nebo zavolá `Abandon`, pokud se funkce nezdaří. Pokud je funkce spuštěná déle než `PeekLock` časový limit, zámek se automaticky obnoví, dokud je funkce spuštěná. 

`maxAutoRenewDuration` lze konfigurovat v *Host. JSON*, který se mapuje na [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Maximální hodnotu povolenou pro toto nastavení je 5 minut podle dokumentace k Service Bus, že můžete zvýšit časový limit funkce z výchozí hodnoty 5 minut až 10 minut. Pro funkce služby Service Bus by má k tomu pak, protože by překročilo limit obnovení služby Service Bus.

## <a name="trigger---message-metadata"></a>Aktivační události – zpráva metadat

Aktivační událost Service Bus poskytuje několik [vlastností metadat](./functions-bindings-expressions-patterns.md#trigger-metadata). Tyto vlastnosti lze použít jako součást výrazy vazby v jiných vazbách nebo jako parametry v kódu. Jedná se o vlastnosti třídy [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) .

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

> [!NOTE]
> V současné době je Trigger Service Bus, který funguje s frontami a odběry s povolenou relací, ve verzi Preview. Sledujte prosím [tuto položku](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) pro všechny další aktualizace, které se týkají tohoto. 

Podívejte se na [Příklady kódu](#trigger---example) , které používají tyto vlastnosti dříve v tomto článku.

## <a name="output"></a>Výstup

Použití Azure Service Bus výstupní vazby pro odesílání zpráv fronty nebo tématu.

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#output---c-example)
* [C#skript (. csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Výstup – příklad v jazyce C#

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která odesílá zprávu Service Bus fronty:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Výstup – příklad skriptu jazyka C#

Následující příklad ukazuje výstupní vazbu Service Bus v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce používá aktivaci časovačem odeslat zprávu fronty každých 15 sekund.

Tady jsou data vazby v souboru *Function. JSON* :

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
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Tady je kód jazyka C# skript, který vytváří více zpráv:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

### <a name="output---f-example"></a>Výstup – F# příklad

Následující příklad ukazuje výstupní vazbu Service Bus v souboru *Function. JSON* a [ F# funkci skriptu](functions-reference-fsharp.md) , která používá vazbu. Funkce používá aktivaci časovačem odeslat zprávu fronty každých 15 sekund.

Tady jsou data vazby v souboru *Function. JSON* :

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

Tady je F# kód, který vytvoří jedna zpráva skriptu:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>Výstup – příklad v jazyce Java

Následující příklad ukazuje funkci jazyka Java, která pošle zprávu do fronty Service Bus `myqueue` při aktivaci požadavkem HTTP.

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

 V [běhové knihovně funkcí jazyka Java](/java/api/overview/azure/functions/runtime)použijte v parametrech funkce anotaci `@QueueOutput`, jejichž hodnota by byla zapsána do fronty Service Bus.  Typ parametru by měl být `OutputBinding<T>`, kde T je jakýkoliv nativní typ Java typu POJO.

Funkce jazyka Java mohou také zapisovat do Service Busho tématu. Následující příklad používá anotaci `@ServiceBusTopicOutput` k popisu konfigurace výstupní vazby. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

### <a name="output---javascript-example"></a>Výstup – příklad v jazyce JavaScript

Následující příklad ukazuje výstupní vazbu Service Bus v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce používá aktivaci časovačem odeslat zprávu fronty každých 15 sekund.

Tady jsou data vazby v souboru *Function. JSON* :

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

### <a name="output---python-example"></a>Výstup – příklad Pythonu

Následující příklad ukazuje, jak zapisovat do fronty ServiceBus v Pythonu.

Definice vazby ServiceBue je definována v *Function. JSON* , kde *typ* je nastaven na `serviceBus`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

V  *_\_init_\_. py*můžete do fronty napsat zprávu předáním hodnoty metodě `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

## <a name="output---attributes"></a>Výstup – atributy

V [ C# knihovně tříd](functions-dotnet-class-library.md)použijte [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Konstruktor atributu přebírá název fronty nebo tématu a odběru. Můžete také zadat připojení přístupová práva. Jak zvolit nastavení přístupových práv je vysvětleno v části [výstup – konfigurace](#output---configuration) . Tady je příklad ukazující použije návratovou hodnotu funkce:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Vlastnost `Connection` můžete nastavit tak, aby určovala Service Bus účet, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v tématu [Output- C# example](#output---c-example).

Atribut `ServiceBusAccount` můžete použít k určení účtu Service Bus, který se má použít na úrovni třídy, metody nebo parametru.  Další informace najdete v tématu [Trigger – atributy](#trigger---attributes).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `ServiceBus`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavena na "služby"Service Bus. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastavena na "out". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje fronty nebo tématu v kódu funkce. Nastavte na "$return" tak, aby odkazovaly návratovou hodnotu funkce. |
|**Proměnné QueueName**|**Proměnné QueueName**|Název fronty.  Nastaví jenom v případě, že odesílá zprávy do fronty, ne pro téma.
|**téma**|**Téma**|Název tématu, které chcete monitorovat. Nastaví jenom v případě, že odesílání zpráv tématu, ne pro frontu.|
|**vázán**|**Vázán**|Název nastavení aplikace, které obsahuje připojovací řetězec služby Service Bus má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat jenom zbývající část názvu. Například pokud nastavíte `connection` na "MyServiceBus", modul runtime Functions vyhledá nastavení aplikace s názvem "AzureWebJobsMyServiceBus". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec Service Bus v nastavení aplikace s názvem "AzureWebJobsServiceBus".<br><br>Pokud chcete získat připojovací řetězec, postupujte podle kroků uvedených v části [získání přihlašovacích údajů pro správu](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Připojovací řetězec musí být pro obor názvů služby Service Bus, do konkrétní fronty nebo tématu není omezený.|
|**accessRights**|**Přístup**|Přístupová práva připojovacího řetězce. Dostupné hodnoty jsou `manage` a `listen`. Výchozí hodnota je `manage`, což znamená, že `connection` má oprávnění **Spravovat** . Pokud použijete připojovací řetězec, který nemá oprávnění **Spravovat** , nastavte `accessRights` na "naslouchání". V opačném případě funkce modulu runtime může selhat pokouší o provedení operace, které vyžadují spravovat práva. V Azure Functions verzi 2.x, tato vlastnost není k dispozici vzhledem k tomu, že nejnovější verze sady SDK úložiště nepodporuje spravovat operace.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

V Azure Functions 1. x modul runtime vytvoří frontu, pokud neexistuje a že jste nastavili `accessRights` na `manage`. Funkce verze 2.x, fronty nebo tématu již musí existovat; Pokud chcete zadat do fronty nebo tématu, která neexistuje, funkce selže. 

V jazyce C# a skript jazyka C# můžete použít následující typy parametrů pro výstupní vazbu:

* `out T paramName` - `T` může být jakýkoli typ serializovatelný pro JSON. Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce vytvoří zprávu s objekt s hodnotou null.
* `out string` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `out byte[]` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `out BrokeredMessage` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro funkce 1. x).
* `out Message` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro Functions 2. x).
* `ICollector<T>` nebo `IAsyncCollector<T>` – pro vytvoření více zpráv. Zpráva se vytvoří při volání metody `Add`.

Při práci s C# funkcemi:

* Asynchronní funkce místo parametru `out` vyžadují návratovou hodnotu nebo `IAsyncCollector`.

* Chcete-li získat přístup k ID relace, vytvořte připojení k typu [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) a použijte vlastnost `sessionId`.

V jazyce JavaScript přejděte ke frontě nebo tématu pomocí `context.bindings.<name from function.json>`. Pro `context.binding.<name>`můžete přiřadit řetězec, bajtové pole nebo objekt JavaScriptu (deserializovat do formátu JSON).

Pokud chcete poslat zprávu do fronty s povolenými relacemi v jinýchC# jazycích než, použijte [sadu SDK Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging) , nikoli integrovanou výstupní vazbu.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Service Bus | [Kódy chyb Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Omezení Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení Host.JSON

Tato část popisuje globální konfiguraci nastavení k dispozici pro tuto vazbu ve verzi 2.x. Příklad souboru host.json níže obsahuje pouze verzi 2.x nastavení pro tuto vazbu. Další informace o globálních nastaveních konfigurace verze 2. x naleznete v tématu [reference Host. JSON pro Azure Functions verze 2. x](functions-host-json.md).

> [!NOTE]
> Odkaz na Host. JSON ve funkcích 1. x najdete v [referenčních informacích k host. JSON pro Azure Functions 1. x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            }
        }
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|Maximální doba, ve kterém se automatické obnovení zámku zprávy.|
|Automatické dokončování|true (pravda)|Určuje, zda by měl aktivační událost okamžitě označit jako kompletní (automatické dokončování) nebo počkejte, zpracování volání dokončení.|
|maxConcurrentCalls|16|Maximální počet souběžných volání zpětného volání, které by mělo zahájit pumpu zpráv. Ve výchozím nastavení modul runtime služby Functions zpracovávat více zpráv souběžně. Pokud chcete modul runtime nasměrovat tak, aby zpracovával jenom jednu frontu nebo zprávu o tématu, nastavte `maxConcurrentCalls` na 1. |
|prefetchCount|neuvedeno|Výchozí PrefetchCount, který se použije základní MessageReceiver.|


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)
