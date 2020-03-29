---
title: Zobrazení a vytváření dotazů na aplikace logiky v protokolech Azure Monitoru
description: Zobrazení a vytváření dotazů v protokolech Azure Monitoru pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908069"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Zobrazení a vytváření dotazů pro monitorování a sledování v protokolech Azure Monitoru pro Azure Logic Apps

Můžete zobrazit základní dotazy, které vytvářejí výsledky z [protokolů Azure Monitor](../log-analytics/log-analytics-overview.md) a vytvářet dotazy, které filtrují výsledky na základě konkrétních kritérií. Můžete například najít zprávy založené na konkrétním čísle ovládacího prvku výměny. Dotazy používají [dotazovací jazyk Kusto](https://aka.ms/LogAnalyticsLanguageReference), který můžete upravit, pokud chcete zobrazit různé výsledky. Další informace naleznete v tématu [Dotazy protokolu Azure Monitor](../azure-monitor/log-query/query-language.md).

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Log Analytics. Pokud nemáte pracovní prostor Log Analytics, přečtěte si, [jak vytvořit pracovní prostor Analýzy protokolů](../azure-monitor/learn/quick-create-workspace.md).

* Aplikace logiky, která je nastavena pomocí protokolování Azure Monitora a odesílá tyto informace do pracovního prostoru Analýzy protokolů. Přečtěte [si, jak nastavit protokoly Azure Monitoru pro vaši aplikaci logiky](../logic-apps/monitor-logic-apps.md).

* Pokud používáte účet integrace, ujistěte se, že jste nastavili účet s protokolováním Azure Monitor u odesílání informací do pracovního prostoru Log Analytics. Přečtěte si, jak [nastavit protokolování Azure Monitoru pro váš účet integrace](../logic-apps/monitor-b2b-messages-log-analytics.md).

## <a name="view-queries-behind-results"></a>Zobrazit dotazy za výsledky

Chcete-li zobrazit nebo upravit dotaz, který vytváří výsledky v souhrnu pracovního prostoru, postupujte takto:

1. Na libovolné stránce s výsledky v dolní části vyberte **Zobrazit vše**.

   ![Zobrazit všechny výsledky](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   Otevře se stránka Protokoly a zobrazí dotaz za předchozí stránku výsledků.

   ![Stránka Protokoly – zobrazení dotazu](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. Na stránce **Protokoly** můžete vybrat tyto možnosti:

   * Chcete-li zobrazit výsledky dotazu jako tabulku, vyberte v editoru dotazů **možnost Tabulka**.

   * Chcete-li dotaz změnit, aktualizujte řetězec dotazu a výběrem **možnosti Spustit** zobrazte výsledky v tabulce.

## <a name="create-your-own-query"></a>Vytvoření vlastního dotazu

Chcete-li najít nebo filtrovat výsledky na základě určitých vlastností nebo hodnot, můžete vytvořit vlastní dotaz tak, že začnete s prázdným dotazem nebo použijete existující dotaz. Další informace najdete [v tématu Začínáme s dotazy protokolu v Azure Monitoru](../azure-monitor/log-query/get-started-queries.md).

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte pracovní prostor Log Analytics.

1. V nabídce pracovního prostoru vyberte v části **Obecné** **položku Protokoly**.

1. Začněte z prázdného dotazu nebo z existujících existujících dotazů.

   * Chcete-li zkontrolovat, zda jsou k dispozici existující dotazy, vyberte na panelu nástrojů dotazu buď **ukázky dotazů** > **Historie**, která zobrazuje dotazy z předchozích spuštění dotazů, nebo vyberte **Průzkumníka dotazů**, který zobrazuje předem sestavené dotazy.

     Například řešení Logic Apps B2B poskytuje tyto předem sestavené dotazy:

     ![Začněte s předem vytvořenými dotazy řešení "Logic Apps B2B"](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Chcete-li začít z prázdného dotazu, začněte v editoru dotazů psát [dotazovací jazyk Kusto](../azure-monitor/log-query/query-language.md) pro dotaz.

     ![Začít s prázdným dotazem](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Další kroky

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémata sledování X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní schémata sledování](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)