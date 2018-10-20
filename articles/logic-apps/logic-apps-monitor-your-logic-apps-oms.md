---
title: Spuštění aplikace logiky monitorování pomocí Log Analytics – Azure Logic Apps | Dokumentace Microsoftu
description: Získejte přehledy a data o spuštěních aplikací logiky s využitím Log Analytics pro ladění pro řešení potíží a Diagnostika
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 177c361734a88acab5fc10d6b460645be82bf437
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457132"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Monitorovat a získávat přehledy o spuštění aplikace logiky s využitím Log Analytics

Pro monitorování a bohatší informace o ladění můžete zapnout Log Analytics ve stejnou dobu, kdy vytvořit aplikaci logiky. Log Analytics poskytuje diagnostické protokolování a monitorování pro aplikaci logiky se spouští na webu Azure portal. Když přidáte řešení Správa služby Logic Apps, zobrazí se agregovaného stavu pro spuštění aplikace logiky a konkrétní podrobnosti, jako je stav, čas spuštění, opakovaným odesláním stav a ID korelace.

Tento článek ukazuje, jak zapnout v Log Analytics, můžete zobrazit události modulu runtime a spusťte data pro vaši aplikaci logiky.

 > [!TIP]
 > K monitorování existujících logic apps, postupujte podle těchto kroků [zapněte protokolování diagnostiky a odesílání dat získaných za běhu aplikace logiky do Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Požadavky

Než začnete, musíte mít pracovní prostor Log Analytics. Přečtěte si [jak vytvořit pracovní prostor Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Zapnutí protokolování diagnostiky vytváření aplikací logiky

1. V [webu Azure portal](https://portal.azure.com), vytvořit aplikaci logiky. Zvolte **vytvořit prostředek** > **integrace** > **aplikace logiky**.

   ![Vytvoření aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

1. V části **vytvořit aplikaci logiky**, jak je znázorněno provádět tyto úlohy:

   1. Zadejte název pro svou aplikaci logiky a vyberte své předplatné Azure. 

   1. Vytvořte nebo vyberte skupinu prostředků Azure.

   1. Nastavte **Log Analytics** k **na**. 

   1. Ze seznamu seznamu pracovního prostoru Log Analytics vyberte pracovní prostor, ve které chcete odesílat data pro vaše aplikace logiky spouští. 

      ![Vytvoření aplikace logiky](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Po dokončení tohoto kroku, Azure vytvoří aplikaci logiky, která je teď spojený s pracovního prostoru Log Analytics. 
      Tento krok navíc také automaticky nainstaluje řešení Správa služby Logic Apps ve vašem pracovním prostoru.

   1. Jakmile budete hotoví, vyberte **Vytvořit**.

1. Chcete-li zobrazit vaše aplikace logiky spouští, [pokračovat tímto postupem](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution"></a>Nainstalujte řešení Správa služby Logic Apps

Pokud již zapne Log Analytics, pokud jste vytvořili aplikaci logiky, tento krok přeskočit. Již máte nainstalované řešení Správa služby Logic Apps.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Všechny služby**. Do vyhledávacího pole zadejte "log analytics" jako filtr a vyberte **Log Analytics**.

   ![Vyberte "Log Analytics"](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. V části **Log Analytics**vyhledejte a vyberte pracovní prostor Log Analytics. 

   ![Vyberte pracovní prostor Log Analytics](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. V části **konfigurovat řešení monitorování**, zvolte **zobrazení řešení**.

   ![Zvolte "Zobrazit řešení"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Na stránce s přehledem, zvolte **přidat**, které se otevře **řešení pro správu** seznamu. V tomto seznamu, vyberte **Správa služby Logic Apps**. 

   ![Vyberte "Správa služby Logic Apps"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Pokud nemůžete najít řešení, v dolní části seznamu, zvolte **načíst další** až se zobrazí řešení.

1. Zvolte **vytvořit**, která nainstaluje řešení.

   ![Zvolte "Přidat" pro "Správa služby Logic Apps"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-runs-in-log-analytics-workspace"></a>Spuštění aplikace logiky zobrazení v pracovním prostoru Log Analytics

1. Pokud chcete zobrazit počet a stav pro spuštění vaší aplikace logiky, přejděte do pracovního prostoru Log Analytics a otevřete stránku přehledu. 

   Zobrazí podrobnosti o spuštěních aplikací logiky na **Správa služby Logic Apps** dlaždici. Chcete-li zobrazit souhrn s dalšími podrobnostmi o spuštěních aplikací logiky, zvolte **Správa služby Logic Apps** dlaždici. 

   ![Dlaždice přehledu zobrazující stav a počet běh aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   Tady spuštěních aplikací logiky se seskupují podle názvu nebo podle stavu spuštění. 
   Tato stránka také zobrazuje podrobnosti o neúspěšných akcí nebo triggerů pro spuštění aplikace logiky.

   ![Souhrn stavu pro vaše aplikace logiky spouští](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
1. Pokud chcete zobrazit všechna spuštění pro aplikaci logiky specifické nebo stav, vyberte řádek pro aplikaci logiky nebo ve stavu.

   Tady je příklad, který zobrazuje všechna spuštění aplikace logiky specifické pro:

   ![Zobrazit spuštění aplikace logiky nebo ve stavu](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Stránka má tyto rozšířené možnosti:

   * **Sledované vlastnosti:**

     Tento sloupec zobrazuje sledované vlastnosti, které jsou seskupeny podle akce pro aplikaci logiky. Chcete-li zobrazit sledované vlastnosti, zvolte **zobrazení**. 
     K vyhledání sledované vlastnosti, použijte filtr sloupce.
   
     ![Zobrazit sledované vlastnosti pro aplikaci logiky.](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Všechny nově přidané sledované vlastnosti může trvat 10 až 15 minut, než se zobrazují první. Přečtěte si [přidání sledované vlastnosti aplikace logiky](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Odešlete:** jeden nebo více spuštění aplikace logiky, které selhaly, byly úspěšné, neúspěšné nebo jsou stále běží. Zaškrtněte políčka pro spuštění, které chcete znovu odeslat a zvolte **znovu odeslat**. 

     ![Znovu odeslat spuštění aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Chcete-li filtrovat výsledky, můžete provést filtrování na straně klienta i stranu serveru.

   * **Filtrování na straně klienta**: pro každý sloupec, vyberte filtry, které potřebujete, třeba:

     ![Příklad sloupcovými filtry.](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtrování na straně serveru**: K výběru konkrétního časového okna nebo omezit počet spuštění, které se zobrazí, pomocí ovládacího prvku oboru v horní části stránky. Ve výchozím nastavení se zobrazí pouze 1 000 záznamů najednou.
   
     ![Změnit časový interval](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
1. Chcete-li zobrazit všechny akce a jejich podrobnosti pro konkrétní spuštění, vyberte řádek pro běh aplikace logiky.

   Tady je příklad, který zobrazuje všechny akce pro běh aplikace logiky konkrétní:

   ![Zobrazení akcí pro běh aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
1. Na libovolné stránce výsledky k zobrazení dotazu za výsledky nebo všechny výsledky, zvolte **najdete v článku všechny**, který otevře stránku prohledávání protokolů.
   
   ![Zobrazit všechny na stránkách s výsledky](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   Na stránce prohledávání protokolů

   * Chcete-li zobrazit výsledky dotazu v tabulce, zvolte **tabulky**.

   * Chcete-li změnit dotaz, můžete upravit řetězec dotazu na panelu hledání. 
   Pro vyšší výkon zvolte **Advanced Analytics**.

     ![Zobrazit akce a podrobnosti pro běh aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     Na stránce Azure Log Analytics můžete aktualizovat dotazy a zobrazit výsledky z tabulky. Tento dotaz používá [Kusto dotazovací jazyk](https://aka.ms/LogAnalyticsLanguageReference), který můžete upravit, pokud chcete zobrazit odlišné výsledky. 

     ![Azure Log Analytics – zobrazení dotazu](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Další postup

* [Monitorování zpráv B2B](../logic-apps/logic-apps-monitor-b2b-message.md)