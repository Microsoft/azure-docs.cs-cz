---
title: Azure Queue storage vazby pro službu Azure Functions
description: Pochopit, jak použít aktivační událost Azure Queue storage a výstupní vazby ve službě Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 61752ad9feda7ad6b8d91f1b996b68f27f24b2c6
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821979"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure Queue storage vazby pro službu Azure Functions

Tento článek vysvětluje, jak pracovat s Azure Queue storage vazby ve službě Azure Functions. Azure Functions podporuje aktivaci a výstupní vazby pro fronty.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby front úložiště jsou součástí [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) balíčku NuGet, verze 2.x. Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) úložiště GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Vazby front úložiště jsou součástí [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) balíčku NuGet, verze 3.x. Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Kódování
Funkce očekávat *base64* řetězec s kódováním. Případně upravit typ kódování (aby bylo možné připravit data jako *ve formátu base64* řetězec s kódováním) potřeba je implementovat ve volání služby.

## <a name="trigger"></a>Trigger

Pomocí aktivační událost fronty ke spuštění funkce při přijetí nové položky do fronty. Zpráva fronty je zadán jako vstup do funkce.

## <a name="trigger---example"></a>Aktivační události – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---Java-example)

### <a name="trigger---c-example"></a>Aktivační události – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který se dotazuje `myqueue-items` zařadit do fronty a zapisuje protokol pokaždé, když se zpracuje položka fronty.

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

Následující příklad ukazuje aktivační událost fronty vazby ve *function.json* souboru a [skript jazyka C# (.csx)](functions-reference-csharp.md) kód, který používá vazba. Funkce dotazování `myqueue-items` zařadit do fronty a zapisuje protokol pokaždé, když se zpracuje položka fronty.

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

[Využití](#trigger---usage) bodu vysvětluje `myQueueItem`, který je pojmenován podle `name` vlastnost function.json.  [Zprávy oddíl metadat](#trigger---message-metadata) vysvětluje všechny jiné proměnné uvedené.

### <a name="trigger---javascript-example"></a>Aktivační události – příklad v jazyce JavaScript

Následující příklad ukazuje aktivační událost fronty vazby ve *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce dotazování `myqueue-items` zařadit do fronty a zapisuje protokol pokaždé, když se zpracuje položka fronty.

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
> Název parametru odráží jako `context.bindings.<name>` v kódu jazyka JavaScript, který obsahuje datovou část položky fronty. Tato datová část je také předat jako druhý parametr funkce.

Tady je kód jazyka JavaScript:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

[Využití](#trigger---usage) bodu vysvětluje `myQueueItem`, který je pojmenován podle `name` vlastnost function.json.  [Zprávy oddíl metadat](#trigger---message-metadata) vysvětluje všechny jiné proměnné uvedené.

### <a name="trigger---java-example"></a>Aktivační události – příklad v jazyce Java

V následujícím příkladu Java ukazuje aktivační událost fronty úložiště funkcí, které zaznamená aktivovaných zprávu do fronty `myqueuename`.
 
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

## <a name="trigger---attributes"></a>Aktivační události – atributy
 
V [knihoven tříd C#](functions-dotnet-class-library.md), můžete nakonfigurovat aktivační událost fronty následující atributy:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  Konstruktor atributu má název fronty, pokud chcete sledovat, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Můžete nastavit `Connection` vlastnosti a určit účet úložiště, který chcete použít, jak je znázorněno v následujícím příkladu:

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

  Poskytuje další způsob, jak zadat účet úložiště. Konstruktor přijme název nastavení aplikace, které obsahuje připojovací řetězec úložiště. Atribut je použít na parametr, metody nebo třídy úroveň. Následující příklad ukazuje úrovni třídy a metody:

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

Účet úložiště se určuje v tomto pořadí:

* `QueueTrigger` Atributu `Connection` vlastnost.
* `StorageAccount` Použije pro stejný parametr, jako `QueueTrigger` atribut.
* `StorageAccount` Použije pro funkci.
* `StorageAccount` Atribut aplikován třídu.
* Nastavení aplikace "AzureWebJobsStorage".

## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `QueueTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno| Musí být nastaveno na `queueTrigger`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction**| neuvedeno | V *function.json* pouze soubor. Musí být nastaveno na `in`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | neuvedeno |Název proměnné, která obsahuje datovou část položky fronty v kódu funkce.  | 
|**queueName** | **queueName**| Název fronty posílat do služby. | 
|**připojení** | **připojení** |Název nastavení aplikace, které obsahuje připojovací řetězec úložiště má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat pouze zbytek název tady. Například pokud nastavíte `connection` na "MyStorage", modul runtime služby Functions vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdný, modul runtime služby Functions používá výchozí úložiště připojovací řetězec v nastavení aplikace, který je pojmenován `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Aktivační události – využití
 
V jazyce C# a skript jazyka C#, přístup k datům zprávy pomocí parametru metody, jako `string paramName`. Ve skriptu jazyka C# `paramName` je zadaná hodnota v `name` vlastnost *function.json*. Můžete vytvořit vazbu k některé z následujících typů:

* Objekt – modul runtime služby Functions deserializuje datovou část JSON do instance libovolné třídy definované ve vašem kódu. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Pokud se pokusíte vytvořit vazbu na `CloudQueueMessage` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](#azure-storage-sdk-version-in-functions-1x).

V jazyce JavaScript, použijte `context.bindings.<name>` pro přístup k datové položky fronty. Pokud je datová část JSON, je deserializovat do objektu.

## <a name="trigger---message-metadata"></a>Aktivační události – zpráva metadat

Aktivační událost fronty nabízí několik [vlastnosti metadat](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Tyto vlastnosti lze použít jako součást výrazy vazby v jiných vazbách nebo jako parametry v kódu. Toto jsou vlastnosti [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) třídy.

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|`QueueTrigger`|`string`|Datová část frontu (Pokud je platný řetězec). Pokud fronta zpráv datové části jako řetězec, `QueueTrigger` má stejnou hodnotu jako proměnnou s názvem podle `name` vlastnost *function.json*.|
|`DequeueCount`|`int`|Počet pokusů, které byly vyjmutou tuto zprávu.|
|`ExpirationTime`|`DateTimeOffset`|Čas, kdy vyprší platnost zprávy.|
|`Id`|`string`|ID fronty zprávy.|
|`InsertionTime`|`DateTimeOffset`|Čas, který byl do fronty přidávají zprávy.|
|`NextVisibleTime`|`DateTimeOffset`|Čas, vedle zprávy budou viditelné.|
|`PopReceipt`|`string`|Potvrzení zprávy.|

## <a name="trigger---poison-messages"></a>Aktivační události – počet nezpracovatelných zpráv

Když selže funkce pro aktivaci fronty Azure Functions zopakuje pokus o funkci až pětkrát dané fronty zprávy, včetně první pokus. Pokud selžou i všechny pokusy o pěti, modul runtime služby functions přidá zprávu do fronty s názvem  *&lt;originalqueuename >-nezpracovatelných*. Můžete napsat, že je potřeba funkci zpracování zpráv z fronty nezpracovatelných podle jejich protokolování nebo odeslání oznámení této ruční pozornost.

Chcete-li ručně zpracovat počet nezpracovatelných zpráv, zkontrolujte [dequeueCount](#trigger---message-metadata) zprávy fronty.

## <a name="trigger---polling-algorithm"></a>Aktivační události – algoritmus cyklického dotazování

Aktivační událost fronty implementuje exponenciální regresní algoritmu náhodných aby se snížil dopad nečinné fronty dotazovat se na náklady za transakce úložiště.  Když se najde zprávu, modul runtime počká 2 sekundy a pak vyhledá další zprávy. Když je nalezena žádná zpráva čeká před dalším pokusem o čtyři sekundy. Po následujících neúspěšných pokusech o získání zpráv fronty dobu čekání i nadále zvyšovat, dokud nedosáhne maximální doba čekání, výchozí nastavení je jedna minuta. Maximální doba čekání se dají konfigurovat přes `maxPollingInterval` vlastnost [host.json souboru](functions-host-json.md#queues).

## <a name="trigger---concurrency"></a>Aktivační události – souběžnosti

Po několika fronty zpráv čekajících se aktivační událost fronty načítá dávku zpráv a vyvolá instancí funkce současně pro jejich zpracování. Ve výchozím nastavení velikost dávky je 16. Při zpracování číslo na 8, modul runtime získá další dávku a spustí zpracování zprávy. Maximální počet souběžných zpráv, které zpracovává za funkce na jeden virtuální počítač (VM) je 24. Toto omezení platí zvlášť pro každou funkci aktivovanou protokolem fronty na každém virtuálním počítači. Pokud vaše aplikace function app škálovat do několika virtuálních počítačů, každý virtuální počítač bude čekat aktivační události a pokusí se spouštět službu functions. Například pokud aplikace function app horizontálně navýší kapacitu, pro 3 virtuální počítače, je výchozí maximální počet souběžných instancí jedné funkce aktivované triggerem queue 72.

Velikost dávky a prahová hodnota pro získání nové dávky v se dají konfigurovat [host.json souboru](functions-host-json.md#queues). Pokud chcete minimalizovat paralelní provádění pro funkce aktivované protokolem fronty v aplikaci function app, můžete nastavit velikost dávky 1. Toto nastavení eliminuje souběžnosti, pouze tak dlouho, dokud vaše aplikace function app běží na jeden virtuální počítač (VM). 

Aktivační událost fronty automaticky brání funkce zpracování zprávy fronty více než jednou; Funkce nemají k zapsání jako idempotentní.

## <a name="trigger---hostjson-properties"></a>Aktivační události – vlastnosti host.json

[Host.json](functions-host-json.md#queues) soubor obsahuje nastavení, která řídí chování aktivační událost fronty.

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="output"></a>Výstup

Použití výstupní vazby pro zápis zpráv do fronty úložiště front Azure.

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Výstup – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , která vytvoří zprávu fronty pro každé žádosti HTTP přijaté.

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

Následující příklad ukazuje vazby v triggeru HTTP *function.json* souboru a [skript jazyka C# (.csx)](functions-reference-csharp.md) kód, který používá vazba. Funkce vytvoří položka fronty s **CustomQueueMessage** objekt datové části pro každé žádosti HTTP přijaté.

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

[Konfigurace](#output---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód jazyka C# skript, který vytvoří jedna fronta zpráv:

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

Můžete odeslat více zpráv najednou pomocí `ICollector` nebo `IAsyncCollector` parametru. Tady je kód jazyka C# skript, který odesílá více zpráv, jednu s daty požadavku HTTP a jednu s pevně definovaných hodnot:

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

Následující příklad ukazuje vazby v triggeru HTTP *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce vytvoří položka fronty pro každé žádosti HTTP přijaté.

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
      "connection": "MyStorageConnectionAppSetting",
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

Můžete odeslat více zpráv najednou tak, že definujete pole zprávy pro `myQueueItem` výstupní vazbu. Následující kód jazyka JavaScript odešle dvě fronty zpráv s pevně definovaných hodnot pro každé žádosti HTTP přijaté.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

### <a name="output---java-example"></a>Výstup – příklad v jazyce Java

 Následující příklad ukazuje funkci Java, která vytvoří zprávu fronty pro aktivovaného požadavku HTTP.

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

V [Java funkce knihovny prostředí runtime](/java/api/overview/azure/functions/runtime), použijte `@QueueOutput` poznámku o parametrech, jehož hodnota by byla zapsána do fronty úložiště.  Typ parametru by měl být `OutputBinding<T>`, kde T je libovolný Java nativní objekt POJO.


## <a name="output---attributes"></a>Výstup – atributy
 
V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

Atribut se vztahuje na `out` parametr nebo návratovou hodnotu funkce. Konstruktor atributu má název fronty, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Můžete nastavit `Connection` vlastnosti a určit účet úložiště, který chcete použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Kompletní příklad naleznete v tématu [výstup – příklad v jazyce C#](#output---c-example).

Můžete použít `StorageAccount` atribut k určení účtu úložiště na úrovni třídy, metody nebo parametr. Další informace najdete v tématu aktivační události – atributy.

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Queue` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastaveno na `queue`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastaveno na `out`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje frontu v kódu funkce. Nastavte na `$return` tak, aby odkazovaly návratovou hodnotu funkce.| 
|**queueName** |**queueName** | Název fronty | 
|**připojení** | **připojení** |Název nastavení aplikace, které obsahuje připojovací řetězec úložiště má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat pouze zbytek název tady. Například pokud nastavíte `connection` na "MyStorage", modul runtime služby Functions vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdný, modul runtime služby Functions používá výchozí úložiště připojovací řetězec v nastavení aplikace, který je pojmenován `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití
 
V jazyce C# a skript jazyka C#, psát pomocí parametru metody, jako zpráva s jednou frontou `out T paramName`. Ve skriptu jazyka C# `paramName` je zadaná hodnota v `name` vlastnost *function.json*. Návratový typ metody místo můžete použít `out` parametr, a `T` může být kterýkoli z následujících typů:

* Objekt serializovat jako JSON.
* `string`
* `byte[]`
* [CloudQueueMessage] 

Pokud se pokusíte vytvořit vazbu na `CloudQueueMessage` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](#azure-storage-sdk-version-in-functions-1x).

V jazyce C# a skript jazyka C# psát více zpráv fronty pomocí jedné z následujících typů: 

* `ICollector<T>` nebo `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

Do funkce jazyka JavaScript, použijte `context.bindings.<name>` pro přístup k výstupní fronty zprávu. Řetězec nebo objekt JSON serializovatelný můžete použít pro datovou část položky fronty.


## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba |  Referenční informace |
|---|---|
| Fronta | [Kódy chyb fronty](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Objekt BLOB, tabulky, fronty | [Kódy chyb úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objekt BLOB, tabulky, fronty |  [Řešení potíží](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení Host.JSON

Tato část popisuje globální konfiguraci nastavení k dispozici pro tuto vazbu ve verzi 2.x. Příklad souboru host.json níže obsahuje pouze verzi 2.x nastavení pro tuto vazbu. Další informace o globální nastavení konfigurace ve verzi 2.x, naleznete v tématu [referenční materiály k host.json pro Azure Functions verze 2.x](functions-host-json.md).

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
|maxPollingInterval|00:00:02|Maximální časový interval mezi dotazuje fronty. Minimální hodnota je 00:00:00.100 (100 ms). | 
|visibilityTimeout|00:00:00|Časový interval mezi opakovanými pokusy při zpracování zprávy se nezdaří. | 
|batchSize|16|Počet zpráv fronty, které modul runtime služby Functions současně načte a zpracuje paralelně. Jakmile číslo zpracovává přejdete dolů k `newBatchThreshold`, modul runtime získá další dávku a spustí zpracování zprávy. Maximální počet souběžných za funkce zpracování zprávy je `batchSize` plus `newBatchThreshold`. Toto omezení platí zvlášť pro každou funkci aktivovanou protokolem fronty. <br><br>Pokud chcete se vyhnout paralelní provádění pro zprávy přijaté pro jednu frontu, můžete nastavit `batchSize` na hodnotu 1. Toto nastavení však eliminuje souběžnosti, pouze tak dlouho, dokud vaše aplikace function app běží na jeden virtuální počítač (VM). Pokud aplikace function app škálovat do několika virtuálních počítačů, každý virtuální počítač může spustit jednu instanci každé funkce aktivované triggerem queue.<br><br>Maximální počet `batchSize` je 32. | 
|maxDequeueCount|5|Počet pokusů, zpracovává zprávu před přesunutím do poškozené fronty.| 
|newBatchThreshold|batchSize/2|Pokaždé, když se počet zpráv souběžně zpracováváno získá na toto číslo, modul runtime načte jiná dávka.| 

## <a name="next-steps"></a>Další postup

* [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Přejděte na kurz, který používá Queue storage výstupní vazby](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
