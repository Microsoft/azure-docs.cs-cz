---
title: Vytvoření náhradních klíčů pomocí identity
description: Doporučení a příklady pro použití identity vlastnost vytvořit náhradní klíče na tabulky v synapse fondu SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: d4a9880ed7ab26d0127026f49c0bc781cfc2a941
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586338"
---
# <a name="using-identity-to-create-surrogate-keys-in-synapse-sql-pool"></a>Použití identity k vytvoření náhradních klíčů ve fondu SYNAPse SQL

Doporučení a příklady pro použití identity vlastnost vytvořit náhradní klíče na tabulky v synapse fondu SQL.

## <a name="what-is-a-surrogate-key"></a>Co je náhradní klíč

Náhradní klíč v tabulce je sloupec s jedinečným identifikátorem pro každý řádek. Klíč není generován z dat tabulky. Modelátoři dat rádi vytvářejí náhradní klíče ve svých tabulkách při navrhování modelů datového skladu. Vlastnost IDENTITY můžete použít k dosažení tohoto cíle jednoduše a efektivně bez ovlivnění výkonu zatížení.  

## <a name="creating-a-table-with-an-identity-column"></a>Vytvoření tabulky se sloupcem IDENTITY

Vlastnost IDENTITY je navržena tak, aby škálování ve všech distribucích ve fondu SYNApse SQL bez ovlivnění výkonu zatížení. Proto je implementace IDENTITY zaměřena na dosažení těchto cílů.

Tabulku můžete definovat jako tabulku s vlastností IDENTITY při prvním vytvoření tabulky pomocí syntaxe, která je podobná následujícímu příkazu:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Potom můžete `INSERT..SELECT` použít k naplnění tabulky.

Tato zbývající část této části zdůrazňuje nuance implementace, která vám pomůže lépe porozumět.  

### <a name="allocation-of-values"></a>Rozdělení hodnot

Identity Vlastnost nezaručuje pořadí, ve kterém jsou přiděleny náhradní hodnoty, což odráží chování SQL Server a Azure SQL Database. Však v synapse fondu SQL absence záruky je výraznější.

Následující příklad je znázorněn na obrázku:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
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

V předchozím příkladu dva řádky přistál v distribuci 1. První řádek má náhradní hodnotu `C1`1 ve sloupci a druhý řádek má náhradní hodnotu 61. Obě tyto hodnoty byly generovány vlastností IDENTITY. Přidělení hodnot však není souvislé. Toto chování je záměrné.

### <a name="skewed-data"></a>Zkosená data

Rozsah hodnot pro datový typ jsou rovnoměrně rozloženy napříč distribucemi. Pokud distribuovaná tabulka trpí zkosená data, rozsah hodnot, které jsou k dispozici pro datový typ může být vyčerpán předčasně. Například pokud všechna data skončí v jedné distribuci, pak efektivně tabulka má přístup pouze k jedné šedesátiny hodnot datového typu. Z tohoto důvodu identity vlastnost `INT` je `BIGINT` omezena na a pouze datové typy.

### <a name="selectinto"></a>Vyberte.. Do

Pokud je do nové tabulky vybrán existující sloupec IDENTITY, nový sloupec zdědí vlastnost IDENTITY, pokud není splněna jedna z následujících podmínek:

- Příkaz SELECT obsahuje spojení.
- Více SELECT příkazy jsou spojeny pomocí UNION.
- Sloupec IDENTITY je uveden více než jednou v seznamu SELECT.
- Sloupec IDENTITY je součástí výrazu.

Pokud některá z těchto podmínek je splněna, sloupec je vytvořen NENÍ NULL namísto dědění identity vlastnost.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

VYTVOŘIT TABULKU JAKO SELECT (CTAS) následuje stejné chování serveru SQL Server, který je dokumentován pro SELECT.. Do. V definici sloupce `CREATE TABLE` části příkazu však nelze zadat vlastnost IDENTITY. Také nelze použít funkci IDENTITY v `SELECT` části CTAS. Chcete-li naplnit tabulku, `CREATE TABLE` je třeba použít `INSERT..SELECT` k definování tabulky následované k naplnění.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Explicitní vkládání hodnot do sloupce IDENTITY

Synapse SQL `SET IDENTITY_INSERT <your table> ON|OFF` fond podporuje syntaxi. Tuto syntaxi můžete použít k explicitnímu vložení hodnot do sloupce IDENTITY.

Mnoho datových modelátorů rádo používá předdefinované záporné hodnoty pro určité řádky v jejich dimenzích. Příkladem je řádek -1 nebo "neznámý člen".

Další skript ukazuje, jak explicitně přidat tento řádek pomocí SET IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT     *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Načítání dat

Přítomnost identity vlastnost má některé důsledky pro váš kód načítání dat. Tato část zvýrazňuje některé základní vzory pro načítání dat do tabulek pomocí identity.

Chcete-li načíst data do tabulky a vygenerovat náhradní klíč pomocí identity, vytvořte tabulku a potom použijte INSERT.. SELECT nebo INSERT.. HODNOTY k provedení zatížení.

Následující příklad zvýrazní základní vzorek:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Není možné použít `CREATE TABLE AS SELECT` aktuálně při načítání dat do tabulky se sloupcem IDENTITY.
>

Další informace o načítání dat naleznete v [tématu Návrh extrahování, načtení a transformace (ELT) pro zdroj ového fondu SQL](design-elt-data-loading.md) a [načtení osvědčených postupů](guidance-for-loading-data.md).

## <a name="system-views"></a>Systémová zobrazení

Zobrazení katalogu [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) můžete použít k identifikaci sloupce, který má vlastnost IDENTITY.

Chcete-li lépe porozumět schématu databáze, tento příklad ukazuje, jak integrovat sys.identity_column' s jinými zobrazeníkatalogu systému:

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

Vlastnost IDENTITY nelze použít:

- Pokud datový typ sloupce není INT nebo BIGINT
- Pokud je sloupec také distribučním klíčem
- Pokud je tabulka externí tabulkou

Následující související funkce nejsou podporovány ve fondu SYNAPSE SQL:

- [IDENTITA()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [Scope_identity](/sql/t-sql/functions/scope-identity-transact-sql)
- [Ident_current](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Běžné úkoly

Tato část obsahuje některé ukázkový kód, který můžete použít k provádění běžných úkolů při práci se sloupci IDENTITY.

Sloupec C1 je identita ve všech následujících úkolech.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Vyhledání nejvyšší přidělené hodnoty pro tabulku

Pomocí `MAX()` funkce určete nejvyšší hodnotu přidělenou pro distribuovanou tabulku:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Najít osivo a přírůstek pro vlastnost IDENTITY

Pomocí zobrazení katalogu můžete zjistit přírůstky identity a hodnoty konfigurace osiva pro tabulku pomocí následujícího dotazu:

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

## <a name="next-steps"></a>Další kroky

- [Přehled tabulky](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [VYTVOŘIT TABULKU (Transact-SQL) IDENTITY (vlastnost)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [KONTROLNÍ OSAVKA DBCC](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)
