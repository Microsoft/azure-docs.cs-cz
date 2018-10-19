---
title: Použití Log Analytics s víceklientských aplikací SQL Database | Dokumentace Microsoftu
description: Nastavit a používat službu Log Analytics s víceklientské aplikace Azure SQL Database SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: b207af3bed40f6287f60b25638f3091fa187aa6f
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405068"
---
# <a name="set-up-and-use-log-analytics-with-a-multitenant-sql-database-saas-app"></a>Nastavit a používat službu Log Analytics s víceklientské aplikace SaaS databáze SQL

V tomto kurzu, můžete nastavit a používat Azure [Log Analytics](/azure/log-analytics/log-analytics-overview) monitorování elastických fondů a databází. Tento kurz vychází [kurzu monitorování a správa výkonu](saas-dbpertenant-performance-monitoring.md). Ukazuje, jak používat službu Log Analytics k posílení monitorování a upozorňování k dispozici na webu Azure Portal. Log Analytics podporuje monitorování tisíce elastických fondů a stovky tisíc databází. Log Analytics poskytuje jedno řešení pro monitorování, které může integrovat monitorování různých aplikací a služeb Azure napříč několika předplatnými Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Instalace a konfigurace Log Analytics.
> * Monitorování fondů a databází pomocí Log Analytics.

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasazení aplikace SaaS aplikace Wingtip Tickets databáze na tenanta. Nasazení za méně než pět minut najdete v tématu [nasazení a zkoumání SaaS aplikace Wingtip Tickets databáze na tenanta aplikace](saas-dbpertenant-get-started-deploy.md).
* Je nainstalované prostředí Azure PowerShell. Další informace najdete v tématu [Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Zobrazit [kurzu monitorování a správa výkonu](saas-dbpertenant-performance-monitoring.md) diskuzi o scénáře SaaS a vzory a způsob, jakým ovlivňují požadavky na řešení pro monitorování.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics"></a>Monitorování a správu databáze a elastického fondu výkonu pomocí Log Analytics

Pro službu Azure SQL Database monitorování a upozorňování k dispozici v databázích a fondech na webu Azure Portal. Toto integrované monitorování a upozorňování je vhodné, ale je také specifické podle prostředků. To znamená, že to je méně vhodné pro monitorování velkých instalací nebo poskytovat jednotné zobrazení napříč prostředky a předplatnými.

Log Analytics můžete použít pro monitorování a upozorňování pro velkoobjemových scénářů. Log Analytics je samostatná služba Azure, která umožňuje analytics přes diagnostické protokoly a telemetrii, která jsou shromážděna v pracovním prostoru z potenciálně mnoha služeb. Log Analytics poskytuje integrovaný dotazovací jazyk a nástroje vizualizace dat umožňující analýzy provozních dat. Řešení SQL Analytics nabízí několik předdefinovaných elastických fondů a databázi sledování a výstrah zobrazení a dotazů. Log Analytics poskytuje také vlastní Návrhář zobrazení.

Pracovní prostory OMS jsou dnes označovány jako pracovní prostory Log Analytics. Na webu Azure Portal otevřete log Analytics pracovní prostory a analytická řešení. Na webu Azure portal je novější přístupový bod, ale může být, co je za portálu Operations Management Suite v některých oblastech.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Vytvoření diagnostické údaje o výkonu pomocí simulace úloh na klienty 

1. Otevřít v PowerShell ISE *... \\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\monitorování a správa výkonu\\Demo-PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože budete chtít spustit několik zatížení scénáře generování během tohoto kurzu.
1. Pokud jste to ještě neudělali, zřiďte dávku tenantů, aby zajímavějšího kontextu monitorování. Tento proces trvá několik minut.

   a. Nastavte **$DemoScenario = 1**, _zřízení dávky tenantů_.

   b. Spusťte skript a nasazení dalších 17 tenantů, stiskněte klávesu F5.

1. Nyní spusťte generátor zatížení ke spuštění simulované zatížení na všech tenantů.

    a. Nastavte **$DemoScenario = 2**, _generování normální intenzity zatížení (asi 30 jednotek DTU)_.

    b. Stisknutím klávesy F5 spusťte skript.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získání skriptů aplikace databáze na tenanta SaaS aplikace Wingtip Tickets

SaaS aplikace Wingtip Tickets víceklientské databázové skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Pokyny ke stažení a odblokování skriptů aplikace Wingtip Tickets Powershellu najdete v článku [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalace a konfigurace Log Analytics a řešení Azure SQL Analytics

Log Analytics je samostatná služba, která musí být nakonfigurované. Log Analytics shromažďuje data protokolu, telemetrie a metrik v pracovním prostoru Log Analytics. Stejně jako ostatní prostředky v Azure musí se vytvořit pracovní prostor Log Analytics. Pracovní prostor není nutné vytvořit ve stejné skupině prostředků jako aplikace, které monitorují. To tak často ale dává největší smysl. Pro aplikace Wingtip Tickets pomocí jedné skupiny prostředků zajistit, aby že pracovní prostor se odstraní společně s aplikací.

1. Otevřít v PowerShell ISE *... \\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\monitorování a správa výkonu\\Log Analytics\\Demo-LogAnalytics.ps1*.
1. Stisknutím klávesy F5 spusťte skript.

Nyní můžete otevřít Log Analytics na portálu Azure portal. Trvá několik minut, než ke shromažďování telemetrických dat v pracovním prostoru Log Analytics a zprůhlední je. Čím déle necháte systém shromažďování diagnostických dat, tím zajímavější výsledné prostředí funguje. 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Monitorování fondů a databází pomocí Log Analytics a řešení SQL Analytics


V tomto cvičení otevřete Log Analytics na webu Azure Portal se podívat na telemetrii získanou pro databáze a fondy.

1. Přejděte na web [Azure Portal](https://portal.azure.com). Vyberte **všechny služby** otevřete Log Analytics. Potom vyhledejte Log Analytics.

   ![Open Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Vyberte pracovní prostor s názvem _wtploganalytics -&lt;uživatele&gt;_.

1. Vyberte **Přehled** k otevření řešení Log Analytics na portálu Azure Portal.

   ![Přehled](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Může trvat několik minut, než začne být řešení aktivní. 

1. Vyberte **Azure SQL Analytics** dlaždici ji otevřete.

    ![Dlaždice přehledu](media/saas-dbpertenant-log-analytics/overview.png)

1. Zobrazení v řešení posune po straně a s jejich vlastní vnitřní posuvník v dolní části. V případě potřeby aktualizujte stránku.

1. Prozkoumat na stránce souhrnu, vyberte dlaždice nebo jednotlivé databáze procházení Průzkumníka můžete otevřít.

    ![Řídicí panel log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Změňte nastavení filtru na Upravit časový rozsah. Pro účely tohoto kurzu vyberte **poslední 1 hodina**.

    ![Filtr času](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Vyberte jednu databázi a prozkoumejte využití dotazu a metriky pro tuto databázi.

    ![Databáze analýzy](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Pokud chcete zobrazit metriky využití, posuňte se na stránce Analýza vpravo.
 
     ![Metriky databáze](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Posuňte se na stránce Analýza nalevo a výběrem dlaždice serveru v **informace o prostředku** seznamu.  

    ![Informace o seznamu prostředků](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Otevře se stránka zobrazující fondů a databází na serveru.

    ![Server s fondy a databáze](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Vyberte fond. Na stránce fondu, které se otevře posuňte doprava a zobrazení metrik fondu. 

    ![Fond metriky](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Zpět v pracovním prostoru Log Analytics vyberte **portál OMS** otevřete pracovní prostor existuje.

    ![Pracovní prostor Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

V pracovním prostoru Log Analytics můžete prozkoumat další data protokolů a metrik. 

Monitorování a upozorňování v Log Analytics jsou založené na dotazech na data v pracovním prostoru, na rozdíl od výstrah definované u jednotlivých prostředků na webu Azure Portal. Pomocí šablony oznámení pro dotazy, můžete definovat jedinou výstrahu, která vypadá za všechny databáze místo definování jeden na databázi. Dotazy jsou omezené pouze podle data, která je k dispozici v pracovním prostoru.

Další informace o tom, jak používat službu Log Analytics k dotazování a nastavování upozornění, najdete v části [pracovat pravidla upozornění v Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Log Analytics pro SQL Database poplatky stanovené podle objemu dat v pracovním prostoru. V tomto kurzu jste vytvořili bezplatný pracovní prostor, což je omezený na 500 MB za den. Po dosažení tohoto limitu se data je již přidána do pracovního prostoru.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Instalace a konfigurace Log Analytics.
> * Monitorování fondů a databází pomocí Log Analytics.

Zkuste [kurz analýza Tenanta](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Další kurzy, které vycházejí z počátečního nasazení databáze na tenanta aplikace SaaS aplikace Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
