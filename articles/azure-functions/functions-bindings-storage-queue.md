---
title: Vazby úložiště front Azure pro Azure Functions
description: Naučte se používat aktivační událost a výstupní vazbu Azure Queue Storage v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 3680de5d8e0e761047e1263c2679da87b1fa2d0b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769451"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Vazby úložiště front Azure pro Azure Functions

Tento článek vysvětluje, jak pracovat s vazbami služby Azure Queue Storage v Azure Functions. Azure Functions podporuje triggery a výstupní vazby pro fronty.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby úložiště fronty jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x. Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) úložiště GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

Vazby úložiště fronty jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) , verze 3. x. Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Encoding
Funkce očekávají řetězec kódovaný v *kódování Base64* . Jakékoli úpravy typu kódování (aby bylo možné připravit data jako řetězec kódovaný v *kódování Base64* ), je nutné implementovat do volající služby.

## <a name="trigger"></a>Trigger

Pomocí triggeru Queue spustíte funkci při přijetí nové položky ve frontě. Jako vstup funkce se poskytuje zpráva fronty.

## <a name="trigger---example"></a>Aktivační události – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Aktivační události – příklad v jazyce C#

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která se dotazuje `myqueue-items` fronty a zapisuje protokol pokaždé, když se zpracuje položka fronty.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Aktivační události – příklad skriptu jazyka C#

Následující příklad ukazuje vazbu triggeru fronty v kódu souboru *Function. JSON* a [ C# skriptu (. csx)](functions-reference-csharp.md) , který používá vazbu. Funkce se dotazuje fronty `myqueue-items` a zapisuje protokol pokaždé, když se zpracuje položka fronty.

Tady je *function.json* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[Konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

Část [použití](#trigger---usage) vysvětluje `myQueueItem`, která je pojmenována vlastností `name` v Function. JSON.  [Část metadata zprávy](#trigger---message-metadata) vysvětluje všechny zobrazené proměnné.

### <a name="trigger---javascript-example"></a>Aktivační události – příklad v jazyce JavaScript

Následující příklad ukazuje vazbu triggeru fronty v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce se dotazuje fronty `myqueue-items` a zapisuje protokol pokaždé, když se zpracuje položka fronty.

Tady je *function.json* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[Konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

> [!NOTE]
> Parametr Name odráží `context.bindings.<name>` v kódu JavaScriptu, který obsahuje datovou část položky fronty. Tato datová část je také předána jako druhý parametr funkce.

Tady je kód jazyka JavaScript:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

Část [použití](#trigger---usage) vysvětluje `myQueueItem`, která je pojmenována vlastností `name` v Function. JSON.  [Část metadata zprávy](#trigger---message-metadata) vysvětluje všechny zobrazené proměnné.

### <a name="trigger---java-example"></a>Aktivační události – příklad v jazyce Java

Následující příklad Java ukazuje funkce triggeru fronty úložiště, které zaprotokolují aktivační zprávu umístěnou do fronty `myqueuename`.

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

### <a name="trigger---python-example"></a>Trigger – příklad Pythonu

Následující příklad ukazuje, jak číst zprávu fronty předanou funkci prostřednictvím triggeru.

Aktivační událost fronty úložiště je definovaná v souboru *Function. JSON* , kde *Type* je nastavená na `queueTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Kód  *_\_init_\_. py* deklaruje parametr jako `func.ServiceBusMessage`, což umožňuje číst zprávu fronty ve funkci.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

## <a name="trigger---attributes"></a>Aktivační události – atributy

V [ C# části knihovny tříd](functions-dotnet-class-library.md)použijte následující atributy ke konfiguraci triggeru fronty:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  Konstruktor atributu přebírá název fronty, která se má monitorovat, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Vlastnost `Connection` můžete nastavit tak, aby určovala účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Kompletní příklad naleznete v tématu [Trigger – C# příklad](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje jiný způsob určení účtu úložiště, který se má použít. Konstruktor převezme název nastavení aplikace, které obsahuje připojovací řetězec úložiště. Atribut je použít na parametr, metody nebo třídy úroveň. Následující příklad ukazuje úrovni třídy a metody:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Účet úložiště, který se má použít, se určuje v tomto pořadí:

* `QueueTrigger` Atributu `Connection` vlastnost.
* `StorageAccount` Použije pro stejný parametr, jako `QueueTrigger` atribut.
* `StorageAccount` Použije pro funkci.
* `StorageAccount` Atribut aplikován třídu.
* Nastavení aplikace "AzureWebJobsStorage".

## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `QueueTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | –| Musí být nastaveno na `queueTrigger`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction**| – | Pouze v souboru *Function. JSON* . Musí být nastaveno na `in`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**name** | – |Název proměnné, která obsahuje datovou část položky fronty v kódu funkce.  |
|**queueName** | **queueName**| Název fronty, která se má dotazovat. |
|**připojení** | **připojení** |Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "MyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační události – využití

Do C# skriptu C# a použijte k datům zprávy parametr metody, jako je například `string paramName`. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *Function. JSON*. Můžete vytvořit propojení s některým z následujících typů:

* Object – modul runtime funkcí deserializace datovou část JSON do instance libovolné třídy definované ve vašem kódu. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Pokud se pokusíte vytvořit navázání na `CloudQueueMessage` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby úložiště](#azure-storage-sdk-version-in-functions-1x).

V jazyce JavaScript použijte `context.bindings.<name>` pro přístup k datové části položky fronty. Pokud je datová část JSON, je deserializována do objektu.

## <a name="trigger---message-metadata"></a>Aktivační události – zpráva metadat

Aktivační událost fronty poskytuje několik [vlastností metadat](./functions-bindings-expressions-patterns.md#trigger-metadata). Tyto vlastnosti lze použít jako součást výrazy vazby v jiných vazbách nebo jako parametry v kódu. Jedná se o vlastnosti třídy [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) .

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|`QueueTrigger`|`string`|Datová část fronty (Pokud platný řetězec). Pokud datová část zprávy fronty jako řetězec, `QueueTrigger` má stejnou hodnotu jako proměnná s názvem vlastností `name` v *Function. JSON*.|
|`DequeueCount`|`int`|Počet, kolikrát byla tato zpráva odstraněna z fronty.|
|`ExpirationTime`|`DateTimeOffset`|Čas vypršení platnosti zprávy|
|`Id`|`string`|ID zprávy fronty|
|`InsertionTime`|`DateTimeOffset`|Čas přidání zprávy do fronty.|
|`NextVisibleTime`|`DateTimeOffset`|Čas, kdy bude zpráva zobrazena dál.|
|`PopReceipt`|`string`|Účtenka pro zprávu pop.|

## <a name="trigger---poison-messages"></a>Aktivační události – počet nezpracovatelných zpráv

Pokud se funkce triggeru fronty nezdařila, Azure Functions se znovu pokusí použít tuto funkci až pětkrát pro danou zprávu fronty, včetně prvního pokusu. Pokud se všechny pět pokusů nezdaří, modul runtime Functions přidá zprávu do fronty s názvem *&lt;originalqueuename >-otrav*. Můžete napsat funkci pro zpracování zpráv z fronty poškození tím, že je přihlásíte nebo posíláte oznámení, že je potřeba ruční pozornost.

Pokud chcete zpracovat poškozené zprávy ručně, podívejte se do [dequeueCount](#trigger---message-metadata) zprávy ve frontě.

## <a name="trigger---polling-algorithm"></a>Aktivační událost – algoritmus cyklického dotazování

Aktivační událost fronty implementuje náhodný exponenciální algoritmus pro snížení účinku nečinnosti při cyklickém dotazování na poplatky za transakce úložiště.

Algoritmus používá následující logiku:

- Když se najde zpráva, modul runtime počká dvě sekundy a pak zkontroluje další zprávu.
- Pokud se nenajde žádná zpráva, před opakováním počkejte přibližně čtyři sekundy.
- Po následném neúspěšném pokusu o získání zprávy fronty se doba čekání zvětšuje, dokud nedosáhne maximální čekací doby, která je ve výchozím nastavení jedna minuta.
- Maximální čekací dobu lze konfigurovat prostřednictvím vlastnosti `maxPollingInterval` v [souboru Host. JSON](functions-host-json.md#queues).

Pro místní vývoj je maximální interval cyklického dotazování ve výchozím nastavení na dvě sekundy.

V souvislosti s fakturací je čas strávený při cyklickém dotazování modulem runtime "Free" a nepočítá se s vaším účtem.

## <a name="trigger---concurrency"></a>Aktivační procedura – souběžnost

Pokud se čeká více zpráv ve frontách, aktivační událost fronty načte dávku zpráv a současně vyvolá instance funkcí, aby je mohla zpracovat. Ve výchozím nastavení je velikost dávky 16. Když se zpracovávané číslo dostane do 8, modul runtime získá další dávku a začne tyto zprávy zpracovávat. Takže maximální počet souběžných zpráv zpracovávaných na jednu funkci na jednom virtuálním počítači (VM) je 24. Toto omezení platí samostatně u každé funkce aktivované frontou na každém virtuálním počítači. Pokud vaše aplikace Functions škáluje na více virtuálních počítačů, bude každý virtuální počítač čekat na triggery a pokusí se spustit funkce. Například pokud se aplikace funkcí škáluje na 3 virtuální počítače, výchozí maximální počet souběžných instancí jedné funkce aktivované frontou je 72.

Velikost dávky a prahová hodnota pro získání nové dávky jsou konfigurovatelné v [souboru Host. JSON](functions-host-json.md#queues). Pokud chcete minimalizovat paralelní spouštění funkcí aktivovaných frontou ve Function App, můžete nastavit velikost dávky na 1. Toto nastavení eliminuje souběžnost, pokud vaše aplikace Function App běží na jednom virtuálním počítači (VM). 

Aktivační událost fronty automaticky zabrání funkci ve zpracování zprávy fronty vícekrát. funkce není nutné zapisovat, aby bylo možné je idempotentní.

## <a name="trigger---hostjson-properties"></a>Aktivační události – vlastnosti host.json

Soubor [Host. JSON](functions-host-json.md#queues) obsahuje nastavení, která řídí chování aktivace fronty. Podrobnosti o dostupných nastaveních najdete v části [Nastavení Host. JSON](#hostjson-settings) .

## <a name="output"></a>Výstup

Zapište zprávy do fronty pomocí výstupní vazby Azure Queue Storage.

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Výstup – příklad v jazyce C#

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která vytvoří zprávu fronty pro každý přijatý požadavek HTTP.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>Výstup – příklad skriptu jazyka C#

Následující příklad ukazuje vazbu triggeru protokolu HTTP v souboru *Function. JSON* a [ C# v kódu skriptu (. csx)](functions-reference-csharp.md) , který používá vazbu. Funkce vytvoří položku fronty s datovou částí objektu **CustomQueueMessage** pro každý PŘIJATÝ požadavek HTTP.

Tady je *function.json* souboru:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

[Konfigurace](#output---configuration) bodu vysvětluje tyto vlastnosti.

Zde je C# kód skriptu, který vytváří jednu zprávu fronty:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Pomocí parametru `ICollector` nebo `IAsyncCollector` můžete odeslat více zpráv najednou. Zde je C# kód skriptu, který odesílá více zpráv, jednu s daty požadavku HTTP a jednu s pevně zakódovanými hodnotami:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Výstup – příklad v jazyce JavaScript

Následující příklad ukazuje vazbu triggeru protokolu HTTP v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce vytvoří položku fronty pro každý přijatý požadavek HTTP.

Tady je *function.json* souboru:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

[Konfigurace](#output---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Více zpráv najednou můžete odeslat tak, že definujete pole zpráv pro výstupní vazbu `myQueueItem`. Následující kód jazyka JavaScript odesílá dvě zprávy fronty s pevně zakódovanými hodnotami pro každý přijatý požadavek HTTP.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

### <a name="output---java-example"></a>Výstup – příklad v jazyce Java

 Následující příklad ukazuje funkci jazyka Java, která vytvoří zprávu fronty pro, když se aktivuje požadavkem HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "AzureWebJobsStorage")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding&lt;String&gt; result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

V [knihovně runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@QueueOutput` anotaci u parametrů, jejichž hodnota by byla zapsána do fronty úložiště.  Typ parametru by měl být `OutputBinding<T>`, kde T je libovolný Java nativní objekt POJO.

### <a name="output---python-example"></a>Výstup – příklad Pythonu

Následující příklad ukazuje, jak vymezit výstup jedné a více hodnot do front úložiště. Konfigurace potřebná pro *funkci Function. JSON* je stejná jako v obou případech.

Vazba fronty úložiště je definována v souboru *Function. JSON* , kde *typ* je nastaven na `queue`.

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
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Chcete-li nastavit samostatnou zprávu ve frontě, předejte jednu hodnotu metodě `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Chcete-li vytvořit více zpráv ve frontě, deklarujte parametr jako příslušný typ seznamu a předejte pole hodnot (odpovídající typu seznamu) metodě `set`.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

## <a name="output---attributes"></a>Výstup – atributy

V [ C# knihovně tříd](functions-dotnet-class-library.md)použijte [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

Atribut se vztahuje na parametr `out` nebo návratovou hodnotu funkce. Konstruktor atributu přebírá název fronty, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Vlastnost `Connection` můžete nastavit tak, aby určovala účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Kompletní příklad naleznete v tématu [výstup – příklad v jazyce C#](#output---c-example).

Atribut `StorageAccount` lze použít k určení účtu úložiště na úrovni třídy, metody nebo parametru. Další informace najdete v tématu Trigger – atributy.

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Queue` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | – | Musí být nastaveno na `queue`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | – | Musí být nastaveno na `out`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**name** | – | Název proměnné, která představuje frontu v kódu funkce. Nastavte na `$return` pro odkaz na návratovou hodnotu funkce.|
|**queueName** |**queueName** | Název fronty. |
|**připojení** | **připojení** |Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "MyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

Do C# skriptu C# a zapište jednu zprávu fronty pomocí parametru metody, jako je například `out T paramName`. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *Function. JSON*. Místo parametru `out` můžete použít návratový typ metody a `T` může být kterýkoli z následujících typů:

* Objekt serializovatelný jako JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Pokud se pokusíte vytvořit navázání na `CloudQueueMessage` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby úložiště](#azure-storage-sdk-version-in-functions-1x).

Do C# skriptu C# a zapište více zpráv fronty pomocí jednoho z následujících typů: 

* `ICollector<T>` nebo `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

Ve funkcích JavaScriptu použijte `context.bindings.<name>` pro přístup ke zprávě výstupní fronty. Pro datovou část položky fronty lze použít řetězec nebo objekt s možností serializace JSON.


## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba |  Referenční informace |
|---|---|
| Fronta | [Chybové kódy fronty](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Objekt blob, tabulka, fronta | [Kódy chyb úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objekt blob, tabulka, fronta |  [Řešení potíží](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení Host.JSON

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2. x a vyšší. Ukázkový soubor host. JSON níže obsahuje pouze nastavení verze 2. x + pro tuto vazbu. Další informace o globálních nastaveních konfigurace ve verzích 2. x a novějších naleznete v tématu [reference Host. JSON pro Azure Functions](functions-host-json.md).

> [!NOTE]
> Pro odkaz host.json ve funkcích 1.x, najdete v článku [referenční materiály k host.json pro Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```


|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Maximální interval mezi cykly dotazování fronty. Minimum je 00:00:00.100 (100 ms) a zvýší až 00:01:00 (1 min).  V 1. x je datový typ milisekund a v 2. x a vyšší je časový interval.|
|visibilityTimeout|00:00:00|Časový interval mezi opakovanými pokusy při zpracování zprávy se nezdařil. |
|batchSize|16|Počet zpráv ve frontě, které funkce runtime Functions načítá současně a procesy paralelně. Když se zpracovávané číslo vrátí do `newBatchThreshold`, modul runtime získá další dávku a začne tyto zprávy zpracovávat. Proto je maximální počet souběžných zpráv zpracovávaných na funkci `batchSize` a `newBatchThreshold`. Toto omezení se vztahuje odděleně na jednotlivé funkce aktivované frontou. <br><br>Pokud se chcete vyhnout paralelnímu provádění zpráv přijatých v jedné frontě, můžete nastavit `batchSize` na 1. Toto nastavení však eliminuje souběžnost, pokud vaše aplikace Function App běží na jednom virtuálním počítači. Pokud se aplikace funkcí škáluje na více virtuálních počítačů, každý virtuální počítač může spustit jednu instanci každé funkce aktivované frontou.<br><br>Maximální `batchSize` je 32. |
|maxDequeueCount|5|Počet pokusů o zpracování zprávy před jejich přesunutím do nepoškozené fronty.|
|newBatchThreshold|batchSize/2|Pokaždé, když se počet zpracovávaných zpráv souběžně vrátí k tomuto číslu, modul runtime načte další dávku.|

## <a name="next-steps"></a>Další kroky

* [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Přejít na kurz, který používá výstupní vazbu úložiště front](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
