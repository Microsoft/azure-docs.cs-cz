---
title: Centra úloh v odolných funkcích – Azure
description: Zjistěte, co je centrum úloh v rozšíření Durable Functions pro funkce Azure. Přečtěte si, jak nakonfigurovat centra úloh.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 427ab6c4e0e769ab881af0af3023d514c1b092c6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604617"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Centra úloh v odolných funkcích (funkce Azure)

*Centrum úloh* v oblasti [Trvalé funkce](durable-functions-overview.md) je logický kontejner pro prostředky Azure Storage, které se používají pro orchestrations. Funkce Orchestrator a aktivity mohou vzájemně komunikovat pouze v případě, že patří do stejného centra úloh.

Pokud více funkčních aplikací sdílí účet úložiště, *musí* být každá aplikace funkce nakonfigurována se samostatným názvem centra úloh. Účet úložiště může obsahovat více rozbočovačů úloh. Následující diagram znázorňuje jedno centrum úloh na aplikaci funkce ve sdílených a vyhrazených účtech úložiště.

![Diagram znázorňující sdílené a vyhrazené účty úložiště.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Prostředky Azure Storage

Centrum úloh se skládá z následujících prostředků úložiště:

* Jedna nebo více kontrolních front.
* Jedna fronta pracovních položek.
* Jeden historický stůl.
* Tabulka jednu instancí.
* Jeden kontejner úložiště obsahující jeden nebo více objektů BLOB zapůjčení.
* Kontejner úložiště obsahující velké datové části zpráv, pokud je to možné.

Všechny tyto prostředky se vytvoří automaticky ve výchozím účtu Úložiště Azure, když se spustí nebo se mají spustit funkce orchestrátoru, entity nebo aktivity. [Výkon a škálování](durable-functions-perf-and-scale.md) článek vysvětluje, jak se používají tyto prostředky.

## <a name="task-hub-names"></a>Názvy rozbočovačů úloh

Centra úloh jsou označena názvem, který odpovídá těmto pravidlům:

* Obsahuje pouze alfanumerické znaky.
* Začíná písmenem
* Má minimální délku 3 znaky, maximální délku 45 znaků.

Název centra úloh je deklarován v souboru *host.json,* jak je znázorněno v následujícím příkladu:

### <a name="hostjson-functions-20"></a>host.json (Funkce 2.0)

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

### <a name="hostjson-functions-1x"></a>host.json (Funkce 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Centra úloh lze také nakonfigurovat pomocí nastavení aplikace, jak je znázorněno v následujícím `host.json` ukázkovém souboru:

### <a name="hostjson-functions-10"></a>host.json (Funkce 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Funkce 2.0)

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

Název centra úloh bude nastaven na `MyTaskHub` hodnotu nastavení aplikace. Následující `local.settings.json` ukazuje, jak definovat `MyTaskHub` nastavení `samplehubname`jako :

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Následující kód ukazuje, jak napsat funkci, která používá [vazbu klienta orchestrace](durable-functions-bindings.md#orchestration-client) pro práci s centrem úloh, které je nakonfigurováno jako nastavení aplikace:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
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

> [!NOTE]
> Předchozí příklad Jazyka C# je pro trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Vlastnost centra úloh `function.json` v souboru je nastavena pomocí nastavení aplikace:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Názvy rozbočovačů úloh musí začínat písmenem a skládat pouze z písmen a číslic. Pokud není zadán, bude použit výchozí název rozbočovače úloh, jak je znázorněno v následující tabulce:

| Odolná verze rozšíření | Výchozí název centra úloh |
| - | - |
| 2.x | Při nasazení v Azure se název centra úloh odvozuje z názvu _aplikace funkce_. Když běží mimo Azure, výchozí název `TestHubName`centra úloh je . |
| 1.x | Výchozí název centra úloh pro `DurableFunctionsHub`všechna prostředí je . |

Další informace o rozdílech mezi verzemi rozšíření naleznete v článku [verze durable functions.](durable-functions-versions.md)

> [!NOTE]
> Název je to, co odlišuje jeden rozbočovač úloh od druhého, když existuje více center úloh v účtu sdíleného úložiště. Pokud máte více aplikací funkcí, které sdílejí účet sdíleného úložiště, musíte explicitně nakonfigurovat různé názvy pro každé centrum úloh v souborech *host.json.* V opačném případě více funkcí aplikace bude soutěžit mezi sebou pro zprávy, což by mohlo mít `Pending` `Running` za následek nedefinované chování, včetně orchestrace získání neočekávaně "zaseknutý" v nebo stavu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se zpracovat správu verzí orchestrace](durable-functions-versioning.md)
