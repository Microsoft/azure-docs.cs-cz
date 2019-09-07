---
title: Centra úloh v Durable Functions – Azure
description: Zjistěte, co je centrum úloh v rozšíření Durable Functions pro Azure Functions. Přečtěte si, jak nakonfigurovat konfiguraci Center úloh.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: b0a58251530467d788710b0584b15715a207e20f
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734328"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Centra úloh v Durable Functions (Azure Functions)

*Centrum úloh* v [Durable Functions](durable-functions-overview.md) je logický kontejner pro Azure Storage prostředky, které se používají pro orchestrace. Funkce Orchestrator a Activity můžou vzájemně fungovat, když patří do stejného centra úloh.

Pokud účet úložiště sdílí víc aplikací funkcí, *musí* být každá aplikace Function App nakonfigurovaná s názvem samostatného centra úloh. Účet úložiště může obsahovat několik Center úloh. Následující diagram znázorňuje jedno centrum úloh na aplikaci Function App ve sdílených a vyhrazených účtech úložiště.

![Diagram znázorňující sdílené a vyhrazené účty úložiště](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Prostředky Azure Storage

Centrum úloh se skládá z následujících prostředků úložiště:

* Jedna nebo více front ovládacích prvků.
* Jedna fronta pracovních položek.
* Jedna tabulka historie.
* Jedna instance tabulky
* Jeden kontejner úložiště obsahující jeden nebo více objektů BLOB zapůjčení

Všechny tyto prostředky se vytvoří automaticky ve výchozím účtu Azure Storage, když se spustí funkce Orchestrator nebo aktivita, nebo se naplánuje jejich spuštění. Článek o [výkonu a škálování](durable-functions-perf-and-scale.md) vysvětluje, jak se tyto prostředky používají.

## <a name="task-hub-names"></a>Názvy centra úloh

Centra úloh jsou identifikována názvem, který je deklarován v souboru *Host. JSON* , jak je znázorněno v následujícím příkladu:

### <a name="hostjson-functions-1x"></a>Host. JSON (funkce 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (Functions 2.x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

Centra úloh je také možné konfigurovat pomocí nastavení aplikace, jak je znázorněno v následujícím souboru jako *Host. JSON* :

### <a name="hostjson-functions-1x"></a>Host. JSON (funkce 1. x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (Functions 2.x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

Název centra úloh se nastaví na hodnotu `MyTaskHub` nastavení aplikace. Následující `local.settings.json` příklad ukazuje, jak `MyTaskHub` definovat nastavení jako `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Tady je předkompilovaný C# příklad, jak napsat funkci, která používá [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) k práci s centrem úloh, které je nakonfigurované jako nastavení aplikace:

### <a name="c"></a>C#

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

### <a name="javascript"></a>JavaScript

Vlastnost Centrum úkolů v `function.json` souboru se nastavuje prostřednictvím nastavení aplikace:
```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Názvy centra úloh musí začínat písmenem a obsahovat jenom písmena a číslice. Pokud není zadán, výchozí název je **DurableFunctionsHub**.

> [!NOTE]
> Název se odlišuje od jednoho centra úkolů v případě, že je ve sdíleném účtu úložiště víc Center úkolů. Pokud máte více aplikací Function App sdílejících sdílený účet úložiště, je nutné explicitně nakonfigurovat různé názvy pro každé centrum úloh v souborech *Host. JSON* . V opačném případě budou aplikace s více aplikacemi navzájem konkurovat pro zprávy, což by mohlo vést k nedefinovanému chování.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Informace o tom, jak zpracovávat správu verzí](durable-functions-versioning.md)
