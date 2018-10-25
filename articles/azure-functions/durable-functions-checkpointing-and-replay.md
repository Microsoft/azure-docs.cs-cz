---
title: Kontrolní body a opětovného přehrání v Durable Functions – Azure
description: Zjistěte, jak vytváření kontrolních bodů a odpověď funguje v rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf
ms.openlocfilehash: 5336c0a510f0be7d548d1d549b5b763c12e700b2
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987711"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Kontrolní body a opětovného přehrání v Durable Functions (Azure Functions)

Mezi klíčové atributy odolná služba Functions je **spolehlivé provádění**. Aktivita funkcí a funkcí nástroje Orchestrator může běžet na různých virtuálních počítačů v rámci datového centra a tyto virtuální počítače nebo základní síťovou infrastrukturu není 100 % spolehlivý.

Přestože to Durable Functions zajišťuje spolehlivé provádění Orchestrace. Dělá to pomocí úložiště front do volání funkce jednotky a pravidelně historie provádění vytváření kontrolních bodů do tabulky úložiště (pomocí vzoru návrhu cloudu říká [modelu Event Sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Této historie můžete znovu přehrát pak automaticky rekonstruovat v paměti stav funkce orchestrátoru.

## <a name="orchestration-history"></a>Historie Orchestrace

Předpokládejme, že máte následující funkce nástroje orchestrator:

#### <a name="c"></a>C#

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    return output;
});
```

V každé `await` (C#) nebo `yield` – příkaz (JavaScript), kontrolní body trvalý Framework úloh stav provádění funkce do tabulky úložiště. Tento stav se, co se označuje jako *Orchestrace historie*.

## <a name="history-table"></a>Tabulky historie

Obecně řečeno trvalý Framework úkolů provede následující akce na každý kontrolní bod:

1. Historie provádění uloží do tabulky Azure Storage.
2. Zařadí zprávy pro funkce orchestrátoru chce volat.
3. Zařadí zprávy pro orchestrator, samotný &mdash; například zpráv trvalý časovače.

Po dokončení kontrolního bodu funkce orchestrátoru je bezplatné odstraněn z paměti, dokud nedojde k více práce na práci.

> [!NOTE]
> Azure Storage neposkytuje žádné záruky transakční mezi ukládání dat do úložiště tabulek a front. Zpracování chyb, poskytovatele úložiště Durable Functions používá *konečné konzistence* vzory. Tyto vzory Ujistěte se, že pokud dojde k chybě nebo ztrátě připojení uprostřed kontrolní bod se neztratila žádná data.

Po dokončení historie funkce je uvedeno výše bude vypadat nějak takto ve službě Azure Table Storage (zkratka pro ilustraci):

| PartitionKey (ID instance)                     | Typ události             | Časové razítko               | Vstup | Název             | Výsledek                                                    | Status | 
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|---------------------| 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     | 
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | Hodnotu Null  | E1_HelloSequence |                                                           |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | "" "Hello Tokio!" ""                                        |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | "" "Hello Seattle!" ""                                      |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "" "Hello Londýn!" ""                                       |                     | 
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokio!" ",""Hello Seattle!" ",""Londýn Hello!" "]" | Dokončeno           | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     | 

Několik poznámek na hodnoty ve sloupcích:
* **PartitionKey**: obsahuje ID instance orchestraci.
* **Typ EventType**: reprezentuje typ události. Může být jedna z následujících typů:
    * **OrchestrationStarted**: funkce orchestrátoru obnovit od await nebo je poprvé spuštěna. `Timestamp` Sloupec slouží k naplnění deterministická hodnota [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) rozhraní API.
    * **ExecutionStarted**: funkce orchestrátoru zahájilo se spuštění poprvé. Tato událost také obsahuje funkce vstup v `Input` sloupce.
    * **TaskScheduled**: byla naplánována funkce aktivitu. Název funkce aktivity jsou zachyceny `Name` sloupce.
    * **TaskCompleted**: aktivita funkce dokončena. Výsledek funkce je ve `Result` sloupce.
    * **TimerCreated**: byl vytvořen trvalý časovače. `FireAt` Sloupec obsahuje naplánovaném čase UTC, kdy vyprší.
    * **TimerFired**: aktivovaných trvalý časovače.
    * **EventRaised**: externí událost byla odeslána do instance Orchestrace. `Name` Sloupec zachycuje název události a `Input` sloupec zachycuje datové části události.
    * **OrchestratorCompleted**: funkce orchestrátoru očekáváno.
    * **ContinueAsNew**: funkce orchestrátoru dokončit a samotné restartuje, takže se nový stav. `Result` Sloupec obsahuje hodnotu, která se používá jako vstup v instanci aplikace restartovala.
    * **ExecutionCompleted**: funkce orchestrátoru dokončena (nebo neúspěšných). Výstupy funkce nebo podrobnosti o chybě jsou uloženy v `Result` sloupce.
* **Časové razítko**: časové razítko The UTC Historie událostí.
* **Název**: název funkce, která byla vyvolána.
* **Vstupní**: vstup ve formátu JSON funkce.
* **Výsledek**: výstupní funkci; to znamená, vrácená hodnota.

> [!WARNING]
> Když je to užitečné jako nástroj pro ladění, nevyřídí všechny závislosti pro tuto tabulku. To může změnit, protože rozšíření Durable Functions vyvíjí.

Pokaždé, když se funkce obnoví ze `await`, trvalý Framework úlohy znovu spustí operaci funkce orchestrátoru úplně od začátku. Na každý znovu spustit consults historii spuštění chcete určit, zda má aktuální asynchronní operace umístíte.  Pokud konal úplně operaci, rozhraní okamžitě přehrává výstupní operace a přejde k další `await`. Tento proces pokračuje, dokud se přehrály celou historii, v tomto okamžiku se obnoví všechny místní proměnné ve funkci nástroje orchestrator na předchozí hodnoty.

## <a name="orchestrator-code-constraints"></a>Omezení kódu produktu Orchestrator

Chování opakování vytvoří omezení na typu kódu, který je možné psát v funkce orchestrátoru:

* Kód produktu Orchestrator musí být **deterministické**. Přehrály více než jednou a musí mít stejný výsledek pokaždé, když. Například žádné přímé volání pro získání aktuální datum a čas, získat náhodných čísel, generovat náhodný GUID nebo volání do vzdálené koncové body.

  Pokud kód orchestrator je potřeba získat aktuální datum a čas, měla by používat [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) rozhraní API, který je bezpečný pro opakování.

  Nedeterministické operace je třeba provést v funkce aktivity. To zahrnuje všechny interakce s jiných vstupních nebo výstupních vazeb. Tím se zajistí, že všechny Nedeterministický hodnoty se budou vygenerované jednou na první spuštění a uloží do historie spouštění. Další spuštění pak bude automaticky používat uložené hodnoty.

* Orchestrator kód by měl být **neblokující**. Například to znamená, že žádné vstupně-výstupní operace a žádná volání `Thread.Sleep` nebo ekvivalentní rozhraní API.

  Pokud je nutné zpoždění orchestrátoru, můžete použít [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) rozhraní API.

* Kód produktu Orchestrator musí **nikdy zahájena libovolné asynchronní operace** s výjimkou použití [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) rozhraní API. Například, ne `Task.Run`, `Task.Delay` nebo `HttpClient.SendAsync`. Trvalý Framework úkol spustí kód nástroje orchestrator v jednom vlákně a nemůžou komunikovat s ostatními vlákny, které mohou být naplánovány tak jiné asynchronní rozhraní API.

* **Mělo by se vyhnout nekonečné smyčky** v kódu produktu orchestrator. Protože trvalý úloh Framework uloží historie provádění funkce průběhu Orchestrace, může způsobit nekonečnou smyčku instanci orchestrator mít nedostatek paměti. U scénářů s nekonečnou smyčku pomocí rozhraní API [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) restartovat spuštění funkce a zrušíte předchozí historie spuštění.

Zatímco tato omezení se může zdát složitý na nejprve v praxi, které nejsou pevné dodržovat. Trvalý Framework úkol pokusí rozpoznat porušení výše uvedených pravidel a vyvolá výjimku `NonDeterministicOrchestrationException`. Ale toto chování zjišťování je best effort a by neměl na ní závisí.

> [!NOTE]
> Všechna tato pravidla vztahují pouze na funkce aktivované `orchestrationTrigger` vazby. Aktivita funkce aktivované službou `activityTrigger` vazbu a funkce, které používají `orchestrationClient` vazby, mají žádné takové omezení.

## <a name="durable-tasks"></a>Trvalý úlohy

> [!NOTE]
> Tato část popisuje podrobnosti interní implementace rozhraní trvalý úloh. Odolná služba Functions můžete použít bez znalosti tyto informace. Je určena pouze pro vám pomůže pochopit chování opakování.

Úlohy, které může být bezpečně očekávána funkcí nástroje orchestrator jsou někdy označovány jako *trvalý úlohy*. Toto jsou úlohy, které se vytváří a spravují trvalý rozhraním úloh. Příklady jsou úlohy vrácené `CallActivityAsync`, `WaitForExternalEvent`, a `CreateTimer`.

Tyto *trvalý úlohy* interně spravuje pomocí seznamu `TaskCompletionSource` objekty. Během opětovného přehrání tyto úlohy vytvořené jako součást spuštění kódu produktu orchestrator a jsou dokončit, protože dispečer zobrazí odpovídající události historie. Tohle všechno se synchronně pomocí jedno vlákno, dokud se znovu přehrát veškerá historie. Žádné trvalé úlohy, které nebyly dokončeny do konce historie opakování má provádět příslušné akce. Zpráva může být například zařazených do fronty k voláním funkce aktivity.

Chování při spuštění je zde popsáno, by vám pomohou pochopit, proč kód funkce produktu orchestrator musí nikdy `await` úlohu krátkodobé: dispečerského vlákna nelze počkejte na její dokončení a jakékoli zpětného volání pomocí této úlohy můžete potenciálně poškodit sledování stav funkce orchestrátoru. Některé kontroly za běhu jsou na místě, chcete-li tomu zabránit.

Pokud chcete další informace o jak trvalý Framework úkol spustí funkcí nástroje orchestrator, je dobré provedete poradit [trvalý úloh zdrojového kódu na Githubu](https://github.com/Azure/durabletask). Zejména, naleznete v tématu [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) a [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o správě instance](durable-functions-instance-management.md)
