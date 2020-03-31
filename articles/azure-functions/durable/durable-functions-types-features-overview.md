---
title: Typy funkcí v azure durable functions
description: Seznamte se s typy funkcí a rolí, které podporují komunikaci mezi funkcemi v orchestraci trvalých funkcí v Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277879"
---
# <a name="durable-functions-types-and-features"></a>Odolné typy a funkce funkcí

Trvalé funkce je rozšíření [funkcí Azure](../functions-overview.md). Trvanlivé funkce můžete použít pro stavové orchestraci spuštění funkce. Aplikace s trvalými funkcemi je řešení, které se skládá z různých funkcí Azure. Funkce mohou hrát různé role v orchestraci trvalé funkce. 

V azure functions jsou aktuálně čtyři trvalé typy funkcí: aktivita, orchestrátor, entita a klient. Zbývající část této části přejde do další podrobnosti o typech funkcí zapojených do orchestraci.

## <a name="orchestrator-functions"></a>Funkce orchestratoru

Funkce Orchestrator popisují, jak jsou akce prováděny a pořadí, ve kterém jsou akce provedeny. Funkce Orchestrator popisují orchestraci v kódu (C# nebo JavaScript), jak je znázorněno ve [vzorcích aplikací Durable Functions](durable-functions-overview.md#application-patterns). Orchestrace může mít mnoho různých typů akcí, včetně [funkcí aktivity](#activity-functions), [sub-orchestrations](durable-functions-orchestrations.md#sub-orchestrations), [čekání na externí události](durable-functions-orchestrations.md#external-events), [HTTP](durable-functions-http-features.md)a [časovače](durable-functions-orchestrations.md#durable-timers). Funkce Orchestrator mohou také interagovat s [funkcemi entity](#entity-functions).

> [!NOTE]
> Funkce Orchestrator jsou zapsány pomocí běžného kódu, ale existují přísné požadavky na to, jak psát kód. Konkrétně orchestrátor funkce kód musí být *deterministický*. Nedodržení těchto požadavků determinismu může způsobit, že funkce orchestrátoru se nezdaří správně spustit. Podrobné informace o těchto požadavcích a o jejich obcházet je naleznete v tématu [omezení kódu.](durable-functions-code-constraints.md)

Podrobnější informace o funkcích orchestratoru a jejich funkcích naleznete v článku [Trvalé orchestrace.](durable-functions-orchestrations.md)

## <a name="activity-functions"></a>Funkce aktivity

Funkce aktivity jsou základní jednotkou práce v orchestraci trvanlivé funkce. Funkce aktivity jsou funkce a úkoly, které jsou orchestrovány v procesu. Můžete například vytvořit funkci orchestrator pro zpracování objednávky. Úkoly zahrnují kontrolu zásob, účtování zákazníka a vytvoření dodávky. Každý úkol by samostatnou funkci aktivity. Tyto funkce aktivity mohou být prováděny sériově, paralelně nebo v některé kombinaci obou.

Na rozdíl od funkcí orchestrátoru nejsou funkce aktivity omezeny v typu práce, kterou v nich můžete provést. Funkce aktivity se často používají k volání v síti nebo ke spuštění operací náročných na procesor. Funkce aktivity může také vrátit data zpět do funkce orchestrator. Rámec trvalé úlohy zaručuje, že každá volaná funkce aktivity bude spuštěna *alespoň jednou* během provádění orchestrace.

> [!NOTE]
> Vzhledem k tomu, že funkce aktivity *zaručují pouze jednou* za spuštění, doporučujeme, abyste, aby vaše funkce aktivity *logika idempotentní* kdykoli je to možné.

Pomocí [aktivační události aktivity](durable-functions-bindings.md#activity-trigger) definujte funkci aktivity. Funkce .NET `DurableActivityContext` obdrží jako parametr. Aktivační událost můžete také svázat s jakýmkoli jiným objektem JSON serializovatelným a předat jej do funkce vstupy. V JavaScriptu můžete přistupovat `<activity trigger binding name>` ke vstupu prostřednictvím vlastnosti [ `context.bindings` objektu](../functions-reference-node.md#bindings). Funkce aktivity mohou mít pouze jednu hodnotu, která jim byla předána. Chcete-li předat více hodnot, musíte použít řazené kolekce členů, pole nebo složité typy.

> [!NOTE]
> Funkci aktivity můžete spustit pouze z funkce orchestrator.

## <a name="entity-functions"></a>Funkce entity

Funkce entity definují operace pro čtení a aktualizaci malých částí stavu. Často označujeme tyto stavové entity jako *trvalé entity*. Podobně jako funkce orchestratoru jsou funkce entity funkce mise se speciálním typem aktivační události, *aktivační událostí entity*. Mohou být také vyvolány z klientských funkcí nebo z funkcí orchestratoru. Na rozdíl od funkcí orchestrator funkce entity nemají žádné konkrétní omezení kódu. Funkce entity také spravovat stav explicitně, nikoli implicitně představující stav prostřednictvím toku řízení.

> [!NOTE]
> Funkce entity a související funkce jsou k dispozici pouze v režimech Durable Functions 2.0 a vyšší.

Další informace o funkcích entit naleznete v článku [Trvalé entity.](durable-functions-entities.md)

## <a name="client-functions"></a>Funkce klienta

Funkce Orchestrator jsou spouštěny [vazbou orchestrace aktivační události](durable-functions-bindings.md#orchestration-trigger) a funkce entity jsou spouštěny [vazbou aktivační události entity](durable-functions-bindings.md#entity-trigger). Obě tyto aktivační události fungují tak, že reagují na zprávy, které jsou zařazeny do fronty do [centra úloh](durable-functions-task-hubs.md). Primární způsob doručení těchto zpráv je pomocí [orchestrator klienta vazby](durable-functions-bindings.md#orchestration-client) nebo [entity klienta vazby](durable-functions-bindings.md#entity-client) z v rámci *funkce klienta*. Jakákoli funkce mimo orchestrator může být *klientskou funkcí*. Můžete například aktivovat orchestrator z funkce aktivované HTTP, funkce aktivované službou Azure Event Hub atd. Co dělá funkci *klientskou funkcí* je jeho použití trvalé hojazyka výstupu vazby klienta.

> [!NOTE]
> Na rozdíl od jiných typů funkcí orchestrátoru a entity funkce nelze aktivovat přímo pomocí tlačítek na webu Azure Portal. Pokud chcete otestovat orchestrator nebo entity funkce na webu Azure Portal, musíte místo toho spustit *funkci klienta,* která spustí orchestrator nebo entity funkce jako součást jeho implementace. Pro nejjednodušší testování je doporučena *funkce ručního spuštění.*

Kromě aktivace funkcí orchestrator nebo entity lze trvalou vazbu *klienta* použít k interakci se spuštěnými orchestracemi a entitami. Orchestrations může být dotazován, ukončena a může mít události, které jsou vyvolány na ně. Další informace o správě orchestrations a entity, najdete v článku [správa instance.](durable-functions-instance-management.md)

## <a name="next-steps"></a>Další kroky

Chcete-li začít, vytvořte první trvalou funkci v [jazyce C#](durable-functions-create-first-csharp.md) nebo [JavaScriptu](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Další informace o orchestracích trvalých funkcí](durable-functions-orchestrations.md)