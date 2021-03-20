---
title: Vytváření sestav napříč cloudových databází s horizontálním škálováním
description: jak nastavit elastické dotazy přes horizontální oddíly
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 148c4828309738a18dbda5fd35ea634e8384bfde
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792102"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Vytváření sestav napříč cloudových databází s horizontálním škálováním (Preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![Dotaz napříč horizontálních oddílů][1]

Databáze horizontálně dělené rozdělují řádky napříč škálovaná datovou vrstvou. Schéma je stejné ve všech zúčastněných databázích, označovaných také jako horizontální dělení. Pomocí elastického dotazu můžete vytvořit sestavy, které budou zahrnovat všechny databáze v databázi horizontálně dělené.

Rychlé zprovoznění najdete v tématu [vytváření sestav napříč cloudových databází s horizontálním škálováním kapacity](elastic-query-getting-started.md).

Pro databáze jiné než horizontálně dělené se podívejte na téma [dotazování napříč cloudovou databází s různými schématy](elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Předpoklady

* Vytvořte mapu horizontálních oddílů pomocí klientské knihovny elastické databáze. viz [Správa mapování horizontálních oddílů](elastic-scale-shard-map-management.md). Nebo použijte ukázkovou aplikaci v části [Začínáme s nástroji elastické databáze](elastic-scale-get-started.md).
* Další informace najdete v tématu [migrace existujících databází do databází s horizontálním](elastic-convert-to-use-elastic-tools.md)navýšení kapacity.
* Uživatel musí mít oprávnění změnit všechna externí zdrojová DATA. Toto oprávnění je součástí oprávnění ALTER DATABASE.
* Aby bylo možné odkazovat na podkladový zdroj dat, je třeba změnit všechna oprávnění ke zdroji externích dat.

## <a name="overview"></a>Přehled

Tyto příkazy vytvoří reprezentace metadat vaší úrovně dat horizontálně dělené v elastické databázi dotazů.

1. [VYTVOŘIT HLAVNÍ KLÍČ](/sql/t-sql/statements/create-master-key-transact-sql)
2. [VYTVOŘIT DATABÁZI S ROZSAHEM POVĚŘENÍ](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [VYTVOŘIT EXTERNÍ ZDROJ DAT](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [VYTVOŘIT EXTERNÍ TABULKU](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1,1 Vytvoření hlavního klíče a přihlašovacích údajů v oboru databáze

Pověření je používáno elastickým dotazem pro připojení ke vzdáleným databázím.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> Ujistěte se, že *" \<username\> "* neobsahuje žádnou příponu *" \@ servername"* .

## <a name="12-create-external-data-sources"></a>1,2 Vytvoření externích zdrojů dat

Syntaxe:

```sql
<External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
        (TYPE = SHARD_MAP_MANAGER,
                   LOCATION = '<fully_qualified_server_name>',
        DATABASE_NAME = ‘<shardmap_database_name>',
        CREDENTIAL = <credential_name>,
        SHARD_MAP_NAME = ‘<shardmapname>’
               ) [;]
```

### <a name="example"></a>Příklad

```sql
CREATE EXTERNAL DATA SOURCE MyExtSrc
WITH
(
    TYPE=SHARD_MAP_MANAGER,
    LOCATION='myserver.database.windows.net',
    DATABASE_NAME='ShardMapDatabase',
    CREDENTIAL= SMMUser,
    SHARD_MAP_NAME='ShardMap'
);
```

Načíst seznam aktuálních externích zdrojů dat:

```sql
select * from sys.external_data_sources;
```

Externí zdroj dat odkazuje na mapu horizontálních oddílů. Elastický dotaz pak použije externí zdroj dat a základní mapu horizontálních oddílů k vytvoření výčtu databází, které se účastní datové vrstvy.
Stejné přihlašovací údaje se používají ke čtení mapy horizontálních oddílů a k přístupu k datům v horizontálních oddílů během zpracování elastického dotazu.

## <a name="13-create-external-tables"></a>1,3 vytvoření externích tabulek

Syntaxe:  

```sql
CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
    ( { <column_definition> } [ ,...n ])
    { WITH ( <sharded_external_table_options> ) }
) [;]  

<sharded_external_table_options> ::=
  DATA_SOURCE = <External_Data_Source>,
  [ SCHEMA_NAME = N'nonescaped_schema_name',]
  [ OBJECT_NAME = N'nonescaped_object_name',]
  DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN
```

**Příklad**

```sql
CREATE EXTERNAL TABLE [dbo].[order_line](
     [ol_o_id] int NOT NULL,
     [ol_d_id] tinyint NOT NULL,
     [ol_w_id] int NOT NULL,
     [ol_number] tinyint NOT NULL,
     [ol_i_id] int NOT NULL,
     [ol_delivery_d] datetime NOT NULL,
     [ol_amount] smallmoney NOT NULL,
     [ol_supply_w_id] int NOT NULL,
     [ol_quantity] smallint NOT NULL,
      [ol_dist_info] char(24) NOT NULL
)

WITH
(
    DATA_SOURCE = MyExtSrc,
     SCHEMA_NAME = 'orders',
     OBJECT_NAME = 'order_details',
    DISTRIBUTION=SHARDED(ol_w_id)
);
```

Načíst seznam externích tabulek z aktuální databáze:

```sql
SELECT * from sys.external_tables;
```

Vyřazení externích tabulek:

```sql
DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]
```

### <a name="remarks"></a>Poznámky

\_Klauzule zdroj dat definuje externí zdroj dat (mapa horizontálních oddílů), který se používá pro externí tabulku.  

\_Klauzule název schématu a \_ název objektu mapují definici externí tabulky na tabulku v jiném schématu. Pokud tento parametr vynecháte, předpokládá se, že schéma vzdáleného objektu bude "dbo" a jeho název se bude shodovat s názvem externí tabulky, kterou definujete. To je užitečné, pokud je název vzdálené tabulky již vytvořen v databázi, ve které chcete vytvořit externí tabulku. Například chcete definovat externí tabulku pro získání agregovaného zobrazení katalogu zobrazení nebo zobrazení dynamické správy na škálované datové vrstvě. Vzhledem k tomu, že zobrazení katalogu a zobrazení dynamické správy již existují místně, nemůžete pro definici externí tabulky použít jejich názvy. Místo toho použijte jiný název a použijte název zobrazení katalogu nebo DMV v \_ klauzulích název schématu nebo \_ název objektu. (Viz následující příklad.)

Klauzule distribuce určuje distribuci dat použitou pro tuto tabulku. Procesor dotazů využívá informace uvedené v klauzuli distribuce k sestavení nejúčinnějších plánů dotazů.

1. **Horizontálně dělené** znamená, že data jsou horizontálně rozdělená mezi databáze. Klíč rozdělení do oddílů pro distribuci dat je parametr **<sharding_column_name>** .
2. **Replikovaný** znamená, že se v každé databázi nacházejí identické kopie tabulky. Je vaší zodpovědností zajistit, aby byly repliky identické napříč databázemi.
3. Kulatá kolečka **\_ DOTAZování** znamená, že je tabulka horizontálně rozdělená pomocí metody distribuce závislé na aplikaci.

**Odkaz na datovou vrstvu**: instance DDL externí tabulky odkazuje na externí zdroj dat. Externí zdroj dat určuje mapu horizontálních oddílů, která poskytuje externí tabulku s informacemi potřebnými k vyhledání všech databází v datové vrstvě.

### <a name="security-considerations"></a>Důležité informace o zabezpečení

Uživatelé s přístupem k externí tabulce automaticky získají přístup k podkladovým vzdáleným tabulkám v rámci přihlašovacích údajů uvedených v definici externího zdroje dat. Vyhněte se neočekávaným zvýšením oprávnění prostřednictvím přihlašovacích údajů externího zdroje dat. Použijte udělení nebo odvolat pro externí tabulku, jako by šlo o běžnou tabulku.  

Po definování externího zdroje dat a externích tabulek teď můžete použít plný T-SQL přes vaše externí tabulky.

## <a name="example-querying-horizontal-partitioned-databases"></a>Příklad: dotazování na horizontálně dělené databáze

Následující dotaz provádí tři způsoby spojení mezi datovými sklady, objednávkami a řádky objednávek a používá několik agregací a selektivního filtru. Předpokládá, že (1) horizontální dělení (horizontálního dělení) a (2) tyto sklady, objednávky a řádky objednávky jsou horizontálně dělené podle sloupce ID skladu a že elastický dotaz může společně vyhledat spojení na horizontálních oddílů a zpracovat nákladný podíl dotazu na horizontálních oddílů paralelně.

```sql
    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount,
         min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Uložená procedura pro vzdálené spuštění T-SQL: SP \_ execute_remote

Elastický dotaz také zavádí uloženou proceduru, která poskytuje přímý přístup k horizontálních oddílů. Uložená procedura se nazývá [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) a dá se použít ke spouštění vzdálených uložených procedur nebo kódu T-SQL na vzdálených databázích. Má následující parametry:

* Název zdroje dat (nvarchar): název externího zdroje dat typu RDBMS.
* Dotaz (nvarchar): dotaz T-SQL, který se má provést na každém horizontálních oddílů.
* Deklarace parametru (nvarchar) – volitelné: řetězec s definicemi datových typů pro parametry používané v parametru dotazu (například sp_executesql).
* Seznam hodnot parametrů – volitelné: čárkami oddělený seznam hodnot parametrů (například sp_executesql).

Metoda SP \_ Execute \_ Remote využívá externí zdroj dat, který je k dispozici v parametrech volání, ke spuštění daného příkazu T-SQL ve vzdálených databázích. Pomocí přihlašovacích údajů k externímu zdroji dat se můžete připojit k databázi shardmap Manageru a vzdáleným databázím.  

Příklad:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Možnosti připojení pro nástroje

Pomocí regulárních připojovacích řetězců SQL Server připojte k databázi nástroje pro integraci aplikace, BI a dat s definicemi vašich externích tabulek. Ujistěte se, že je pro nástroj SQL Server podporována jako zdroj dat. Pak se připojte k databázi elastického dotazu jako k jakékoli jiné SQL Server databázi připojené k nástroji a použijte externí tabulky z nástroje nebo aplikace, jako kdyby byly místní tabulky.

## <a name="best-practices"></a>Osvědčené postupy

* Zajistěte, aby byla databáze koncových bodů elastického dotazu udělena přístup k databázi shardmap a všem horizontálních oddílů prostřednictvím SQL Database firewallů.  
* Ověří nebo vynutilo distribuci dat definovanou externí tabulkou. Pokud se vaše skutečná distribuce dat liší od distribuce zadané v definici tabulky, můžou dotazy vracet neočekávané výsledky.
* Elastický dotaz v současné době neprovádí eliminaci horizontálních oddílů, pokud predikáty přes klíč horizontálního dělení umožní bezpečně vyloučit určité horizontálních oddílů ze zpracování.
* Elastický dotaz funguje nejlépe pro dotazy, kde je možné provést většinu výpočtů na horizontálních oddílů. Obvykle získáte nejlepší výkon dotazů pomocí predikátů selektivního filtru, které je možné vyhodnotit na horizontálních oddílů nebo se spojí s oddíly dělení, které lze provádět v rámci všech horizontálních oddílů. Další vzory dotazů můžou potřebovat načíst velké objemy dat z horizontálních oddílů do hlavního uzlu a můžou fungovat špatně.

## <a name="next-steps"></a>Další kroky

* Přehled elastického dotazu najdete v tématu [Přehled elastického dotazu](elastic-query-overview.md).
* Kurz pro vertikální dělení najdete v tématu [Začínáme s mezidatabázovým dotazem (vertikální dělení)](elastic-query-getting-started-vertical.md).
* Syntaxe a ukázkové dotazy pro vertikálně dělená data najdete v tématu [dotazování na vertikálně dělená data](elastic-query-vertical-partitioning.md) .
* Kurz horizontálního dělení na oddíly (horizontálního dělení) najdete v tématu [Začínáme s elastickým dotazem pro horizontální dělení na oddíly (horizontálního dělení)](elastic-query-getting-started.md).
* V tématu [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) pro uloženou proceduru, která provádí příkaz Transact-SQL na jednom vzdáleném Azure SQL Database nebo sadě databází, která slouží jako horizontálních oddílů ve vodorovném schématu dělení.

<!--Image references-->
[1]: ./media/elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->