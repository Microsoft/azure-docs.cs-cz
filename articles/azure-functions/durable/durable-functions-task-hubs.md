---
title: Centra úloh v Durable Functions – Azure
description: Zjistěte, jaké úlohy centrum je v rozšíření Durable Functions pro službu Azure Functions. Další informace o konfiguraci Konfigurace centra úloh.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: 596eedab39ff926fcdc880c82c49ac464b7ff23b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753461"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Centra úloh v Durable Functions (Azure Functions)

A *centra úloh* v [Durable Functions](durable-functions-overview.md) je logický kontejner prostředků služby Azure Storage, které se používají pro Orchestrace. Funkce nástroje Orchestrator a aktivity pouze komunikovat mezi sebou když patří do stejné centra úloh.

Pokud sdílet více aplikací funkcí účet úložiště, každá aplikace function app *musí* nakonfigurovat pomocí názvu centra samostatné úlohy. Účet úložiště může obsahovat více Center úloh. Následující diagram znázorňuje jedno Centrum úkolů na aplikace function app v účtech úložiště sdílené a vyhrazené.

![Diagram znázorňující sdílené a vyhrazené účty úložiště.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Prostředky Azure Storage

Centra úloh se skládá z následujících prostředků služby storage:

* Jednu či více front ovládacího prvku.
* Jedna fronta pracovní položky.
* Tabulky historie jeden.
* Jedna instance tabulka.
* Jeden kontejner úložiště obsahuje minimálně jeden objekt BLOB zapůjčení.

Všechny tyto prostředky se vytvoří automaticky v účtu Azure Storage výchozí při orchestrator nebo funkce aktivity spustit nebo jsou naplánovány ke spuštění. [Výkon a škálování](durable-functions-perf-and-scale.md) článek vysvětluje, jak se používají tyto prostředky.

## <a name="task-hub-names"></a>Názvy centra úloh

Centra úloh je identifikována názvem, který je deklarován v *host.json* souboru, jak je znázorněno v následujícím příkladu:

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

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

Centra úloh lze také nastavit pomocí nastavení aplikace, jak je znázorněno v následujícím *host.json* ukázkového souboru:

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

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

Název centra úloh se nastaví na hodnotu `MyTaskHub` nastavení aplikace. Následující `local.settings.json` ukazuje, jak definovat `MyTaskHub` nastavení jako `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Tady je předkompilované C# příklad, jak napsat funkci, která používá [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) pro práci s rozbočovači úkolu, který je nakonfigurovaný jako nastavení aplikace:

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

A tady je požadované konfigurace pro jazyk JavaScript. Vlastnosti centra úloh v `function.json` soubor pomocí nastavení aplikace:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Centrum názvy úloh musí začínat písmenem a obsahovat jenom písmena a číslice. Pokud není zadán, výchozí název je **DurableFunctionsHub**.

> [!NOTE]
> Název je jedno Centrum úkolů co odlišuje od jiného existuje více centra úloh v účtu úložiště sdílené. Pokud máte více aplikací funkcí sdílení účet sdílené úložiště, musí explicitně nakonfigurovat jiné názvy jednotlivých úloh centra *host.json* soubory. Více aplikací funkcí v opačném případě se mezi sebou soutěžit o zprávy, které může způsobit nedefinované chování.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak zpracovat správy verzí](durable-functions-versioning.md)
