---
title: Dílčí orchestrations pro odolná funkce – Azure
description: Způsob volání orchestrations z orchestrations v rozšíření trvanlivý funkce pro Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 7545a371749ed9af88f08af23cce3a513f494374
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33761329"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Dílčí orchestrations trvanlivý funkcí (Azure Functions)

Kromě volání funkcí aktivitu, můžete volat funkce orchestrator jiných funkcí produktu orchestrator. Můžete například vytvořit větší orchestration mimo knihovnu funkcí produktu orchestrator. Nebo můžete spustit víc instancí funkce orchestrator paralelně.

Funkce produktu orchestrator můžete volat jinou funkci orchestrator voláním [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) nebo [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) metoda. [Zpracování chyb & kompenzace](durable-functions-error-handling.md#automatic-retry-on-failure) článek obsahuje další informace o automatické opakování.

Funkce dílčí orchestrator chovají stejně jako funkce aktivitu z hlediska volajícího. Se můžete vrátit hodnotu, způsobí výjimku a může být očekáváno orchestrator funkcí nadřazené.

> [!NOTE]
> `CallSubOrchestratorAsync` a `CallSubOrchestratorWithRetryAsync` metody ještě nejsou k dispozici v jazyce JavaScript.

## <a name="example"></a>Příklad:

Následující příklad ilustruje scénářem IoT ("Internet věcí") tam, kde existuje více zařízení, které musí být zřízená. Neexistuje konkrétní orchestration, který je potřeba pro každé zařízení, které může vypadat přibližně takto:

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

Tato funkce orchestrator lze použít jako-je pro zřizování jednorázové zařízení, nebo může být součástí větší orchestration. V takovém případě můžete naplánovat funkce orchestrator nadřazené instance `DeviceProvisioningOrchestration` pomocí `CallSubOrchestratorAsync` rozhraní API.

Tady je příklad, který ukazuje, jak spustit víc funkcí orchestrator paralelně.

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
> [Zjistěte, jaké úlohu centra jsou a způsob jejich konfigurace](durable-functions-task-hubs.md)
