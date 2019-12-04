---
title: Vytváření sledovacích dotazů pro zprávy B2B
description: Vytváření dotazů, které sledují zprávy AS2, X12 a EDIFACT v Azure Log Analytics pro Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 36cf45aa0f7d46b62caa586d1939ec52e67b1a3e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792855"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Vytváření sledovacích dotazů pro zprávy B2B v protokolu Azure Monitor pro Azure Logic Apps

Chcete-li vyhledat zprávy AS2, X12 nebo EDIFACT, které sledujete pomocí [protokolů Azure monitor](../log-analytics/log-analytics-overview.md), můžete vytvářet dotazy, které filtrují akce založené na konkrétních kritériích. Můžete například vyhledat zprávy na základě konkrétního kontrolního čísla výměny.

> [!NOTE]
> Na této stránce jsou dřív popsané kroky, jak provádět tyto úlohy s Microsoft Operations Management Suite (OMS), která se [vyřazuje z ledna 2019](../azure-monitor/platform/oms-portal-transition.md), místo toho nahrazuje tyto kroky pomocí Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Předpoklady

* Aplikace logiky, která je nastavená pomocí diagnostického protokolování. Naučte [se, jak vytvořit aplikaci logiky](quickstart-create-first-logic-app-workflow.md) a [jak nastavit protokolování pro tuto aplikaci logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Účet pro integraci, který je nastavený s monitorováním a protokolováním. Přečtěte si, [jak vytvořit účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a [jak nastavit monitorování a protokolování pro tento účet](../logic-apps/logic-apps-monitor-b2b-message.md).

* Pokud jste to ještě neudělali, [publikujte diagnostická data pro Azure monitor protokolů](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) a [nastavte sledování zpráv v protokolech Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Vytváření dotazů s filtry

Chcete-li vyhledat zprávy na základě konkrétních vlastností nebo hodnot, můžete vytvořit dotazy, které používají filtry. 

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Všechny služby**. Do vyhledávacího pole Najděte Log Analytics a vyberte **Log Analytics**.

   ![Vyberte Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. V části **Log Analytics**Najděte pracovní prostor Log Analytics a vyberte ho. 

   ![Vybrat Log Analytics pracovní prostor](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. V nabídce pracovního prostoru v části **Obecné**vyberte buď **protokoly (Classic)** nebo **protokoly**. 

   Tento příklad ukazuje, jak používat klasické zobrazení protokolů. 
   Pokud zvolíte **Zobrazit protokoly** v části **maximalizovat prostředí pro Log Analytics** , v části **Hledat a analyzovat protokoly**získáte **protokoly (klasické zobrazení)** . 

   ![Zobrazení klasických protokolů](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. V poli pro úpravy dotazu začněte psát název pole, které chcete najít. Když začnete psát, zobrazí Editor dotazů možné shody a operace, které můžete použít. Po vytvoření dotazu klikněte na tlačítko **Spustit** nebo stiskněte klávesu ENTER.

   Tento příklad vyhledá shodu v **LogicAppB2B**. 
   Přečtěte si další informace o [tom, jak najít data v protokolech Azure monitor](../log-analytics/log-analytics-log-searches.md).

   ![Začněte psát řetězec dotazu.](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Chcete-li změnit časový rámec, který chcete zobrazit, v levém podokně vyberte ze seznamu trvání nebo přetáhněte posuvník. 

   ![Změnit časový rámec](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Chcete-li do dotazu přidat filtr, klikněte na tlačítko **Přidat**. 

   ![Přidat filtr do dotazu](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. V části **Přidat filtry**zadejte název filtru, který chcete najít. Pokud tento filtr najdete, vyberte tento filtr. V levém podokně klikněte na tlačítko **Přidat** znovu.

   Například tady je jiný dotaz, který hledá události **Type = = "AzureDiagnostics"** a vyhledá výsledky založené na řídicím čísle výměny výběrem filtru **event_record_messageProperties_interchangeControlNumber_s** .

   ![Vybrat hodnotu filtru](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Po výběru možnosti **Přidat**se dotaz aktualizuje o vybranou událost filtru a hodnotu. 
   Předchozí výsledky jsou teď filtrované. 

   Tento dotaz například vyhledá **typ = = "AzureDiagnostics"** a vyhledá výsledky založené na řídicím čísle výměny pomocí filtru **event_record_messageProperties_interchangeControlNumber_s** .

   ![Filtrované výsledky](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Uložit dotaz

Pokud chcete dotaz uložit v zobrazení **protokolů (Classic)** , postupujte takto:

1. V dotazu na stránce **protokoly (Classic)** vyberte **Analýza**. 

   ![Výběr možnosti Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. Na panelu nástrojů dotazu klikněte na tlačítko **Uložit**.

   ![Volba možnosti Uložit](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Zadejte podrobnosti o dotazu, například zadejte název dotazu, vyberte **dotaz**a zadejte název kategorie. Jakmile budete hotoví, vyberte **Uložit**.

   ![Volba možnosti Uložit](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Uložené dotazy zobrazíte tak, že se vrátíte na stránku dotazu. Na panelu nástrojů dotazu vyberte možnost **uložená hledání**.

   ![Výběr uložených hledání](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. V části **uložená hledání**vyberte dotaz, abyste mohli zobrazit výsledky. 

   ![Vybrat dotaz](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Chcete-li aktualizovat dotaz, abyste mohli najít různé výsledky, upravte dotaz.

## <a name="find-and-run-saved-queries"></a>Hledání a spouštění uložených dotazů

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Všechny služby**. Do vyhledávacího pole Najděte Log Analytics a vyberte **Log Analytics**.

   ![Vyberte Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. V části **Log Analytics**Najděte pracovní prostor Log Analytics a vyberte ho. 

   ![Vybrat Log Analytics pracovní prostor](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. V nabídce pracovního prostoru v části **Obecné**vyberte buď **protokoly (Classic)** nebo **protokoly**. 

   Tento příklad ukazuje, jak používat klasické zobrazení protokolů. 

1. Po otevření stránky dotazu klikněte na panelu nástrojů dotazu na možnost **uložená hledání**.

   ![Výběr uložených hledání](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. V části **uložená hledání**vyberte dotaz, abyste mohli zobrazit výsledky. 

   ![Vybrat dotaz](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   Dotaz se spustí automaticky, ale pokud se z nějakého důvodu dotaz nespustí, v editoru dotazů vyberte **Spustit**.

## <a name="next-steps"></a>Další kroky

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémata sledování X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní schémata sledování](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)