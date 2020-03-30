---
title: Protokoly Azure Monitoru s víceklientnou aplikací
description: Nastavení a používání protokolů Azure Monitoru s víceklientovou aplikací Azure SQL Database SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 7429a9d5e9a803f0e9a6f900c5d81e77e7477a48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214492"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Nastavení a používání protokolů Azure Monitoru s víceklientovou aplikací SQL Database SaaS

V tomto kurzu nastavíte a použijete [protokoly Azure Monitor](/azure/log-analytics/log-analytics-overview) u monitorování elastických fondů a databází. Tento kurz vychází z [kurzu sledování a správy výkonu](saas-dbpertenant-performance-monitoring.md). Ukazuje, jak používat protokoly Azure Monitor rozšířit monitorování a upozorňování poskytované na webu Azure Portal. Protokoly Azure Monitor podporuje monitorování tisíce elastických fondů a stovky tisíc databází. Protokoly Azure Monitor poskytuje jediné řešení monitorování, které můžete integrovat monitorování různých aplikací a služeb Azure napříč více předplatnými Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Nainstalujte a nakonfigurujte protokoly Azure Monitoru.
> * Protokoly Azure Monitor umonitorujte fondy a databáze.

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Wingtip Tickets SaaS databáze na tenanta aplikace se nasadí. Informace o nasazení za méně než pět minut najdete v [tématu Nasazení a prozkoumání aplikace Wingtip Tickets SaaS pro každý klient .](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Další informace najdete v tématu [Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Podívejte se na [kurz sledování a správy výkonu](saas-dbpertenant-performance-monitoring.md) pro diskusi o scénářích scénáře a vzory a jak ovlivňují požadavky na řešení monitorování.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Sledování a správa výkonu databáze a elastického fondu pomocí protokolů Azure Monitoru

Pro Azure SQL Database je monitorování a upozorňování dostupné v databázích a fondech na webu Azure Portal. Toto integrované monitorování a výstrahy je pohodlné, ale je také specifické pro prostředky. To znamená, že je méně vhodný pro monitorování velkých instalací nebo poskytuje jednotné zobrazení napříč prostředky a předplatnými.

Pro scénáře s velkým objemem můžete použít protokoly Azure Monitor pro monitorování a výstrahy. Azure Monitor je samostatná služba Azure, která umožňuje analýzu přes protokoly shromážděné v pracovním prostoru z potenciálně mnoha služeb. Protokoly Azure Monitor poskytuje integrovaný dotazovací jazyk a nástroje pro vizualizaci dat, které umožňují analýzu provozních dat. Řešení SQL Analytics poskytuje několik předdefinovaných elastického fondu a monitorování databáze a upozorňování zobrazení a dotazů. Protokoly Azure Monitor také poskytuje vlastní návrháře zobrazení.

Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics. Pracovní prostory a analytická řešení Log Analytics se otevírají na portálu Azure. Portál Azure je novější přístupový bod, ale v některých oblastech může být to, co je za portálem Operations Management Suite.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Vytvoření diagnostických dat výkonu simulací úlohy vašich klientů 

1. V prostředí PowerShell ISE otevřete *.. WingtipTicketsSaaS-MultiTenantDb-master\\Learning\\Modules Sledování\\výkonu a správa Demo-PerformanceMonitoringAndManagement.ps1 . \\* Ponechte tento skript otevřený, protože můžete chtít spustit několik scénářů generování zatížení během tohoto kurzu.
1. Pokud jste tak již neučinili, zřídit dávku klientů, aby kontext monitorování zajímavější. Tento proces trvá několik minut.

   a. Nastavit **$DemoScenario = 1**, _Zřídit dávku klientů_.

   b. Chcete-li spustit skript a nasadit dalších 17 klientů, stiskněte klávesu F5.

1. Nyní spusťte generátor zatížení spustit simulované zatížení na všechny klienty.

    a. Nastavte **$DemoScenario = 2**, Generovat normální _intenzitu zatížení (přibližně 30 DTU)_.

    b. Skript spustíte stisknutím klávesy F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získejte skripty aplikace SaaS pro jednotlivé klienty wingtip tickets

Wingtip Tickets SaaS víceklientské databázové skripty a zdrojový kód aplikace jsou k dispozici v [wingtipticketssaas-dbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub úložiště. Postup stažení a odblokování skriptů prostředí PowerShell wingtip tickets naleznete v [obecných pokynech](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Instalace a konfigurace pracovního prostoru Log Analytics a řešení Azure SQL Analytics

Azure Monitor je samostatná služba, která musí být nakonfigurovaná. Protokoly Azure Monitor shromažďuje data protokolu, telemetrie a metriky v pracovním prostoru Analýzy protokolů. Stejně jako ostatní prostředky v Azure, musí být vytvořen pracovní prostor Analýzy protokolů. Pracovní prostor nemusí být vytvořen ve stejné skupině prostředků jako aplikace, které monitoruje. Dělat tak často dává největší smysl ačkoli. Pro aplikaci Wingtip Tickets použijte jednu skupinu prostředků, abyste se ujistili, že je pracovní prostor odstraněn s aplikací.

1. V prostředí PowerShell ISE otevřete *.. WingtipTicketsSaaS-MultiTenantDb-master\\Learning\\Modules Sledování\\výkonu\\a analýza logů správy Demo-LogAnalytics.ps1 . \\*
1. Skript spustíte stisknutím klávesy F5.

Teď můžete otevřít protokoly Azure Monitor u na webu Azure Portal. Trvá několik minut shromažďovat telemetrie v pracovním prostoru Log Analytics a zpřístupnit ji viditelné. Čím déle systém opouštíte a shromažďujete diagnostická data, tím zajímavější je zážitek. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Použití pracovního prostoru Log Analytics a řešení SQL Analytics ke sledování fondů a databází


V tomto cvičení otevřete pracovní prostor Log Analytics na webu Azure Portal a podívejte se na telemetrii shromážděnou pro databáze a fondy.

1. Přejděte na [portál Azure](https://portal.azure.com). Vyberte **Všechny služby,** chcete-li otevřít pracovní prostor Log Analytics. Potom vyhledejte Analýzu protokolů.

   ![Otevřít pracovní prostor Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Vyberte pracovní prostor s názvem _&lt;wtploganalytics- user&gt;_.

1. Výběrem **možnosti Přehled** otevřete řešení analýzy protokolů na webu Azure Portal.

   ![Přehled](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Může trvat několik minut, než bude řešení aktivní. 

1. Kliknutím na dlaždici **Azure SQL Analytics** ji otevřete.

    ![Dlaždice Přehled](media/saas-dbpertenant-log-analytics/overview.png)

1. Zobrazení v řešení posouvat do strany, s jejich vlastní vnitřní posuvník v dolní části. V případě potřeby stránku aktualizujte.

1. Chcete-li prozkoumat stránku souhrnu, vyberte dlaždice nebo jednotlivé databáze a otevřete průzkumníka přechodu k podrobnostem.

    ![Řídicí panel analýzy protokolů](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Změňte nastavení filtru a upravte časový rozsah. V tomto kurzu vyberte **Poslední 1 hodina**.

    ![Časový filtr](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Vyberte jednotlivé databáze prozkoumat využití dotazu a metriky pro tuto databázi.

    ![Analýza databáze](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Chcete-li zobrazit metriky využití, posuňte stránku analýzy doprava.
 
     ![Metriky databáze](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Posuňte stránku analýzy doleva a vyberte dlaždici serveru v seznamu **Informace o zdroji.**  

    ![Seznam Informací o zdrojích](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Otevře se stránka, která zobrazuje fondy a databáze na serveru.

    ![Server s fondy a databázemi](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Vyberte fond. Na stránce fondu, která se otevře, přejděte doprava a zobceme metriky fondu. 

    ![Metriky fondu](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. V pracovním prostoru Log Analytics vyberte **portál OMS portal** a otevřete tamní pracovní prostor.

    ![Pracovní prostor služby Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

V pracovním prostoru Log Analytics můžete dále prozkoumat data protokolu a metriky. 

Monitorování a upozorňování v protokolech Azure Monitor uzakládány na dotazech na data v pracovním prostoru, na rozdíl od výstrah definovaných na jednotlivých prostředkůch na webu Azure Portal. Tím, že zakládá výstrahy na dotazy, můžete definovat jednu výstrahu, která se dívá přes všechny databáze, spíše než definovat jeden na databázi. Dotazy jsou omezeny pouze daty, které jsou k dispozici v pracovním prostoru.

Další informace o tom, jak používat protokoly Azure Monitor udotazovat a nastavit výstrahy, najdete [v tématu Práce s pravidly výstrah v protokolech Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Azure Monitor protokoluje poplatky za databázi SQL na základě svazku dat v pracovním prostoru. V tomto kurzu jste vytvořili volný pracovní prostor, který je omezen na 500 MB za den. Po dosažení tohoto limitu se data již nepřidávají do pracovního prostoru.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nainstalujte a nakonfigurujte protokoly Azure Monitoru.
> * Protokoly Azure Monitor umonitorujte fondy a databáze.

Vyzkoušejte [kurz analýzy klienta](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Další zdroje

* [Další kurzy, které vycházejí z počátečního nasazení aplikace Wingtip Tickets SaaS databáze na tenanta](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Protokoly azure monitoru](../azure-monitor/insights/azure-sql.md)
