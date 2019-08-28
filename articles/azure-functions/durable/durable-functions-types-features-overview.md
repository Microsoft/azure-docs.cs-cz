---
title: Typy a funkce funkcí v rozšíření Durable Functions Azure Functions
description: Přečtěte si o typech funkcí a rolí, které podporují komunikaci typu Function-to-Function v Durable Functions orchestrace v Azure Functions.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 0d3087c768a02bb5c647fc0d10db3aa4274804f4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097741"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable Functions typy a funkce (Azure Functions)

Durable Functions je rozšíření [Azure Functions](../functions-overview.md). Durable Functions lze použít pro stavovou orchestraci provádění funkce. Trvalá funkce je řešení, které se skládá z různých Azure Functions. Funkce mohou hrát různé role v rámci trvalé orchestrace funkcí. 

Tento článek poskytuje přehled typů funkcí, které můžete použít při Durable Functions orchestrace. Tento článek obsahuje některé běžné vzory, které můžete použít k připojení funkcí. Přečtěte si, jak vám Durable Functions můžou vyřešit problémy s vývojem aplikací.

![Obrázek, který zobrazuje typy trvalých funkcí][1]  

## <a name="types-of-durable-functions"></a>Typy trvalých funkcí

V Azure Functions můžete použít čtyři typy trvalých funkcí: Activity, Orchestrator, entity a Client.

### <a name="activity-functions"></a>Funkce aktivity

Funkce aktivity představují základní pracovní jednotku v rámci trvalé orchestrace funkcí. Funkce aktivity jsou funkce a úkoly, které jsou v procesu Orchestrované. Můžete například vytvořit trvalou funkci pro zpracování objednávky. Úkoly zahrnují kontrolu inventáře a účtování zákazníků a vytváření dodávek. Každý úkol by představoval funkci aktivity. 

Funkce aktivity nejsou omezeny v typu práce, kterou lze v nich provádět. Funkci aktivity můžete napsat v jakémkoli [jazyce, který Durable Functions podporu](durable-functions-overview.md#language-support). Prostředí trvalého zpracování úloh zaručuje, že každá funkce s názvem funkce se spustí alespoň jednou během orchestrace.

K aktivaci funkce aktivity použijte [Trigger aktivity](durable-functions-bindings.md#activity-triggers) . Funkce .NET obdrží [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako parametr. Můžete také navázání triggeru na libovolný jiný objekt, který předává vstup do funkce. V jazyce JavaScript můžete k vstupu přistupovat prostřednictvím `<activity trigger binding name>` vlastnosti [ `context.bindings` objektu](../functions-reference-node.md#bindings).

Funkce Activity může také vrátit hodnoty do nástroje Orchestrator. Pokud odešlete nebo vrátíte velký počet hodnot z funkce Activity, můžete použít [řazené kolekce členů nebo pole](durable-functions-bindings.md#passing-multiple-parameters). Funkci aktivity lze aktivovat pouze z instance orchestrace. I když funkce aktivity a jiná funkce (například funkce aktivované protokolem HTTP) může sdílet nějaký kód, každá funkce může mít jenom jednu Trigger.

Další informace a příklady najdete v tématu [funkce aktivit](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Funkce nástroje Orchestrator

Funkce nástroje Orchestrator popisují způsob provádění akcí a pořadí, ve kterém jsou akce provedeny. Funkce nástroje Orchestrator popisují orchestraci v kódu (C# nebo JavaScriptu), jak je znázorněno v [Durable Functionsch vzorech a technických konceptech](durable-functions-concepts.md). Orchestrace může mít mnoho různých typů akcí, včetně [funkcí aktivity](#activity-functions), dílčích [orchestrací](#sub-orchestrations), [čekání na externí události](#external-events)a časovače. [](#durable-timers) Funkce nástroje Orchestrator mohou také interagovat s [funkcemi entit](#entity-functions).

Funkci Orchestrator musí aktivovat [Trigger orchestrace](durable-functions-bindings.md#orchestration-triggers).

Nástroj Orchestrator spouští [klient nástroje Orchestrator](#client-functions). Nástroj Orchestrator můžete aktivovat z libovolného zdroje (HTTP, Queue, datový proud událostí). Každá instance orchestrace má identifikátor instance. Identifikátor instance se dá automaticky vygenerovat (doporučeno) nebo generovat uživatelem. Pomocí identifikátoru instance můžete [Spravovat instance](durable-functions-instance-management.md) orchestrace.

Další informace a příklady najdete v tématu [triggery orchestrace](durable-functions-bindings.md#orchestration-triggers).

###  <a name="entity-functions"></a>Funkce entit (Preview)

Funkce entit definují operace pro čtení a aktualizaci malých částí stavu, označovaných jako *odolné entity*. Podobně jako funkce nástroje Orchestrator jsou funkce entit funkce se speciálním typem triggeru, *triggerem entity*. Na rozdíl od funkcí Orchestrator nemají entity Functions žádná konkrétní omezení kódu. Funkce entit také spravují stav explicitně namísto implicitního reprezentace stavu prostřednictvím řízení toku.

> [!NOTE]
> Funkce entit a související funkce jsou dostupné jenom v Durable Functions 2,0 a novějších.

Další informace o funkcích entit najdete v dokumentaci k funkcím funkce [entity Functions](durable-functions-preview.md#entity-functions) ve verzi Preview.

### <a name="client-functions"></a>Klientské funkce

Funkce klienta jsou aktivované funkcemi, které vytvářejí a spravují instance Orchestrace a entit. Jsou efektivní vstupním bodem pro interakci s Durable Functions. Můžete aktivovat funkci klienta z libovolného zdroje (HTTP, Queue, datový proud události atd.). Klientská funkce používá [vazbu klienta Orchestration](durable-functions-bindings.md#orchestration-client) k vytváření a správě trvalých orchestrací a entit.

Nejběžnějším příkladem klientské funkce je funkce aktivovaná protokolem HTTP, která spouští funkci Orchestrator, a potom vrátí odpověď stavu check. Příklad najdete v tématu [zjišťování adresy URL rozhraní API protokolu HTTP](durable-functions-http-api.md#http-api-url-discovery).

Další informace a příklady najdete v tématu věnovaném [klientovi Orchestration](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funkce a vzory

V dalších částech jsou popsány funkce a vzory Durable Functionsch typů.

### <a name="sub-orchestrations"></a>Dílčí orchestrace

Funkce nástroje Orchestrator mohou volat funkce aktivity, ale mohou také volat jiné funkce nástroje Orchestrator. Můžete například vytvořit větší orchestraci z knihovny funkcí nástroje Orchestrator. Nebo můžete souběžně spustit více instancí funkce nástroje Orchestrator.

Další informace a příklady najdete v tématu [dílčí orchestrace](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Trvalé časovače

[Durable Functions](durable-functions-overview.md) poskytuje *odolné časovače* , které lze použít ve funkcích nástroje Orchestrator pro implementaci zpoždění nebo nastavení časových limitů pro asynchronní akce. Používejte odolné časovače ve funkcích nástroje `Thread.Sleep` Orchestrator `Task.Delay` namístoC#a ( `setTimeout()` ) `setInterval()` nebo a (JavaScript).

Další informace a příklady najdete v tématu [trvalé časovače](durable-functions-timers.md).

### <a name="external-events"></a>Externí události

Funkce Orchestrator mohou počkat na externí události a aktualizovat instanci Orchestration. Tato funkce Durable Functions často je užitečná pro zpracování lidské interakce nebo jiných externích zpětných volání.

Další informace a příklady najdete v tématu [externí události](durable-functions-external-events.md).

### <a name="error-handling"></a>Zpracování chyb

Použijte kód pro implementaci orchestrace Durable Functions. Můžete použít funkce pro zpracování chyb v programovacím jazyce. Vzorce, `try` jako /jepráceve vašíorchestraci`catch` . 

Durable Functions taky přináší předdefinované zásady opakování. Akce může zpozdit a opakovat činnost automaticky, když dojde k výjimce. Pomocí opakovaných pokusů můžete zpracovat přechodné výjimky, aniž byste museli opustit orchestraci.

Další informace a příklady najdete v tématu [zpracování chyb](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Komunikace mezi funkcemi aplikace

I když trvalá orchestrace běží v kontextu jediné aplikace Function App, můžete použít vzory ke koordinaci orchestrací napříč mnoha aplikacemi funkcí. Komunikace mezi aplikacemi může probíhat přes protokol HTTP, ale při použití odolné architektury pro každou aktivitu můžete stále udržovat trvalý proces ve dvou aplikacích.

Následující příklady znázorňují orchestraci aplikací mezi funkcemi v C# a JavaScriptu. V každém příkladu jedna aktivita spouští externí orchestraci. Další aktivita načte a vrátí stav. Nástroj Orchestrator čeká na `Complete` dokončení stavu, než bude pokračovat.

Tady je několik příkladů orchestrace aplikací mezi funkcemi:

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
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
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

Chcete-li začít, vytvořte svou první trvalou [C#](durable-functions-create-first-csharp.md) funkci v nebo [JavaScriptu](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Přečtěte si další informace o Durable Functions](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
