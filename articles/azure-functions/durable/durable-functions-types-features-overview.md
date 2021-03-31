---
title: Typy funkcí v Azure Durable Functions
description: Přečtěte si o typech funkcí a rolí, které podporují komunikaci typu Function-to-Function v Durable Functions orchestrace v Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84697703"
---
# <a name="durable-functions-types-and-features"></a>Durable Functions typy a funkce

Durable Functions je rozšíření [Azure Functions](../functions-overview.md). Durable Functions lze použít pro stavovou orchestraci provádění funkce. Trvalá aplikace Function App je řešení, které se skládá z různých Azure Functions. Funkce mohou hrát různé role v rámci trvalé orchestrace funkcí. 

V Azure Functions aktuálně existují čtyři typy trvalých funkcí: Activity, Orchestrator, entity a Client. Zbytek této části se zabývá dalšími podrobnostmi o typech funkcí zapojených do orchestrace.

## <a name="orchestrator-functions"></a>Funkce nástroje Orchestrator

Funkce nástroje Orchestrator popisují způsob provádění akcí a pořadí, ve kterém jsou akce provedeny. Funkce Orchestrator popisují orchestraci v kódu (C# nebo JavaScript), jak je znázorněno v [Durable Functionsch vzorcích aplikací](durable-functions-overview.md#application-patterns). Orchestrace může mít mnoho různých typů akcí, včetně [funkcí aktivity](#activity-functions), [dílčích orchestrací](durable-functions-orchestrations.md#sub-orchestrations), [čekání na externí události](durable-functions-orchestrations.md#external-events), [http](durable-functions-http-features.md)a [časovače](durable-functions-orchestrations.md#durable-timers). Funkce nástroje Orchestrator mohou také interagovat s [funkcemi entit](#entity-functions).

> [!NOTE]
> Funkce nástroje Orchestrator jsou zapisovány pomocí obyčejného kódu, ale existují přísné požadavky na zápis kódu. Konkrétně musí být kód funkce nástroje Orchestrator *deterministický*. Po neúspěšném provedení těchto požadavků determinismem může dojít ke správnému spuštění funkcí nástroje Orchestrator. Podrobné informace o těchto požadavcích a způsobu jejich řešení najdete v tématu [omezení kódu](durable-functions-code-constraints.md) .

Podrobnější informace o funkcích nástroje Orchestrator a jejich funkcích naleznete v článku [trvalé orchestrace](durable-functions-orchestrations.md) .

## <a name="activity-functions"></a>Funkce aktivity

Funkce aktivity představují základní pracovní jednotku v rámci trvalé orchestrace funkcí. Funkce aktivity jsou funkce a úkoly, které jsou v procesu Orchestrované. Například můžete vytvořit funkci Orchestrator pro zpracování objednávky. Úkoly zahrnují kontrolu inventáře a účtování zákazníků a vytváření dodávek. Každý úkol by byl samostatnou funkcí aktivity. Tyto funkce aktivit můžou být spouštěny sériově, paralelně nebo v kombinaci obou.

Na rozdíl od funkcí Orchestrator nejsou funkce aktivity omezeny v typu práce, kterou v nich můžete dělat. Funkce aktivity se často používají k provádění síťových volání nebo provádění operací náročných na procesor. Funkce Activity může také vracet data zpět do funkce Orchestrator. Rozhraní odolné úlohy zaručuje, že každá funkce s názvem funkce se spustí *alespoň jednou* během provádění orchestrace.

> [!NOTE]
> Vzhledem k tomu, že funkce aktivity zaručují *alespoň jedno* spuštění, doporučujeme, abyste *idempotentní* logiku funkce Activity, kdykoli to bude možné.

K definování funkce aktivity použijte [Trigger aktivity](durable-functions-bindings.md#activity-trigger) . Funkce .NET obdrží `DurableActivityContext` jako parametr. Můžete také navazovat Trigger na jakýkoli jiný objekt, který lze serializovat, a předat do něj vstupní hodnoty funkce. V jazyce JavaScript můžete k vstupu přistupovat prostřednictvím `<activity trigger binding name>` vlastnosti [ `context.bindings` objektu](../functions-reference-node.md#bindings). Funkcí aktivity může být předána pouze jedna hodnota. Chcete-li předat více hodnot, je nutné použít řazené kolekce členů, pole nebo komplexní typy.

> [!NOTE]
> Funkci aktivity můžete aktivovat jenom z funkce Orchestrator.

## <a name="entity-functions"></a>Funkce entit

Funkce entit definují operace pro čtení a aktualizaci malých částí stavu. Často odkazujeme na tyto stavové entity jako *odolné entity*. Podobně jako funkce nástroje Orchestrator jsou funkce entit funkce se speciálním typem triggeru, *triggerem entity*. Mohou být také vyvolány z klientských funkcí nebo z funkcí nástroje Orchestrator. Na rozdíl od funkcí Orchestrator nemají entity Functions žádná konkrétní omezení kódu. Funkce entit také spravují stav explicitně namísto implicitního reprezentace stavu prostřednictvím řízení toku.

> [!NOTE]
> Funkce entit a související funkce jsou dostupné jenom v Durable Functions 2,0 a novějších.

Další informace o funkcích entit najdete v článku [trvalé entity](durable-functions-entities.md) .

## <a name="client-functions"></a>Klientské funkce

Funkce Orchestrator se aktivují [aktivační vazbou orchestrace](durable-functions-bindings.md#orchestration-trigger) a funkce entit se spouštějí [vazbou triggeru entity](durable-functions-bindings.md#entity-trigger). Obě tyto triggery fungují tak, že jednají zprávy zařazené do [centra úloh](durable-functions-task-hubs.md). Hlavním způsobem, jak doručovat tyto zprávy, je použití [vazby klienta nástroje Orchestrator](durable-functions-bindings.md#orchestration-client) nebo [vazby klienta entit](durable-functions-bindings.md#entity-client) z *klientské funkce*. Jakákoli funkce bez nástroje Orchestrator může být *klientská funkce*. Můžete například aktivovat nástroj Orchestrator z funkce aktivované protokolem HTTP, funkcí aktivovaných centrem událostí Azure atd. To znamená, že funkce *klienta* funguje jako jeho použití trvalé výstupní vazby klienta.

> [!NOTE]
> Na rozdíl od jiných typů funkcí se funkce Orchestrator a entity nedají aktivovat přímo pomocí tlačítek na webu Azure Portal. Pokud chcete otestovat funkci Orchestrator nebo entity na webu Azure Portal, musíte místo toho spustit *klientskou funkci* , která jako součást implementace spustí nástroj Orchestrator nebo entity. Pro nejjednodušší prostředí testování je doporučena funkce *Ruční aktivace* .

Kromě toho, že se aktivují funkce Orchestrator nebo entity, se dá *trvalé vazba klienta* použít k interakci s běžícími orchestrací a entitami. Například orchestrace lze dotazovat, ukončit a mohou mít vyvolané události. Další informace o správě orchestrací a entit najdete v článku věnovaném [správě instancí](durable-functions-instance-management.md) .

## <a name="next-steps"></a>Další kroky

Chcete-li začít, vytvořte svou první trvalou funkci v [jazyce C#](durable-functions-create-first-csharp.md) nebo [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Přečtěte si další informace o orchestraci Durable Functions](durable-functions-orchestrations.md)