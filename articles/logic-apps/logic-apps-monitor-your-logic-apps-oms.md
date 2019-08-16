---
title: Monitorování aplikací logiky pomocí Azure Monitor-Azure Logic Apps
description: Získání přehledů a ladění dat pro řešení potíží a diagnostiku spuštění aplikace logiky s protokoly Azure Monitor
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 08/16/2019
ms.openlocfilehash: 2f82bd9c0bcacf2c552df84cdd4f8f2cd6a68c8a
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543228"
---
# <a name="get-insights-and-debugging-data-for-logic-apps-by-using-azure-monitor-logs"></a>Získání přehledů a ladění dat pro Logic Apps pomocí Azure Monitorch protokolů

Pokud chcete monitorovat a získat podrobné informace o Logic Apps, zapněte [Azure monitor protokoly](../log-analytics/log-analytics-overview.md) při vytváření aplikace logiky. Protokoly Azure Monitor poskytují diagnostické protokolování a monitorování pro vaše aplikace logiky při instalaci řešení Logic Apps Management v Azure Portal. Toto řešení také poskytuje agregované informace pro vaši aplikaci logiky s konkrétními podrobnostmi, jako je stav, doba provádění, stav opětovného odeslání a ID korelace. Tento článek ukazuje, jak zapnout protokoly Azure Monitor, abyste mohli zobrazit běhové události a data pro spuštění aplikace logiky.

V tomto tématu se dozvíte, jak nastavit protokoly Azure Monitor při vytváření aplikace logiky. Pokud chcete zapnout protokoly Azure Monitor pro existující aplikace logiky, postupujte podle těchto kroků [a zapněte diagnostické protokolování a odešlete data modulu runtime aplikace logiky do protokolů Azure monitor](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Na této stránce se dřív popsal postup provedení těchto úloh s Microsoft Operations Management Suite (OMS), která se využívala [v lednu 2019](../azure-monitor/platform/oms-portal-transition.md), a nahrazuje tyto kroky pomocí [protokolů Azure monitor](../azure-monitor/platform/data-platform-logs.md), které nahradily Log Analytics. Data protokolu se pořád ukládají do Log Analyticsho pracovního prostoru a pořád se shromažďují a analyzují pomocí stejné služby Log Analytics. Další informace najdete v tématu [Azure monitor změny terminologie](../azure-monitor/terminology.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete Log Analytics pracovní prostor. Naučte [se, jak vytvořit pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

## <a name="turn-on-logging-for-new-logic-apps"></a>Zapnutí protokolování pro nové aplikace logiky

1. V [Azure Portal](https://portal.azure.com)vytvořte aplikaci logiky. V hlavní nabídce Azure vyberte **vytvořit prostředek** > **Integration** > **Logic App**.

   ![Vytvoření nové aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/create-new-logic-app.png)

1. V části **Aplikace logiky**proveďte tyto kroky:

   1. Zadejte název vaší aplikace logiky a vyberte své předplatné Azure.

   1. Vytvořte nebo vyberte skupinu prostředků Azure. Vyberte umístění aplikace logiky.

   1. V části **Log Analytics**vyberte **zapnuto**.

   1. V seznamu **pracovní prostor Log Analytics** vyberte pracovní prostor, ve kterém chcete poslat data z vaší aplikace logiky.

      ![Zadání informací o aplikaci logiky](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app-details.png)

      Po dokončení tohoto kroku Azure vytvoří aplikaci logiky, která je teď přidružená k vašemu pracovnímu prostoru Log Analytics. Tento krok také automaticky nainstaluje řešení pro správu Logic Apps do svého pracovního prostoru.

   1. Až to budete mít, vyberte **Vytvořit**.

1. Pokud chcete zobrazit vaše běhy aplikace logiky, [pokračujte v tomto postupu](#view-logic-app-runs-oms).

## <a name="install-logic-apps-management-solution"></a>Instalace řešení pro správu Logic Apps

Pokud jste již zapnuli Azure Monitor protokoly při vytváření aplikace logiky, přeskočte tento krok. Již máte nainstalované řešení pro správu Logic Apps.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Všechny služby**. Do vyhledávacího pole Najděte "pracovní prostory Log Analytics" a vyberte **Log Analytics pracovní prostory**.

   ![Vyberte Log Analytics pracovní prostory.](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. V části **Log Analytics pracovní prostory**vyberte svůj pracovní prostor.

   ![Vyberte svůj pracovní prostor Log Analytics](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. V podokně Přehled v části Začínáme **s Log Analytics** > **Konfigurace řešení monitorování**vyberte **Zobrazit řešení**.

   ![Vyberte Zobrazit řešení.](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. V části **Přehled**vyberte **Přidat**.

   ![Vyberte Přidat.](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

1. Po otevření **Marketplace** do vyhledávacího pole zadejte "Správa Logic Apps" a vyberte **Logic Apps Management**.

   ![Vyberte Logic Apps Management.](./media/logic-apps-monitor-your-logic-apps-oms/select-logic-apps-management.png)

1. V podokně s popisem řešení vyberte **vytvořit**.

   ![Vyberte "vytvořit" pro "Logic Apps Management"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

1. Zkontrolujte a potvrďte Log Analytics pracovní prostor, do kterého chcete řešení nainstalovat, a pak vyberte **vytvořit** znovu.

   ![Vyberte "vytvořit" pro "Logic Apps Management"](./media/logic-apps-monitor-your-logic-apps-oms/confirm-log-analytics-workspace.png)

   Jakmile Azure nasadí řešení do skupiny prostředků Azure, která obsahuje váš pracovní prostor Log Analytics, toto řešení se zobrazí v podokně Souhrn v pracovním prostoru.

   ![Podokno souhrnu pracovního prostoru](./media/logic-apps-monitor-your-logic-apps-oms/workspace-summary-pane-logic-apps-management.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Zobrazit informace o běhu aplikace logiky

Po spuštění aplikace logiky můžete zobrazit stav a počet těchto běhů na dlaždici **správa Logic Apps** .

1. Přejdete do pracovního prostoru Log Analytics a vyberete možnost Souhrn > **Logic Apps Správa** **pracovního prostoru**.

   ![Stav a počet spuštění aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary.png)

   Tady se vaše běhy aplikace logiky seskupují podle názvu nebo podle stavu spuštění. Tato stránka obsahuje také podrobnosti o selhání v akcích nebo aktivačních událostech pro spuštění aplikace logiky.

   ![Shrnutí stavu spuštění aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary-details.png)

1. Pokud chcete zobrazit všechna spuštění konkrétní aplikace logiky nebo stav, vyberte řádek pro aplikaci logiky nebo stav.

   Tady je příklad, který ukazuje všechna spuštění pro konkrétní aplikaci logiky:

   ![Zobrazení spuštění pro aplikaci logiky nebo stav](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Tato stránka obsahuje tyto rozšířené možnosti:

   * **Sledované vlastnosti:**

     V tomto sloupci se zobrazují sledované vlastnosti, které jsou seskupené podle akcí pro aplikaci logiky. Chcete-li zobrazit sledované vlastnosti, vyberte možnost **Zobrazit**. Chcete-li vyhledat sledované vlastnosti, použijte filtr sloupce.

     ![Zobrazení sledovaných vlastností aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Jakékoli nově přidané sledované vlastnosti mohou trvat 10-15 minut, než se budou zobrazovat poprvé. Přečtěte si, [jak přidat sledované vlastnosti do aplikace logiky](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Odešlete** Můžete znovu odeslat jednu nebo více spuštění aplikace logiky, které selhaly, byly úspěšné nebo stále spuštěné. Zaškrtněte políčka pro spuštění, která chcete znovu odeslat, a pak vyberte **znovu odeslat**.

     ![Znovu spustit běhy aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Chcete-li tyto výsledky filtrovat, můžete provádět filtrování na straně klienta i na straně serveru.

   * **Filtr na straně klienta**: U každého sloupce vyberte filtry, které chcete, například:

     ![Příklady filtrů sloupců](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtr na straně serveru**: Chcete-li vybrat konkrétní časový interval nebo omezit počet zobrazených běhů, použijte ovládací prvek rozsah v horní části stránky. Ve výchozím nastavení se zobrazují jenom 1 000 záznamů.

     ![Změna časového intervalu](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)

1. Pokud chcete zobrazit všechny akce a jejich podrobnosti pro konkrétní běh, vyberte řádek pro spuštění aplikace logiky.

   Tady je příklad, který ukazuje všechny akce pro konkrétní spuštění aplikace logiky:

   ![Zobrazení akcí pro spuštění aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)

1. Pokud chcete zobrazit dotaz za výsledky nebo zobrazit všechny výsledky, vyberte na stránce výsledky možnost **Zobrazit vše**. otevře se stránka hledání v protokolu.

   ![Zobrazit vše na stránkách výsledků](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)

   Na stránce prohledávání protokolu můžete zvolit tyto možnosti:

   * Chcete-li zobrazit výsledky dotazu v tabulce, vyberte možnost **tabulka**.

   * Chcete-li změnit dotaz, můžete upravit řetězec dotazu na panelu hledání. Pro lepší prostředí vyberte Pokročilá **Analýza**.

     ![Zobrazení akcí a podrobností pro spuštění aplikace logiky](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)

     Na stránce Log Analytics můžete aktualizovat dotazy a zobrazit výsledky z tabulky. Tento dotaz používá [dotazovací jazyk Kusto](https://aka.ms/LogAnalyticsLanguageReference), který můžete upravit, pokud chcete zobrazit různé výsledky.

     ![Log Analytics – zobrazení dotazu](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Další kroky

* [Monitorování zpráv B2B](../logic-apps/logic-apps-monitor-b2b-message.md)