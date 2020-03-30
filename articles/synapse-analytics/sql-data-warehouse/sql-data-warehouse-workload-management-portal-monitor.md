---
title: Monitorování portálu pro správu úloh
description: Pokyny pro monitorování portálu pro správu úloh v Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: f904619b1cea97849e631310cf303ed07194a01e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349917"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure Synapse Analytics – monitorování portálu pro správu úloh (preview)
Tento článek vysvětluje, jak sledovat využití prostředků [skupiny úloh](sql-data-warehouse-workload-isolation.md#workload-groups) a aktivity dotazů. Podrobnosti o tom, jak nakonfigurovat Průzkumník metrik Azure, najdete v článku [Začínáme s Průzkumníkem metrik Azure.](../../azure-monitor/platform/metrics-getting-started.md)  Podrobnosti o monitorování systémových prostředků najdete v části [Využití prostředků](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) v dokumentaci k monitorování Azure Synapse Analytics.
Existují dvě různé kategorie metrik skupiny úloh k dispozici pro monitorování správy úloh: přidělení prostředků a aktivity dotazu.  Tyto metriky lze rozdělit a filtrovat podle skupiny pracovního vytížení.  Metriky lze rozdělit a filtrovat na základě toho, zda jsou definovány systémem (skupiny úloh třídy prostředků) nebo uživatelem definované (vytvořené uživatelem se syntaxí [CREATE WORKLOAD GROUP).](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)

## <a name="workload-management-metric-definitions"></a>Definice metrik správy úloh

|Název metriky                    |Popis  |Typ agregace |
|-------------------------------|-------------|-----------------|
|Procento prostředků efektivního limitu | *Efektivní cap procento zdrojů* je pevný limit na procento zdrojů přístupných skupinou pracovního vytížení, s přihlédnutím *efektivní minimální procento zdrojů* přidělené pro jiné skupiny pracovního vytížení. Metrika *Procento prostředků efektivního zakončení* je konfigurována pomocí parametru `CAP_PERCENTAGE_RESOURCE` v syntaxi VYTVOŘIT [SKUPINU ÚLOH.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Efektivní hodnota je popsána zde.<br><br>Například pokud je `DataLoads` vytvořena `CAP_PERCENTAGE_RESOURCE` skupina pracovního vytížení s = 100 a další skupina pracovního vytížení `DataLoads` je vytvořena s efektivní min procento prostředků 25 %, efektivní cap procento *prostředku* pro skupinu pracovního vytížení je 75%.<br><br>*Procento prostředku efektivní omezení* určuje horní mez souběžnosti (a tedy potenciální propustnost) skupiny pracovního vytížení může dosáhnout.  Pokud je potřeba další propustnost nad rámec toho, co je `CAP_PERCENTAGE_RESOURCE`aktuálně `MIN_PERCENTAGE_RESOURCE` hlášeno metrikou Efektivní cap *procento zdrojů,* buď zvýšit , snížit ostatní skupiny pracovního vytížení nebo vertikálně navýšit instanci přidat další prostředky.  Snížení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` může zvýšit souběžnost, ale nemusí zvýšit celkovou propustnost.| Min, Avg, Max |
|Efektivní min zdroj procent |*Efektivní minimální procento zdrojů* je minimální procento zdrojů rezervovaných a izolovaných pro skupinu pracovního vytížení s přihlédnutím k minimu úrovně služby.  Metrika Efektivní hodnota procenta prostředku `MIN_PERCENTAGE_RESOURCE` min je konfigurována pomocí parametru v syntaxi [VYTVOŘIT SKUPINU ÚLOH.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Efektivní hodnota je popsána [zde](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values).<br><br>Typ agregace součtu použijte, pokud tato metrika není filtrovaná a nerozdělená, můžete sledovat celkovou izolaci pracovního vytížení nakonfigurovanou v systému.<br><br>*Procento prostředku Efektivní min* určuje dolní mez garantované souběžnosti (a tedy zaručené propustnost) skupiny pracovního vytížení může dosáhnout.  Pokud jsou potřeba další zaručené prostředky nad rámec toho, co je `MIN_PERCENTAGE_RESOURCE` aktuálně hlášeno metrikou Efektivní procento prostředků *min,* zvyšte parametr nakonfigurovaný pro skupinu pracovního vytížení.  Snížení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` může zvýšit souběžnost, ale nemusí zvýšit celkovou propustnost. |Min, Avg, Max|
|Aktivní dotazy skupiny úloh  |Tato metrika hlásí aktivní dotazy v rámci skupiny pracovního vytížení.  Pomocí této metriky nefiltrované a nerozdělené zobrazí všechny aktivní dotazy spuštěné v systému.|Součet         |
|Přidělení skupiny pracovních vytížek podle maximálního procenta prostředků |Tato metrika zobrazuje procentuální přidělení zdrojů vzhledem k *percentrovém uzávěru % zdrojů* na skupinu pracovního vytížení.  Tato metrika poskytuje efektivní využití skupiny pracovního vytížení.<br><br>Zvažte skupinu `DataLoads` úloh s efektivní cap zdroj `REQUEST_MIN_RESOURCE_GRANT_PERCENT` *procento* 75 % a nakonfigurované na 25 %.  *Přidělení skupiny pracovního vytížení podle maximální* hodnoty procenta zdroje filtrované na `DataLoads` 33 % (25 % / 75 %) pokud byl v této skupině úloh spuštěn jeden dotaz.<br><br>Tato metrika slouží k identifikaci využití skupiny pracovních vytížek.  Hodnota blížící se 100 % označuje, že jsou používány všechny prostředky, které jsou k dispozici pro skupinu pracovních vytížení.  Metrika *dotazů skupiny úloh ve frontě* pro stejnou skupinu úloh zobrazující hodnotu větší než nula by navíc naznačovala, že skupina úloh by při přidělení využívala další prostředky.  Naopak pokud tato metrika je trvale nízká a *pracovní vytížení skupiny aktivní dotazy* je nízká skupina úloh není využívána.  Tato situace je obzvláště problematická, pokud je *procento prostředku efektivní houženaté ho uzávěru* větší než nula, protože by to znamenalo [nevyužitou izolaci pracovního vytížení](#underutilized-workload-isolation).|Min, Avg, Max |
|Přidělení skupiny pracovních vytížek podle procent systému | Tato metrika zobrazuje procentuální rozdělení zdrojů vzhledem k celému systému.<br><br>Zvažte skupinu `DataLoads` `REQUEST_MIN_RESOURCE_GRANT_PERCENT` úloh s nakonfigurovanou na 25 %.  *Přidělení skupiny úloh podle hodnoty procenta systému* filtrované na `DataLoads` 25 % (25 % / 100 %) pokud byl v této skupině úloh spuštěn jeden dotaz.|Min, Avg, Max |
|Časové limity dotazů skupiny úloh |Dotazy na skupinu úloh, které mají časový čas.  Časové limity dotazu hlášené touto metrikou jsou pouze po spuštění dotazu (nezahrnuje čekací dobu z důvodu uzamčení nebo čekání prostředků).<br><br>Časový limit dotazu `QUERY_EXECUTION_TIMEOUT_SEC` je konfigurován pomocí parametru v syntaxi [CREATE WORKLOAD GROUP.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Zvýšení hodnoty může snížit počet časových limitů dotazu.<br><br>Zvažte `REQUEST_MIN_RESOURCE_GRANT_PERCENT` zvýšení parametru pro skupinu pracovního vytížení snížit množství časových limitů a přidělit více prostředků na dotaz.  Všimněte `REQUEST_MIN_RESOURCE_GRANT_PERCENT` si, že zvýšení snižuje množství souběžnosti pro skupinu pracovního vytížení. |Součet |
|Dotazy skupiny úloh ve frontě | Dotazy na skupinu úloh, které jsou aktuálně zařazeny do fronty a čekají na spuštění.  Dotazy mohou být fronty, protože čekají na prostředky nebo zámky.<br><br>Dotazy mohou čekat z mnoha důvodů.  Pokud je systém přetížený a poptávka souběžnosti je větší než co je k dispozici, dotazy budou fronty.<br><br>Zvažte přidání dalších prostředků do `CAP_PERCENTAGE_RESOURCE` skupiny pracovního vytížení zvýšením parametru v příkazu [CREATE WORKLOAD GROUP.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Pokud `CAP_PERCENTAGE_RESOURCE` je větší než *metrika efektivní cap procento prostředků,* nakonfigurovaná izolace pracovního vytížení pro jinou skupinu pracovních vytížení má vliv na prostředky přidělené této skupině úloh.  Zvažte `MIN_PERCENTAGE_RESOURCE` snížení jiných skupin pracovních vytížek nebo vertikálně navýšit kapacitu instance přidat další prostředky. |Součet |

## <a name="monitoring-scenarios-and-actions"></a>Sledování scénářů a akcí
Níže je uvedena řada konfigurací grafů, které zvýrazňují využití metriky správy úloh pro řešení potíží spolu s přidruženými akcemi k řešení problému.

### <a name="underutilized-workload-isolation"></a>Nevyužitá izolace pracovního vytížení
Zvažte následující konfiguraci skupiny úloh a `wgPriority` třídění, kde je vytvořena skupina úloh `wcCEOPriority` s názvem a *TheCEO* `membername` je na mapován na něj pomocí třídění úlohy.  Skupina `wgPriority` úloh má 25 % izolace`MIN_PERCENTAGE_RESOURCE` pracovního vytížení nakonfigurované pro něj ( = 25).  Každý dotaz odeslaný *theCEO* je uveden`REQUEST_MIN_RESOURCE_GRANT_PERCENT` 5 % systémových prostředků ( = 5).
```sql
CREATE WORKLOAD GROUP wgPriority 
WITH ( MIN_PERCENTAGE_RESOURCE = 25   
      ,CAP_PERCENTAGE_RESOURCE = 50 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5); 

CREATE WORKLOAD CLASSIFIER wcCEOPriority 
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```
Níže uvedený graf je nakonfigurován takto:<br>
Metrika 1: *Efektivní min zdroj procento* (Avg agregace, `blue line`)<br>
Metrika 2: *Přidělení skupiny pracovního vytížení podle procent systému* (agregace vznětových úloh), `purple line`)<br>
Filtr: [Skupina pracovního vytížení] =`wgPriority`<br>
![nevyužité-wg.png](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) Graf ukazuje, že s 25% izolace pracovního vytížení, pouze 10% se používá v průměru.  V tomto případě `MIN_PERCENTAGE_RESOURCE` hodnota parametru může být snížena na mezi 10 nebo 15 a umožnit jiné úlohy v systému spotřebovávat prostředky.

### <a name="workload-group-bottleneck"></a>Problémové místo skupiny úloh
Zvažte následující konfiguraci skupiny úloh a `wgDataAnalyst` třídění, kde je vytvořena skupina úloh `wcDataAnalyst` s názvem a *DataAnalyst* `membername` je na mapován na něj pomocí třídění úlohy.  Skupina `wgDataAnalyst` pracovního vytížení má 6`MIN_PERCENTAGE_RESOURCE` % izolace pracovního vytížení nakonfigurované pro něj ( = 6) a limit prostředků 9 % (`CAP_PERCENTAGE_RESOURCE` = 9).  Každý dotaz odeslaný *dataalytou je* `REQUEST_MIN_RESOURCE_GRANT_PERCENT` uveden 3 % systémových prostředků ( = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6   
      ,CAP_PERCENTAGE_RESOURCE = 9 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3); 

CREATE WORKLOAD CLASSIFIER wcDataAnalyst 
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```
Níže uvedený graf je nakonfigurován takto:<br>
Metrika 1: *Efektivní procento prostředků cap* (agregace vznětových hodnot), `blue line`)<br>
Metrika 2: *Přidělení skupiny pracovního vytížení podle maximálního procenta prostředků* (agregace vznětových), `purple line`)<br>
Metrika 3: *Dotazy skupiny pracovních vytížení ve frontě* (agregace součtů, `turquoise line`)<br>
Filtr: [Skupina pracovního vytížení] =`wgDataAnalyst`<br>
![lahvová krkavice](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) Graf ukazuje, že s 9% omezením prostředků je skupina pracovního vytížení využívána 90 % + (z *přidělení skupiny pracovního vytížení podle metriky maximálního procenta prostředků).*  Je stabilní fronty dotazů, jak je znázorněno z *metriky dotazů skupiny úloh ve frontě*.  V tomto případě `CAP_PERCENTAGE_RESOURCE` zvýšení na hodnotu vyšší než 9 % umožní souběžné provádění více dotazů.  Zvýšení `CAP_PERCENTAGE_RESOURCE` předpokládá, že existuje dostatek prostředků k dispozici a není izolované jinými skupinami úloh.  Ověřte, zda se limit zvýšil kontrolou *metriky Efektivní cap procento zdrojů*.  Pokud je požadováno více propustnost, zvažte také zvýšení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` na hodnotu větší než 3.  Zvýšení `REQUEST_MIN_RESOURCE_GRANT_PERCENT` by mohlo umožnit rychlejší dotazování.

## <a name="next-steps"></a>Další kroky
[Rychlý start: Konfigurace izolace pracovního vytížení pomocí T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
[VYTVOŘIT SKUPINU ÚLOH (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
[VYTVOŘIT TŘÍDĚNÍ ÚLOH (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
[Sledování využití prostředků](sql-data-warehouse-concept-resource-utilization-query-activity.md)

