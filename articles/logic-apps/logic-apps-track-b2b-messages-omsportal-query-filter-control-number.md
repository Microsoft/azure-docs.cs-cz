---
title: Vytváření dotazů sledování zpráv B2B v Log Analytics – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření dotazů, které sledování AS2, X 12 a EDIFACT zpráv ve službě Azure Log Analytics pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 5cfab07e19e543b7a46fcce8f449a46395c144d6
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995321"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-log-analytics-for-azure-logic-apps"></a>Vytváření dotazů sledování zpráv B2B v Azure Log Analytics pro Azure Logic Apps

Chcete-li najít AS2, X12 nebo EDIFACT zprávy, že sledujete s [Azure Log Analytics](../log-analytics/log-analytics-overview.md), můžete vytvořit dotazy, které akce na základě určitých kritérií filtru. Například můžete vyhledat zprávy založené na kontrolní číslo výměny konkrétní.

> [!NOTE]
> Tato stránka výše popsaný postup, jak k provádění těchto úkolů se Microsoft Operations Management Suite (OMS), což je [vyřazení z provozu v lednu 2019](../azure-monitor/platform/oms-portal-transition.md), nahradí tyto kroky místo toho pomocí služby Azure Log Analytics. 

## <a name="prerequisites"></a>Požadavky

* Aplikace logiky, která je nastavená pomocí diagnostického protokolování. Přečtěte si [jak vytvořit aplikaci logiky](quickstart-create-first-logic-app-workflow.md) a [jak nastavit protokolování pro tuto aplikaci logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Účet integrace, který je nastaven díky monitorování a protokolování. Přečtěte si [tom, jak vytvořit integrační účet](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a [jak nastavit monitorování a protokolování pro tento účet](../logic-apps/logic-apps-monitor-b2b-message.md).

* Pokud jste tak dosud neučinili, [publikovat diagnostická data do Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) a [nastavení sledování v Log Analytics zpráv](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Vytváření dotazů s filtry

K vyhledání zpráv na základě konkrétních vlastností nebo hodnot, můžete vytvořit dotazy, které používají filtry. 

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Všechny služby**. Do vyhledávacího pole vyhledejte "log analytics" a vyberte **Log Analytics**.

   ![Vyberte Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. V části **Log Analytics**vyhledejte a vyberte pracovní prostor Log Analytics. 

   ![Vyberte pracovní prostor Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. V nabídce pracovního prostoru v části **Obecné**, vyberte buď **protokoly (classic)** nebo **protokoly**. 

   Tento příklad ukazuje způsob použití klasického zobrazení protokolů. 
   Pokud se rozhodnete **zobrazit protokoly** v **maximálně využívat Log Analytics** pod **Hledat a analyzovat protokoly**, získáte **protokoly (klasické zobrazení)** . 

   ![Klasické zobrazení protokolů](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. V dotazu upravit pole, začněte psát název pole, které chcete najít. Když začnete psát, editor dotazů zobrazí možných shod a operace, které můžete použít. Po vytvoření dotazu zvolte **spustit** nebo stiskněte klávesu Enter.

   Tento příklad vyhledá odpovídající položky na **LogicAppB2B**. 
   Další informace o [jak najít data ve službě Log Analytics](../log-analytics/log-analytics-log-searches.md).

   ![Začněte psát řetězec dotazu](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Chcete-li změnit časový rámec, který chcete zobrazit, v levém podokně vyberte ze seznamu dobu trvání nebo přetažením posuvníku. 

   ![Změnit časový rámec](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Přidání filtru do dotazu, zvolte **přidat**. 

   ![Přidání filtru do dotazu](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. V části **přidat filtry**, zadejte název filtru, které chcete najít. Pokud zjistíte filtr, vyberte tento filtr. V levém podokně vyberte **přidat** znovu.

   Například tady je jiný dotaz, který hledá **typ == "AzureDiagnostics"** události a najde výsledky podle kontrolní číslo výměny tak, že vyberete **event_record_messageProperties_ interchangeControlNumber_s** filtru.

   ![Vyberte hodnotu filtru](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Po zvolení **přidat**, dotazu je aktualizován vybraný filtr událostí a hodnotu. 
   Předchozí výsledky se teď filtrují příliš. 

   Například tento dotaz vyhledává **typ == "AzureDiagnostics"** a najde výsledky podle kontrolní číslo výměny pomocí **event_record_messageProperties_interchangeControlNumber_s**filtru.

   ![Filtrované výsledky](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Uložit dotaz

Uložit dotaz v **protokoly (classic)** zobrazení, postupujte podle těchto kroků:

1. Z dotazu na **protokoly (classic)** zvolte **Analytics**. 

   ![Zvolte možnost "Analytics"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. Na panelu nástrojů themeroller dotazu **Uložit**.

   ![Volba možnosti Uložit](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Zadejte podrobnosti o dotazu, třeba, zadejte název, vyberte dotaz **dotazu**a zadejte název kategorie. Jakmile budete hotoví, vyberte **Uložit**.

   ![Volba možnosti Uložit](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Chcete-li zobrazit uložené dotazy, přejděte zpět na stránku dotazu. Na panelu nástrojů themeroller dotazu **uložená hledání**.

   ![Zvolte možnost "Uložená hledání"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. V části **uložená hledání**, vyberte dotaz, abyste mohli zobrazit výsledky. 

   ![Vyberte dotaz](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Pro aktualizaci dotazu, abyste mohli vyhledat jiné výsledky, upravte dotaz.

## <a name="find-and-run-saved-queries"></a>Vyhledání a spuštění uložené dotazy

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Všechny služby**. Do vyhledávacího pole vyhledejte "log analytics" a vyberte **Log Analytics**.

   ![Vyberte Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. V části **Log Analytics**vyhledejte a vyberte pracovní prostor Log Analytics. 

   ![Vyberte pracovní prostor Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. V nabídce pracovního prostoru v části **Obecné**, vyberte buď **protokoly (classic)** nebo **protokoly**. 

   Tento příklad ukazuje způsob použití klasického zobrazení protokolů. 

1. Po otevření stránky dotazu na panelu nástrojů dotazu zvolte **uložená hledání**.

   ![Zvolte možnost "Uložená hledání"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. V části **uložená hledání**, vyberte dotaz, abyste mohli zobrazit výsledky. 

   ![Vyberte dotaz](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   Dotaz se automaticky spustí, ale pokud nelze spustit dotaz z jakéhokoli důvodu, a to v editoru dotazů zvolte **spustit**.

## <a name="next-steps"></a>Další postup

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémata sledování X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní sledování schémata](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)