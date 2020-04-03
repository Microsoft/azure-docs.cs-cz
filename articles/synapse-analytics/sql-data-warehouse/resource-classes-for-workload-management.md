---
title: Třídy prostředků pro správu pracovního vytížení
description: Pokyny pro použití tříd prostředků ke správě souběžnosti a výpočetníprostředky pro dotazy v Azure Synapse Analytics.
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
ms.openlocfilehash: 8ac9ff1f46e1d2d0ddaa313499340b4723c7da07
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584251"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Správa úloh s třídami prostředků v Azure Synapse Analytics

Pokyny pro použití tříd prostředků ke správě paměti a souběžnosti pro dotazy fondu SYNAPse SQL v Azure Synapse.  

## <a name="what-are-resource-classes"></a>Co jsou třídy prostředků

Kapacita výkonu dotazu je určena třídou prostředků uživatele.  Třídy prostředků jsou předem určené limity prostředků ve fondu SYNAPSE SQL, které řídí výpočetní prostředky a souběžnost pro spuštění dotazu. Třídy prostředků vám mohou pomoci nakonfigurovat prostředky pro vaše dotazy nastavením omezení počtu dotazů, které běží souběžně, a výpočetních prostředků přiřazených ke každému dotazu.  Existuje kompromis mezi pamětí a souběžnost.

- Menší třídy prostředků snížit maximální paměti na dotaz, ale zvýšit souběžnost.
- Větší třídy prostředků zvýšit maximální paměť na dotaz, ale snížit souběžnost.

Existují dva typy tříd prostředků:

- Statické prostředky třídy, které jsou vhodné pro zvýšení souběžnosti na velikost datové sady, která je pevná.
- Dynamické třídy prostředků, které jsou vhodné pro datové sady, které se zvětšují a potřebují vyšší výkon, protože úroveň služeb je navýšena.

Třídy prostředků používají sloty souběžnosti k měření spotřeby prostředků.  [Souběžnost sloty](#concurrency-slots) jsou vysvětleny dále v tomto článku.

- Informace o využití prostředků pro třídy prostředků naleznete v tématu [Limity paměti a souběžnosti](memory-concurrency-limits.md).
- Chcete-li upravit třídu prostředků, můžete spustit dotaz pod jiným uživatelem nebo změnit členství [ve třídě prostředků aktuálního uživatele.](#change-a-users-resource-class)

### <a name="static-resource-classes"></a>Statické třídy prostředků

Statické třídy prostředků přidělují stejné množství paměti bez ohledu na aktuální úroveň výkonu, která se měří v [jednotkách datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md). Vzhledem k tomu, že dotazy získat stejné přidělení paměti bez ohledu na úroveň výkonu, [horizontální navýšení kapacity datového skladu](quickstart-scale-compute-portal.md) umožňuje další dotazy ke spuštění v rámci třídy prostředků.  Statické třídy prostředků jsou ideální, pokud je datový svazek znám a konstantní.

Statické třídy prostředků jsou implementovány s těmito předdefinovanými databázovými rolemi:

- statrc10
- statrc20
- statrc30
- statrc40
- statrc50
- statrc60
- statrc70
- statrc80

### <a name="dynamic-resource-classes"></a>Dynamické třídy prostředků

Dynamické třídy prostředků přidělují proměnlivé množství paměti v závislosti na aktuální úrovni služby. Zatímco statické třídy prostředků jsou výhodné pro vyšší souběžnost a statické datové svazky, dynamické třídy prostředků jsou vhodnější pro rostoucí nebo proměnné množství dat.  Při škálování až na větší úroveň služby, vaše dotazy automaticky získat více paměti.  

Třídy dynamických prostředků jsou implementovány s těmito předdefinovanými databázovými rolemi:

- smallrc
- mediumrc
- largerc
- xlargerc

Přidělení paměti pro každou třídu prostředků je následující. 

| Úroveň služeb  | smallrc           | mediumrc               | largerc                | xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25 %               | 25 %                    | 25 %                    | 70 %                    |
| DW200c         | 12.5%             | 12.5%                  | 22%                    | 70 %                    |
| DW300c         | 8 %                | 10 %                    | 22%                    | 70 %                    |
| DW400c         | 6.25%             | 10 %                    | 22%                    | 70 %                    |
| DW500c.         | 5 %                | 10 %                    | 22%                    | 70 %                    |
| DW1000c až<br> DW30000c | 3 %       | 10 %                    | 22%                    | 70 %                    |



### <a name="default-resource-class"></a>Výchozí třída prostředků

Ve výchozím nastavení je každý uživatel členem třídy dynamického prostředku **smallrc**.

Třída prostředků správce služby je stanovena na smallrc a nelze ji změnit.  Správce služby je uživatel vytvořený během procesu zřizování.  Správce služby v tomto kontextu je přihlášení určené pro "Přihlášení správce serveru" při vytváření nového fondu Synapse SQL s novým serverem.

> [!NOTE]
> Uživatelé nebo skupiny definované jako správce služby Active Directory jsou také správci služeb.
>
>

## <a name="resource-class-operations"></a>Operace třídy prostředků

Třídy prostředků jsou navrženy ke zlepšení výkonu pro správu dat a manipulaci s nimi. Složité dotazy mohou také těžit z běhu pod v rámci velké třídy prostředků. Například výkon dotazu pro velká spojení a řazení může zlepšit, pokud je třída prostředků dostatečně velká, aby umožnila spuštění dotazu v paměti.

### <a name="operations-governed-by-resource-classes"></a>Operace řízené třídami prostředků

Tyto operace se řídí třídami prostředků:

- VLOŽIT-VYBRAT, AKTUALIZOVAT, ODSTRANIT
- SELECT (při dotazování uživatelských tabulek)
- ALTER INDEX - REBUILD nebo REORGANIZE
- ZMĚNIT OPĚTOVNÉ SESTAVENÍ TABULKY
- CREATE INDEX
- VYTVOŘIT CLUSTEROVANÝ INDEX COLUMNSTORE
- VYTVOŘIT TABULKU JAKO VÝBĚR (CTAS)
- Načítání dat
- Operace přesunu dat prováděné službou Pro přesun dat (DMS)

> [!NOTE]  
> Příkazy SELECT na zobrazení dynamické správy (DMVs) nebo jiné zobrazení systému se neřídí žádným omezením souběžnosti. Systém můžete sledovat bez ohledu na počet dotazů, které jsou v něm prováděny.
>
>

### <a name="operations-not-governed-by-resource-classes"></a>Operace, které se neřídí třídami prostředků

Některé dotazy vždy spustit ve třídě prostředků smallrc i v případě, že uživatel je členem větší třídy prostředků. Tyto dotazy osvobozeny od daně se nezapočítávají do limitu souběžnosti. Například pokud je limit souběžnosti 16, mnoho uživatelů může vybírat ze systémových zobrazení bez dopadu na dostupné sloty souběžnosti.

Následující příkazy jsou osvobozeny od tříd prostředků a vždy spustit v smallrc:

- VYTVOŘIT nebo PŘETÁHNOUT TABULKU
- ZMĚNIT TABULKU ... PŘEPNOUT, ROZDĚLIT NEBO SLOUČIT ODDÍL
- ZMĚNIT INDEX ZAKÁZAT
- DROP INDEX
- VYTVOŘIT, AKTUALIZOVAT nebo PŘETAŽENÍ STATISTIKY
- Zkrátit tabulku
- ZMĚNIT POVOLENÍ
- VYTVOŘIT PŘIHLÁŠENÍ
- VYTVOŘIT, ZMĚNIT NEBO PŘETAŽENÍ UŽIVATELE
- VYTVOŘIT, ZMĚNIT nebo PŘETAŽENÍ PROCEDURY
- VYTVOŘIT nebo PŘETÁHNOUT POHLED
- VLOŽIT HODNOTY
- SELECT ze systémových zobrazení a dmvs
- Vysvětlit
- Dbcc

<!--
Removed as these two are not confirmed / supported under SQL DW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Sloty souběžnosti

Souběžně sloty jsou pohodlný způsob, jak sledovat prostředky, které jsou k dispozici pro spuštění dotazu. Jsou to jako vstupenky, které si koupíte rezervovat místa na koncertě, protože sezení je omezená. Celkový počet slotů souběžnosti na datový sklad je určen úrovní služby. Před spuštěním dotazu musí být schopen rezervovat dostatek slotů souběžnosti. Po dokončení dotazu uvolní jeho sloty souběžnosti.  

- Dotaz spuštěný s 10 sloty souběžnosti může přistupovat 5krát více výpočetních prostředků než dotaz spuštěný se 2 sloty souběžnosti.
- Pokud každý dotaz vyžaduje 10 slotů souběžnosti a existují 40 sloty souběžnosti, pak pouze 4 dotazy lze spustit současně.

Pouze dotazy řízené prostředky spotřebovávají sloty souběžnosti. Systémové dotazy a některé triviální dotazy nespotřebovávají žádné sloty. Přesný počet spotřebovaných slotů souběžnosti je určen třídou prostředků dotazu.

## <a name="view-the-resource-classes"></a>Zobrazit třídy zdrojů

Třídy prostředků jsou implementovány jako předdefinované databázové role. Existují dva typy tříd prostředků: dynamické a statické. Chcete-li zobrazit seznam tříd prostředků, použijte následující dotaz:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Změna třídy prostředků uživatele

Třídy prostředků jsou implementovány přiřazením uživatelů k databázovým rolím. Když uživatel spustí dotaz, dotaz se spustí s třídou prostředků uživatele. Například pokud je uživatel členem role databáze staticrc10, jejich dotazy spustit s malým množstvím paměti. Pokud je uživatel databáze členem databázových rolí xlargerc nebo staticrc80, jejich dotazy jsou spuštěny s velkým množstvím paměti.

Chcete-li zvýšit třídu prostředků uživatele, použijte [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) k přidání uživatele do databázové role velké třídy prostředků.  Níže uvedený kód přidá uživatele do role větší databáze.  Každý požadavek získá 22 % systémové paměti.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Chcete-li snížit třídu prostředků, použijte [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  Pokud 'loaduser' není členem nebo jiné třídy prostředků, přejdou do výchozí třídy prostředků smallrc s 3 % paměti grant.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Priorita třídy zdroje

Uživatelé mohou být členy více tříd prostředků. Pokud uživatel patří do více než jedné třídy prostředků:

- Dynamické třídy prostředků mají přednost před třídami statických prostředků. Například pokud je uživatel členem mediumrc(dynamic) a staticrc80 (statické), dotazy spustit s mediumrc.
- Větší třídy prostředků mají přednost před menšími třídami prostředků. Například pokud je uživatel členem mediumrc a largerc, dotazy spustit s largerc. Podobně pokud je uživatel členem staticrc20 a statirc80, dotazy spustit s staticrc80 přidělení prostředků.

## <a name="recommendations"></a>Doporučení

>[!NOTE]
>Zvažte využití možností správy úloh[(izolace pracovního vytížení](sql-data-warehouse-workload-isolation.md), [klasifikace](sql-data-warehouse-workload-classification.md) a [důležitost](sql-data-warehouse-workload-importance.md)) pro větší kontrolu nad vaší úlohou a předvídatelný výkon.  
>
>

Doporučujeme vytvořit uživatele, který je vyhrazen pro spuštění určitého typu dotazu nebo operace načtení. Podejte tomuto uživateli trvalou třídu prostředků namísto časté změny třídy prostředků. Statické třídy prostředků poskytují větší celkovou kontrolu nad pracovním vytížením, proto doporučujeme použít statické třídy prostředků před zvážením dynamických tříd prostředků.

### <a name="resource-classes-for-load-users"></a>Třídy prostředků pro uživatele zatížení

`CREATE TABLE`Ve výchozím nastavení používá clusterované indexy columnstore. Komprese dat do indexu columnstore je operace náročná na paměť a tlak paměti může snížit kvalitu indexu. Tlak paměti může vést k nutnosti vyšší třídy prostředků při načítání dat. Chcete-li zajistit, aby zatížení měli dostatek paměti, můžete vytvořit uživatele, který je určen pro spouštění zatížení, a přiřadit jej k vyšší třídě prostředků.

Paměť potřebná k efektivnímu zpracování zatížení závisí na povaze načtené tabulky a velikosti dat. Další informace o požadavcích na paměť naleznete v [tématu Maximalizace kvality skupiny řádků](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Po určení požadavku na paměť zvolte, zda chcete přiřadit uživatele zatížení ke statické nebo dynamické třídě prostředků.

- Třídu statického prostředku použijte v případě, že požadavky na paměť tabulky spadají do určitého rozsahu. Zatížení spustit s příslušnou pamětí. Při škálování datového skladu zatížení nepotřebují více paměti. Pomocí statické třídy prostředků přidělení paměti zůstat konstantní. Tato konzistence šetří paměť a umožňuje souběžné spuštění dalších dotazů. Doporučujeme, aby nová řešení nejprve používala třídy statických prostředků, protože poskytují větší kontrolu.
- Třídu dynamických prostředků použijte v případě, že se požadavky na paměť tabulky značně liší. Zatížení může vyžadovat více paměti, než poskytuje aktuální úroveň DWU nebo cDWU. Škálování datového skladu přidá více paměti pro načtení operací, což umožňuje rychlejší načítání.

### <a name="resource-classes-for-queries"></a>Třídy prostředků pro dotazy

Některé dotazy jsou náročné na výpočetní prostředky a některé ne.  

- Zvolte třídu dynamického prostředku, pokud jsou dotazy složité, ale nepotřebují vysokou souběžnost.  Například generování denních nebo týdenních sestav je příležitostnou potřebou zdrojů. Pokud sestavy zpracovávají velké množství dat, škálování datového skladu poskytuje více paměti pro existující třídu prostředků uživatele.
- Zvolte třídu statického prostředku, pokud se očekávání zdrojů během dne liší. Například statická třída prostředků funguje dobře, když je datový sklad dotazován mnoha lidmi. Při škálování datového skladu se velikost paměti přidělené uživateli nezmění. V důsledku toho další dotazy mohou být provedeny paralelně v systému.

Správné paměti granty závisí na mnoha faktorech, jako je například množství dotazovaných dat, povaha schémat tabulky a různá spojení, výběr a skupiny predikáty. Obecně přidělení více paměti umožňuje dotazy k dokončení rychleji, ale snižuje celkovou souběžnost. Pokud souběžnost není problém, přerozdělování paměti nepoškozuje propustnost.

Chcete-li optimalizovat výkon, použijte různé třídy prostředků. V další části poskytuje uloženou proceduru, která vám pomůže zjistit nejlepší třídu prostředků.

## <a name="example-code-for-finding-the-best-resource-class"></a>Příklad kódu pro nalezení nejlepší třídy prostředků

Následující zadanou uloženou proceduru můžete použít k zjištění souběžnosti a udělení paměti pro třídu prostředků v daném slo a nejlepší třídu prostředků pro operace CCI náročné na paměť v tabulce CCI bez oddílů v dané třídě prostředků:

Zde je účel této uložené procedury:

1. Chcete-li zobrazit souběžnost a udělení paměti pro třídu prostředku v daném SLO. Uživatel musí poskytnout hodnotu NULL pro schéma i název tabulky, jak je znázorněno v tomto příkladu.  
2. Chcete-li zobrazit nejlepší třídu prostředků pro operace CCI náročné na paměť (zatížení, kopírování tabulky, znovu sestavit index atd.) v tabulce CCI bez oddílů v dané třídě prostředků. Uložené proc používá schéma tabulky zjistit požadované paměti grant.

### <a name="dependencies--restrictions"></a>Závislosti & omezení

- Tato uložená procedura není určena k výpočtu požadavku na paměť pro tabulku cci s oddíly.
- Tato uložená procedura nebere v úvahu požadavky na paměť pro část SELECT CTAS/INSERT-SELECT a předpokládá, že je SELECT.
- Tato uložená procedura používá dočasnou tabulku, která je k dispozici v relaci, kde byla vytvořena tato uložená procedura.
- Tato uložená procedura závisí na aktuální nabídky (například konfigurace hardwaru, Konfigurace DMS), a pokud některý z těchto změn pak to uložené proc nebude fungovat správně.  
- Tato uložená procedura závisí na existující nabídky omezení souběžnosti a pokud tyto změny pak tato uložená procedura nebude fungovat správně.  
- Tato uložená procedura závisí na existující nabídky třídy prostředků a pokud tyto změny pak tato uložená procedura nebude fungovat správně.  

>[!NOTE]  
>Pokud nejste získání výstupu po provedení uložené procedury s parametry k dispozici, pak může být dva případy.
>
>1. Buď parametr DW obsahuje neplatnou hodnotu SLO
>2. Nebo neexistuje žádná odpovídající třída prostředků pro operaci CCI v tabulce.
>
>Například na DW100c nejvyšší paměti grant k dispozici je 1 GB a pokud schéma tabulky je dostatečně široká, aby přes požadavek 1 GB.

### <a name="usage-example"></a>Příklad použití

Syntaxe:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU:Buď zadejte parametr NULL pro extrahování aktuálního DWU z DW DB, nebo uveďte jakékoli podporované DWU ve formě "DW100c"
2. @SCHEMA_NAME:Zadejte název schématu tabulky.
3. @TABLE_NAME:Zadejte název tabulky úroku.

Příklady provádění tohoto uloženého proc:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

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

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500 
  ELSE Mem*100 
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
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
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
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
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>Další kroky

Další informace o správě uživatelů databáze a zabezpečení naleznete [v tématu Zabezpečení databáze v synapse SQL](sql-data-warehouse-overview-manage-security.md). Další informace o tom, jak větší třídy prostředků může zlepšit kvalitu indexu clusterované columnstore, naleznete v [tématu Optimalizace paměti pro kompresi columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

