---
title: Fakturace odolných funkcí – Funkce Azure
description: Přečtěte si o interním chování trvanlivých funkcí a o tom, jak ovlivňují fakturaci pro funkce Azure.
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 504ef93a0002895bc5662d95ad269c8593170ee2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74233017"
---
# <a name="durable-functions-billing"></a>Fakturace odolných funkcí

[Trvalé funkce](durable-functions-overview.md) se účtují stejným způsobem jako funkce Azure. Další informace najdete v tématu [Azure Functions pricing](https://azure.microsoft.com/pricing/details/functions/).

Při provádění funkcí orchestrator v [plánu Azure](../functions-scale.md#consumption-plan)Functions Consumption , musíte být vědomi některých chování fakturace. Následující části popisují toto chování a jejich účinek podrobněji.

## <a name="orchestrator-function-replay-billing"></a>Účtování funkce Orchestrator

[Funkce orchestratoru](durable-functions-orchestrations.md) může přehrát několikrát po celou dobu životnosti orchestrace. Každý záznam je zobrazen za běhu Azure Functions jako odlišné vyvolání funkce. Z tohoto důvodu v plánu Azure Functions Consumption se vám účtuje za každé přehrání funkce orchestratoru. Jiné typy plánu se neúčtují za přehrávání funkcí orchestratoru.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Čekání a poddajnost ve funkcích orchestrátoru

Když funkce orchestrator čeká na dokončení asynchronní akce pomocí **await** v jazyce C# nebo **yield** v jazyce JavaScript, modul runtime považuje toto konkrétní spuštění za dokončené. Fakturace funkce orchestrator se zastaví v tomto okamžiku. Neobnoví se, dokud se nepřehraje další funkce orchestrátoru. Neúčtují se vám žádný čas strávený čekáním nebo poddajností funkce orchestrátoru.

> [!NOTE]
> Funkce volající jiné funkce je některými považovány za antipattern. Důvodem je problém označovaný jako _dvojité fakturace_. Když funkce volá jinou funkci přímo, obě spustit ve stejnou dobu. Volaná funkce aktivně spouštějící kód, zatímco volající funkce čeká na odpověď. V takovém případě musíte zaplatit za čas, který volající funkce stráví čekáním na spuštění volané funkce.
>
> Ve funkcích orchestratoru neexistuje dvojí fakturace. Funkce orchestratoru se zastaví, zatímco čeká na výsledek funkce aktivity nebo suborchestraci.

## <a name="durable-http-polling"></a>Trvalé dotazování HTTP

Funkce Orchestrator mohou provádět dlouhotrvající volání HTTP externích koncových bodů, jak je popsáno v [článku funkce PROTOKOLU HTTP](durable-functions-http-features.md). Metoda **CallHttpAsync** v jazyce C# a metoda **callHttp** v jazyce JavaScript mohou interně dotazuje koncový bod HTTP při sledování [asynchronního vzoru 202](durable-functions-http-features.md#http-202-handling).

V současné době neexistuje přímá fakturace pro interní operace dotazování HTTP. Interní dotazování však může způsobit, že funkce orchestrator bude pravidelně přehrávat. Za tyto interní záznamy funkcí vám budou účtovány standardní poplatky.

## <a name="azure-storage-transactions"></a>Transakce azure úložiště

Trvalé funkce používá Azure Storage ve výchozím nastavení udržovat stav trvalé, zpracování zpráv a spravovat oddíly prostřednictvím zapůjčení objektů blob. Vzhledem k tomu, že vlastníte tento účet úložiště, všechny transakční náklady se účtují do vašeho předplatného Azure. Další informace o artefaktech Azure Storage používaných trvanlivými funkcemi najdete v [článku Centra úloh](durable-functions-task-hubs.md).

Ke skutečným nákladům na úložiště Azure, které vaše aplikace Durable Functions vznikly, přispívá několik faktorů:

* Aplikace s jednou funkcí je přidružená k jedinému centru úloh, které sdílí sadu prostředků Azure Storage. Tyto prostředky jsou používány všechny trvalé funkce v aplikaci funkce. Skutečný počet funkcí v aplikaci funkce nemá žádný vliv na transakční náklady služby Azure Storage.
* Každá instance aplikace funkce interně dotazuje více front v účtu úložiště pomocí algoritmu dotazování exponenciální-backoff. Nečinná instance aplikace dotazování fronty méně často než aktivní aplikace, což má za následek méně transakčních nákladů. Další informace o chování dotazování fronty durable functions naleznete v [části dotazování fronty v článku Výkon a měřítko](durable-functions-perf-and-scale.md#queue-polling).
* Když běží v azure functions spotřeba nebo Premium plány, [řadič škálování Azure Funkce](../functions-scale.md#how-the-consumption-and-premium-plans-work) pravidelně dotazování všech front centra úloh na pozadí. Pokud je aplikace funkce ve světle až středním měřítku, pouze jedna instance řadiče škálování bude dotazování těchto front. Pokud se aplikace funkce škáluje na velký počet instancí, může být přidáno více instancí řadiče škálování. Tyto další instance řadiče škálování můžete zvýšit celkové náklady na transakce fronty.
* Každá instance aplikace funkce soutěží o sadu zapůjčených objektů blob. Tyto instance budou pravidelně volat službu Azure Blob buď k obnovení držené zapůjčení nebo k pokusu o získání nové zapůjčení. Počet nakonfigurovaných oddílů centra úloh určuje počet zapůjčených objektů blob. Škálování na větší počet instancí aplikace funkce pravděpodobně zvyšuje náklady na transakce Azure Storage spojené s těmito operacemi zapůjčení.

Další informace o cenách Azure Storage najdete v cenové dokumentaci [k Úložišti Azure.](https://azure.microsoft.com/pricing/details/storage/) 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o cenách Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
