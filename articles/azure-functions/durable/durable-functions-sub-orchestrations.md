---
title: Dílčí orchestrací pro Durable Functions – Azure
description: Jak volat Orchestrace z Orchestrace v rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: df4cfd8cdf720dd085c3f14ad518c557f270ffa4
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53340857"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Dílčí Orchestrace v Durable Functions (Azure Functions)

Kromě volání funkcí aktivitu, můžete volat funkce orchestrátoru jiné funkce nástroje orchestrator. Můžete například vytvořit větší Orchestrace mimo knihovnu funkcí nástroje orchestrator. Nebo můžete spouštět více instancí funkce orchestrátoru paralelně.

Funkce orchestrátoru můžete volat jiné funkce nástroje orchestrator pomocí volání [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) nebo [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) metody v rozhraní .NET, nebo `callSubOrchestrator` nebo `callSubOrchestratorWithRetry` metody v jazyce JavaScript. [Zpracování chyb a kompenzace](durable-functions-error-handling.md#automatic-retry-on-failure) článek obsahuje další informace o automatické opakování.

Dílčí orchestrator funkce se chovají stejně jako funkce aktivitu z hlediska volajícího. Vracejí hodnotu, vyvolat výjimku a může být očekávána pomocí funkce orchestrátoru nadřazené.

## <a name="example"></a>Příklad:

Následující příklad ukazuje obsahující scénáře IoT ("Internet of Things"), kdy existuje více zařízení, které je potřeba zřídit. Neexistuje konkrétní Orchestrace, která musí provést pro každé zařízení, která může vypadat přibližně takto:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

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

Tato funkce produktu orchestrator může sloužit jako-je pro zřizování jednorázové zařízení nebo ho můžou být součástí větší Orchestrace. V takovém případě funkce orchestrátoru nadřazené lze naplánovat instance `DeviceProvisioningOrchestration` pomocí `CallSubOrchestratorAsync` (C#) nebo `callSubOrchestrator` (JavaScript) rozhraní API.

Tady je příklad, který ukazuje, jak k paralelnímu spouštění více funkcí nástroje orchestrator.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

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
> [Zjistěte, co jsou centra úloh a jejich konfiguraci](durable-functions-task-hubs.md)
