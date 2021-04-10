---
title: Azure Monitor protokolů pomocí víceklientské aplikace
description: Nastavení a použití protokolů Azure Monitor s víceklientské aplikací Azure SQL Database SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 029e3ba799e5f239bde0ef049316dd268ebe4c8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588777"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-azure-sql-database-saas-app"></a>Nastavení a použití protokolů Azure Monitor s víceklientské aplikací Azure SQL Database SaaS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu nastavíte a použijete [protokoly Azure monitor](../../azure-monitor/logs/log-query-overview.md) k monitorování elastických fondů a databází. Tento kurz sestaví v [kurzu monitorování a správy výkonu](saas-dbpertenant-performance-monitoring.md). Ukazuje, jak používat protokoly Azure Monitor k rozšíření monitorování a upozorňování, které jsou k dispozici v Azure Portal. Protokoly Azure Monitor podporují monitorování tisíců elastických fondů a stovky tisíc databází. Protokoly Azure Monitor poskytují jedno řešení monitorování, které umožňuje integrovat monitorování různých aplikací a služeb Azure napříč několika předplatnými Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Nainstalujte a nakonfigurujte protokoly Azure Monitor.
> * K monitorování fondů a databází použijte protokoly Azure Monitor.

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasadí se aplikace Wingtip Tickets SaaS Database-na tenanta. Pokud ho chcete nasadit za méně než pět minut, přečtěte si téma [nasazení a prozkoumání aplikace Wingtip Tickets SaaS Database-per-tenant](./saas-dbpertenant-get-started-deploy.md).
* Je nainstalované prostředí Azure PowerShell. Další informace najdete v tématu [Začínáme s Azure PowerShellem](/powershell/azure/get-started-azureps).

Diskuzi o scénářích a vzorcích SaaS a o tom, jak ovlivňují požadavky na řešení monitorování, najdete v [kurzu monitorování a Správa výkonu](saas-dbpertenant-performance-monitoring.md) .

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Monitorování a Správa výkonu databáze a elastického fondu pomocí protokolů Azure Monitor

Pro Azure SQL Database jsou pro databáze a fondy v Azure Portal k dispozici monitorování a upozorňování. Toto integrované monitorování a upozorňování je pohodlné, ale je také specifické pro prostředky. To znamená, že je méně vhodné pro monitorování velkých instalací nebo pro zajištění sjednoceného zobrazení napříč prostředky a předplatnými.

U scénářů s vysokým objemem můžete použít protokoly Azure Monitor pro monitorování a upozorňování. Azure Monitor je samostatná služba Azure, která umožňuje analýzám protokolů shromážděných v pracovním prostoru z potenciálně mnoha služeb. Protokoly Azure Monitor poskytují integrovaný dotazovací jazyk a nástroje pro vizualizaci dat, které umožňují analýzu provozních dat. Řešení SQL Analytics nabízí několik předdefinovaných zobrazení a dotazů pro elastický fond a monitorování databáze a jejich upozorňování. Protokoly Azure Monitor také poskytují vlastní Návrhář zobrazení.

Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics. Log Analytics pracovní prostory a analytická řešení se otevřou v Azure Portal. Azure Portal je novější přístupový bod, ale může to být to, co se nachází na portálu Operations Management Suite v některých oblastech.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Vytváření diagnostických dat výkonu díky simulaci úloh ve vašich klientech 

1. V prostředí PowerShell ISE otevřete *.. \\ WingtipTicketsSaaS-MultiTenantDb – hlavní \\ výukové moduly pro \\ sledování a správu výkonu \\Demo-PerformanceMonitoringAndManagement.ps1*. Nechejte tento skript otevřený, protože během tohoto kurzu budete chtít spustit několik scénářů generování zátěže.
1. Pokud jste to ještě neudělali, zřiďte dávku tenantů, aby byl kontext monitorování zajímavější. Tento proces trvá několik minut.

   a. Nastavte **$DemoScenario = 1** a _zřiďte dávku tenantů_.

   b. Chcete-li spustit skript a nasadit další 17 klientů, stiskněte klávesu F5.

1. Nyní spusťte generátor zatížení, který spustí simulované zatížení všech tenantů.

    a. Nastavte **$DemoScenario = 2**, _vygenerujte normální zatížení intenzity (přibližně 30 DTU)_.

    b. Pokud chcete skript spustit, stiskněte klávesu F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získání skriptů aplikace Wingtip Tickets SaaS Database – pro klienty

V úložišti GitHubu [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) jsou k dispozici skripty SaaS víceklientské databáze a zdrojový kód aplikace Wingtip Tickets. Postup pro stažení a odblokování skriptů prostředí PowerShell pro společnost Wingtip lístky najdete v části [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Instalace a konfigurace Log Analyticsho pracovního prostoru a řešení Azure SQL Analytics

Azure Monitor je samostatná služba, kterou je třeba nakonfigurovat. Protokoly Azure Monitor shromažďují data protokolů, telemetrie a metriky v pracovním prostoru Log Analytics. Stejně jako u jiných prostředků v Azure je třeba vytvořit pracovní prostor Log Analytics. Pracovní prostor není nutné vytvořit ve stejné skupině prostředků jako aplikace, které monitoruje. V takovém případě to často dává co nejužitečnější. V případě aplikace Wingtip Tickets použijte jednu skupinu prostředků, abyste se ujistili, že se pracovní prostor odstraní s aplikací.

1. V prostředí PowerShell ISE otevřete *.. \\ WingtipTicketsSaaS-MultiTenantDb – hlavní \\ výukové moduly pro \\ sledování a správu výkonu \\ Log Analytics \\Demo-LogAnalytics.ps1*.
1. Pokud chcete skript spustit, stiskněte klávesu F5.

Nyní můžete otevřít protokoly Azure Monitor v Azure Portal. Shromažďování telemetrie v pracovním prostoru Log Analytics a jejich viditelnost trvá několik minut. Čím déle necháte systém shromažďování diagnostických dat, tím zajímavější je prostředí. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Monitorování fondů a databází pomocí Log Analyticsho pracovního prostoru a řešení SQL Analytics


V tomto cvičení otevřete Log Analytics pracovní prostor v Azure Portal a podívejte se na telemetrii získanou pro databáze a fondy.

1. Přejděte na [Azure Portal](https://portal.azure.com). Výběrem možnost **všechny služby** otevřete Log Analytics pracovní prostor. Pak vyhledejte Log Analytics.

   ![Otevřít Log Analytics pracovní prostor](./media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Vyberte pracovní prostor s názvem _wtploganalytics &lt; - &gt; User_.

1. Výběrem **přehledu** otevřete řešení log analytics v Azure Portal.

   ![Přehled](./media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Než bude řešení aktivní, může trvat několik minut. 

1. Vyberte dlaždici **Azure SQL Analytics** a otevřete ji.

    ![Dlaždice přehledu](./media/saas-dbpertenant-log-analytics/overview.png)

1. Zobrazení v řešení se posunou dolů s vlastním vnitřním posuvníkem v dolní části. V případě potřeby aktualizujte stránku.

1. Chcete-li prozkoumat stránku Souhrn, vyberte dlaždice nebo jednotlivé databáze a otevřete tak Průzkumníka podrobností.

    ![Řídicí panel Log Analytics](./media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Změnou nastavení filtru upravte časový rozsah. Pro tento kurz vyberte **poslední 1 hodina**.

    ![Filtr času](./media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Vyberte jednotlivou databázi a prozkoumejte využití dotazu a metriky pro tuto databázi.

    ![Analýza databáze](./media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Pokud chcete zobrazit metriky využití, posuňte stránku Analytics doprava.
 
     ![Metriky databáze](./media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Posuňte stránku Analytics na levou stranu a vyberte dlaždici Server v seznamu **informace o zdroji** .  

    ![Seznam informací o prostředku](./media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Otevře se stránka, která zobrazuje fondy a databáze na serveru.

    ![Server s fondy a databázemi](./media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Vyberte fond. Na stránce fond, který se otevře, přejděte k pravému panelu, abyste viděli metriky fondu. 

    ![Metriky fondu](./media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Zpátky v pracovním prostoru Log Analytics vyberte **portál OMS** . tím otevřete pracovní prostor.

    ![Pracovní prostor služby Log Analytics](./media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

V pracovním prostoru Log Analytics můžete ještě víc prozkoumat data protokolu a metriky. 

Monitorování a upozorňování v protokolu Azure Monitor jsou založené na dotazech na data v pracovním prostoru, na rozdíl od upozornění definovaných u jednotlivých prostředků v Azure Portal. Když zadáte výstrahy na dotazy, můžete definovat jednu výstrahu, která se bude vycházet ze všech databází, a ne definovat jednu pro databázi. Dotazy jsou omezeny pouze daty dostupnými v pracovním prostoru.

Další informace o tom, jak používat protokoly Azure Monitor k dotazování a nastavení výstrah, najdete v tématu [práce s pravidly upozornění v protokolech Azure monitor](../../azure-monitor/alerts/alerts-metric.md).

Protokoly Azure Monitor pro SQL Database poplatky na základě objemu dat v pracovním prostoru. V tomto kurzu jste vytvořili bezplatný pracovní prostor, který je omezený na 500 MB za den. Po dosažení tohoto limitu již nebudou data do pracovního prostoru přidána.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nainstalujte a nakonfigurujte protokoly Azure Monitor.
> * K monitorování fondů a databází použijte protokoly Azure Monitor.

Vyzkoušejte [kurz analýzy tenanta](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Další kurzy, které se sestavují na počátečních Lístkech Wingtip Tickets SaaS Database – nasazení pro klientské aplikace](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Protokoly Azure Monitoru](../../azure-monitor/insights/azure-sql.md)