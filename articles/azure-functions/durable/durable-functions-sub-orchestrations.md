---
title: Dílčí orchestrace pro Durable Functions – Azure
description: Jak volat orchestrace z orchestrace v rozšíření Durable Functions pro Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: azfuncdf
ms.openlocfilehash: 7b5e811daecbb7687abe7a37b75e2730d7830c2c
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983623"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Dílčí orchestrace v Durable Functions (Azure Functions)

Kromě volání funkcí aktivity mohou funkce Orchestrator volat jiné funkce nástroje Orchestrator. Můžete například vytvořit větší orchestraci z knihovny funkcí nástroje Orchestrator. Nebo můžete souběžně spustit více instancí funkce nástroje Orchestrator.

Funkce Orchestrator může volat jinou funkci nástroje Orchestrator voláním metody [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) nebo [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) v rozhraní .NET nebo `callSubOrchestrator` metod nebo `callSubOrchestratorWithRetry` v jazyce JavaScript. Další informace o automatickém opakování najdete v článku o [zpracování chyb & kompenzaci](durable-functions-error-handling.md#automatic-retry-on-failure) .

Funkce dílčího nástroje Orchestrator se chovají stejně jako funkce aktivity z perspektivy volajícího. Mohou vracet hodnotu, vyvolat výjimku a může být očekávána nadřazenou funkcí Orchestrator. 

> [!NOTE]
> V současné době je nutné zadat `instanceId` hodnotu argumentu rozhraní API pro podorchestraci v JavaScriptu.

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

> [!NOTE]
> Dílčí orchestrace musí být definovaná ve stejné aplikaci Function App jako nadřazená orchestrace. Pokud potřebujete zavolat a počkat na orchestraci v jiné aplikaci Function App, zvažte použití integrované podpory rozhraní HTTP API a vzoru příjemce cyklického dotazování HTTP 202. Další informace najdete v tématu [funkce protokolu HTTP](durable-functions-http-features.md) .

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přečtěte si, jak nastavit vlastní stav orchestrace.](durable-functions-custom-orchestration-status.md)