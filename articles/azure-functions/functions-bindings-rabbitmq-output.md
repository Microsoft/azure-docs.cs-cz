---
title: RabbitMQ výstupní vazby pro Azure Functions
description: Naučte se odesílat RabbitMQ zprávy z Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 1664656f82492e664b7574339893cd688f0a061d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097309"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>RabbitMQ výstupní vazba pro Azure Functions přehled

> [!NOTE]
> Vazby RabbitMQ jsou plně podporované jenom na plánech **Premium a na vyhrazené** úrovni. Spotřeba se nepodporuje.

Pomocí výstupní vazby RabbitMQ můžete odesílat zprávy do fronty RabbitMQ.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](functions-bindings-rabbitmq-output.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která pošle zprávu RabbitMQ, když se aktivuje TimerTrigger každých 5 minut pomocí návratové hodnoty metody jako výstup:

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ(QueueName = "outputQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Následující příklad ukazuje, jak používat rozhraní IAsyncCollector k posílání zpráv.

```cs
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] string rabbitMQEvent,
[RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<string> outputEvents,
ILogger log)
{
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(rabbitMQEvent));
}
```

Následující příklad ukazuje, jak odeslat zprávy jako POCOs.

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }
    public static class RabbitMQOutput{
        [FunctionName("RabbitMQOutput")]
        public static async Task Run(
        [RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass rabbitMQEvent,
        [RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<TestClass> outputPocObj,
        ILogger log)
        {
            // send the message
            await outputPocObj.AddAsync(rabbitMQEvent);
        }
    }
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje výstupní vazbu RabbitMQ v souboru *function.js* a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce přečte zprávu z triggeru HTTP a zapíše ji do fronty RabbitMQ.

Tady jsou data vazby v *function.js* souboru:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Tady je kód skriptu jazyka C#:

```C#
using System;
using Microsoft.Extensions.Logging;

public static void Run(string input, out string outputMessage, ILogger log)
{
    log.LogInformation(input);
    outputMessage = input;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje výstupní vazbu RabbitMQ v souboru v *function.js* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce přečte zprávu z triggeru HTTP a zapíše ji do fronty RabbitMQ.

Tady jsou data vazby v *function.js* souboru:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Kód JavaScriptu:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje výstupní vazbu RabbitMQ v souboru v *function.js* a funkci Pythonu, která používá vazbu. Funkce přečte zprávu z triggeru HTTP a zapíše ji do fronty RabbitMQ.

Tady jsou data vazby v *function.js* souboru:

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
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

V *_\_ init_ \_ . py*:

```python
import azure.functions as func

def main(req: func.HttpRequest, outputMessage: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    outputMessage.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

Následující funkce jazyka Java používá `@RabbitMQOutput` anotaci z [typů Java RabbitMQ](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) k popisu konfigurace výstupní vazby fronty RabbitMQ. Funkce pošle zprávu do fronty RabbitMQ při spuštění TimerTrigger každých 5 minut.

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "hello") OutputBinding<String> output,
final ExecutionContext context) {
    output.setValue("Some string");
}
```

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte rozhraní [RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs).

Zde je `RabbitMQAttribute` atribut v signatuře metody:

```csharp
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("SourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("DestinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    ...
}
```

Úplný příklad najdete v tématu [příklad](#example)v jazyce C#.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

`RabbitMQOutput`Poznámka umožňuje vytvořit funkci, která se spustí při odeslání zprávy RabbitMQ. K dispozici jsou možnosti konfigurace, včetně názvu fronty a názvu připojovacího řetězce. Další podrobnosti o parametrech najdete v [poznámkách Java RabbitMQOutput](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQOutput.java).

Další podrobnosti najdete v [příkladu](#example) výstupní vazby.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `RabbitMQ` atribut.

|function.jsvlastnost | Vlastnost atributu |Description|
|---------|---------|----------------------|
|**textový** | Není k dispozici | Musí být nastavené na "RabbitMQ".|
|**směr** | Není k dispozici | Musí být nastavené na "out". |
|**Jméno** | Není k dispozici | Název proměnné, která představuje frontu v kódu funkce. |
|**Proměnné QueueName**|**Proměnné QueueName**| Název fronty, do které se budou posílat zprávy |
|**Název hostitele**|**Název hostitele**|(ignoruje se, pokud používáte ConnectStringSetting) <br>Název hostitele fronty (např.: 10.26.45.210)|
|**Jmen**|**Jmen**|(ignoruje se, pokud používáte ConnectionStringSetting) <br>Název nastavení aplikace, které obsahuje uživatelské jméno pro přístup do fronty. Například UserNameSetting: "< UserNameFromSettings >"|
|**heslo**|**Heslo**|(ignoruje se, pokud používáte ConnectionStringSetting) <br>Název nastavení aplikace, které obsahuje heslo pro přístup do fronty. Například UserNameSetting: "< UserNameFromSettings >"|
|**connectionStringSetting**|**ConnectionStringSetting**|Název nastavení aplikace, které obsahuje připojovací řetězec fronty zpráv RabbitMQ Upozorňujeme, že pokud zadáte připojovací řetězec přímo a nikoli prostřednictvím nastavení aplikace v local.settings.js, aktivační událost nebude fungovat. (Např.: v *function.jsna*: connectionStringSetting: "rabbitMQConnection" <br> V *local.settings.js*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**přístavní**|**Port**|(ignoruje se, pokud používáte ConnectionStringSetting) Získá nebo nastaví použitý port. Výchozí hodnota je 0, což odkazuje na výchozí nastavení portu RabbitMQ klienta: 5672.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

Pro výstupní vazbu použijte následující typy parametrů:

* `byte[]` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `string` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `POCO` – Pokud hodnota parametru není formátována jako objekt jazyka C#, bude přijata chyba. Úplný příklad najdete v tématu [příklad](#example)v jazyce C#.

Při práci s funkcemi jazyka C#:

* Asynchronní funkce vyžadují návratovou hodnotu nebo `IAsyncCollector` místo `out` parametru.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Pro výstupní vazbu použijte následující typy parametrů:

* `byte[]` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `string` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `POCO` – Pokud hodnota parametru není formátována jako objekt jazyka C#, bude přijata chyba. Úplný příklad najdete v tématu [příklad](#example)skriptu jazyka C#.

Při práci s funkcemi skriptu jazyka C#:

* Asynchronní funkce vyžadují návratovou hodnotu nebo `IAsyncCollector` místo `out` parametru.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Zpráva fronty je k dispozici prostřednictvím Context. Bindings.<NAME> kde <NAME> odpovídá názvu definovanému v function.js. Pokud je datová část JSON, hodnota je deserializována do objektu.

# <a name="python"></a>[Python](#tab/python)

Podívejte se na [příklad](#example)Pythonu.

# <a name="java"></a>[Java](#tab/java)

Pro výstupní vazbu použijte následující typy parametrů:

* `byte[]` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `string` – Pokud má parametr hodnotu null, když funkce skončí, funkce nevytvoří zprávu.
* `POJO` – Pokud hodnota parametru není formátována jako objekt Java, bude přijata chyba.

---

## <a name="next-steps"></a>Další kroky

- [Spustit funkci při vytvoření zprávy RabbitMQ (trigger)](./functions-bindings-rabbitmq-trigger.md)
