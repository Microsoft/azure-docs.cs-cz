---
title: Fakturace trvalých funkcí – Azure Functions
description: Přečtěte si o interním chování trvalých funkcí a o tom, jak ovlivňují fakturaci Azure Functions.
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: b79d50adf932bd5c316fbda9d0964eea7c0484ca
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935884"
---
# <a name="durable-functions-billing"></a>Durable Functions fakturaci

[Durable Functions](durable-functions-overview.md) se účtují stejně jako Azure Functions. Další informace najdete v tématu [ceny Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Při provádění funkcí Orchestrator v plánu Azure Functions [spotřeby](../functions-scale.md#consumption-plan)existují některá nastavení fakturace, o kterých je potřeba vědět. Následující části popisují tato chování a jejich dopad podrobněji.

## <a name="orchestrator-function-replay-billing"></a>Faktura pro opětovné přehrání funkcí Orchestrator

[Funkce Orchestrator](durable-functions-orchestrations.md) se můžou několikrát přehrát během celé životnosti orchestrace. Každé přehrání je prohlížející modulem runtime Azure Functions jako odlišné vyvolání funkce. Z tohoto důvodu se vám v plánu Azure Functions spotřeby bude účtovat každé přehrání funkce Orchestrator. Pro další typy plánů se neúčtují žádné poplatky za opětovné přehrání funkcí nástroje Orchestrator.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Čeká se a bude se vracet ve funkcích Orchestrator.

Když funkce Orchestrator počká, než se asynchronní akce dokončí pomocí `await` (C#) nebo `yield` (JavaScript), modul runtime posuzuje, že konkrétní spuštění bylo dokončeno. Fakturace pro funkci Orchestrator se v tomto okamžiku zastaví a nebude pokračovat, dokud se další funkce Orchestrator znovu nespustí. Neúčtují se vám žádné čas strávený čekáním nebo zavracením do funkce Orchestrator.

> [!NOTE]
> Funkce, které volají jiné funkce, jsou považovány za anti-Pattern. Důvodem je problém známý jako _Dvojitá fakturace_. Když funkce přímo volá jinou funkci, spustí se současně. Volaný aktivně spouští kód, zatímco volající čeká na odpověď. V takovém případě musíte platit za čas, který volající čeká na spuštění volaného. Funkce nástroje Orchestrator netrpí touto dvojitou fakturací, protože při čekání na výsledek funkce aktivity (nebo dílčí orchestrace) se ukončí fakturace funkce Orchestrator.

## <a name="durable-http-polling"></a>Trvalé cyklické dotazování HTTP

Funkce Orchestrator můžou provádět dlouhotrvající volání HTTP do externích koncových bodů, jak je popsáno v článku o [funkcích http](durable-functions-http-features.md) . Metoda (C#) a `callHttp` metoda (JavaScript) se můžou interně dotazovat na koncový bod HTTP, když následují za [asynchronním vzor 202.](durable-functions-http-features.md#http-202-handling) `CallHttpAsync` V současné době není k dispozici přímá faktura za interní operace cyklického dotazování HTTP. Interní dotazování může ale způsobit, že se funkce Orchestrator znovu přehraje a za tyto interní funkce se vám budou účtovat standardní poplatky.

## <a name="azure-storage-transactions"></a>Transakce Azure Storage

Ve výchozím nastavení Azure Storage používá Durable Functions k zachování stavu, zpracování zpráv a správě oddílů prostřednictvím zapůjčení objektů BLOB. Tento účet úložiště vlastníte, takže veškeré poplatky za transakce se účtují do předplatného Azure. Další informace o artefaktech Azure Storage používaných v Durable Functions najdete v článku [centra úloh](durable-functions-task-hubs.md) .

Několik faktorů přispívá k skutečným Azure Storagem nákladům, které vznikly v rámci vaší Durable Functions aplikace.

* Jedna aplikace Function App je přidružená k jednomu rozbočovači úloh, který sdílí sadu prostředků Azure Storage. Tyto prostředky používá všechny trvalé funkce ve Function App. Skutečný počet funkcí v aplikaci Function App nemá žádný vliv na náklady na Azure Storage transakce.
* Každá instance aplikace Function interně se dotazuje více front v účtu úložiště pomocí exponenciálního algoritmu cyklického dotazování omezení rychlosti. Instance nečinné aplikace bude dotazovat fronty méně často než aktivní aplikace, což vede k menšímu počtu transakčních nákladů. Další informace o chování při cyklickém dotazování ve frontě Durable Functions najdete v [části cyklické dotazování front v článku o výkonu a škálování](durable-functions-perf-and-scale.md#queue-polling) .
* Při spuštění ve Azure Functions spotřebě nebo plánu Premium se [kontroler Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) pravidelně dotazuje na všechny fronty centra úkolů na pozadí. Pod světlem střední škály se tyto fronty dotazují jenom na jednu instanci řadiče s jednou stupnicí. Pokud se aplikace funkcí škáluje na velký počet instancí, můžou se přidat další instance řadičů škálování. Tyto dodatečné instance kontroleru škálování můžou zvýšit celkové náklady na transakci fronty.
* Každá instance aplikace funkcí soutěží o sadu zapůjčení objektů BLOB. Tyto instance budou pravidelně provádět volání služby Azure Blob service, aby se podařilo prodloužit zapůjčení, se pokusí získat nová zapůjčení. Počet zapůjčení objektů BLOB se určuje podle počtu nakonfigurovaných oddílů centra úloh. Horizontální navýšení kapacity na větší počet instancí aplikace Functions bude nejspíš zvýšit náklady na Azure Storage transakce spojené s těmito operacemi zapůjčení.

Další informace o cenách Azure Storage najdete v dokumentaci pro [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/) .

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o cenách Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
