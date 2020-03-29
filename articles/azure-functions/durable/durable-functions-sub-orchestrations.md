---
title: Podorchestrations pro trvanlivé funkce – Azure
description: Jak volat orchestrations z orchestrations v rozšíření trvalé funkce pro funkce Azure.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: d4d599063f727510cbf504ea3d121bdabfe001c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261513"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Podorchestrace v trvalých funkcích (funkce Azure)

Kromě volání funkce aktivity orchestrator funkce můžete volat jiné funkce orchestrator. Můžete například vytvořit větší orchestraci z knihovny menších funkcí orchestratoru. Nebo můžete spustit více instancí funkce orchestrator paralelně.

Funkce orchestrator může volat jinou funkci `CallSubOrchestratorAsync` orchestrator pomocí `CallSubOrchestratorWithRetryAsync` metod `callSubOrchestrator` nebo `callSubOrchestratorWithRetry` v rozhraní .NET nebo metod or v Jazyce JavaScript. Článek [Kompenzace zpracování &](durable-functions-error-handling.md#automatic-retry-on-failure) obsahuje další informace o automatickém opakování.

Funkce suborchestrtoru se chovají stejně jako funkce aktivity z pohledu volajícího. Mohou vrátit hodnotu, vyvolat výjimku a může být očekáván nadřazenou funkcí orchestrator. 
## <a name="example"></a>Příklad

Následující příklad ilustruje scénář IoT ("Internet věcí"), kde existuje více zařízení, které je potřeba zřídit. Následující funkce představuje pracovní postup zřizování, který je třeba provést pro každé zařízení:

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Tato funkce orchestrator lze použít jako-je pro zřizování jednorázové zařízení nebo může být součástí větší orchestraci. V druhém případě může nadřazená funkce `DeviceProvisioningOrchestration` orchestrator naplánovat instance použití rozhraní `CallSubOrchestratorAsync` API (.NET) nebo `callSubOrchestrator` (JavaScript).

Zde je příklad, který ukazuje, jak spustit více funkcí orchestrator paralelně.

# <a name="c"></a>[C #](#tab/csharp)

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
> Předchozí příklady jazyka C# jsou pro trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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
> Podorchestrations musí být definovány ve stejné aplikaci funkce jako nadřazené orchestraci. Pokud potřebujete volat a čekat na orchestrace v jiné aplikaci funkce, zvažte použití integrované podpory pro http api a http 202 dotazování spotřebitele vzor. Další informace naleznete v tématu [Funkce protokolu HTTP.](durable-functions-http-features.md)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si, jak nastavit vlastní stav orchestrace](durable-functions-custom-orchestration-status.md)