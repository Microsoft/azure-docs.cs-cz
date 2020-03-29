---
title: Zpracování externích událostí v trvanlivých funkcích – Azure
description: Zjistěte, jak zpracovat externí události v rozšíření Trvalé funkce pro funkce Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0877161f8d668141c8efb7c06b10643bf209341f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262958"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Zpracování externích událostí v trvalých funkcích (Funkce Azure)

Funkce Orchestrator mají schopnost čekat a poslouchat externí události. Tato funkce [trvanlivé funkce](durable-functions-overview.md) je často užitečné pro manipulaci s lidskou interakci nebo jiné externí aktivační události.

> [!NOTE]
> Externí události jsou jednosměrné asynchronní operace. Nejsou vhodné pro situace, kdy klient odesílající událost potřebuje synchronní odpověď z funkce orchestrator.

## <a name="wait-for-events"></a>Čekání na události

Metody `WaitForExternalEvent` (.NET) `waitForExternalEvent` a (JavaScript) [orchestrace trigger vazby](durable-functions-bindings.md#orchestration-trigger) umožňuje funkci orchestrator asynchronně čekat a poslouchat externí událost. Funkce orchestrátoru naslouchání deklaruje *název* události a *tvar dat,* která očekává, že obdrží.

# <a name="c"></a>[C #](#tab/csharp)

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
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Předchozí příklad naslouchá pro konkrétní jednu událost a provede akci, když je přijata.

Můžete naslouchat více událostí současně, jako v následujícím příkladu, který čeká na jednu ze tří možných oznámení událostí.

# <a name="c"></a>[C #](#tab/csharp)

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
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Předchozí příklad naslouchá *některé* z více událostí. Je také možné čekat na *všechny* události.

# <a name="c"></a>[C #](#tab/csharp)

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
> Předchozí kód je pro trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

V rozhraní .NET, pokud datovou část události `T`nelze převést na očekávaný typ , je vyvolána výjimka.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

`WaitForExternalEvent`čeká na neomezenou dobu pro některé vstup.  Aplikace funkce může být bezpečně uvolněna při čekání. Pokud a když událost dorazí pro tuto instanci orchestrace, je automaticky probuzena a okamžitě zpracuje událost.

> [!NOTE]
> Pokud vaše aplikace funkce používá plán spotřeby, žádné fakturační poplatky vznikají, zatímco `WaitForExternalEvent` funkce orchestrator `waitForExternalEvent` čeká na úkol z (.NET) nebo (JavaScript), bez ohledu na to, jak dlouho čeká.

## <a name="send-events"></a>Odesílání událostí

Metoda `RaiseEventAsync` (.NET) `raiseEvent` nebo (JavaScript) [vazby klienta orchestrace](durable-functions-bindings.md#orchestration-client) odešle události, na které `WaitForExternalEvent` (.NET) nebo `waitForExternalEvent` (JavaScript) čeká.  Metoda `RaiseEventAsync` přebírá *eventName* a *eventData* jako parametry. Data události musí být JSON serializovatelný.

Níže je ukázka funkce spouštěná frontou, která odešle událost "Approval" instanci funkce orchestrator. ID instance orchestrace pochází z těla zprávy fronty.

# <a name="c"></a>[C #](#tab/csharp)

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
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x `OrchestrationClient` je nutné `DurableClient` použít atribut namísto `DurableOrchestrationClient` atributu a `IDurableOrchestrationClient`místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

Interně `RaiseEventAsync` (.NET) `raiseEvent` nebo (JavaScript) zařadí zprávu, která se zachytí funkcí čekajícího orchestrátoru. Pokud instance nečeká na zadaný *název události,* zpráva o události je přidána do fronty v paměti. Pokud instance orchestrace později začne naslouchat pro *tento název události,* zkontroluje fronty pro zprávy událostí.

> [!NOTE]
> Pokud neexistuje žádná instance orchestrace se zadaným *ID instance*, zpráva o události je zahozena.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si, jak implementovat zpracování chyb](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Spuštění vzorku, který čeká na interakci s lidmi](durable-functions-phone-verification.md)