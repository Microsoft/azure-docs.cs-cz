---
title: Kontrolní body a opětovného přehrání trvanlivý funkcí – Azure
description: Zjistěte, jak vytváření kontrolních bodů a odpovědi funguje v rozšíření trvanlivý funkce pro Azure Functions.
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
ms.openlocfilehash: 39cdb9b2c6eae9a3176aedc64b8d187e298fdfdd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764566"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Kontrolní body a opětovného přehrání trvanlivý funkcí (Azure Functions)

Jedním z klíčových atributů trvanlivý funkcí je **spolehlivé provádění**. Orchestrator funkce a funkce aktivity mohou být spuštěné v různých virtuálních počítačů v rámci datového centra a tyto virtuální počítače nebo podkladová síťová infrastruktura není spolehlivá 100 %.

Přestože to trvanlivý funkce zajišťuje spolehlivé provádění orchestrations. Dělá to tak pomocí fronty úložiště pro volání funkce jednotky a pravidelně historie provádění vytváření kontrolních bodů do úložiště tabulek (pomocí vzoru návrhu cloudu známé jako [Sourcing událostí](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Tuto historii může být přehraje virtuálním pevném automaticky znovu sestavit v paměti stav funkce produktu orchestrator.

## <a name="orchestration-history"></a>Historie Orchestration

Předpokládejme, že máte následující funkce orchestrator:

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

#### <a name="javascript-functions-v2-only"></a>JavaScript (pouze funkce v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const output = [];
    output.push(yield context.df.callActivityAsync("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivityAsync("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivityAsync("E1_SayHello", "London"));

    return output;
});
```

Na každé `await` (C#) nebo `yield` – příkaz (JavaScript), kontrolní body trvanlivý Framework úloh stav spuštění funkce do úložiště tabulek. Tento stav se, co se označuje jako *orchestration historie*.

## <a name="history-table"></a>Tabulka historie

Obecně řečeno rozhraní trvanlivý úkolů provede následující akce u každého kontrolního bodu:

1. Historie provádění uloží do tabulek Azure Storage.
2. Enqueues zprávy pro funkce orchestrator chce vyvolat.
3. Enqueues zprávy pro orchestrator, samotné &mdash; například trvanlivý časovače zprávy.

Po dokončení kontrolního bodu funkce orchestrator je zdarma odeberou z paměti, dokud není k dispozici další práci na práci.

> [!NOTE]
> Úložiště Azure neposkytuje žádné záruky transakcí mezi ukládání dat do úložiště tabulek a front. Zpracování chyb, používá zprostředkovatel úložiště odolné funkce *konzistence typu případné* vzory. Tyto vzory Ujistěte se, že žádná data je ztraceno v případě havárie nebo ztráty připojení uprostřed kontrolní bod.

Po dokončení zpracování se v historii funkce uvedena výše vypadá podobně jako následující ve službě Azure Table Storage (zkratka pro účely obrázek):

| PartitionKey (identifikátor InstanceId)                     | Typ události             | Časové razítko               | Vstup | Název             | Výsledek                                                    | Status | 
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
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | "" "Hello Praha!" ""                                      |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "" "Hello Praha!" ""                                       |                     | 
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokio!" ",""Hello Seattle!" ",""Hello Praha!" "]" | Dokončené           | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     | 

Několik poznámky na hodnoty ve sloupcích:
* **PartitionKey**: obsahuje instance ID orchestration.
* **Typ události**: představuje typ události. Může být jedna z následujících typů:
    * **OrchestrationStarted**: funkce orchestrator obnovena ze await nebo je poprvé spuštěna. `Timestamp` Column se používá k naplnění deterministický hodnota [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) rozhraní API.
    * **ExecutionStarted**: funkce orchestrator spuštěna poprvé. Tato událost také obsahuje funkce vstup v `Input` sloupce.
    * **TaskScheduled**: byla naplánována funkce aktivity. Název funkce aktivity zachytí ve `Name` sloupce.
    * **TaskCompleted**: aktivita funkce byla dokončena. Výsledek funkce je v `Result` sloupce.
    * **TimerCreated**: byl vytvořen trvanlivý časovač. `FireAt` Sloupec obsahuje plánovaným časem UTC, kdy vyprší platnost časovač.
    * **TimerFired**: trvanlivý časovač aktivováno.
    * **EventRaised**: externí událost byla odeslána do orchestration instance. `Name` Sloupec zachytí název události a `Input` sloupec zaznamená datové části události.
    * **OrchestratorCompleted**: funkce orchestrator očekáváno.
    * **ContinueAsNew**: funkce orchestrator dokončit a restartuje samotné s nový stav. `Result` Sloupec obsahuje hodnotu, která se používá jako vstup v restartovat instanci.
    * **ExecutionCompleted**: funkce orchestrator byl dokončen (nebo se nezdařilo). Výstupy funkce nebo podrobnosti o chybě jsou uložené v `Result` sloupce.
* **Časové razítko**: časové razítko UTC Historie událostí.
* **Název**: název funkce, která byla vyvolána.
* **Vstupní**: vstup funkce s formátem JSON.
* **Výsledek**: výstup funkce; to znamená, hodnoty.

> [!WARNING]
> I když je užitečný jako nástroj pro ladění, nemusíte vytvářet žádné závislosti v této tabulce. Se může změnit při zpracovaní rozšíření trvanlivý funkce.

Pokaždé, když funkce obnoví ze `await`, rozhraní trvanlivý úkolů znovu spustí funkci orchestrator od začátku. Na každý znovu spustit, je zajímají historie provádění určit, zda má aktuální asynchronní operace umístíte.  Pokud operace byla provedena, rozhraní okamžitě replays výstup této operace a přejde k další `await`. Tento proces pokračuje, dokud se přehrány celou historii, v tomto okamžiku jsou všechny místní proměnné ve funkci orchestrator obnoví na předchozí hodnoty.

## <a name="orchestrator-code-constraints"></a>Omezení kód produktu Orchestrator

Chování opětovného přehrání vytvoří omezení na typu kód, který může být napsán v orchestrator funkce:

* Kód produktu Orchestrator musí být **deterministickou**. To budou přehrány víckrát a musí mít stejný výsledek pokaždé, když. Žádné přímé volání například získat aktuální datum a čas, získat náhodná čísla, Generovat náhodné identifikátory GUID nebo volání do vzdálené koncové body.

  Pokud kód orchestrator musí získat aktuální datum a čas, měla by používat [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) rozhraní API, které je bezpečný pro opakování.

  Nedeterministické operace je třeba provést ve funkcích aktivity. To zahrnuje interakce s jiných vstupních nebo výstupních vazby. To zajišťuje, že veškeré Nedeterministický hodnoty bude generovaným jednou na první spuštění a uložen do historie provádění. Dalších spuštěních pak bude automaticky používat uložena hodnota.

* Orchestrator kód by měl být **neblokující**. Například to znamená žádné vstupně-výstupních operací a bez volání `Thread.Sleep` nebo ekvivalentní rozhraní API.

  Pokud orchestrator potřebuje zpoždění, můžete použít [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) rozhraní API.

* Kód Orchestrator musí **nikdy zahájena všechny asynchronní operace** s výjimkou pomocí [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) rozhraní API. Například Ne `Task.Run`, `Task.Delay` nebo `HttpClient.SendAsync`. Trvanlivý Framework úloh provede orchestrator kód na jedno vlákno a nemůžou komunikovat s ostatními vlákny, které mohou být naplánovány podle jiných asynchronní rozhraní API.

* **Nekonečné smyčky je nutno** v kódu produktu orchestrator. Protože trvanlivý úloh Framework uloží historie provádění v průběhu funkce orchestration, nekonečné smyčce by mohl způsobit instanci orchestrator do dostatek paměti. Pro scénáře nekonečná smyčka pomocí rozhraní API, jako například [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) restartujte spuštění funkce a zrušení předchozí historie provádění.

Při zdánlivě složitý na první, v praxi, které nejsou těžko postupujte podle těchto omezení. Trvanlivý Framework úloh pokouší rozpoznat porušení výše uvedených pravidel a vyvolá výjimku `NonDeterministicOrchestrationException`. Ale toto chování zjišťování je typu best effort a nesmí na ní závisí.

> [!NOTE]
> Všechna tato pravidla se vztahují pouze na funkce, které jsou aktivovány `orchestrationTrigger` vazby. Aktivita funkce aktivované `activityTrigger` vazby a funkce, které používají `orchestrationClient` vazby, mít žádné takových omezení.

## <a name="durable-tasks"></a>Trvanlivý úlohy

> [!NOTE]
> Tato část popisuje podrobnosti implementace interní trvanlivý Framework úloh. Aniž by věděly, tyto informace můžete použít trvanlivý funkce. Je určena pouze k vám pomůže porozumět chování opětovného přehrání.

Úlohy, které můžete bezpečně očekávaná orchestrator funkcí se někdy označují jako *trvanlivý úlohy*. Toto jsou úlohy, které se vytváří a spravuje trvanlivý Framework úloh. Příklady jsou úlohy vrácený `CallActivityAsync`, `WaitForExternalEvent`, a `CreateTimer`.

Tyto *trvanlivý úlohy* interně jsou spravovány pomocí seznamu `TaskCompletionSource` objekty. Během opětovného přehrání tyto úlohy vytvořeny jako součást provádění kódu orchestrator a proběhne podle dispečera zobrazí odpovídající události historie. To je vše na jednom synchronně pomocí jedním vláknem, dokud má byla přehrány veškerá historie. Všechny trvanlivý úlohy, které nebyly dokončeny na konci historie opakování má provádět příslušné akce. Zpráva může být například zařazených do fronty zavolat funkci aktivity.

Chování při spuštění v rámci popsaného by vám pomohou pochopit, proč kód funkce orchestrator musí nikdy `await` úlohu krátkodobé: než je vlákno dispečera nemůže čekat na její dokončení a všechny zpětného volání pomocí této úlohy může potenciálně dojít k poškození sledování stav funkce produktu orchestrator. Některé kontroly runtime jsou na místě, chcete-li tomu zabránit.

Pokud vás zajímají další informace o tom, jak rozhraní trvanlivý úloh provede orchestrator funkce, nejlepším krokem je prostudovat [trvanlivý úloh zdrojového kódu na Githubu](https://github.com/Azure/durabletask). Konkrétně, najdete v části [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) a [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o instanci správy](durable-functions-instance-management.md)
