---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 4db460a5dcefb49de3ad2b594d3957cbcf7445c3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592797"
---
V projektu C# knihovny tříd jsou vazby definovány jako atributy vazby v metodě Function. Soubor *Function. JSON* se pak automaticky generuje na základě těchto atributů.

Otevřete soubor projektu *HttpTrigger.cs* a přidejte následující `using` příkaz:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Do `Run` definice metody přidejte následující parametr:

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

`msg` Parametr`ICollector<T>` je typ, který představuje kolekci zpráv zapsaných do výstupní vazby po dokončení funkce. V tomto případě je výstupem fronta úložiště s názvem `outqueue`. Připojovací řetězec pro účet úložiště je nastaven pomocí `StorageAccountAttribute`. Tento atribut označuje nastavení, které obsahuje připojovací řetězec účtu úložiště a dá se použít na úrovni třídy, metody nebo parametru. V tomto případě můžete vynechat `StorageAccountAttribute` , protože už používáte výchozí účet úložiště.

Definice metody spuštění by teď měla vypadat takto:  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```
