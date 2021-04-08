---
title: Dílčí orchestrace pro Durable Functions – Azure
description: Jak volat orchestrace z orchestrace v rozšíření Durable Functions pro Azure Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 5625bc2ddfa4b6f527ca16f19f33d257a1834d4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85340817"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Dílčí orchestrace v Durable Functions (Azure Functions)

Kromě volání funkcí aktivity mohou funkce Orchestrator volat jiné funkce nástroje Orchestrator. Například můžete vytvořit větší orchestraci z knihovny menších funkcí nástroje Orchestrator. Nebo můžete souběžně spustit více instancí funkce nástroje Orchestrator.

Funkce Orchestrator může volat jinou funkci nástroje Orchestrator pomocí `CallSubOrchestratorAsync` `CallSubOrchestratorWithRetryAsync` metod nebo v rozhraní .NET nebo `callSubOrchestrator` `callSubOrchestratorWithRetry` metod nebo v jazyce JavaScript. Další informace o automatickém opakování najdete v článku o [zpracování chyb & kompenzaci](durable-functions-error-handling.md#automatic-retry-on-failure) .

Funkce dílčího nástroje Orchestrator se chovají stejně jako funkce aktivity z perspektivy volajícího. Mohou vracet hodnotu, vyvolat výjimku a může být očekávána nadřazenou funkcí Orchestrator. 

> [!NOTE]
> V rozhraní .NET a JavaScriptu se aktuálně podporují dílčí orchestrace.

## <a name="example"></a>Příklad

Následující příklad znázorňuje scénář IoT ("Internet věcí"), kde je více zařízení, které je třeba zřídit. Následující funkce představuje pracovní postup zřizování, který je třeba spustit pro každé zařízení:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

---

Tato funkce Orchestrator se dá použít jako pro jednorázové zřizování zařízení nebo může být součástí větší orchestrace. V druhém případě může nadřazená funkce Orchestrator naplánovat instance `DeviceProvisioningOrchestration` pomocí `CallSubOrchestratorAsync` rozhraní API (.NET) nebo `callSubOrchestrator` (JavaScript).

Tady je příklad, který ukazuje, jak paralelně spustit více funkcí nástroje Orchestrator.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> Předchozí příklady jazyka C# jsou pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

---

> [!NOTE]
> Dílčí orchestrace musí být definovaná ve stejné aplikaci Function App jako nadřazená orchestrace. Pokud potřebujete zavolat a počkat na orchestraci v jiné aplikaci Function App, zvažte použití integrované podpory rozhraní HTTP API a vzoru příjemce cyklického dotazování HTTP 202. Další informace najdete v tématu [funkce protokolu HTTP](durable-functions-http-features.md) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si, jak nastavit vlastní stav orchestrace.](durable-functions-custom-orchestration-status.md)
