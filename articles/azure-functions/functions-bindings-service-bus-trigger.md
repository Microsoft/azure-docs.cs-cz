---
title: Azure Service Bus vazby pro službu Azure Functions
description: Naučte se spouštět službu Azure Function při vytváření Azure Service Busch zpráv.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273569"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Aktivační událost Azure Service Bus pro Azure Functions

Pomocí aktivační události služby Service Bus můžete reagovat na zprávy z fronty služby Service Bus nebo téma.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](functions-bindings-service-bus-output.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která čte [metadata zprávy](#message-metadata) a protokoluje zprávu fronty Service Bus:

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

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

Následující příklad ukazuje Service Bus aktivační vazby v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce přečte [metadata zprávy](#message-metadata) a zaprotokoluje zprávu fronty Service Bus.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje Service Bus aktivační vazby v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce přečte [metadata zprávy](#message-metadata) a zaprotokoluje zprávu fronty Service Bus. 

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

# <a name="python"></a>[Python](#tab/python)

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

Kód v  *_\_init_\_. py* deklaruje parametr jako `func.ServiceBusMessage`, což umožňuje číst ve funkci zprávu fronty.

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

# <a name="java"></a>[Java](#tab/java)

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

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte následující atributy ke konfiguraci aktivační události Service Bus:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Konstruktor atributu přebírá název fronty nebo tématu a odběru. V Azure Functions verzi 1.x, můžete také zadat připojení přístupová práva. Pokud nezadáte přístupová práva, výchozí hodnota je `Manage`. Další informace najdete v části věnované [konfiguraci triggeru](#configuration) .

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

  Úplný příklad najdete v tématu [Trigger – příklad](#example).

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

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

Anotace `ServiceBusQueueTrigger` umožňuje vytvořit funkci, která se spustí při vytvoření zprávy fronty Service Bus. K dispozici jsou možnosti konfigurace, včetně názvu fronty a názvu připojovacího řetězce.

Anotace `ServiceBusTopicTrigger` umožňuje určit téma a předplatné k určení toho, jaká data aktivují funkci.

Další podrobnosti najdete v [příkladu](#example) triggeru.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `ServiceBusTrigger`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavena na "serviceBusTrigger". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastavena na "in". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje zprávu fronty nebo tématu v kódu funkce. |
|**Proměnné QueueName**|**Proměnné QueueName**|Název fronty k monitorování.  Nastaví jenom v případě, že monitorování frontu, ne pro téma.
|**téma**|**Téma**|Název tématu, které chcete monitorovat. Nastaví jenom v případě, že monitorování tématu, ne pro frontu.|
|**subscriptionName**|**SubscriptionName**|Název odběru, který chcete monitorovat. Nastaví jenom v případě, že monitorování tématu, ne pro frontu.|
|**vázán**|**Vázán**|Název nastavení aplikace, které obsahuje připojovací řetězec služby Service Bus má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat jenom zbývající část názvu. Například pokud nastavíte `connection` na "MyServiceBus", modul runtime Functions vyhledá nastavení aplikace s názvem "AzureWebJobsMyServiceBus". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec Service Bus v nastavení aplikace s názvem "AzureWebJobsServiceBus".<br><br>Pokud chcete získat připojovací řetězec, postupujte podle kroků uvedených v části [získání přihlašovacích údajů pro správu](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Připojovací řetězec musí být pro obor názvů služby Service Bus, do konkrétní fronty nebo tématu není omezený. |
|**accessRights**|**Přístup**|Přístupová práva připojovacího řetězce. Dostupné hodnoty jsou `manage` a `listen`. Výchozí hodnota je `manage`, což znamená, že `connection` má oprávnění **Spravovat** . Pokud použijete připojovací řetězec, který nemá oprávnění **Spravovat** , nastavte `accessRights` na "naslouchání". V opačném případě funkce modulu runtime může selhat pokouší o provedení operace, které vyžadují spravovat práva. V Azure Functions verze 2. x nebo vyšší není tato vlastnost k dispozici, protože nejnovější verze Service Bus SDK nepodporuje operace správy.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`, pokud se připojujete ke frontě nebo předplatnému [využívajícím relaci](../service-bus-messaging/message-sessions.md) . `false` jinak, což je výchozí hodnota.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

Pro zprávu o frontě nebo tématu jsou k dispozici následující typy parametrů:

* `string` – Pokud se jedná o text zprávy
* `byte[]` – užitečné pro binární data.
* Vlastní typ – Pokud zpráva obsahuje JSON, Azure Functions se pokusí rekonstruovat JSON data.
* `BrokeredMessage` – poskytuje deserializovanou zprávu s metodou [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Tyto typy parametrů jsou pro Azure Functions verze 1. x; u 2. x a vyšších použijte místo `BrokeredMessage`[`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) .

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

Pro zprávu o frontě nebo tématu jsou k dispozici následující typy parametrů:

* `string` – Pokud se jedná o text zprávy
* `byte[]` – užitečné pro binární data.
* Vlastní typ – Pokud zpráva obsahuje JSON, Azure Functions se pokusí rekonstruovat JSON data.
* `BrokeredMessage` – poskytuje deserializovanou zprávu s metodou [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Tyto parametry jsou pro Azure Functions verze 1. x; u 2. x a vyšších použijte místo `BrokeredMessage`[`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup ke zprávě fronty nebo tématu pomocí `context.bindings.<name from function.json>`. Zprávy služby Service Bus je předán do funkce jako řetězec nebo objekt JSON.

# <a name="python"></a>[Python](#tab/python)

Zpráva fronty je k dispozici funkci prostřednictvím parametru zadaného jako `func.ServiceBusMessage`. Zprávy služby Service Bus je předán do funkce jako řetězec nebo objekt JSON.

# <a name="java"></a>[Java](#tab/java)

Příchozí Service Bus zpráva je k dispozici prostřednictvím parametru `ServiceBusQueueMessage` nebo `ServiceBusTopicMessage`.

[Podrobnosti najdete v příkladu](#example).

---

## <a name="poison-messages"></a>Poškozené zprávy

Manipulaci s nezpracovatelnými zprávami nelze řídit nebo nakonfigurovat ve službě Azure Functions. Service Bus zpracovává nezpracovatelných zpráv samotný.

## <a name="peeklock-behavior"></a>Chování PeekLock

Modul runtime Functions obdrží zprávu v [režimu PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Volá `Complete` ve zprávě, pokud se funkce dokončí úspěšně, nebo zavolá `Abandon`, pokud se funkce nezdaří. Pokud je funkce spuštěná déle než `PeekLock` časový limit, zámek se automaticky obnoví, dokud je funkce spuštěná. 

`maxAutoRenewDuration` lze konfigurovat v *Host. JSON*, který se mapuje na [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Maximální hodnotu povolenou pro toto nastavení je 5 minut podle dokumentace k Service Bus, že můžete zvýšit časový limit funkce z výchozí hodnoty 5 minut až 10 minut. Pro funkce služby Service Bus by má k tomu pak, protože by překročilo limit obnovení služby Service Bus.

## <a name="message-metadata"></a>Metadata zprávy

Aktivační událost Service Bus poskytuje několik [vlastností metadat](./functions-bindings-expressions-patterns.md#trigger-metadata). Tyto vlastnosti lze použít jako součást výrazy vazby v jiných vazbách nebo jako parametry v kódu. Tyto vlastnosti jsou členy třídy [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) .

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Počet doručení.|
|`DeadLetterSource`|`string`|Zdroj nedoručených zpráv.|
|`ExpiresAtUtc`|`DateTime`|Čas vypršení platnosti ve standardu UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Čas zařazení do fronty ve standardu UTC.|
|`MessageId`|`string`|Uživatelem definovanou hodnotu, která služby Service Bus můžete použít k identifikaci duplicitních zpráv, pokud je povoleno.|
|`ContentType`|`string`|Identifikátor typu obsahu využitý odesílatelem a příjemcem pro logiku specifickou pro aplikaci.|
|`ReplyTo`|`string`|Odpověď na adresa fronty.|
|`SequenceNumber`|`Int64`|Jedinečné číslo přiřazené zprávy ve službě Service Bus.|
|`To`|`string`|Odeslání na adresu.|
|`Label`|`string`|Popisek specifický pro aplikaci.|
|`CorrelationId`|`string`|ID korelace.|

Podívejte se na [Příklady kódu](#example) , které používají tyto vlastnosti dříve v tomto článku.

## <a name="next-steps"></a>Další kroky

- [Posílání zpráv Azure Service Bus z Azure Functions (výstupní vazba)](./functions-bindings-service-bus-output.md)
