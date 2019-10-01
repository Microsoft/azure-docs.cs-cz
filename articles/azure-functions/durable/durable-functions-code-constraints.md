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
ms.openlocfilehash: 66e33874c3cfe8f9d2594489b3165f81b2ce84ab
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694827"
---
# <a name="orchestrator-function-code-constraints"></a>Omezení kódu funkce nástroje Orchestrator

Durable Functions je rozšíření [Azure Functions](../functions-overview.md) , které umožňuje sestavovat stavové aplikace. [Funkci Orchestrator](durable-functions-orchestrations.md) můžete použít k orchestraci provádění dalších trvalých funkcí v rámci aplikace Function App. Funkce nástroje Orchestrator jsou stavová, spolehlivá a potenciálně dlouhodobě spuštěná.

## <a name="orchestrator-code-constraints"></a>Omezení kódu orchestrátoru

Funkce Orchestrator používají k zajištění spolehlivého provádění a zachování stavu místní proměnné použití [zdroje událostí](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) . Chování při opětovném [přehrání](durable-functions-orchestrations.md#reliability) kódu Orchestrator vytvoří omezení pro typ kódu, který můžete zapsat do funkce Orchestrator. Například funkce Orchestrator musí být *deterministické*: funkce Orchestrator bude opakovaně přehrána několikrát a ta musí mít stejný výsledek pokaždé.

### <a name="using-deterministic-apis"></a>Použití deterministického rozhraní API

V této části najdete některé jednoduché pokyny, které vám pomohou zajistit, aby byl kód deterministický.

Funkce Orchestrator mohou volat libovolné rozhraní API v jejich cílových jazycích. Je však důležité, aby funkce nástroje Orchestrator volaly pouze deterministické rozhraní API. *Deterministické rozhraní API* je rozhraní API, které vždycky vrací stejnou hodnotu pro stejný vstup, bez ohledu na to, kdy nebo jak často je volána.

V následující tabulce jsou uvedeny příklady rozhraní API, se kterými byste se měli vyhnout, protože *nejsou deterministické.* Tato omezení platí pouze pro funkce nástroje Orchestrator. Jiné typy funkcí nemají taková omezení.

| Kategorie rozhraní API | Důvod | Alternativní řešení |
| ------------ | ------ | ---------- |
| Data a časy  | Rozhraní API, která vrací aktuální datum nebo čas, jsou nedeterministické, protože vrácená hodnota je pro každé přehrání jiná. | Použijte rozhraní [**CurrentUtcDateTime**](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API v rozhraní .NET nebo rozhraní **CurrentUtcDateTime** API v JavaScriptu, které je bezpečné pro opětovné přehrání. |
| GUID a identifikátory UUID  | Rozhraní API, která vracejí náhodný identifikátor GUID nebo UUID, jsou nedeterministické, protože vygenerovaná hodnota se pro každé přehrání liší. | K bezpečnému generování náhodných identifikátorů GUID použijte [**NewGuid**](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) v .NET nebo **NewGuid** v JavaScriptu. |
| Náhodná čísla | Rozhraní API, která vrací náhodná čísla, jsou nedeterministické, protože vygenerovaná hodnota se pro každé přehrání liší. | Použijte funkci aktivity k vrácení náhodných čísel do orchestrace. Vrácené hodnoty funkcí aktivity jsou pro opětovné přehrání vždy bezpečné. |
| Vazby | Vstupní a výstupní vazby obvykle dělají vstupně-výstupní operace a jsou nedeterministické. Funkce Orchestrator nesmí přímo používat ani [klient orchestrace](durable-functions-bindings.md#orchestration-client) a vazby [klienta entit](durable-functions-bindings.md#entity-client) . | Použijte vstupní a výstupní vazby v rámci funkcí klienta nebo aktivity. |
| Síť | Síťová volání zahrnují externí systémy a nedeterministické. | K zajištění síťových volání použijte funkce aktivity. Pokud potřebujete provést volání HTTP z funkce Orchestrator, můžete použít také [trvalá rozhraní API http](durable-functions-http-features.md#consuming-http-apis). |
| Blokující rozhraní API | Blokující rozhraní API jako **vlákno. Režim spánku** v rozhraní .NET a podobná rozhraní API mohou způsobit problémy s výkonem a škálováním pro funkce nástroje Orchestrator a je třeba se jim vyhnout. V plánu Azure Functions spotřeby můžou dokonce vést k zbytečným poplatkům za modul runtime. | Používejte alternativy k blokování rozhraní API, když jsou k dispozici. Například použijte **CreateTimer** k zavedení zpoždění při provádění orchestrace. [Trvalá zpoždění časovače](durable-functions-timers.md) se nepočítají směrem k době provádění funkce Orchestrator. |
| Asynchronní rozhraní API | Kód Orchestrator nesmí spustit žádnou asynchronní operaci s výjimkou použití rozhraní [**DurableOrchestrationContext**](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API nebo rozhraní API **kontextu. DF** objektu. V jazyce JavaScript nemůžete například použít **Task. Run**, **Task. Delay**a **HttpClient. SendAsync** v rozhraních .NET nebo **setTimeout** a **setInterval** . Prostředí trvalého zpracování úloh spouští kód Orchestrator v jednom vlákně. Nemůže komunikovat s jinými vlákny, které by mohly být volány jinými asynchronními rozhraními API. | Funkce Orchestrator by měla dělat jenom trvalá asynchronní volání. Funkce aktivity by měly provádět jakákoli další asynchronní volání rozhraní API. |
| Asynchronní funkce JavaScriptu | Funkce nástroje JavaScript Orchestrator nemůžete deklarovat jako **asynchronní** , protože modul runtime Node. js nezaručuje, že asynchronní funkce jsou deterministické. | Deklarujete funkce nástroje JavaScript Orchestrator jako synchronní funkce generátoru. |
| Rozhraní API pro dělení na vlákna | Prostředí trvalého zpracování úloh spouští kód Orchestrator v jednom vlákně a nemůže pracovat s jinými vlákny. Zavedení nových vláken do provádění orchestrace může vést k nedeterministickému spuštění nebo zablokování. | Funkce Orchestrator by téměř nikdy neměly používat rozhraní API pro dělení na vlákna. Pokud jsou taková rozhraní API nezbytná, omezte jejich použití jenom na funkce aktivity. |
| Statické proměnné | Nepoužívejte nekonstantní statické proměnné ve funkcích nástroje Orchestrator, protože jejich hodnoty se mohou v průběhu času měnit, což vede k nedeterministickému chování za běhu. | Použijte konstanty nebo omezte použití statických proměnných na funkce aktivity. |
| Proměnné prostředí | Nepoužívejte proměnné prostředí ve funkcích nástroje Orchestrator. Jejich hodnoty se můžou v průběhu času měnit, což vede k nedeterministickému chování za běhu. | Na proměnné prostředí se musí odkazovat jenom v rámci funkcí klienta nebo funkcí aktivity. |
| Nekonečné smyčky | Vyhněte se nekonečným smyčkám ve funkcích nástroje Orchestrator. Vzhledem k tomu, že architektura trvalého úlohy ukládá historii spouštění, protože funkce orchestrace pokračuje, nekonečná smyčka může způsobit nedostatek paměti z důvodu instance nástroje Orchestrator. | V případě scénářů nekonečné smyčky použijte rozhraní API, jako je [**ContinueAsNew**](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) v .NET nebo **ContinueAsNew** v JavaScriptu, pro restartování funkce a zahození předchozí historie spouštění. |

I když použití těchto omezení může být v prvé době obtížné, v praxi je budete moct snadno sledovat.

Prostředí trvalého úkolu se pokusí zjistit porušení předchozích pravidel. Pokud zjistí porušení, rozhraní vyvolá výjimku **NonDeterministicOrchestrationException** . Toto chování při detekci ale nezpůsobí zachycení všech porušení a neměli byste na ní záviset.

## <a name="versioning"></a>Správa verzí

Trvalá orchestrace může běžet nepřetržitě pro dny, měsíce, roky nebo dokonce [eternally](durable-functions-eternal-orchestrations.md). Jakékoli aktualizace kódu provedené v aplikacích Durable Functions, které ovlivňují nedokončené orchestraci, můžou poškodit chování při opětovném přehrání Orchestration. To je důvod, proč je důležité při provádění aktualizací kódu pečlivě naplánovat. Podrobnější popis způsobu verze kódu naleznete v [článku Správa verzí](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Trvalé úkoly

> [!NOTE]
> Tato část popisuje interní implementace podrobností o trvalém rozhraní úlohy. Nemusíte-li znát tyto informace, můžete použít trvalé funkce. Je určena jenom k tomu, aby vám pomohla pochopit chování při přehrávání.

Úlohy, které můžou bezpečně čekat na funkce nástroje Orchestrator, se občas označují jako *odolné úkoly*. Rozhraní odolné úlohy vytváří a spravuje tyto úlohy. Příklady jsou úlohy vracené **CallActivityAsync**, **WaitForExternalEvent**a **CreateTimer** ve funkcích .NET Orchestrator.

Tyto odolné úkoly jsou interně spravovány seznamem objektů **TaskCompletionSource** v rozhraní .NET. Během opakovaného přehrávání se tyto úkoly vytvoří v rámci provádění kódu Orchestrator. Jsou dokončeny, protože dispečer vytvoří výčet odpovídajících událostí historie.

Úkoly jsou spouštěny synchronně pomocí jednoho vlákna, dokud se veškerá historie znovu nepřehraje. Trvalé úkoly, které nejsou dokončeny na konci historie, mají provedeny příslušné akce. Například zpráva může být zařazena do fronty pro volání funkce aktivity.

Popis tohoto oddílu chování za běhu by vám měl porozumět tomu, proč funkce Orchestrator nemůže použít funkci **await** nebo **yield** v netrvalé úloze. Existují dva důvody: dispečerský podproces nemůže počkat na dokončení úlohy a jakékoli zpětné volání této úlohy může poškodit stav sledování funkce Orchestrator. K detekci těchto porušení jsou k dismístě některé kontroly za běhu.

Další informace o tom, jak prostředí trvalé úlohy spouští funkce nástroje Orchestrator, najdete v článku [trvalý zdrojový kód úlohy na GitHubu](https://github.com/Azure/durabletask). Zejména viz [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) a [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se volat dílčí orchestrace.](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Informace o tom, jak zpracovávat správu verzí](durable-functions-versioning.md)
