---
title: Aktivační událost RabbitMQ pro Azure Functions
description: Naučte se spouštět funkci Azure, když se vytvoří zpráva RabbitMQ.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/16/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 1db27db97cdc1746b3392bd386ee6539980cd6d6
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630730"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>RabbitMQ Trigger for Azure Functions – přehled

> [!NOTE]
> Vazby RabbitMQ jsou plně podporované jenom na **Windows Premium a na vyhrazených** plánech. Spotřeba a Linux se aktuálně nepodporují.

Pomocí triggeru RabbitMQ můžete reagovat na zprávy z fronty RabbitMQ.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](functions-bindings-rabbitmq.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která čte a zapisuje zprávu RabbitMQ jako [událost RabbitMQ](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html):

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnection")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

Následující příklad ukazuje, jak číst zprávu jako POCO.

```cs
public class TestClass
{
    public string x { get; set; }
}

[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnection")] TestClass pocObj,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(pocObj)}");
}
```

Podobně jako u objektů JSON dojde k chybě, pokud zpráva není správně naformátována jako objekt jazyka C#. Pokud je, je pak svázán s proměnnou pocObj, která se dá použít k tomu, co je potřeba pro.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje vazbu triggeru RabbitMQ v *function.js* souboru a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce přečte a zaprotokoluje zprávu RabbitMQ.

Tady jsou data vazby v *function.js* souboru:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

Tady je kód skriptu jazyka C#:

```csx
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vazbu triggeru RabbitMQ v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce přečte a zaznamená zprávu RabbitMQ.

Tady jsou data vazby v *function.js* souboru:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

Tady je kód skriptu JavaScriptu:

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje, jak číst zprávu fronty RabbitMQ prostřednictvím triggeru.

Vazba RabbitMQ je definována v *function.jsna* místě, kde je *typ* nastaven na `RabbitMQTrigger` .

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

Následující funkce jazyka Java používá `@RabbitMQTrigger` anotaci z [typů Java RabbitMQ](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) k popisu konfigurace triggeru RabbitMQ Queue. Funkce přiřadí zprávu umístěnou ve frontě a přidá ji do protokolů.

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnection", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs) .

Zde je `RabbitMQTrigger` atribut v signatuře metody:

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
{
    ...
}
```

Úplný příklad najdete v tématu příklad v jazyce C#.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

`RabbitMQTrigger`Poznámka umožňuje vytvořit funkci, která se spustí při vytvoření zprávy RabbitMQ. K dispozici jsou možnosti konfigurace, včetně názvu fronty a názvu připojovacího řetězce. Další podrobnosti o parametrech najdete v [poznámkách Java RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java).

Další podrobnosti najdete v [příkladu](#example) triggeru.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `RabbitMQTrigger` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**textový** | neuvedeno | Musí být nastavené na "RabbitMQTrigger".|
|**směr** | neuvedeno | Musí být nastavené na "in".|
|**Jméno** | neuvedeno | Název proměnné, která představuje frontu v kódu funkce. |
|**Proměnné QueueName**|**Proměnné QueueName**| Název fronty, ze které se mají přijímat zprávy |
|**Název hostitele**|**Název hostitele**|(ignoruje se, pokud používáte ConnectStringSetting) <br>Název hostitele fronty (např.: 10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(ignoruje se, pokud používáte ConnectionStringSetting) <br>Název nastavení aplikace, které obsahuje uživatelské jméno pro přístup do fronty. Například UserNameSetting: "% < UserNameFromSettings >%"|
|**passwordSetting**|**PasswordSetting**|(ignoruje se, pokud používáte ConnectionStringSetting) <br>Název nastavení aplikace, které obsahuje heslo pro přístup do fronty. Například PasswordSetting: "% < PasswordFromSettings >%"|
|**connectionStringSetting**|**ConnectionStringSetting**|Název nastavení aplikace, které obsahuje připojovací řetězec fronty zpráv RabbitMQ Upozorňujeme, že pokud zadáte připojovací řetězec přímo a nikoli prostřednictvím nastavení aplikace v local.settings.js, aktivační událost nebude fungovat. (Např.: v *function.jsna*: connectionStringSetting: "rabbitMQConnection" <br> V *local.settings.js*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**přístavní**|**Port**|(ignoruje se, pokud používáte ConnectionStringSetting) Získá nebo nastaví použitý port. Výchozí hodnota je 0.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

Výchozí typ zprávy je [RabbitMQ událost](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html)a `Body` vlastnost události RabbitMQ lze číst jako níže uvedené typy:

* `An object serializable as JSON` -Zpráva je doručena jako platný řetězec JSON.
* `string`
* `byte[]`
* `POCO` -Zpráva je formátována jako objekt jazyka C#. Úplný příklad najdete v tématu [příklad](#example)v jazyce C#.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Výchozí typ zprávy je [RabbitMQ událost](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html)a `Body` vlastnost události RabbitMQ lze číst jako níže uvedené typy:

* `An object serializable as JSON` -Zpráva je doručena jako platný řetězec JSON.
* `string`
* `byte[]`
* `POCO` -Zpráva je formátována jako objekt jazyka C#. Úplný příklad najdete v tématu [příklad](#example)skriptu jazyka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Zpráva fronty je k dispozici prostřednictvím Context. Bindings.<NAME> kde <NAME> odpovídá názvu definovanému v function.js. Pokud je datová část JSON, hodnota je deserializována do objektu.

# <a name="python"></a>[Python](#tab/python)

Podívejte se na [příklad](#example)Pythonu.

# <a name="java"></a>[Java](#tab/java)

Přečtěte si téma [atributy a poznámky](#attributes-and-annotations)jazyka Java.

---

## <a name="dead-letter-queues"></a>Fronty nedoručených zpráv
Fronty nedoručených zpráv a výměny nelze řídit ani konfigurovat pomocí triggeru RabbitMQ.  Aby bylo možné používat fronty nedoručených zpráv, předem nakonfigurujte frontu používanou triggerem v RabbitMQ. Přečtěte si prosím [dokumentaci k RabbitMQ](https://www.rabbitmq.com/dlx.html).

## <a name="hostjson-settings"></a>host.jsnastavení

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2. x a vyšší. Příklad *host.jsv* souboru níže obsahuje pouze nastavení této vazby. Další informace o globálních nastaveních konfigurace najdete v tématu [host.jsv referenčních informacích pro Azure Functions verzi](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|prefetchCount|30|Získá nebo nastaví počet zpráv, které může příjemce zprávy současně požadovat a který je uložen do mezipaměti.|
|Proměnné QueueName|neuvedeno| Název fronty, ze které se mají přijímat zprávy |
|připojovací řetězec|neuvedeno|Název nastavení aplikace, které obsahuje připojovací řetězec fronty zpráv RabbitMQ Upozorňujeme, že pokud zadáte připojovací řetězec přímo a nikoli prostřednictvím nastavení aplikace v local.settings.js, aktivační událost nebude fungovat.|
|port|0|(ignoruje se při použití connectionString) Maximální počet relací, které mohou být zpracovány souběžně podle škálované instance.|

## <a name="local-testing"></a>Místní testování

> [!NOTE]
> Řetězec connectionString má přednost před názvem "název hostitele", "userName" a "Password". Pokud jsou všechny nastaveny, připojovací řetězec přepíše ostatní dva.

Pokud testujete místně bez připojovacího řetězce, měli byste nastavit nastavení "název hostitele" a "uživatelské jméno" a "heslo", pokud je to možné v části "rabbitMQ" *host.jsna*:

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "userNameSetting",
            "password": "passwordSetting"
        }
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|Název hostitele|neuvedeno|(ignoruje se, pokud používáte ConnectStringSetting) <br>Název hostitele fronty (např.: 10.26.45.210)|
|userName|neuvedeno|(ignoruje se, pokud používáte ConnectionStringSetting) <br>Název pro přístup do fronty |
|heslo|neuvedeno|(ignoruje se, pokud používáte ConnectionStringSetting) <br>Heslo pro přístup do fronty|

## <a name="monitoring-rabbitmq-endpoint"></a>Monitorování koncového bodu RabbitMQ
Monitorování front a výměn pro určitý koncový bod RabbitMQ:

* Povolení [modulu plug-in pro správu RabbitMQ](https://www.rabbitmq.com/management.html)
* Přejděte na http://{node-hostname}: 15672 a přihlaste se pomocí uživatelského jména a hesla.

## <a name="next-steps"></a>Další kroky

- [Posílání zpráv RabbitMQ z Azure Functions (výstupní vazba)](./functions-bindings-rabbitmq-output.md)
