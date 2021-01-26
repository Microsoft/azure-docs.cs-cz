---
title: Azure Service Bus výstupní vazby pro Azure Functions
description: Naučte se odesílat Azure Service Bus zprávy z Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 12e57361b9e275fc441df27a3a1381989d48751c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788566"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Service Bus výstupní vazba pro Azure Functions

Pro posílání zpráv fronty nebo témat použít výstupní vazbu Azure Service Bus.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](functions-bindings-service-bus.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která odesílá zprávu Service Bus fronty:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje výstupní vazbu Service Bus v *function.js* souboru a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce používá aktivační událost časovače k odeslání zprávy fronty každých 15 sekund.

Tady jsou data vazby v *function.js* souboru:

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

Zde je kód skriptu jazyka C#, který vytváří jednu zprávu:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Zde je kód skriptu jazyka C#, který vytváří více zpráv:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje funkci jazyka Java, která pošle zprávu do fronty Service Bus, `myqueue` když se aktivuje požadavkem http.

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

 V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@QueueOutput` anotaci u parametrů funkce, jejichž hodnota by byla zapsána do fronty Service Bus.  Typ parametru by měl být `OutputBinding<T>` , kde T je jakýkoliv nativní typ Java typu Pojo.

Funkce jazyka Java mohou také zapisovat do Service Busho tématu. Následující příklad používá `@ServiceBusTopicOutput` anotaci k popisu konfigurace výstupní vazby. 

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje výstupní vazbu Service Bus v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce používá aktivační událost časovače k odeslání zprávy fronty každých 15 sekund.

Tady jsou data vazby v *function.js* souboru:

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Následující příklad ukazuje výstupní vazbu Service Bus v *function.js* souboru a [funkci prostředí PowerShell](functions-reference-powershell.md) , která používá vazbu. 

Tady jsou data vazby v *function.js* souboru:

```json
{
  "bindings": [
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "outputSbMsg",
      "queueName": "outqueue",
      "topicName": "outtopic"
    }
  ]
}
```

Tady je PowerShell, který vytvoří zprávu jako výstup funkce.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name outputSbMsg -Value @{ 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje, jak zapisovat do fronty Service Bus v Pythonu.

Definice vazby Service Bus je definována v *function.jsna* místě, kde je *typ* nastaven na `serviceBus` .

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

V *_\_ init_ \_ . py* můžete do fronty napsat zprávu předáním hodnoty `set` metodě.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte rozhraní [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Konstruktor atributu přebírá název fronty nebo tématu a předplatného. Můžete také zadat přístupová práva pro připojení. Jak zvolit nastavení přístupových práv je vysvětleno v části [výstup – konfigurace](#configuration) . Tady je příklad, který ukazuje atribut aplikovaný na návratovou hodnotu funkce:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Vlastnost můžete nastavit `Connection` tak, aby určovala název nastavení aplikace, které obsahuje Service Bus připojovacího řetězce, který se má použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v tématu [Output-example](#example).

Atribut můžete použít `ServiceBusAccount` k určení Service Bus účtu pro použití na úrovni třídy, metody nebo parametru.  Další informace najdete v tématu [Trigger – atributy](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueOutput`Poznámky a `ServiceBusTopicOutput` jsou k dispozici pro zápis zprávy jako výstup funkce. Parametr upravený pomocí těchto poznámek musí být deklarován jako, `OutputBinding<T>` kde `T` je typ odpovídající typu zprávy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell nepodporuje atributy.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `ServiceBus` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**textový** | Není k dispozici | Musí být nastavené na "serviceBus". Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal.|
|**směr** | Není k dispozici | Musí být nastavené na "out". Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal. |
|**Jméno** | Není k dispozici | Název proměnné, která představuje zprávu fronty nebo tématu v kódu funkce. Nastavte na "$return", chcete-li odkazovat na návratovou hodnotu funkce. |
|**Proměnné QueueName**|**Proměnné QueueName**|Název fronty  Nastavte pouze v případě, že jsou odesílány zprávy fronty, nikoli téma.
|**téma**|**Téma**|Název tématu. Nastaveno pouze při posílání zpráv témat, nikoli pro frontu.|
|**vázán**|**Připojení**|Název nastavení aplikace, které obsahuje připojovací řetězec Service Bus, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyServiceBus", modul runtime Functions vyhledá nastavení aplikace s názvem "AzureWebJobsMyServiceBus". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec Service Bus v nastavení aplikace s názvem "AzureWebJobsServiceBus".<br><br>Pokud chcete získat připojovací řetězec, postupujte podle kroků uvedených v části [získání přihlašovacích údajů pro správu](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Připojovací řetězec musí být pro obor názvů Service Bus, který není omezen na konkrétní frontu nebo téma.|
|**accessRights** (jenom V1)|**Přístup**|Přístupová práva k připojovacímu řetězci Dostupné hodnoty jsou `manage` a `listen` . Výchozí hodnota je `manage` , což znamená, že `connection` má oprávnění **Spravovat** . Pokud použijete připojovací řetězec, který nemá oprávnění **Spravovat** , nastavte `accessRights` na "naslouchání". V opačném případě může modul runtime Functions selhat při pokusu o provedení operací, které vyžadují oprávnění ke správě. V Azure Functions verze 2. x nebo vyšší není tato vlastnost k dispozici, protože nejnovější verze Service Bus SDK nepodporuje operace správy.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

V Azure Functions 1. x modul runtime vytvoří frontu, pokud neexistuje a že jste nastavili `accessRights` na `manage` . V funkcích Functions verze 2. x a vyšší již musí fronta nebo téma existovat; Pokud zadáte frontu nebo téma, které neexistují, funkce se nezdaří. 

# <a name="c"></a>[C#](#tab/csharp)

Pro výstupní vazbu použijte následující typy parametrů:

* `out T paramName` - `T` může být jakýkoli typ serializovatelný jako JSON. Pokud má parametr hodnotu null, když funkce skončí, funkce vytvoří zprávu s objektem s hodnotou null.
* `out string` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `out byte[]` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `out BrokeredMessage` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro funkce 1. x).
* `out Message` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro Functions 2. x a vyšší).
* `ICollector<T>` nebo `IAsyncCollector<T>` (pro asynchronní metody) – pro vytváření více zpráv. Zpráva se vytvoří při volání `Add` metody.

Při práci s funkcemi jazyka C#:

* Asynchronní funkce vyžadují návratovou hodnotu nebo `IAsyncCollector` místo `out` parametru.

* Chcete-li získat přístup k ID relace, připojte se k [`Message`](/dotnet/api/microsoft.azure.servicebus.message) typu a použijte `sessionId` vlastnost.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Pro výstupní vazbu použijte následující typy parametrů:

* `out T paramName` - `T` může být jakýkoli typ serializovatelný jako JSON. Pokud má parametr hodnotu null, když funkce skončí, funkce vytvoří zprávu s objektem s hodnotou null.
* `out string` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `out byte[]` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `out BrokeredMessage` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro funkce 1. x).
* `out Message` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro Functions 2. x a vyšší).
* `ICollector<T>` nebo `IAsyncCollector<T>` – pro vytvoření více zpráv. Zpráva se vytvoří při volání `Add` metody.

Při práci s funkcemi jazyka C#:

* Asynchronní funkce vyžadují návratovou hodnotu nebo `IAsyncCollector` místo `out` parametru.

* Chcete-li získat přístup k ID relace, připojte se k [`Message`](/dotnet/api/microsoft.azure.servicebus.message) typu a použijte `sessionId` vlastnost.

# <a name="java"></a>[Java](#tab/java)

Místo předdefinované výstupní vazby použijte [sadu Azure Service Bus SDK](../service-bus-messaging/index.yml) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup ke frontě nebo tématu pomocí `context.bindings.<name from function.json>` . Můžete přiřadit řetězec, bajtové pole nebo objekt JavaScriptu (deserializovat do formátu JSON) do `context.binding.<name>` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Výstup do Service Bus je k dispozici prostřednictvím `Push-OutputBinding` rutiny, kde předáte argumenty, které odpovídají názvu určenému parametrem názvu vazby v *function.jsv* souboru.

# <a name="python"></a>[Python](#tab/python)

Místo předdefinované výstupní vazby použijte [sadu Azure Service Bus SDK](../service-bus-messaging/index.yml) .

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Service Bus | [Kódy chyb Service Bus](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| Service Bus | [Omezení Service Bus](../service-bus-messaging/service-bus-quotas.md) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsnastavení

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2. x a vyšší. Příklad host.jsv souboru níže obsahuje pouze nastavení této vazby. Další informace o globálních nastaveních konfigurace najdete v tématu [host.jsv referenčních informacích pro Azure Functions verzi](functions-host-json.md).

> [!NOTE]
> Odkaz na host.jspro ve funkcích 1. x naleznete v tématu [host.json reference for Azure Functions 1. x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
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

Pokud jste `isSessionsEnabled` nastavili na `true` , `sessionHandlerOptions` bude dodržena.  Pokud jste `isSessionsEnabled` nastavili na `false` , `messageHandlerOptions` bude dodržena.

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|prefetchCount|0|Získá nebo nastaví počet zpráv, které může příjemce zprávy současně požadovat.|
|maxAutoRenewDuration|00:05:00|Maximální doba, během které bude zámek zprávy obnoven automaticky.|
|Zobrazovat|true|Určuje, zda má být aktivační událost automaticky volána po zpracování, nebo pokud kód funkce bude ručně volána možnost Dokončit.<br><br>Nastavení na `false` je podporováno pouze v jazyce C#.<br><br>Je-li nastavena na hodnotu `true` , aktivační událost automaticky dokončí zprávu, pokud se provádění funkce dokončí úspěšně a zpráva se v opačném případě opustí.<br><br>Když nastavíte `false` , zodpovídáte za volání metod [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver?view=azure-dotnet&preserve-view=true) k dokončení, zrušení nebo nedoručené zprávě. Pokud je vyvolána výjimka (a není volána žádná z `MessageReceiver` metod), zůstane zámek. Po vypršení platnosti zámku se zpráva znovu zařadí do fronty s `DeliveryCount` přírůstkem a automaticky se obnoví zámek.<br><br>Ve funkcích, které nejsou v jazyce C #, výsledkem výjimek ve funkci jsou volání za běhu `abandonAsync` na pozadí. Pokud nedojde k žádné výjimce, `completeAsync` je volána na pozadí. |
|maxConcurrentCalls|16|Maximální počet souběžných volání zpětného volání, které by mělo pumpa zpráv iniciovat na úrovni instance. Ve výchozím nastavení aplikace runtime Functions zpracovává více zpráv souběžně.|
|maxConcurrentSessions|2000|Maximální počet relací, které mohou být zpracovány souběžně podle škálované instance.|

## <a name="next-steps"></a>Další kroky

- [Spustit funkci při vytvoření Service Bus fronty nebo tématu (trigger)](./functions-bindings-service-bus-trigger.md)
