---
title: Ceny a fakturace - Azure Logic Apps | Microsoft Docs
description: Zjistěte, jak funguje ceny a fakturace pro Azure Logic Apps
services: logic-apps
author: kevinlam1
manager: cfowler
editor: ''
documentationcenter: ''
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/11/2018
ms.author: klam
ms.openlocfilehash: 3f01ac12bb3987d564f8d9f70706ae6aabd0a0ee
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="logic-apps-pricing-model"></a>Cenový model Logic Apps

Můžete vytvořit a spouštění automatizovaných škálovatelnou integraci pracovních postupů v cloudu pomocí Azure Logic Apps. Zde jsou informace o fakturaci a cenách fungování Logic Apps. 

## <a name="consumption-pricing-model"></a>Cenový model spotřeby

S nově vytvořený logic apps platíte jenom za můžete použít. Nové aplikace logiky použít plán spotřeby a cenový model, což znamená, že všechny spuštěních akce prováděné instanci aplikace logiky jsou měření podle objemu. Každý krok v definici aplikace logiky je akce, která zahrnuje aktivačních událostí, řízení toku kroky, volání vestavěné akce a volání konektory. Další informace najdete v tématu [ceny aplikace logiky](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Triggery

Aktivační události jsou zvláštní akce, které vytvořit instanci aplikace logiky, když se stane konkrétní události. Aktivační události fungují různými způsoby, které ovlivňují, jak je aplikace logiky měření podle objemu.

* **Aktivační událost dotazování** – této aktivační události neustále kontroluje koncový bod pro zprávy, které splňují kritéria pro vytvoření instance aplikace logiky a spuštění pracovního postupu. Každý požadavek dotazování počítá jako spuštění a účtované podle objemu, i když je vytvořena žádná instance aplikace logiky. Chcete-li zadat interval dotazování, nastavte aktivační událost pomocí návrháře logiku aplikace.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Aktivační události Webhooku** – této aktivační události čeká klienta k odeslání požadavku na konkrétní. Každý požadavek odeslaný ke koncovému bodu webhooku se počítá jako spuštění akce. Například aktivační událost požadavku a Webhooku protokolu HTTP jsou obě aktivační události webhooku.

* **Aktivační událost opakování** – této aktivační události vytvoří instanci aplikace logiky na základě intervalu opakování, které jste nastavili v aktivační události. Můžete například nastavit nahoru opakování aktivační událost, která spouští každé tři dny nebo podle plánu složitější.

Aktivační událost spuštěních můžete najít v podokně přehled aplikace logiky v části historie aktivační události.

## <a name="actions"></a>Akce

Předdefinované akce, jako je akce, které volání HTTP, Azure Functions nebo API Management a také řízení toku kroky se měří jako nativní akce, které mají jejich odpovídající typy. Akce, které volají [konektory](https://docs.microsoft.com/connectors) typ "ApiConnection". Tyto konektory jsou klasifikovány jako standard nebo enterprise konektory, které jsou – měření podle objemu založené na jejich odpovídající [ceny][pricing]. 

Všechny úspěšně a neúspěšně spuštění akce se počítá nebo účtované jako spuštění akce. Akce, které jsou přeskočen, protože unmet podmínky nebo akce, které nelze spustit, protože aplikace logiky ukončeno před dokončením, není však počítají jako spuštění akce. Zakázané logiku aplikace nelze vytvořit instanci nové instance, tak jejich neúčtujeme při jsou zakázány.

> [!NOTE]
> Po zakázání aplikace logiky všechny aktuálně spuštěné instance může trvat nějakou dobu, než úplně zastavit.

Akce, které běží v smyčky, se počítají na každý cyklus ve smyčce. Například jedna akce ve smyčce "pro každou", který zpracovává seznam 10 položek se počítá vynásobením počet položek seznamu (10) podle počtu akcí ve smyčce (1) a jeden pro spuštění smyčky. Ano, v tomto příkladu je výpočet (10 * 1) + 1, což vede k 11 spuštění akce.

## <a name="integration-account-usage"></a>Používání účtů integrace

Na základě spotřeby využití zahrnuje [integrace účet](logic-apps-enterprise-integration-create-integration-account.md) kde můžete prozkoumat, vývoj a testování [B2B/EDI](logic-apps-enterprise-integration-b2b.md) a [zpracování kódu XML](logic-apps-enterprise-integration-xml.md) funkce Logic Apps na Ne Další náklady. Může mít jeden účet integrace na oblast a úložiště až konkrétní [čísla artefaktů](../logic-apps/logic-apps-limits-and-config.md), jako jsou například EDI obchodními partnery a smlouvy, map, schémata, sestavení, certifikáty a konfigurace batch.

Služba Logic Apps nabízí také účty pro integraci úrovně basic a standard s podporované SLA aplikace logiky. Můžete použít účty základní integraci při buď chcete použít jenom zpracování zpráv nebo fungují jako partner malé firmy, která má vztah obchodním partnerem s větší obchodní entity. Účty pro integraci standardní podpora složitější vztahy B2B a zvýšit počet entit, které můžete spravovat. Další informace najdete v tématu [Azure ceny](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Další postup

* [Další informace o Logic Apps][whatis]
* [Vytvoření první aplikace logiky][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

