---
title: Aktivační událost služby Azure Queue Storage pro Azure Functions
description: Naučte se spouštět službu Azure Functions, protože se mění data služby Azure Queue Storage.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 59cedb25295770ba4ae4a33aac3287c5fed1297d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381490"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Aktivační událost služby Azure Queue Storage pro Azure Functions

Aktivační událost Queue Storage spustí funkci při přidání zpráv do úložiště Azure Queue Storage.

## <a name="encoding"></a>Encoding

Funkce očekávají řetězec kódovaný v *kódování Base64* . Jakékoli úpravy typu kódování (aby bylo možné připravit data jako řetězec kódovaný v *kódování Base64* ), je nutné implementovat do volající služby.

## <a name="example"></a>Příklad

Pomocí triggeru Queue spustíte funkci při přijetí nové položky ve frontě. Jako vstup funkce se poskytuje zpráva fronty.

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která se dotazuje do `myqueue-items` fronty a zapisuje protokol pokaždé, když je zpracována položka fronty.

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

Následující příklad ukazuje vazbu triggeru fronty v *function.js* kódu souboru a [skriptu jazyka C# (. csx)](functions-reference-csharp.md) , který používá vazbu. Funkce dotazuje `myqueue-items` frontu a zapisuje protokol pokaždé, když je zpracována položka fronty.

Tady je *function.js* souboru:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

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

Část [použití](#usage) vysvětluje `myQueueItem` , která je pojmenována `name` vlastností v function.jsna.  [Část metadata zprávy](#message-metadata) vysvětluje všechny zobrazené proměnné.

# <a name="java"></a>[Java](#tab/java)

Následující příklad Java ukazuje funkci triggeru fronty úložiště, která zaznamená aktivační zprávu umístěnou do fronty `myqueuename` .

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vazbu triggeru fronty v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce dotazuje `myqueue-items` frontu a zapisuje protokol pokaždé, když je zpracována položka fronty.

Tady je *function.js* souboru:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

> [!NOTE]
> Parametr Name se odráží jako `context.bindings.<name>` v kódu JavaScriptu, který obsahuje datovou část položky fronty. Tato datová část je také předána jako druhý parametr funkce.

Tady je kód JavaScriptu:

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

Část [použití](#usage) vysvětluje `myQueueItem` , která je pojmenována `name` vlastností v function.jsna.  [Část metadata zprávy](#message-metadata) vysvětluje všechny zobrazené proměnné.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Následující příklad ukazuje, jak číst zprávu fronty předanou funkci prostřednictvím triggeru.

Aktivační událost fronty úložiště je definována v *function.jsv* souboru, kde `type` je nastavena na `queueTrigger` .

```json
{
  "bindings": [
    {
      "name": "QueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Kód v souboru *Run.ps1* deklaruje parametr jako `$QueueItem` , který umožňuje čtení zprávy fronty ve funkci.

```powershell
# Input bindings are passed in via param block.
param([string] $QueueItem, $TriggerMetadata)

# Write out the queue message and metadata to the information log.
Write-Host "PowerShell queue trigger function processed work item: $QueueItem"
Write-Host "Queue item expiration time: $($TriggerMetadata.ExpirationTime)"
Write-Host "Queue item insertion time: $($TriggerMetadata.InsertionTime)"
Write-Host "Queue item next visible time: $($TriggerMetadata.NextVisibleTime)"
Write-Host "ID: $($TriggerMetadata.Id)"
Write-Host "Pop receipt: $($TriggerMetadata.PopReceipt)"
Write-Host "Dequeue count: $($TriggerMetadata.DequeueCount)"
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje, jak číst zprávu fronty předanou funkci prostřednictvím triggeru.

Aktivační událost fronty úložiště je definována v *function.jsna* místě, kde je *typ* nastaven na `queueTrigger` .

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

Kód *_\_ init_ \_ . py* deklaruje parametr jako `func.QueueMessage` , který umožňuje čtení zprávy fronty ve funkci.

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

 ---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd C#](functions-dotnet-class-library.md)pomocí následujících atributů nakonfigurujte Trigger fronty:

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

  Vlastnost můžete nastavit `Connection` tak, aby určovala nastavení aplikace, které obsahuje připojovací řetězec účtu úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Úplný příklad naleznete v tématu [příklad](#example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje jiný způsob určení účtu úložiště, který se má použít. Konstruktor převezme název nastavení aplikace, které obsahuje připojovací řetězec úložiště. Atribut lze použít na úrovni parametru, metody nebo třídy. Následující příklad ukazuje úroveň třídy a úroveň metody:

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

* `QueueTrigger` `Connection` Vlastnost atributu
* `StorageAccount`Atribut aplikovaný na stejný parametr jako `QueueTrigger` atribut.
* `StorageAccount`Atribut aplikovaný na funkci.
* `StorageAccount`Atribut aplikovaný na třídu.
* Nastavení aplikace "AzureWebJobsStorage".

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="java"></a>[Java](#tab/java)

`QueueTrigger`Poznámka vám poskytne přístup ke frontě, která aktivuje funkci. V následujícím příkladu je k dispozici zpráva fronty pro funkci prostřednictvím `message` parametru.

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
|`name`       | Deklaruje název parametru v signatuře funkce. Když je funkce aktivována, hodnota tohoto parametru má obsah zprávy ve frontě. |
|`queueName`  | Deklaruje název fronty v účtu úložiště. |
|`connection` | Odkazuje na připojovací řetězec účtu úložiště. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell nepodporuje atributy.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `QueueTrigger` atribut.

|function.jsvlastnost | Vlastnost atributu |Description|
|---------|---------|----------------------|
|**textový** | Není k dispozici| Musí být nastaven na hodnotu `queueTrigger` . Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal.|
|**směr**| Není k dispozici | V *function.jspouze v* souboru. Musí být nastaven na hodnotu `in` . Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal. |
|**Jméno** | Není k dispozici |Název proměnné, která obsahuje datovou část položky fronty v kódu funkce.  |
|**Proměnné QueueName** | **Proměnné QueueName**| Název fronty, která se má dotazovat. |
|**vázán** | **Připojení** |Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu.<br><br>Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "MyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage` .<br><br>Pokud používáte [verzi 5. x nebo vyšší z rozšíření](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)místo připojovacího řetězce, můžete zadat odkaz na oddíl konfigurace, který definuje připojení. Viz [připojení](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Výchozí

Přístup k datům zprávy pomocí parametru metody, jako je například `string paramName` . Můžete vytvořit propojení s některým z následujících typů:

* Object – modul runtime funkcí deserializace datovou část JSON do instance libovolné třídy definované ve vašem kódu.
* `string`
* `byte[]`
* [CloudQueueMessage]

Pokud se pokusíte vytvořit navázání `CloudQueueMessage` a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK úložiště](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

### <a name="additional-types"></a>Další typy

Aplikace používající [5.0.0 nebo vyšší verze rozšíření úložiště](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) můžou používat taky typy ze [sady Azure SDK pro .NET](/dotnet/api/overview/azure/storage.queues-readme). Tato verze vyřazuje podporu pro starší `CloudQueueMessage` typ, a to ve prospěch následujících typů:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
 
Příklady použití těchto typů najdete v [úložišti GitHub pro rozšíření](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

### <a name="default"></a>Výchozí

Přístup k datům zprávy pomocí parametru metody, jako je například `string paramName` . `paramName`Je hodnota zadaná ve `name` vlastnosti *function.jsv*. Můžete vytvořit propojení s některým z následujících typů:

* Object – modul runtime funkcí deserializace datovou část JSON do instance libovolné třídy definované ve vašem kódu. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Pokud se pokusíte vytvořit navázání `CloudQueueMessage` a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK úložiště](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

### <a name="additional-types"></a>Další typy

Aplikace používající [5.0.0 nebo vyšší verze rozšíření úložiště](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) můžou používat taky typy ze [sady Azure SDK pro .NET](/dotnet/api/overview/azure/storage.queues-readme). Tato verze vyřazuje podporu pro starší `CloudQueueMessage` typ, a to ve prospěch následujících typů:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)

Příklady použití těchto typů najdete v [úložišti GitHub pro rozšíření](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="java"></a>[Java](#tab/java)

[QueueTrigger](/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable&preserve-view=true) anotace poskytuje přístup ke zprávě fronty, která funkci aktivovala.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Datová část položky fronty je k dispozici prostřednictvím `context.bindings.<NAME>` , kde se `<NAME>` shoduje s názvem definovaným v *function.js*. Pokud je datová část JSON, hodnota je deserializována do objektu.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Přístup ke zprávě fronty prostřednictvím řetězcového parametru, který odpovídá názvu určenému `name` parametrem vazby v *function.jsv* souboru.

# <a name="python"></a>[Python](#tab/python)

Přístup ke zprávě fronty prostřednictvím parametru zadaného jako [QueueMessage](/python/api/azure-functions/azure.functions.queuemessage?view=azure-python&preserve-view=true).

---

## <a name="message-metadata"></a>Metadata zprávy

Aktivační událost fronty poskytuje několik [vlastností metadat](./functions-bindings-expressions-patterns.md#trigger-metadata). Tyto vlastnosti lze použít jako součást výrazů vazby v jiných vazbách nebo jako parametry v kódu. Vlastnosti jsou členy třídy [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) .

|Vlastnost|Typ|Description|
|--------|----|-----------|
|`QueueTrigger`|`string`|Datová část fronty (Pokud platný řetězec). Pokud je datová část zprávy fronty řetězec, `QueueTrigger` má stejnou hodnotu jako proměnná s názvem `name` vlastností v *function.js*.|
|`DequeueCount`|`int`|Počet, kolikrát byla tato zpráva odstraněna z fronty.|
|`ExpirationTime`|`DateTimeOffset`|Čas vypršení platnosti zprávy|
|`Id`|`string`|ID zprávy fronty|
|`InsertionTime`|`DateTimeOffset`|Čas přidání zprávy do fronty.|
|`NextVisibleTime`|`DateTimeOffset`|Čas, kdy bude zpráva zobrazena dál.|
|`PopReceipt`|`string`|Účtenka pro zprávu pop.|

## <a name="poison-messages"></a>Poškozené zprávy

Pokud se funkce triggeru fronty nezdařila, Azure Functions se znovu pokusí použít tuto funkci až pětkrát pro danou zprávu fronty, včetně prvního pokusu. Pokud se všechny pět pokusů nezdaří, modul runtime Functions přidá zprávu do fronty s názvem *&lt; originalqueuename>-jed*. Můžete napsat funkci pro zpracování zpráv z fronty poškození tím, že je přihlásíte nebo posíláte oznámení, že je potřeba ruční pozornost.

Pokud chcete zpracovat poškozené zprávy ručně, podívejte se do [dequeueCount](#message-metadata) zprávy ve frontě.

## <a name="polling-algorithm"></a>Algoritmus cyklického dotazování

Aktivační událost fronty implementuje náhodný exponenciální algoritmus pro snížení účinku nečinnosti při cyklickém dotazování na poplatky za transakce úložiště.

Algoritmus používá následující logiku:

- Když se najde zpráva, modul runtime počká dvě sekundy a pak zkontroluje další zprávu.
- Pokud se nenajde žádná zpráva, před opakováním počkejte přibližně čtyři sekundy.
- Po následném neúspěšném pokusu o získání zprávy fronty se doba čekání zvětšuje, dokud nedosáhne maximální čekací doby, která je ve výchozím nastavení jedna minuta.
- Maximální čekací dobu lze konfigurovat prostřednictvím `maxPollingInterval` vlastnosti v [host.jsv souboru](functions-host-json-v1.md#queues).

Pro místní vývoj je maximální interval cyklického dotazování ve výchozím nastavení na dvě sekundy.

V souvislosti s fakturací je čas strávený při cyklickém dotazování modulem runtime "Free" a nepočítá se s vaším účtem.

## <a name="concurrency"></a>Souběžnost

Pokud se čeká více zpráv ve frontách, aktivační událost fronty načte dávku zpráv a současně vyvolá instance funkcí, aby je mohla zpracovat. Ve výchozím nastavení je velikost dávky 16. Když se zpracovávané číslo dostane do 8, modul runtime získá další dávku a začne tyto zprávy zpracovávat. Takže maximální počet souběžných zpráv zpracovávaných na jednu funkci na jednom virtuálním počítači (VM) je 24. Toto omezení platí samostatně u každé funkce aktivované frontou na každém virtuálním počítači. Pokud vaše aplikace Functions škáluje na více virtuálních počítačů, bude každý virtuální počítač čekat na triggery a pokusí se spustit funkce. Například pokud se aplikace funkcí škáluje na 3 virtuální počítače, výchozí maximální počet souběžných instancí jedné funkce aktivované frontou je 72.

Velikost dávky a prahová hodnota pro získání nové dávky lze konfigurovat v [host.jsv souboru](functions-host-json.md#queues). Pokud chcete minimalizovat paralelní spouštění funkcí aktivovaných frontou ve Function App, můžete nastavit velikost dávky na 1. Toto nastavení eliminuje souběžnost, pokud vaše aplikace Function App běží na jednom virtuálním počítači (VM). 

Aktivační událost fronty automaticky zabrání funkci ve zpracování zprávy fronty vícekrát.

## <a name="hostjson-properties"></a>host.jsvlastností

[host.jsv](functions-host-json.md#queues) souboru obsahuje nastavení, která řídí chování fronty při spouštění. Podrobnosti o dostupných nastaveních najdete v části [host.jsv nastavení](functions-bindings-storage-queue.md#hostjson-settings) .

## <a name="next-steps"></a>Další kroky

- [Zápis zpráv do fronty úložiště (výstupní vazba)](./functions-bindings-storage-queue-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
