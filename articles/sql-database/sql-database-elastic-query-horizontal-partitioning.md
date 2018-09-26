---
title: Vytváření sestav napříč databázemi s horizontálním navýšením kapacity | Dokumentace Microsoftu
description: jak vytvořit elastické dotazy nad horizontální oddíly
services: sql-database
ms.service: sql-database
subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 906c6825dbfdf7d4873c765d0b3eba626ef52e1b
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159359"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Vytváření sestav napříč databázemi s horizontálním navýšením kapacity (preview)
![Dotazování napříč horizontálními oddíly][1]

Horizontálně dělené databáze distribuovat řádky mezi škálovaném datové vrstvy. Schéma je shodné pro všechny zúčastněné databáze, označované také jako horizontální dělení. Použitím elastického dotazu, můžete vytvořit sestavy, které jsou rozmístěny všech databází v horizontálně dělené databáze.

Rychlý start, naleznete v tématu [vytváření sestav napříč databázemi s horizontálním navýšením kapacity](sql-database-elastic-query-getting-started.md).

Horizontálně dělené databáze, najdete v části [dotaz přes cloudové databáze s různými schématy](sql-database-elastic-query-vertical-partitioning.md). 

## <a name="prerequisites"></a>Požadavky
* Vytvoření mapy horizontálních oddílů pomocí Klientská knihovna elastic database. Zobrazit [správy mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md). Nebo použijte ukázkovou aplikaci v [Začínáme s nástroji elastic database](sql-database-elastic-scale-get-started.md).
* Alternativně si zobrazte [migrace existujících databází do databází s horizontálním navýšením kapacity](sql-database-elastic-convert-to-use-elastic-tools.md).
* Uživatel musí mít oprávnění ALTER ANY EXTERNAL DATA SOURCE. Toto oprávnění je součástí oprávnění ALTER DATABASE.
* K odkazování na podkladový zdroj dat jsou potřeba oprávnění ALTER ANY EXTERNAL DATA SOURCE.

## <a name="overview"></a>Přehled
Tyto příkazy vytvořit reprezentaci metadat úrovně horizontálně dělených dat v databázi elastického dotazu. 

1. [VYTVOŘTE HLAVNÍ KLÍČ](https://msdn.microsoft.com/library/ms174382.aspx)
2. [VYTVOŘENÍ DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [VYTVOŘENÍ EXTERNÍHO ZDROJE DAT](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREATE EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Vytvoření hlavního klíče s rozsahem databáze a přihlašovacích údajů
Přihlašovací údaje, které používá elastický dotaz pro připojení k vzdálené databáze.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Ujistěte se, že *"\<uživatelské jméno\>"* neobsahuje žádné *"\@název_serveru"* příponu. 
> 
> 

## <a name="12-create-external-data-sources"></a>1.2 vytvořit externí zdroje dat
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

Načte seznam aktuální externí zdroje dat: 

    select * from sys.external_data_sources; 

Externí zdroj dat odkazuje mapa horizontálních oddílů. Elastický dotaz použije externí zdroj dat a podkladové mapy horizontálních oddílů k zobrazení výčtu databází, které jsou součástí datové vrstvy. Stejné přihlašovací údaje se používají ke čtení mapy horizontálních oddílů a získat přístup k datům na horizontální oddíly během zpracování elastický dotaz. 

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

Načíst seznam externích tabulek z aktuální databáze: 

    SELECT * from sys.external_tables; 

Chcete-li vyřadit externí tabulky:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Poznámky
DATA\_zdroj klauzule definuje externí zdroj dat (mapy horizontálních oddílů), který se používá pro externí tabulky.  

SCHÉMA\_název a OBJEKT\_název klauzule namapovat definici externí tabulky do tabulky v jiné schéma. Pokud tento parametr vynechán, schéma vzdáleného objektu je předpokládá se, že "dbo" a jeho název je považován za shodný s názvem externí tabulky, je definována. To je užitečné, pokud název vzdálené tabulky se už používá v databázi místo vytvoření externí tabulky. Například můžete definovat externí tabulky k získání agregované zobrazení zobrazení katalogu nebo zobrazení dynamických zpráv na horizontálně dat vrstvy. Protože zobrazení katalogu a zobrazení dynamické správy už místně, nelze použít jejich názvy pro definici externí tabulky. Místo toho použijte jiný název a použijte zobrazení katalogu nebo zobrazení dynamické správy název ve schématu\_název a/nebo OBJEKT\_klauzule název. (Viz následující příklad). 

DISTRIBUČNÍ klauzule určuje distribuci dat použít pro tuto tabulku. Procesor dotazů využívá informace uvedené v distribuční klauzule pro sestavení nejúčinnější plány dotazů.  

1. **Horizontálně DĚLENÉ** znamená, že data se horizontálně dělené do oddílů napříč databázemi. Dělení klíč pro distribuci dat je **< sharding_column_name >** parametru.
2. **REPLIKOVANÉ** znamená, že jsou k dispozici v každé databázi totožné kopie v tabulce. Je vaší povinností ujistit, že tyto repliky jsou identické napříč databázemi.
3. **ROUND\_ROBIN** znamená, že v tabulce je horizontálně dělené do oddílů pomocí metody distribuce závislých na aplikaci. 

**Data úrovně odkaz**: DDL odkazuje na externí zdroj dat externí tabulky. Externí zdroj dat určuje mapy horizontálních oddílů, který poskytuje informace potřebné k vyhledání všech databází ve vaší datové vrstvě externí tabulky. 

### <a name="security-considerations"></a>Aspekty zabezpečení
Uživatelé s přístupem k externí tabulky automaticky získáte přístup k podkladové vzdálených tabulek v části přihlašovací údaje zadané v definici zdroje externí data. Vyhněte se nežádoucí zvýšení oprávnění prostřednictvím přihlašovacích údajů zdroje dat externí. Pomocí GRANT nebo REVOKE pro externí tabulky, stejně, jako by šlo o běžnou tabulku.  

Po definování externího zdroje dat. a externích tabulek, můžete nyní použít úplné T-SQL na externí tabulky.

## <a name="example-querying-horizontal-partitioned-databases"></a>Příklad: dotazování vodorovné dělenou databází
Následující dotaz spojí trojcestných sklady, objednávky a řádky a používá několik agregace a selektivní filtru. Předpokládá (1) horizontální dělení (sharding) a (2), sklady, objednávky a řádky jsou horizontálně dělené podle sloupce id skladu a že elastický dotaz můžete společně umísťovat spojení na horizontální oddíly a zpracovávat náročná část dotazu na horizontální oddíly takovým paralelní. 

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

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Uložené procedury pro vzdálené spuštění T-SQL: aktualizace sp\_execute_remote
Elastický dotaz také zavádí uloženou proceduru, která poskytuje přímý přístup k horizontální oddíly. Volání uložené procedury [sp\_provést \_vzdálené](https://msdn.microsoft.com/library/mt703714) a je možné ke spouštění uložené procedury vzdálený nebo kód T-SQL na vzdálené databáze. Ji používá následující parametry: 

* Název zdroje dat (nvarchar): název externí zdroj dat typu relační databázový systém. 
* Dotaz (nvarchar): dotaz T-SQL k provedení na jednotlivých horizontálních oddílů. 
* Deklarace parametru (nvarchar) – volitelné: řetězec s definice typu dat pro parametry použité v parametru dotazu (např. sp_executesql). 
* Seznam hodnot parametru - volitelné: čárkami oddělený seznam hodnot parametrů (jako je sp_executesql).

Sp\_provést\_vzdálené externí zdroj dat součástí Parametry vyvolání používá ke spouštění daný příkaz T-SQL na vzdálené databáze. Přihlašovací údaje z externí zdroj dat používá pro připojení k databázi manager shardmap a vzdálené databáze.  

Příklad: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Připojení nástroje
Použít regulární systému SQL Server připojovací řetězce k připojení aplikace, nástrojům integrace BI a data do databáze s vaší definice externí tabulky. Ujistěte se, že systém SQL Server je podporovaný jako zdroj dat pro nástroj. Potom odkaz dotaz na elastic database stejně jako jakékoli jiné databáze systému SQL Server připojen k nástroje a použijte externí tabulky z nástroje nebo aplikace jako kdyby byly místní tabulky. 

## <a name="best-practices"></a>Osvědčené postupy
* Ujistěte se, že koncový bod databáze elastický dotaz byl udělen přístup k databázi shardmap a všemi horizontálními oddíly přes brány firewall databáze SQL.  
* Ověřit nebo vynutit distribuci dat určené externí tabulky. Pokud vaše skutečná data distribuce se liší od zadané v definici tabulky distribuci, může vaše dotazy vést k neočekávaným výsledkům. 
* Elastický dotaz momentálně neprovádí horizontálních oddílů odstranění při predikáty přes klíč horizontálního dělení by mohla bezpečně vyloučení některých horizontálních oddílů z zpracování.
* Elastický dotaz je nejvhodnější pro dotazy kde většinu výpočtu lze provést na horizontální oddíly. Obvykle získejte nejlepší výkon dotazů pomocí predikátů selektivní filtru, které lze vyhodnotit na horizontální oddíly nebo spojení přes dělení klíčů, které lze provést tak zarovnání oddílu na všechny horizontální oddíly. Další vzory dotazů může potřebovat načítání velkých objemů dat z horizontální oddíly k hlavnímu uzlu a může být špatná

## <a name="next-steps"></a>Další postup

* Přehled elastický dotaz, naleznete v tématu [elastický dotaz přehled](sql-database-elastic-query-overview.md).
* Vertikální dělení kurz najdete v tématu [Začínáme s mezidatabázovými dotazy (vertikální oddíly)](sql-database-elastic-query-getting-started-vertical.md).
* Syntaxe a ukázkové dotazy na vertikálně dělená data, najdete v části [dotazování na vertikálně dělené data)](sql-database-elastic-query-vertical-partitioning.md)
* Horizontální dělení (sharding) kurz najdete v tématu [Začínáme se službou elastický dotaz pro horizontální dělení (sharding)](sql-database-elastic-query-getting-started.md).
* Naleznete v tématu [sp\_provést \_vzdálené](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, která provádí příkaz jazyka Transact-SQL na jeden vzdálený Azure SQL Database nebo sadu databází, které slouží jako horizontální oddíly takovým vodorovné schéma vytváření oddílů.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
