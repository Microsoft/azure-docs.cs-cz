---
title: Zpracování externího událostí v Durable Functions – Azure
description: Zjistěte, jak zpracovat externí akce, které v rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf
ms.openlocfilehash: 98380cc5b9daff314283ac4e45e5edf7b5601e1b
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49983942"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Zpracování externího událostí v Durable Functions (Azure Functions)

Funkce nástroje Orchestrator mají možnost počkat a naslouchat událostem externí. Tato funkce [Durable Functions](durable-functions-overview.md) je často užitečné pro zpracování zásahem ze strany nebo jiné externí aktivační události.

## <a name="wait-for-events"></a>Čekání na události

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) metoda umožňuje funkce orchestrátoru asynchronně čekat a naslouchat vnější události. Deklaruje naslouchání funkce orchestrátoru *název* události a *tvar dat.* očekává příjem.

#### <a name="c"></a>C#

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

#### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

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

V předchozím příkladu naslouchá určité jedna událost a provede akci při obdržení.

Může naslouchat více událostí současně, jako v následujícím příkladu, který čeká na jednu ze tří oznámení událostí.

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

#### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

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

Čeká na předchozí příklad *jakékoli* více událostí. Je také možné počkat *všechny* události.

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

#### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

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

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) po neomezenou dobu čeká na vstup.  Aplikace function app může být bezpečně uvolněna při čekání. Pokud události dorazí pro tuto instanci Orchestrace, se probudí, automaticky a okamžitě zpracovává událost.

> [!NOTE]
> Pokud vaše aplikace function app používá, je plán Consumption, žádné poplatky se účtují, když funkce orchestrátoru je čekání na úlohu z `WaitForExternalEvent`, bez ohledu na to, jak dlouho čekat.

V rozhraní .NET, pokud se datová část události nejde převést na očekávaný typ. `T`, je vyvolána výjimka.

## <a name="send-events"></a>Odesílání událostí

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metodu [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) třídy odesílá události, která `WaitForExternalEvent` čeká.  `RaiseEventAsync` Přijímá metodu *eventName* a *eventData* jako parametry. Data události musí být serializovatelný JSON.

Níže je příklad fronty aktivuje funkce, která odesílá událost "Schválení" na instanci funkce nástroje orchestrator. ID instance Orchestrace pochází z těla zprávy fronty.

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)
V jazyce JavaScript musíme volání rest api k aktivaci událost, pro který se čeká odolné funkce.
Následující kód používá balíček "žádost". Metoda níže lze použít pro vyvolání žádné události pro všechny instance trvalý – funkce

```js
function raiseEvent(instanceId, eventName) {
        var url = `<<BASE_URL>>/runtime/webhooks/durabletask/instances/${instanceId}/raiseEvent/${eventName}?taskHub=DurableFunctionsHub`;
        var body = <<BODY>>
            
        return new Promise((resolve, reject) => {
            request({
                url,
                json: body,
                method: "POST"
            }, (e, response) => {
                if (e) {
                    return reject(e);
                }

                resolve();
            })
        });
    }
```

<< BASE_URL >> bude základní adresu url vaše aplikace function app. Pokud používáte kód místně a pak ji bude vypadat podobně jako http://localhost:7071 nebo v Azure jako https://<<functionappname>>. azurewebsites.net


Interně `RaiseEventAsync` zařadí zprávu, která získá vyzvednou funkce orchestrátoru čekání.

> [!WARNING]
> Pokud neexistuje žádná instance orchestration se zadaným *instance ID* nebo pokud není instance čeká na zadaný *název události*, zpráva o události se zahodí. Další informace o tomto chování najdete v tématu [problém Githubu](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak nastavit externí Orchestrace](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Spuštění ukázky, která čeká na vnější události](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Spuštění ukázky, která čeká na lidské interakce](durable-functions-phone-verification.md)

