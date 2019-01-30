---
title: Pomocí IDENTITY pro vytváření klíčů náhradní – Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Doporučení a příklady pro vlastnost IDENTITY používá k vytváření náhradní klíče tabulky ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 5cb406a52cb8fa9b5e40d9b0775f4a616950f507
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250853"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Chcete-li vytvořit náhradní klíče ve službě Azure SQL Data Warehouse pomocí IDENTITY
Doporučení a příklady pro vlastnost IDENTITY používá k vytváření náhradní klíče tabulky ve službě Azure SQL Data Warehouse.

## <a name="what-is-a-surrogate-key"></a>Co je náhradní klíč?
Náhradní klíč u tabulky se sloupci s jedinečným identifikátorem pro každý řádek. Klíč negeneruje z dat tabulky. Modelování dat, jako je vytvořit náhradní klíče v jejich tabulky při návrhu datové modely skladu. Vlastnost IDENTITY můžete použít k dosažení tohoto cíle jednoduše a efektivně, aniž by to ovlivnilo výkon načítání.  

## <a name="creating-a-table-with-an-identity-column"></a>Vytvoření tabulky se sloupcem IDENTITY
Vlastnost IDENTITY je navržená pro horizontální navýšení kapacity napříč distribuce v datovém skladu, aniž by to ovlivnilo výkon načítání. Implementace IDENTITY je proto orientované na dosažení těchto cílů. 

Můžete definovat tabulku tak, že má vlastnost IDENTITY při prvním vytvoření tabulky pomocí syntaxe, která se podobá následující příkaz:

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

Pak můžete použít `INSERT..SELECT` naplnění tabulky.

Tato zbytku této části ukazuje drobné rozdíly v implementaci vám pomůžou pochopit jejich podrobněji.  

### <a name="allocation-of-values"></a>Přidělování hodnot
Vlastnost IDENTITY nezaručuje pořadí, ve které jsou přiděleny náhradní hodnoty, která odráží chování systému SQL Server a Azure SQL Database. Ale ve službě Azure SQL Data Warehouse, neexistence záruku je zřetelnější. 

V následujícím příkladu je obrázku:

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

V předchozím příkladu jsou dva řádky dostali do distribuční 1. První řádek obsahuje ve sloupci na náhradní hodnotu 1 `C1`, a druhý řádek obsahuje náhradní hodnotu 61. Oba tyto hodnoty se vygeneruje vlastnost IDENTITY. Přidělování hodnot ale není souvislé. Toto chování je záměrné.

### <a name="skewed-data"></a>Výrazně nerovnoměrnou distribucí dat. 
Rozsah hodnot pro datový typ jsou rovnoměrně rozloženy distribuce. Pokud distribuované tabulky se setká s výrazně nerovnoměrnou distribucí dat, můžete rozsah hodnot, které jsou k dispozici pro datový typ vyčerpání předčasně ukončen. Například pokud všechna data končí v jedné distribuce, pak efektivně tabulka má přístup k jedné pouze šedesátině hodnot datového typu. Z tohoto důvodu je omezená na vlastnost IDENTITY `INT` a `BIGINT` datové typy pouze.

### <a name="selectinto"></a>VYBERTE... DO
Pokud do nové tabulky je vybraná existující sloupec IDENTITY, nového sloupce, který dědí vlastnost Identita, pokud je splněna jedna z následujících podmínek:
- Příkaz SELECT obsahuje spojení.
- Více příkazů SELECT se propojují s použitím SJEDNOCENÍ.
- Sloupec IDENTITY je uveden více než jednou v seznamu SELECT.
- Sloupec IDENTITY je součástí výrazu.
    
Pokud platí některá z těchto podmínek, je vytvořen sloupce není NULL namísto dědí vlastnost Identita.

### <a name="create-table-as-select"></a>VYTVOŘENÍ TABLE AS SELECT
Vytvoření TABLE AS SELECT (CTAS) následuje stejné chování systému SQL Server, které jsou uvedené pro vybrané... DO. Však nelze zadat vlastnost IDENTITY v definici sloupce `CREATE TABLE` část příkazu. Funkci IDENTITY v také nelze použít `SELECT` součástí příkazu CTAS. K vyplnění tabulky, budete muset použít `CREATE TABLE` k definování tabulky, za nímž následuje `INSERT..SELECT` ho naplnit.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Explicitně vkládání hodnot do sloupce IDENTITY 
SQL Data Warehouse podporuje `SET IDENTITY_INSERT <your table> ON|OFF` syntaxe. Můžete použít tuto syntaxi explicitně vložení hodnoty do sloupce IDENTITY.

Mnoho modelování dat, jako je použití předdefinovaných záporné hodnoty pro určité řádky v jejich dimenze. Příkladem je hodnotu -1 nebo řádek "Neznámý člen". 

Další skript ukazuje, jak pomocí nastavení IDENTITY_INSERT explicitně přidat tento řádek:

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

Přítomnost vlastnost identita má vliv na některé načítání dat kódu. Tato část ukazuje některé základní vzory pro načítání dat do tabulky pomocí IDENTITY. 

K načtení dat do tabulky a generovat náhradní klíč pomocí IDENTITY, vytvoření tabulky a následné použití INSERT... Vyberte nebo VLOŽTE... HODNOTY k provedení zatížení.

Následující příklad ukazuje základní vzor:
 
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
> Není možné použít vytvořit vybrat jako tabulku ' aktuálně při načítání dat do tabulky se sloupcem IDENTITY.
> 

Další informace o načítání dat najdete v tématu [navrhování extrakce, načítání a transformace (ELT) pro Azure SQL Data Warehouse](design-elt-data-loading.md) a [osvědčené postupy načítání](guidance-for-loading-data.md).


## <a name="system-views"></a>Systémová zobrazení
Můžete použít [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) katalogu uvidíte, jaké sloupce, který má vlastnost IDENTITY.

Které vám pomůžou lépe porozumět schéma databáze, tento příklad ukazuje, jak integrovat sys.identity_column "s dalšími zobrazeními katalogu systému:

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
- Když sloupec je také distribučního klíče
- Když je tabulka externí tabulky 

Následující související funkce nejsou podporované ve službě SQL Data Warehouse:

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)
- [DBCC CHECK_IDENT()](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)

## <a name="common-tasks"></a>Běžné úkoly

Tato část obsahuje ukázkový kód, který můžete použít k provádění běžných úkolů při práci s sloupce IDENTITY. 

Sloupec C1 je identita v následující úlohy.
 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>Vyhledá nejvyšší hodnotu přidělené pro tabulku
Použití `MAX()` funkce, která se určí přidělené pro distribuované tabulky nejvyšší hodnotu:

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Vyhledejte počáteční hodnoty a přírůstek pro vlastnost IDENTITY
Zobrazení katalogu můžete použít ke zjištění hodnot identity přírůstek a počáteční konfiguraci pro tabulku s použitím následujícího dotazu: 

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

* Další informace o vývoji tabulky, najdete v článku [Přehled tabulek] [přehled].  

