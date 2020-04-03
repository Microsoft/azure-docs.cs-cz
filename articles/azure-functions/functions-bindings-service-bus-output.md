---
title: Vazby Azure Service Bus pro funkce Azure
description: Naučte se odesílat zprávy Azure Service Bus z Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 02d9ce87d45c5f1c9a123aae18f7d710b268f03e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582253"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Výstupní vazba Azure Service Bus pro funkce Azure

K odesílání zpráv fronty nebo tématu použijte výstupní vazbu služby Azure Service Bus.

Informace o nastavení a konfiguraci naleznete v [přehledu](functions-bindings-service-bus-output.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci C#,](functions-dotnet-class-library.md) která odesílá zprávu fronty služby Service Bus:

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

Následující příklad ukazuje výstupní vazbu service bus v souboru *function.json* a [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která tuto vazbu používá. Funkce používá aktivační událost časovače k odeslání zprávy fronty každých 15 sekund.

Zde jsou data vazby v souboru *function.json:*

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

Zde je c# skript kód, který vytvoří jednu zprávu:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Zde je c# skript kód, který vytváří více zpráv:

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

Následující příklad ukazuje výstupní vazbu service bus v souboru *function.json* a [funkci JavaScript,](functions-reference-node.md) která vazbu používá. Funkce používá aktivační událost časovače k odeslání zprávy fronty každých 15 sekund.

Zde jsou data vazby v souboru *function.json:*

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

Zde je JavaScript skript kód, který vytváří jednu zprávu:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Zde je JavaScript skript kód, který vytváří více zpráv:

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

Následující příklad ukazuje, jak zapisovat do fronty service bus v Pythonu.

Definice vazby služby Service Bus je *type* definována v `serviceBus` *souboru function.json,* kde je typ nastaven na .

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

V `set` init *\_.py můžete zapsat zprávu do fronty předáním hodnoty metodě. _ \__*

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje funkci Java, která odešle `myqueue` zprávu do fronty service bus při aktivaci požadavku HTTP.

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

 V [knihovně runtime funkcí](/java/api/overview/azure/functions/runtime) `@QueueOutput` jazyka Java použijte poznámku o parametrech funkce, jejichž hodnota by byla zapsána do fronty sběrnice.  Typ parametru `OutputBinding<T>`by měl být , kde T je libovolný nativní typ Java POJO.

Funkce jazyka Java mohou také zapisovat do tématu service bus. Následující příklad používá `@ServiceBusTopicOutput` poznámku k popisu konfigurace pro výstupní vazbu. 

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

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte [atribut ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Konstruktor atributu přebírá název fronty nebo téma a odběr. Můžete také určit přístupová práva připojení. Jak zvolit nastavení přístupových práv je vysvětleno v části [Výstup - konfigurace.](#configuration) Zde je příklad, který ukazuje atribut použitý na vrácenou hodnotu funkce:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Vlastnost můžete `Connection` nastavit tak, aby určila název nastavení aplikace, které obsahuje připojovací řetězec Service Bus, který chcete použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v tématu [Output - example](#example).

`ServiceBusAccount` Atribut můžete použít k určení účtu Service Bus, který se má použít na úrovni třídy, metody nebo parametru.  Další informace naleznete v tématu [Trigger - atributy](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

A `ServiceBusQueueOutput` `ServiceBusTopicOutput` poznámky jsou k dispozici pro zápis zprávy jako výstup funkce. Parametr dekorovaný těmito anotacemi `OutputBinding<T>` musí `T` být deklarován jako kde je typ odpovídající typu zprávy.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `ServiceBus` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být nastavena na "serviceBus". Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastavena na "out". Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje zprávu fronty nebo tématu v kódu funkce. Nastavte na "$return" pro odkaz na vrácenou hodnotu funkce. |
|**název_fronty**|**Název fronty**|Název fronty.  Nastavte pouze v případě, že odesíláte zprávy fronty, nikoli pro téma.
|**topicName**|**Název_tématu**|Název tématu. Nastavte pouze v případě, že odesíláte tematické zprávy, nikoli pro frontu.|
|**Připojení**|**Připojení**|Název nastavení aplikace, která obsahuje připojovací řetězec Service Bus použít pro tuto vazbu. Pokud název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytek názvu. Například pokud nastavíte `connection` "MyServiceBus", funkce runtime hledá nastavení aplikace s názvem "AzureWebJobsMyServiceBus". Pokud necháte `connection` prázdné, spustí se funkce runtime použije výchozí řetězec připojení Service Bus v nastavení aplikace s názvem "AzureWebJobsServiceBus".<br><br>Chcete-li získat připojovací řetězec, postupujte podle kroků uvedených v části [Získání pověření pro správu](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Připojovací řetězec musí být pro obor názvů service bus, není omezen na konkrétní frontu nebo téma.|
|**accessRights**|**Přístup**|Přístupová práva pro připojovací řetězec. Dostupné hodnoty `manage` `listen`jsou a . Výchozí hodnota `manage`je , `connection` což znamená, že má **oprávnění Spravovat.** Pokud používáte připojovací řetězec, který `accessRights` nemá **oprávnění Spravovat,** nastavte na "naslouchání". V opačném případě funkce za běhu může selhat při pokusu o operace, které vyžadují práva ke správě. V Azure Functions verze 2.x a vyšší, tato vlastnost není k dispozici, protože nejnovější verze service bus SDK nepodporuje operace správy.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

V Azure Functions 1.x, runtime vytvoří frontu, pokud neexistuje `accessRights` `manage`a jste nastavili . Ve funkcích verze 2.x a vyšší musí fronta nebo téma již existovat. Pokud zadáte frontu nebo téma, které neexistuje, funkce se nezdaří. 

# <a name="c"></a>[C#](#tab/csharp)

Pro výstupní vazbu použijte následující typy parametrů:

* `out T paramName` - `T`může být libovolný typ JSON-serializovatelný. Pokud je hodnota parametru nulová při ukončení funkce, funkce vytvoří zprávu s nulovým objektem.
* `out string`- Pokud je hodnota parametru null při ukončení funkce funkce nevytvoří zprávu.
* `out byte[]`- Pokud je hodnota parametru null při ukončení funkce funkce nevytvoří zprávu.
* `out BrokeredMessage`- Pokud je hodnota parametru nulová při ukončení funkce, funkce nevytvoří zprávu (pro funkce 1.x)
* `out Message`- Pokud je hodnota parametru nulová, když funkce ukončí, funkce nevytvoří zprávu (pro funkce 2.x a vyšší)
* `ICollector<T>`nebo `IAsyncCollector<T>` - Pro vytváření více zpráv. Zpráva je vytvořena při `Add` volání metody.

Při práci s funkcemi jazyka C#:

* Asynchronní funkce potřebují `IAsyncCollector` vrácenou `out` hodnotu nebo místo parametru.

* Chcete-li získat přístup k [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) ID relace, spojte se s typem a použijte `sessionId` vlastnost.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Pro výstupní vazbu použijte následující typy parametrů:

* `out T paramName` - `T`může být libovolný typ JSON-serializovatelný. Pokud je hodnota parametru nulová při ukončení funkce, funkce vytvoří zprávu s nulovým objektem.
* `out string`- Pokud je hodnota parametru null při ukončení funkce funkce nevytvoří zprávu.
* `out byte[]`- Pokud je hodnota parametru null při ukončení funkce funkce nevytvoří zprávu.
* `out BrokeredMessage`- Pokud je hodnota parametru nulová při ukončení funkce, funkce nevytvoří zprávu (pro funkce 1.x)
* `out Message`- Pokud je hodnota parametru nulová, když funkce ukončí, funkce nevytvoří zprávu (pro funkce 2.x a vyšší)
* `ICollector<T>`nebo `IAsyncCollector<T>` - Pro vytváření více zpráv. Zpráva je vytvořena při `Add` volání metody.

Při práci s funkcemi jazyka C#:

* Asynchronní funkce potřebují `IAsyncCollector` vrácenou `out` hodnotu nebo místo parametru.

* Chcete-li získat přístup k [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) ID relace, spojte se s typem a použijte `sessionId` vlastnost.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup k frontě `context.bindings.<name from function.json>`nebo tématu pomocí aplikace . Můžete přiřadit řetězec, bajtové pole nebo javascriptový objekt (rekonstruovaný do `context.binding.<name>`JSON) .

# <a name="python"></a>[Python](#tab/python)

Použijte [sdk azure service bus](https://docs.microsoft.com/azure/service-bus-messaging) místo integrované výstupní vazby.

# <a name="java"></a>[Java](#tab/java)

Použijte [sdk azure service bus](https://docs.microsoft.com/azure/service-bus-messaging) místo integrované výstupní vazby.

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Service Bus | [Kódy chyb sběrnice service bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Limity sběrnice](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení host.json

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2.x a vyšších. Příklad souboru host.json níže obsahuje pouze nastavení pro tuto vazbu. Další informace o nastavení globální konfigurace naleznete v [tématu host.json reference for Azure Functions version](functions-host-json.md).

> [!NOTE]
> Odkaz na host.json ve funkcích 1.x najdete v [tématu reference host.json pro Funkce Azure 1.x](functions-host-json-v1.md).

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
|prefetchCount|0|Získá nebo nastaví počet zpráv, které může příjemce zprávy současně požadovat.|
|maxAutoRenewDuration|00:05:00|Maximální doba, po kterou bude zámek zprávy automaticky obnoven.|
|Automatické dokončování|true|Určuje, zda má aktivační událost okamžitě označit zprávu jako dokončenou (automatické dokončování) nebo čekat na úspěšné ukončení funkce, aby se volání dokončilo.|
|maxConcurrentCalls maxConcurrentCalls maxConcurrentCalls maxCon|16|Maximální počet souběžných volání zpětného volání, které by mělo čerpadlo zprávy zahájit. Ve výchozím nastavení pracuje za běhu functions více zpráv současně. Chcete-li nasměrovat za běhu na zpracování pouze jedné `maxConcurrentCalls` fronty nebo tematické zprávy současně, nastavte na 1. |

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce při vytvoření fronty služby Service Bus nebo zprávy tematické zprávy (Aktivační událost)](./functions-bindings-service-bus-trigger.md)
