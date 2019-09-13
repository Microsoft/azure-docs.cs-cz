---
title: Trvalé omezení kódu Orchestrator – Azure Functions
description: Opětovné přehrání a omezení kódu funkce orchestrace pro Azure Durable Functions.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: azfuncdf
ms.openlocfilehash: 87851a4879760c76950f765d05de4662ecb04bea
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935765"
---
# <a name="orchestrator-function-code-constraints"></a>Omezení kódu funkce nástroje Orchestrator

Durable Functions je rozšíření [Azure Functions](../functions-overview.md) , které umožňuje sestavovat stavové aplikace. [Funkci Orchestrator](durable-functions-orchestrations.md) můžete použít k orchestraci provádění dalších trvalých funkcí v rámci aplikace Function App. Funkce nástroje Orchestrator jsou stavová, spolehlivá a potenciálně dlouhodobě spuštěná.

## <a name="orchestrator-code-constraints"></a>Omezení kódu Orchestrator

Funkce Orchestrator používají k zajištění spolehlivého provádění a zachování stavu místní proměnné použití [zdroje událostí](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) . Chování při opětovném [přehrání](durable-functions-orchestrations.md#reliability) kódu Orchestrator vytvoří omezení pro typ kódu, který můžete zapsat do funkce Orchestrator. Například kód Orchestrator musí být *deterministický*.  Funkce nástroje Orchestrator budou znovu přehrány několikrát a je nutné pokaždé vyvolat stejný výsledek.

V následujících částech jsou uvedeny některé jednoduché pokyny pro zajištění deterministického kódu.

### <a name="using-deterministic-apis"></a>Použití deterministického rozhraní API

Funkce nástroje Orchestrator jsou bezplatné pro volání libovolného rozhraní API, které chtějí v jejich cílovém jazyce. Je však důležité, aby funkce nástroje Orchestrator volaly pouze *deterministické* rozhraní API. *Deterministické rozhraní API* je rozhraní API, které vždycky vrací přesně stejnou hodnotu pro daný vstup, bez ohledu na to, kdy nebo jak často se volá.

Níže jsou uvedeny některé příklady rozhraní API, která by se měla vyhnout, protože *nejsou deterministické.* Tato omezení platí pouze pro funkce nástroje Orchestrator. Jiné typy funkcí nemají taková omezení.

| Kategorie rozhraní API | Reason | Alternativní řešení |
| ------------ | ------ | ---------- |
| Data a časy  | Rozhraní API, která vrací _aktuální_ datum nebo čas, jsou nedeterministické, protože vracené hodnoty se pro každé přehrání liší. | Použijte rozhraní API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.NET) `currentUtcDateTime` nebo (JavaScript), které je bezpečné pro opětovné přehrání. |
| GUID/identifikátory UUID  | Rozhraní API, která vracejí _náhodný_ identifikátor GUID/UUID, nejsou deterministické, protože vygenerovaná hodnota bude pro každé přehrání odlišná. | K bezpečnému vygenerování náhodných `newGuid` identifikátorů GUID použijte rozhraní [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.NET) nebo (JavaScript). |
| Náhodná čísla | Rozhraní API, která vracejí náhodná čísla, nejsou deterministické, protože vygenerovaná hodnota se bude pro každé přehrání lišit. | Použijte funkci aktivity k vrácení náhodných čísel do orchestrace. Vrácené hodnoty funkcí aktivity jsou pro opětovné přehrání vždy bezpečné. |
| Vazby | Vstupní a výstupní vazby obvykle dělají vstupně-výstupní operace a nejsou deterministické. I [klient orchestrace](durable-functions-bindings.md#orchestration-client) a vazby [klienta entity](durable-functions-bindings.md#entity-client) nesmí být použity přímo funkcí nástroje Orchestrator. | Použijte vstupní a výstupní vazby v rámci funkcí klienta nebo aktivity. |
| Síť | Síťová volání zahrnují externí systémy a nejsou deterministické. | K zajištění síťových volání použijte funkce aktivity. Pokud potřebujete provést volání HTTP z funkce Orchestrator, máte také možnost použít [trvalá rozhraní API http](durable-functions-http-features.md#consuming-http-apis). |
| Blokující rozhraní API | Blokování rozhraní API, `Thread.Sleep` například (.NET) nebo jiných podobných rozhraní API, může způsobit problémy s výkonem a škálováním pro funkce nástroje Orchestrator a je třeba se jim vyhnout. V plánu Azure Functions spotřeby můžou dokonce vést k zbytečným poplatkům za spuštění. | Používejte alternativy k blokování rozhraní API, pokud jsou k `CreateTimer` dispozici, například k zavedení zpoždění při provádění orchestrace. [Trvalá zpoždění časovače](durable-functions-timers.md) se nepočítají směrem k době provádění funkce Orchestrator. |
| Asynchronní rozhraní API | Kód Orchestrator nesmí **iniciovat žádnou asynchronní operaci** s výjimkou použití [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) rozhraní API DurableOrchestrationContext `context.df` nebo rozhraní API objektu. Například ne `Task.Run`, `Task.Delay` `setTimeout()` nebo `HttpClient.SendAsync` v rozhraní .NET nebo`setInterval()` v jazyce JavaScript. Prostředí trvalého úkolu spouští kód Orchestrator v jednom vlákně a nemůže pracovat s jinými vlákny, které by mohly být naplánovány jinými asynchronními rozhraními API. | Funkce Orchestrator by měla provést pouze *trvalá* asynchronní volání. Všechna ostatní asynchronní volání rozhraní API by se měla provádět z funkcí aktivity. |
| Asynchronní funkce JavaScriptu | Funkce nástroje JavaScript Orchestrator nemůže `async` být způsobena tím, že modul runtime Node. js nezaručuje, že asynchronní funkce budou deterministické. | Funkce nástroje JavaScript Orchestrator musí být deklarovány jako synchronní funkce generátoru. |
| Rozhraní API pro dělení na vlákna | Prostředí trvalého úkolu spouští kód Orchestrator v jednom vlákně a nemůže pracovat s jinými vlákny. Zavedení nových vláken do provádění orchestrace může vést k nedeterministickému spuštění nebo zablokování. | Rozhraní API pro dělení na vlákna by se neměla ve funkcích Orchestrator používat téměř nikdy. Pokud jsou potřeba, měly by být omezené na funkce aktivity. |
| Statické proměnné | Nekonstantní statické proměnné by se měly vyhnout ve funkcích nástroje Orchestrator, protože jejich hodnoty se mohou v průběhu času měnit, což vede k nedeterministickému chování při spuštění. | Použijte konstanty nebo omezte použití statických proměnných na funkce aktivity. |
| Proměnné prostředí | Nepoužívejte proměnné prostředí ve funkcích nástroje Orchestrator. Jejich hodnoty se mohou v průběhu času měnit, což vede k nedeterministickému chování při spuštění. | Na proměnné prostředí se musí odkazovat jenom z funkcí klienta nebo funkcí aktivit. |
| Nekonečné smyčky | Nekonečná smyčka by se měla vyhnout ve funkcích nástroje Orchestrator. Nekonečná smyčka úlohy ukládá historii spouštění, protože funkce orchestrace pokračuje, takže nekonečná smyčka může způsobit nedostatek paměti pro instanci nástroje Orchestrator. | V případě scénářů nekonečné smyčky použijte rozhraní API, jako je [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) ( `continueAsNew` .NET) nebo (JavaScript), a restartujte provádění funkce a zahoďte předchozí historii spuštění. |

I když se tato omezení můžou jevit jako těžké, v praxi je nemusíte sledovat. Prostředí trvalého úkolu se pokusí zjistit porušení výše uvedených pravidel a vyvolá `NonDeterministicOrchestrationException`. Toto chování při detekci je ale nejlepší úsilí a na něj byste neměli záviset.

## <a name="versioning"></a>Správa verzí

Trvalá orchestrace se může spouštět nepřetržitě po dnech, měsících, rocích nebo dokonce [eternally](durable-functions-eternal-orchestrations.md). Jakékoli aktualizace kódu provedené v Durable Functionsch aplikacích, které mají vliv na nedokončené orchestrace, mohou poškodit chování při přehrání. Je proto důležité při provádění aktualizací kódu pečlivě naplánovat. Podrobnější popis způsobu verze kódu naleznete v článku [Správa verzí](durable-functions-versioning.md) .

## <a name="durable-tasks"></a>Trvalé úkoly

> [!NOTE]
> Tato část popisuje interní implementace podrobností o trvalém rozhraní úlohy. Durable Functions můžete použít bez znalosti těchto informací. Je určena jenom k tomu, aby vám pomohla pochopit chování při přehrávání.

Úkoly, které je možné bezpečně očekávat v rámci funkcí nástroje Orchestrator, se občas označují jako *odolné úlohy*. Tyto úlohy jsou vytvořeny a spravovány v rámci systému trvalých úloh. Příklady jsou úlohy vracené funkcemi `CallActivityAsync`, `WaitForExternalEvent`a `CreateTimer` v rámci funkce .NET Orchestrator.

Tyto *odolné úkoly* jsou interně spravovány pomocí seznamu `TaskCompletionSource` objektů v rozhraní .NET. Během opakovaného přehrávání se tyto úlohy vytvoří jako součást provádění kódu Orchestrator a jsou dokončeny, protože dispečer vytvoří výčet odpovídajících událostí historie. Provádění je prováděno synchronně pomocí jednoho vlákna, dokud nebude všechna Historie znovu přehrána. Jakékoli trvalé úkoly, které nejsou dokončeny na konci historie, mají provedeny příslušné akce. Například zpráva může být zařazena do fronty pro volání funkce aktivity.

Chování, které se tady popisuje, by vám mělo porozumět tomu, proč `await` kód `yield` funkce Orchestrator nesmí nikdy nebo být netrvalá úloha: dispečerský podproces nemůže počkat na dokončení a jakékoli zpětné volání této úlohy by mohlo potenciálně trvat. byl poškozen stav sledování funkce nástroje Orchestrator. K pokusu o zjištění těchto porušení jsou k dismístě některé kontroly za běhu.

Pokud chcete získat další informace o tom, jak architektura trvalého úlohy spouští funkce nástroje Orchestrator, je nejlepší postup, jak se obrátit na [trvalý zdrojový kód úlohy na GitHubu](https://github.com/Azure/durabletask). Zejména viz [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) a [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Naučte se volat dílčí orchestrace.](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Informace o tom, jak zpracovávat správu verzí](durable-functions-versioning.md)
