---
title: Třídy prostředků pro správu úloh
description: Pokyny pro použití tříd prostředků ke správě souběžných a výpočetních prostředků pro dotazy ve službě Azure synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7c3793daa820d0cb5b5b6900402704756f206425
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488384"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Správa úloh pomocí tříd prostředků v Azure synapse Analytics

Pokyny pro použití tříd prostředků ke správě paměti a souběžnosti Synapsech dotazů fondu SQL v Azure synapse.  

## <a name="what-are-resource-classes"></a>Co jsou třídy prostředků

Kapacita výkonu dotazu je určena třídou prostředků uživatele.  Třídy prostředků jsou předem určené limity prostředků v synapse fondu SQL, které řídí výpočetní prostředky a souběžnost pro provádění dotazů. Třídy prostředků vám pomůžou nakonfigurovat prostředky pro vaše dotazy nastavením omezení počtu dotazů, které běží souběžně, a výpočetních prostředků přiřazených každému dotazu.  Existuje kompromis mezi pamětí a souběžně.

- Menší třídy prostředků omezují maximální velikost paměti na jeden dotaz, ale zvyšují souběžnost.
- Větší třídy prostředků zvyšují maximální velikost paměti na jeden dotaz, ale omezují souběžnost.

Existují dva typy tříd prostředků:

- Třídy statických prostředků, které jsou vhodné pro zvýšení souběžnosti na velikost sady dat, která je pevná.
- Dynamické třídy prostředků, které jsou vhodné pro sady dat, které mají větší velikost a vyžadují zvýšený výkon, protože úroveň služby se škáluje.

Třídy prostředků používají pro měření spotřeby prostředků sloty souběžnosti.  [Sloty souběžnosti](#concurrency-slots) jsou vysvětleny dále v tomto článku.

- Chcete-li zobrazit využití prostředků pro třídy prostředků, přečtěte si téma [limity paměti a souběžnosti](memory-concurrency-limits.md).
- Chcete-li upravit třídu prostředků, můžete spustit dotaz pod jiným uživatelem nebo změnit členství ve [třídě prostředku aktuálního uživatele](#change-a-users-resource-class) .

### <a name="static-resource-classes"></a>Třídy statických prostředků

Statické třídy prostředků přidělují stejnou velikost paměti bez ohledu na aktuální úroveň výkonu, která se měří v [jednotkách datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md). Vzhledem k tomu, že dotazy získají stejné přidělení paměti bez ohledu na úroveň výkonu, [škálování datového skladu](quickstart-scale-compute-portal.md) umožňuje spuštění více dotazů v rámci třídy prostředků.  Statické třídy prostředků jsou ideální, pokud je datový svazek známý a konstantní.

Třídy statických prostředků jsou implementovány s těmito předem definovanými databázovými rolemi:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Dynamické třídy prostředků

Dynamické třídy prostředků přidělují proměnlivou velikost paměti v závislosti na aktuální úrovni služby. I když jsou statické třídy prostředků užitečné pro vyšší souběžné a statické objemy dat, jsou dynamické třídy prostředků lépe vhodné pro rostoucí nebo proměnlivé množství dat.  Při horizontálním navýšení kapacity na větší úroveň služby budou dotazy automaticky mít více paměti.  

Dynamické třídy prostředků jsou implementovány s těmito předem definovanými databázovými rolemi:

- smallrc
- mediumrc
- largerc
- xlargerc

Přidělení paměti pro jednotlivé třídy prostředků je následující.

| Úroveň služeb  | smallrc           | mediumrc               | largerc                | xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25%               | 25%                    | 25%                    | 70 %                    |
| DW200c         | 12,5%             | 12,5%                  | 22                    | 70 %                    |
| DW300c         | 8 %                | 10 %                    | 22                    | 70 %                    |
| DW400c         | 6,25%             | 10 %                    | 22                    | 70 %                    |
| DW500c.         | 5 %                | 10 %                    | 22                    | 70 %                    |
| DW1000c na<br> DW30000c | 3 %       | 10 %                    | 22                    | 70 %                    |

### <a name="default-resource-class"></a>Výchozí třída prostředků

Ve výchozím nastavení je každý uživatel členem dynamické třídy prostředků **smallrc**.

Třída prostředků správce služby je pevně nastavená na smallrc a nedá se změnit.  Správce služby je uživatel vytvořený během procesu zřizování.  Správce služby v tomto kontextu je přihlašovací jméno zadané pro přihlašovací jméno správce serveru při vytváření nového synapse fondu SQL pomocí nového serveru.

> [!NOTE]
> Uživatelé nebo skupiny, kteří jsou definováni jako správce služby Active Directory, jsou také správci služeb.
>
>

## <a name="resource-class-operations"></a>Operace třídy prostředku

Třídy prostředků jsou navržené tak, aby vylepšily výkon pro aktivity správy a manipulace s daty. Ke složitým dotazům může také doběžet v rámci velké třídy prostředků. Například výkon dotazů pro velké spojení a seřazení může zlepšit, pokud je třída prostředků dostatečně velká, aby mohl dotaz provádět v paměti.

### <a name="operations-governed-by-resource-classes"></a>Operace, které se řídí třídami prostředků

Tyto operace se řídí třídami prostředků:

- VLOŽIT – VYBRAT, AKTUALIZOVAT, ODSTRANIT
- VYBRAT (při dotazování na tabulky uživatelů)
- ZMĚNIT INDEX – znovu sestavit nebo reorganizovat
- ZMĚNIT OPĚTOVNÉ SESTAVENÍ TABULKY
- CREATE INDEX
- VYTVOŘIT CLUSTEROVANÝ INDEX COLUMNSTORE
- CREATE TABLE JAKO SELECT (CTAS)
- Načítání dat
- Operace přesunu dat prováděné službou pro přesun dat (DMS)

> [!NOTE]  
> Příkazy SELECT pro zobrazení dynamické správy (zobrazení dynamické správy) nebo jiná systémová zobrazení se neřídí žádnou z omezení souběžnosti. Systém můžete monitorovat bez ohledu na počet prováděných dotazů.
>
>

### <a name="operations-not-governed-by-resource-classes"></a>Operace, na které se neřídí třídy prostředků

Některé dotazy se vždy spouštějí ve třídě prostředků smallrc, i když je uživatel členem větší třídy prostředků. Tyto vyloučené dotazy nepočítají do limitu souběžnosti. Například pokud je limit souběžnosti 16, mnoho uživatelů může vybírat ze systémových zobrazení, aniž by to ovlivnilo dostupné sloty souběžnosti.

Následující příkazy jsou vyjmuty z tříd prostředků a vždy se spouštějí v smallrc:

- VYTVOŘIT nebo vyřadit tabulku
- ZMĚNIT TABULKU... PŘEPNOUT, rozdělit nebo sloučit oddíl
- ALTER INDEX DISABLE
- DROP INDEX
- Vytvoření, aktualizace nebo vyřazení statistiky
- TRUNCATE TABLE
- ZMĚNIT AUTORIZACI
- VYTVOŘIT PŘIHLAŠOVACÍ ÚDAJE
- Vytvoření, změna nebo vyřazení uživatele
- VYTVOŘIT, změnit nebo odstranit PROCEDURu
- VYTVOŘIT nebo vyřadit zobrazení
- VLOŽIT HODNOTY
- VYBRAT ze systémových zobrazení a zobrazení dynamické správy
- ČÁSTECH
- NÁSTROJI

<!--
Removed as these two are not confirmed / supported under Azure Synapse Analytics
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Sloty souběžnosti

Sloty souběžnosti představují pohodlný způsob, jak sledovat prostředky dostupné pro provádění dotazů. Jsou to jako lístky, které jste si koupili při vzájemné rezervaci míst, protože je k dispozici pouze pracovní postup. Celkový počet slotů souběžnosti na datový sklad závisí na úrovni služby. Než bude možné spustit dotaz, musí být schopen vyhradit dostatek slotů souběžnosti. Po dokončení dotazu uvolňuje své sloty souběžnosti.  

- Dotaz, který běží s 10 sloty souběžnosti, může mít za přístup k většímu počtu výpočetních prostředků než dotaz spuštěný se 2 sloty souběžnosti.
- Pokud každý dotaz vyžaduje 10 slotů souběžnosti a máte 40 slotů souběžnosti, může být souběžně spuštěno pouze 4 dotazy.

Jenom dotazy s řízenou prostředky spotřebovávají sloty souběžnosti. Systémové dotazy a některé triviální dotazy nevyužívají žádné sloty. Přesný počet spotřebovaných slotů souběžnosti je určen třídou prostředku dotazu.

## <a name="view-the-resource-classes"></a>Zobrazení tříd prostředků

Třídy prostředků jsou implementovány jako předem definované databázové role. Existují dva typy tříd prostředků: dynamická a statická. Chcete-li zobrazit seznam tříd prostředků, použijte následující dotaz:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Změna třídy prostředků uživatele

Třídy prostředků jsou implementovány přiřazením uživatelů k databázovým rolím. Když uživatel spustí dotaz, dotaz se spustí s třídou prostředků uživatele. Například pokud je uživatel členem role databáze staticrc10, jejich dotazy se spouštějí s malým množstvím paměti. Pokud je uživatel databáze členem rolí databáze xlargerc nebo staticrc80, jejich dotazy se spouštějí s velkým množstvím paměti.

Chcete-li zvýšit třídu prostředků uživatele, použijte [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) k přidání uživatele do databázové role velké třídy prostředků.  Níže uvedený kód přidá uživatele do role databáze largerc.  Každý požadavek získá 22% systémové paměti.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Chcete-li snížit třídu prostředků, použijte [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Pokud ' loaduser ' není člen nebo žádné jiné třídy prostředků, přejde do výchozí třídy prostředků smallrc s přidělením 3% paměti.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Priorita třídy prostředků

Uživatelé můžou být členy více tříd prostředků. Když uživatel patří do více než jedné třídy prostředku:

- Třídy dynamických prostředků mají přednost před třídami statických prostředků. Například pokud je uživatel členem obou mediumrc (Dynamic) i staticrc80 (static), dotazy se spouštějí s mediumrc.
- Větší třídy prostředků mají přednost před menšími třídami prostředků. Například pokud je uživatel členem služby mediumrc a largerc, dotazy se spouštějí s largerc. Podobně platí, že pokud je uživatel členem obou staticrc20 i statirc80, dotazy se spouštějí s přidělením prostředků staticrc80.

## <a name="recommendations"></a>Doporučení

>[!NOTE]
>Zvažte využití možností správy úloh ([izolace úloh](sql-data-warehouse-workload-isolation.md), [klasifikace](sql-data-warehouse-workload-classification.md) a [důležitost](sql-data-warehouse-workload-importance.md)) pro lepší kontrolu nad úlohou a předvídatelným výkonem.  
>
>

Doporučujeme vytvořit uživatele, který je vyhrazený pro spuštění konkrétního typu dotazu nebo operace načtení. Poskytněte tomuto uživateli trvalou třídu prostředků namísto časté změny třídy prostředků. Statické třídy prostředků poskytují větší celkovou kontrolu nad úlohou, takže před zvážením dynamických tříd prostředků doporučujeme použít statické třídy prostředků.

### <a name="resource-classes-for-load-users"></a>Třídy prostředků pro načtení uživatelů

`CREATE TABLE` používá ve výchozím nastavení clusterované indexy columnstore. Komprimace dat do indexu columnstore je operace náročná na paměť a zatížení paměti může snížit kvalitu indexu. Tlak paměti může vést k nutnosti větší třídy prostředků při načítání dat. Aby bylo zajištěno, že zatížení bude mít dostatek paměti, můžete vytvořit uživatele, který je určen ke spouštění zátěže, a přiřadit tohoto uživatele k vyšší třídě prostředků.

Paměť potřebná ke zpracování zatížení efektivně závisí na povaze načtené tabulky a velikosti dat. Další informace o požadavcích na paměť najdete v tématu [maximalizace kvality skupiny řádků](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Jakmile určíte požadavek na paměť, určete, jestli se má uživatel pro načtení přiřadit ke statické nebo dynamické třídě prostředků.

- Použijte statickou třídu prostředků, pokud požadavky na paměť tabulky spadají do určitého rozsahu. Zatížení se spouští s vhodnou pamětí. Při škálování datového skladu nemusí zatížení potřebovat více paměti. Když použijete statickou třídu prostředků, přidělení paměti zůstane konstantní. Tato konzistence zachovává paměť a umožňuje souběžné spouštění dalších dotazů. Doporučujeme, aby nová řešení používala statické třídy prostředků nejprve, protože poskytují větší kontrolu.
- Použijte dynamickou třídu prostředků, pokud se požadavky na paměť v tabulce značně liší. Načtení může vyžadovat více paměti, než je aktuální úroveň DWU nebo cDWU. Škálování datového skladu přičítá více paměti pro operace načítání, což umožňuje rychlejší provádění zátěže.

### <a name="resource-classes-for-queries"></a>Třídy prostředků pro dotazy

Některé dotazy jsou náročné na výpočetní výkon a některé ne.  

- Vyberte dynamickou třídu prostředků, když jsou dotazy složité, ale nepotřebují vysokou souběžnost.  Například generování denních nebo týdenních sestav je příležitostná nutnost pro prostředky. Pokud sestavy zpracovávají velké objemy dat, škálování datového skladu poskytuje větší množství paměti pro stávající třídu prostředků uživatele.
- Vyberte statickou třídu prostředků, pokud se očekávání prostředků mění v průběhu dne. Například třída statických prostředků funguje dobře, když je datový sklad dotazován mnoha lidmi. Při škálování datového skladu se množství paměti přidělené uživateli nezmění. V důsledku toho je možné spustit více dotazů paralelně na systému.

Náležité nároky na paměť jsou závislé na mnoha faktorech, například na množství dotazovaných dat, na povaze schémat tabulek a v různých predikátech spojení, Select a Group. Obecně platí, že přidělování více paměti umožňuje rychlejší dokončení dotazů, ale snižuje celkovou souběžnost. Pokud souběžnost není problém, přetížení paměti nepoškozuje propustnost.

Pro optimalizaci výkonu použijte různé třídy prostředků. Další část poskytuje uloženou proceduru, která vám pomůže zjistit nejlepší třídu prostředků.

## <a name="example-code-for-finding-the-best-resource-class"></a>Ukázkový kód pro vyhledání nejlepší třídy prostředku

Následující uloženou proceduru můžete použít k určení souběžnosti a přidělení paměti na třídu prostředků v dané třídě SLO a nejlepší třídy prostředků pro operace v rámci Ski v nerozdělené paměti v dané třídě prostředků:

Tady je účel této uložené procedury:

1. Zobrazení souběžnosti a přidělení paměti na třídu prostředků na daném objektu SLO. Uživatel musí zadat hodnotu NULL pro schéma i TableName, jak je znázorněno v tomto příkladu.  
2. Chcete-li zobrazit nejlepší třídu prostředků pro operace KONZULÁRNÍch operací náročné na paměť (načítání, kopírování tabulky, index opětovného sestavení atd.) v nerozdělené tabulce INSTRUKCí v dané třídě prostředků. Uložená procedura používá schéma tabulky k zjištění požadovaného přidělení paměti.

### <a name="dependencies--restrictions"></a>Závislosti & omezení

- Tato uložená procedura není navržená tak, aby počítala požadavky na paměť pro dělenou tabulku instrukcí.
- Tato uložená procedura nebere v úvahu požadavky na paměť pro vybranou část CTAS/INSERT-SELECT a předpokládá, že se jedná o výběr.
- Tato uložená procedura používá dočasnou tabulku, která je k dispozici v relaci, kde byla tato uložená procedura vytvořena.
- Tato uložená procedura závisí na aktuálních nabídkách (například hardwarová konfigurace, DMS config) a pokud nějaká z nich se změní, tato uložená procedura nebude správně fungovat.  
- Tato uložená procedura závisí na existující nabídce omezení souběžnosti a pokud se tyto změny změní, tato uložená procedura nebude fungovat správně.  
- Tato uložená procedura závisí na existující nabídce tříd prostředků a pokud se tyto změny změní, tato uložená procedura nebude fungovat správně.  

>[!NOTE]  
>Pokud nezískáváte výstup po provedení uložené procedury se zadanými parametry, mohou být dva případy.
>
>1. Buď parametr DW obsahuje neplatnou hodnotu SLO.
>2. Nebo neexistuje žádná vyhovující Třída prostředků pro operaci Ski v tabulce.
>
>Například na adrese DW100c je k dispozici nejvyšší povolená velikost paměti 1 GB, a pokud je schéma tabulky dostačující pro více požadavků 1 GB.

### <a name="usage-example"></a>Příklad použití

Syntaxe:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: Zadejte parametr s hodnotou NULL pro extrakci aktuálního DWU z databáze DW nebo poskytněte jakékoli podporované DWU ve formátu ' DW100c '.
2. @SCHEMA_NAME: Zadejte název schématu pro tabulku.
3. @TABLE_NAME: Zadejte název tabulky zájmu.

Příklady spouštění této uložené procedury:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Následující příkaz vytvoří Tabulka1, který je použit v předchozích příkladech.
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

Další informace o správě uživatelů a zabezpečení databáze najdete v tématu [zabezpečení databáze v synapse SQL](sql-data-warehouse-overview-manage-security.md). Další informace o tom, jak můžou větší třídy prostředků zlepšit kvalitu clusterovaných indexů columnstore, najdete v tématu [optimalizace paměti pro kompresi columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).
