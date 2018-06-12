---
title: Odstraňovat a diagnostikovat chyby - Azure Logic Apps | Microsoft Docs
description: Pochopit, jak a proč aplikací logiky nezdaří
services: logic-apps
documentationcenter: ''
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 10/15/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 5af99821305fe6daab8a213d0351c5a1c5936461
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298786"
---
# <a name="troubleshoot-and-diagnose-logic-app-failures"></a>Řešení potíží a diagnostikování selhání aplikace logiky

Aplikace logiky generuje informace, které vám pomohou diagnostikovat a ladit problémy ve vaší aplikaci. Aplikace logiky můžete diagnostikovat kontrolou každý krok v pracovním postupu prostřednictvím portálu Azure. Nebo některé kroky můžete přidat do pracovního postupu pro ladění modulu runtime.

## <a name="review-trigger-history"></a>Zkontrolujte historii aktivační události

Každá aplikace logiky začíná aktivační události. Pokud není fire aktivační událost, nejprve zkontrolujte historii aktivační události. Tato historie uvádí všechny pokusy o aktivační události provedené svou aplikaci logiky a podrobnosti o vstupy a výstupy pro jednotlivé pokusy o aktivační události.

1. Chcete-li zkontrolovat, jestli aktivační událost vyvolána v nabídce aplikace logiky, vyberte **přehled**. V části **historie aktivační událost**, zkontrolovat stav aktivační události.

   > [!TIP]
   > Pokud se nabídka aplikace logiky nezobrazí, zkuste se vrátit na řídicí panel Azure a otevřít aplikaci logiky znovu.

   ![Zkontrolujte historii aktivační události](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Pokud nenajdete data, která očekáváte, zkuste vybrat **aktualizovat** na panelu nástrojů.
   > * Pokud v seznamu jsou uvedeny mnoho pokusů o aktivaci, a nelze nalézt položku, kterou chcete, zkuste filtrování seznamu.

   Zde je možné stavy pro pokus o aktivační události:

   | Status | Popis | 
   | ------ | ----------- | 
   | **Bylo úspěšné** | Aktivační událost zaškrtnutí koncový bod a nalézt dostupná data. Obvykle "Fired" stav se zobrazí také společně se tento stav. Pokud ne, definici aktivace může obsahovat podmínku, nebo `SplitOn` příkaz, který nebyl splněn. <p>Tento stav může použít pro ruční aktivační událost, opakování aktivační události nebo cyklického dotazování aktivační události. Aktivační událost můžete spustit úspěšně, ale samotné spustit může stále selhat, když akce, které generují neošetřené chyby. | 
   | **Vynecháno** | Aktivační událost zaškrtnutí koncový bod, ale najít žádná data. | 
   | **Se nezdařilo** | Došlo k chybě. Chcete-li zkontrolovat generovaného chybové zprávy pro aktivační procedury se nezdařilo, vyberte tento pokus aktivační událost a zvolte **výstupy**. Můžete například zjistit vstupních hodnot, které nejsou platné. | 
   ||| 

   Můžete mít více položek aktivační událost se stejné datum a čas, který se stane, když aplikace logiky najde více položek. 
   Pokaždé, když aktivační událost je aktivována, modul Logic Apps vytvoří instanci aplikace logiky ke spuštění pracovního postupu. Ve výchozím nastavení každá instance běží paralelně, aby se žádný pracovní postup má čekat před zahájením spustit.

   > [!TIP]
   > Aktivační událost můžete překontrolovat bez čekání na další opakování. Na panelu nástrojů přehled zvolte **spustit aktivační událost**a vyberte aktivační událost, která vynutí kontrolu. Nebo vyberte **spustit** na panelu nástrojů Návrhář aplikace logiky.

3. V části prozkoumat podrobnosti požadavku na aktivační událost **historie aktivační událost**, vyberte tento pokus aktivační události. 

   ![Vyberte pokus o aktivační události](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Zkontrolujte vstupní hodnoty a všechny výstupy, které generuje aktivační událost. Aktivační událost výstupy zobrazit data, která pochází aktivační událost. Tyto výstupů vám pomohou určit, zda všechny vlastnosti vrátila podle očekávání.

   > [!NOTE]
   > Pokud zjistíte, veškerý obsah, který neznáte, přečtěte si jak Azure Logic Apps [zpracovává jiné typy obsahu](../logic-apps/logic-apps-content-type.md).

   ![Výstupy aktivační události](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Kontrola historie spuštění

Každý přímým trigger spouští pracovní postup spustit. Můžete zkontrolovat, co se stalo při je spuštěno, včetně stavu každého kroku v pracovním postupu plus vstupy a výstupy každého kroku.

1. V nabídce aplikace logiky zvolte **Přehled**. V části **spouští historie**, zkontrolujte spustit pro aktivní aktivační událost.

   > [!TIP]
   > Pokud se nabídka aplikace logiky nezobrazí, zkuste se vrátit na řídicí panel Azure a otevřít aplikaci logiky znovu.

   ![Kontrola spustí historie](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Pokud nenajdete data, která očekáváte, zkuste vybrat **aktualizovat** na panelu nástrojů.
   > * Pokud v seznamu jsou uvedeny mnoho běží, a nemůžete najít položku, kterou chcete, zkuste filtrování seznamu.

   Zde je možné stavy spustit:

   | Status | Popis | 
   | ------ | ----------- | 
   | **Bylo úspěšné** | Všechny akce bylo úspěšné. <p>Pokud došlo k jeho selhání v rámci konkrétní akce, zpracovávat následující akce v pracovním postupu tohoto selhání. | 
   | **Se nezdařilo** | Nejméně jedna akce se nezdařilo a žádné novější akce v pracovním postupu byly nastaveny pro zpracování chyby. | 
   | **Zrušena** | Pracovní postup byl spuštěn, ale přijal žádost o zrušení. | 
   | **Spuštění** | Pracovní postup je právě spuštěna. <p>Tento stav může dojít, omezenému pracovních postupů, nebo kvůli aktuální cenový plán. Další informace najdete v tématu [akce omezení na stránce s cenami](https://azure.microsoft.com/pricing/details/logic-apps/). Pokud jste nastavili [protokolování diagnostiky](../logic-apps/logic-apps-monitor-your-logic-apps.md), můžete také získat informace o všech omezení událostí, které dojít. | 
   ||| 

2. Podrobné informace pro každý krok v konkrétní spustit. V části **spouští historie**, vyberte spuštění, které chcete prověřit.

   ![Kontrola spustí historie](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Jestli spustit samotné byla úspěšná nebo neúspěšná, zobrazení Podrobnosti o spuštění uvádí každý krok a jestli byla úspěšná nebo neúspěšná.

   ![Zobrazení podrobností o spuštění aplikace logiky](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. K prozkoumání vstupy, výstupy a chybové zprávy pro konkrétní krok, zvolte tak, aby tvar, který rozbalí a zobrazí podrobnosti tohoto kroku. Příklad:

   ![Zobrazení podrobností o kroku](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Proveďte ladění za běhu

Usnadní ladění, můžete přidat diagnostiky kroky do pracovního postupu, společně s Kontrola aktivační události a spouští historie. Například můžete přidat kroky, které používají [RequestBin](http://requestb.in) služby, aby mohli prohlédnout požadavků HTTP a určují jejich přesnou velikost, tvar a formát.

1. Vytvořte RequestBin, kterou můžete změnit na privátní a zobrazovat pouze v prohlížeči.

2. V aplikaci logiky přidáte akce HTTP POST s obsah textu, který chcete otestovat, například, výraz nebo jiné krok výstup.

3. Vložte adresu URL pro váš RequestBin do akce HTTP POST.

4. Chcete-li zkontrolovat, jak je žádost o formátu při vygenerování z modulu Logic Apps, spusťte aplikaci logiky a aktualizujte vaše RequestBin.

## <a name="next-steps"></a>Další postup

[Sledování aplikace logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md)