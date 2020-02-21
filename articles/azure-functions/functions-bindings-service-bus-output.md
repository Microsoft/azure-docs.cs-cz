---
title: Azure Service Bus vazby pro službu Azure Functions
description: Naučte se odesílat Azure Service Bus zprávy z Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 7e00d03a8b3ec7ef56935ff7714fd932bc343cd3
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493043"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Service Bus výstupní vazba pro Azure Functions

Použití Azure Service Bus výstupní vazby pro odesílání zpráv fronty nebo tématu.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](functions-bindings-service-bus-output.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

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

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [ C# knihovně tříd](functions-dotnet-class-library.md)použijte [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Konstruktor atributu přebírá název fronty nebo tématu a odběru. Můžete také zadat připojení přístupová práva. Jak zvolit nastavení přístupových práv je vysvětleno v části [výstup – konfigurace](#configuration) . Tady je příklad ukazující použije návratovou hodnotu funkce:

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

Úplný příklad naleznete v tématu [Output-example](#example).

Atribut `ServiceBusAccount` můžete použít k určení účtu Service Bus, který se má použít na úrovni třídy, metody nebo parametru.  Další informace najdete v tématu [Trigger – atributy](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

Poznámky `ServiceBusQueueOutput` a `ServiceBusTopicOutput` jsou k dispozici pro zápis zprávy jako výstup funkce. Parametr upravený pomocí těchto poznámek musí být deklarován jako `OutputBinding<T>`, kde `T` je typ odpovídající typu zprávy.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `ServiceBus`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavena na "služby"Service Bus. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastavena na "out". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje zprávu fronty nebo tématu v kódu funkce. Nastavte na "$return" tak, aby odkazovaly návratovou hodnotu funkce. |
|**Proměnné QueueName**|**Proměnné QueueName**|Název fronty.  Nastaví jenom v případě, že odesílá zprávy do fronty, ne pro téma.
|**téma**|**Téma**|Název tématu. Nastaví jenom v případě, že odesílání zpráv tématu, ne pro frontu.|
|**vázán**|**Vázán**|Název nastavení aplikace, které obsahuje připojovací řetězec služby Service Bus má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat jenom zbývající část názvu. Například pokud nastavíte `connection` na "MyServiceBus", modul runtime Functions vyhledá nastavení aplikace s názvem "AzureWebJobsMyServiceBus". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec Service Bus v nastavení aplikace s názvem "AzureWebJobsServiceBus".<br><br>Pokud chcete získat připojovací řetězec, postupujte podle kroků uvedených v části [získání přihlašovacích údajů pro správu](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Připojovací řetězec musí být pro obor názvů služby Service Bus, do konkrétní fronty nebo tématu není omezený.|
|**accessRights**|**Přístup**|Přístupová práva připojovacího řetězce. Dostupné hodnoty jsou `manage` a `listen`. Výchozí hodnota je `manage`, což znamená, že `connection` má oprávnění **Spravovat** . Pokud použijete připojovací řetězec, který nemá oprávnění **Spravovat** , nastavte `accessRights` na "naslouchání". V opačném případě funkce modulu runtime může selhat pokouší o provedení operace, které vyžadují spravovat práva. V Azure Functions verze 2. x nebo vyšší není tato vlastnost k dispozici, protože nejnovější verze Service Bus SDK nepodporuje operace správy.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

V Azure Functions 1. x modul runtime vytvoří frontu, pokud neexistuje a že jste nastavili `accessRights` na `manage`. V funkcích Functions verze 2. x a vyšší již musí fronta nebo téma existovat; Pokud zadáte frontu nebo téma, které neexistují, funkce se nezdaří. 

# <a name="c"></a>[C#](#tab/csharp)

Pro výstupní vazbu použijte následující typy parametrů:

* `out T paramName` - `T` může být jakýkoli typ serializovatelný pro JSON. Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce vytvoří zprávu s objekt s hodnotou null.
* `out string` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `out byte[]` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `out BrokeredMessage` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro funkce 1. x).
* `out Message` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro Functions 2. x a vyšší).
* `ICollector<T>` nebo `IAsyncCollector<T>` – pro vytvoření více zpráv. Zpráva se vytvoří při volání metody `Add`.

Při práci s C# funkcemi:

* Asynchronní funkce místo parametru `out` vyžadují návratovou hodnotu nebo `IAsyncCollector`.

* Chcete-li získat přístup k ID relace, vytvořte připojení k typu [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) a použijte vlastnost `sessionId`.

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

Pro výstupní vazbu použijte následující typy parametrů:

* `out T paramName` - `T` může být jakýkoli typ serializovatelný pro JSON. Pokud hodnota parametru má hodnotu null při ukončení funkce, funkce vytvoří zprávu s objekt s hodnotou null.
* `out string` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `out byte[]` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `out BrokeredMessage` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro funkce 1. x).
* `out Message` – Pokud má parametr hodnotu null, když se funkce ukončí, funkce nevytvoří zprávu (pro Functions 2. x a vyšší).
* `ICollector<T>` nebo `IAsyncCollector<T>` – pro vytvoření více zpráv. Zpráva se vytvoří při volání metody `Add`.

Při práci s C# funkcemi:

* Asynchronní funkce místo parametru `out` vyžadují návratovou hodnotu nebo `IAsyncCollector`.

* Chcete-li získat přístup k ID relace, vytvořte připojení k typu [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) a použijte vlastnost `sessionId`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup ke frontě nebo tématu pomocí `context.bindings.<name from function.json>`. Pro `context.binding.<name>`můžete přiřadit řetězec, bajtové pole nebo objekt JavaScriptu (deserializovat do formátu JSON).

# <a name="python"></a>[Python](#tab/python)

Místo předdefinované výstupní vazby použijte [sadu Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) .

# <a name="java"></a>[Java](#tab/java)

Místo předdefinované výstupní vazby použijte [sadu Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) .

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Service Bus | [Kódy chyb Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Omezení Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení Host.JSON

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2. x a vyšší. Ukázkový soubor host. JSON obsahuje pouze nastavení této vazby. Další informace o nastavení globálních konfigurací naleznete v tématu [reference Host. JSON pro verzi Azure Functions](functions-host-json.md).

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
|maxConcurrentCalls|16|Maximální počet souběžných volání zpětného volání, které by mělo zahájit pumpu zpráv. Ve výchozím nastavení modul runtime služby Functions zpracovávat více zpráv souběžně. Pokud chcete modul runtime nasměrovat tak, aby zpracovával jenom jednu frontu nebo zprávu o tématu, nastavte `maxConcurrentCalls` na 1. |

## <a name="next-steps"></a>Další kroky

- [Spustit funkci při vytvoření Service Bus fronty nebo tématu (trigger)](./functions-bindings-service-bus-trigger.md)
