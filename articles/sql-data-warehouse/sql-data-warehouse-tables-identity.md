---
title: Pomocí IDENTITY k vytváření klíčů náhradní – Azure SQL Data Warehouse | Microsoft Docs
description: Doporučení a příklady pro vytvoření náhradní klíče pro tabulky v Azure SQL Data Warehouse pomocí vlastnost IDENTITY.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: ab028705f5af7c37017d2e697240b7d3436f5f71
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Pomocí IDENTITY k vytváření klíčů náhradní v Azure SQL Data Warehouse
Doporučení a příklady pro vytvoření náhradní klíče pro tabulky v Azure SQL Data Warehouse pomocí vlastnost IDENTITY.

## <a name="what-is-a-surrogate-key"></a>Co je náhradní klíč?
Náhradní klíč v tabulce je sloupec s jedinečným identifikátorem pro každý řádek. Klíč Binder negeneruje z dat v tabulce. Modelování dat, například k vytváření klíčů náhradní na jejich tabulky při návrhu modely datového skladu. Vlastnost IDENTITY můžete použít k dosažení tohoto cíle jednoduše a efektivně, aniž by to ovlivnilo zatížení výkonu.  

## <a name="creating-a-table-with-an-identity-column"></a>Vytvoření tabulky se sloupcem IDENTITY
Vlastnost IDENTITY je určena pro škálovat napříč všechny distribuce v datovém skladu, aniž by to ovlivnilo zatížení výkonu. Implementace IDENTITY je proto orientované na dosažení těchto cílů. 

Můžete definovat tabulku tak, že má vlastnost IDENTITY při prvním vytvoření tabulky pomocí syntaxe, který je podobný následující příkaz:

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

Pak můžete použít `INSERT..SELECT` k vyplnění tabulky.

Tento zbytek této části jsou zdůrazněné drobné odlišnosti ve implementaci vám pomohou pochopit je podrobněji.  

### <a name="allocation-of-values"></a>Přidělování hodnot
Vlastnost IDENTITY nezaručuje pořadí, ve kterém jsou přiděleny náhradní hodnoty, které odráží chování systému SQL Server a databáze SQL Azure. Ale v Azure SQL Data Warehouse absenci záruku je mnohem výraznější. 

V následujícím příkladu je obrázek:

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

V předchozím příkladu dva řádky dostali distribuční 1. První řádek má náhradní hodnotu 1 ve sloupci `C1`, a druhý řádek obsahuje náhradní hodnota 61. Obě tyto hodnoty byly vygenerovány vlastnost IDENTITY. Přidělení hodnoty však není souvislé. Toto chování je záměrné.

### <a name="skewed-data"></a>Zkreslilo dat 
Rozsah hodnot pro datový typ jsou rovnoměrně rozloženy distribuce. Pokud se vyskytne distribuované tabulku z zkreslilo dat, můžete rozsah hodnot, které jsou k dispozici pro datový typ vyčerpán předčasně. Například pokud všechna data skončilo v jednom distribučním, pak efektivně tabulka má přístup k jedné pouze šedesátině hodnot datového typu. Z tohoto důvodu je omezena na vlastnost IDENTITY `INT` a `BIGINT` datové typy jenom.

### <a name="selectinto"></a>VYBERTE... DO
Pokud do nové tabulky je vybraná jako existující sloupec IDENTITY, nového sloupce, který dědí vlastnost Identita, pokud je splněna jedna z následujících podmínek:
- Příkaz SELECT obsahuje spojení.
- Více příkazů SELECT připojeni pomocí UNION.
- Sloupec IDENTITY je uveden více než jednou v seznamu SELECT.
- Sloupec IDENTITY je součástí výrazu.
    
Pokud platí jedna z těchto podmínek, vytvoří se sloupci NOT NULL místo dědí vlastnost Identita.

### <a name="create-table-as-select"></a>VYTVOŘENÍ TABLE AS SELECT
Vytvoření tabulky AS vyberte funkce CTAS () odpovídá stejné chování systému SQL Server, která je popsána vyberte... DO. V definici sloupce však nelze určit vlastnost IDENTITY `CREATE TABLE` část příkazu. Funkci IDENTITY v také nelze použít `SELECT` součástí funkce CTAS. Vyplnění tabulky, budete muset použít `CREATE TABLE` definovat v tabulce, za nímž následuje `INSERT..SELECT` k jejímu naplnění.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Explicitně vložení hodnoty do sloupce IDENTITY 
SQL Data Warehouse podporuje `SET IDENTITY_INSERT <your table> ON|OFF` syntaxe. Pomocí této syntaxe explicitně vložení hodnoty do sloupce IDENTITY.

Mnoho modelování dat se chtěli použít předdefinované záporné hodnoty pro některé řádky v jejich dimenzí. Příkladem je "neznámého člena" řádek nebo hodnotu -1. 

Další skriptu ukazuje, jak chcete explicitně přidat pomocí nastavení IDENTITY_INSERT tento řádek:

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

## <a name="loading-data"></a>Načítání dat

Přítomnost vlastnost IDENTITY má některé důsledky načítání dat kódu. V této části jsou zdůrazněné některé základní vzory pro načítání dat do tabulky pomocí IDENTITY. 

Načtení dat do tabulky a vygenerovat klíč náhradní pomocí IDENTITY, vytvořit v tabulce a pak použít příkaz INSERT... Vyberte nebo VLOŽTE... HODNOTY k provedení zatížení.

V následujícím příkladu jsou uvedeny základní vzor:
 
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

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> Není možné použít vytvořit VYBR AS tabulku ' aktuálně při načítání dat do tabulky se sloupcem IDENTITY.
> 

Další informace o načítání dat najdete v tématu [navrhování extrahovat, zatížení a transformace ELT () pro Azure SQL Data Warehouse](design-elt-data-loading.md) a [načítání osvědčené postupy](guidance-for-loading-data.md).


## <a name="system-views"></a>Systémová zobrazení
Můžete použít [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) katalogu zobrazení k identifikaci sloupec, který má vlastnost IDENTITY.

Vám pomůžou lépe pochopit schématu databáze, tento příklad ukazuje, jak integrovat sys.identity_column' s dalšími zobrazeními katalogu systému:

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
- Pokud není sloupec datový typ INT nebo BIGINT
- Pokud je sloupec také distribučního klíče
- Pokud je tabulka externí tabulky 

Následující související funkce nejsou podporovány v SQL Data Warehouse:

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [FUNKCE IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)
- [PŘÍKAZ DBCC CHECK_IDENT()](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)

## <a name="common-tasks"></a>Běžné úkoly

Tato část obsahuje některé ukázkový kód, který slouží k provádění běžných úloh při práci s sloupců IDENTITY. 

Sloupec C1 je identita v následující úlohy.
 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>Vyhledá nejvyšší hodnotu přidělené pro tabulku
Použití `MAX()` funkce lze určit nejvyšší hodnotu přidělené pro distribuované tabulku:

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Vyhledejte počáteční hodnoty a přírůstku pro vlastnost IDENTITY
Zobrazení katalogu můžete použít ke zjištění hodnoty identity přírůstek a počáteční konfigurace pro tabulku s použitím následujícího dotazu: 

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

* Další informace o vývoji tabulky, najdete v článku [tabulka přehled] [přehled].  

