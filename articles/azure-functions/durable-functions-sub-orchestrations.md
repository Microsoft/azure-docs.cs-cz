---
title: Dílčí orchestrací pro Durable Functions – Azure
description: Jak volat Orchestrace z Orchestrace v rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 59e8eb41b7e9fe3d57196f6844d1a768c3ef598b
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094428"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Dílčí Orchestrace v Durable Functions (Azure Functions)

Kromě volání funkcí aktivitu, můžete volat funkce orchestrátoru jiné funkce nástroje orchestrator. Můžete například vytvořit větší Orchestrace mimo knihovnu funkcí nástroje orchestrator. Nebo můžete spouštět více instancí funkce orchestrátoru paralelně.

Funkce orchestrátoru můžete volat jiné funkce nástroje orchestrator pomocí volání [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) nebo [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) metody. [Zpracování chyb a kompenzace](durable-functions-error-handling.md#automatic-retry-on-failure) článek obsahuje další informace o automatické opakování.

Dílčí orchestrator funkce se chovají stejně jako funkce aktivitu z hlediska volajícího. Vracejí hodnotu, vyvolat výjimku a může být očekávána pomocí funkce orchestrátoru nadřazené.

> [!NOTE]
> `CallSubOrchestratorAsync` a `CallSubOrchestratorWithRetryAsync` metody ještě nejsou k dispozici v jazyce JavaScript.

## <a name="example"></a>Příklad:

Následující příklad ukazuje obsahující scénáře IoT ("Internet of Things"), kdy existuje více zařízení, které je potřeba zřídit. Neexistuje konkrétní Orchestrace, která musí provést pro každé zařízení, která může vypadat přibližně takto:

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string deviceId = ctx.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await ctx.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await ctx.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await ctx.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

Tato funkce produktu orchestrator může sloužit jako-je pro zřizování jednorázové zařízení nebo ho můžou být součástí větší Orchestrace. V takovém případě funkce orchestrátoru nadřazené lze naplánovat instance `DeviceProvisioningOrchestration` pomocí `CallSubOrchestratorAsync` rozhraní API.

Tady je příklad, který ukazuje, jak k paralelnímu spouštění více funkcí nástroje orchestrator.

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string[] deviceIds = await ctx.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = ctx.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, co jsou centra úloh a jejich konfiguraci](durable-functions-task-hubs.md)
