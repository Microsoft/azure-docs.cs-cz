---
title: Ladění výkonu s využitím materializovaných zobrazení
description: Doporučení a důležité informace, které byste měli vědět při použití materializovaných zobrazení ke zlepšení výkonu dotazu.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e942130d9acf803665e52498ef6a4976cc9ade7
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743175"
---
# <a name="performance-tuning-with-materialized-views"></a>Ladění výkonu s využitím materializovaných zobrazení

Materialized zobrazení ve fondu Synapse SQL poskytují metodu nízké údržby pro složité analytické dotazy získat rychlý výkon bez jakékoli změny dotazu. Tento článek popisuje obecné pokyny pro použití zhmotněných zobrazení.

Materializovaná zobrazení v Azure SQL Data Warehouse poskytují metodu nízké údržby pro složité analytické dotazy, abyste získali rychlý výkon bez jakékoli změny dotazu. Tento článek popisuje obecné pokyny pro použití zhmotněných zobrazení.

## <a name="materialized-views-vs-standard-views"></a>Zhmotněná zobrazení vs. standardní pohledy

Fond SQL podporuje standardní a materializovaná zobrazení.  Obě jsou virtuální tabulky vytvořené pomocí výrazů SELECT a prezentované dotazům jako logické tabulky.  Zobrazení zapouzdřují složitost běžného výpočtu dat a přidávají vrstvu abstrakce do změn výpočtu, takže není nutné přepisovat dotazy.  

Standardní zobrazení vypočítá svá data pokaždé, když je použito zobrazení.  Na disku nejsou uložena žádná data. Uživatelé obvykle používají standardní zobrazení jako nástroj, který pomáhá organizovat logické objekty a dotazy v databázi.  Chcete-li použít standardní zobrazení, dotaz musí provést přímý odkaz na něj.

Materializované zobrazení předem vypočítá, uloží a udržuje svá data ve fondu SQL stejně jako tabulka.  Při použití zhmotněné zobrazení není potřeba žádná recomputace.  To je důvod, proč dotazy, které používají všechna nebo podmnožinu dat v materializovaných zobrazeních, mohou získat vyšší výkon.  Ještě lepší je, že dotazy mohou používat materializované zobrazení bez přímého odkazu, takže není nutné měnit kód aplikace.  

Většina požadavků na standardní pohled se stále vztahuje na zhmotněný pohled. Podrobnosti o syntaxi materializovaného zobrazení a dalších požadavcích naleznete v části [CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

| Srovnání                     | Zobrazit                                         | Materialized View
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Zobrazení definice                 | Uloženo ve fondu SQL.              | Uloženo ve fondu SQL.
|Zobrazení obsahu                    | Generováno při každém použití zobrazení.   | Předem zpracovány a uloženy ve fondu SQL během vytváření zobrazení. Aktualizováno jako data jsou přidány do podkladových tabulek.
|Aktualizace dat                    | Vždy aktualizováno                               | Vždy aktualizováno
|Rychlost načítání dat zobrazení ze složitých dotazů     | Pomalé                                         | Rychle  
|Extra úložiště                   | Ne                                           | Ano
|Syntaxe                          | VYTVOŘIT POHLED                                  | VYTVOŘIT ZHMOTNĚNÝ POHLED JAKO VÝBĚR

## <a name="benefits-of-using-materialized-views"></a>Výhody použití zhmotněných pohledů

Správně navržené zhmotněné zobrazení může poskytnout následující výhody:

- Zkraťte dobu provádění složitých dotazů pomocí akcí JOIN a agregačních funkcí. Čím složitější dotaz, tím vyšší je potenciál pro úsporu času spuštění. Největší výhoda je získána, když jsou náklady na výpočty dotazu vysoké a výsledná sada dat je malá.  
- Optimalizátor ve fondu SQL můžete automaticky použít nasazená materializovaná zobrazení ke zlepšení plány spuštění dotazu.  Tento proces je transparentní pro uživatele poskytující rychlejší výkon dotazů a nevyžaduje dotazy, aby se přímý odkaz na zhmotněné zobrazení.
- Vyžadovat nízkou údržbu pohledů.  Všechny přírůstkové změny dat ze základních tabulek jsou automaticky přidány do zhmotněných pohledů synchronním způsobem.  Tento návrh umožňuje dotazování materializovaných zobrazení vrátit stejná data jako přímé dotazování na základní tabulky.
- Data v materializovaném zobrazení lze distribuovat odlišně od základních tabulek.  
- Data v materializovaných zobrazeních zůspívá stejně vysokou dostupnost a odolnost jako data v běžných tabulkách.  

Materializovaná zobrazení implementovaná ve fondu SQL také poskytují následující další výhody:

Ve srovnání s jinými poskytovateli datového skladu poskytují materializovaná zobrazení implementovaná v Azure SQL Data Warehouse také následující další výhody:

- Automatická a synchronní aktualizace dat se změnami dat v základních tabulkách. Není vyžadována žádná akce uživatele.
- Široká agregovaná podpora funkcí. Viz [VYTVOŘENÍ MATERIALIZED VIEW AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
- Podpora pro doporučení materializovaného zobrazení specifické pro dotaz.  Viz [EXPLAIN (Transact-SQL).](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="common-scenarios"></a>Obvyklé scénáře  

Materializovaná zobrazení se obvykle používají v následujících scénářích:

**Potřeba zlepšit výkonnost složitých analytických dotazů proti velkým objemům dat**

Komplexní analytické dotazy obvykle používají více funkcí agregace a spojení tabulek, což způsobuje další operace náročné na výpočetní výkon, jako jsou náhodné přehrávání a spojení při provádění dotazů.  To je důvod, proč tyto dotazy trvat déle, než se dokončí, speciálně na velkých tabulkách.  

Uživatelé mohou vytvářet materializovaná zobrazení pro data vrácená z běžných výpočtů dotazů, takže není potřeba recomputation, pokud jsou tato data potřeba dotazy, což umožňuje nižší výpočetní náklady a rychlejší odezvu na dotaz.

**Potřebujete vyšší výkon bez minimálních změn dotazů**

Změny schématu a dotazů ve fondech SQL jsou obvykle omezeny na minimum pro podporu běžných operací ETL a vytváření sestav.  Uživatelé mohou použít materializovaná zobrazení pro optimalizaci výkonu dotazu, pokud náklady vzniklé zobrazením mohou být kompenzovány nárůstem výkonu dotazu.

Ve srovnání s jinými možnostmi ladění, jako je škálování a správa statistik, je to mnohem méně působivá změna výroby pro vytvoření a udržení materializovaného zobrazení a jeho potenciální zvýšení výkonu je také vyšší.

- Vytváření nebo udržování materializovaných zobrazení nemá vliv na dotazy spuštěné v základních tabulkách.
- Optimalizátor dotazů může automaticky použít nasazená materializovaná zobrazení bez přímého odkazu na zobrazení v dotazu. Tato funkce snižuje potřebu změny dotazu v optimalizaci výkonu.

**Potřebujete jinou strategii distribuce dat pro rychlejší výkon dotazů**

SQL pool je distribuovaný masivně paralelní procesor (MPP) systém.   Data v tabulce fondu SQL jsou distribuována mezi 60 uzly pomocí jedné ze tří [distribučních strategií](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (hash, round_robin nebo replikováno).  

Distribuce dat je určena v době vytvoření tabulky a zůstane nezměněna, dokud není tabulka vynechána. Materializované zobrazení je virtuální tabulka na disku podporuje hash a round_robin distribuce dat.  Uživatelé mohou zvolit distribuci dat, která se liší od základních tabulek, ale optimální pro výkon dotazů, které používají zobrazení nejvíce.  

## <a name="design-guidance"></a>Pokyny k návrhu

Zde jsou obecné pokyny pro použití materializovaných zobrazení ke zlepšení výkonu dotazu:

**Návrh pro vaši pracovní zátěž**

Než začnete vytvářet materializovaná zobrazení, je důležité mít hluboké pochopení úlohy z hlediska vzorců dotazů, důležitosti, četnosti a velikosti výsledných dat.  

Uživatelé mohou spustit vysvětlující WITH_RECOMMENDATIONS <SQL_statement> pro zhmotněná zobrazení doporučená optimalizátorem dotazů.  Vzhledem k tomu, že tato doporučení jsou specifické pro dotaz, zhmotněné zobrazení, které využívá jeden dotaz nemusí být optimální pro jiné dotazy ve stejném pracovním vytížení.  

Vyhodnoťte tato doporučení s ohledem na vaše potřeby pracovního vytížení.  Ideální zhmotněné pohledy jsou ty, které mají prospěch z výkonu pracovního vytížení.  

**Uvědomte si kompromis mezi rychlejšími dotazy a náklady**

Pro každé materializované zobrazení jsou náklady na úložiště dat a náklady na údržbu zobrazení.  S tím, jak se data mění v základních tabulkách, se zvětšuje velikost materializovaného zobrazení a mění se také jeho fyzická struktura.  Chcete-li se vyhnout snížení výkonu dotazu, každé zhmotněné zobrazení je udržováno samostatně pomocí modulu fondu SQL.  

Pracovní vytížení údržby se zvýší, když se zvýší počet materializovaných pohledů a změny základní tabulky.   Uživatelé by měli zkontrolovat, zda náklady vzniklé ze všech zhmotněných pohledů mohou být kompenzovány zvýšením výkonu dotazu.  

Tento dotaz můžete spustit pro seznam materializovaných zobrazení v databázi:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Možnosti snížení počtu zhmotněných pohledů:

- Identifikujte běžné sady dat, které často používají složité dotazy ve vaší pracovní zátěži.  Vytvořte materializovaná zobrazení pro uložení těchto datových sad, aby je optimalizátor mohl použít jako stavební bloky při vytváření plánů provádění.  

- Přetáhněte zhmotněné pohledy, které mají nízké využití nebo již nejsou potřeba.  Zakázané zhmotněné zobrazení není zachováno, ale stále mu vznikají náklady na úložiště.  

- Kombinujte materializované pohledy vytvořené ve stejných nebo podobných základních tabulkách, i když se jejich data nepřekrývají.  Česání materializovaných pohledů může mít za následek větší velikost pohledu než součet samostatných pohledů, avšak náklady na údržbu pohledu by se měly snížit.  Například:

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Ne všechny optimalizace výkonu vyžadují změnu dotazu**

Optimalizátor fondu SQL může automaticky použít nasazená materializovaná zobrazení ke zlepšení výkonu dotazů.  Tato podpora je použita transparentně na dotazy, které neodkazují na zobrazení a dotazy, které používají agregace nepodporované při vytváření materializovaných zobrazení.  Není nutná žádná změna dotazu. Můžete zkontrolovat plán odhadovaného spuštění dotazu a potvrdit, zda se používá zhmotněné zobrazení.  

**Sledování zhmotněných pohledů**

Materializované zobrazení je uloženo ve fondu SQL stejně jako tabulka s indexem clusterovaného columnstore (CCI).  Čtení dat z materializovaného zobrazení zahrnuje skenování segmentů indexu CCI a použití všech přírůstkových změn ze základních tabulek. Pokud je počet přírůstkových změn příliš vysoký, může vyřešení dotazu z materializovaného zobrazení trvat déle než přímý dotaz na základní tabulky.  

Chcete-li se vyhnout snížení výkonu dotazu, je vhodné spustit [PDW_SHOWMATERIALIZEDVIEWOVERHEAD DBCC](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ke sledování overhead_ratio zobrazení (total_rows / max(1, base_view_row)).  Uživatelé by měli znovu zhmotnit zobrazení, pokud jeho overhead_ratio je příliš vysoká.

**Ukládání do mezipaměti zhmotněné zobrazení a sada výsledků**

Tyto dvě funkce jsou zavedeny ve fondu SQL přibližně ve stejnou dobu pro optimalizaci výkonu dotazu.  Ukládání do mezipaměti sady výsledků se používá pro získání vysoké souběžnosti a rychlé odezvy z opakovaných dotazů proti statickým datům.  

Chcete-li použít výsledek uložený v mezipaměti, musí se forma dotazu požadujícího dotaz mezipaměti shodovat s dotazem, který vytvořil mezipaměť.  Kromě toho musí být výsledek uložený v mezipaměti aplikován na celý dotaz.  

Materializovaná zobrazení umožňují změny dat v základních tabulkách.  Data v materializovaných pohledech lze použít na část dotazu.  Tato podpora umožňuje stejné zhmotněné zobrazení, které mají být použity různé dotazy, které sdílejí některé výpočty pro rychlejší výkon.

## <a name="example"></a>Příklad

Tento příklad používá dotaz podobný TPCDS, který vyhledá zákazníky, kteří utratí více peněz prostřednictvím katalogu než v obchodech, identifikují upřednostňované zákazníky a jejich zemi původu.   Dotaz zahrnuje výběr TOP 100 záznamů z UNIE tří sub-SELECT příkazy zahrnující SUM() a GROUP BY.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Zkontrolujte plán odhadovaného spuštění dotazu.  K dispozici je 18 shuffles a 17 připojí operace, které trvat déle, než k provedení. Nyní vytvoříme jeden zhmotněný pohled pro každý ze tří příkazů sub-SELECT.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Zkontrolujte plán spuštění původního dotazu znovu.  Nyní se počet spojení změní ze 17 na 5 a už není žádný shuffle.  Klepněte na ikonu Operace filtru v plánu, jeho výstupní seznam zobrazuje data, která se čtou z materializovaných zobrazení namísto základních tabulek.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

S materializovanými zobrazeními se stejný dotaz spustí mnohem rychleji bez jakékoli změny kódu.  

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj naleznete [v tématu Přehled vývoje fondu SQL Synapse](sql-data-warehouse-overview-develop.md).
