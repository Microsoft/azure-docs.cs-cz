---
title: Třídy prostředků pro úlohy správy – Azure SQL Data Warehouse | Microsoft Docs
description: Pokyny pro použití třídy prostředků ke správě souběžnosti a výpočetní prostředky pro dotazy v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: kevin
ms.reviewer: jrj
ms.openlocfilehash: 289281567eff7f2575f26f1ae7ec2f9ee4389461
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="workload-management-with-resource-classes-in-azure-sql-data-warehouse"></a>Úlohy správy pomocí třídy prostředků v Azure SQL Data Warehouse
Pokyny pro použití třídy prostředků ke správě paměti a souběžnost pro dotazy v Azure SQL Data Warehouse.  
 
## <a name="what-is-workload-management"></a>Co je pracovní zatížení management?
Úlohy správy je možnost optimalizovat celkový výkon všech dotazů. Dobře ujít zatížení spustí dotazy a operace zatížení efektivně bez ohledu na tom, jestli mají náročné nebo náročné na vstupně-výstupní operace.  SQL Data Warehouse poskytuje možnosti správy úloh pro prostředí s více uživateli. Datový sklad není určeno pro více klientů úlohy.

Kapacita výkonu datového skladu je určena [úroveň výkonu](memory-and-concurrency-limits.md#performance-tiers) a [datového skladu jednotky](what-is-a-data-warehouse-unit-dwu-cdwu.md). 

- Paměť a souběžnost limity pro všechny profily výkonu najdete v tématu [omezení paměti a souběžnost](memory-and-concurrency-limits.md).
- Chcete-li upravit výkonu kapacitu, můžete [škálovat nahoru nebo dolů](quickstart-scale-compute-portal.md).

Kapacita výkonu dotazu je určen podle třídy prostředků dotazu. Tento zbývající část tohoto článku vysvětluje, co jsou třídy prostředků a způsob jejich nastavení.


## <a name="what-are-resource-classes"></a>Jaké jsou třídy prostředků?
Třídy prostředků se předem určit, že omezení prostředků v Azure SQL Data Warehouse, které řídí výpočetní prostředky a souběžnost pro spuštění dotazu. Třídy prostředků vám může pomoci spravovat vaši úlohu nastavením omezení na počet dotazů, které běží souběžně a výpočetní prostředky přiřazené každý dotaz. Je kompromis mezi paměti a souběžnosti.

- Menší třídy prostředků snížit maximální paměť na dotaz, ale zvýšit souběžnost.
- Větší třídy prostředků zvyšuje maximální paměť na dotaz, ale snížit souběžnosti. 

Kapacita výkonu dotazu je určen podle třídy prostředků uživatele.

- Využití prostředků pro třídy prostředků najdete v tématu [omezení paměti a souběžnost](memory-and-concurrency-limits.md#concurrency-maximums).
- Chcete-li upravit Třída prostředků, můžete spustit dotaz pod odlišným uživatelským nebo [změnit Třída prostředků aktuálního uživatele](#change-a-user-s-resource-class) členství. 

Třídy prostředků pomocí souběžnosti sloty k měření spotřeby prostředků.  [Sloty souběžnosti](#concurrency-slots) jsou popsané dále v tomto článku. 

### <a name="static-resource-classes"></a>Statické prostředků třídy
Statické prostředků třídy přidělit stejnou velikost paměti, bez ohledu na aktuální úroveň výkonu, který se měří v [datového skladu jednotky](what-is-a-data-warehouse-unit-dwu-cdwu.md). Vzhledem k tomu, že dotazy získat stejné přidělení paměti bez ohledu na úroveň výkonu [škálování datového skladu](quickstart-scale-compute-portal.md) umožňuje další dotazy ke spuštění v rámci třídy prostředků.

Statické prostředků třídy jsou implementované pomocí těchto předem definovaných databázových rolí:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

Tyto třídy prostředků jsou nejvhodnější pro řešení, které zvyšují Třída prostředků se získat další výpočetní prostředky.

### <a name="dynamic-resource-classes"></a>Dynamické prostředků třídy
Dynamické třídy prostředků přidělit proměnné množství paměti v závislosti na aktuální úrovni služby. Při změně měřítka na vyšší úroveň služby, vaše dotazy automaticky získat více paměti. 

Dynamické prostředků třídy jsou implementované pomocí těchto předem definovaných databázových rolí:

- smallrc
- mediumrc
- largerc
- xlargerc. 

Tyto třídy prostředků jsou nejvhodnější pro řešení, které zvýšení výpočetní škálování, chcete-li získat další prostředky. 


### <a name="default-resource-class"></a>Třída prostředků výchozí
Ve výchozím nastavení, každý uživatel je členem třídy dynamické prostředků **smallrc**. 

Třída prostředků Správce služby je pevná a nedá se změnit.  Správce služeb je uživatelem vytvořené během procesu zřizování.

> [!NOTE]
> Správci služeb jsou i uživatelé nebo skupiny, které jsou definované jako správce Active Directory.
>
>

## <a name="resource-class-operations"></a>Operace prostředků – třída

Třídy prostředků jsou navrženy pro zlepšení výkonu pro aktivity správy a manipulace dat. Komplexní dotazy mohou také těžit z spuštěna pod třídou prostředků velké. Například dotazování výkonu pro velké spojení a řazení může zvýšit, pokud je třída prostředků dostatečně velké na to, aby dotaz na provedení v paměti.

### <a name="operations-governed-by-resource-classes"></a>Operace řídí třídy prostředků

Třídy prostředků se řídí tyto operace:

* VYBERTE PŘÍKAZ INSERT, UPDATE, DELETE
* Vyberte (při dotazování tabulky uživatelů)
* Příkaz ALTER INDEX - znovu SESTAVIT nebo REORGANIZOVAT
* PŘÍKAZ ALTER TABLE OPĚTOVNÉ SESTAVENÍ
* VYTVOŘENÍ INDEXU
* VYTVOŘIT CLUSTEROVANÝ INDEX COLUMNSTORE
* VYTVOŘENÍ TABLE AS SELECT (FUNKCE CTAS)
* Načítání dat
* Operace přesunu dat prováděné pomocí služby přesun dat (DMS)

> [!NOTE]  
> Vyberte příkazy na zobrazení dynamické správy (zobrazení dynamické správy) nebo jiné systémy, které nejsou žádné omezení souběžnosti řídí zobrazení. Můžete monitorovat systém bez ohledu na počet dotazy na spuštění v něm.
> 
> 

### <a name="operations-not-governed-by-resource-classes"></a>Operace není řídí třídy prostředků
Některé dotazy se vždy spustit v Třída prostředků smallrc, i když je uživatel členem skupiny větší Třída prostředků. Tyto vyloučený dotazy není započítávat limit souběžnosti. Například pokud limit souběžnosti 16, řada uživatelů můžete vybírat ze zobrazení systému bez dopadu na sloty dostupné souběžnosti.

Následující příkazy vyjmuté z třídy prostředků a vždy spouštějí v smallrc:

* Vytvořit nebo VYŘADIT tabulku
* PŘÍKAZ ALTER TABLE... PŘEPÍNAČE, rozdělení nebo sloučit oddíl
* PŘÍKAZ ALTER INDEX ZAKÁZAT
* DROP INDEX
* Vytvoření, aktualizace a použít příkaz DROP STATISTICS
* ZKRÁCENÍ TABULKY
* PŘÍKAZ ALTER AUTORIZACE
* VYTVOŘIT PŘIHLÁŠENÍ
* CREATE, ALTER nebo DROP USER
* CREATE, ALTER nebo VYŘADIT postup
* Vytvořit nebo VYŘADIT zobrazení
* VLOŽENÍ HODNOTY
* Vyberte z systémová zobrazení a zobrazení dynamické správy
* VYSVĚTLUJÍ
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Sloty souběžnosti
Concurrency sloty jsou pohodlný způsob, jak sledovat prostředky k dispozici pro spuštění dotazu. Jsou to například lístků, které jste si koupili tak, aby vyhradil licencí na vzájemné součinnosti, protože zasedací je omezená. Celkový počet patic souběžnosti za datového skladu je dáno úrovně služby. Předtím, než dotaz můžete spustit provádění, musí být možné vyhradit dostatek sloty souběžnosti. Po dokončení dotazu uvolní jeho sloty souběžnosti.  

- Dotaz s 10 sloty souběžnosti můžete přístup 5krát víc zdrojích výpočtů než dotazu s 2 sloty souběžnosti.
- Pokud každý dotaz vyžaduje 10 souběžnosti sloty a nejsou 40 souběžnosti sloty, můžete současně spustit jenom 4 dotazy.
 
Pouze na dotazy prostředků, které využívají sloty souběžnosti. Dotazy na systém a některé trivial není využívat žádné sloty. Přesné číslo souběžnosti sloty spotřebované je dáno Třída prostředků dotazu.

## <a name="view-the-resource-classes"></a>Zobrazí třídy prostředků

Třídy prostředků se implementují jako předem definovaných databázových rolí. Existují dva typy třídy prostředků: dynamických a statických. Chcete-li zobrazit seznam tříd prostředků, použijte následující dotaz:

    ```sql
    SELECT name FROM sys.database_principals
    WHERE name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
    ```

## <a name="change-a-users-resource-class"></a>Změnit Třída prostředků uživatele

Třídy prostředků se implementují přiřadit uživatele k role databáze. Když uživatel spustí dotaz, spustí dotaz s třídou prostředků uživatele. Například pokud je uživatel členem role databáze smallrc nebo staticrc10, jejich dotazy se spustí s malé množství paměti. Pokud je databáze uživatel členem role databáze xlargerc nebo staticrc80, jejich dotazy se spustí s velkými objemy paměti. 

Třída prostředků uživatele zvýšit, použijte uloženou proceduru [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). 

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Chcete-li snížit Třída prostředků, použijte [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Priorita prostředků – třída
Uživatelé mohou být členy více tříd prostředků. Když uživatel patří do více než jedna třída prostředků:

- Dynamické prostředků třídy mají přednost před třídy statické prostředků. Například pokud je uživatel členem mediumrc(dynamic) a staticrc80 (statické), dotazy se spustí s mediumrc.
- Větší třídy prostředků má přednost před menší třídy prostředků. Například pokud je uživatel členem mediumrc a largerc, dotazy se spustí s largerc. Podobně pokud je uživatel členem staticrc20 a statirc80, dotazy se spustí s staticrc80 přidělení prostředků.

## <a name="recommendations"></a>Doporučení
Doporučujeme vytvoření uživatele, který je vyhrazený ke spouštění určitý typ dotazu nebo načtení operace. Tento uživatel pak umožnit třída trvalé prostředků místo změna Třída prostředků na základě časté. Vzhledem k tomu, že statické prostředků třídy dovolit větší kontrolu celkové na pracovním vytížení také navrhnout pomocí těchto první před zvažování třídy dynamického prostředku.

### <a name="resource-classes-for-load-users"></a>Třídy prostředků pro zatížení uživatele
`CREATE TABLE` používá Clusterované indexy columnstore ve výchozím nastavení. Komprese dat do columnstore index je operace náročné na paměť a přetížení paměti může snížit kvalitu index. Proto se nejpravděpodobněji při načítání dat vyžadují vyšší Třída prostředků. K zajištění, že zatížení k dispozici dostatek paměti, můžete vytvořit uživatele, který je určen pro spuštění zatížení a přiřaďte ho do vyšší Třída prostředků.

Paměť potřebnému ke zpracování zatížení efektivně závisí na povahu načíst tabulky a velikost dat. Další informace o požadavky na paměť, najdete v části [Maximalizace kvality rowgroup](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Jakmile zjistíte, požadavek na množství paměti, vyberte, zda uživatel zatížení přiřadit třídy statickou nebo dynamickou prostředku.

- Použití třídy statické prostředků, požadavky na paměť tabulky, které jsou v rámci určitého rozsahu. Načítání spusťte s příslušnou paměti. Při změně měřítka datového skladu, nemusí se zatížení více paměti. Pomocí třídy statické prostředku zůstane konstantní přidělení paměti. Tato konzistence šetří paměť a umožňuje další dotazy k běžet souběžně. Doporučujeme použít třídy statické prostředků nová řešení nejdřív tyto poskytují větší kontrolu.
- Použití třídy dynamické prostředků, jestliže se požadavky na paměť tabulky velmi liší. Zatížení může vyžadovat více paměti, než je aktuální DWU nebo cDWU úroveň poskytuje. Škálování datového skladu nepřidá více paměti operací zatížení, která umožňuje načítání rychlejší.

### <a name="resource-classes-for-queries"></a>Třídy prostředků pro dotazy

Některé dotazy jsou náročné na výkon a některé nejsou.  

- Třída dynamické prostředků vyberte, pokud jsou komplexní dotazy, ale není nutné vysoké souběžnosti.  Generování sestav denní nebo týdenní je například příležitostně potřebu prostředků. Pokud sestavy jsou zpracování velkých objemů dat, škálování datový sklad poskytuje více paměti pro existující třída prostředků uživatele.
- Zvolte třídu statické prostředků při očekávání prostředků se liší v průběhu dne. Například třída statické prostředků funguje dobře, pokud datový sklad je dotazován mnoho uživatelů. Při zvětšení velikosti datového skladu, množství paměti přidělené pro uživatele se nezmění. Další dotazy v důsledku toho mohou být provedeny souběžně v systému.

Výběr přidělení správné paměti závislá na mnoha faktorech, například množství dat získaných, povahu schémata tabulek a různé spojení, vyberte a skupiny predikáty. Obecně platí přidělení více paměti umožňuje vytvářet dotazy rychlejší, ale snižuje celkové souběžnosti. Pokud souběžnosti není problém, přepsání přidělování paměti není poškodit propustnost. 

Pro optimalizaci výkonu, použijte jiný prostředek třídy. Poskytuje další části, uložené procedury, která vám pomůže pochopit nejlepší Třída prostředků.

## <a name="example-code-for-finding-the-best-resource-class"></a>Ukázkový kód pro hledání nejlepší Třída prostředků
 
Můžete provádět následující uložené procedury a pokuste se zjistit grant souběžnosti a paměti na třídě prostředků v dané SLO a nejbližší nejlepší Třída prostředků pro operace náročné na prostředky KÚS paměti na bez oddílů tabulky KÚS na třídy daného prostředku:

Tady je účelem tuto uloženou proceduru:  
1. Zobrazíte souběžnosti a paměti udělit za Třída prostředků v daném objektu SLO. Uživatel musí zadat hodnotu NULL pro schématu a název tabulky, jsou uvedené v tomto příkladu.  
2. Zobrazíte nejbližší nejlepší Třída prostředků KÚS náročné na paměť operací (zatížení, kopie tabulku znovu sestavit index, atd.) na jiný oddílů KÚS tabulky na třídu daného prostředku. Uložená procedura používá schéma tabulky a zjistěte, udělte požadovanou paměť.

### <a name="dependencies--restrictions"></a>Závislosti & omezení:
- Tuto uloženou proceduru nebyla navržena pro vypočítat požadavek na paměť pro tabulku oddílů KÚS.    
- Tuto uloženou proceduru neberou požadavek na paměť v úvahu vyberte součást funkce CTAS/INSERT-výběr a předpokládá, že je s výběrem.
- Tuto uloženou proceduru používá dočasnou tabulku, která je k dispozici v relaci, které bylo vytvořeno tuto uloženou proceduru.    
- Tuto uloženou proceduru závisí na aktuální nabídky (například hardwarová konfigurace, konfigurace DMS), a pokud všechny této změny pak tato uložená procedura nebude správně fungovat.  
- Tuto uloženou proceduru závisí na existující nabízený souběžnosti limit a pokud se změní, pak tuto uloženou proceduru nebude fungovat správně.  
- Tuto uloženou proceduru závisí na stávající nabídky Třída prostředků, a pokud se změní, pak tuto uloženou proceduru nebude fungovat správně.  

>  [!NOTE]  
>  Pokud se nezobrazují výstupu po spuštění uložené procedury s parametry zadané, pak může existovat dva případy. <br />1. Buď parametr datového skladu obsahuje neplatnou hodnotu SLO <br />2. Nebo, neexistuje žádná odpovídající Třída prostředků pro operaci KÚS v tabulce. <br />Například v od DW100, nejvyšší přidělení paměti k dispozici je 400 MB, a pokud schématu tabulky se dostatečnou šířku překříží požadavek 400 MB.
      
### <a name="usage-example"></a>Příklad použití:
Syntaxe:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: Buď zadejte parametr hodnotu NULL pro rozbalení aktuální DWU z databáze datového skladu nebo zadejte všechny podporované DWU ve tvaru "od DW100.
2. @SCHEMA_NAME: Zadejte název schématu tabulky
3. @TABLE_NAME: Zadejte název tabulky zájmu

Příklady provádění této uložené procedury:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Následující příkaz vytvoří tabulky1, který se používá v předchozích příkladech.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definice uložené procedury

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```




## <a name="next-steps"></a>Další postup
Další informace o správě uživatelů a zabezpečení najdete v tématu [zabezpečení databáze v SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Další informace o tom, jak větší třídy prostředků můžete zlepšení kvality indexu columnstore clusteru, najdete v části [optimalizace paměti pro kompresi columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
