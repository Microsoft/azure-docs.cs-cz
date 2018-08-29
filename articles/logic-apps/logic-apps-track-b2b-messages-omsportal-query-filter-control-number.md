---
title: Vytváření dotazů na zprávy B2B ve službě Log Analytics – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření dotazů, které sledování AS2, X 12 a EDIFACT zprávy pomocí služby Log Analytics pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/19/2018
ms.openlocfilehash: baccd255fc2812eae0de3a98dfcef3dcbc7e1b46
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124266"
---
# <a name="create-queries-for-tracking-as2-x12-and-edifact-messages-in-log-analytics-for-azure-logic-apps"></a>Vytváření dotazů ke sledování AS2, X 12 a EDIFACT zpráv ve službě Log Analytics pro Azure Logic Apps

Chcete-li najít AS2, X12 nebo EDIFACT zprávy, že sledujete s [Azure Log Analytics](../log-analytics/log-analytics-overview.md), můžete vytvořit dotazy, které akce na základě určitých kritérií filtru. Například můžete vyhledat zprávy založené na kontrolní číslo výměny konkrétní.

## <a name="requirements"></a>Požadavky

* Aplikace logiky, která je nastavená pomocí diagnostického protokolování. Přečtěte si [jak vytvořit aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) a [jak nastavit protokolování pro tuto aplikaci logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Účet integrace, který je nastaven díky monitorování a protokolování. Přečtěte si [tom, jak vytvořit integrační účet](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a [jak nastavit monitorování a protokolování pro tento účet](../logic-apps/logic-apps-monitor-b2b-message.md).

* Pokud jste tak dosud neučinili, [publikovat diagnostická data do Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) a [nastavení sledování v Log Analytics zpráv](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Po splnění předchozí požadavky, měli byste mít pracovní prostor v Log Analytics. Měli byste použít stejný pracovní prostor pro sledování vaši komunikaci B2B ve službě Log Analytics. 
>  
> Pokud nemáte pracovní prostor Log Analytics, přečtěte si [jak vytvořit pracovní prostor Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="create-message-queries-with-filters-in-log-analytics"></a>Vytvořit zprávu dotazy s filtry v Log Analytics

Tento příklad ukazuje, jak najít zprávy podle jejich kontrolní číslo výměny.

> [!TIP] 
> Pokud znáte název vašeho pracovního prostoru Log Analytics, přejděte na domovskou stránku vašeho pracovního prostoru (`https://{your-workspace-name}.portal.mms.microsoft.com`) a začít od kroku 4. V opačném případě spusťte v kroku 1.

1. V [webu Azure portal](https://portal.azure.com), zvolte **všechny služby**. Vyhledejte "log analytics" a klikněte na tlačítko **Log Analytics** jak je znázorněno zde:

   ![Hledání Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. V části **Log Analytics**vyhledejte a vyberte pracovní prostor Log Analytics.

   ![Vyberte pracovní prostor Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. V části **správu**, zvolte **prohledávání protokolů**.

   ![Zvolte Lo hledání](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/azure-portal-page.png)

4. Do vyhledávacího pole zadejte pole, které chcete najít a stiskněte klávesu **Enter**. Když začnete psát, Log Analytics se dozvíte možných shod a operace, které můžete použít. Další informace o [jak najít data ve službě Log Analytics](../log-analytics/log-analytics-log-searches.md).

   Tento příklad vyhledá pro události se **typ = AzureDiagnostics**.

   ![Začněte psát řetězec dotazu](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

5. V levém panelu vyberte časový rámec, který chcete zobrazit. Přidání filtru do dotazu, zvolte **+ přidat**.

   ![Přidání filtru do dotazu](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

6. V části **přidat filtry**, zadejte název filtru, abyste mohli vyhledat požadovaný filtr. Vyberte filtr a zvolte **+ přidat**.

   Kontrolní číslo výměny najdete v tomto příkladu vyhledává slova "výměnu" a vybere **event_record_messageProperties_interchangeControlNumber_s** jako filtr.

   ![Vyberte filtr](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

7. V levém panelu, vyberte hodnotu filtru, který chcete použít a zvolte **použít**.

   Tento příklad vybere kontrolní číslo výměny zpráv, které chceme.

   ![Vyberte hodnotu filtru](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

8. Nyní se vraťte na dotaz, který vytváříte. Váš dotaz má aktualizované a přinášejí vybraný filtr událostí a hodnotu. Předchozí výsledky se teď filtrují příliš.

    ![Vraťte se do dotazu pomocí filtrované výsledky](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Uložit dotaz pro budoucí použití

1. Z dotazu na **prohledávání protokolů** zvolte **Uložit**. Pojmenujte svůj dotaz, vyberte kategorii a zvolte **Uložit**.

   ![Zadejte dotaz, název a kategorie](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. Chcete-li zobrazit dotaz, zvolte **Oblíbené**.

   ![Zvolte možnost "Oblíbené"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. V části **uložená hledání**, vyberte svůj dotaz tak, aby se zobrazí výsledky. Pro aktualizaci dotazu, abyste mohli vyhledat jiné výsledky, upravte dotaz.

   ![Vyberte dotaz](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-log-analytics"></a>Vyhledání a spuštění uložené dotazy v Log Analytics

1. Otevřete domovskou stránku pracovního prostoru Log Analytics (`https://{your-workspace-name}.portal.mms.microsoft.com`) a zvolte **prohledávání protokolů**.

   ![Na domovské stránce Log Analytics zvolte možnost "Prohledávání protokolů"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -nebo-

   ![V nabídce vyberte "Prohledávání protokolů"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. Na **prohledávání protokolů** domovskou stránku, zvolte **Oblíbené**.

   ![Zvolte možnost "Oblíbené"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. V části **uložená hledání**, vyberte svůj dotaz tak, aby se zobrazí výsledky. Pro aktualizaci dotazu, abyste mohli vyhledat jiné výsledky, upravte dotaz.

   ![Vyberte dotaz](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Další postup

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémata sledování X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní sledování schémata](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)