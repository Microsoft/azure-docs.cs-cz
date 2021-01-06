---
title: Fakturace trvalých funkcí – Azure Functions
description: Přečtěte si o interním chování Durable Functions a o tom, jak ovlivňují fakturaci pro Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 2ec1b080c195a47caafd0120240b5fb61ede062b
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97932278"
---
# <a name="durable-functions-billing"></a>Durable Functions fakturaci

[Durable Functions](durable-functions-overview.md) se účtuje stejným způsobem jako Azure Functions. Další informace najdete v tématu [Azure Functions ceny](https://azure.microsoft.com/pricing/details/functions/).

Při provádění funkcí Orchestrator v [plánu Azure Functions spotřeby](../consumption-plan.md)musíte být vědomi některých chování fakturace. Následující části popisují tato chování a jejich efekt podrobněji.

## <a name="orchestrator-function-replay-billing"></a>Faktura pro opětovné přehrání funkcí Orchestrator

[Funkce Orchestrator](durable-functions-orchestrations.md) se můžou několikrát přehrát během celé životnosti orchestrace. Každé přehrání je prohlížející modulem runtime Azure Functions jako odlišné vyvolání funkce. Z tohoto důvodu se v plánu Azure Functions spotřeby účtuje za každé přehrání funkce Orchestrator. Pro další typy plánů se neúčtují žádné poplatky za opětovné přehrání funkce nástroje Orchestrator.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Čeká se a bude se vracet ve funkcích Orchestrator.

Když funkce Orchestrator čeká na dokončení asynchronní akce pomocí **operátoru await** v jazyce **C# nebo v** jazyce JavaScript, modul runtime považuje za to, že konkrétní spuštění bude dokončeno. Fakturace pro funkci Orchestrator se v tomto okamžiku zastaví. Nebude pokračovat, dokud se další funkce nástroje Orchestrator znovu dohraje. Neúčtují se vám žádné čas strávený čekáním nebo zavracením do funkce Orchestrator.

> [!NOTE]
> Funkce, které volají jiné funkce, jsou považovány za antipattern. Důvodem je problém známý jako _Dvojitá fakturace_. Když funkce přímo volá jinou funkci, obě se spustí současně. Volaná funkce aktivně spouští kód, zatímco volající funkce čeká na odpověď. V takovém případě musíte platit za čas, kdy volání funkce stráví čekáním na spuštění volané funkce.
>
> Ve funkcích nástroje Orchestrator není žádná dvojitá fakturace. Při čekání na výsledek funkce aktivity nebo dílčí orchestrace se ukončí fakturace funkce Orchestrator.

## <a name="durable-http-polling"></a>Trvalé cyklické dotazování HTTP

Funkce Orchestrator můžou provádět dlouhotrvající volání HTTP do externích koncových bodů, jak je popsáno v [článku o funkcích http](durable-functions-http-features.md). Metoda **CallHttpAsync** v jazyce C# a metoda **CallHttp** v jazyce JavaScript může interně DOTAZovat koncový bod HTTP za běhu za [asynchronního vzoru 202](durable-functions-http-features.md#http-202-handling).

V současné době není k dispozici přímá fakturace za interní operace cyklického dotazování HTTP. Interní dotazování může ale způsobit, že se funkce Orchestrator bude pravidelně přehrávat. U těchto interních funkcí se vám budou účtovat standardní poplatky.

## <a name="azure-storage-transactions"></a>Transakce Azure Storage

Durable Functions používá ve výchozím nastavení Azure Storage, aby bylo možné trvale zachovávat stav, zpracovávat zprávy a spravovat oddíly prostřednictvím zapůjčení objektů BLOB. Vzhledem k tomu, že tento účet úložiště vlastníte, účtují se všechny poplatky za transakce za vaše předplatné Azure. Další informace o artefaktech Azure Storage používaných v Durable Functions najdete v [článku centra úloh](durable-functions-task-hubs.md).

Několik faktorů přispívá k skutečným Azure Storagem nákladům, které vznikly v aplikaci Durable Functions:

* Jedna aplikace Function App je přidružená k jednomu rozbočovači úloh, který sdílí sadu prostředků Azure Storage. Tyto prostředky používá všechny trvalé funkce ve Function App. Skutečný počet funkcí v aplikaci Function App nemá žádný vliv na Azure Storage náklady transakce.
* Každá instance aplikace Function interně se dotazuje více front v účtu úložiště pomocí algoritmu cyklického dotazování exponenciálního omezení rychlosti. Nečinná instance aplikace dotazuje fronty méně často než aktivní aplikace, což vede k menšímu počtu transakčních nákladů. Další informace o chování při cyklickém dotazování ve frontě Durable Functions najdete v [článku o výkonu a škálování v části cyklické dotazování front](durable-functions-perf-and-scale.md#queue-polling).
* Při spuštění ve Azure Functions spotřebě nebo plánu Premium se [kontroler Azure Functions](../event-driven-scaling.md) pravidelně dotazuje na všechny fronty centra úkolů na pozadí. Pokud se aplikace Function App dohlíží ke střednímu měřítku, bude se tyto fronty dotazovat jenom u jediné instance řadiče škálování. Pokud se aplikace funkcí škáluje na velký počet instancí, můžou se přidat další instance kontroleru škálování. Tyto dodatečné instance kontroleru škálování můžou zvýšit celkovou cenu za transakce ve frontě.
* Každá instance aplikace funkcí soutěží o sadu zapůjčení objektů BLOB. Tyto instance budou pravidelně volat Blob service Azure, aby obnovili držené zapůjčení nebo se pokusili získat nová zapůjčení. Počet nakonfigurovaného oddílu centra úkolů určuje počet zapůjčení objektů BLOB. Horizontální navýšení kapacity u většího počtu instancí aplikace Functions může zvýšit náklady na Azure Storage transakce spojené s těmito operacemi zapůjčení.

Další informace o cenách Azure Storage najdete v dokumentaci k [Azure Storageám s cenami](https://azure.microsoft.com/pricing/details/storage/) . 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o cenách Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
