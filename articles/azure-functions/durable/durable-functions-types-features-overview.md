---
title: Typy funkcí a funkcí v rozšíření Durable Functions, Azure functions
description: Další informace o typech funkce a role, které podporují funkce funkce komunikace v Orchestrace Durable Functions ve službě Azure Functions.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 03/19/2019
ms.author: v-junlch
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731107"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Odolné funkce typy a funkce (Azure Functions)

Odolná služba Functions je rozšířením [Azure Functions](../functions-overview.md). Odolná služba Functions můžete použít pro stavové Orchestrace provádění funkce. Odolné funkce je řešení, která se skládá z různých funkcí Azure. Funkce můžete přehrát různé role v Orchestrace odolné funkce. 

Tento článek obsahuje přehled typů funkcí, které můžete použít v Orchestrace Durable Functions. Tento článek obsahuje některé běžné vzory, které můžete použít pro připojení funkce. Zjistěte, jak Durable Functions vám může pomoct vyřešit problémy při vývoji aplikace.

![Obrázek, který se zobrazí typy odolná služba functions][1]  

## <a name="types-of-durable-functions"></a>Typy odolná služba functions

Můžete použít tři typy trvalý funkcí ve službě Azure Functions: aktivita, orchestrator a klienta.

### <a name="activity-functions"></a>Funkce aktivity

Aktivita funkce jsou základní jednotku práce v Orchestrace odolné funkce. Aktivita funkce jsou funkce a úkoly, které jsou orchestrované v procesu. Například může vytvořit trvalý funkci ke zpracování objednávky. Úkoly zahrnují kontroly inventáře, účtování zákazníka a vytváření dodávky. Každý úkol by funkce protokolem aktivit. 

Aktivita funkce nejsou s omezením pomocí specifikátoru typu práce můžete můžete v nich. Funkce protokolem aktivit můžete napsat v jakémkoli [jazyk, který podporují Durable Functions](durable-functions-overview.md#language-support). Trvalý úloh framework zaručuje, že každá funkce volané aktivita se spustí alespoň jednou během Orchestrace.

Použití [aktivační událost pro aktivitu](durable-functions-bindings.md#activity-triggers) k aktivaci funkce protokolem aktivit. Zobrazí funkce .NET [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako parametr. Aktivační událost můžete také navázat na libovolný objekt a zajistěte tak předání vstupy do funkce. V jazyce JavaScript, dostanete vstup prostřednictvím `<activity trigger binding name>` vlastnost [ `context.bindings` objekt](../functions-reference-node.md#bindings).

Aktivita funkce můžete také vrátit hodnoty orchestrátor. Pokud odesílání nebo vrátit velký počet hodnot z funkce aktivitu, můžete použít [řazených kolekcí členů nebo pole](durable-functions-bindings.md#passing-multiple-parameters). Můžete aktivovat funkce protokolem aktivit pouze z instance Orchestrace. I když funkce protokolem aktivit a jiné funkci (například funkci aktivovanou protokolem HTTP) může sdílet kód, každá funkce může mít jenom jeden trigger.

Další informace a příklady najdete v tématu [funkce aktivity](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Funkce nástroje Orchestrator

Funkce nástroje Orchestrator popisují, jak akce spustí a pořadí, ve kterém jsou provedení akce. Orchestrace v kódu popisují funkce produktu Orchestrator (C# nebo JavaScript) jak je znázorněno v [Durable Functions vzory a technických konceptech](durable-functions-concepts.md). Orchestrace může mít mnoho různých typů akcí, včetně [funkce aktivity](#activity-functions), [dílčí Orchestrace](#sub-orchestrations), [čekající na externí události](#external-events)a [časovače](#durable-timers). 

Funkce orchestrátoru musí být aktivované [Orchestrace trigger](durable-functions-bindings.md#orchestration-triggers).

Orchestrátor je tím, že [orchestrator klienta](#client-functions). Můžete spustit nástroje orchestrator z libovolného zdroje (HTTP, fronty, datového proudu událostí). Každá instance Orchestrace má identifikátor instance. Identifikátor instance může být automaticky generované (doporučeno) nebo generovaný uživatelem. Můžete použít identifikátor instance na [správu instancí](durable-functions-instance-management.md) o orchestraci.

Další informace a příklady najdete v tématu [Orchestrace triggery](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Funkce klienta

Funkce klienta jsou aktivované funkce, které vytvářejí nové instance Orchestrace. Funkce klienta jsou vstupní bod pro vytvoření instance Orchestrace Durable Functions. Můžete aktivovat funkci klienta z libovolného zdroje (HTTP, fronty, datového proudu událostí). Funkce klienta můžete napsat v jakémkoli jazyce, který aplikace podporuje. 

Funkce klienta je navíc [klient Orchestrace](durable-functions-bindings.md#orchestration-client) vazby. Funkce klienta může použít klient Orchestrace vazby můžete vytvářet a spravovat trvalé Orchestrace. 

Základní příklad funkce klienta je funkci aktivovanou protokolem HTTP, spuštění funkce orchestrátoru, který vrátí odpověď stavu zaškrtnutí. Příklad najdete v tématu [zjišťování adresy URL rozhraní API HTTP](durable-functions-http-api.md#http-api-url-discovery).

Další informace a příklady najdete v tématu [klient Orchestrace](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funkce a vzory

Další části popisují funkce a vzory Durable Functions typů.

### <a name="sub-orchestrations"></a>Dílčí orchestrace

Funkce nástroje Orchestrator můžete volat funkce aktivity, ale můžete také volat jiné funkce nástroje orchestrator. Můžete například vytvořit větší Orchestrace mimo knihovnu funkcí nástroje orchestrator. Nebo můžete spouštět více instancí funkce orchestrátoru paralelně.

Další informace a příklady najdete v tématu [dílčí Orchestrace](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Trvalý časovače

[Odolná služba Functions](durable-functions-overview.md) poskytuje *trvalý časovače* , který se ve funkcích produktu orchestrator můžete použít k implementaci zpoždění nebo nastavit vypršení časového limitu na asynchronní akce. Použít trvalý časovače funkcí nástroje orchestrator místo `Thread.Sleep` a `Task.Delay` (C#) nebo `setTimeout()` a `setInterval()` (JavaScript).

Další informace a příklady najdete v tématu [trvalý časovače](durable-functions-timers.md).

### <a name="external-events"></a>Externí události

Funkce nástroje Orchestrator můžete počkat externí akce, které se aktualizovat instance Orchestrace. Tato funkce Durable Functions často je vhodný pro zpracování zásahem ze strany nebo jiné externí zpětná volání.

Další informace a příklady najdete v tématu [externí akce, které](durable-functions-external-events.md).

### <a name="error-handling"></a>Zpracování chyb

Implementace Orchestrace Durable Functions pomocí kódu. Můžete použít zpracování chyb funkce programovacího jazyka. Vzorů, jako jsou `try` / `catch` pracovat ve vaší Orchestrace. 

Odolná služba Functions se také dodávají s předdefinované zásady opakování. Akci můžete odložit a aktivity automaticky opakovat, pokud dojde k výjimce. Opakování můžete použít ke zpracování přechodným výjimkám bez opuštění orchestraci.

Další informace a příklady najdete v tématu [zpracování chyb](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Komunikace mezi function app

I když trvalý Orchestrace běží v kontextu jedné funkce aplikace, můžete použít vzorce ke koordinaci Orchestrace napříč mnoha aplikace function App. Dojít ke komunikaci mezi aplikacemi přes protokol HTTP, ale pomocí rozhraní odolné pro každou aktivitu znamená, že se že můžete stále udržovat trvalý proces mezi dvěma aplikacemi.

Následující příklady ukazují různé funkce aplikace Orchestrace ve C# a JavaScript. V obou příkladech spustí jednu aktivitu externí Orchestrace. Jiné aktivity načte a vrátí stav. Orchestrátor čeká stav `Complete` před pokračováním.

Tady je několik příkladů Orchestrace aplikace funkcí mezi:

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.chinacloudsites.cn/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.chinacloudsites.cn/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>Další postup

Abyste mohli začít, vytvoření první funkce trvalý v [ C# ](durable-functions-create-first-csharp.md) nebo [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Další informace o Durable Functions](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png

<!-- Update_Description: wording update -->