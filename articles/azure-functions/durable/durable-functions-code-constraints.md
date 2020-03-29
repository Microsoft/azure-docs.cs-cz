---
title: Trvalá omezení kódu orchestratoru – Funkce Azure
description: Opětovné přehrání funkce orchestrace a omezení kódu pro funkce Azure Durable.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4ed604302ca187ad4953e865d68dc73030a37c02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562135"
---
# <a name="orchestrator-function-code-constraints"></a>Omezení kódu funkce Orchestrator

Durable Functions je rozšíření [funkcí Azure,](../functions-overview.md) které vám umožní vytvářet stavové aplikace. [Funkci orchestrator](durable-functions-orchestrations.md) můžete použít k orchestraci provádění dalších trvalých funkcí v rámci aplikace funkce. Funkce orchestratoru jsou stavové, spolehlivé a potenciálně dlouhotrvající.

## <a name="orchestrator-code-constraints"></a>Omezení kódu orchestrátoru

Funkce Orchestrator používají [zdroje událostí](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) k zajištění spolehlivého provádění a k udržení stavu místní proměnné. [Chování přehrání](durable-functions-orchestrations.md#reliability) kódu orchestratoru vytváří omezení typu kódu, který můžete napsat ve funkci orchestrator. Například funkce orchestrator musí být *deterministické*: funkce orchestrator bude přehrána vícekrát a musí pokaždé vytvořit stejný výsledek.

### <a name="using-deterministic-apis"></a>Použití deterministických api

Tato část obsahuje několik jednoduchých pokynů, které pomáhají zajistit, že váš kód je deterministický.

Funkce Orchestrator můžete volat libovolné rozhraní API v jejich cílových jazycích. Je však důležité, aby funkce orchestrator volání pouze deterministické API. *Deterministické rozhraní API* je rozhraní API, které vždy vrací stejnou hodnotu za stejný vstup, bez ohledu na to, kdy a jak často se nazývá.

V následující tabulce jsou uvedeny příklady řešení API, kterým byste se měli vyhnout, protože *nejsou* deterministická. Tato omezení platí pouze pro funkce orchestrator. Jiné typy funkcí nemají taková omezení.

| Kategorie rozhraní API | Důvod | Alternativní řešení |
| ------------ | ------ | ---------- |
| Data a časy  | Api, které vracejí aktuální datum nebo čas, jsou nedeterministické, protože vrácená hodnota se liší pro každé přehrání. | Použijte`CurrentUtcDateTime` rozhraní API v `currentUtcDateTime` rozhraní .NET nebo rozhraní API v Jazyce JavaScript, které jsou bezpečné pro přehrávání. |
| Identifikátory GUID a UUID  | Rozhraní API, která vracejí náhodný identifikátor GUID nebo UUID, jsou nedeterministická, protože vygenerovaná hodnota se pro každé přehrání liší. | Používá `NewGuid` se v `newGuid` rozhraní .NET nebo v jazyce JavaScript k bezpečnému generování náhodných identifikátorů GUID. |
| Náhodná čísla | Api, které vracejí náhodná čísla, jsou nedeterministická, protože generovaná hodnota se pro každé přehrání liší. | Pomocí funkce aktivity vraťte náhodná čísla do orchestraci. Vrácené hodnoty funkcí aktivity jsou vždy bezpečné pro přehrání. |
| Vazby | Vstupní a výstupní vazby obvykle vstupně-výstupní a jsou nedeterministické. Funkce orchestrator nesmí přímo používat ani [orchestraci klienta](durable-functions-bindings.md#orchestration-client) a [entity vazby klienta.](durable-functions-bindings.md#entity-client) | Použijte vstupní a výstupní vazby uvnitř klienta nebo funkce aktivity. |
| Network (Síť) | Síťová volání zahrnují externí systémy a jsou nedeterministická. | Pomocí funkcí aktivity uskutečujte síťová volání. Pokud potřebujete provést volání HTTP z funkce orchestrator, můžete také použít [trvalá http api](durable-functions-http-features.md#consuming-http-apis). |
| Blokování přístupů api | Blokování rozhraní `Thread.Sleep` API, jako je v rozhraní .NET a podobná rozhraní API, může způsobit problémy s výkonem a škálování funkcí orchestratoru a je třeba se jim vyhnout. V plánu Azure Functions Consumption můžou dokonce vést ke zbytečným runtime poplatkům. | Použijte alternativy k blokování rozhraní API, pokud jsou k dispozici. Například použít `CreateTimer` k zavedení zpoždění při provádění orchestrace. [Trvalé zpoždění časovače](durable-functions-timers.md) se nezapočítávají do doby provádění funkce orchestratoru. |
| Asynchronní api | Orchestrator kód nesmí nikdy spustit žádnou `IDurableOrchestrationContext` asynchronní `context.df` operaci s výjimkou pomocí rozhraní API nebo rozhraní API objektu. Nelze `Task.Run`například použít , `Task.Delay`a `HttpClient.SendAsync` v rozhraní `setTimeout` .NET nebo a `setInterval` v jazyce JavaScript. Rozhraní Durable Task Framework spouští kód orchestrator v jednom vlákně. Nemůže pracovat s jinými vlákny, která mohou být volána jinými asynchronními rozhraními API. | Funkce orchestrator by měla provádět pouze trvalé asynchronní volání. Funkce aktivity by měly provádět všechna další asynchronní volání rozhraní API. |
| Asynchronní funkce JavaScriptu | Nelze deklarovat javascriptové orchestrátory funkce jako `async` proto, že modul runtime node.js nezaručuje, že asynchronní funkce jsou deterministické. | Deklarovat JavaScript orchestrator funguje jako synchronní generátor funkce. |
| Rozhraní API pro zřetězení | Architektura durable task framework spouští kód orchestrator v jednom vlákně a nemůže pracovat s jinými vlákny. Zavedení nových podprocesů do provádění orchestrace může mít za následek nedeterministické spuštění nebo zablokování. | Funkce orchestratoru by téměř nikdy neměly používat rozhraní API pro zřetězení. Například v rozhraní .NET `ConfigureAwait(continueOnCapturedContext: false)`se vyhněte použití ; Tím je zajištěno pokračování úlohy spustit na původní `SynchronizationContext`funkci orchestrator . Pokud jsou taková api nezbytná, omezte jejich použití pouze na funkce aktivity. |
| Statické proměnné | Vyhněte se použití nekonstantní statické proměnné v orchestrátoru funkce, protože jejich hodnoty se mohou měnit v průběhu času, výsledkem je nedeterministické chování za běhu. | Použijte konstanty nebo omezte použití statických proměnných na funkce aktivity. |
| Proměnné prostředí | Nepoužívejte proměnné prostředí ve funkcích orchestratoru. Jejich hodnoty se mohou v průběhu času měnit, což vede k nedeterministickému chování za běhu. | Proměnné prostředí musí být odkazovány pouze z funkce klienta nebo funkce aktivity. |
| Nekonečné smyčky | Vyhněte se nekonečné smyčky v orchestrátoru funkcí. Vzhledem k tomu, že architektura durable task framework ukládá historii provádění jako postupovací funkce orchestrace, nekonečná smyčka může způsobit, že instance orchestratoru bude spuštěna bez paměti. | Pro scénáře nekonečné smyčky použijte `ContinueAsNew` rozhraní API `continueAsNew` jako v rozhraní .NET nebo v jazyce JavaScript k restartování spuštění funkce a k zahození předchozí historie spuštění. |

I když použití těchto omezení se může zdát obtížné na první, v praxi jsou snadno sledovat.

Rámec trvalých úloh se pokusí zjistit porušení předchozích pravidel. Pokud zjistí porušení, rozhraní filtru vyvolá **Výjimku UnDeterministicOrchestraationException.** Toto chování zjišťování však nezachytí všechna porušení a nemělo by na něm záviset.

## <a name="versioning"></a>Správa verzí

Trvalá orchestrace může běžet nepřetržitě dny, měsíce, roky nebo dokonce [věčně](durable-functions-eternal-orchestrations.md). Všechny aktualizace kódu provedené v aplikacích Durable Functions, které ovlivňují nedokončené orchestrace, mohou přerušit chování orchestrations při přehrávání. To je důvod, proč je důležité pečlivě plánovat při provádění aktualizací kódu. Podrobnější popis, jak vytvořit verzi kódu, naleznete v [článku správy verzí](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Trvalé úkoly

> [!NOTE]
> Tato část popisuje podrobnosti interní implementace rámce trvalých úloh. Můžete použít trvalé funkce bez znalosti těchto informací. Je určen pouze k pochopení chování přehrání.

Úkoly, které mohou bezpečně čekat ve funkcích orchestrátoru, jsou občas označovány jako *trvalé úkoly*. Rámec truživých úloh vytváří a spravuje tyto úkoly. Příklady jsou úkoly vrácené **funkcemi CallActivityAsync**, **WaitForExternalEvent**a **CreateTimer** ve funkcích orchestrátoru .NET.

Tyto trvalé úkoly jsou interně `TaskCompletionSource` spravovány seznamem objektů v rozhraní .NET. Během přehrávání jsou tyto úkoly vytvořeny jako součást spuštění kódu orchestrator. Jsou hotové jako dispečer vyjmenovává odpovídající události historie.

Úlohy jsou prováděny synchronně pomocí jednoho vlákna, dokud nebude přehrána celá historie. Trvalé úkoly, které nejsou dokončeny do konce historie replay mají příslušné akce provedeny. Například zpráva může být zařazena do fronty pro volání funkce aktivity.

Popis chování za běhu v této části by vám měl pomoci pochopit, proč funkci orchestratoru nelze použít `await` nebo `yield` v trvalé úloze. Existují dva důvody: dispečer vlákno nemůže čekat na dokončení úkolu a jakékoli zpětné volání podle této úlohy může potenciálně poškodit stav sledování funkce orchestrator. Některé kontroly za běhu jsou zavedeny, aby pomohly zjistit tato porušení.

Další informace o tom, jak architektura durable task framework provádí funkce orchestratoru, naleznete ve [zdrojovém kódu trvanlivé úlohy na GitHubu](https://github.com/Azure/durabletask). Viz zejména [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) a [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se vyvolat podorchestrations](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Přečtěte si, jak zpracovat správu verzí](durable-functions-versioning.md)
