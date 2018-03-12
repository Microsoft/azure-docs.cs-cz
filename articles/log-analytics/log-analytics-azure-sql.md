---
title: "Řešení Azure SQL analýzy v Log Analytics | Microsoft Docs"
description: "Řešení analýzy SQL Azure pomáhá spravovat vaše databáze Azure SQL."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: magoedte
ms.openlocfilehash: 5fb7fd0be8b131ee098689b06c34c4e7c333801e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Monitorování databáze Azure SQL pomocí analýzy SQL Azure (Preview) v analýzy protokolů

![Azure SQL Analytics symbol](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Řešení Azure SQL analýzy v Azure Log Analytics shromažďuje a vizualizuje důležité metriky výkonu SQL Azure. Pomocí metriky, které shromažďujete s řešením, můžete vytvořit vlastní pravidla monitorování a výstrahy. A, můžete monitorovat Azure SQL Database a metriky elastického fondu napříč více předplatných Azure a elastického fondu a vizualizovat je. Řešení také vám pomůže identifikovat problémy v každé vrstvě zásobníku vaší aplikace.  Používá [Azure diagnostiky metriky](log-analytics-azure-storage.md) společně s zobrazení analýzy protokolů pro zobrazení dat o databází Azure SQL a elastické fondy v jedné pracovní prostor analýzy protokolů.

Toto řešení preview v současné době podporuje až 150 000 databází SQL Azure a 5 000 SQL elastické fondy podle pracovního prostoru.

Řešení Azure SQL Analytics, jako je k dispozici pro analýzy protokolů, ostatní pomáhá sledovat a přijímání oznámení o stavu vašich prostředků Azure – v tomto případě Azure SQL Database. Microsoft Azure SQL Database je služba škálovatelné relační databáze, který nabízí známé jako SQL Server – funkce pro aplikace běžící v cloudu Azure. Analýzy protokolů umožňuje shromažďovat, korelaci a vizualizovat strukturovaná i nestrukturovaná data.

Praktické přehled o používání řešení analýzy SQL Azure a příklady typických scénářů použití naleznete v tématu vložené video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Připojené zdroje

Řešení Azure SQL analýzy nepoužívá agentů pro připojení ke službě Analýza protokolů.

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje.

| Připojený zdroj | Podpora | Popis |
| --- | --- | --- |
| [Agenti systému Windows](log-analytics-windows-agent.md) | Ne | Přímé agentů v systému Windows nejsou používány nástrojem řešení. |
| [Agenti systému Linux](log-analytics-linux-agents.md) | Ne | Přímé agenty Linux nejsou používány nástrojem řešení. |
| [Skupiny správy nástroje SCOM](log-analytics-om-agents.md) | Ne | Přímé připojení z agenta nástroje SCOM k analýze protokolů nepoužívá řešení. |
| [Účet služby Azure Storage](log-analytics-azure-storage.md) | Ne | Analýzy protokolů číst data z účtu úložiště. |
| [Diagnostika Azure](log-analytics-azure-storage.md) | Ano | Metrika a protokolu dat Azure jsou odesílány Log Analytics přímo v Azure. |

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte, můžete vytvořit jeden pro [volné](https://azure.microsoft.com/free/).
- Pracovní prostor analýzy protokolů. Můžete použít existující, nebo můžete [vytvořte novou](log-analytics-quick-create-workspace.md) než začnete používat tohoto řešení.
- Povolit Azure Diagnostics pro databáze Azure SQL a elastické fondy a [nakonfigurovat je pro odesílají data k analýze protokolů](../sql-database/sql-database-metrics-diag-logging.md).

## <a name="configuration"></a>Konfigurace

Proveďte následující postup pro přidání řešení analýzy SQL Azure do pracovního prostoru.

1. Přidat řešení analýzy SQL Azure do pracovního prostoru z [Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) nebo pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md).
2. Na portálu Azure klikněte na tlačítko **vytvořit prostředek** > **monitorování + správu**.  
    ![Monitorování a správa](./media/log-analytics-azure-sql/monitoring-management.png)
3. V **monitorování + správu** seznamu klikněte na tlačítko **zobrazit všechny**.
4. V **doporučeno** seznamu, klikněte na tlačítko **Další**a pak v seznamu nové vyhledejte **analýzy SQL Azure (Preview)** a pak ho vyberte.  
    ![Řešení Azure SQL analýzy](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. V **analýzy SQL Azure (Preview)** oblast, klikněte na tlačítko **vytvořit**.  
    ![Vytvoření](./media/log-analytics-azure-sql/portal-create.png)
6. V **vytvořte nové řešení** oblasti, vyberte pracovní prostor, který chcete přidat řešení a pak klikněte na tlačítko **vytvořit**.  
    ![Přidat do pracovního prostoru](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Ke konfiguraci víc předplatných Azure

Pro podporu více předplatných, pomocí skriptu prostředí PowerShell z [povolit Azure prostředku metriky protokolování pomocí prostředí PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Při provádění skriptu k odesílání diagnostických dat z prostředků v rámci jednoho předplatného Azure do pracovního prostoru v jiného předplatného Azure, zadejte ID prostředku prostoru jako parametr.

**Příklad**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Použití řešení

>[!NOTE]
> Upgradujte vaší analýzy protokolů, abyste získali nejnovější verzi Azure SQL Analytics.
>

Když přidáte řešení do pracovního prostoru, dlaždici analýzy SQL Azure je přidat do pracovního prostoru, a zobrazí se v přehledu. Na dlaždici zobrazuje počet databází Azure SQL a Azure SQL elastické fondy, které řešení je připojen k.

![Azure SQL Analytics dlaždice](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Zobrazení dat analýzy SQL Azure

Klikněte na **analýzy SQL Azure** dlaždici otevřete řídicí panel Azure SQL Analytics. Řídicí panel obsahuje přehled všech databází, které jsou monitorovány prostřednictvím různých perspektiv. Pro různých perspektiv pracovat je nutné povolit správné metriky nebo protokoly na vašich prostředků SQL tok dat do pracovního prostoru analýzy protokolů Azure.

![Přehled analýzy Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Výběrem libovolné dlaždice, otevře se podrobná sestava do konkrétní perspektivy. Když je vybraný perspektivy, je otevřené podrobná sestava.

![Časové limity analýzy Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Každý perspektivy obsahuje souhrny předplatné, server, elastický fond a úroveň databáze. Kromě toho každá perspektiva zobrazuje hlediska specifické pro sestavy na pravé straně. Výběr předplatného, server, fondu nebo databáze ze seznamu bude pokračovat procházení.

| Perspektivy | Popis |
| --- | --- |
| Prostředek podle typu | Perspektivu, která udává všechny prostředky, které jsou monitorovány. Procházení poskytuje souhrn DTU a GB metriky. |
| Insights | Poskytuje hierarchické procházení do inteligentního statistiky. Další informace o inteligentního statistice. |
| Chyby | Poskytuje hierarchické procházení do SQL chybách, ke kterým došlo v databázích. |
| Časové limity | Poskytuje hierarchické procházení do SQL vypršení časových limitů, které bylo provedeno v databázích. |
| Blokování | Poskytuje hierarchické procházení do blokování SQL, které bylo provedeno v databázích. |
| Počká databáze | Poskytuje hierarchické procházení do SQL čekání Statistika na úrovni databáze. Obsahuje souhrnné informace o celkový čas čekání a doba čekání na typ čekání. |
| Doba trvání dotazu | Poskytuje hierarchické procházení do statistik provádění dotazů například doba trvání dotazu, využití procesoru, vstupně-výstupní operace dat využití, využití vstupně-výstupní operace protokolu. |
| Dotaz čekat | Poskytuje hierarchické procházení do statistiky čekání dotaz podle kategorie čekání. |

### <a name="intelligent-insights-report"></a>Inteligentní Statistika sestavy

Azure SQL Database [inteligentního Statistika](../sql-database/sql-database-intelligent-insights.md) umožňuje víte co se děje s výkon databáze. Všechny inteligentního Statistika shromážděných můžete vizualizována a přístupné prostřednictvím perspektivy statistiky.

![Přehled analýzy Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Elastický fond a databáze sestav

Databáze i elastické fondy mají své vlastní konkrétní sestavy, které budou zobrazit všechna data, která se shromažďují pro prostředek v určeném čase.

![Databáze Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Elastický fond Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Dotaz sestavy

Prostřednictvím doba trvání dotazu a dotaz počká perspektivy mohou korelovat výkon jakýkoli dotaz dotazu sestavy. Tato sestava porovnávány výkon dotazů v rámci různých databází a usnadňuje přesné databází, které provádějí vybraný dotaz dobře a ta, která jsou pomalé.

![Analytické dotazy Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analyzovat data a vytvářet výstrahy

Výstrahy můžete snadno vytvořit s dat pocházejících z prostředků Azure SQL Database. Tady jsou některé užitečné [hledání protokolů](log-analytics-log-searches.md) dotazy, které můžete použít pro výstrahy:

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]


*Vysoký počet jednotek DTU na databázi Azure SQL*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*Vysoký počet jednotek DTU na fond elastické databáze Azure SQL*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

Tyto dotazy na základě výstrahy můžete výstrahy na specifické prahové hodnoty pro Azure SQL Database a elastické fondy. Konfigurace oznámení pro pracovní prostor analýzy protokolů:

#### <a name="to-configure-an-alert-for-your-workspace"></a>Konfigurace oznámení pro pracovní prostor

1. Přejděte na [portálu OMS](http://mms.microsoft.com/) a přihlaste se.
2. Otevřete pracovní prostor, který jste nakonfigurovali pro řešení.
3. Na stránce Přehled klikněte na **analýzy SQL Azure (Preview)** dlaždici.
4. Spusťte jeden z příkladů dotazů.
5. V protokolu hledání, klikněte na **výstrahy**.  
![Vytvořit výstrahu pro vyhledávání](./media/log-analytics-azure-sql/create-alert01.png)
6. Na **přidat pravidlo výstrahy** nakonfigurujte příslušné vlastnosti a specifické prahové hodnoty, které chcete a pak klikněte na tlačítko **Uložit**. 
![Přidání pravidla výstrahy](./media/log-analytics-azure-sql/create-alert02.png)

## <a name="next-steps"></a>Další postup

- Použití [protokolu hledání](log-analytics-log-searches.md) v analýzy protokolů, chcete-li zobrazit podrobné dat Azure SQL.
- [Vytvořit vlastní řídicí panely](log-analytics-dashboards.md) zobrazující dat Azure SQL.
- [Vytvářet výstrahy](log-analytics-alerts.md) při výskytu určité události Azure SQL.
