---
title: Aktivační událost Azure Service Bus pro Azure Functions
description: Naučte se spouštět službu Azure Function při vytváření Azure Service Busch zpráv.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f2a514af99baa2d828df1aee35a0e6339d39e617
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788549"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Aktivační událost Azure Service Bus pro Azure Functions

Pokud chcete reagovat na zprávy z fronty nebo tématu Service Bus, použijte Trigger Service Bus. Počínaje verzí rozšíření 3.1.0 můžete aktivovat ve frontě nebo tématu s povolenou relací.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](functions-bindings-service-bus.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která čte [metadata zprávy](#message-metadata) a protokoluje zprávy fronty Service Bus:

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

Následující příklad ukazuje Service Bus aktivační vazby v *function.js* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce přečte [metadata zprávy](#message-metadata) a zaprotokoluje zprávu fronty Service Bus.

Tady jsou data vazby v *function.js* souboru:

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

# <a name="java"></a>[Java](#tab/java)

Následující funkce jazyka Java používá `@ServiceBusQueueTrigger` anotaci z [běhové knihovny Functions jazyka Java](/java/api/overview/azure/functions/runtime) k popisu konfigurace pro aktivační událost Service Bus Queue. Funkce přiřadí zprávu umístěnou ve frontě a přidá ji do protokolů.

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

Funkce Java se můžou aktivovat i při přidání zprávy do tématu Service Bus. Následující příklad používá `@ServiceBusTopicTrigger` poznámku k popisu konfigurace triggeru.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje Service Bus aktivační vazby v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce přečte [metadata zprávy](#message-metadata) a zaprotokoluje zprávu fronty Service Bus.

Tady jsou data vazby v *function.js* souboru:

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Následující příklad ukazuje Service Bus aktivační vazby v *function.js* souboru a [funkce PowerShellu](functions-reference-powershell.md) , která používá vazbu. 

Tady jsou data vazby v *function.js* souboru:

```json
{
  "bindings": [
    {
      "name": "mySbMsg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "topicName": "mytopic",
      "subscriptionName": "mysubscription",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Zde je funkce, která se spouští při odeslání zprávy Service Bus.

```powershell
param([string] $mySbMsg, $TriggerMetadata)

Write-Host "PowerShell ServiceBus queue trigger function processed message: $mySbMsg"
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje, jak číst zprávu Service Bus Queue prostřednictvím triggeru.

Vazba Service Bus je definována v *function.jsna* místě, kde je *typ* nastaven na `serviceBusTrigger` .

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

Kód v příkazu *_\_ init_ \_ . py* deklaruje parametr jako `func.ServiceBusMessage` , který umožňuje čtení zprávy fronty ve funkci.

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
        'metadata' : msg.metadata
    })

    logging.info(result)
```

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd C#](functions-dotnet-class-library.md)pomocí následujících atributů nakonfigurujte aktivační událost Service Bus:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Konstruktor atributu přebírá název fronty nebo tématu a předplatného. V Azure Functions verze 1. x můžete také zadat přístupová práva připojení. Pokud nezadáte přístupová práva, výchozí hodnota je `Manage` . Další informace najdete v části věnované [konfiguraci triggeru](#configuration) .

  Tady je příklad, který ukazuje atribut použitý s parametrem řetězce:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Vzhledem k tomu `Connection` , že vlastnost není definována, funkce vyhledá nastavení aplikace s názvem `AzureWebJobsServiceBus` , což je výchozí název připojovacího řetězce Service Bus. Vlastnost můžete také nastavit `Connection` tak, aby určovala název nastavení aplikace, které obsahuje Service Bus připojovacího řetězce, který se má použít, jak je znázorněno v následujícím příkladu:

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

* `ServiceBusTrigger` `Connection` Vlastnost atributu
* `ServiceBusAccount`Atribut aplikovaný na stejný parametr jako `ServiceBusTrigger` atribut.
* `ServiceBusAccount`Atribut aplikovaný na funkci.
* `ServiceBusAccount`Atribut aplikovaný na třídu.
* Nastavení aplikace "AzureWebJobsServiceBus".

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueTrigger`Poznámka umožňuje vytvořit funkci, která se spustí při vytvoření zprávy fronty Service Bus. K dispozici jsou možnosti konfigurace, včetně názvu fronty a názvu připojovacího řetězce.

`ServiceBusTopicTrigger`Poznámka vám umožní určit téma a předplatné k určení toho, jaká data aktivují funkci.

Další podrobnosti najdete v [příkladu](#example) triggeru.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell nepodporuje atributy.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `ServiceBusTrigger` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**textový** | Není k dispozici | Musí být nastavené na "serviceBusTrigger". Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal.|
|**směr** | Není k dispozici | Musí být nastavené na "in". Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal. |
|**Jméno** | Není k dispozici | Název proměnné, která představuje zprávu fronty nebo tématu v kódu funkce. |
|**Proměnné QueueName**|**Proměnné QueueName**|Název fronty, která se má monitorovat  Nastavte pouze v případě, že monitorování fronty, nikoli tématu.
|**téma**|**Téma**|Název tématu, které se má monitorovat Nastavte pouze v případě, že monitorování tématu, nikoli pro frontu.|
|**subscriptionName**|**SubscriptionName**|Název předplatného, které se má monitorovat Nastavte pouze v případě, že monitorování tématu, nikoli pro frontu.|
|**vázán**|**Připojení**|Název nastavení aplikace, které obsahuje připojovací řetězec Service Bus, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyServiceBus", modul runtime Functions vyhledá nastavení aplikace s názvem "AzureWebJobsMyServiceBus". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec Service Bus v nastavení aplikace s názvem "AzureWebJobsServiceBus".<br><br>Pokud chcete získat připojovací řetězec, postupujte podle kroků uvedených v části [získání přihlašovacích údajů pro správu](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Připojovací řetězec musí být pro obor názvů Service Bus, který není omezen na konkrétní frontu nebo téma. |
|**accessRights**|**Přístup**|Přístupová práva k připojovacímu řetězci Dostupné hodnoty jsou `manage` a `listen` . Výchozí hodnota je `manage` , což znamená, že `connection` má oprávnění **Spravovat** . Pokud použijete připojovací řetězec, který nemá oprávnění **Spravovat** , nastavte `accessRights` na "naslouchání". V opačném případě může modul runtime Functions selhat při pokusu o provedení operací, které vyžadují oprávnění ke správě. V Azure Functions verze 2. x nebo vyšší není tato vlastnost k dispozici, protože nejnovější verze Service Bus SDK nepodporuje operace správy.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` Pokud se připojujete ke frontě nebo předplatnému [využívajícím relaci](../service-bus-messaging/message-sessions.md) . `false` jinak, což je výchozí hodnota.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

Pro zprávu o frontě nebo tématu jsou k dispozici následující typy parametrů:

* `string` – Pokud se jedná o text zprávy
* `byte[]` – Užitečné pro binární data.
* Vlastní typ – Pokud zpráva obsahuje JSON, Azure Functions se pokusí deserializovat data JSON.
* `BrokeredMessage` – Poskytuje deserializovanou zprávu s metodou [BrokeredMessage. GetBody \<T> ()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1&preserve-view=true) .
* [`MessageReceiver`](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver?view=azure-dotnet&preserve-view=true) – Používá se k přijímání a potvrzení zpráv z kontejneru zpráv (povinné, když [`autoComplete`](functions-bindings-service-bus-output.md#hostjson-settings) je nastavená na `false` )

Tyto typy parametrů jsou pro Azure Functions verze 1. x; pro 2. x a novější použijte [`Message`](/dotnet/api/microsoft.azure.servicebus.message) místo `BrokeredMessage` .

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Pro zprávu o frontě nebo tématu jsou k dispozici následující typy parametrů:

* `string` – Pokud se jedná o text zprávy
* `byte[]` – Užitečné pro binární data.
* Vlastní typ – Pokud zpráva obsahuje JSON, Azure Functions se pokusí deserializovat data JSON.
* `BrokeredMessage` – Poskytuje deserializovanou zprávu s metodou [BrokeredMessage. GetBody \<T> ()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1&preserve-view=true) .

Tyto parametry jsou pro Azure Functions verze 1. x; pro 2. x a novější použijte [`Message`](/dotnet/api/microsoft.azure.servicebus.message) místo `BrokeredMessage` .

# <a name="java"></a>[Java](#tab/java)

Příchozí Service Bus zpráva je k dispozici prostřednictvím `ServiceBusQueueMessage` `ServiceBusTopicMessage` parametru nebo.

[Podrobnosti najdete v příkladu](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup ke zprávě fronty nebo tématu pomocí `context.bindings.<name from function.json>` . Zpráva Service Bus se předává do funkce jako objekt String nebo JSON.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Instance Service Bus je k dispozici prostřednictvím parametru nakonfigurovaného ve vlastnosti název souboru *function.js* .

# <a name="python"></a>[Python](#tab/python)

Zpráva fronty je k dispozici funkci prostřednictvím parametru zadaného jako `func.ServiceBusMessage` . Zpráva Service Bus se předává do funkce jako objekt String nebo JSON.

---

## <a name="poison-messages"></a>Poškozené zprávy

V Azure Functions nelze řídit ani konfigurovat zpracování nezpracovatelných zpráv. Service Bus zpracovává samotné poškozené zprávy.

## <a name="peeklock-behavior"></a>Chování PeekLock

Modul runtime Functions obdrží zprávu v [režimu PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Volá `Complete` zprávu, pokud se funkce dokončí úspěšně, nebo volá, `Abandon` Pokud se funkce nezdaří. Pokud je funkce spuštěná déle, než je `PeekLock` časový limit, zámek se automaticky obnoví, dokud je funkce spuštěná.

`maxAutoRenewDuration`Je možné konfigurovat v *host.js*, který se mapuje na [OnMessageOptions. MaxAutoRenewDuration](/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet&preserve-view=true). Maximální povolený počet pro toto nastavení je 5 minut v závislosti na Service Bus dokumentaci, zatímco časový limit funkcí můžete zvýšit z výchozí hodnoty 5 minut na 10 minut. U Service Busch funkcí byste to neudělali, protože byste překročili Service Bus limit obnovení.

## <a name="message-metadata"></a>Metadata zprávy

Aktivační událost Service Bus poskytuje několik [vlastností metadat](./functions-bindings-expressions-patterns.md#trigger-metadata). Tyto vlastnosti lze použít jako součást výrazů vazby v jiných vazbách nebo jako parametry v kódu. Tyto vlastnosti jsou členy třídy [zpráv](/dotnet/api/microsoft.azure.servicebus.message?view=azure-dotnet&preserve-view=true) .

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|`ContentType`|`string`|Identifikátor typu obsahu využitý odesílatelem a příjemcem pro logiku specifickou pro aplikaci.|
|`CorrelationId`|`string`|ID korelace.|
|`DeadLetterSource`|`string`|Zdroj nedoručených zpráv.|
|`DeliveryCount`|`Int32`|Počet doručení.|
|`EnqueuedTimeUtc`|`DateTime`|Čas zařazení do fronty ve standardu UTC.|
|`ExpiresAtUtc`|`DateTime`|Čas vypršení platnosti ve standardu UTC.|
|`Label`|`string`|Popisek specifický pro aplikaci.|
|`MessageId`|`string`|Uživatelem definovaná hodnota, kterou Service Bus může použít k identifikaci duplicitních zpráv, pokud je povoleno.|
|`MessageReceiver`|`MessageReceiver`|Service Bus příjemce zprávy. Dá se použít k opuštění, dokončení nebo nedoručené zprávě.|
|`MessageSession`|`MessageSession`|Přijímač zpráv určený speciálně pro fronty a témata s povolenými relacemi.|
|`ReplyTo`|`string`|Odpověď na adresu fronty|
|`SequenceNumber`|`long`|Jedinečné číslo přiřazené zprávě Service Bus.|
|`To`|`string`|Adresa pro odeslání.|
|`UserProperties`|`IDictionary<string, object>`|Vlastnosti nastavené odesílatelem|

Podívejte se na [Příklady kódu](#example) , které používají tyto vlastnosti dříve v tomto článku.

## <a name="next-steps"></a>Další kroky

- [Posílání zpráv Azure Service Bus z Azure Functions (výstupní vazba)](./functions-bindings-service-bus-output.md)
