---
title: Pomocí analýzy protokolů a víceklientské aplikaci SQL Database | Microsoft Docs
description: Nastavení a použití analýzy protokolů víceklientské aplikace SaaS databáze SQL Azure
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 285b8d0acc8a6cbe1a6441a4aabf372de204309e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="set-up-and-use-log-analytics-with-a-multitenant-sql-database-saas-app"></a>Nastavení a použití analýzy protokolů víceklientské aplikace SaaS databáze SQL

V tomto kurzu nastavit a používat Azure [analýzy protokolů](/azure/log-analytics/log-analytics-overview) monitorování elastické fondy a databází. V tomto kurzu vychází [výkonu monitorování a správu kurzu](saas-dbpertenant-performance-monitoring.md). Ukazuje, jak používat analýzy protokolů k posílení monitorování a výstrahy poskytuje na portálu Azure. Analýzy protokolů podporuje monitorování tisíce elastické fondy a stovky tisíc databáze. Log Analytics poskytuje jeden řešení monitorování, které můžete integrovat monitorování jiným aplikacím a službám Azure napříč několika předplatných Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Instalace a konfigurace analýzy protokolů.
> * Pomocí analýzy protokolů sledování fondů a databází.

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasazení aplikace SaaS lístky Wingtip databáze za klienta. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s databáze za klienta aplikace SaaS lístky Wingtip](saas-dbpertenant-get-started-deploy.md).
* Je nainstalované prostředí Azure PowerShell. Další informace najdete v tématu [Začínáme s Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Najdete v článku [výkonu monitorování a správu kurzu](saas-dbpertenant-performance-monitoring.md) diskuzi o SaaS scénáře a vzory a jejich vliv na požadavky na řešení monitorování.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics"></a>Sledování a správě výkon databáze a elastického fondu s analýzy protokolů

Pro databázi SQL Azure monitorování a výstrah je k dispozici na databáze a fondy na portálu Azure. Toto integrované monitorování a výstrah je vhodné, ale je také konkrétní prostředky. To znamená, že je vhodná méně i k monitorování velké instalace nebo poskytnout jednotný pohled mezi prostředky a odběry.

U scénářů s vysokým zatížením můžete analýzy protokolů pro monitorování a výstrahy. Analýzy protokolů je samostatný služba Azure, která umožňuje analytics přes diagnostické protokoly a telemetrii, která se nashromáždí v pracovním prostoru z potenciálně mnoho služeb. Analýzy protokolů poskytuje integrovaného dotazu jazyka a data vizualizace nástroje, které umožňují provozních dat analytics. Řešení SQL Analytics poskytuje několik předdefinovaných elastického fondu a databáze monitorování a výstrah zobrazení a dotazy. Analýzy protokolů taky poskytuje Návrhář vlastní zobrazení.

Log Analytics pracovních prostorů a analýzy řešení otevřete na portálu Azure a v Operations Management Suite. Portál Azure je novější přístupový bod, ale může to být za portál Operations Management Suite v určité oblasti.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Vytvoření diagnostických údajů o výkonu tak, že simuluje pracovní zatížení klientům 

1. V prostředí PowerShell ISE, otevřete *... \\WingtipTicketsSaaS-MultiTenantDb-master\\učení moduly\\sledování výkonu a správy\\ukázku PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože můžete chtít spustit několik zatížení generování scénáře během tohoto kurzu.
2. Pokud jste to ještě neudělali, zřídit dávky klientů, aby zajímavějšího kontext monitorování. Tento proces trvá několik minut.

   a. Nastavit **$DemoScenario = 1**, _zřídit dávky klienty_.

   b. Spusťte skript a nasadit další 17, stisknutím klávesy F5.

3. Nyní spusťte generátor zatížení spustit Simulovaná zatížení na všechny klienty.

    a. Nastavit **$DemoScenario = 2**, _generování normální intenzitou zatížení (asi 30 DTU)_.

    b. Pokud chcete spustit skript, stisknutím klávesy F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získat skripty databáze za klienta aplikace SaaS Wingtip lístky

Adresář Wingtip lístky SaaS víceklientské databázové skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Pokyny ke stažení a odblokování skripty prostředí PowerShell Wingtip lístků, najdete v části [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalace a konfigurace analýzy protokolů a řešení Azure SQL Analytics

Analýzy protokolů je samostatná služba, která musí být nakonfigurovaná. Analýzy protokolů shromažďuje data protokolu, telemetrie a metriky v pracovním prostoru analýzy protokolů. Stejně jako ostatní prostředky v Azure je třeba vytvořit pracovní prostor analýzy protokolů. Pracovní prostor nemusí být vytvořen ve stejné skupině prostředků jako aplikace, které sleduje. Provádění tak často, když je nejvhodnější. Pro aplikace Wingtip lístky použijte jedna skupina prostředků a ujistěte se, že je odstranit pracovní prostor s aplikací.

1. V prostředí PowerShell ISE, otevřete *... \\WingtipTicketsSaaS-MultiTenantDb-master\\učení moduly\\sledování výkonu a správy\\protokolu analýzy\\** ukázku LogAnalytics.ps1***.
2. Pokud chcete spustit skript, stisknutím klávesy F5.

Nyní můžete otevřít analýzy protokolů v portálu Azure nebo na portálu služby Operations Management Suite. Trvá několik minut shromažďování telemetrických dat v pracovním prostoru analýzy protokolů a zpřístupněte ho. Čím delší je ponechat systému shromažďování diagnostických dat, je více zajímavé zkušenosti. 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Monitorování fondů a databází pomocí Log Analytics a řešení SQL Analytics


V tomto cvičení otevřete analýzy protokolů a portál Operations Management Suite se podívat na telemetrická data, shromáždit pro databáze a fondy.

1. Vyhledejte [portál Azure](https://portal.azure.com). Vyberte **všechny služby** otevřete analýzy protokolů. Poté vyhledejte analýzy protokolů.

   ![Open Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

2. Vyberte pracovní prostor s názvem _wtploganalytics -&lt;uživatele&gt;_.

3. Vyberte **Přehled** k otevření řešení Log Analytics na portálu Azure Portal.

   ![Přehled](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Může trvat několik minut, než je aktivní řešení. 

4. Vyberte **Azure SQL Analytics** dlaždice ho otevřete.

    ![Dlaždice přehledu](media/saas-dbpertenant-log-analytics/overview.png)

5. Zobrazení v řešení přejděte do stran, s vlastní vnitřní posuvníku v dolní části. V případě potřeby aktualizujte stránku.

6. Na stránce Souhrn prozkoumat, vyberte dlaždice nebo jednotlivé databáze procházení Průzkumníka můžete otevřít.

    ![Řídicí panel analýzy protokolů](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

7. Změňte nastavení filtru změnit časový rozsah. V tomto kurzu vyberte **poslední 1 hodinu**.

    ![Filtr času](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

8. Vyberte jednu databázi prozkoumat využití dotazu a metriky pro tuto databázi.

    ![databáze analýzy](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

9. Metriky využití najdete posuňte stránce analytics vpravo.
 
     ![metriky databáze](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

10. Posuňte zobrazení stránky analytics vlevo a vyberte dlaždici serveru v **informace** seznamu.  

    ![Seznam zdrojů informací](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Na stránce otevře zobrazující fondy a databází na serveru.

    ![Server s fondy a databáze](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

11. Vyberte fond. Na stránce fondu, které se otevře se posuňte doprava zobrazíte metriky fondu. 

    ![metriky fondu](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


12. Zpět v pracovním prostoru analýzy protokolů vyberte **portálu OMS** otevřete pracovní prostor existuje.

    ![Dlaždice Operations Management Suite portálu](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Na portálu služby Operations Management Suite můžete data protokolu a metriku, v pracovním prostoru dále prozkoumat. 

Monitorování a generování výstrah v analýzy protokolů jsou založené na dotazech přes data v pracovním prostoru, na rozdíl od výstrahy definované na každého prostředku na portálu Azure. Po vytvoření výstrahy na základě dotazů, lze definovat jedna výstraha, která vypadá přes všechny databáze, místo definující jeden do každého databáze. Dotazy jsou omezena pouze data, která je k dispozici v pracovním prostoru.

Další informace o tom, jak používat analýzy protokolů pro dotazování a nastavit upozornění, najdete v tématu [pracovat s pravidla výstrah v analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Analýzy protokolů pro databáze SQL poplatky stanovené podle objemu dat v pracovním prostoru. V tomto kurzu jste vytvořili volného prostoru, který je omezen na 500 MB za den. Po dosažení tohoto limitu data je již přidána do pracovního prostoru.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Instalace a konfigurace analýzy protokolů.
> * Pomocí analýzy protokolů sledování fondů a databází.

Zkuste [kurzu analýza klienta](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Další kurzy, které vychází z počátečního nasazení databáze za klienta aplikace SaaS Wingtip lístky](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
