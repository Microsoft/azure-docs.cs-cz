---
title: Dotaz pro B2B zprávy v protokolu analýzy - Azure Logic Apps | Microsoft Docs
description: Vytvářet dotazy ke sledování AS2, X 12 a EDIFACT zprávy v analýzy protokolů
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 520a1212eaccc48f8b8b423f7dede9c16409220b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300323"
---
# <a name="query-for-as2-x12-and-edifact-messages-in-log-analytics"></a>Dotaz pro AS2, X 12 a EDIFACT zprávy v analýzy protokolů

Chcete-li najít AS2, X12 nebo EDIFACT zprávy sledujete s [Azure Log Analytics](../log-analytics/log-analytics-overview.md), můžete vytvořit dotazy, které akce na základě konkrétních kritérií filtru. Například můžete najít na základě různých řízení konkrétní výměnu zpráv.

## <a name="requirements"></a>Požadavky

* Aplikace logiky, který je nastavený s protokolování diagnostiky. Další informace [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) a [postup nastavení protokolování pro tuto aplikaci logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Integrace účet, který je nastavený s sledování a protokolování. Další informace [postup vytvoření účtu integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a [postup nastavení sledování a protokolování pro tento účet](../logic-apps/logic-apps-monitor-b2b-message.md).

* Pokud jste to ještě neudělali, [publikování diagnostických dat k analýze protokolů](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) a [nastavení sledování v analýzy protokolů zpráv](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Po splnění předchozí požadavky, měli byste mít pracovního prostoru v analýzy protokolů. Měli byste použít ve stejném pracovním prostoru pro sledování vaší komunikace B2B ve analýzy protokolů. 
>  
> Pokud nemáte pracovní prostor analýzy protokolů, přečtěte si [jak vytvořit pracovní prostor analýzy protokolů](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="create-message-queries-with-filters-in-log-analytics"></a>Vytvořte dotazy zprávy s filtry v analýzy protokolů

Tento příklad ukazuje, jak můžete najít podle jejich výměnu řízení počtu zpráv.

> [!TIP] 
> Pokud znáte název pracovního prostoru analýzy protokolů, přejděte na domovskou stránku pracovního prostoru (`https://{your-workspace-name}.portal.mms.microsoft.com`) a začít od kroku 4. Jinak začněte v kroku 1.

1. V [portál Azure](https://portal.azure.com), zvolte **všechny služby**. Vyhledejte "analýzy protokolů" a potom vyberte **analýzy protokolů** jak je vidět tady:

   ![Najít analýzy protokolů](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. V části **analýzy protokolů**, najděte a vyberte pracovní prostor analýzy protokolů.

   ![Vyberte pracovní prostor analýzy protokolů](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. V části **správy**, zvolte **portálu OMS**.

   ![Zvolte portálu OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/omsportalpage.png)

4. Na domovské stránce, zvolte **hledání protokolů**.

   ![Na domovské stránce zvolte "Hledání protokolů"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -nebo-

   ![V nabídce zvolte "Hledání protokolů"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

5. Do vyhledávacího pole zadejte pole, které chcete najít a stiskněte klávesu **Enter**. Když začnete psát, analýzy protokolů se dozvíte možné shody a operací, které můžete použít. Další informace o [vyhledávání dat v analýzy protokolů](../log-analytics/log-analytics-log-searches.md).

   Tento příklad vyhledá pro události se **typ = AzureDiagnostics**.

   ![Začněte psát řetězec dotazu](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

6. V levém panelu vyberte časový rámec, který chcete zobrazit. Chcete-li přidat filtr do dotazu, zvolte **+ přidat**.

   ![Přidání filtru dotazu](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

7. V části **přidat filtry**, zadejte název filtru filtr, který chcete vyhledávat. Vyberte filtr a vyberte **+ přidat**.

   Číslo výměnu řízení najdete v tomto příkladu slovo "výměnu" vyhledá a vybere **event_record_messageProperties_interchangeControlNumber_s** jako filtr.

   ![Vyberte filtr](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

9. V levém panelu, vyberte hodnota filtru, který chcete použít a vyberte **použít**.

   Tento příklad vybere výměnu řízení číslo pro zprávy, které má být.

   ![Vyberte hodnotu filtru](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

10. Nyní se vraťte na dotaz, který vytváříte. Dotaz se aktualizovala se vybraný filtr událostí a hodnotou. Předchozí výsledky jsou nyní příliš filtrovány.

    ![Vrátí do dotazu s filtrované výsledky.](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Své uložit pro budoucí použití

1. Z dotazu na **hledání protokolů** vyberte **Uložit**. Zadejte název dotazu, vyberte kategorii a zvolte **Uložit**.

   ![Dejte dotazu, název a kategorii](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. Chcete-li zobrazit dotaz, zvolte **Oblíbené**.

   ![Zvolte "Oblíbené"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. V části **uložená hledání**, vyberte svůj dotaz tak, aby se zobrazí výsledky. Chcete-li aktualizovat dotaz vyhledávat odlišné výsledky, upravte dotaz.

   ![Vyberte dotaz](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-log-analytics"></a>Najít a spustit uložené dotazy v analýzy protokolů

1. Otevřete domovskou stránku pracovní prostor analýzy protokolů (`https://{your-workspace-name}.portal.mms.microsoft.com`) a zvolte **hledání protokolů**.

   ![Na domovské stránce analýzy protokolů zvolte "Hledání protokolů"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -nebo-

   ![V nabídce zvolte "Hledání protokolů"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. Na **hledání protokolů** – Domovská stránka, zvolte **Oblíbené**.

   ![Zvolte "Oblíbené"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. V části **uložená hledání**, vyberte svůj dotaz tak, aby se zobrazí výsledky. Chcete-li aktualizovat dotaz vyhledávat odlišné výsledky, upravte dotaz.

   ![Vyberte dotaz](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Další postup

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémata sledování X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní sledování schémata](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)