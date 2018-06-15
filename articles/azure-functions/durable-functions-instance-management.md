---
title: Správa instancí trvanlivý funkcí – Azure
description: Naučte se spravovat instancí v rozšíření trvanlivý funkce pro Azure Functions.
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
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 0e573b4973ea30b990043b54c5cdcf0805135a40
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764651"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Správa instancí trvanlivý funkcí (Azure Functions)

[Trvanlivý funkce](durable-functions-overview.md) orchestration instance může být spuštěna, byla ukončena, dotaz a odeslat oznámení události. Správa všech instancí se provádí pomocí [orchestration klienta vazby](durable-functions-bindings.md). Tento článek přejde na podrobné informace o každé instance operace správy.

## <a name="starting-instances"></a>Výchozí instance

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metodu [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) spustí novou instanci funkce produktu orchestrator. Instance této třídy lze získat pomocí `orchestrationClient` vazby. Interně tato to metoda enqueues zprávu do fronty ovládací prvek, který potom aktivuje spuštění funkce se zadaným názvem, který používá `orchestrationTrigger` aktivovat vazby. 

Úloha dokončení při spuštění procesu orchestration. V rámci 30 sekund by se měl spustit proces orchestration. Pokud trvá déle, `TimeoutException` je vyvolána výjimka. 

Parametry, které chcete [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) jsou následující:

* **Název**: název funkce orchestrator při plánování.
* **Vstupní**: žádná JSON serializovatelný data, která mají být předány jako vstup do funkce produktu orchestrator.
* **Identifikátor InstanceId**: (volitelné) Jedinečný ID instance. Pokud není zadaný, vygeneruje se ID náhodných instance.

Zde je jednoduchý příklad C#:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

Pro jazyky rozhraní .NET, funkce výstup vazby lze spustit také nové instance. V takovém případě můžete použít všechny JSON serializovatelný objekt, který má výše tři parametry jako pole. Zvažte například následující funkce JavaScript:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

> [!NOTE]
> Doporučujeme použít identifikátor náhodných pro ID instance. To vám pomůže zajištění jako distribučního stejné zatížení při orchestrator funkce škálování mezi několika virtuálními počítači. Správný čas používat-náhodné instance ID je při ID musí pocházet z externího zdroje nebo při implementaci [singleton orchestrator](durable-functions-singletons.md) vzor.

## <a name="querying-instances"></a>Dotazování instancí

[GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metodu [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) třída dotazuje se na stav instance orchestration. Jak dlouho trvá `instanceId` (povinné), `showHistory` (volitelné), a `showHistoryOutput` (volitelné) jako parametry. Pokud `showHistory` je nastaven na `true`, odpověď bude obsahovat historie provádění. Pokud `showHistoryOutput` je nastaven na `true` taky historie provádění bude obsahovat výstupů aktivity. Metoda vrátí objekt s následujícími vlastnostmi:

* **Název**: název funkce produktu orchestrator.
* **Identifikátor InstanceId**: instance ID orchestration (musí být stejná jako `instanceId` vstupní).
* **CreatedTime**: čas, kdy funkce orchestrator spuštění.
* **LastUpdatedTime**: čas, kdy orchestration poslední kontrolní bod.
* **Vstupní**: vstup funkce jako hodnotu JSON.
* **CustomStatus**: vlastní orchestration stav ve formátu JSON. 
* **Výstup**: výstup funkce jako hodnota JSON (Pokud je funkce byla dokončena). Pokud funkce orchestrator se nezdařilo, tato vlastnost bude obsahovat podrobnosti o chybě. Pokud funkci orchestrator bylo ukončeno, tato vlastnost bude obsahovat zadaný důvod pro ukončení (pokud existuje).
* **RuntimeStatus**: jeden z následujících hodnot:
    * **Spuštění**: instance byl spuštěn.
    * **Dokončit**: instance dokončil normálně.
    * **ContinuedAsNew**: instance samotného s novou historie restartování. Toto je přechodný stav.
    * **Se nezdařilo**: instance došlo k chybě.
    * **Byl ukončen**: instance náhle byl ukončen.
* **Historie**: historie provádění orchestration. Toto pole je vyplněný, pouze když `showHistory` je nastaven na `true`.
    
Tato metoda vrátí hodnotu `null` Pokud instance neexistuje nebo ještě nezačala systémem.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

## <a name="terminating-instances"></a>Ukončení instance

Spuštěné instance orchestration můžete ukončit pomocí [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metodu [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) třídy. Jsou dva parametry `instanceId` a `reason` řetězce, který se zapisují do protokolů a stav instance. Ukončenou instance se zastaví také nedosáhne Další `await` bod, nebo se ukončí okamžitě pokud už je na `await`. 

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Ukončení instance nejsou aktuálně rozšířena. Aktivita funkce a dílčí orchestrations se spustí až do ukončení bez ohledu na to, zda byla ukončena orchestration instance, která je volána.

## <a name="sending-events-to-instances"></a>Odesílání událostí do instance

Oznamování událostí lze odeslat buď do spuštěné instance pomocí [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metodu [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) třídy. Instance, které může zpracovat tyto události jsou ty, které čekají na volání [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

Parametry, které chcete [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) jsou následující:

* **Identifikátor InstanceId**: jedinečné ID instance.
* **EventName**: název události k odeslání.
* **EventData**: datovou část JSON serializovatelný k odeslání do instance.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!WARNING]
> Pokud není žádná instance orchestration se zadaným *instance ID* nebo pokud není instance čekání na zadaný *název události*, zpráva o události se zahodí. Další informace o tomto chování najdete v tématu [potíže Githubu](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="wait-for-orchestration-completion"></a>Čekání na dokončení orchestration

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) třídy zpřístupňuje [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) rozhraní API, které můžete použít k získání synchronně skutečný výstup z orchestration instance. Metoda používá výchozí hodnotu 10 sekund, `timeout` a 1 sekunda pro `retryInterval` Pokud nejsou nastavené.  

Tady je příklad funkce triggeru protokolu HTTP, které ukazuje, jak používat toto rozhraní API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

Funkci nelze volat pomocí 2 sekund časového limitu a interval opakování 0,5 sekundu tento řádek:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

V závislosti na čas potřebný k získat odpověď z orchestration instance jsou dva případy:

1. Instance orchestration dokončit v rámci definovaného časového limitu (v tomto případě 2 sekundy), odpověď je výstup instance skutečné orchestration doručit synchronně:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

2. Instance orchestration nelze dokončit v rámci definovaného časového limitu (v tomto případě 2 sekundy), je odpověď na výchozí jeden popsané v **zjišťování adresy URL rozhraní API HTTP**:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Formát adresy URL webhooku se můžou lišit v závislosti na spuštěné verze hostitele Azure Functions. V předchozím příkladu je pro hostitele Azure funkce 2.0.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Naučte se používat rozhraní API HTTP pro instanci správy](durable-functions-http-api.md)
