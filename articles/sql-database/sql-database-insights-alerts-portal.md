---
title: Nastavení výstrah a oznámení (Azure Portal)
description: Pomocí Azure Portal můžete vytvářet SQL Database výstrahy, které můžou aktivovat oznámení nebo automatizaci při splnění zadaných podmínek.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 11/02/2018
ms.openlocfilehash: ec625f203e9282d070e6c1b3b3d712be7ab789cf
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810382"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Vytváření upozornění pro Azure SQL Database a datový sklad pomocí Azure Portal

## <a name="overview"></a>Přehled
V tomto článku se dozvíte, jak nastavit Azure SQL Database a výstrahy datového skladu pomocí Azure Portal. Když některá metrika (například velikost databáze nebo využití procesoru) dosáhne prahové hodnoty, můžou vám výstrahy poslat e-mail nebo zavolat webový Hook. Tento článek také poskytuje osvědčené postupy pro nastavení dob upozornění.    

> [!IMPORTANT]
> Tato funkce není zatím k dispozici ve spravované instanci. Jako alternativu můžete použít agenta SQL k posílání e-mailových upozornění pro některé metriky na základě [zobrazení dynamické správy](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Můžete obdržet upozornění na základě metrik monitorování pro události nebo služby Azure.

* **Hodnoty metrik** – výstraha se aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte v obou směrech. To znamená, že se aktivuje při prvním splnění podmínky a následně v případě, že se už podmínka nesplní.    
* **Události protokolu aktivit** – výstraha se může aktivovat *každou* událost, nebo jenom v případě, že dojde k určitému počtu událostí.

Můžete nakonfigurovat výstrahu, která při triggeru provede následující akce:

* odesílání e-mailových oznámení správci služeb a spolusprávcům
* Odešlete e-mail na další e-maily, které zadáte.
* volání Webhooku

Můžete nakonfigurovat a získat informace o pravidlech upozornění pomocí

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [rozhraní příkazového řádku (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Vytvoření pravidla výstrahy na metrikě s Azure Portal
1. Na [portálu](https://portal.azure.com/)vyhledejte prostředek, který chcete monitorovat, a vyberte ho.
2. V části monitorování vyberte **výstrahy (Classic)** . Text a ikona se mohou mírně lišit pro různé prostředky.  
   
     ![Monitorování](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **Jenom SQL DW**: klikněte na graf **využití DWU** . Vybrat **zobrazení klasických výstrah**

3. Vyberte tlačítko **Přidat upozornění metriky (Classic)** a vyplňte pole.
   
    ![Přidat výstrahu](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. **Pojmenujte** pravidlo upozornění a vyberte **Popis**, který se zobrazí také v oznámeních e-mailů.
5. Vyberte **metriku** , kterou chcete monitorovat, a pak zvolte **podmínku** a **prahovou** hodnotu pro metriku. Také vyberte časové **období** , po které musí být pravidlo metriky splněné před triggerem výstrahy. Pokud například použijete tečku "PT5M" a vaše výstraha vyhledá procesor nad 80%, výstraha se aktivuje, když je **průměrný** procesor nad 80% po dobu 5 minut. Po prvním triggeru se znovu spustí, když je průměrný procesor pod 80% za 5 minut. Měření procesoru probíhá každé 1 minuty. V následující tabulce najdete podporovaná časová okna a typ agregace, které jednotlivé výstrahy používají – ne všechny výstrahy používají průměrnou hodnotu.   
6. Kontrolovat **vlastníky e-mailů...** Pokud chcete, aby správci a spolusprávci byli při aktivaci výstrahy e-mailem.
7. Pokud chcete, aby vám další e-maily dostaly oznámení v případě, že se výstraha aktivuje, přidejte je do pole **Další e-maily správce** . Oddělte více e-mailů středníkem – *e-mail\@contoso. com; email2\@contoso.com*
8. Vložte do pole **Webhooku** platný identifikátor URI, pokud chcete, aby se vyvolalo, když se výstraha aktivuje.
9. Po dokončení vytváření výstrahy vyberte **OK** .   

Během několika minut je výstraha aktivní a triggery, jak je popsáno výše.

## <a name="managing-your-alerts"></a>Správa výstrah
Jakmile vytvoříte upozornění, můžete ho vybrat a:

* Zobrazení grafu znázorňujícího prahovou hodnotu metriky a skutečné hodnoty z předchozího dne.
* Upravte nebo odstraňte.
* Tuto možnost **zakažte** nebo **Povolte** , pokud chcete dočasně zastavit nebo obnovit přijímání oznámení pro tuto výstrahu.


## <a name="sql-database-alert-values"></a>SQL Database hodnoty výstrah

| Typ prostředku | Název metriky | Popisný název | Typ agregace | Minimální časový interval výstrahy|
| --- | --- | --- | --- | --- |
| Databáze SQL | cpu_percent | Procento CPU | Průměr | 5 minut |
| Databáze SQL | physical_data_read_percent | Procento datových V/V | Průměr | 5 minut |
| Databáze SQL | log_write_percent | Procentní hodnota protokolu v/v | Průměr | 5 minut |
| Databáze SQL | dtu_consumption_percent | Procento DTU | Průměr | 5 minut |
| Databáze SQL | úložiště | Celková velikost databáze | Maximum | 30 minut |
| Databáze SQL | connection_successful | Úspěšná připojení | Celkem | 10 minut |
| Databáze SQL | connection_failed | Neúspěšná připojení | Celkem | 10 minut |
| Databáze SQL | blocked_by_firewall | Blokováno bránou firewall | Celkem | 10 minut |
| Databáze SQL | Ukončení | Zablokování | Celkem | 10 minut |
| Databáze SQL | storage_percent | Procento velikosti databáze | Maximum | 30 minut |
| Databáze SQL | xtp_storage_percent | Procento úložiště v paměti OLTP (Preview) | Průměr | 5 minut |
| Databáze SQL | workers_percent | Procento pracovních procesů | Průměr | 5 minut |
| Databáze SQL | sessions_percent | Procento relací | Průměr | 5 minut |
| Databáze SQL | dtu_limit | Limit DTU | Průměr | 5 minut |
| Databáze SQL | dtu_used | Využité DTU | Průměr | 5 minut |
||||||
| Elastický fond | cpu_percent | Procento CPU | Průměr | 10 minut |
| Elastický fond | physical_data_read_percent | Procento datových V/V | Průměr | 10 minut |
| Elastický fond | log_write_percent | Procentní hodnota protokolu v/v | Průměr | 10 minut |
| Elastický fond | dtu_consumption_percent | Procento DTU | Průměr | 10 minut |
| Elastický fond | storage_percent | Procento úložiště | Průměr | 10 minut |
| Elastický fond | workers_percent | Procento pracovních procesů | Průměr | 10 minut |
| Elastický fond | eDTU_limit | limit eDTU | Průměr | 10 minut |
| Elastický fond | storage_limit | Omezení úložiště | Průměr | 10 minut |
| Elastický fond | eDTU_used | využité eDTU | Průměr | 10 minut |
| Elastický fond | storage_used | Využité úložiště | Průměr | 10 minut |
||||||               
| SQL Data Warehouse | cpu_percent | Procento CPU | Průměr | 10 minut |
| SQL Data Warehouse | physical_data_read_percent | Procento datových V/V | Průměr | 10 minut |
| SQL Data Warehouse | connection_successful | Úspěšná připojení | Celkem | 10 minut |
| SQL Data Warehouse | connection_failed | Neúspěšná připojení | Celkem | 10 minut |
| SQL Data Warehouse | blocked_by_firewall | Blokováno bránou firewall | Celkem | 10 minut |
| SQL Data Warehouse | service_level_objective | Úroveň služby databáze | Celkem | 10 minut |
| SQL Data Warehouse | dwu_limit | DWU limit | Maximum | 10 minut |
| SQL Data Warehouse | dwu_consumption_percent | Procento DWU | Průměr | 10 minut |
| SQL Data Warehouse | dwu_used | DWU použito | Průměr | 10 minut |
||||||


## <a name="next-steps"></a>Další kroky
* [Získejte přehled o monitorování Azure](../monitoring-and-diagnostics/monitoring-overview.md) , včetně typů informací, které můžete shromažďovat a monitorovat.
* Přečtěte si další informace o [konfiguraci webhooků v upozorněních](../azure-monitor/platform/alerts-webhooks.md).
* Získejte [Přehled diagnostických protokolů](../azure-monitor/platform/resource-logs-overview.md) a Shromážděte na vaší službě detailní metriky s vysokou frekvencí.
* Získejte [Přehled o kolekci metrik](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) , abyste měli jistotu, že je vaše služba dostupná a reaguje.
