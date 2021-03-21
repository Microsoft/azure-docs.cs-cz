---
title: Zpracování externích událostí v Durable Functions – Azure
description: Naučte se zpracovávat externí události v rozšíření Durable Functions pro Azure Functions.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: c08306edcea02a9207ab5a15eb62b7fffc2ecb44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576325"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Zpracování externích událostí v Durable Functions (Azure Functions)

Funkce nástroje Orchestrator mají možnost čekat a naslouchat externím událostem. Tato funkce [Durable Functions](durable-functions-overview.md) je často užitečná pro zpracování lidské interakce nebo jiných externích triggerů.

> [!NOTE]
> Externí události jsou jednosměrné asynchronní operace. Nejsou vhodné pro situace, kdy klient odesílající událost potřebuje synchronní odpověď od funkce Orchestrator.

## <a name="wait-for-events"></a>Počkat na události

Metody [WaitForExternalEvent](/dotnet/api/microsoft.azure.webjobs.durableorchestrationcontextbase.waitforexternalevent?view=azure-dotnet-legacy) (.NET), `waitForExternalEvent` (JavaScript) a `wait_for_external_event` (Python) [vazby triggeru Orchestration](durable-functions-bindings.md#orchestration-trigger) umožňují, aby funkce Orchestrator asynchronně čekala a naslouchala externí události. Naslouchající funkce Orchestrator deklaruje *název* události a *tvar dat* , která očekává k přijetí.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> Předchozí kód jazyka C# je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    approved = context.wait_for_external_event('Approval')
    if approved:
        # approval granted - do the approved action
    else:
        # approval denied - send a notification

main = df.Orchestrator.create(orchestrator_function)
```

---

Předchozí příklad naslouchá konkrétní jedné události a provede akci, jakmile bude přijata.

Můžete naslouchat více událostem souběžně, jako v následujícím příkladu, který čeká na jedno ze tří možných oznámení o událostech.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> Předchozí kód jazyka C# je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    event1 = context.wait_for_external_event('Event1')
    event2 = context.wait_for_external_event('Event2')
    event3 = context.wait_for_external_event('Event3')

    winner = context.task_any([event1, event2, event3])
    if winner == event1:
        # ...
    elif winner == event2:
        # ...
    elif winner == event3:
        # ...

main = df.Orchestrator.create(orchestrator_function)
```

---

Předchozí příklad naslouchá *libovolné* z několika událostí. Je také možné počkat na *všechny* události.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> Předchozí kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

V rozhraní .NET, pokud nelze datovou část události převést na očekávaný typ `T` , je vyvolána výjimka.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    application_id = context.get_input()
    
    gate1 = context.wait_for_external_event('CityPlanningApproval')
    gate2 = context.wait_for_external_event('FireDeptApproval')
    gate3 = context.wait_for_external_event('BuildingDeptApproval')

    yield context.task_all([gate1, gate2, gate3])
    yield context.call_activity('IssueBuildingPermit', application_id)

main = df.Orchestrator.create(orchestrator_function)
```

---

`WaitForExternalEvent` pro určitý vstup počká na neomezenou dobu.  Aplikace Function App se dá bezpečně uvolnit při čekání. Pokud a když událost dorazí na tuto instanci orchestrace, je automaticky probuzeny a okamžitě zpracuje událost.

> [!NOTE]
> Pokud vaše aplikace Function App používá plán spotřeby, neúčtují se žádné poplatky za to, že funkce Orchestrator očekává úkol od `WaitForExternalEvent` (.NET), `waitForExternalEvent` (JavaScript) nebo `wait_for_external_event` (Python) bez ohledu na to, jak dlouho čeká.

## <a name="send-events"></a>Odesílání událostí

Můžete použít metody [RaiseEventAsync](/dotnet/api/microsoft.azure.webjobs.durableorchestrationclientbase.raiseeventasync?view=azure-dotnet-legacy) (.NET) nebo `raiseEventAsync` (JavaScript) k odeslání externí události do orchestrace. Tyto metody jsou zpřístupněny prostřednictvím vazby [klienta Orchestration](durable-functions-bindings.md#orchestration-client) . K odeslání externí události do orchestrace můžete také použít integrované [rozhraní HTTP API události vyvolání](durable-functions-http-api.md#raise-event) .

Vyvolaná událost zahrnuje *ID instance*, *EventName* a *eventData* jako parametry. Funkce Orchestrator zpracovává tyto události pomocí `WaitForExternalEvent` rozhraní API (.NET) nebo `waitForExternalEvent` (JavaScript). Aby bylo možné zpracovat událost, je nutné, aby se *EventName* shodovala na koncích odesílání i přijímání. Data události musí být také JSON-serializovatelný.

Interně, mechanismy vyvolání události zařadí do fronty zprávu, která se vybrala čekající funkcí Orchestrator. Pokud instance nečeká na zadaný *název události,* zpráva události se přidá do fronty v paměti. Pokud instance orchestrace později začne naslouchat tomuto *názvu události,* zkontroluje ve frontě zprávy o událostech.

> [!NOTE]
> Pokud není k dispozici žádná instance orchestrace se zadaným *ID instance*, zpráva události bude zahozena.

Níže je uvedená příklad funkce aktivované frontou, která odesílá událost schválení do instance funkce Orchestrator. ID instance Orchestration pochází z těla zprávy fronty.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> Předchozí kód jazyka C# je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `OrchestrationClient` atribut namísto `DurableClient` atributu a místo toho musíte použít `DurableOrchestrationClient` typ parametru `IDurableOrchestrationClient` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(instance_id:str, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    await client.raise_event(instance_id, 'Approval', True)
```

---

Interně, `RaiseEventAsync` (.NET), `raiseEvent` (JavaScript), nebo `raise_event` (Python) zařadí do fronty zprávu, která se vybrala čekající funkcí Orchestrator. Pokud instance nečeká na zadaný *název události,* zpráva události se přidá do fronty v paměti. Pokud instance orchestrace později začne naslouchat tomuto *názvu události,* zkontroluje ve frontě zprávy o událostech.

> [!NOTE]
> Pokud není k dispozici žádná instance orchestrace se zadaným *ID instance*, zpráva události bude zahozena.

### <a name="http"></a>HTTP

Následuje příklad požadavku HTTP, který vyvolává událost schválení instance orchestrace. 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

V tomto případě je ID instance pevně zakódované jako *MyInstanceId*.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se implementovat zpracování chyb.](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Spustit ukázku, která čeká na lidskou interakci](durable-functions-phone-verification.md)