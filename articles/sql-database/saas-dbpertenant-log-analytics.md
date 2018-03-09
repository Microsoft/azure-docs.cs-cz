---
title: "Použití Log Analytics s aplikací s více tenanty využívající službu SQL Database | Dokumentace Microsoftu"
description: "Nastavit a používat analýzy protokolů (OMS) víceklientské aplikace SaaS databáze SQL Azure"
keywords: kurz k sql database
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: billgib; sstein
ms.openlocfilehash: 7747092d5613a40fa0aff09cfbdfb9b786b37954
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Nastavení a použití analýzy protokolů (OMS) víceklientské aplikace SaaS databáze SQL Azure

V tomto kurzu, nastavení a použití *analýzy protokolů ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* pro monitorování elastické fondy a databází. V tomto kurzu vychází [sledování výkonu a správy kurzu](saas-dbpertenant-performance-monitoring.md). Ukazuje, jak používat *analýzy protokolů* k posílení monitorování a generování výstrah v portálu Azure. Analýzy protokolů podporuje monitorování tisíce elastické fondy a stovky tisíc databáze. Log Analytics poskytuje jeden řešení monitorování, které můžete integrovat monitorování jiným aplikacím a službám Azure napříč několika předplatných Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Instalace a konfigurace Log Analytics (OMS)
> * Monitorování fondů a databází pomocí Log Analytics

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasazení aplikace Wingtip lístky SaaS databáze za klienta. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s aplikací Wingtip lístky SaaS databáze za klienta](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Diskusi ke scénářům a vzorcům SaaS a způsob, jakým ovlivňují požadavky na řešení pro monitorování, najdete v [kurzu Monitorování a správa výkonu](saas-dbpertenant-performance-monitoring.md).

## <a name="monitoring-and-managing-database-and-elastic-pool-performance-with-log-analytics-or-operations-management-suite-oms"></a>Monitorování a Správa databáze a elastického fondu výkonu k analýze protokolů nebo Operations Management Suite (OMS)

Pro databázi SQL monitorování a výstrah je k dispozici na databáze a fondy na portálu Azure. Toto integrované monitorování a výstrah je vhodné, ale se konkrétní prostředky, je vhodně vhodná pro monitorování velké instalace, nebo pro zajištěna jednotný pohled na prostředky a odběry.

U scénářů s vysokým zatížením analýzy protokolů slouží pro monitorování a výstrahy. Analýzy protokolů je samostatný služba Azure, která umožňuje analytics přes diagnostické protokoly a telemetrii, která se nashromáždí v pracovním prostoru z potenciálně mnoho služeb. Analýzy protokolů poskytuje integrovaného dotazu jazyka a data nástroje vizualizace, povolení provozních dat analytics. Řešení SQL Analytics poskytuje několik předem definovaných elastického fondu a databáze monitorování a výstrah zobrazení a dotazy. OMS poskytuje také vlastní návrhář zobrazení.

Pracovní prostory a analytická řešení Log Analytics se dají otevírat z portálu Azure Portal i v OMS. Azure Portal je novější přístupový bod, ale v některých oblastech může být za portálem OMS.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Vytvoření diagnostických údajů o výkonu tak, že simuluje pracovní zatížení klientům 

1. V **prostředí PowerShell ISE**, otevřete *... \\WingtipTicketsSaaS-MultiTenantDb-master\\učení moduly\\sledování výkonu a správy\\** ukázku PerformanceMonitoringAndManagement.ps1***. Tento skript nechte spuštěný, protože je možné, že během tohoto kurzu budete spouštět několik scénářů generování zatížení.
1. Pokud jste tak ještě neučinili, zřídit dávky klientům poskytnout zajímavějšího monitorování kontextu. Tato akce trvá několik minut:
   1. Nastavit **$DemoScenario = 1**, _zřídit dávky klientů_
   1. Spusťte skript a nasadit další 17, stiskněte klávesu **F5**.  

1. Nyní spusťte generátor zatížení spustit Simulovaná zatížení na všechny klienty.  
    1. Nastavit **$DemoScenario = 2**, _generování normální intenzitou zatížení (asi 30 DTU)_.
    1. Chcete-li spustit skript, stiskněte **F5**.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získat aplikační skripty Wingtip lístky SaaS databáze za klienta

Adresář Wingtip lístky SaaS víceklientské databázové skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty prostředí PowerShell Wingtip lístků.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalace a konfigurace Log Analytics a řešení Azure SQL Analytics

Log Analytics je samostatná služba, která vyžaduje konfiguraci. Analýzy protokolů shromažďuje data protokolu, telemetrie a metriky v pracovním prostoru analýzy protokolů. Pracovní prostor log analytics je prostředek, stejně jako ostatní prostředky v Azure a musí být vytvořen. Když pracovní prostor nemusí být vytvořen ve stejné skupině prostředků jako aplikace monitoruje, provádění tak často díky nejvhodnější. Použití jedna skupina prostředků pro aplikace Wingtip lístky zajišťuje, že je odstranit pracovní prostor s aplikací.

1. V **prostředí PowerShell ISE**, otevřete *... \\WingtipTicketsSaaS-MultiTenantDb-master\\učení moduly\\sledování výkonu a správy\\protokolu analýzy\\** ukázku LogAnalytics.ps1***.
1. Chcete-li spustit skript, stiskněte **F5**.

V tomto okamžiku byste měli mít open Log Analytics v portálu Azure (nebo na portálu OMS). Jak dlouho trvá několik minut, než telemetrie, které se mají shromažďovat v pracovním prostoru analýzy protokolů a pak bude viditelný. Čím delší necháte systému shromažďování diagnostických dat, je více zajímavé zkušenosti. Teď si můžete dát chvilku pauzu – jen se ujistěte, že je generátor pořád spuštěný!

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Monitorování fondů a databází pomocí Log Analytics a řešení SQL Analytics


V tomto cvičení otevřete analýzy protokolů a podívejte se na telemetrie shromážděných pro databáze a fondy na portálu OMS.

1. Vyhledejte [portál Azure](https://portal.azure.com) a otevřít analýzy protokolů kliknutím **všechny služby**, vyhledejte analýzy protokolů:

   ![otevření log analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Vyberte pracovní prostor s názvem _wtploganalytics -&lt;uživatele&gt;_.

1. Vyberte **Přehled** k otevření řešení Log Analytics na portálu Azure Portal.

   ![Přehled – odkaz](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Může trvat několik minut, než je aktivní řešení. Buďte prosím trpěliví.

1. Kliknutím otevřete dlaždici Azure SQL Analytics.

    ![overview](media/saas-dbpertenant-log-analytics/overview.png)

    ![analytics](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Zobrazení v řešení přejděte do stran, s vlastní vnitřní posuvníku v dolní části (aktualizujte stránku v případě potřeby).

1. Prozkoumejte souhrnnou stránku kliknutím na dlaždice nebo na jednotlivé databázi otevřete Průzkumníka procházení.

1. Změňte nastavení upravit časové rozmezí – tento kurz vybrat filtru _poslední 1 hodinu_

    ![Filtr času](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Vyberte jednu databázi prozkoumat využití dotazu a metriky pro tuto databázi.

    ![databáze analýzy](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Zobrazit využití přejděte metriky stránce analytics vpravo.
 
     ![metriky databáze](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Procházení analytics stránky na levé straně a klikněte na dlaždici serveru v seznamu prostředků informací. Otevře se stránka, zobrazující fondy a databází na serveru. 

     ![informace o prostředku](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

 
     ![Server s fondy a databáze](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Na serveru stránky, které se otevře, který ukazuje, fondy a databází na serveru, klikněte na fond.  Na stránce fondu, které se otevře se posuňte doprava zobrazíte metriky fondu.  

     ![metriky fondu](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)



1. Zpět na pracovní prostor analýzy protokolů, vyberte **portálu OMS** otevřete pracovní prostor existuje.

    ![oms](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Na portálu OMS můžete data protokolu a metriku, v pracovním prostoru dále prozkoumat.  

Monitorování a generování výstrah v analýzy protokolů a OMS je založená na dotazech přes data v pracovním prostoru, na rozdíl od výstrahy definované na každého prostředku na portálu Azure. Po vytvoření výstrahy na základě dotazů, lze definovat jedna výstraha, která vypadá přes všechny databáze, místo definující jeden do každého databáze. Dotazy jsou omezené jenom daty dostupnými v pracovním prostoru.

Další informace o používání OMS pro dotazování a nastavit výstrahy najdete [práce s pravidla výstrah v analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Log Analytics pro SQL Database se účtuje podle objemu dat v pracovním prostoru. V tomto kurzu jste vytvořili volného prostoru, který je omezen na 500 MB za den. Po dosažení tohoto limitu data je již přidána do pracovního prostoru.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Instalace a konfigurace Log Analytics (OMS)
> * Monitorování fondů a databází pomocí Log Analytics

[Kurz Analýza tenanta](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>Další zdroje informací:

* [Další kurzy, které sestavení po počátečním nasazení aplikace Wingtip lístky SaaS databáze za klienta](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
