---
title: Použití IDENTITY k vytváření náhradních klíčů – Azure SQL Data Warehouse | Microsoft Docs
description: Doporučení a příklady použití vlastnosti IDENTITY k vytváření náhradních klíčů v tabulkách v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 4c65bf7cc8edfa246508bb22001aed40c34414f3
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515595"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Použití IDENTITY k vytváření náhradních klíčů v Azure SQL Data Warehouse

Doporučení a příklady použití vlastnosti IDENTITY k vytváření náhradních klíčů v tabulkách v Azure SQL Data Warehouse.

## <a name="what-is-a-surrogate-key"></a>Co je náhradní klíč

Náhradní klíč v tabulce je sloupec s jedinečným identifikátorem pro každý řádek. Klíč se negeneruje z dat tabulky. Datové modely, jako je vytváření náhradních klíčů ve svých tabulkách při návrhu modelů datového skladu. Vlastnost IDENTITY můžete použít k dosažení tohoto cíle jednoduše a efektivně, aniž by to ovlivnilo výkon zatížení.  

## <a name="creating-a-table-with-an-identity-column"></a>Vytvoření tabulky se sloupcem IDENTITY

Vlastnost IDENTITY je navržena pro horizontální navýšení kapacity napříč všemi distribucemi v datovém skladu, aniž by to ovlivnilo výkon zatížení. Proto se implementace IDENTITY orientuje směrem k dosažení těchto cílů.

Můžete definovat tabulku, která má vlastnost IDENTITY při prvním vytvoření tabulky pomocí syntaxe, která je podobná následujícímu příkazu:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Pak můžete použít `INSERT..SELECT` k naplnění tabulky.

Tato zbývající část této části popisuje drobné odlišnosti implementace, která vám pomůže s jejich úplným pochopením.  

### <a name="allocation-of-values"></a>Přidělení hodnot

Vlastnost IDENTITY nezaručuje pořadí, ve kterém jsou přiděleny náhradní hodnoty, což odráží chování SQL Server a Azure SQL Database. V Azure SQL Data Warehouse ale neexistence záruky je vyslovější.

Následující příklad je ilustrace:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

V předchozím příkladu byly vyloženo dva řádky v distribuci 1. První řádek má hodnotu náhrady 1 ve sloupci `C1`a druhý řádek má náhradní hodnotu 61. Obě tyto hodnoty byly vygenerovány vlastností IDENTITY. Přidělení hodnot však není souvislé. Toto chování je záměrné.

### <a name="skewed-data"></a>Zkosená data

Rozsah hodnot datového typu se rovnoměrně rozprostře napříč distribucí. Pokud bude distribuovaná tabulka z rozdělených dat vyčerpána, může být rozsah hodnot dostupných pro datový typ předčasně vyčerpán. Například pokud všechna data skončí v rámci jedné distribuce, pak efektivně má tabulka přístup pouze k sixtieth hodnot datového typu. Z tohoto důvodu je vlastnost identity omezená jenom na `INT` `BIGINT` datové typy.

### <a name="selectinto"></a>VYBERTE.. USKLADNĚN

Když je sloupec existující IDENTITY vybraný do nové tabulky, zdědí nový sloupec vlastnost IDENTITY, pokud není splněná některá z následujících podmínek:

- Příkaz SELECT obsahuje spojení.
- Vícenásobné příkazy SELECT jsou spojeny pomocí SJEDNOCENí.
- Sloupec IDENTITY je v seznamu pro výběr uveden ve více než jednom okamžiku.
- Sloupec IDENTITY je součástí výrazu.

Pokud je jedna z těchto podmínek pravdivá, je sloupec vytvořen bez hodnoty NULL namísto dědění vlastnosti IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE JAKO VÝBĚR

CREATE TABLE AS SELECT (CTAS) se řídí stejným chováním SQL Server, které je popsané pro možnost vybrat. Uskladněn. V definici `CREATE TABLE` sloupce části příkazu ale nemůžete zadat vlastnost identity. V `SELECT` části CTAS také nemůžete použít funkci identita. K naplnění tabulky je nutné použít `CREATE TABLE` k definování tabulky, `INSERT..SELECT` za kterou se má naplnit.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Explicitní vkládání hodnot do sloupce IDENTITY

SQL Data Warehouse podporuje `SET IDENTITY_INSERT <your table> ON|OFF` syntaxi. Tuto syntaxi můžete použít k explicitnímu vkládání hodnot do sloupce IDENTITY.

Řada datových modelů, jako je například použití předdefinovaných záporných hodnot pro určité řádky v jejich dimenzích. Příkladem je řádek-1 nebo "Neznámý člen".

Následující skript ukazuje, jak tento řádek explicitně přidat pomocí SET IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Načítají se data

Přítomnost vlastnosti IDENTITY má nějaký vliv na váš kód pro načítání dat. Tato část popisuje několik základních vzorů pro načítání dat do tabulek pomocí IDENTITY.

Chcete-li načíst data do tabulky a vygenerovat náhradní klíč pomocí IDENTITY, vytvořte tabulku a pak použijte příkaz INSERT.. Vyberte nebo vložte... HODNOTY pro provedení zátěže.

Následující příklad zvýrazní základní vzor:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> V tuto chvíli není možné použít `CREATE TABLE AS SELECT` při načítání dat do tabulky se sloupcem identity.
>

Další informace o načítání dat najdete v článku [Návrh extrakce, načítání a transformace (ELT) pro Azure SQL Data Warehouse](design-elt-data-loading.md) a [načítání osvědčených postupů](guidance-for-loading-data.md).

## <a name="system-views"></a>Systémová zobrazení

Můžete použít zobrazení katalogu [Sys. identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) k identifikaci sloupce, který má vlastnost identity.

V tomto příkladu, který vám pomůže lépe pochopit schéma databáze, tento příklad ukazuje, jak integrovat sys. identity_column s dalšími zobrazeními systémového katalogu:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Omezení

Vlastnost IDENTITY se nedá použít:

- Když datový typ sloupce není INT nebo BIGINT
- Pokud je sloupec také distribučním klíčem
- Když je tabulka externí tabulkou

Následující související funkce nejsou v SQL Data Warehouse podporovány:

- [IDENTITA ()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Běžné úkoly

V této části najdete ukázkový kód, který můžete použít k provádění běžných úkolů při práci se sloupci IDENTITY.

Sloupec C1 je identita ve všech následujících úlohách.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Vyhledání nejvyšší přidělené hodnoty pro tabulku

`MAX()` Pomocí funkce určete nejvyšší přidělenou hodnotu pro distribuovanou tabulku:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Vyhledání počáteční hodnoty a přírůstku vlastnosti IDENTITY

Zobrazení katalogu můžete použít ke zjištění zvýšení identity a hodnot konfigurace počáteční hodnoty pro tabulku pomocí následujícího dotazu:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Další postup

- [Přehled tabulek](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Identita CREATE TABLE (Transact-SQL) (vlastnost)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)
