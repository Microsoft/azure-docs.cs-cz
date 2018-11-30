---
title: Přehled funkcí pro Durable Functions - Azure a typy funkce
description: Další typy funkcí a rolí, které umožňují komunikaci funkce funkce v rámci Orchestrace odolné funkce.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/04/2018
ms.author: azfuncdf
ms.openlocfilehash: 265314ebf2568bd586934d371e1e6c1d74e0b9bb
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642309"
---
# <a name="overview-of-function-types-and-features-for-durable-functions-azure-functions"></a>Přehled funkcí pro Durable Functions (Azure Functions) a typy funkce

Odolná služba Azure Functions poskytuje stavové Orchestrace provádění funkce. Řešení se skládá z různých Azure Functions je odolné funkce. Každá z těchto funkcí můžete přehrát různé role v rámci Orchestrace. Následující dokument obsahuje přehled typů funkcí, které jsou součástí Orchestrace odolné funkce. Zahrnuje také některé běžné vzory v propojení funkce.  Pokud chcete rovnou začít, vytvořte první odolné funkce v [ C# ](durable-functions-create-first-csharp.md) nebo [JavaScript](quickstart-js-vscode.md).

![Typy odolná služba functions][1]  

## <a name="types-of-functions"></a>Typy funkcí

### <a name="activity-functions"></a>Funkce aktivity

Aktivita funkce jsou základní jednotku práce v trvalé Orchestrace.  Aktivita funkce jsou funkce a úkoly jsou orchestrované v procesu.  Může například vytvořit odolné funkce, která se zákazníkovi účtovat zpracování objednávky - zkontrolujte soubor inventáře a vytvořit dodávku.  Jedna z těchto úloh by funkce protokolem aktivit.  Aktivita funkce nemají v typu práce můžete můžete v nich nějaká omezení.  Může být napsán v jakémkoli jazyce, Azure Functions podporuje.  Trvalý úloh framework zaručuje, že každá funkce volané aktivita se spustí alespoň jednou během Orchestrace.

Funkce protokolem aktivity musí být aktivované [aktivační událost pro aktivitu](durable-functions-bindings.md#activity-triggers).  Tato funkce se zobrazí [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako parametr. Aktivační událost můžete také navázat na libovolný objekt a zajistěte tak předání vstupy do funkce.  Aktivita funkce může také vracet hodnoty zpět na orchestrátoru.  Pokud odesílání nebo mnoho hodnot vrácení z funkce aktivitu, můžete [využít řazených kolekcí členů nebo pole](durable-functions-bindings.md#passing-multiple-parameters).  Aktivita funkce se dá spouštět jenom z instance Orchestrace.  Když nějaký kód mohou sdílet mezi funkcí sady aktivit a jiné funkci (například funkci aktivovanou protokolem HTTP), každá funkce může mít jenom jeden trigger.

Další informace a příklady najdete v [Durable Functions vazby článku](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Funkce nástroje Orchestrator

Funkce nástroje Orchestrator jsou základem odolné funkce.  Funkce nástroje Orchestrator popisují způsob a pořadí spouštění akcí.  Funkce nástroje Orchestrator popisují Orchestrace v kódu (C# nebo JavaScript), jak je znázorněno [odolná služba functions přehled](durable-functions-overview.md).  Orchestrace může mít mnoho různých typů akce, jako je třeba [funkce aktivity](#activity-functions), [dílčí Orchestrace](#sub-orchestrations), [čekající na externí události](#external-events), a [ časovače](#durable-timers).  

Funkce orchestrátoru musí být aktivované [Orchestrace trigger](durable-functions-bindings.md#orchestration-triggers).

Orchestrátor je tím, že [orchestrator klienta](#client-functions) které může sám aktivovat z libovolného zdroje (HTTP, fronty, streamy událostí).  Každá instance Orchestrace má identifikátor instance uvozovacím znakem, který může být automaticky generované (doporučeno) nebo generovaný uživatelem.  Tento identifikátor je možné použít k [správu instancí](durable-functions-instance-management.md) o orchestraci.

Další informace a příklady najdete v [Durable Functions vazby článku](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Funkce klienta

Klient funkce jsou aktivované funkce, které bude vytváření nových instancí Orchestrace.  Jsou vstupní bod pro vytvoření instance trvalý Orchestrace.  Funkce klienta můžete aktivované žádné aktivační události (HTTP, fronty, streamy událostí, atd.) a napsané v libovolném jazyce podporovaných aplikací.  Kromě aktivační událost, mají funkce klienta [klient Orchestrace](durable-functions-bindings.md#orchestration-client) vazby, která umožňuje vytvářet a spravovat trvalé Orchestrace.  Základní příklad funkce klienta je funkci aktivovanou protokolem HTTP, která se spustí funkce orchestrátoru a vrátí odpověď na kontrolu stavu jako [ukazuje následující příklad](durable-functions-http-api.md#http-api-url-discovery).

Další informace a příklady najdete v [Durable Functions vazby článku](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funkce a vzory

### <a name="sub-orchestrations"></a>Dílčí orchestrace

Kromě volání funkcí aktivitu, můžete volat funkce orchestrátoru jiné funkce nástroje orchestrator. Můžete například vytvořit větší Orchestrace mimo knihovnu funkcí nástroje orchestrator. Nebo můžete spouštět více instancí funkce orchestrátoru paralelně.

Další informace a příklady najdete v [dílčí Orchestrace článku](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Trvalý časovače

[Odolná služba Functions](durable-functions-overview.md) poskytuje *trvalý časovače* pro použití ve funkcích produktu orchestrator k implementaci zpoždění nebo nastavit vypršení časového limitu na asynchronní akce. Trvalý časovače byste měli použít ve funkce orchestrátoru místo `Thread.Sleep` a `Task.Delay` (C#), nebo `setTimeout()` a `setInterval()` (JavaScript).

Další informace a příklady trvalý časovače najdete v [článku trvalý časovače](durable-functions-timers.md)

### <a name="external-events"></a>Externí události

Funkce nástroje Orchestrator můžete počkat externí akce, které se aktualizovat instance Orchestrace. Tato funkce odolná služba Functions je často užitečné pro zpracování zásahem ze strany nebo jiné externí zpětná volání.

Další informace a příklady najdete v [externí akce, které článku](durable-functions-external-events.md).

### <a name="error-handling"></a>Zpracování chyb

Trvalý Orchestrace funkce jsou implementované v kódu a můžete použít zpracování chyb funkce programovacího jazyka.  To znamená, že vzorů, jako je "try/catch" bude fungovat ve vaší Orchestrace.  Odolná služba functions se také dodávají s některé předdefinované zásady opakování.  Akci můžete odložit a opakování aktivity automaticky při výjimkách.  Opakované pokusy umožňují zpracovat přechodným výjimkám bez nutnosti opouštět orchestraci.

Další informace a příklady najdete v [článku pro zpracování chyb](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Komunikace mezi function app

Přestože trvalý Orchestrace obecně kdekoli v rámci kontextu aplikace s jedinou funkci, existují vzorce a umožňují koordinaci Orchestrace napříč mnoha aplikace function App.  I když může být děje komunikaci mezi aplikacemi přes protokol HTTP, pomocí rozhraní odolné pro každou aktivitu znamená, že trvalý proces můžete udržovat stále mezi dvěma aplikacemi.

Příklad aplikace funkcí mezi Orchestrace v jazyce C# jsou uvedeny níže.  Externí Orchestrace se spustí jednu aktivitu. Jiné aktivity pak načte a vrátí stav.  Orchestrátor bude čekat stavu na dokončení, než budete pokračovat.

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now go do more work...
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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Pokračujte ve čtení dokumentaci Durable Functions](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
