---
title: Monitorování portálu pro správu úloh
description: Doprovodné materiály k monitorování portálu správy úloh ve službě Azure synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/01/2021
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 702cc1fc8b135b2eb4af9106a81946873918c4fa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694389"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring"></a>Analýza Azure synapse Analytics – úlohy Portál pro správu monitorování

Tento článek vysvětluje, jak monitorovat využití prostředků [skupiny úloh](sql-data-warehouse-workload-isolation.md#workload-groups) a aktivity dotazů.
Podrobnosti o tom, jak nakonfigurovat Azure Průzkumník metrik, najdete v článku [Začínáme s azure Průzkumník metrik](../../azure-monitor/essentials/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .  Podrobnosti o tom, jak monitorovat spotřebu systémových prostředků, najdete v části věnované [využití prostředků](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) v dokumentaci monitorování Azure synapse Analytics.
Existují dvě různé kategorie metrik skupin úloh, které jsou k dispozici pro správu úloh monitorování: přidělování prostředků a aktivita dotazů.  Tyto metriky je možné rozdělit a filtrovat podle skupiny úloh.  Metriky je možné rozdělit a filtrovat podle toho, jestli jsou definované systémem (skupiny úloh třídy prostředků), nebo uživatelem definované (vytvořené uživatelem s syntaxí [skupiny úloh Create](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ).

## <a name="workload-management-metric-definitions"></a>Definice metrik správy úloh

|Název metriky                    |Popis  |Typ agregace |
|-------------------------------|-------------|-----------------|
|Procento efektivního Cap prostředku | *Procentuální hodnota efektivního* limitu prostředků je pevně nastavená procentuální hodnota z prostředků, které skupina úloh zpřístupní, přičemž vezme v úvahu *efektivní minimální procento prostředků* přidělených pro jiné skupiny úloh. Metrika *procenta efektivního limitu prostředků* je nakonfigurovaná pomocí `CAP_PERCENTAGE_RESOURCE` parametru v SYNTAXI [vytvořit skupinu úloh](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Platné hodnoty jsou popsány zde.<br><br>Pokud je například `DataLoads` vytvořená skupina úloh s `CAP_PERCENTAGE_RESOURCE` = 100 a je vytvořena jiná skupina úloh s platným minimálním procentem 25%, je *procento prostředku efektivního limitu* pro `DataLoads` skupinu úloh 75%.<br><br>*Procentuální hodnota prostředku efektivního Cap* určuje horní mez souběžnosti (a tudíž potenciální propustnost), kterou může skupina úloh dosáhnout.  Pokud je potřeba další propustnost, než je aktuálně hlášena metrikou *procentuální hodnoty efektivního snížení kapacity prostředku* , zvětšete `CAP_PERCENTAGE_RESOURCE` , snižte `MIN_PERCENTAGE_RESOURCE` počet ostatních skupin úloh nebo nahorizontální navýšení kapacity instance, aby bylo možné přidat další prostředky.  Snížení úrovně `REQUEST_MIN_RESOURCE_GRANT_PERCENT` může zvýšit souběžnost, ale nemusí zvyšovat celkovou propustnost.| Min, AVG, Max |
|Efektivní minimální procento prostředků |*Platný* minimální procento prostředků je minimální procento rezervovaných a izolovaných prostředků pro skupinu úloh, které berou v úvahu minimum úrovně služby.  Metrika platných minimálních procentuálních hodnot prostředků je nakonfigurovaná pomocí `MIN_PERCENTAGE_RESOURCE` parametru v syntaxi [vytvořit skupinu úloh](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Platné hodnoty jsou popsány [zde](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values).<br><br>Pokud chcete monitorovat celkovou izolaci úloh nakonfigurovanou v systému, použijte typ agregace Sum, pokud je tato metrika nefiltrovaná, a nerozdělujte.<br><br>*Hodnota platné minimální procento prostředků* určuje dolní mez garantované souběžnosti (a tak zaručenou propustnost), kterou může skupina úloh dosáhnout.  Pokud jsou požadovány další garantované prostředky, které jsou aktuálně hlášeny v *hodnotě efektivní minimální* metriky prostředku, zvyšte `MIN_PERCENTAGE_RESOURCE` parametr nakonfigurovaný pro skupinu úloh.  Snížení úrovně `REQUEST_MIN_RESOURCE_GRANT_PERCENT` může zvýšit souběžnost, ale nemusí zvyšovat celkovou propustnost. |Min, AVG, Max|
|Aktivní dotazy skupiny úloh  |Tato metrika hlásí aktivní dotazy v rámci skupiny úloh.  Použití této metriky Nefiltrováno a zrušit rozdělení zobrazuje všechny aktivní dotazy spuštěné v systému.|Sum         |
|Přidělení skupiny úloh podle zakončení prostředků v procentech |Tato metrika zobrazuje procento přidělení prostředků vzhledem k *procentuální hodnotě prostředku efektivního Cap* na skupinu úloh.  Tato metrika poskytuje efektivní využití skupiny úloh.<br><br>Vezměte v úvahu skupinu úloh `DataLoads` s neplatnou *procentuální hodnotou prostředku* 75% a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` nakonfigurovanou na 25%.  *Procentuální hodnota alokace skupin úloh podle zakončení prostředků* se vyfiltruje na `DataLoads` 33% (25%/75%). Pokud byl v této skupině úloh spuštěn jeden dotaz.<br><br>Pomocí této metriky Identifikujte využití skupiny úloh.  Hodnota blízko 100% znamená, že se používají všechny prostředky, které jsou k dispozici pro skupinu úloh.  Kromě toho, *Skupina úloh ve frontě dotazuje metriky* pro stejnou skupinu úloh, která zobrazuje hodnotu větší než nula, by znamenala, že skupina úloh bude v případě přidělení využívat další prostředky.  Pokud je tato metrika konzistentně nízká a u *aktivních dotazů skupin úloh* je nedostatek, skupina úloh se nevyužívá.  Tato situace je obzvláště problematická, pokud je *procentuální hodnota efektivního zvýšení kapacity prostředků* větší než nula, protože by to znamenalo [nevyužitou izolaci úloh](#underutilized-workload-isolation).|Min, AVG, Max |
|Přidělení skupiny úloh podle systémových procent | Tato metrika zobrazuje procento přidělení prostředků vzhledem k celému systému.<br><br>Vezměte v úvahu skupinu úloh `DataLoads` s `REQUEST_MIN_RESOURCE_GRANT_PERCENT` nakonfigurovaným 25%.  *Přidělení skupiny úloh podle procentuální hodnoty systémových* hodnot se vyfiltruje na `DataLoads` 25% (25%/100%). Pokud byl v této skupině úloh spuštěn jeden dotaz.|Min, AVG, Max |
|Vypršení časového limitu dotazu skupiny úloh |Dotazy na skupinu úloh, jejichž časový limit vypršel.  Vypršení časových limitů dotazů, které tato metrika oznamuje, je jenom jednou, když je dotaz spuštěný (nezahrnuje čekací dobu z důvodu uzamčení nebo čekání na prostředky).<br><br>Časový limit dotazu se konfiguruje pomocí `QUERY_EXECUTION_TIMEOUT_SEC` parametru v syntaxi [vytvořit skupinu úloh](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Zvýšení hodnoty může snížit počet vypršení časového limitu dotazu.<br><br>Zvažte zvýšení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parametru pro skupinu úloh, abyste snížili množství časových limitů a přidělili více prostředků na dotaz.  Všimněte si, že zvýšení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` snižuje množství souběžnosti pro skupinu úloh. |Sum |
|Dotazování skupin úloh ve frontě | Dotazy na skupinu úloh, které jsou aktuálně ve frontě čekající na zahájení provádění.  Dotazy mohou být zařazeny do fronty, protože čekají na prostředky nebo zámky.<br><br>Dotazy mohly čekat z mnoha důvodů.  Pokud je systém přetížený a poptávka za souběžnosti je větší, než je dostupné, dotazy se zařadí do fronty.<br><br>Zvažte přidání dalších prostředků do skupiny úloh, a to tak, že zvýšíte `CAP_PERCENTAGE_RESOURCE` parametr v příkazu [vytvořit skupinu úloh](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Pokud `CAP_PERCENTAGE_RESOURCE` je větší než metrika *efektivního snížení kapacity prostředku* , má nakonfigurované izolaci úloh pro jinou skupinu úloh vliv na prostředky přidělené této skupině úloh.  Zvažte snížení `MIN_PERCENTAGE_RESOURCE` počtu dalších skupin úloh nebo navýšení kapacity instance a přidejte další prostředky. |Sum |

## <a name="monitoring-scenarios-and-actions"></a>Scénáře a akce monitorování

Níže najdete řadu konfigurací grafů pro zvýraznění využití metriky správy úloh pro řešení potíží spolu s přidruženými akcemi pro vyřešení problému.

### <a name="underutilized-workload-isolation"></a>Nevyužitá izolace úloh

Vezměte v úvahu následující skupinu úloh a konfiguraci klasifikátoru, kde se vytvoří skupina úloh s názvem `wgPriority` a  `membername` na ni se namapuje TheCEO pomocí `wcCEOPriority` třídění úloh.  `wgPriority`Pro skupinu úloh je nakonfigurované 25% izolace úloh ( `MIN_PERCENTAGE_RESOURCE` = 25).  Každý dotaz odeslaný službou *TheCEO* je dán 5% systémových prostředků ( `REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).

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
Metrika 1: *efektivní minimální procento prostředků* (průměrná agregace, `blue line` )<br>
Metrika 2: *přidělení skupiny úloh podle systému v procentech* (průměrná agregace, `purple line` )<br>
Filtr: [skupina úloh] = `wgPriority`<br>
![Snímek obrazovky se zobrazením grafu se dvěma metrikami a filtrem.](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png)
Graf ukazuje, že s 25% izolací úloh se v průměru používá jenom 10%.  V takovém případě `MIN_PERCENTAGE_RESOURCE` může být hodnota parametru snížena na 10 nebo 15 a umožní jiným úlohám v systému využívat prostředky.

### <a name="workload-group-bottleneck"></a>Kritické místo skupiny úloh

Vezměte v úvahu následující skupinu úloh a konfiguraci klasifikátoru, kde je vytvořena skupina úloh s názvem `wgDataAnalyst` a že  `membername` je k ní namapována úloha dataanalýz pomocí `wcDataAnalyst` klasifikátoru úloh.  `wgDataAnalyst`Skupina úloh má nakonfigurované 6% izolací úloh ( `MIN_PERCENTAGE_RESOURCE` = 6) a omezení prostředků 9% ( `CAP_PERCENTAGE_RESOURCE` = 9).  Každý dotaz odeslaný pomocí *Dataanalyzuje* je dán 3% systémových prostředků ( `REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

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
Metrika 1: *procentuální hodnota prostředku Cap* (průměrná agregace, `blue line` )<br>
Metrika 2: *přidělení skupiny úloh podle Cap prostředku procento* (průměrná agregace, `purple line` )<br>
Metrika 3: *skupiny úloh ve frontě – dotazy* (agregace Sum, `turquoise line` )<br>
Filtr: [skupina úloh] = `wgDataAnalyst`<br>
![Snímek obrazovky znázorňuje graf se třemi metrikami a filtrem.](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png)
Tento graf znázorňuje, že s 9% Cap k prostředkům, skupina úloh je 90% + využitá (od *přidělení skupiny úloh podle limitu prostředku*).  Existuje stálá fronta dotazů, jak je znázorněno v *metriku dotazů ve skupině úloh ve frontě*.  V takovém případě zvýšení `CAP_PERCENTAGE_RESOURCE` hodnoty na hodnotu vyšší než 9% umožní souběžné spuštění více dotazů.  Zvýšením `CAP_PERCENTAGE_RESOURCE` předpokládáme, že je k dispozici dostatek prostředků a nejsou izolované jinými skupinami úloh.  Ověřte zvýšení limitu tím, že zkontrolujete *metriku procentuální hodnoty efektivního limitu prostředků*.  Pokud je žádoucí větší propustnost, zvažte také zvýšení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` hodnoty na hodnotu větší než 3.  Zvýšení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` může umožní rychlejší spouštění dotazů.

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: Konfigurace izolace úloh pomocí T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
- [Vytvoření skupiny úloh (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [Vytvoření KLASIFIKÁTORu úloh (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [Monitorování využití prostředků](sql-data-warehouse-concept-resource-utilization-query-activity.md)
