---
title: Zpracování externích událostí v Durable Functions – Azure
description: Naučte se zpracovávat externí události v rozšíření Durable Functions pro Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: d9c546064589e82cfef367978ebea98c2c202307
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087308"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Zpracování externích událostí v Durable Functions (Azure Functions)

Funkce nástroje Orchestrator mají možnost čekat a naslouchat externím událostem. Tato funkce [Durable Functions](durable-functions-overview.md) je často užitečná pro zpracování lidské interakce nebo jiných externích triggerů.

> [!NOTE]
> Externí události jsou jednosměrné asynchronní operace. Nejsou vhodné pro situace, kdy klient odesílající událost potřebuje synchronní odpověď od funkce Orchestrator.

## <a name="wait-for-events"></a>Počkat na události

Metoda [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) umožňuje, aby funkce Orchestrator asynchronně čekala a naslouchala externí události. Naslouchající funkce Orchestrator deklaruje *název* události a *tvar dat* , která očekává k přijetí.

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

Předchozí příklad naslouchá konkrétní jedné události a provede akci, jakmile bude přijata.

Můžete naslouchat více událostem souběžně, jako v následujícím příkladu, který čeká na jedno ze tří možných oznámení o událostech.

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

Předchozí příklad naslouchá *libovolné* z několika událostí. Je také možné počkat na *všechny* události.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) čeká na nějaké zadání neomezenou dobu.  Aplikace Function App se dá bezpečně uvolnit při čekání. Pokud a když událost dorazí na tuto instanci orchestrace, je automaticky probuzeny a okamžitě zpracuje událost.

> [!NOTE]
> Pokud vaše aplikace Function App používá plán spotřeby, neúčtují se žádné poplatky za to, že funkce Orchestrator očekává úkol od `WaitForExternalEvent` (.NET) nebo `waitForExternalEvent` (JavaScript) bez ohledu na to, jak dlouho čekají.

V rozhraní .NET, pokud nelze datovou část události převést na očekávaný typ `T`, je vyvolána výjimka.

## <a name="send-events"></a>Odesílání událostí

Metoda [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) třídy [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) odesílá události, které `WaitForExternalEvent` (.NET) nebo `waitForExternalEvent` (JavaScript) čeká.  Metoda přijímá jako parametry *EventName* a *eventData.* `RaiseEventAsync` Data události musí být serializovatelný v kódu JSON.

Níže je uvedená příklad funkce aktivované frontou, která odesílá událost schválení do instance funkce Orchestrator. ID instance Orchestration pochází z těla zprávy fronty.

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Interně `RaiseEventAsync` , (.NET) `raiseEvent` nebo (JavaScript) zařadí do fronty zprávu, která se vybrala čekající funkcí Orchestrator. Pokud instance nečeká na zadaný *název události,* zpráva události se přidá do fronty v paměti. Pokud instance orchestrace později začne naslouchat tomuto *názvu události,* zkontroluje ve frontě zprávy o událostech.

> [!NOTE]
> Pokud není k dispozici žádná instance orchestrace se zadaným *ID instance*, zpráva události bude zahozena. Další informace o tomto chování najdete v tématu [problém GitHubu](https://github.com/Azure/azure-functions-durable-extension/issues/29). 

> [!WARNING]
> Při vývoji místně v JavaScriptu budete muset nastavit proměnnou `WEBSITE_HOSTNAME` prostředí na `localhost:<port>`, ex. `localhost:7071`pro použití metod v `DurableOrchestrationClient`. Další informace o tomto požadavku najdete v tématu [problém GitHubu](https://github.com/Azure/azure-functions-durable-js/issues/28).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přečtěte si, jak nastavit orchestrace externí.](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Spustit ukázku, která čeká na externí události](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Spustit ukázku, která čeká na lidskou interakci](durable-functions-phone-verification.md)