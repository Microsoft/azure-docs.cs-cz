---
title: Azure Service Bus vazby pro službu Azure Functions
description: Naučte se používat Azure Service Bus triggerů a vazeb ve službě Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: 424d797410c091dc53687284c2b32e2f1f0358e1
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549066"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus vazby pro službu Azure Functions

Tento článek vysvětluje, jak pracovat s Azure Service Bus vazby ve službě Azure Functions. Služba Azure Functions podporuje vazby výstupu a triggerů pro témata a fronty služby Service Bus.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby služby Service Bus jsou součástí [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) balíčku NuGet, verze 2.x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

Vazby služby Service Bus jsou součástí [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) balíčku NuGet, verze 3.x. Zdrojový kód balíčku je v úložišti GitHub [Azure-Functions-ServiceBus-Extension](https://github.com/Azure/azure-functions-servicebus-extension) .

> [!NOTE]
> Verze 2. x a vyšší nevytvářejí téma nebo předplatné nakonfigurované v instanci `ServiceBusTrigger`. Tyto verze jsou založené na [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) , který nezpracovává správu fronty.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Pomocí aktivační události služby Service Bus můžete reagovat na zprávy z fronty služby Service Bus nebo téma. 

## <a name="trigger---example"></a>Aktivační události – příklad

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který čte [zpráva metadat](#trigger---message-metadata) a zaznamená zprávu fronty služby Service Bus:

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

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Následující příklad ukazuje, jak číst zprávu Service Bus Queue prostřednictvím triggeru.

Vazba Service Bus je definována v *Function. JSON* , kde *typ* je nastaven na `serviceBusTrigger`.

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

## <a name="trigger---attributes-and-annotations"></a>Aktivační události – atributy a poznámky

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

V [knihoven tříd C#](functions-dotnet-class-library.md), můžete nakonfigurovat aktivační událost služby Service Bus následující atributy:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Konstruktor atributu přebírá název fronty nebo tématu a odběru. V Azure Functions verzi 1.x, můžete také zadat připojení přístupová práva. Pokud nezadáte přístupová práva, výchozí hodnota je `Manage`. Další informace najdete v tématu [aktivační události – konfigurace](#trigger---configuration) oddílu.

  Tady je příklad ukazující atribut používaný s parametrem řetězce:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Vlastnost `Connection` můžete nastavit tak, aby určovala název nastavení aplikace, které obsahuje připojovací řetězec Service Bus, který se má použít, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Úplný příklad najdete v tématu [Trigger – příklad](#trigger---example).

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

* `ServiceBusTrigger` Atributu `Connection` vlastnost.
* `ServiceBusAccount` Použije pro stejný parametr, jako `ServiceBusTrigger` atribut.
* `ServiceBusAccount` Použije pro funkci.
* `ServiceBusAccount` Atribut aplikován třídu.
* Nastavení aplikace "AzureWebJobsServiceBus".

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="javatabjava"></a>[Java](#tab/java)

Anotace `ServiceBusQueueTrigger` umožňuje vytvořit funkci, která se spustí při vytvoření zprávy fronty Service Bus. K dispozici jsou možnosti konfigurace, včetně názvu fronty a názvu připojovacího řetězce.

Anotace `ServiceBusTopicTrigger` umožňuje určit téma a předplatné k určení toho, jaká data aktivují funkci.

Další podrobnosti najdete v [příkladu](#trigger---example) triggeru.

---

## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `ServiceBusTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | – | Musí být nastavena na "serviceBusTrigger". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | – | Musí být nastavena na "in". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**name** | – | Název proměnné, která představuje zprávu fronty nebo tématu v kódu funkce. |
|**queueName**|**queueName**|Název fronty k monitorování.  Nastaví jenom v případě, že monitorování frontu, ne pro téma.
|**topicName**|**topicName**|Název tématu, které chcete monitorovat. Nastaví jenom v případě, že monitorování tématu, ne pro frontu.|
|**subscriptionName**|**subscriptionName**|Název odběru, který chcete monitorovat. Nastaví jenom v případě, že monitorování tématu, ne pro frontu.|
|**připojení**|**připojení**|Název nastavení aplikace, které obsahuje připojovací řetězec služby Service Bus má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat jenom zbývající část názvu. Například pokud nastavíte `connection` na "MyServiceBus", modul runtime služby Functions vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyServiceBus." Necháte-li `connection` prázdný, připojovací řetězec služby Service Bus výchozí modul runtime služby Functions používá v nastavení aplikace, který je pojmenován "AzureWebJobsServiceBus".<br><br>Pokud chcete získat připojovací řetězec, postupujte podle kroků uvedených v části [získání přihlašovacích údajů pro správu](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Připojovací řetězec musí být pro obor názvů služby Service Bus, do konkrétní fronty nebo tématu není omezený. |
|**accessRights**|**Přístup**|Přístupová práva připojovacího řetězce. Dostupné jsou hodnoty `manage` a `listen`. Výchozí hodnota je `manage`, což znamená, že `connection` má **spravovat** oprávnění. Pokud používáte připojovací řetězec, který nemá **spravovat** sadu oprávnění, `accessRights` poslouchat "". V opačném případě funkce modulu runtime může selhat pokouší o provedení operace, které vyžadují spravovat práva. V Azure Functions verze 2. x nebo vyšší není tato vlastnost k dispozici, protože nejnovější verze Service Bus SDK nepodporuje operace správy.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`, pokud se připojujete ke frontě nebo předplatnému [využívajícím relaci](../service-bus-messaging/message-sessions.md) . `false` jinak, což je výchozí hodnota.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační události – využití

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Pro zprávu o frontě nebo tématu jsou k dispozici následující typy parametrů:

* `string` – Pokud má zpráva text.
* `byte[]` -Vhodné pro binární data.
* Vlastní typ – Pokud zpráva obsahuje JSON, Azure Functions se pokusí rekonstruovat JSON data.
* `BrokeredMessage` – poskytuje deserializovanou zprávu s metodou [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Tyto typy parametrů jsou pro Azure Functions verze 1. x; u 2. x a vyšších použijte místo `BrokeredMessage`[`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) .

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Pro zprávu o frontě nebo tématu jsou k dispozici následující typy parametrů:

* `string` – Pokud má zpráva text.
* `byte[]` -Vhodné pro binární data.
* Vlastní typ – Pokud zpráva obsahuje JSON, Azure Functions se pokusí rekonstruovat JSON data.
* `BrokeredMessage` – poskytuje deserializovanou zprávu s metodou [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Tyto parametry jsou pro Azure Functions verze 1. x; u 2. x a vyšších použijte místo `BrokeredMessage`[`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) .

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Přístup ke zprávě fronty nebo tématu pomocí `context.bindings.<name from function.json>`. Zprávy služby Service Bus je předán do funkce jako řetězec nebo objekt JSON.

# <a name="pythontabpython"></a>[Python](#tab/python)

Zpráva fronty je k dispozici funkci prostřednictvím parametru zadaného jako `func.ServiceBusMessage`. Zprávy služby Service Bus je předán do funkce jako řetězec nebo objekt JSON.

# <a name="javatabjava"></a>[Java](#tab/java)

Příchozí Service Bus zpráva je k dispozici prostřednictvím parametru `ServiceBusQueueMessage` nebo `ServiceBusTopicMessage`.

[Podrobnosti najdete v příkladu](#trigger).

---

## <a name="trigger---poison-messages"></a>Aktivační události – počet nezpracovatelných zpráv

Manipulaci s nezpracovatelnými zprávami nelze řídit nebo nakonfigurovat ve službě Azure Functions. Service Bus zpracovává nezpracovatelných zpráv samotný.

## <a name="trigger---peeklock-behavior"></a>Aktivační události – PeekLock chování

Modul runtime služby Functions přijme zprávu v [PeekLock režimu](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Volá `Complete` na zprávu, pokud funkci dokončí úspěšně, nebo volání `Abandon` Pokud funkce selže. Pokud je funkce spuštěná déle, než `PeekLock` vypršení časového limitu, zámek se obnovuje automaticky za předpokladu, že funkce běží. 

`maxAutoRenewDuration` lze konfigurovat v *Host. JSON*, který se mapuje na [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Maximální hodnotu povolenou pro toto nastavení je 5 minut podle dokumentace k Service Bus, že můžete zvýšit časový limit funkce z výchozí hodnoty 5 minut až 10 minut. Pro funkce služby Service Bus by má k tomu pak, protože by překročilo limit obnovení služby Service Bus.

## <a name="trigger---message-metadata"></a>Aktivační události – zpráva metadat

Aktivační události služby Service Bus nabízí několik [vlastnosti metadat](./functions-bindings-expressions-patterns.md#trigger-metadata). Tyto vlastnosti lze použít jako součást výrazy vazby v jiných vazbách nebo jako parametry v kódu. Toto jsou vlastnosti [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) třídy.

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
|`Label`|`string`|Popisek specifický pro aplikaci.|
|`CorrelationId`|`string`|ID korelace.|

> [!NOTE]
> V současné době je Service Bus aktivační událost, která funguje s frontami s povolenou relací a odběry ve verzi Preview. Sledujte prosím [tuto položku](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) pro všechny další aktualizace, které se týkají tohoto. 

Zobrazit [příklady kódu](#trigger---example) , které používají tyto vlastnosti dříve v tomto článku.

## <a name="output"></a>Výstup

Použití Azure Service Bus výstupní vazby pro odesílání zpráv fronty nebo tématu.

### <a name="output---example"></a>Výstup – příklad

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , která odešle zprávu fronty služby Service Bus:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Následující příklad ukazuje, jak zapisovat do fronty Service Bus v Pythonu.

Definice vazby Service Bus je definována v *Function. JSON* , kde *typ* je nastaven na `serviceBus`.

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

## <a name="output---attributes-and-annotations"></a>Výstup – atributy a poznámky

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Konstruktor atributu přebírá název fronty nebo tématu a odběru. Můžete také zadat připojení přístupová práva. Jak zvolit přístupová práva, nastavení je podrobně [výstup - konfigurace](#output---configuration) oddílu. Tady je příklad ukazující použije návratovou hodnotu funkce:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Vlastnost `Connection` můžete nastavit tak, aby určovala název nastavení aplikace, které obsahuje připojovací řetězec Service Bus, který se má použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v tématu [Output-example](#output---example).

Můžete použít `ServiceBusAccount` atribut zadat účet služby Service Bus na úrovni třídy, metody nebo parametr.  Další informace najdete v tématu [aktivační události – atributy](#trigger---attributes-and-annotations).

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="javatabjava"></a>[Java](#tab/java)

Poznámky `ServiceBusQueueOutput` a `ServiceBusTopicOutput` jsou k dispozici pro zápis zprávy jako výstup funkce. Parametr upravený pomocí těchto poznámek musí být deklarován jako `OutputBinding<T>`, kde `T` je typ odpovídající typu zprávy.

---

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `ServiceBus` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | – | Musí být nastavena na "služby"Service Bus. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | – | Musí být nastavena na "out". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**name** | – | Název proměnné, která představuje zprávu fronty nebo tématu v kódu funkce. Nastavte na "$return" tak, aby odkazovaly návratovou hodnotu funkce. |
|**queueName**|**queueName**|Název fronty.  Nastaví jenom v případě, že odesílá zprávy do fronty, ne pro téma.
|**topicName**|**topicName**|Název tématu. Nastaví jenom v případě, že odesílání zpráv tématu, ne pro frontu.|
|**připojení**|**připojení**|Název nastavení aplikace, které obsahuje připojovací řetězec služby Service Bus má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat jenom zbývající část názvu. Například pokud nastavíte `connection` na "MyServiceBus", modul runtime služby Functions vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyServiceBus." Necháte-li `connection` prázdný, připojovací řetězec služby Service Bus výchozí modul runtime služby Functions používá v nastavení aplikace, který je pojmenován "AzureWebJobsServiceBus".<br><br>Pokud chcete získat připojovací řetězec, postupujte podle kroků uvedených v části [získání přihlašovacích údajů pro správu](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Připojovací řetězec musí být pro obor názvů služby Service Bus, do konkrétní fronty nebo tématu není omezený.|
|**accessRights**|**Přístup**|Přístupová práva připojovacího řetězce. Dostupné jsou hodnoty `manage` a `listen`. Výchozí hodnota je `manage`, což znamená, že `connection` má **spravovat** oprávnění. Pokud používáte připojovací řetězec, který nemá **spravovat** sadu oprávnění, `accessRights` poslouchat "". V opačném případě funkce modulu runtime může selhat pokouší o provedení operace, které vyžadují spravovat práva. V Azure Functions verze 2. x nebo vyšší není tato vlastnost k dispozici, protože nejnovější verze Service Bus SDK nepodporuje operace správy.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

Ve službě Azure Functions 1.x, modul runtime vytvoří frontu Pokud neexistuje a nastavili jste `accessRights` k `manage`. V funkcích Functions verze 2. x a vyšší již musí fronta nebo téma existovat; Pokud zadáte frontu nebo téma, které neexistují, funkce se nezdaří. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Pro výstupní vazbu použijte následující typy parametrů:

* `out T paramName` - `T` může být libovolný typ serializovat na JSON. Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce vytvoří zprávu s objekt s hodnotou null.
* `out string` – Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce nevytváří zprávu.
* `out byte[]` – Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce nevytváří zprávu.
* `out BrokeredMessage` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro funkce 1. x).
* `out Message` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro Functions 2. x a vyšší).
* `ICollector<T>` nebo `IAsyncCollector<T>` – pro vytváření více zpráv. Zpráva se vytvoří při volání `Add` metody.

Při práci s C# funkcemi:

* Asynchronní funkce místo parametru `out` vyžadují návratovou hodnotu nebo `IAsyncCollector`.

* Chcete-li získat přístup k ID relace, vytvořte připojení k typu [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) a použijte vlastnost `sessionId`.

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Pro výstupní vazbu použijte následující typy parametrů:

* `out T paramName` - `T` může být libovolný typ serializovat na JSON. Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce vytvoří zprávu s objekt s hodnotou null.
* `out string` – Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce nevytváří zprávu.
* `out byte[]` – Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce nevytváří zprávu.
* `out BrokeredMessage` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro funkce 1. x).
* `out Message` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro Functions 2. x a vyšší).
* `ICollector<T>` nebo `IAsyncCollector<T>` – pro vytváření více zpráv. Zpráva se vytvoří při volání `Add` metody.

Při práci s C# funkcemi:

* Asynchronní funkce místo parametru `out` vyžadují návratovou hodnotu nebo `IAsyncCollector`.

* Chcete-li získat přístup k ID relace, vytvořte připojení k typu [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) a použijte vlastnost `sessionId`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Přístup ke frontě nebo tématu pomocí `context.bindings.<name from function.json>`. Pro `context.binding.<name>`můžete přiřadit řetězec, bajtové pole nebo objekt JavaScriptu (deserializovat do formátu JSON).

# <a name="pythontabpython"></a>[Python](#tab/python)

Místo předdefinované výstupní vazby použijte [sadu Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) .

# <a name="javatabjava"></a>[Java](#tab/java)

Místo předdefinované výstupní vazby použijte [sadu Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) .

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Service Bus | [Kódy chyb služby Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Omezení služby Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení Host.JSON

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2. x a vyšší. Ukázkový soubor host. JSON obsahuje pouze nastavení této vazby. Další informace o nastavení globálních konfigurací naleznete v tématu [reference Host. JSON pro verzi Azure Functions](functions-host-json.md).

> [!NOTE]
> Pro odkaz host.json ve funkcích 1.x, najdete v článku [referenční materiály k host.json pro Azure Functions 1.x](functions-host-json-v1.md).

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
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|Maximální doba, ve kterém se automatické obnovení zámku zprávy.|
|Automatické dokončování|true|Určuje, zda má aktivační událost zprávu hned označit jako dokončenou (automatické dokončování), nebo počkat na úspěšné ukončení funkce, aby bylo volání dokončeno.|
|maxConcurrentCalls|16|Maximální počet souběžných volání zpětného volání, které by mělo zahájit pumpu zpráv. Ve výchozím nastavení modul runtime služby Functions zpracovávat více zpráv souběžně. Chcete-li řídit modul runtime najednou zpracovat pouze jedné frontě nebo tématu zprávy, nastavte `maxConcurrentCalls` na hodnotu 1. |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
