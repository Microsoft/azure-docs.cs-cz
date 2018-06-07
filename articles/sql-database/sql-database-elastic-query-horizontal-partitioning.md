---
title: Vytváření sestav napříč instancemi cloudu databáze | Microsoft Docs
description: jak nastavit elastické dotazy přes vodorovné oddíly
services: sql-database
documentationcenter: ''
manager: craigg
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: mlandzic
ms.openlocfilehash: fcb498542a496e4a887c825808642d3f586ef1d9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646350"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Vytváření sestav napříč instancemi cloudu databází (preview)
![Dotazování mezi horizontálních oddílů][1]

Horizontálně dělené databáze distribuovat řádky napříč škálovaný dat vrstvy. Schéma je stejná na všechny zúčastněné databáze, také známé jako vodorovné rozdělení do oddílů. Pomocí elastické dotazu, můžete vytvořit sestavy, které jsou rozmístěny všechny databáze v horizontálně dělené databázi.

Rychle začít, najdete v části [vytváření sestav napříč instancemi cloudu databáze](sql-database-elastic-query-getting-started.md).

Horizontálně dělené databází, najdete v části [dotazu mezi databázemi cloudu s různými schématy](sql-database-elastic-query-vertical-partitioning.md). 

## <a name="prerequisites"></a>Požadavky
* Vytvoření mapy horizontálního oddílu pomocí klientské knihovny elastické databáze. v tématu [horizontálního oddílu mapy správu](sql-database-elastic-scale-shard-map-management.md). Nebo použijte ukázkovou aplikaci v [začít pracovat s nástroji elastické databáze](sql-database-elastic-scale-get-started.md).
* Alternativně si zobrazte [migrovat existující databáze do databází upraveným](sql-database-elastic-convert-to-use-elastic-tools.md).
* Uživatel musí mít oprávnění ALTER ANY EXTERNAL DATA SOURCE. Toto oprávnění je součástí oprávnění ALTER DATABASE.
* K odkazování na podkladový zdroj dat jsou potřeba oprávnění ALTER ANY EXTERNAL DATA SOURCE.

## <a name="overview"></a>Přehled
Tyto příkazy vytvoření metadat reprezentace horizontálně dělené datové vrstvě v databázi elastické dotazu. 

1. [VYTVOŘENÍ HLAVNÍHO KLÍČE](https://msdn.microsoft.com/library/ms174382.aspx)
2. [VYTVOŘENÍ DATABÁZE OBOR PŘIHLAŠOVACÍCH ÚDAJŮ](https://msdn.microsoft.com/library/mt270260.aspx)
3. [VYTVOŘENÍ EXTERNÍHO ZDROJE DAT](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREATE EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 vytvořit hlavní klíč databáze obor a přihlašovací údaje
Přihlašovací údaje se používá v elastické dotazu pro připojení k vzdálené databáze.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Ujistěte se, že *"\<uživatelské jméno\>"* neobsahuje žádné *"@servername"* příponu. 
> 
> 

## <a name="12-create-external-data-sources"></a>1.2 Vytvoření externích zdrojů dat.
Syntaxe:

    <External_Data_Source> ::=    
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                                              
            (TYPE = SHARD_MAP_MANAGER,
                       LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>, 
            SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### <a name="example"></a>Příklad:
    CREATE EXTERNAL DATA SOURCE MyExtSrc 
    WITH 
    ( 
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ShardMapDatabase', 
        CREDENTIAL= SMMUser, 
        SHARD_MAP_NAME='ShardMap' 
    );

Načtěte seznam aktuálních zdrojů dat externí: 

    select * from sys.external_data_sources; 

Externí zdroj dat odkazuje na mapě horizontálního oddílu. Dotaz elastické pak používá externí zdroj dat a základní mapy horizontálního oddílu výčet databází, které jsou součástí datové vrstvy. Stejné přihlašovací údaje se používají ke čtení mapy horizontálního oddílu a k přístupu k datům na horizontálních oddílů během zpracování elastické dotazu. 

## <a name="13-create-external-tables"></a>1.3 Vytvoření externí tabulky
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

Načtení seznamu externí tabulky z aktuální databáze: 

    SELECT * from sys.external_tables; 

Chcete-li vyřadit externí tabulky:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Poznámky
DATA\_zdroj klauzule definuje zdroj externích dat (horizontálních map), který se používá pro externí tabulky.  

SCHÉMA\_název a OBJEKT\_název klauzule mapování definici externí tabulky do tabulky v jiném schématu. Pokud tento parametr vynechán, je schéma ke vzdálenému objektu předpokládá se "dbo" a její název se považuje za stejný jako název externí tabulky definovaný. To je užitečné, když se název vzdálené tabulky se už používá v databázi kde chcete vytvořit externí tabulky. Například můžete definovat externí tabulku získat agregované zobrazení katalogu zobrazení nebo zobrazení dynamické správy na škálovaný dat vrstvy. Vzhledem k tomu, že zobrazení katalogu a zobrazení dynamické správy již existují místně, nemůžete použít jejich názvy pro definici externí tabulky. Místo toho použijte jiný název a použijte zobrazení katalogu nebo DMV název ve schématu\_název nebo OBJEKT\_klauzule název. (Viz následující příklad.) 

V klauzuli distribuční Určuje distribuci dat použít pro tuto tabulku. Procesor dotazů využívá informací uvedených v klauzuli distribuční k vytvoření nejúčinnější plány dotazů.  

1. **Horizontálně DĚLENÉ** znamená data vodorovně rozdělena mezi databází. Klíč rozdělení pro distribuci dat je **< sharding_column_name >** parametr.
2. **REPLIKOVAT** znamená, že jsou identické kopií tabulky na každou databázi. Je vaší povinností ujistit, že repliky jsou identické mezi databází.
3. **ZAOKROUHLÍ\_každý s každým** znamená, že v tabulce je vodorovně rozdělena na oddíly pomocí metody distribuce závislé aplikace. 

**Datové vrstvy odkaz**: DDL odkazuje na externí zdroj dat externí tabulky. Externí zdroj dat určuje horizontálního oddílu mapy, které poskytuje informace potřebné k vyhledání všechny databáze v datové vrstvě externí tabulky. 

### <a name="security-considerations"></a>Aspekty zabezpečení
Uživatelé s přístupem k externí tabulky automaticky získají přístup k základní vzdálených tabulek v části přihlašovací údaje zadané v definici zdrojové externí data. Vyhněte se nežádoucí zvýšení oprávnění pomocí přihlašovacích údajů zdroje dat externí. Pomocí GRANT nebo REVOKE pro externí tabulky stejně, jako by šlo o běžnou tabulku.  

Jakmile definujete zdroj externích dat a externí tabulky, teď můžete použít úplnou T-SQL na externí tabulky.

## <a name="example-querying-horizontal-partitioned-databases"></a>Příklad: dotazy na vodorovné oddílů databáze
Následující dotaz spojí třícestný sklady a pořadí řádky objednávek a používá několik agregace a selektivní filtru. Předpokládá (1) vodorovné rozdělení (horizontálního dělení) a (2), sklady, objednávek a pořadí řádky jsou horizontálně dělené podle id sloupce skladu, a zda elastické dotaz můžete společně umísťovat spojení na horizontálních oddílů zpracovat nákladné část dotazu na horizontálních oddílů v paralelní. 

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

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Uložené procedury pro vzdálené spuštění T-SQL: sp\_execute_remote
Elastické dotazu také zavádí uložené procedury, která poskytuje přímý přístup k horizontálních oddílů. Uložená procedura je volána [sp\_provést \_vzdáleného](https://msdn.microsoft.com/library/mt703714) a můžete použít ke spuštění vzdálené uložené procedury nebo kód T-SQL na vzdálené databáze. Ji používá následující parametry: 

* Název zdroje dat (nvarchar): název zdroje externích dat typu relační. 
* Dotaz (nvarchar): na každý horizontálního oddílu provedení dotazu T-SQL. 
* Deklarace parametru (nvarchar) - volitelné: řetězec s definice typu dat pro parametry použité v parametru dotazu (např. sp_executesql). 
* Seznam hodnot parametru - volitelné: čárkami oddělený seznam hodnot parametrů (např. sp_executesql).

Sp\_provést\_vzdálené používá externí zdroj dat součástí Parametry vyvolání daný příkaz T-SQL nelze provést na vzdálené databáze. Přihlašovací údaje z externí zdroj dat používá pro připojení k databázi správce shardmap a vzdálené databáze.  

Příklad: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Připojení nástroje
Používat regulární připojovací řetězce SQL serveru pro připojení vaší aplikace, vaše data a BI nástrojů pro integraci do databáze s definic externí tabulky. Ujistěte se, že systém SQL Server je podporovaný jako zdroj dat pro vaše nástroje. Pak odkaz databáze elastické dotazu jako jakékoli jiné databáze systému SQL Server připojen k nástroj a použijte externí tabulky z nástroje nebo aplikace jako kdyby byly místní tabulky. 

## <a name="best-practices"></a>Osvědčené postupy
* Ujistěte se, že koncový bod databáze elastické dotazu byl udělen přístup k databázi shardmap a všechny horizontálních oddílů přes brány firewall SQL DB.  
* Ověřit nebo vynutit distribuci dat definované externí tabulky. Pokud distribuční skutečná data se liší od rozdělení zadaný v definici vaší tabulky, vaše dotazy mohou vést k neočekávaným výsledkům. 
* Elastické dotazu momentálně neprovádí odstranění horizontálních při predikáty nad klíčem horizontálního dělení by mohla bezpečně vyloučit některé horizontálních oddílů z zpracování.
* Elastické dotazu je nejvhodnější pro dotazy kde většinu výpočet lze provést na horizontálních oddílů. Obvykle získáte nejlepší výkon dotazů s predikáty selektivní filtru, které lze vyhodnotit na horizontálních oddílů nebo spojení přes rozdělení klíčů, které lze provést tak oddílu zarovnaný na všechny horizontálních oddílů. Ostatní typy dotazů možná muset načíst velkých objemů dat z horizontálních oddílů k hlavnímu uzlu a může být špatná

## <a name="next-steps"></a>Další postup

* Přehled elastické dotazů najdete v tématu [elastické dotazu přehled](sql-database-elastic-query-overview.md).
* Vertikální dělení kurzu, najdete v části [Začínáme s mezidatabázové dotazu (vertikální dělení)](sql-database-elastic-query-getting-started-vertical.md).
* Syntaxe a ukázkové dotazy pro svisle oddílů data, najdete v části [dotazování svisle na oddíly dat)](sql-database-elastic-query-vertical-partitioning.md)
* Podívejte se kurz vodorovné rozdělení do oddílů (horizontálního dělení) [Začínáme s elastické dotazu pro vodorovné rozdělení do oddílů (horizontálního dělení)](sql-database-elastic-query-getting-started.md).
* V tématu [sp\_provést \_vzdáleného](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, který spouští příkaz jazyka Transact-SQL na jedné vzdálené databáze SQL Azure nebo sadu databází, které slouží jako horizontálních oddílů v vodorovné schéma rozdělení oddílů.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
