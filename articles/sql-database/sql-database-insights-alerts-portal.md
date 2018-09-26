---
title: Pomocí webu Azure portal k vytvoření databáze SQL upozornění | Dokumentace Microsoftu
description: Pomocí webu Azure portal k vytvoření databáze SQL výstrah, které můžete aktivovat upozornění nebo automatizace při splnění zadané podmínky.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: 27f807c8f2f6b6a65ef95136047f5eed1b3aab02
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159308"
---
# <a name="use-azure-portal-to-create-alerts-for-azure-sql-database-and-data-warehouse"></a>Vytvoření výstrah pro Azure SQL Database a Data Warehouse pomocí webu Azure portal

## <a name="overview"></a>Přehled
V tomto článku se dozvíte, jak nastavit službu Azure SQL Database a datovým skladem vykazovaných výstrah pomocí webu Azure portal. Tento článek také obsahuje osvědčené postupy pro nastavení výstrah období.    

Obdržíte výstrahu na základě monitorování metrik pro nebo události v rámci služeb Azure.

* **Hodnoty metrik** – výstraha se aktivuje, když hodnota zadaného metrika překročí mezní hodnotu přiřadíte v obou směrech. To znamená, aktivuje, i když je splněna první podmínka a potom později při podmínka, která už probíhá není splněná.    
* **Události protokolu aktivit** – může výstrahu aktivovat *každý* události nebo pouze tehdy, když dojde k určitý počet událostí.

Můžete nakonfigurovat výstrahu při aktivaci provést následující:

* odeslání e-mailová oznámení správce služeb a spolupracujících správců
* odeslání e-mailu další e-maily, které zadáte.
* Volání webhooku

Můžete nakonfigurovat a získat informace o použití pravidel upozornění

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [Rozhraní příkazového řádku (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [Rozhraní REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Vytvoření pravidla upozornění na metriku pomocí webu Azure portal
1. V [portál](https://portal.azure.com/), vyhledejte prostředek zájem o sledování a vyberte ho.
2. Tento krok se liší pro SQL Database a elastické fondy a SQL data Warehouse: 

   - **SQL Database a elastické fondy**: vyberte **výstrahy** nebo **pravidla upozornění** v části monitorování. Text a ikona se mohou mírně lišit pro různé prostředky.  
   
     ![Monitorování](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButton.png)
  
   - **SQL data Warehouse pouze**: vyberte **monitorování** v části společné úkoly. Klikněte na tlačítko **využití DWU** grafu.

     ![BĚŽNÉ ÚLOHY](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButtonDW.png)

3. Vyberte **přidat upozornění** příkazů a přejít k vyplnění polí.
   
    ![Přidat výstrahu](../monitoring-and-diagnostics/media/insights-alerts-portal/AddDBAlertPage.png)
4. **Název** upozornění pravidlo a vyberte **popis**, které zároveň ukazuje oznámení e-mailů.
5. Vyberte **metrika** chcete monitorovat, a potom vyberte **podmínku** a **prahová hodnota** hodnoty pro metriku. Se také rozhodnout **období** , kterou pravidlo metriky musí být splněny před výstrah aktivační události. Třeba Pokud používáte období "PT5M" a upozornění hledá CPU vyšší než 80 %, aktivaci upozornění při **průměrné** procesoru byla vyšší než 80 % po dobu 5 minut. Jakmile dojde k první aktivační událost, se znovu aktivuje, když průměrné využití procesoru je nižší než 80 % více než 5 minut. Měření využití procesoru dochází každou 1 minutu. Podporované časových oken naleznete v následující tabulce a že každý typ agregace upozornění používá ne všechny výstrahy použít průměrnou hodnotu.   
6. Zkontrolujte **e-mailu vlastníky...**  Pokud chcete, aby správci a spolusprávci e-mailem odešle, když se aktivuje upozornění.
7. Pokud chcete další e-maily přijímat oznámení, když se aktivuje upozornění, přidejte je **další email(s) správce** pole. Více e-mailů oddělujte středníkem -  *email@contoso.com;email2@contoso.com*
8. Vložit platný identifikátor URI, **Webhooku** pole, pokud chcete, volá se, když se aktivuje upozornění.
9. Vyberte **OK** po dokončení vytvoření výstrahy.   

Během několika minut upozornění je aktivní a aktivuje jak bylo popsáno dříve.

## <a name="managing-your-alerts"></a>Správa upozornění
Po vytvoření výstrahy, můžete ho vybrat a:

* Zobrazte graf s informacemi o mezní hodnota metriky a skutečnými hodnotami z předchozího dne.
* Upravit nebo odstranit.
* **Zakázat** nebo **povolit** ji, pokud chcete dočasně zastavit nebo obnovit příjem oznámení pro toto upozornění.


## <a name="sql-database-alert-values"></a>Upozornění hodnot v databázi SQL

| Typ prostředku | Název metriky | Popisný název | Typ agregace | Minimální výstrah časový interval|
| --- | --- | --- | --- | --- |
| Databáze SQL | cpu_percent | Procento CPU | Průměr | 5 minut |
| Databáze SQL | physical_data_read_percent | Procento datových V/V | Průměr | 5 minut |
| Databáze SQL | log_write_percent | Procento v/v protokolu | Průměr | 5 minut |
| Databáze SQL | dtu_consumption_percent | Procento DTU | Průměr | 5 minut |
| Databáze SQL | úložiště | Celkovou velikost databáze | Maximum | 30 minut |
| Databáze SQL | connection_successful | Úspěšná připojení | Celkem | 10 minut |
| Databáze SQL | connection_failed | Neúspěšná připojení | Celkem | 10 minut |
| Databáze SQL | blocked_by_firewall | Blokovaná bránou Firewall | Celkem | 10 minut |
| Databáze SQL | Zablokování | Zablokování | Celkem | 10 minut |
| Databáze SQL | storage_percent | Procento velikosti databáze | Maximum | 30 minut |
| Databáze SQL | xtp_storage_percent | Percent(Preview) úložiště OLTP v paměti | Průměr | 5 minut |
| Databáze SQL | workers_percent | Procento pracovních procesů | Průměr | 5 minut |
| Databáze SQL | sessions_percent | Procento relací | Průměr | 5 minut |
| Databáze SQL | dtu_limit | Omezení jednotek DTU | Průměr | 5 minut |
| Databáze SQL | dtu_used | DTU použít | Průměr | 5 minut |
||||||
| Elastický fond | cpu_percent | Procento CPU | Průměr | 10 minut |
| Elastický fond | physical_data_read_percent | Procento datových V/V | Průměr | 10 minut |
| Elastický fond | log_write_percent | Procento v/v protokolu | Průměr | 10 minut |
| Elastický fond | dtu_consumption_percent | Procento DTU | Průměr | 10 minut |
| Elastický fond | storage_percent | Procento úložiště | Průměr | 10 minut |
| Elastický fond | workers_percent | Procento pracovních procesů | Průměr | 10 minut |
| Elastický fond | eDTU_limit | omezení eDTU | Průměr | 10 minut |
| Elastický fond | storage_limit | Limit úložiště. | Průměr | 10 minut |
| Elastický fond | eDTU_used | použít eDTU | Průměr | 10 minut |
| Elastický fond | storage_used | Využité úložiště | Průměr | 10 minut |
||||||               
| SQL Data Warehouse | cpu_percent | Procento CPU | Průměr | 10 minut |
| SQL Data Warehouse | physical_data_read_percent | Procento datových V/V | Průměr | 10 minut |
| SQL Data Warehouse | úložiště | Celkovou velikost databáze | Maximum | 10 minut |
| SQL Data Warehouse | connection_successful | Úspěšná připojení | Celkem | 10 minut |
| SQL Data Warehouse | connection_failed | Neúspěšná připojení | Celkem | 10 minut |
| SQL Data Warehouse | blocked_by_firewall | Blokovaná bránou Firewall | Celkem | 10 minut |
| SQL Data Warehouse | service_level_objective | Úrovně služby databáze | Celkem | 10 minut |
| SQL Data Warehouse | dwu_limit | limit jednotky | Maximum | 10 minut |
| SQL Data Warehouse | dwu_consumption_percent | Procento DWU | Průměr | 10 minut |
| SQL Data Warehouse | dwu_used | Použít DWU | Průměr | 10 minut |
||||||


## <a name="next-steps"></a>Další postup
* [Získejte přehled o Azure monitoring](../monitoring-and-diagnostics/monitoring-overview.md) včetně typů informací můžete shromažďovat a sledovat.
* Další informace o [konfiguraci webhooků ve výstrahách](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Získat [přehled diagnostických protokolů](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) a shromažďovat podrobné vysokou frekvencí metriky pro vaši službu.
* Získat [přehled shromažďování metrik](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) k Ujistěte se, že vaše služba není k dispozici a reagují.
