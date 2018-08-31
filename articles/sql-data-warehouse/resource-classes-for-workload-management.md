---
title: Třídy prostředků pro správu úloh – Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Pokyny k používání třídy prostředků ke správě souběžnosti a výpočetní prostředky pro dotazy ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/26/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 8d0138d20e1a30ab3efc509eb71f17a6b1e4e8e5
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287468"
---
# <a name="workload-management-with-resource-classes-in-azure-sql-data-warehouse"></a>Správa úloh pomocí tříd prostředků ve službě Azure SQL Data Warehouse
Pokyny k používání třídy prostředků ke správě paměti a souběžnosti pro dotazy ve službě Azure SQL Data Warehouse.  
 
## <a name="what-is-workload-management"></a>Co je Správa úloh?
Správa úloh je schopnost optimalizovat výkon všech dotazů. Zapíná se dobře úloha běží, dotazy a operace načítání efektivně bez ohledu na to, zda jsou náročné na výpočetní nebo vstupně-výstupní operace náročné na prostředky.  SQL Data Warehouse nabízí možnosti správy úloh pro prostředí více uživatelů. Datový sklad není určena pro úlohy s více tenanty.

Kapacita výkonu služby data warehouse je určena [jednotkách datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md). 

- Omezení paměti a souběžnosti pro všechny profily výkonu, naleznete v tématu [omezení paměti a souběžnosti](memory-and-concurrency-limits.md).
- Chcete-li upravit kapacitu výkonu, můžete [vertikálně navyšovat nebo snižovat](quickstart-scale-compute-portal.md).

Kapacita výkon dotazu je určena třída prostředku v dotazu. Zbývající část tohoto článku vysvětluje co jsou třídy prostředků a jak upravovat.

## <a name="what-are-resource-classes"></a>Co jsou třídy prostředků?
Kapacita výkon dotazu je určena třída prostředků uživatele.  Třídy prostředků se předem určit omezení prostředků ve službě Azure SQL Data Warehouse, kterými se řídí výpočetních prostředků a souběžnost pro provedení dotazu. Třídy prostředků vám umožňují spravovat vaše úlohy nastavením limitů počtu dotazů, které běží souběžně a výpočetní prostředky každého dotazu Query přiřazena. Mezi pamětí a souběžnosti je obchod vypnout.

- Menší třídy prostředků snížit maximální paměť na dotazu, ale zvýšit souběžnost.
- Větší třídy prostředků se zvyšuje maximální paměť na dotazu, ale snížit souběžnost. 

Existují dva typy tříd prostředků:

- Třídy statických prostředků, které jsou vhodné pro vyšší souběžnosti na velikost datové sady, který je vyřešit.
- Dynamický prostředek třídy, které jsou vhodné pro datové sady, které jsou stále se rozšiřující velikost a zvýšit výkon, jako je škálovat na úrovni služby.   

Třídy prostředků měření využití prostředků pomocí slotů souběžnosti.  [Sloty souběžnosti](#concurrency-slots) jsou vysvětleny dále v tomto článku. 

- Chcete-li zobrazit využití prostředků pro třídy prostředků, najdete v článku [omezení paměti a souběžnosti](memory-and-concurrency-limits.md#concurrency-maximums).
- Chcete-li upravit třídy prostředků, můžete spustit dotaz pod jiným uživatelským nebo [změnit třídu prostředků aktuálního uživatele](#change-a-users-resource-class) členství. 

### <a name="static-resource-classes"></a>Statických tříd prostředků
Statických tříd prostředků přidělit stejné množství paměti bez ohledu na aktuální úroveň výkonu, který se měří v [jednotkách datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md). Protože dotazů získat stejné přidělení paměti bez ohledu na úroveň výkonu [horizontální navýšení kapacity datového skladu](quickstart-scale-compute-portal.md) umožňuje více dotazů ke spuštění v rámci třídy prostředků.  Statický prostředek třídy jsou ideální, pokud se označuje datový svazek a konstantní.

Tyto předdefinované databázové role jsou implementovány statických tříd prostředků:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Dynamický prostředek třídy
Dynamické třídy prostředků přidělit variabilní velikost paměti v závislosti na aktuální úrovni služby. Statických tříd prostředků jsou užitečné pro vyšší souběžnosti a statické datové svazky, dynamický prostředek třídy se lépe hodí pro množství dat rostoucí nebo proměnné.  Když vertikálně navýšit kapacitu na vyšší úroveň služby, dotazy, automaticky získá větší množství paměti.  

Dynamický prostředek třídy jsou implementovány pomocí tyto předdefinované databázové role:

- smallrc
- mediumrc
- largerc
- xlargerc 

### <a name="gen2-dynamic-resource-classes-are-truly-dynamic"></a>Dynamický prostředek třídy Gen2 jsou skutečně dynamické
Při digging do podrobností třídy dynamické prostředků na Gen1, existuje několik podrobností, které přidávají další složitosti k pochopení jejich chování:

- Třída prostředků smallrc funguje s modelem pevné paměti jako statický prostředek třídy.  Dotazy Smallrc dynamicky nelze získat větší množství paměti, jako je vyšší úroveň služby.
- Měnit úrovně služeb, k dispozici dotazu souběžnosti Přejít nahoru nebo dolů.
- Škálování úrovně služby neposkytuje proporční změna je paměť přidělená pro stejné třídy prostředků.

Na **Gen2 pouze**, dynamický prostředek třídy jsou skutečně dynamické adresy bodů uvedených výše.  Nové pravidlo je 3-10-22-70 pro procento přidělení paměti pro třídy prostředků (krátkodobé používání) – střední velké xlarge **bez ohledu na úroveň služeb**.  Níže uvedená tabulka obsahuje konsolidované podrobné informace o procenta přidělení paměti a minimální počet souběžných dotazů, na kterých běží, bez ohledu na úrovni služby.

| Třída prostředku | Procento paměti | Minimální počet souběžných dotazů |
|:--------------:|:-----------------:|:----------------------:|
| smallrc        | 3 %                | 32                     |
| mediumrc       | 10 %               | 10                     |
| largerc        | 22 %               | 4                      |
| xlargerc       | 70 %               | 1                      |


### <a name="default-resource-class"></a>Výchozí třídy prostředků
Ve výchozím nastavení, každý uživatel je členem třídy dynamický prostředek **smallrc**. 

Třída prostředků Správce služeb je pevná a nedá se změnit.  Správce služeb je uživatelem vytvořené během procesu zřizování.

> [!NOTE]
> Uživatele nebo skupiny, které jsou definované jako správce Active Directory jsou také správci služeb.
>
>

## <a name="resource-class-operations"></a>Operace prostředků třídy

Třídy prostředků slouží pro zvýšení výkonu pro aktivity správy a manipulace dat. Komplexní dotazy mohou také těžit z spuštění pod velké třídy prostředků. Například dotazování výkonu pro velká spojení a vylepšit řazení při dostatečně velký, aby dotaz v paměti spouštět třídy prostředků.

### <a name="operations-governed-by-resource-classes"></a>Operace řídí třídy prostředků

Tyto operace se řídí třídy prostředků:

* VÝBĚR INSERT, UPDATE, DELETE
* Vyberte (při dotazování tabulky uživatelů)
* Příkaz ALTER INDEX - znovu SESTAVIT nebo REORGANIZOVAT
* PŘÍKAZ ALTER TABLE OPĚTOVNÉ SESTAVENÍ
* VYTVOŘENÍ INDEXU
* VYTVOŘIT CLUSTEROVANÝ INDEX COLUMNSTORE
* VYTVOŘENÍ TABLE AS SELECT (CTAS)
* Načítání dat
* Operace přesunu dat, které podle dat přesun Service (DMS)

> [!NOTE]  
> Vyberte příkazy na zobrazení dynamické správy (DMV) nebo jiný systém, které zobrazení se řídí podle těchto limitů souběžnosti. Systém bez ohledu na počet dotazů provádění na něm můžete monitorovat.
> 
> 

### <a name="operations-not-governed-by-resource-classes"></a>Operace není řídí třídy prostředků
Některé dotazy se vždy spustit ve třídě prostředků smallrc i v případě, že uživatel je členem větší třídu prostředků. Tyto dotazy vyloučení nepočítá směrem k omezení souběžnosti. Například pokud omezení souběžnosti je 16, mnoho uživatelů můžete budete vybírat ze zobrazení systému bez dopadu na slotů souběžnosti k dispozici.

Následující příkazy jsou vyloučené z třídy prostředků a vždy spouštějí v smallrc:

* Vytvořit nebo DROP TABLE
* PŘÍKAZ ALTER TABLE... PŘEPÍNAČE, ROZDĚLIT nebo sloučit oddíl
* PŘÍKAZ ALTER INDEX DISABLE
* ODSTRANIT INDEX
* Vytvoření, aktualizace nebo použít příkaz DROP STATISTICS
* TRUNCATE TABLE
* PŘÍKAZ ALTER AUTORIZACE
* VYTVOŘTE PŘIHLAŠOVACÍ ÚDAJE
* CREATE, ALTER nebo DROP USER
* CREATE, ALTER nebo VYŘADIT PROCEDURY
* Vytvořit nebo VYŘADIT zobrazení
* VLOŽENÍ HODNOT
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
Sloty souběžnosti jsou pohodlný způsob, jak sledovat prostředky dostupné pro spuštění dotazu. Jsou to například lístků, které jste zakoupili k rezervaci míst v shodě, protože sedadel je omezen. Celkový počet slotů souběžnosti na datový sklad se určuje podle úrovně služby. Předtím, než dotaz můžete spustit provádění, musí být schopni rezervovat dostatek slotů souběžnosti. Po dokončení dotazu uvolní jeho slotů souběžnosti.  

- Dotaz s 10 slotů souběžnosti můžete přistupovat 5krát další výpočetní prostředky než dotazu s 2 slotů souběžnosti.
- Pokud každý dotaz vyžaduje 10 slotů souběžnosti a existují 40 slotů souběžnosti, pak pouze 4 dotazy můžou běžet souběžně.
 
Pouze dotazy netrvá využívat slotů souběžnosti. Dotazy systému a některé jednoduché nespotřebovávají žádné sloty. Třídy prostředků tohoto dotazu je určeno přesný počet spotřebovaných slotů souběžnosti.

## <a name="view-the-resource-classes"></a>Zobrazení tříd prostředků

Třídy prostředků se implementují jako předem definované databázové role. Existují dva typy třídy prostředků: dynamická a statická. Chcete-li zobrazit seznam tříd prostředků, použijte následující dotaz:

```sql
SELECT name 
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Změnit třídy prostředků tohoto uživatele

Třídy prostředků se implementují prostřednictvím přiřazování uživatelů do databázových rolí. Když uživatel spustí dotaz, spustí se dotaz s třídy prostředků tohoto uživatele. Například pokud je uživatel členem role databáze smallrc nebo staticrc10, jejich dotazy se spustí s malé množství paměti. V případě databáze uživatel je členem databázové role xlargerc nebo staticrc80, jejich dotazy se spustí s velkým množstvím paměti. 

Ke zvýšení třídy prostředků tohoto uživatele, použijte uloženou proceduru [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). 

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Chcete-li snížit třídy prostředků, použijte [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Priorita třídy prostředků
Uživatelé můžou být členy více tříd prostředků. Pokud uživatel patří do více než jeden prostředek třídy:

- Dynamický prostředek třídy má přednost před statických tříd prostředků. Například pokud je uživatel členem mediumrc(dynamic) a staticrc80 (statické), dotazy spustit s mediumrc.
- Větší třídy prostředků má přednost před menší třídy prostředků. Například pokud je uživatel členem mediumrc a largerc, dotazy spustit s largerc. Podobně pokud je uživatel členem staticrc20 a statirc80, dotazy se spustí s staticrc80 přidělení prostředků.

## <a name="recommendations"></a>Doporučení
Doporučujeme vytvořit jako uživatel, který je vyhrazený ke spouštění určitého typu dotazu nebo operace načítání. Potom dát takovému uživateli trvalý prostředek třídy místo změny třídy prostředků často. Vzhledem k tomu, že statických tříd prostředků poskytují větší kontrolu celkové na pracovním vytížení také doporučujeme použít ty předtím, než dynamický prostředek třídy.

### <a name="resource-classes-for-load-users"></a>Třídy prostředků pro zatížení uživatele
`CREATE TABLE` použití Clusterované indexy columnstore ve výchozím nastavení. Komprese dat do columnstore index je operace s vysokými nároky na paměť a přetížení paměti může snížit kvalitu indexu. Proto se nejpravděpodobněji při načítání dat vyžadují vyšší třídě prostředků. K zajištění, že zatížení k dispozici dostatek paměti, můžete vytvořit uživatele, který je určený pro spouštění načítání a přiřaďte ho k vyšší třídě prostředků.

Paměť potřebná ke zpracování zátěže efektivně závisí na povaze tabulce načten a velikosti dat. Další informace o požadavky na paměť, naleznete v tématu [maximalizuje rowgroup kvality](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Po určení požadavek na paměť, zvolte, jestli se má přiřadit uživatele načítání pro třídu prostředků se statickou nebo dynamickou.

- Používejte třídu prostředků se statické požadavky na paměť tabulky spadá do určitého rozsahu. Načítání spuštění s odpovídající paměti. Když horizontálně snížíte kapacitu datového skladu, nemusí se zatížení více paměti. Pomocí třídy statických prostředků, přidělení paměti zůstanou konstantní. Taková konzistence šetří paměť a umožňuje jak souběžně spustit více dotazů. Doporučujeme použít nová řešení statických tříd prostředků nejdřív tyto poskytují větší kontrolu.
- Používejte dynamickou třídu prostředků tabulce požadavky na paměť se výrazně lišit. Zatížení může vyžadovat více paměti než aktuální DWU nebo cDWU úroveň poskytuje. Proto škálování datového skladu přidá větší množství paměti zatížení operací, což umožňuje rychlejší načítání.

### <a name="resource-classes-for-queries"></a>Třídy prostředků pro dotazy

Některé dotazy jsou náročné na výpočetní a jiné ne.  

- Zvolte dynamickou třídu prostředků, pokud jsou komplexní dotazy, ale není nutné vysokou souběžnosti.  Generování sestav denní nebo týdenní je například občasné potřebu prostředky. Pokud zprávy zpracovávají velké objemy dat, škálování datový sklad poskytuje více paměti pro existující třídy prostředků tohoto uživatele.
- Zvolte třídu statických prostředků při očekávání prostředků se liší v průběhu dne. Například třída statických prostředků funguje dobře, pokud datový sklad je dotazován mnoho uživatelů. Při horizontálním škálování datového skladu, množství paměti přidělené pro uživatele se nezmění. V důsledku toho mohou být provedeny více dotazů paralelně v systému.

Výběr přidělení správné paměti závislá na mnoha faktorech, jako množství dat získaných, druh schémat tabulek a různých spojení, vybrat a skupině predikáty. Obecně platí přidělení více paměti umožňuje vytvářet dotazy rychleji, ale snižuje celkový souběžnosti. Pokud souběžnost není problém, over-pass-the přidělování paměti nepoškodí propustnost. 

Pro optimalizaci výkonu, použijte jiný prostředek třídy. Další část poskytuje uloženou proceduru, která vám pomůže zjistit nejlepší třídy prostředků.

## <a name="example-code-for-finding-the-best-resource-class"></a>Ukázkový kód pro vyhledání nejlepších třídy prostředků
 
Můžete použít následující uložené procedury na **Gen1 pouze** zjistěte souběžnosti a paměti udělit na třídu prostředků na daný objekt SLO a nejbližší nejlepší Třída prostředků pro operace náročné na CCI na CCI bez oddílů tabulky v paměti daný prostředek třídy:

Tady je účelem tuto uloženou proceduru:  
1. Zobrazíte souběžnosti a za třídu prostředků na daný objekt SLO přidělení paměti. Uživatel musí poskytnout hodnotu NULL pro schéma a tablename, jak je znázorněno v tomto příkladu.  
2. Zobrazíte nejbližší nejlepší třídy prostředků pro CCI vysokými nároky na paměť operace (zatížení, zkopírujte tabulku znovu sestavit index, atd.) v jiných oddílů CCI tabulky ve třídě daný prostředek. Uložené procedury používá k nalezení limitu přidělení paměti požadované schéma tabulky.

### <a name="dependencies--restrictions"></a>Závislosti a omezení:
- Tato uložená procedura není určená k výpočtu požadavek na paměť pro cci dělenou tabulku.    
- Tuto uloženou proceduru nepřijímá požadavky na paměť v úvahu pro výběr součástí CTAS nebo INSERT-SELECT a předpokládá, že se že jedná s výběrem.
- Tuto uloženou proceduru používá dočasnou tabulku, která je k dispozici v relaci, kde byl vytvořen tuto uloženou proceduru.    
- Tuto uloženou proceduru závisí na aktuálně dostupných nabídek (třeba konfigurace hardwaru, konfigurace DMS), a pokud se změní některý, který pak tato uložená procedura nebude správně fungovat.  
- Tuto uloženou proceduru závisí na existující limit nabízených souběžnosti a pokud se změní, který pak tato uložená procedura nebude fungovat správně.  
- Tuto uloženou proceduru závisí na stávající nabídky třídy prostředků a pokud se změní, který pak tato uložená procedura nebude fungovat správně.  

>  [!NOTE]  
>  Pokud nedostáváte výstup po spuštění uložené procedury s parametry poskytnutými, pak může existovat dva možné případy. <br />1. Buď parametr datový Sklad obsahuje neplatnou hodnotu SLO <br />2. Nebo, neexistuje žádná odpovídající Třída prostředků pro operaci CCI v tabulce. <br />Například v DW100, nejvyšší přidělení paměti, která je k dispozici je 400 MB, a pokud schéma tabulky je dost široký, vývoj pro různé požadavky na 400 MB.
      
### <a name="usage-example"></a>Příklad použití:
Syntaxe:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: Buď zadejte parametr NULL extrahovat aktuální DWU z databáze datového skladu, nebo všechny podporované DWU ve formě "DW100.
2. @SCHEMA_NAME: Zadejte název schématu tabulky
3. @TABLE_NAME: Zadejte název tabulky zájmu

Příklady provádění této uložené procedury:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```
> [!NOTE]
> Hodnot fronty definovaných v této verzi uložená procedura platí jenom pro Gen1.
>
>

Následující příkaz vytvoří Table1, který se používá v předchozích příkladech.
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
Další informace o správě uživatelů a zabezpečení najdete v tématu [zabezpečit databázi ve službě SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Další informace o tom, jak větší třídy prostředků může zlepšit kvalitu indexu columnstore clusteru, najdete v části [optimalizace paměti pro kompresi columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
