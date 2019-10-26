---
title: Azure Service Bus vazby pro Azure Functions
description: Naučte se používat Azure Service Bus triggery a vazby v Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: funkce Azure Functions, Functions, zpracování událostí, dynamická výpočetní funkce a architektura bez serveru
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: c39a2e8daf9ca46902cf1a1fac89c59918a6854d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934335"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus vazby pro Azure Functions

Tento článek vysvětluje, jak pracovat s Azure Service Busmi vazbami v Azure Functions. Azure Functions podporuje triggery a výstupní vazby pro Service Bus fronty a témata.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1. x

Vazby Service Bus jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) , verze 2. x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2. x

Vazby Service Bus jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) , verze 3. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/) .

> [!NOTE]
> Verze 2. x nevytváří téma ani předplatné nakonfigurované v instanci `ServiceBusTrigger`. Verze 2. x je založená na [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) a nezpracovává správu fronty.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Pokud chcete reagovat na zprávy z fronty nebo tématu Service Bus, použijte Trigger Service Bus. 

## <a name="trigger---example"></a>Aktivační procedura – příklad

Podívejte se na příklad konkrétního jazyka:

* [C#](#trigger---c-example)
* [C#skript (. csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Aktivační procedura C# – příklad

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která čte [metadata zprávy](#trigger---message-metadata) a protokoluje zprávu fronty Service Bus:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
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

Tento příklad je určen pro Azure Functions verze 1. x. Chcete-li tento kód použít pro 2. x:

- [vynechat parametr Access Rights](#trigger---configuration)
- Změňte typ parametru protokolu z `TraceWriter` na `ILogger`
- změnit `log.Info` na `log.LogInformation`

### <a name="trigger---c-script-example"></a>Aktivační procedura C# – příklad skriptu

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

Tady je kód C# skriptu:

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

### <a name="trigger---f-example"></a>Aktivační procedura F# – příklad

Následující příklad ukazuje Service Bus aktivační vazby v souboru *Function. JSON* a [ F# funkci](functions-reference-fsharp.md) , která používá vazbu. Funkce zaznamená zprávu fronty Service Bus. 

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

Tady je kód F# skriptu:

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>Trigger – příklad Java

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

### <a name="trigger---javascript-example"></a>Trigger – příklad JavaScriptu

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

Tady je kód skriptu JavaScriptu:

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

## <a name="trigger---attributes"></a>Aktivační atributy

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte následující atributy ke konfiguraci aktivační události Service Bus:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Konstruktor atributu přebírá název fronty nebo tématu a předplatného. V Azure Functions verze 1. x můžete také zadat přístupová práva připojení. Pokud nezadáte přístupová práva, výchozí hodnota je `Manage`. Další informace najdete v části věnované [konfiguraci triggeru](#trigger---configuration) .

  Tady je příklad, který ukazuje atribut použitý s parametrem řetězce:

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

  Poskytuje další způsob, jak určit účet Service Bus, který se má použít. Konstruktor převezme název nastavení aplikace, které obsahuje připojovací řetězec Service Bus. Atribut lze použít na úrovni parametru, metody nebo třídy. Následující příklad ukazuje úroveň třídy a úroveň metody:

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

Účet Service Bus, který se má použít, je určen v následujícím pořadí:

* Vlastnost `Connection` atributu `ServiceBusTrigger`.
* Atribut `ServiceBusAccount` aplikovaný na stejný parametr jako atribut `ServiceBusTrigger`.
* Atribut `ServiceBusAccount` aplikovaný na funkci.
* Atribut `ServiceBusAccount` aplikovaný na třídu.
* Nastavení aplikace "AzureWebJobsServiceBus".

## <a name="trigger---configuration"></a>Aktivační událost – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `ServiceBusTrigger`.

|Function. JSON – vlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | – | Musí být nastavené na "serviceBusTrigger". Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal.|
|**direction** | – | Musí být nastavené na "in". Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal. |
|**Jméno** | – | Název proměnné, která představuje zprávu fronty nebo tématu v kódu funkce. Nastavte na "$return", chcete-li odkazovat na návratovou hodnotu funkce. |
|**Proměnné QueueName**|**Proměnné QueueName**|Název fronty, která se má monitorovat  Nastavte pouze v případě, že monitorování fronty, nikoli tématu.
|**téma**|**Téma**|Název tématu, které se má monitorovat Nastavte pouze v případě, že monitorování tématu, nikoli pro frontu.|
|**subscriptionName**|**SubscriptionName**|Název předplatného, které se má monitorovat Nastavte pouze v případě, že monitorování tématu, nikoli pro frontu.|
|**vázán**|**Vázán**|Název nastavení aplikace, které obsahuje připojovací řetězec Service Bus, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyServiceBus", modul runtime Functions vyhledá nastavení aplikace s názvem "AzureWebJobsMyServiceBus". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec Service Bus v nastavení aplikace s názvem "AzureWebJobsServiceBus".<br><br>Pokud chcete získat připojovací řetězec, postupujte podle kroků uvedených v části [získání přihlašovacích údajů pro správu](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Připojovací řetězec musí být pro obor názvů Service Bus, který není omezen na konkrétní frontu nebo téma. |
|**accessRights**|**Přístup**|Přístupová práva k připojovacímu řetězci Dostupné hodnoty jsou `manage` a `listen`. Výchozí hodnota je `manage`, což znamená, že `connection` má oprávnění **Spravovat** . Pokud použijete připojovací řetězec, který nemá oprávnění **Spravovat** , nastavte `accessRights` na "naslouchání". V opačném případě může modul runtime Functions selhat při pokusu o provedení operací, které vyžadují oprávnění ke správě. V Azure Functions verze 2. x není tato vlastnost k dispozici, protože nejnovější verze sady SDK úložiště nepodporuje operace správy.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační událost – využití

V C# rámci C# skriptu a můžete pro zprávu o frontě nebo tématu použít následující typy parametrů:

* `string` – Pokud se jedná o text zprávy
* `byte[]` – užitečné pro binární data.
* Vlastní typ – Pokud zpráva obsahuje JSON, Azure Functions se pokusí deserializovat data JSON.
* `BrokeredMessage` – poskytuje deserializovanou zprávu s metodou [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Tyto parametry jsou pro Azure Functions verze 1. x; pro 2. x použijte [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) místo `BrokeredMessage`.

V jazyce JavaScript přihlaste ke zprávě fronty nebo tématu pomocí `context.bindings.<name from function.json>`. Zpráva Service Bus se předává do funkce jako objekt String nebo JSON.

## <a name="trigger---poison-messages"></a>Aktivační události – nepoškozené zprávy

V Azure Functions nelze řídit ani konfigurovat zpracování nezpracovatelných zpráv. Service Bus zpracovává samotné poškozené zprávy.

## <a name="trigger---peeklock-behavior"></a>PeekLock chování triggeru

Modul runtime Functions obdrží zprávu v [režimu PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Volá `Complete` ve zprávě, pokud se funkce dokončí úspěšně, nebo zavolá `Abandon`, pokud se funkce nezdaří. Pokud je funkce spuštěná déle než `PeekLock` časový limit, zámek se automaticky obnoví, dokud je funkce spuštěná. 

`maxAutoRenewDuration` lze konfigurovat v *Host. JSON*, který se mapuje na [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Maximální povolený počet pro toto nastavení je 5 minut v závislosti na Service Bus dokumentaci, zatímco časový limit funkcí můžete zvýšit z výchozí hodnoty 5 minut na 10 minut. U Service Busch funkcí byste to neudělali, protože byste překročili Service Bus limit obnovení.

## <a name="trigger---message-metadata"></a>Metadata aktivačních zpráv

Aktivační událost Service Bus poskytuje několik [vlastností metadat](./functions-bindings-expressions-patterns.md#trigger-metadata). Tyto vlastnosti lze použít jako součást výrazů vazby v jiných vazbách nebo jako parametry v kódu. Jedná se o vlastnosti třídy [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) .

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Počet doručení.|
|`DeadLetterSource`|`string`|Zdroj nedoručených zpráv.|
|`ExpiresAtUtc`|`DateTime`|Čas vypršení platnosti ve standardu UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Čas zařazení do fronty ve standardu UTC.|
|`MessageId`|`string`|Uživatelem definovaná hodnota, kterou Service Bus může použít k identifikaci duplicitních zpráv, pokud je povoleno.|
|`ContentType`|`string`|Identifikátor typu obsahu využitý odesílatelem a příjemcem pro logiku specifickou pro aplikaci.|
|`ReplyTo`|`string`|Odpověď na adresu fronty|
|`SequenceNumber`|`Int64`|Jedinečné číslo přiřazené zprávě Service Bus.|
|`To`|`string`|Adresa pro odeslání.|
|`Label`|`string`|Popisek specifický pro aplikaci.|
|`CorrelationId`|`string`|ID korelace.|

> [!NOTE]
> V současné době je Trigger Service Bus, který funguje s frontami a odběry s povolenou relací, ve verzi Preview. Sledujte prosím [tuto položku](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) pro všechny další aktualizace, které se týkají tohoto. 

Podívejte se na [Příklady kódu](#trigger---example) , které používají tyto vlastnosti dříve v tomto článku.

## <a name="trigger---hostjson-properties"></a>Trigger – vlastnosti Host. JSON

Soubor [Host. JSON](functions-host-json.md#servicebus) obsahuje nastavení, která řídí chování triggeru Service Bus.

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "maxAutoRenewDuration": "00:05:00"
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|maxConcurrentCalls|16|Maximální počet souběžných volání zpětného volání, které by mělo iniciovat čerpadlo zpráv. Ve výchozím nastavení aplikace runtime Functions zpracovává více zpráv souběžně. Pokud chcete modul runtime nasměrovat tak, aby zpracovával jenom jednu frontu nebo zprávu o tématu, nastavte `maxConcurrentCalls` na 1. |
|prefetchCount|–|Výchozí PrefetchCount, které bude používat základní MessageReceiver.|
|maxAutoRenewDuration|00:05:00|Maximální doba, během které bude zámek zprávy obnoven automaticky.|

## <a name="output"></a>Výstup

Pro posílání zpráv fronty nebo témat použít výstupní vazbu Azure Service Bus.

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad konkrétního jazyka:

* [C#](#output---c-example)
* [C#skript (. csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Výstup – C# příklad

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

### <a name="output---c-script-example"></a>Ukázka výstupního C# skriptu

Následující příklad ukazuje výstupní vazbu Service Bus v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce používá aktivační událost časovače k odeslání zprávy fronty každých 15 sekund.

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

Zde je C# kód skriptu, který vytváří jednu zprávu:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Zde je C# kód skriptu, který vytváří více zpráv:

```cs
public static void Run(TimerInfo myTimer, ILogger log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Výstup – F# příklad

Následující příklad ukazuje výstupní vazbu Service Bus v souboru *Function. JSON* a [ F# funkci skriptu](functions-reference-fsharp.md) , která používá vazbu. Funkce používá aktivační událost časovače k odeslání zprávy fronty každých 15 sekund.

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

Zde je F# kód skriptu, který vytváří jednu zprávu:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>Výstup – příklad Java

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

 V [běhové knihovně funkcí jazyka Java](/java/api/overview/azure/functions/runtime)použijte v parametrech funkce anotaci `@QueueOutput`, jejichž hodnota by byla zapsána do fronty Service Bus.  Typ parametru by měl být `OutputBinding<T>`, kde T je jakýkoliv nativní typ Java POJO.

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

### <a name="output---javascript-example"></a>Výstup – příklad JavaScriptu

Následující příklad ukazuje výstupní vazbu Service Bus v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce používá aktivační událost časovače k odeslání zprávy fronty každých 15 sekund.

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

Tady je kód skriptu JavaScriptu, který vytvoří jednu zprávu:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Tady je kód skriptu JavaScriptu, který vytváří několik zpráv:

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

## <a name="output---attributes"></a>Výstupní atributy

V [ C# knihovně tříd](functions-dotnet-class-library.md)použijte [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Konstruktor atributu přebírá název fronty nebo tématu a předplatného. Můžete také zadat přístupová práva pro připojení. Jak zvolit nastavení přístupových práv je vysvětleno v části [výstup – konfigurace](#output---configuration) . Tady je příklad, který ukazuje atribut aplikovaný na návratovou hodnotu funkce:

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

|Function. JSON – vlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | – | Musí být nastavené na "serviceBus". Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal.|
|**direction** | – | Musí být nastavené na "out". Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal. |
|**Jméno** | – | Název proměnné, která představuje frontu nebo téma v kódu funkce. Nastavte na "$return", chcete-li odkazovat na návratovou hodnotu funkce. |
|**Proměnné QueueName**|**Proměnné QueueName**|Název fronty  Nastavte pouze v případě, že jsou odesílány zprávy fronty, nikoli téma.
|**téma**|**Téma**|Název tématu, které se má monitorovat Nastaveno pouze při posílání zpráv témat, nikoli pro frontu.|
|**vázán**|**Vázán**|Název nastavení aplikace, které obsahuje připojovací řetězec Service Bus, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyServiceBus", modul runtime Functions vyhledá nastavení aplikace s názvem "AzureWebJobsMyServiceBus". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec Service Bus v nastavení aplikace s názvem "AzureWebJobsServiceBus".<br><br>Pokud chcete získat připojovací řetězec, postupujte podle kroků uvedených v části [získání přihlašovacích údajů pro správu](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Připojovací řetězec musí být pro obor názvů Service Bus, který není omezen na konkrétní frontu nebo téma.|
|**accessRights**|**Přístup**|Přístupová práva k připojovacímu řetězci Dostupné hodnoty jsou `manage` a `listen`. Výchozí hodnota je `manage`, což znamená, že `connection` má oprávnění **Spravovat** . Pokud použijete připojovací řetězec, který nemá oprávnění **Spravovat** , nastavte `accessRights` na "naslouchání". V opačném případě může modul runtime Functions selhat při pokusu o provedení operací, které vyžadují oprávnění ke správě. V Azure Functions verze 2. x není tato vlastnost k dispozici, protože nejnovější verze sady SDK úložiště nepodporuje operace správy.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

V Azure Functions 1. x modul runtime vytvoří frontu, pokud neexistuje a že jste nastavili `accessRights` na `manage`. Ve funkcích verze 2. x už musí existovat fronta nebo téma; Pokud zadáte frontu nebo téma, které neexistují, funkce se nezdaří. 

Ve C# skriptu C# a můžete pro výstupní vazbu použít následující typy parametrů:

* `out T paramName` - `T` může být jakýkoli typ serializovatelný pro JSON. Pokud má parametr hodnotu null, když funkce skončí, funkce vytvoří zprávu s objektem s hodnotou null.
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

| Ovládacího | Referenční informace |
|---|---|
| Service Bus | [Kódy chyb Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Omezení Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení Host. JSON

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzi 2. x. Ukázkový soubor host. JSON níže obsahuje pouze nastavení verze 2. x pro tuto vazbu. Další informace o globálních nastaveních konfigurace verze 2. x naleznete v tématu [reference Host. JSON pro Azure Functions verze 2. x](functions-host-json.md).

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
|maxAutoRenewDuration|00:05:00|Maximální doba, během které bude zámek zprávy obnoven automaticky.|
|Zobrazovat|true|Zda má Trigger okamžitě označit jako dokončený (automatické dokončování) nebo počkat na dokončení zpracování.|
|maxConcurrentCalls|16|Maximální počet souběžných volání zpětného volání, které by mělo iniciovat čerpadlo zpráv. Ve výchozím nastavení aplikace runtime Functions zpracovává více zpráv souběžně. Pokud chcete modul runtime nasměrovat tak, aby zpracovával jenom jednu frontu nebo zprávu o tématu, nastavte `maxConcurrentCalls` na 1. |
|prefetchCount|–|Výchozí PrefetchCount, které bude používat základní MessageReceiver.|


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)
