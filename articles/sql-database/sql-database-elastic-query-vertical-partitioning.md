---
title: Dotaz přes cloudové databáze s různými schématy | Dokumentace Microsoftu
description: jak nastavit mezidatabázové dotazy nad vertikální oddíly
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 75c021f7b2c2584580f2d9dbf30cbcdf11d3fdc5
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875361"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Dotaz přes cloudové databáze s různými schématy (preview)
![Dotazování přes tabulky v různých databázích][1]

Vertikálně dělené databáze použít jinou sadu tabulek v různých databázích. To znamená, že schéma se liší v různých databázích. Například všechny tabulky inventáře jsou na jednu databázi, zatímco všechny tabulky související s monitorování účtů na druhém databáze. 

## <a name="prerequisites"></a>Požadavky
* Uživatel musí mít oprávnění ALTER ANY EXTERNAL DATA SOURCE. Toto oprávnění je součástí oprávnění ALTER DATABASE.
* K odkazování na podkladový zdroj dat jsou potřeba oprávnění ALTER ANY EXTERNAL DATA SOURCE.

## <a name="overview"></a>Přehled

> [!NOTE]
> Na rozdíl od s horizontální dělení, tyto příkazy jazyka DDL není závislý na definování datové vrstvě s mapy horizontálních oddílů pomocí Klientská knihovna elastic database.
>

1. [VYTVOŘTE HLAVNÍ KLÍČ](https://msdn.microsoft.com/library/ms174382.aspx)
2. [VYTVOŘENÍ DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [VYTVOŘENÍ EXTERNÍHO ZDROJE DAT](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREATE EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Vytvořte hlavní klíč s rozsahem databáze a přihlašovacích údajů
Přihlašovací údaje, které používá elastický dotaz pro připojení k vzdálené databáze.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Ujistěte se, že `<username>` neobsahuje žádné **"\@název_serveru"** příponu. 
>

## <a name="create-external-data-sources"></a>Vytvoření externích zdrojů dat.
Syntaxe:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> Parametr typu musí být nastaven na **relační databázový systém**. 
>

### <a name="example"></a>Příklad:
Následující příklad ukazuje použití příkazu CREATE pro externí zdroje dat. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Načíst seznam aktuální externí zdroje dat: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Externí tabulky
Syntaxe:

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 

    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Příklad:
    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 

Následující příklad ukazuje, jak načíst seznam externích tabulek z aktuální databáze: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Poznámky
Elastický dotaz rozšiřuje existující externí tabulky syntaxe pro definování externích tabulek, které používají externí zdroje dat typu relační databázový systém. Definici externí tabulky pro vertikální dělení zahrnuje následující aspekty: 

* **Schéma**: externí tabulky DDL definuje schéma, které můžete použít své dotazy. Zadané v definici externí tabulky schéma musí odpovídat schématu tabulky v vzdálenou databázi, kde je uložena skutečná data. 
* **Vzdálený databázový odkaz**: DDL odkazuje na externí zdroj dat externí tabulky. Externí zdroj dat Určuje název logického serveru a název databáze vzdálené databáze, kde je uložen na skutečná data tabulky. 

Pomocí externího zdroje dat, jak je uvedeno v předchozí části, se syntaxí pro vytvoření externí tabulky, které vypadá takto: 

V klauzuli DATA_SOURCE definuje externí zdroj dat (tj. Vzdálená databáze v případě vertikálního dělení), který se používá pro externí tabulky.  

Klauzule SCHEMA_NAME a OBJECT_NAME poskytují možnost namapovat definici externí tabulky do tabulky v jiné schéma na vzdálenou databázi, nebo na tabulku s jiným názvem, v uvedeném pořadí. To je užitečné, pokud chcete definovat externí tabulky pro zobrazení katalogu nebo zobrazení dynamické správy na vzdálené databáze - nebo jakékoliv jiné situaci, kde název vzdálené tabulky se už používá místně.  

Následující příkaz DDL zahodí existující definici externí tabulky z místního katalogu. To ale nijak neovlivní vzdálené databáze. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Oprávnění pro příkaz CREATE/DROP externí tabulky**: pro externí tabulky DDL, který je také nutný k odkazování na podkladový zdroj dat jsou potřeba oprávnění ALTER ANY EXTERNAL DATA SOURCE.  

## <a name="security-considerations"></a>Aspekty zabezpečení
Uživatelé s přístupem k externí tabulky automaticky získáte přístup k podkladové vzdálených tabulek v části přihlašovací údaje zadané v definici zdroje externí data. Pokud se chcete vyhnout nechtěné zvýšení oprávnění prostřednictvím přihlašovací údaje z externí zdroj dat měli pečlivě spravujete přístup k externí tabulky. Pravidelné SQL oprávnění lze udělit nebo ODVOLAT přístup k externí tabulky, stejně, jako by šlo o běžnou tabulku.  

## <a name="example-querying-vertically-partitioned-databases"></a>Příklad: dotazování na vertikálně dělené databáze
Následující dotaz spojí trojcestných dvě místní tabulky objednávky a řádky a vzdálenou tabulku pro zákazníky. Toto je příklad způsobu použití dat odkaz pro dotaz na elastic: 

    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100


## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Uložené procedury pro vzdálené spuštění T-SQL: aktualizace sp\_execute_remote
Elastický dotaz také zavádí uloženou proceduru, která poskytuje přímý přístup ke vzdálené databázi. Volání uložené procedury [sp\_provést \_vzdálené](https://msdn.microsoft.com/library/mt703714) a je možné ke spouštění uložené procedury vzdálený nebo kód T-SQL na vzdálené databáze. Ji používá následující parametry: 

* Název zdroje dat (nvarchar): název externí zdroj dat typu relační databázový systém. 
* Dotaz (nvarchar): dotaz T-SQL k provedení na vzdálené databáze. 
* Deklarace parametru (nvarchar) – volitelné: řetězec s definice typu dat pro parametry použité v parametru dotazu (např. sp_executesql). 
* Seznam hodnot parametru - volitelné: čárkami oddělený seznam hodnot parametrů (jako je sp_executesql).

Sp\_provést\_vzdálené externí zdroj dat součástí Parametry vyvolání používá ke spouštění daný příkaz T-SQL na vzdálené databáze. Přihlašovací údaje z externí zdroj dat používá pro připojení ke vzdálené databázi.  

Příklad: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 



## <a name="connectivity-for-tools"></a>Připojení nástroje
Můžete použít regulární systému SQL Server připojovací řetězce k připojení k databázím na serveru SQL DB, který má povolené elastický dotaz a externích tabulek definovaných integrace nástroje pro BI a data. Ujistěte se, že systém SQL Server je podporovaný jako zdroj dat pro nástroj. Potom použijte dotaz na elastic database a její externí tabulky, stejně jako jakékoli jiné databáze systému SQL Server, který by se připojit s nástrojem. 

## <a name="best-practices"></a>Osvědčené postupy
* Ujistěte se, že databázi elastický dotaz koncový bod má byl udělen přístup ke vzdálené databázi tím, že umožňuje přístup pro služby Azure ve své konfiguraci brány firewall databáze SQL. Ujistěte se také, že zadané v definici zdroje dat externí přihlašovací údaje můžete úspěšně přihlásit do vzdálené databáze a má oprávnění pro přístup k vzdálené tabulky.  
* Elastický dotaz je nejvhodnější pro dotazy kde většinu výpočtu lze na vzdálené databáze. Obvykle získáte nejlepší výkon dotazů pomocí predikátů selektivní filtru, které lze vyhodnotit na vzdálené databáze nebo spojení, které lze provést zcela na vzdálené databáze. Další vzory dotazů může potřebovat načítání velkých objemů dat od vzdálené databáze a může provádět špatně. 

## <a name="next-steps"></a>Další postup

* Přehled elastický dotaz, naleznete v tématu [elastický dotaz přehled](sql-database-elastic-query-overview.md).
* Vertikální dělení kurz najdete v tématu [Začínáme s mezidatabázovými dotazy (vertikální oddíly)](sql-database-elastic-query-getting-started-vertical.md).
* Horizontální dělení (sharding) kurz najdete v tématu [Začínáme se službou elastický dotaz pro horizontální dělení (sharding)](sql-database-elastic-query-getting-started.md).
* Syntaxe a ukázkové dotazy pro horizontálně dělená data, najdete v části [dotazování na horizontálně dělené data)](sql-database-elastic-query-horizontal-partitioning.md)
* Naleznete v tématu [sp\_provést \_vzdálené](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, která provádí příkaz jazyka Transact-SQL na jeden vzdálený Azure SQL Database nebo sadu databází, které slouží jako horizontální oddíly takovým vodorovné schéma vytváření oddílů.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
