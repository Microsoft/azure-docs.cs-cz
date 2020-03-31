---
title: Vazby Azure Service Bus pro funkce Azure
description: Naučte se spouštět funkci Azure při vytváření zpráv Azure Service Bus.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273569"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Aktivační událost Azure Service Bus pro funkce Azure

Pomocí aktivační události služby Service Bus můžete reagovat na zprávy z fronty nebo tématu služby Service Bus.

Informace o nastavení a konfiguraci naleznete v [přehledu](functions-bindings-service-bus-output.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [funkci C#,](functions-dotnet-class-library.md) která čte [metadata zprávy](#message-metadata) a zaznamenává zprávu fronty služby Service Bus:

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

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje vazby aktivační události service bus v souboru *function.json* a [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která používá vazbu. Funkce přečte [metadata zprávy](#message-metadata) a zaznamená zprávu fronty služby Service Bus.

Zde jsou data vazby v souboru *function.json:*

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

Zde je kód skriptu C#:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje vazby aktivační události service bus v souboru *function.json* a [funkci JavaScript,](functions-reference-node.md) která používá vazbu. Funkce přečte [metadata zprávy](#message-metadata) a zaznamená zprávu fronty služby Service Bus. 

Zde jsou data vazby v souboru *function.json:*

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

Zde je kód skriptu JavaScript:

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

Následující příklad ukazuje, jak číst zprávu fronty služby Service Bus prostřednictvím aktivační události.

Vazba služby Service Bus je definována v `serviceBusTrigger` *souboru function.json,* kde je *typ* nastaven na .

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

Kód v * _ \_init_\_.py* deklaruje parametr jako `func.ServiceBusMessage`, který umožňuje číst zprávu fronty ve vaší funkci.

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

Následující funkce Jazyka `@ServiceBusQueueTrigger` Java používá poznámku z [knihovny runtime funkcí jazyka Java](/java/api/overview/azure/functions/runtime) k popisu konfigurace aktivační události fronty služby Service Bus. Funkce uchopí zprávu umístěnou ve frontě a přidá ji do protokolů.

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

Funkce jazyka Java lze také aktivovat při přidání zprávy do tématu service bus. Následující příklad používá `@ServiceBusTopicTrigger` poznámku k popisu konfigurace aktivační události.

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

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte ke konfiguraci aktivační události služby Service Bus následující atributy:

* [Atribut servicebustrigger](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Konstruktor atributu přebírá název fronty nebo téma a odběr. V Azure Functions verze 1.x můžete také určit přístupová práva připojení. Pokud nezadáte přístupová práva, výchozí `Manage`hodnota je . Další informace naleznete v části [Trigger - configuration.](#configuration)

  Zde je příklad, který ukazuje atribut použitý s parametrem řetězce:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Vlastnost můžete `Connection` nastavit tak, aby určila název nastavení aplikace, které obsahuje připojovací řetězec Service Bus, který chcete použít, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Úplný příklad naleznete v tématu [Trigger - example](#example).

* [Atribut účtu ServiceBus](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Poskytuje další způsob, jak zadat účet Service Bus, který se má použít. Konstruktor přebírá název nastavení aplikace, která obsahuje připojovací řetězec Service Bus. Atribut lze použít na úrovni parametru, metody nebo třídy. Následující příklad ukazuje úroveň třídy a úroveň metody:

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

Účet service bus, který chcete použít, je určen v následujícím pořadí:

* Vlastnost `ServiceBusTrigger` atributu. `Connection`
* Atribut `ServiceBusAccount` použitý na stejný parametr `ServiceBusTrigger` jako atribut.
* Atribut `ServiceBusAccount` použitý pro funkci.
* Atribut `ServiceBusAccount` použitý pro třídu.
* Nastavení aplikace "AzureWebJobsServiceBus".

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Anotace `ServiceBusQueueTrigger` umožňuje vytvořit funkci, která se spustí při vytvoření zprávy fronty služby Service Bus. Mezi dostupné možnosti konfigurace patří název fronty a název připojovacího řetězce.

Anotace `ServiceBusTopicTrigger` umožňuje určit téma a odběr cílit, jaká data spustí funkci.

Další podrobnosti naleznete v [příkladu](#example) aktivační události.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `ServiceBusTrigger` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být nastavena na "serviceBusTrigger". Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastavena na "in". Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje zprávu fronty nebo tématu v kódu funkce. |
|**název_fronty**|**Název fronty**|Název fronty ke sledování.  Nastavte pouze v případě, že sledování fronty, nikoli pro téma.
|**topicName**|**Název_tématu**|Název tématu ke sledování. Nastavte pouze v případě, že sledování tématu, nikoli pro frontu.|
|**název předplatného**|**Název předplatného**|Název předplatného ke sledování. Nastavte pouze v případě, že sledování tématu, nikoli pro frontu.|
|**Připojení**|**Připojení**|Název nastavení aplikace, která obsahuje připojovací řetězec Service Bus použít pro tuto vazbu. Pokud název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytek názvu. Například pokud nastavíte `connection` "MyServiceBus", funkce runtime hledá nastavení aplikace s názvem "AzureWebJobsMyServiceBus". Pokud necháte `connection` prázdné, spustí se funkce runtime použije výchozí řetězec připojení Service Bus v nastavení aplikace s názvem "AzureWebJobsServiceBus".<br><br>Chcete-li získat připojovací řetězec, postupujte podle kroků uvedených v části [Získání pověření pro správu](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Připojovací řetězec musí být pro obor názvů service bus, není omezen na konkrétní frontu nebo téma. |
|**accessRights**|**Přístup**|Přístupová práva pro připojovací řetězec. Dostupné hodnoty `manage` `listen`jsou a . Výchozí hodnota `manage`je , `connection` což znamená, že má **oprávnění Spravovat.** Pokud používáte připojovací řetězec, který `accessRights` nemá **oprávnění Spravovat,** nastavte na "naslouchání". V opačném případě funkce za běhu může selhat při pokusu o operace, které vyžadují práva ke správě. V Azure Functions verze 2.x a vyšší, tato vlastnost není k dispozici, protože nejnovější verze service bus SDK nepodporuje operace správy.|
|**isSessionsEnabled**|**IsSessionsPovoleno**|`true`pokud se připojujete k frontě nebo předplatnému [podporujícímu relaci.](../service-bus-messaging/message-sessions.md) `false`v opačném případě, což je výchozí hodnota.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C #](#tab/csharp)

Pro zprávu fronty nebo tématu jsou k dispozici následující typy parametrů:

* `string`- Pokud je ta zpráva textová.
* `byte[]`- Užitečné pro binární data.
* Vlastní typ – Pokud zpráva obsahuje JSON, Azure Functions se pokusí rekonstruovat data JSON.
* `BrokeredMessage`- Poskytuje deserializovanou zprávu s [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metoda.

Tyto typy parametrů jsou pro Funkce Azure verze 1.x; pro 2.x a [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) vyšší, `BrokeredMessage`použijte místo .

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Pro zprávu fronty nebo tématu jsou k dispozici následující typy parametrů:

* `string`- Pokud je ta zpráva textová.
* `byte[]`- Užitečné pro binární data.
* Vlastní typ – Pokud zpráva obsahuje JSON, Azure Functions se pokusí rekonstruovat data JSON.
* `BrokeredMessage`- Poskytuje deserializovanou zprávu s [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metoda.

Tyto parametry jsou pro Funkce Azure verze 1.x; pro 2.x a [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) vyšší, `BrokeredMessage`použijte místo .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Přístup ke zprávě fronty `context.bindings.<name from function.json>`nebo tématu pomocí aplikace . Zpráva service bus je předána do funkce jako řetězec nebo objekt JSON.

# <a name="python"></a>[Python](#tab/python)

Zpráva fronty je k dispozici funkci prostřednictvím `func.ServiceBusMessage`parametru zadaného jako . Zpráva service bus je předána do funkce jako řetězec nebo objekt JSON.

# <a name="java"></a>[Java](#tab/java)

Zpráva příchozí sběrnice service bus `ServiceBusQueueMessage` `ServiceBusTopicMessage` je k dispozici prostřednictvím parametru nebo.

[Podrobnosti najdete v příkladu](#example).

---

## <a name="poison-messages"></a>Otrávené zprávy

Zpracování poškozená zpráva nelze řídit nebo nakonfigurovat v Azure Functions. Service Bus zpracovává jedovaté zprávy sám.

## <a name="peeklock-behavior"></a>Chování PeekLock

Za běhu Funkce obdrží zprávu v [režimu PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Volá `Complete` zprávu, pokud funkce úspěšně dokončí nebo `Abandon` volání, pokud funkce selže. Pokud funkce běží déle `PeekLock` než časový čas, zámek se automaticky obnoví tak dlouho, dokud je spuštěna funkce. 

Je `maxAutoRenewDuration` konfigurovatelný v *host.json*, který mapuje [na OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Maximální povolené pro toto nastavení je 5 minut podle dokumentace service bus, zatímco můžete zvýšit časový limit Funkce z výchozí ho 5 minut na 10 minut. Pro funkce service bus byste to pak nechtěli dělat, protože byste překročili limit obnovení sběrnice.

## <a name="message-metadata"></a>Metadata zprávy

Aktivační událost služby Service Bus poskytuje několik [vlastností metadat](./functions-bindings-expressions-patterns.md#trigger-metadata). Tyto vlastnosti lze použít jako součást výrazy vazby v jiných vazeb nebo jako parametry v kódu. Tyto vlastnosti jsou členy [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) třídy.

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Počet dodávek.|
|`DeadLetterSource`|`string`|Zdroj mrtvých dopisů.|
|`ExpiresAtUtc`|`DateTime`|Doba vypršení platnosti v utc.|
|`EnqueuedTimeUtc`|`DateTime`|Doba zařazená do fronty v čase UTC.|
|`MessageId`|`string`|Uživatelem definovaná hodnota, kterou service bus může použít k identifikaci duplicitních zpráv, pokud je povolena.|
|`ContentType`|`string`|Identifikátor typu obsahu, který odesílatel a příjemce používá pro logiku specifickou pro aplikaci.|
|`ReplyTo`|`string`|Odpověď na adresu fronty.|
|`SequenceNumber`|`Int64`|Jedinečné číslo přiřazené ke zprávě službou Service Bus.|
|`To`|`string`|Poslat na adresu.|
|`Label`|`string`|Popisek specifický pro aplikaci.|
|`CorrelationId`|`string`|ID korelace.|

Viz [příklady kódu,](#example) které používají tyto vlastnosti dříve v tomto článku.

## <a name="next-steps"></a>Další kroky

- [Odesílání zpráv Azure Service Bus z Azure Functions (výstupní vazba)](./functions-bindings-service-bus-output.md)
