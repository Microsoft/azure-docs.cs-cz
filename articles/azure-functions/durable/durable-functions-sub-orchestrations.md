---
title: Dílčí orchestrace pro Durable Functions – Azure
description: Jak volat orchestrace z orchestrace v rozšíření Durable Functions pro Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 868efad58e14fd817729f0aa9ac785bc0f960867
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087032"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Dílčí orchestrace v Durable Functions (Azure Functions)

Kromě volání funkcí aktivity mohou funkce Orchestrator volat jiné funkce nástroje Orchestrator. Můžete například vytvořit větší orchestraci z knihovny funkcí nástroje Orchestrator. Nebo můžete souběžně spustit více instancí funkce nástroje Orchestrator.

Funkce Orchestrator může volat jinou funkci nástroje Orchestrator voláním metody [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) nebo [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) v rozhraní .NET nebo `callSubOrchestrator` metod nebo `callSubOrchestratorWithRetry` v jazyce JavaScript. Další informace o automatickém opakování najdete v článku o [zpracování chyb & kompenzaci](durable-functions-error-handling.md#automatic-retry-on-failure) .

Funkce dílčího nástroje Orchestrator se chovají stejně jako funkce aktivity z perspektivy volajícího. Mohou vracet hodnotu, vyvolat výjimku a může být očekávána nadřazenou funkcí Orchestrator.

## <a name="example"></a>Příklad

Následující příklad znázorňuje scénář IoT ("Internet věcí"), kde je více zařízení, které je třeba zřídit. K dispozici je konkrétní orchestrace, kterou je třeba provést pro každé zařízení, což může vypadat nějak takto:

### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

Tato funkce Orchestrator se dá použít jako pro jednorázové zřizování zařízení nebo může být součástí větší orchestrace. V druhém případě může nadřazená funkce Orchestrator naplánovat instance `DeviceProvisioningOrchestration` `CallSubOrchestratorAsync` pomocí rozhraní API (C#) nebo `callSubOrchestrator` (JavaScript).

Tady je příklad, který ukazuje, jak paralelně spustit více funkcí nástroje Orchestrator.

### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    for (const deviceId of deviceIds) {
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.push(provisionTask);
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, co jsou centra úloh a jak je nakonfigurovat.](durable-functions-task-hubs.md)
