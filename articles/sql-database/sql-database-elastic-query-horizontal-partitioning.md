---
title: Vytváření sestav napříč cloudovými databázemi s horizontálním navýšením kapacity
description: nastavení elastických dotazů přes vodorovné oddíly
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 79abaade22fc107fa4c848607ff48232eeeb58ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823767"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Vytváření sestav napříč škálovatetými cloudovými databázemi (preview)

![Dotaz napříč úlomky][1]

Horizontálně rozdělené databáze distribuovat řádky přes škálované datové vrstvy. Schéma je identické ve všech zúčastněných databázích, označované také jako horizontální dělení. Pomocí elastického dotazu můžete vytvořit sestavy, které pokrývají všechny databáze v databázi s oddíly.

Pro rychlý start najdete [v tématu Vytváření sestav napříč cloudovými databázemi s horizontálním navýšením kapacity](sql-database-elastic-query-getting-started.md).

Pro databáze bez rozdílových disponování naleznete [v tématu Dotaz napříč cloudovými databázemi s různými schématy](sql-database-elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Požadavky

* Vytvořte mapu střepů pomocí klientské knihovny elastické databáze. viz [Správa map svižné bitové ženáže](sql-database-elastic-scale-shard-map-management.md). Nebo použijte ukázkovou aplikaci v [článku Začínáme s nástroji pro elastické databáze](sql-database-elastic-scale-get-started.md).
* Případně najdete [v tématu Migrace existujících databází na škálované databáze](sql-database-elastic-convert-to-use-elastic-tools.md).
* Uživatel musí mít oprávnění ALTER any external data source. Toto oprávnění je součástí oprávnění ALTER DATABASE.
* ZMĚNIT všechna oprávnění externího zdroje dat jsou potřebné k odkazování na základní zdroj dat.

## <a name="overview"></a>Přehled

Tyto příkazy vytvořit reprezentaci metadat vaší vrstvy s vrstvou s vrstvou s vrstvou s vrstvou elastického dotazu.

1. [VYTVOŘIT HLAVNÍ KLÍČ](https://msdn.microsoft.com/library/ms174382.aspx)
2. [Vytvořit pověření s rozsahem databáze](https://msdn.microsoft.com/library/mt270260.aspx)
3. [VYTVOŘENÍ EXTERNÍHO ZDROJE DAT](https://msdn.microsoft.com/library/dn935022.aspx)
4. [VYTVOŘIT EXTERNÍ TABULKU](https://msdn.microsoft.com/library/dn935021.aspx)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Vytvoření hlavního klíče a pověření s rozsahem databáze

Pověření se používá elastické dotaz pro připojení ke vzdáleným databázím.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Ujistěte se, že *"\<uživatelské jméno\>"* neobsahuje žádnou příponu " *\@název_serveru".*

## <a name="12-create-external-data-sources"></a>1.2 Vytvoření externích zdrojů dat

Syntaxe:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
            (TYPE = SHARD_MAP_MANAGER,
                       LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>,
            SHARD_MAP_NAME = ‘<shardmapname>’
                   ) [;]

### <a name="example"></a>Příklad

    CREATE EXTERNAL DATA SOURCE MyExtSrc
    WITH
    (
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net',
        DATABASE_NAME='ShardMapDatabase',
        CREDENTIAL= SMMUser,
        SHARD_MAP_NAME='ShardMap'
    );

Načíst seznam aktuálních externích zdrojů dat:

    select * from sys.external_data_sources;

Externí zdroj dat odkazuje na mapu úlomku. Elastický dotaz pak používá externí zdroj dat a podkladové mapování svižnitého objektu k vytvoření výčtu databází, které se účastní datové vrstvy.
Stejná pověření se používají ke čtení mapy střepů a přístup k datům na úlomcích během zpracování elastického dotazu.

## <a name="13-create-external-tables"></a>1.3 Vytvoření externích tabulek

Syntaxe:  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  

    <sharded_external_table_options> ::=
      DATA_SOURCE = <External_Data_Source>,
      [ SCHEMA_NAME = N'nonescaped_schema_name',]
      [ OBJECT_NAME = N'nonescaped_object_name',]
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Příklad**

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

Načíst seznam externích tabulek z aktuální databáze:

    SELECT * from sys.external_tables;

Přetažení externích tabulek:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Poznámky

Klauzule\_DATA SOURCE definuje externí zdroj dat (mapu střepu), který se používá pro externí tabulku.  

Klauzule Název\_schema a NÁZEV OBJEKTU\_mapují definici externí tabulky na tabulku v jiném schématu. Pokud je vynecháno, schéma vzdáleného objektu se považuje za "dbo" a jeho název je považován za shodný s definovaným názvem externí tabulky. To je užitečné, pokud je název vzdálené tabulky již přijata v databázi, kde chcete vytvořit externí tabulku. Chcete-li například definovat externí tabulku, chcete-li získat agregované zobrazení zobrazení katalogu nebo dmvs na škálované datové vrstvě. Vzhledem k tomu, že zobrazení katalogu a dmv již existují místně, nelze použít jejich názvy pro definici externí tabulky. Místo toho použijte jiný název a použijte název zobrazení katalogu nebo DMV\_v klauzulích\_SCHEMA NAME a/nebo OBJECT NAME. (Viz příklad níže.)

Klauzule DISTRIBUTION určuje distribuci dat použitou pro tuto tabulku. Procesor dotazu využívá informace uvedené v klauzuli DISTRIBUTION k vytvoření nejefektivnějších plánů dotazů.

1. **SHARDED** znamená, že data jsou horizontálně rozdělena napříč databázemi. Klíč dělení pro distribuci dat je **<sharding_column_name>** parametr.
2. **REPLICATED** znamená, že identické kopie tabulky jsou k dispozici v každé databázi. Je vaší odpovědností zajistit, aby repliky jsou identické v databázích.
3. **ROUND\_ROBIN** znamená, že tabulka je horizontálně rozdělena pomocí distribuční metody závislé na aplikaci.

**Odkaz na datovou vrstvu**: Externí tabulka DDL odkazuje na externí zdroj dat. Externí zdroj dat určuje mapu střepů, která poskytuje externí tabulce informace potřebné k vyhledání všech databází v datové vrstvě.

### <a name="security-considerations"></a>Důležité informace o zabezpečení

Uživatelé s přístupem k externí tabulce automaticky získají přístup k podkladovým vzdáleným tabulkám pod pověřením uvedeným v definici externího zdroje dat. Vyhněte se nežádoucímu zvýšení oprávnění prostřednictvím pověření externího zdroje dat. Použijte GRANT nebo REVOKE pro externí tabulku, stejně jako by se jednalo o běžnou tabulku.  

Jakmile definujete externí zdroj dat a externí tabulky, můžete nyní použít úplné T-SQL přes externí tabulky.

## <a name="example-querying-horizontal-partitioned-databases"></a>Příklad: dotazování na vodorovné rozdělené databáze

Následující dotaz provádí třícestné spojení mezi sklady, objednávkami a řádky objednávky a používá několik agregací a selektivní filtr. Předpokládá (1) horizontální dělení (horizontální dělení) a (2) že sklady, objednávky a řádky objednávky jsou horizontálně rozděleny sloupcem ID skladu a že elastický dotaz může co-locate spojení na horizontálních oddílů a zpracovat nákladnou část dotazu na horizontálních oddílů v Paralelní.

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

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Uložená procedura pro vzdálené\_spuštění T-SQL: sp execute_remote

Elastický dotaz také zavádí uloženou proceduru, která poskytuje přímý přístup k úlomky. Uložená procedura se nazývá [sp\_spustit \_vzdálené](https://msdn.microsoft.com/library/mt703714) a lze jej použít ke spuštění vzdálené uložené procedury nebo T-SQL kód ve vzdálených databázích. To trvá následující parametry:

* Název zdroje dat (nvarchar): Název externího zdroje dat typu RDBMS.
* Dotaz (nvarchar): Dotaz T-SQL, který má být proveden na každém úlovku.
* Deklarace parametru (nvarchar) - volitelné: Řetězec s definicemi datového typu pro parametry použité v parametru Dotaz (například sp_executesql).
* Seznam hodnot parametrů - volitelné: Seznam hodnot parametrů oddělených čárkami (například sp_executesql).

Vzdálené\_spuštění\_sp používá externí zdroj dat uvedený v parametrech vyvolání k provedení daného příkazu T-SQL ve vzdálených databázích. Používá pověření externího zdroje dat pro připojení k databázi správce shardmap a vzdáleným databázím.  

Příklad:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Konektivita pro nástroje

Pomocí běžných připojovacích řetězců SQL Serveru můžete připojit aplikaci, bi a nástroje pro integraci dat k databázi pomocí externích definic tabulek. Ujistěte se, že SQL Server je podporován jako zdroj dat pro váš nástroj. Potom odkazovat na databázi elastických dotazů, stejně jako všechny ostatní sql server databáze připojené k nástroji a použít externí tabulky z vašeho nástroje nebo aplikace, jako by byly místní tabulky.

## <a name="best-practices"></a>Osvědčené postupy

* Ujistěte se, že databáze koncového bodu elastického dotazu byl udělen přístup k databázi shardmap a všechny oddíly prostřednictvím brány firewall SQL DB.  
* Ověřte nebo vynucujte distribuci dat definovanou externí tabulkou. Pokud se vaše skutečné rozdělení dat liší od distribuce zadané v definici tabulky, mohou dotazy přinést neočekávané výsledky.
* Elastický dotaz aktuálně neprovádí odstranění štřepů, když predikáty přes klíč srážlivosti by mu umožnilo bezpečně vyloučit některé oddíly šermu ze zpracování.
* Elastický dotaz funguje nejlépe pro dotazy, kde většinu výpočtu lze provést na úlomcích. Obvykle získáte nejlepší výkon dotazu s predikáty selektivní filtr, které mohou být vyhodnoceny na šiřitelé nebo připojí přes klíče dělení, které lze provést způsobem zarovnaný oddíl na všechny oddíly. Jiné vzorky dotazu může být nutné načíst velké množství dat z úlomků do hlavního uzlu a může pracovat špatně

## <a name="next-steps"></a>Další kroky

* Přehled elastického dotazu najdete v [tématu Přehled elastického dotazu](sql-database-elastic-query-overview.md).
* Kurz vertikálního dělení naleznete v [tématu Začínáme s dotazem mezi databázemi (vertikální dělení)](sql-database-elastic-query-getting-started-vertical.md).
* Syntaxe a ukázkové dotazy pro svisle dělená data, najdete [v tématu Dotazování svisle dělených dat)](sql-database-elastic-query-vertical-partitioning.md)
* Kurz horizontálního dělení (horizontálního dělení) naleznete [v tématu Začínáme s elastickým dotazem pro horizontální dělení (horizontální dělení)](sql-database-elastic-query-getting-started.md).
* Viz [\_sp \_spustit vzdálené](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, která provede příkaz Transact-SQL na jednom vzdáleném Azure SQL Database nebo sadu databází, které slouží jako horizontální oddíly v horizontálním dělení schéma.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
