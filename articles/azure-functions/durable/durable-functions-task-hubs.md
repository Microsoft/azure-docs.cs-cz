---
title: Centra úloh v Durable Functions – Azure
description: Zjistěte, co je centrum úloh v rozšíření Durable Functions pro Azure Functions. Naučte se konfigurovat centra úloh.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 26234039c77601bc1d29beeebd3fcb8461d6d6c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009513"
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
* Kontejner úložiště obsahující velkou datovou část zprávy (je-li k dispozici).

Všechny tyto prostředky se vytvoří automaticky ve výchozím účtu Azure Storage, když se spustí funkce Orchestrator, entita nebo aktivita, nebo se naplánuje jejich spuštění. Článek o [výkonu a škálování](durable-functions-perf-and-scale.md) vysvětluje, jak se tyto prostředky používají.

## <a name="task-hub-names"></a>Názvy centra úloh

Centra úloh se identifikují podle názvu, který odpovídá těmto pravidlům:

* Obsahuje jenom alfanumerické znaky.
* Začíná písmenem
* Má minimální délku 3 znaky, maximální délka je 45 znaků.

Název centra úloh je deklarován v *host.jsv* souboru, jak je znázorněno v následujícím příkladu:

### <a name="hostjson-functions-20"></a>host.jszapnuto (Functions 2,0)

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

### <a name="hostjson-functions-1x"></a>host.jszapnuto (Functions 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Centra úloh je také možné konfigurovat pomocí nastavení aplikace, jak je znázorněno v následujícím `host.json` ukázkovém souboru:

### <a name="hostjson-functions-10"></a>host.jszapnuto (Functions 1,0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.jszapnuto (Functions 2,0)

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

Název centra úloh se nastaví na hodnotu `MyTaskHub` nastavení aplikace. Následující `local.settings.json` příklad ukazuje, jak definovat `MyTaskHub` nastavení jako `samplehubname` :

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Následující kód ukazuje, jak napsat funkci, která používá [vazbu klienta Orchestration](durable-functions-bindings.md#orchestration-client) pro práci s centrem úloh, které je nakonfigurováno jako nastavení aplikace:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    object eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> Předchozí příklad v jazyce C# je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Vlastnost Centrum úkolů v `function.json` souboru se nastavuje prostřednictvím nastavení aplikace:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

Vlastnost Centrum úkolů v `function.json` souboru se nastavuje prostřednictvím nastavení aplikace:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Názvy centra úloh musí začínat písmenem a obsahovat jenom písmena a číslice. Pokud tento parametr nezadáte, použije se výchozí název centra úloh, jak je znázorněno v následující tabulce:

| Trvalá verze rozšíření | Výchozí název centra úloh |
| - | - |
| 2.x | Při nasazení v Azure je název centra úloh odvozený od názvu _aplikace Function App_. Při spuštění mimo Azure je výchozí název centra úloh `TestHubName` . |
| verze | Výchozí název centra úloh pro všechna prostředí jsou `DurableFunctionsHub` . |

Další informace o rozdílech mezi verzemi rozšíření naleznete v článku o [Durable Functions verzích](durable-functions-versions.md) .

> [!NOTE]
> Název se odlišuje od jednoho centra úkolů v případě, že je ve sdíleném účtu úložiště víc Center úkolů. Pokud máte více aplikací Function App sdílejících sdílený účet úložiště, musíte pro každé centrum úloh v *host.js* pro soubory explicitně nakonfigurovat jiné názvy. V opačném případě budou aplikace s více aplikacemi vzájemně soutěžit na zprávy, což by mohlo vést k nedefinovanému chování, včetně orchestrace neočekávaného "zablokování" ve `Pending` `Running` stavu nebo.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se zvládnout správu verzí orchestrace.](durable-functions-versioning.md)
