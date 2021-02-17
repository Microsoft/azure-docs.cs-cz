---
title: Zobrazení a vytváření dotazů pro Logic Apps v protokolech Azure Monitor
description: Zobrazení a vytváření dotazů v protokolech Azure Monitor pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5de795811923c965a1473f8d91e3fd654aeef266
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574552"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Zobrazení a vytváření dotazů pro monitorování a sledování v protokolech Azure Monitor pro Azure Logic Apps

Můžete zobrazit základní dotazy, které vytvářejí výsledky z [Azure monitor protokoly](../azure-monitor/logs/log-query-overview.md) a vytvářet dotazy, které filtrují výsledky na základě konkrétních kritérií. Můžete například vyhledat zprávy na základě konkrétního kontrolního čísla výměny. Dotazy používají [dotazovací jazyk Kusto](/azure/data-explorer/kusto/query/), který můžete upravit, pokud chcete zobrazit různé výsledky. Další informace najdete v tématu [Azure monitor dotazy protokolu](/azure/data-explorer/kusto/query/).

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Log Analytics. Pokud nemáte pracovní prostor Log Analytics, přečtěte si, [jak vytvořit pracovní prostor Log Analytics](../azure-monitor/logs/quick-create-workspace.md).

* Aplikace logiky, která je nastavená s Azure Monitor protokolování a odesílá tyto informace do pracovního prostoru Log Analytics. Přečtěte si, [jak nastavit protokoly Azure monitor pro vaši aplikaci logiky](../logic-apps/monitor-logic-apps.md).

* Pokud používáte účet pro integraci, ujistěte se, že jste nastavili účet pomocí Azure Monitor protokolování, které tyto informace odešle do pracovního prostoru Log Analytics. Přečtěte si, jak [nastavit protokolování Azure monitor pro účet pro integraci](../logic-apps/monitor-b2b-messages-log-analytics.md).

## <a name="view-queries-behind-results"></a>Zobrazení dotazů za výsledky

Chcete-li zobrazit nebo upravit dotaz, který vytváří výsledky v souhrnu pracovního prostoru, postupujte takto:

1. Na stránce jakékoli výsledky vyberte v dolní části **Zobrazit vše**.

   ![Zobrazit všechny výsledky](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   Otevře se stránka protokoly a zobrazí se dotaz za předchozí stránkou výsledků.

   ![Stránka protokoly – zobrazení dotazu](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. Na stránce **protokoly** můžete vybrat tyto možnosti:

   * Chcete-li zobrazit výsledky dotazu jako tabulku, v editoru dotazů vyberte možnost **tabulka**.

   * Chcete-li změnit dotaz, aktualizujte řetězec dotazu a vyberte možnost **Spustit** pro zobrazení výsledků v tabulce.

## <a name="create-your-own-query"></a>Vytvoření vlastního dotazu

Pokud chcete výsledky najít nebo filtrovat na základě konkrétních vlastností nebo hodnot, můžete vytvořit vlastní dotaz tak, že začnete od prázdného dotazu, nebo použijete existující dotaz. Další informace najdete v tématu [Začínáme s dotazy protokolu v Azure monitor](../azure-monitor/logs/get-started-queries.md).

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte pracovní prostor Log Analytics.

1. V nabídce pracovního prostoru v části **Obecné** vyberte **protokoly**.

1. Začněte od prázdného dotazu nebo všech dostupných existujících dotazů.

   * Chcete-li ověřit, zda jsou k dispozici nějaké existující dotazy, vyberte na panelu nástrojů dotaz  >  **historii** dotazů, které zobrazují dotazy z předchozích spuštěných dotazů, nebo vyberte možnost **Průzkumník dotazů**, který zobrazuje předem připravené dotazy.

     Například řešení Logic Apps B2B poskytuje tyto předem sestavené dotazy:

     ![Začínáme s předem vytvořenými dotazy pro řešení "Logic Apps B2B"](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Chcete-li začít z prázdného dotazu, spusťte v editoru dotazů zadání [dotazovacího jazyka Kusto](/azure/data-explorer/kusto/query/) pro váš dotaz.

     ![Spustit s prázdným dotazem](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Další kroky

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémata sledování X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní schémata sledování](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)