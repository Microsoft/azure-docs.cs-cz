---
title: Monitorování a získání přehledy o aplikaci logiky spouští pomocí analýzy protokolů - Azure Logic Apps | Microsoft Docs
description: Monitorování vaší logiku aplikace běží s analýzy protokolů získat přehledy a bohatší ladění podrobnosti pro řešení potíží a diagnostiku
author: divyaswarnkar
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: LADocs; divswa
ms.openlocfilehash: 0dc7f28ebe3816ba317eb3a4e28fc640c8a12a4e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294570"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Monitorovat a získat přehled o logiku aplikace běží s analýzy protokolů

Pro monitorování a bohatší informace o ladění můžete zapnout analýzy protokolů ve stejnou dobu, po vytvoření aplikace logiky. Log Analytics poskytuje možnosti pro diagnostiku protokolování a monitorování pro svou aplikaci logiky spouští prostřednictvím portálu Azure. Když přidáte do řešení pro správu aplikace logiky, zobrazí agregovaný stav pro logiku aplikace běží a konkrétní podrobnosti, jako je stav, čas spuštění, opakované odeslání stavu a ID korelace.

Tento článek ukazuje, jak zapnout analýzy protokolů, aby lze zobrazit události modulu runtime a spusťte data pro svou aplikaci logiky.

 > [!TIP]
 > Pokud chcete sledovat existující aplikace logiky, použijte následující postup [zapnout protokolování diagnostiky a odesílání dat za běhu aplikace logiky k analýze protokolů](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Požadavky

Než začnete, musíte mít pracovní prostor analýzy protokolů. Další informace [jak vytvořit pracovní prostor analýzy protokolů](../log-analytics/log-analytics-quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Zapnutí protokolování diagnostiky při vytváření aplikací logiky

1. V [portál Azure](https://portal.azure.com), vytvoření aplikace logiky. Zvolte **vytvořit prostředek** > **Enterprise integrace** > **aplikace logiky**.

   ![Vytvoření aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

2. V **vytvořit aplikaci logiky** proveďte tyto úlohy, jak je znázorněno:

   1. Zadejte název pro svou aplikaci logiky a vyberte předplatné Azure. 
   2. Vytvořte nebo vyberte skupinu prostředků Azure.
   3. Nastavit **protokolu analýzy** k **na**. 
   Vyberte pracovní prostor analýzy protokolů, které chcete odeslat data pro svou aplikaci logiky spouští. 
   4. Až budete připraveni, zvolte **připnout na řídicí panel** > **vytvořit**.

      ![Vytvoření aplikace logiky](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Po dokončení tohoto kroku, Azure vytvoří aplikace logiky, která je teď přidružené pracovní prostor analýzy protokolů. 
      Tento krok navíc také automaticky nainstaluje řešení pro správu aplikace logiky v pracovním prostoru.

3. Chcete-li zobrazit aplikaci logiky běží, [pokračovat v těchto krocích](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution"></a>Nainstalujte do řešení pro správu aplikace logiky

Pokud jste již zapnuli analýzy protokolů při vytvoření aplikace logiky, tento krok přeskočte. Už máte nainstalovaný řešení pro správu aplikace logiky.

1. V [portál Azure](https://portal.azure.com), zvolte **více služeb**. Vyhledejte "analýzy protokolů" jako filtr a vyberte **analýzy protokolů** znázorněné:

   ![Zvolte "Analýzy protokolů"](media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

2. V části **analýzy protokolů**, najděte a vyberte pracovní prostor analýzy protokolů. 

   ![Vyberte pracovní prostor analýzy protokolů](media/logic-apps-monitor-your-logic-apps-oms/select-logic-app.png)

3. V části **správy**, zvolte **přehled**.

   ![Zvolte "Portál OMS"](media/logic-apps-monitor-your-logic-apps-oms/ibiza-portal-page.png)

4. Na stránce Přehled zvolte **přidat** otevřete dlaždici řešení pro správu. 

   ![Zvolte "Správa aplikace logiky"](media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

5. Procházení seznamu **řešení pro správu**, zvolte **správy Logic Apps** řešení a zvolte **vytvořit** k instalaci na stránku Přehled.

   ![Zvolte "Přidat" pro "Správa aplikace logiky"](media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-your-logic-app-runs-in-your-log-analytics-workspace"></a>Zobrazení, které běží vaše aplikace logiky v pracovním prostoru analýzy protokolů

1. Chcete-li zobrazit počet a stav pro vaše aplikace běží logiku, přejděte na stránku přehled pro pracovní prostor analýzy protokolů. Zkontrolujte podrobnosti v **správy Logic Apps** dlaždici.

   ![Dlaždice přehledu se zobrazuje počet logiku aplikace spustit a stav](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

2. Chcete-li zobrazit souhrn s dalšími podrobnostmi o vašem logiku aplikace běží, vyberte **správy Logic Apps** dlaždici.

   Zde že logiku aplikace běží jsou seskupené podle názvu nebo stav spuštění. Můžete také zjistit podrobnosti o selhání v akce nebo aktivačních událostí pro logiku aplikace běží.

   ![Souhrn stavu pro svou aplikaci logiky běží](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
3. Chcete-li zobrazit všechny spuštění pro určitou logiku aplikace nebo stav, vyberte řádek pro aplikace logiky nebo stav.

   Tady je příklad, který zobrazuje všechny spuštění pro určitou logiku aplikace:

   ![Spuštění zobrazení pro aplikaci logiky nebo ve stavu](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Existují dvě pokročilé možnosti na této stránce:
   * **Sledovat vlastnosti:** tomto sloupci se zobrazuje sledovaných vlastnosti, které jsou seskupené podle akce pro aplikaci logiky. Chcete-li zobrazit vlastnosti sledovaných, zvolte **zobrazení**. Pomocí filtru sloupec může hledat sledovaných vlastnosti.
   
     ![Zobrazit sledovaných vlastnosti pro aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Všechny nově přidané vlastnosti sledovaných může trvat 10 až 15 minut, než se objeví poprvé. Další informace [jak přidat do aplikace logiky sledovaných vlastnosti](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Odešlete:** můžete znovu odeslat jeden nebo více spustí logiku aplikace, které selhaly, byly úspěšné, nebo jsou stále běží. Zaškrtněte políčka pro spuštění, které chcete znovu odeslat a vyberte **odešlete znovu**. 

     ![Odešlete znovu logiku aplikace běží](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

4. Chcete-li filtrovat tyto výsledky, můžete provádět klientské a serverové filtrování.

   * Filtr na straně klienta: pro každý sloupec vyberte filtry, které chcete. 
   Zde je několik příkladů:

     ![Příklad filtry sloupců](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * Filtr na straně serveru: Zvolte konkrétního časového okna nebo omezit počet spuštění, které se zobrazují, pomocí ovládacího prvku oboru v horní části stránky. 
   Ve výchozím nastavení zobrazí pouze 1000 záznamů najednou. 
   
     ![Změnit časové okno](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
5. Chcete-li zobrazit všechny akce a jejich podrobnosti o konkrétní spustit, vyberte řádek pro spuštění aplikace logiky.

   Tady je příklad, který zobrazuje všechny akce pro určitou logiku aplikaci spustit:

   ![Zobrazení akce pro spuštění aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
6. Na všechny stránky s výsledky, chcete-li zobrazit dotaz za výsledky nebo chcete-li zobrazit všechny výsledky, zvolte **najdete v článku všechny**, což otevře se stránka hledání protokolů.
   
   ![Zobrazit všechny na výsledky stránky](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   Na stránce hledání protokolů
   * Chcete-li zobrazit výsledky dotazu v tabulce, zvolte **tabulky**.
   * Chcete-li změnit dotaz, můžete upravit řetězec dotazu v panelu vyhledávání. 
   Pro lepší podmínky, zvolte **pokročilé analýzy**.

     ![Zobrazit akce a podrobnosti pro spuštění aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     Zde můžete na stránce Azure Log Analytics aktualizovat dotazy a zobrazit výsledky z tabulky. 
     Tento dotaz používá [Kusto dotazovací jazyk](https://docs.loganalytics.io/docs/Language-Reference), který můžete upravit, pokud chcete zobrazit různé výsledky. 

     ![Azure Log Analytics - zobrazení dotazu](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Další postup

* [Monitorování zpráv B2B](../logic-apps/logic-apps-monitor-b2b-message.md)

