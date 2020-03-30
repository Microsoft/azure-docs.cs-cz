---
title: Aktivační událost úložiště fronty Azure pro funkce Azure
description: Naučte se spouštět funkci Azure jako změny dat úložiště fronty Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 74ca984232bef979062221a451d0ee10a6965bc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277372"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Aktivační událost úložiště fronty Azure pro funkce Azure

Aktivační událost úložiště fronty spustí funkci jako zprávy se přidávají do úložiště Fronty Azure.

## <a name="encoding"></a>Kódování

Funkce očekávají řetězec kódovaný *base64.* Všechny úpravy typu kódování (za účelem přípravy dat jako řetězec zakódovaný *base64)* musí být implementovány v volající službě.

## <a name="example"></a>Příklad

Pomocí aktivační události fronty spusťte funkci při přijetí nové položky do fronty. Jako vstup funkce se poskytuje zpráva fronty.

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která dotazování `myqueue-items` fronty a zapíše protokol při každém zpracování položky fronty.

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

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje vazby aktivační události fronty v souboru *function.json* a kódu [skriptu Jazyka C# (.csx),](functions-reference-csharp.md) který používá vazbu. Funkce dotazování `myqueue-items` fronty a zapíše protokol při každém zpracování položky fronty.

Zde je *soubor function.json:*

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód skriptu C#:

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

Oddíl [použití](#usage) vysvětluje `myQueueItem`, který je `name` pojmenován vlastností v function.json.  [Část metadat zprávy](#message-metadata) vysvětluje všechny ostatní zobrazené proměnné.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje vazby aktivační události fronty v souboru *function.json* a [funkci JavaScriptu,](functions-reference-node.md) která vazbu používá. Funkce dotazování `myqueue-items` fronty a zapíše protokol při každém zpracování položky fronty.

Zde je *soubor function.json:*

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

> [!NOTE]
> Parametr name odráží `context.bindings.<name>` jako v kódu Jazyka JavaScript, který obsahuje datovou část položky fronty. Tato datová část je také předána jako druhý parametr funkce.

Zde je kód JavaScript:

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

Oddíl [použití](#usage) vysvětluje `myQueueItem`, který je `name` pojmenován vlastností v function.json.  [Část metadat zprávy](#message-metadata) vysvětluje všechny ostatní zobrazené proměnné.

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje, jak číst zprávu fronty předané funkci prostřednictvím aktivační události.

Aktivační událost fronty úložiště je definována v *souboru function.json,* kde je *typ* nastaven na `queueTrigger`.

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

Kód `func.ServiceBusMessage`init *\_.py deklaruje parametr jako , který umožňuje číst zprávu fronty ve vaší funkci. _ \__*

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

# <a name="java"></a>[Java](#tab/java)

Následující příklad jazyka Java ukazuje funkci aktivační události fronty úložiště, `myqueuename`která zaznamenává aktivovanou zprávu umístěnou do fronty .

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

 ---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte ke konfiguraci aktivační události fronty následující atributy:

* [Atribut Trigger queuetrigger](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  Konstruktor atributu přebírá název fronty ke sledování, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Vlastnost můžete `Connection` nastavit tak, aby určovat nastavení aplikace, která obsahuje připojovací řetězec účtu úložiště použít, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Úplný příklad naleznete v [příkladu](#example).

* [Atribut účtu úložiště](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje další způsob, jak zadat účet úložiště, který se má použít. Konstruktor přebírá název nastavení aplikace, která obsahuje připojovací řetězec úložiště. Atribut lze použít na úrovni parametru, metody nebo třídy. Následující příklad ukazuje úroveň třídy a úroveň metody:

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

Účet úložiště, který se má použít, se určuje v následujícím pořadí:

* Vlastnost `QueueTrigger` atributu. `Connection`
* Atribut `StorageAccount` použitý na stejný parametr `QueueTrigger` jako atribut.
* Atribut `StorageAccount` použitý pro funkci.
* Atribut `StorageAccount` použitý pro třídu.
* Nastavení aplikace "AzureWebJobsStorage".

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Anotace `QueueTrigger` umožňuje přístup do fronty, která spouští funkci. Následující příklad zpřístupní zprávu fronty funkci `message` prostřednictvím parametru.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Vlastnost    | Popis |
|-------------|-----------------------------|
|`name`       | Deklaruje název parametru v podpisu funkce. Při spuštění funkce má hodnota tohoto parametru obsah zprávy fronty. |
|`queueName`  | Deklaruje název fronty v účtu úložiště. |
|`connection` | Odkazuje na připojovací řetězec účtu úložiště. |

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `QueueTrigger` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno| Musí být `queueTrigger`nastavena na . Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal.|
|**direction**| neuvedeno | Pouze v souboru *function.json.* Musí být `in`nastavena na . Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal. |
|**Jméno** | neuvedeno |Název proměnné, která obsahuje datovou část položky fronty v kódu funkce.  |
|**název_fronty** | **Název fronty**| Název fronty k dotazování. |
|**Připojení** | **Připojení** |Název nastavení aplikace, která obsahuje připojovací řetězec úložiště pro tuto vazbu. Pokud název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytek názvu zde. Například pokud nastavíte `connection` "MyStorage", funkce runtime hledá nastavení aplikace s názvem "MyStorage." Pokud necháte `connection` prázdné, spustí se s funkcí, která použije výchozí `AzureWebJobsStorage`připojovací řetězec úložiště v nastavení aplikace s názvem .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C #](#tab/csharp)

Přístup k datům zprávy pomocí `string paramName`parametru metody, například . Můžete vytvořit vazbu na některý z následujících typů:

* Objekt - Functions runtime deserializes json datové části do instance libovolné třídy definované ve vašem kódu. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Pokud se pokusíte `CloudQueueMessage` vytvořit vazbu a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Přístup k datům zprávy pomocí `string paramName`parametru metody, například . Je `paramName` hodnota zadaná `name` ve vlastnosti *function.json*. Můžete vytvořit vazbu na některý z následujících typů:

* Objekt - Functions runtime deserializes json datové části do instance libovolné třídy definované ve vašem kódu. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Pokud se pokusíte `CloudQueueMessage` vytvořit vazbu a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[Javascript](#tab/javascript)

Datová část položky `context.bindings.<NAME>` fronty `<NAME>` je k dispozici prostřednictvím souboru, kde odpovídá názvu definovanému v *souboru function.json*. Pokud je datová část JSON, hodnota je deserializována na objekt.

# <a name="python"></a>[Python](#tab/python)

Přístup ke zprávě fronty prostřednictvím parametru zadaného jako [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="java"></a>[Java](#tab/java)

Anotace [QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) umožňuje přístup ke zprávě fronty, která spustila funkci.

---

## <a name="message-metadata"></a>Metadata zprávy

Aktivační událost fronty poskytuje několik [vlastností metadat](./functions-bindings-expressions-patterns.md#trigger-metadata). Tyto vlastnosti lze použít jako součást výrazy vazby v jiných vazeb nebo jako parametry v kódu. Vlastnosti jsou členy [třídy CloudQueueMessage.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|`QueueTrigger`|`string`|Datová část fronty (pokud je platný řetězec). Pokud je datová část zprávy `QueueTrigger` fronty řetězec, má stejnou `name` hodnotu jako proměnná pojmenovaná vlastností v *souboru function.json*.|
|`DequeueCount`|`int`|Počet vyřazení této zprávy do fronty.|
|`ExpirationTime`|`DateTimeOffset`|Čas vypršení platnosti zprávy.|
|`Id`|`string`|ID zprávy fronty.|
|`InsertionTime`|`DateTimeOffset`|Čas, kdy byla zpráva přidána do fronty.|
|`NextVisibleTime`|`DateTimeOffset`|Čas, který bude zpráva příště viditelná.|
|`PopReceipt`|`string`|Účtenka za zprávu.|

## <a name="poison-messages"></a>Otrávené zprávy

Když se nezdaří funkce aktivační události fronty, Azure Functions opakuje funkci až pětkrát pro danou zprávu fronty, včetně prvního pokusu. Pokud všech pět pokusů nezdaří, funkce runtime přidá zprávu do fronty s názvem * &lt;originalqueuename>-poison*. Můžete napsat funkci pro zpracování zpráv z fronty jed jejich protokolováním nebo odesláním oznámení, že je nutná ruční pozornost.

Chcete-li zpracovat poškozená zprávy ručně, zkontrolujte [dequeueCount](#message-metadata) zprávy fronty.

## <a name="polling-algorithm"></a>Algoritmus dotazování

Aktivační událost fronty implementuje algoritmus náhodného exponenciálního back-off, aby se snížil vliv dotazování fronty nečinnosti na náklady na transakce úložiště.

Algoritmus používá následující logiku:

- Při nalezení zprávy hodiná runtime čeká dvě sekundy a pak zkontroluje jinou zprávu
- Pokud není nalezena žádná zpráva, čeká asi čtyři sekundy před pokusem znovu.
- Po následných neúspěšných pokusech o získání zprávy fronty se čekací doba nadále zvyšuje, dokud nedosáhne maximální čekací doby, která je výchozí na jednu minutu.
- Maximální čekací doba je konfigurovatelná prostřednictvím vlastnosti `maxPollingInterval` v [souboru host.json](functions-host-json.md#queues).

Pro místní vývoj maximální interval dotazování výchozí na dvě sekundy.

Pokud jde o fakturaci, čas strávený dotazováním za běhu je "zdarma" a nezapočítává se do vašeho účtu.

## <a name="concurrency"></a>Souběžnost

Pokud čeká více zpráv fronty, aktivační událost fronty načte dávku zpráv a vyvolá instance funkcí současně k jejich zpracování. Ve výchozím nastavení je velikost dávky 16. Když číslo, které jsou zpracovávány, se ztotožní s 8, za běhu získá další dávku a začne tyto zprávy zpracovávat. Maximální počet souběžných zpráv zpracovávaných na funkci na jednom virtuálním počítači (VM) je tedy 24. Toto omezení platí samostatně pro každou funkci aktivovanou frontou na každém virtuálním počítači. Pokud se vaše aplikace funkcí škáluje na více virtuálních počítačích, každý virtuální počítač bude čekat na aktivační události a pokusí se spustit funkce. Pokud se například aplikace funkce škáluje na 3 virtuální chodů, výchozí maximální počet souběžných instancí jedné funkce aktivované frontou je 72.

Velikost dávky a prahová hodnota pro získání nové dávky jsou konfigurovatelné v [souboru host.json](functions-host-json.md#queues). Pokud chcete minimalizovat paralelní spuštění funkcí spouštěných frontou v aplikaci funkce, můžete nastavit velikost dávky na 1. Toto nastavení eliminuje souběžnost pouze tak dlouho, dokud vaše aplikace funkce běží na jednom virtuálním počítači (VM). 

Aktivační událost fronty automaticky zabrání funkci ve vícenásobném zpracování zprávy fronty. nemusí být napsány, aby byly idempotentní.

## <a name="hostjson-properties"></a>vlastnosti host.json

Soubor [host.json](functions-host-json.md#queues) obsahuje nastavení, která řídí chování fronty. Podrobnosti o dostupných nastaveních naleznete v části [nastavení host.json.](functions-bindings-storage-queue-output.md#hostjson-settings)

## <a name="next-steps"></a>Další kroky

- [Zápis zpráv úložiště fronty (výstupní vazba)](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
