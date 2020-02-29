---
title: Monitorování portálu pro správu úloh
description: Doprovodné materiály k monitorování portálu správy úloh ve službě Azure synapse Analytics.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 69a200d4fda940f072960da9224f84a22db51647
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78193794"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure synapse Analytics – úlohy monitorování Portál pro správu (Preview)
Tento článek vysvětluje, jak monitorovat využití prostředků [skupiny úloh](sql-data-warehouse-workload-isolation.md#workload-groups) a aktivity dotazů. Podrobnosti o tom, jak nakonfigurovat Azure Průzkumník metrik, najdete v článku [Začínáme s azure Průzkumník metrik](../azure-monitor/platform/metrics-getting-started.md) .  Podrobnosti o tom, jak monitorovat spotřebu systémových prostředků, najdete v části věnované [využití prostředků](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) v dokumentaci monitorování Azure synapse Analytics.
Existují dvě různé kategorie metrik skupin úloh, které jsou k dispozici pro správu úloh monitorování: přidělování prostředků a aktivita dotazů.  Tyto metriky je možné rozdělit a filtrovat podle skupiny úloh.  Metriky je možné rozdělit a filtrovat podle toho, jestli jsou definované systémem (skupiny úloh třídy prostředků), nebo uživatelem definované (vytvořené uživatelem s syntaxí [skupiny úloh Create](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) ).

## <a name="workload-management-metric-definitions"></a>Definice metrik správy úloh

|Název metriky                    |Popis  |Typ agregace |
|-------------------------------|-------------|-----------------|
|Procento efektivního Cap prostředku | *Procentuální hodnota efektivního* limitu prostředků je pevně nastavená procentuální hodnota z prostředků, které skupina úloh zpřístupní, přičemž vezme v úvahu *efektivní minimální procento prostředků* přidělených pro jiné skupiny úloh. Metrika *procentuální hodnoty efektivního limitu prostředku* je nakonfigurovaná pomocí parametru `CAP_PERCENTAGE_RESOURCE` v SYNTAXI [vytvořit skupinu úloh](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Platné hodnoty jsou popsány zde.<br><br>Pokud je například skupina úloh `DataLoads` vytvořená pomocí `CAP_PERCENTAGE_RESOURCE` = 100 a vytvoří se jiná skupina úloh s nejefektivnějším minimálním procentem 25%, je *procento prostředku efektivního limitu* pro skupinu úloh `DataLoads` 75%.<br><br>*Procentuální hodnota prostředku efektivního Cap* určuje horní mez souběžnosti (a tudíž potenciální propustnost), kterou může skupina úloh dosáhnout.  Pokud je potřeba další propustnost, než je aktuálně hlášena metrikou *procentuální hodnoty efektivního snížení kapacity prostředku* , zvětšete `CAP_PERCENTAGE_RESOURCE`, snižte `MIN_PERCENTAGE_RESOURCE` jiných skupin úloh nebo navýšení kapacity instance přidejte další prostředky.  Snížení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` může zvýšit souběžnost, ale nemusí zvyšovat celkovou propustnost.| Min, AVG, Max |
|Efektivní minimální procento prostředků |*Platný* minimální procento prostředků je minimální procento rezervovaných a izolovaných prostředků pro skupinu úloh, které berou v úvahu minimum úrovně služby.  Metrika platných minimálních procentuálních hodnot prostředků je nakonfigurovaná pomocí parametru `MIN_PERCENTAGE_RESOURCE` v syntaxi [vytvořit skupinu úloh](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Platné hodnoty jsou popsány [zde](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values).<br><br>Pokud chcete monitorovat celkovou izolaci úloh nakonfigurovanou v systému, použijte typ agregace Sum, pokud je tato metrika nefiltrovaná, a nerozdělujte.<br><br>*Hodnota platné minimální procento prostředků* určuje dolní mez garantované souběžnosti (a tak zaručenou propustnost), kterou může skupina úloh dosáhnout.  Pokud jsou vyžadovány další garantované prostředky, které jsou aktuálně hlášeny v *hodnotě efektivní minimální* metriky prostředku, zvyšte parametr `MIN_PERCENTAGE_RESOURCE` nakonfigurovaný pro skupinu úloh.  Snížení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` může zvýšit souběžnost, ale nemusí zvyšovat celkovou propustnost. |Min, AVG, Max|
|Aktivní dotazy skupiny úloh  |Tato metrika hlásí aktivní dotazy v rámci skupiny úloh.  Použití této metriky Nefiltrováno a zrušit rozdělení zobrazuje všechny aktivní dotazy spuštěné v systému.|Součet         |
|Přidělení skupiny úloh podle maximálního procenta prostředků |Tato metrika zobrazuje procento přidělení prostředků vzhledem k *procentuální hodnotě prostředku efektivního Cap* na skupinu úloh.  Tato metrika poskytuje efektivní využití skupiny úloh.<br><br>Vezměte v úvahu skupinu úloh `DataLoads` s *efektivní procentuální hodnotou prostředku* 75% a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` nakonfigurovaná na 25%.  *Přidělení skupiny úloh podle maximální procentuální hodnoty prostředků* filtrované na `DataLoads` by bylo 33% (25%/75%) Pokud byl v této skupině úloh spuštěn jeden dotaz.<br><br>Pomocí této metriky Identifikujte využití skupiny úloh.  Hodnota blízko 100% znamená, že se používají všechny prostředky, které jsou k dispozici pro skupinu úloh.  Kromě toho, *Skupina úloh ve frontě dotazuje metriky* pro stejnou skupinu úloh, která zobrazuje hodnotu větší než nula, by znamenala, že skupina úloh bude v případě přidělení využívat další prostředky.  Pokud je tato metrika konzistentně nízká a u *aktivních dotazů skupin úloh* je nedostatek, skupina úloh se nevyužívá.  Tato situace je obzvláště problematická, pokud je *procentuální hodnota efektivního zvýšení kapacity prostředků* větší než nula, protože by to znamenalo [nevyužitou izolaci úloh](#underutilized-workload-isolation).|Min, AVG, Max |
|Přidělení skupiny úloh podle systémových procent | Tato metrika zobrazuje procento přidělení prostředků vzhledem k celému systému.<br><br>Vezměte v úvahu skupinu úloh `DataLoads` s `REQUEST_MIN_RESOURCE_GRANT_PERCENT` nakonfigurovanou na 25%.  *Přidělení skupiny úloh podle procentuální hodnoty systému* filtrované na `DataLoads` by bylo 25% (25%/100%) Pokud byl v této skupině úloh spuštěn jeden dotaz.|Min, AVG, Max |
|Vypršení časového limitu dotazu skupiny úloh |Dotazy na skupinu úloh, jejichž časový limit vypršel.  Vypršení časových limitů dotazů, které tato metrika oznamuje, je jenom jednou, když je dotaz spuštěný (nezahrnuje čekací dobu z důvodu uzamčení nebo čekání na prostředky).<br><br>Časový limit dotazu se konfiguruje pomocí parametru `QUERY_EXECUTION_TIMEOUT_SEC` v syntaxi [Vytvoření skupiny úloh](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Zvýšení hodnoty může snížit počet vypršení časového limitu dotazu.<br><br>Zvažte zvýšení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parametru pro skupinu úloh, abyste snížili množství časových limitů a přidělili více prostředků na dotaz.  Všimněte si, že zvýšení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` snižuje množství souběžnosti pro skupinu úloh. |Součet |
|Dotazování skupin úloh ve frontě | Dotazy na skupinu úloh, které jsou aktuálně ve frontě čekající na zahájení provádění.  Dotazy mohou být zařazeny do fronty, protože čekají na prostředky nebo zámky.<br><br>Dotazy mohly čekat z mnoha důvodů.  Pokud je systém přetížený a poptávka za souběžnosti je větší, než je dostupné, dotazy se zařadí do fronty.<br><br>Zvažte přidání dalších prostředků do skupiny úloh, a to zvýšením parametru `CAP_PERCENTAGE_RESOURCE` v příkazu [vytvořit skupinu úloh](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Pokud je `CAP_PERCENTAGE_RESOURCE` větší než *procento efektivního snížení kapacity prostředku* , má nakonfigurované izolaci úloh pro jinou skupinu úloh vliv na prostředky přidělené této skupině úloh.  Zvažte snížení `MIN_PERCENTAGE_RESOURCE` jiných skupin úloh nebo navýšení kapacity instance za účelem přidání dalších prostředků. |Součet |

## <a name="monitoring-scenarios-and-actions"></a>Scénáře a akce monitorování
Níže najdete řadu konfigurací grafů pro zvýraznění využití metriky správy úloh pro řešení potíží spolu s přidruženými akcemi pro vyřešení problému.

### <a name="underutilized-workload-isolation"></a>Nevyužitá izolace úloh
Vezměte v úvahu následující skupinu úloh a konfiguraci klasifikátoru, kde se vytvoří skupina úloh s názvem `wgPriority` a na ni se namapuje *TheCEO* `membername` pomocí `wcCEOPriority` třídění úloh.  Pro skupinu úloh `wgPriority` je nakonfigurované 25% izolace úloh (`MIN_PERCENTAGE_RESOURCE` = 25).  Každý dotaz odeslaný službou *TheCEO* je dán 5% systémových prostředků (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).
```sql
CREATE WORKLOAD GROUP wgPriority 
WITH ( MIN_PERCENTAGE_RESOURCE = 25   
      ,CAP_PERCENTAGE_RESOURCE = 50 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5); 

CREATE WORKLOAD CLASSIFIER wcCEOPriority 
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```
Následující graf je nakonfigurován následujícím způsobem:<br>
Metrika 1: *efektivní minimální procento prostředků* (průměrná agregace, `blue line`)<br>
Metrika 2: *přidělení skupiny úloh podle systému v procentech* (prům. agregace, `purple line`)<br>
Filter: [skupina úloh] = `wgPriority`<br>
![underutilized-WG. png](media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) graf ukazuje, že s 25% izolací úloh se v průměru používá jenom 10%.  V takovém případě by hodnota parametru `MIN_PERCENTAGE_RESOURCE` mohla být snížena na 10 nebo 15 a umožněna jiným úlohám v systému využívat prostředky.

### <a name="workload-group-bottleneck"></a>Kritické místo skupiny úloh
Vezměte v úvahu následující skupinu úloh a konfiguraci klasifikátoru, kde je vytvořena skupina úloh s názvem `wgDataAnalyst` a k ní je namapována `membername` *Dataanalyzujes* pomocí `wcDataAnalyst` třídění úloh.  Skupina úloh `wgDataAnalyst` má nakonfigurované 6% izolací úloh (`MIN_PERCENTAGE_RESOURCE` = 6) a omezení prostředků na 9% (`CAP_PERCENTAGE_RESOURCE` = 9).  Každý dotaz odeslaný *Dataanalýzou* je dán 3% systémových prostředků (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6   
      ,CAP_PERCENTAGE_RESOURCE = 9 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3); 

CREATE WORKLOAD CLASSIFIER wcDataAnalyst 
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```
Následující graf je nakonfigurován následujícím způsobem:<br>
Metrika 1: *procentuální hodnota prostředku Cap* (průměrná agregace, `blue line`)<br>
Metrika 2: *přidělení skupiny úloh podle maximálního počtu prostředků* (průměr agregace, `purple line`)<br>
Metrika 3: *Skupina úloh – dotazy ve frontě* (agregace Sum, `turquoise line`)<br>
Filter: [skupina úloh] = `wgDataAnalyst`<br>
![](media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) WG na lahvích se v grafu zobrazuje, že s 9% Cap k prostředkům je skupina úlohy 90% + využitá (od *přidělení skupiny úloh podle maximálního počtu prostředků v procentech*).  Existuje stálá fronta dotazů, jak je znázorněno v *metriku dotazů ve skupině úloh ve frontě*.  V takovém případě zvýšení `CAP_PERCENTAGE_RESOURCE` na hodnotu vyšší než 9% umožní spuštění více dotazů současně.  Zvýšení `CAP_PERCENTAGE_RESOURCE` předpokládá, že je k dispozici dostatek prostředků a nejsou izolované jinými skupinami úloh.  Ověřte zvýšení limitu tím, že zkontrolujete *metriku procentuální hodnoty efektivního limitu prostředků*.  Pokud je žádoucí větší propustnost, zvažte také zvýšení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` na hodnotu větší než 3.  Zvýšení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` může umožňovat rychlejší spouštění dotazů.

## <a name="next-steps"></a>Další kroky
[Rychlý Start: Konfigurace izolace úloh pomocí T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
[Vytvoření skupiny úloh (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
[Vytvoření KLASIFIKÁTORu úloh (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
[Monitorování využití prostředků](sql-data-warehouse-concept-resource-utilization-query-activity.md)

