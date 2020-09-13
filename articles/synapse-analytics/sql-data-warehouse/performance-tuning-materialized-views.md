---
title: Optimalizace výkonu s využitím materializovaná zobrazení
description: Doporučení a důležité informace, které byste měli znát při použití materializovaná zobrazení ke zlepšení výkonu dotazů.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: ddab106e11340dd040b99d1e8abf9d93e9338ab7
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441200"
---
# <a name="performance-tune-with-materialized-views"></a>Optimalizace výkonu s využitím materializovaná zobrazení

Materializovaná zobrazení v synapse fondu SQL poskytují metodu údržby pro složité analytické dotazy, aby se zajistil rychlý výkon bez jakýchkoli změn dotazů. Tento článek popisuje obecné pokyny k používání materializovaná zobrazení.

## <a name="materialized-views-vs-standard-views"></a>Materializovaná zobrazení vs. standardní zobrazení

Fond SQL podporuje standardní a materializovaná zobrazení.  Obě jsou virtuální tabulky vytvořené pomocí výrazů SELECT a prezentují se dotazům jako logické tabulky.  Zobrazení zapouzdřují složitost běžných výpočtů dat a přidávají do výpočtů změny, takže není potřeba přepisovat dotazy.  

Standardní zobrazení vypočítá data při každém použití zobrazení.  Na disku nejsou uložená žádná data. Lidé obvykle používají standardní zobrazení jako nástroj, který pomáhá organizovat logické objekty a dotazy v databázi.  Chcete-li použít standardní zobrazení, dotaz musí na něj vytvořit přímý odkaz.

Materializované zobrazení předběžně počítá, ukládá a udržuje data ve fondu SQL stejně jako tabulka.  Při použití materializované zobrazení není nutná žádná recompute.  To je důvod, proč dotazy, které používají všechny nebo podmnožiny dat v materializovaná zobrazení, mohou dosáhnout rychlejšího výkonu.  I lepší, dotazy mohou používat materializovaná zobrazení bez přímého odkazu na něj, takže není nutné měnit kód aplikace.  

Většina požadavků na standardní zobrazení se stále vztahuje na materializované zobrazení. Podrobnosti o syntaxi materializované zobrazení a dalších požadavcích najdete v tématu [Vytvoření materializované zobrazení jako SELECT.](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

| Porovnání                     | Zobrazit                                         | Materialized View
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Zobrazení definice                 | Uloženo ve fondu SQL.              | Uloženo ve fondu SQL.
|Zobrazení obsahu                    | Vygenerováno pokaždé, když je použito zobrazení.   | Během vytváření zobrazení se předem zpracoval a uloží ve fondu SQL. Aktualizováno při přidání dat do podkladových tabulek.
|Aktualizace dat                    | Vždy Aktualizováno                               | Vždy Aktualizováno
|Rychlost načtení dat zobrazení ze složitých dotazů     | Pomalá                                         | Rychlý  
|Dodatečné úložiště                   | No                                           | Yes
|Syntax                          | VYTVOŘIT ZOBRAZENÍ                                  | VYTVOŘIT MATERIALIZOVANÁ ZOBRAZENÍ JAKO VYBRAT

## <a name="benefits-of-using-materialized-views"></a>Výhody použití materializovaná zobrazení

Správně navržené materializované zobrazení přináší následující výhody:

- Zkraťte dobu provádění složitých dotazů pomocí spojení a agregačních funkcí. Čím složitější je dotaz, tím vyšší je potenciál při ukládání. Nejvíc výhod se získá, když jsou náklady na výpočet dotazu vysoké a výsledná datová sada je malá.  
- Optimalizátor ve fondu SQL může automaticky použít nasazená vyhodnocená zobrazení ke zlepšení plánů spouštění dotazů.  Tento proces je transparentní pro uživatele, kteří poskytují rychlejší výkon dotazů a nevyžadují dotazy, aby přímo odkazovaly na materializovaná zobrazení.
- Vyžaduje nízkou údržbu zobrazení.  Všechny přírůstkové změny dat ze základních tabulek se synchronním způsobem automaticky přidávají do materializovaná zobrazení.  Tento návrh umožňuje dotazování na materializovaná zobrazení, aby vracela stejná data jako přímo dotaz na základní tabulky.
- Data v materializované zobrazení je možné distribuovat jinak než základní tabulky.  
- Data v materializovaná zobrazení mají stejné výhody vysoké dostupnosti a odolnosti jako data v běžných tabulkách.  

Materializovaná zobrazení implementovaná ve fondu SQL poskytují také tyto další výhody:

V porovnání s jinými poskytovateli datového skladu poskytují materializovaná zobrazení implementovaná ve službě Azure synapse Analytics také tyto další výhody:

- Automatická a synchronní aktualizace dat se změnami dat v základních tabulkách Není vyžadována žádná akce uživatele.
- Podpora široké agregační funkce Viz téma [Vytvoření materializované zobrazení jako Select (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
- Podpora doporučení pro materializované zobrazení pro konkrétní dotazy  Viz [vysvětlení (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="common-scenarios"></a>Obvyklé scénáře  

Materializovaná zobrazení se obvykle používají v následujících scénářích:

**Je potřeba zlepšit výkon složitých analytických dotazů na velikost velkých dat.**

Složité analytické dotazy obvykle využívají více agregačních funkcí a tabulkových spojení, což způsobuje více výpočetních operací, jako jsou například operace v průběhu a spojení při provádění dotazu.  To je důvod, proč se tyto dotazy dokončí déle, zvláště u velkých tabulek.  

Uživatelé mohou vytvářet materializovaná zobrazení pro data vrácená ze společných výpočtů dotazů, takže není nutné provádět žádné přepočítání, když je tato data potřebná pro dotazy, což umožňuje snížit náklady na výpočetní výkon a rychlejší reakci na dotazy.

**Potřebujete rychlejší výkon pomocí žádné nebo minimální změny dotazu.**

Změny schématu a dotazů v fondech SQL jsou obvykle udržovány minimálně pro podporu běžných operací ETL a vytváření sestav.  Uživatelé mohou použít materializovaná zobrazení pro ladění výkonu dotazů, pokud náklady vzniklé zobrazeními mohou být posunuty pomocí nárůstu výkonu dotazů.

V porovnání s dalšími možnostmi optimalizace, jako je škálování a Správa statistiky, je to mnohem méně ovlivněná produkční změna k vytvoření a údržbě materializované zobrazení a jeho potenciálního nárůstu výkonu je také vyšší.

- Vytváření nebo udržování materializovaná zobrazení nemá vliv na dotazy běžící se základními tabulkami.
- Optimalizátor dotazů může automaticky použít nasazená vyhodnocená zobrazení bez přímého odkazu zobrazení v dotazu. Tato schopnost snižuje nutnost změny dotazů v ladění výkonu.

**Pro rychlejší výkon dotazů potřebujete jinou strategii distribuce dat**

Fond SQL je distribuovaný systém pro hromadné paralelní zpracování (MPP).   Data v tabulce fondů SQL jsou distribuována mezi 60 uzlů pomocí jedné ze tří [strategií distribuce](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (hash, round_robin nebo replikovaný).  

Distribuce dat je určena v okamžiku vytvoření tabulky a zůstane beze změny, dokud není tabulka vyřazena. Materializované zobrazení, které je virtuální tabulkou na disku, podporuje distribuce dat a round_robin.  Uživatelé můžou zvolit distribuci dat, která se liší od základních tabulek, ale optimální pro výkon dotazů, které používají zobrazení nejvíce.  

## <a name="design-guidance"></a>Doprovodné materiály k návrhu

Tady je obecné pokyny k používání materializovaná zobrazení pro zlepšení výkonu dotazů:

**Návrh pro vaše úlohy**

Než začnete vytvářet materializovaná zobrazení, je důležité porozumět vašim úlohám z hlediska vzorů dotazů, důležitosti, četnosti a velikosti výsledných dat.  

Uživatelé mohou pro vyhodnocená zobrazení doporučená nástrojem pro optimalizaci dotazu spustit vysvětlení WITH_RECOMMENDATIONS <SQL_statement>.  Vzhledem k tomu, že tato doporučení jsou specifická pro konkrétní dotazy, materializované zobrazení, které přináší jeden dotaz, nemusí být optimální pro jiné dotazy ve stejné zátěži.  

Vyhodnoťte tato doporučení s ohledem na vaše úlohy.  Ideální materializovaná zobrazení jsou ta, která mají vliv na výkon úloh.  

**Mějte na paměti kompromisy mezi rychlejšími dotazy a náklady**

Pro každé materializované zobrazení jsou k dispozici náklady na úložiště dat a náklady na údržbu zobrazení.  Při změně dat v základních tabulkách se zvyšuje velikost vyhodnoceného zobrazení a jeho fyzická struktura se také změní.  Aby se zabránilo snížení výkonu dotazů, jsou všechna materializovaná zobrazení udržována samostatně modulem fondu SQL.  

Úloha údržby získá vyšší hodnotu, když se zvýší počet materializovaná zobrazení a základní tabulka.   Uživatelé by měli zjistit, zda náklady vzniklé ze všech hodnocených zobrazení mohou být posunuty pomocí nárůstu výkonu dotazů.  

Tento dotaz můžete spustit pro seznam materializované zobrazení v databázi:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Možnosti snížení počtu materializovaná zobrazení:

- Identifikujte běžné datové sady často používané složitými dotazy ve vašich úlohách.  Vytvořte materializovaná zobrazení pro ukládání těchto datových sad, aby je Optimalizátor mohl použít jako stavební bloky při vytváření plánů provádění.  

- Vyřadit materializovaná zobrazení, která mají nízké využití nebo již nejsou potřebná.  Zakázané zobrazení s materializací není zachováno, ale stále stojí náklady na úložiště.  

- Zkombinujte materializovaná zobrazení vytvořená ve stejných nebo podobných základních tabulkách i v případě, že se jejich data nepřekrývají.  Kombinování zobrazení s materializací může mít za následek větší zobrazení velikosti, než součet samostatných zobrazení, ale náklady na údržbu by se měly snížit.  Například:

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

**Ne všechny optimalizace výkonu vyžadují změnu dotazu.**

Optimalizátor fondů SQL může automaticky využívat nasazená vyhodnocená zobrazení ke zlepšení výkonu dotazů.  Tato podpora se transparentně aplikuje na dotazy, které neodkazují na zobrazení a dotazy, které používají agregace nepodporované při vytváření materializovaná zobrazení.  Žádná změna dotazu není nutná. Můžete zkontrolovat předpokládaný plán spouštění dotazu a ověřit, zda je použito materializované zobrazení.  

**Monitorování materializovaná zobrazení**

Materializované zobrazení je uloženo ve fondu SQL stejným způsobem jako tabulka s clusterovaným indexem columnstore (Ski).  Čtení dat z materializované zobrazení zahrnuje kontrolu segmentů indexu instrukce a použití přírůstkových změn ze základních tabulek. Pokud je počet přírůstkových změn příliš vysoký, řešení dotazu z materializované zobrazení může trvat déle než přímé dotazování na základní tabulky.  

Aby se zabránilo snížení výkonu dotazů, je dobrým zvykem spustit [příkaz DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) pro monitorování overhead_ratio zobrazení (total_rows/Max (1, base_view_row)).  Uživatelé by měli znovu sestavit materializované zobrazení, pokud je jeho overhead_ratio příliš vysoký.

**Materializované zobrazení a ukládání sad výsledků do mezipaměti**

Tyto dvě funkce jsou představené ve fondu SQL zhruba po stejnou dobu pro ladění výkonu dotazů.  Mezipaměť sady výsledků slouží k získání vysoké souběžnosti a rychlé odezvy z opakujících se dotazů na statická data.  

Chcete-li použít výsledek uložený v mezipaměti, musí být ve formátu mezipaměti požadující dotaz odpovídající dotazu, který vytvořil mezipaměť.  Kromě toho musí výsledek uložený v mezipaměti platit pro celý dotaz.  

Materializovaná zobrazení povolují změny dat v základních tabulkách.  Data v materializovaná zobrazení je možné použít na určitou část dotazu.  Tato podpora umožňuje použít stejné materializovaná zobrazení v různých dotazech, které sdílejí určitý výpočet pro rychlejší výkon.

## <a name="example"></a>Příklad

V tomto příkladu se používá dotaz podobné TPCDS, který vyhledává zákazníky, kteří tráví více peněz prostřednictvím katalogu než v obchodech, identifikujte preferované zákazníky a jejich zemi nebo oblast původu.   Dotaz zahrnuje výběr prvních 100 záznamů ze SJEDNOCENí tří příkazů dílčího výběru zahrnujících SUM () a GROUP BY.

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

Ověřte odhadované plánované spuštění dotazu.  K dispozici je 18 operací s více operacemi a 17 operací spojení, což trvá více času. Nyní vytvoříme pro každé tři příkazy dílčího výběru jedno materializované zobrazení.

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

Znovu ověřte plán spuštění původního dotazu.  Počet spojení se teď změní ze 17 na 5 a už není náhodně prováděná.  V plánu klikněte na ikonu operace filtru. v seznamu výstup se zobrazí data čtená z vyhodnocených zobrazení místo základních tabulek.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

U materializovaná zobrazení se stejný dotaz spouští mnohem rychleji, aniž by došlo ke změně kódu.  

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje synapse fondu SQL](sql-data-warehouse-overview-develop.md).
