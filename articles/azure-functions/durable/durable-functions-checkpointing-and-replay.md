---
title: Kontrolní body a přehrávání v Durable Functions – Azure
description: Přečtěte si, jak se v rozšíření Durable Functions pro Azure Functions funguje kontrolní bod a odpověď.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 79cb276f121c351a9954994038d9d826819edf5d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087458"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Kontrolní body a opakované přehrání v Durable Functions (Azure Functions)

Jedním z klíčových atributů Durable Functions je **spolehlivé provádění**. Funkce a funkce nástroje Orchestrator mohou běžet na různých virtuálních počítačích v rámci datového centra. Tyto virtuální počítače nebo základní síťové infrastruktury nejsou 100% Reliable.

Navzdory tomu Durable Functions zajišťuje spolehlivé provádění orchestrace. K tomu slouží fronty úložiště k řízení vyvolání funkcí a pravidelnému vytváření kontrolních bodů historie spouštění do tabulek úložiště (pomocí vzoru návrhu cloudu známého jako [zdroje událostí](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Tato historie se pak může přehrát, aby automaticky znovu vytvořila stav v paměti funkce Orchestrator.

## <a name="orchestration-history"></a>Historie orchestrace

Předpokládejme, že máte následující funkci Orchestrator:

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

V každém `await` příkazuC#() `yield` nebo (JavaScript), trvalá architektura pro úlohy kontrolní stav provádění funkce do tabulkového úložiště. Tento stav je označován jako *Historie orchestrace*.

## <a name="history-table"></a>Tabulka historie

Obecně řečeno, trvalá architektura pro úlohy v každém kontrolním bodu provede následující:

1. Uloží historii spouštění do Azure Storage tabulek.
2. Zařazování zpráv do fronty pro funkce, které chce Orchestrator vyvolat.
3. Ve frontě zařazování zpráv pro &mdash; nástroj Orchestrator, například trvalé zprávy časovače.

Po dokončení kontrolního bodu je možné odebrat funkci Orchestrator z paměti, dokud nebude k dispozici více práce.

> [!NOTE]
> Azure Storage neposkytuje žádné transakční záruky mezi uložením dat do úložiště tabulek a front. Pro zpracování selhání používá poskytovatel úložiště Durable Functions konečné vzorce *konzistence* . Tyto vzory zajistí, že nedojde ke ztrátě dat v případě selhání nebo ztráty připojení uprostřed kontrolního bodu.

Po dokončení bude historie dříve zobrazených funkcí vypadat přibližně takto v Azure Table Storage (pro ilustraci je zkrácený):

| PartitionKey (InstanceId)                     | Typ události             | Timestamp               | Vstup | Name             | Výsledek                                                    | Stav |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | "" "Hello Tokio!" "                                        |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | "" "Hello Praha!" "                                      |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "" "Hello Londýn!" "                                       |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[" "Hello Tokio!" "," "Hello Seattle!" "," "Hello Londýn!" "]" | Dokončeno           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |

Několik poznámek na hodnotách sloupců:

* **PartitionKey**: Obsahuje ID instance orchestrace.
* **Typ události**: Představuje typ události. Může být jeden z následujících typů:
  * **OrchestrationStarted**: Funkce Orchestrator obnovila z await nebo je spuštěná poprvé. Sloupec se používá k naplnění deterministické hodnoty rozhraní CurrentUtcDateTime API. [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) `Timestamp`
  * **ExecutionStarted**: Funkce nástroje Orchestrator zahájila první spuštění. Tato událost také obsahuje vstup funkce ve `Input` sloupci.
  * **TaskScheduled**: Naplánovala se funkce aktivity. Název funkce aktivity je zachycen ve `Name` sloupci.
  * **TaskCompleted**: Funkce aktivity je dokončená. Výsledek funkce je ve `Result` sloupci.
  * **TimerCreated**: Byl vytvořen trvalý časovač. `FireAt` Sloupec obsahuje plánovaný čas UTC, kdy vyprší platnost časovače.
  * **TimerFired**: Byl aktivován trvalý časovač.
  * **EventRaised**: Do instance Orchestration byla odeslána externí událost. Sloupec zachytí název události `Input` a sloupec zachycuje datovou část události. `Name`
  * **OrchestratorCompleted**: Očekává se funkce Orchestrator.
  * **ContinueAsNew**: Funkce Orchestrator se dokončila a sama se restartovala s novým stavem. `Result` Sloupec obsahuje hodnotu, která se používá jako vstup v restartované instanci.
  * **ExecutionCompleted**: Funkce Orchestrator se úspěšně dokončila (nebo selhala). Výstupy funkce nebo podrobnosti o chybě jsou uloženy ve `Result` sloupci.
* **Časové razítko**: Časové razítko UTC události historie.
* **Název**: Název funkce, která byla vyvolána.
* **Vstup**: Vstup funkce ve formátu JSON.
* **Výsledek**: Výstup funkce; To znamená, že návratová hodnota.

> [!WARNING]
> I když je to užitečné jako ladicí nástroj, neprovádějte žádnou závislost v této tabulce. Může se změnit, jak se vyvíjí rozšíření Durable Functions.

Pokaždé, když se funkce obnoví z `await` rozhraníC#() `yield` nebo (JavaScript), reaktivuje se znovu funkce Orchestrator od nuly. Při každém opakovaném spuštění prochází historii spouštění a určí, zda byla provedena aktuální asynchronní operace.  Pokud tato operace proběhla, rozhraní přehraje výstup této operace okamžitě a přesune se k dalšímu `await` (C#) nebo `yield` (JavaScriptu). Tento proces pokračuje, dokud nebude celá historie znovu přehrána, přičemž všechny místní proměnné ve funkci Orchestrator budou obnoveny na jejich předchozí hodnoty.

## <a name="orchestrator-code-constraints"></a>Omezení kódu Orchestrator

Chování při opětovném přehrání vytvoří omezení pro typ kódu, který lze zapsat do funkce Orchestrator:

* Kód Orchestrator musí být **deterministický**. Bude opakovaně přehrána několikrát a je nutné pokaždé vyvolat stejný výsledek. Například žádná přímá volání pro získání aktuálního data a času, získání náhodných čísel, generování náhodných identifikátorů GUID nebo volání vzdálených koncových bodů.

  Pokud kód Orchestrator potřebuje získat aktuální datum a čas, měl by používat rozhraní API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.NET) nebo `currentUtcDateTime` (JavaScript), které je bezpečné pro opětovné přehrání.

  Pokud kód Orchestrator potřebuje vygenerovat náhodný identifikátor GUID, měl by používat rozhraní API [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.NET), které je bezpečné pro opětovné přehrání, nebo delegovat generování identifikátoru GUID na funkci Activity (JavaScript), jako v tomto příkladu:

  ```javascript
  const uuid = require("uuid/v1");

  module.exports = async function(context) {
    return uuid();
  }
  ```

  Nedeterministické operace se musí provádět ve funkcích aktivit. To zahrnuje jakoukoli interakci s dalšími vstupními nebo výstupními vazbami. Tím se zajistí, že se při prvním spuštění vygeneruje všechny nedeterministické hodnoty a uloží se do historie spouštění. Další vykonání se pak automaticky použije uložená hodnota.

* Kód Orchestrator by neměl být **zablokován**. Například to znamená, že nejsou žádné vstupně-výstupní operace ani žádná `Thread.Sleep` volání rozhraní (.NET) nebo ekvivalentní rozhraní API.

  Pokud se nástroj Orchestrator potřebuje zpozdit, může použít rozhraní API [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) (.NET) nebo `createTimer` (JavaScript).

* Kód Orchestrator nesmí **iniciovat žádnou asynchronní operaci** s výjimkou použití [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) rozhraní API DurableOrchestrationContext `context.df` nebo rozhraní API objektu. Například ne `Task.Run`, `Task.Delay` `setTimeout()` nebo `HttpClient.SendAsync` v rozhraní .NET nebo`setInterval()` v jazyce JavaScript. Prostředí trvalého úkolu spouští kód Orchestrator v jednom vlákně a nemůže pracovat s jinými vlákny, které by mohly být naplánovány jinými asynchronními rozhraními API. Pokud k tomu dojde `InvalidOperationException` , je vyvolána výjimka.

* **Nekonečná smyčka by se měla** v kódu Orchestrator vyhnout. Vzhledem k tomu, že architektura trvalého úlohy ukládá historii spouštění, protože funkce orchestrace pokračuje, může nekonečná smyčka způsobit nedostatek paměti z důvodu instance nástroje Orchestrator. V případě scénářů nekonečné smyčky použijte rozhraní API, jako je [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) ( `continueAsNew` .NET) nebo (JavaScript), a restartujte provádění funkce a zahoďte předchozí historii spuštění.

* Funkce nástroje JavaScript Orchestrator nemohou `async`být. Musí být deklarovány jako synchronní funkce generátoru.

I když se tato omezení můžou jevit jako těžké, v praxi je nemusíte sledovat. Prostředí trvalého úkolu se pokusí zjistit porušení výše uvedených pravidel a vyvolá `NonDeterministicOrchestrationException`. Toto chování při detekci je ale nejlepší úsilí a na něj byste neměli záviset.

> [!NOTE]
> Všechna tato pravidla platí pouze pro funkce aktivované `orchestrationTrigger` vazbou. Funkce aktivity aktivované `activityTrigger` vazbou a funkce, které `orchestrationClient` používají vazbu, nemají taková omezení.

## <a name="durable-tasks-net"></a>Trvalé úkoly (.NET)

> [!NOTE]
> Tato část popisuje interní implementace podrobností o trvalém rozhraní úlohy. Durable Functions můžete použít bez znalosti těchto informací. Je určena jenom k tomu, aby vám pomohla pochopit chování při přehrávání.

Úkoly, které je možné bezpečně očekávat v rámci funkcí nástroje Orchestrator, se občas označují jako *odolné úlohy*. Jedná se o úlohy, které jsou vytvářeny a spravovány trvalým architekturou úloh. Příklady jsou úlohy vracené `CallActivityAsync`, `WaitForExternalEvent`a `CreateTimer`.

Tyto *odolné úkoly* jsou interně spravovány pomocí seznamu `TaskCompletionSource` objektů. Během opakovaného přehrávání se tyto úlohy vytvoří jako součást provádění kódu Orchestrator a jsou dokončeny, protože dispečer vytvoří výčet odpovídajících událostí historie. To vše se provádí synchronně pomocí jednoho vlákna, dokud se veškerá historie znovu nepřehraje. Jakékoli trvalé úkoly, které nejsou dokončeny na konci historie, mají provedeny příslušné akce. Například zpráva může být zařazena do fronty pro volání funkce aktivity.

Chování, které se tady popisuje, by vám mělo porozumět tomu, proč `await` kód funkce Orchestrator nesmí nikdy Nerušit úkol: dispečerský podproces nemůže počkat na jeho dokončení a jakékoli zpětné volání této úlohy by mohlo poškodit sledování. stav funkce Orchestrator K tomu je potřeba provést několik kontrol za běhu.

Pokud chcete získat další informace o tom, jak architektura trvalého úlohy spouští funkce nástroje Orchestrator, je nejlepší postup, jak se obrátit na [trvalý zdrojový kód úlohy na GitHubu](https://github.com/Azure/durabletask). Zejména viz [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) a [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o správě instancí](durable-functions-instance-management.md)
