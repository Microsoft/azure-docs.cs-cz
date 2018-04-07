---
title: Dotaz mezi databázemi cloudu s jinou schématu | Microsoft Docs
description: jak nastavit mezidatabázové dotazy přes svislé oddíly
services: sql-database
manager: craigg
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: mlandzic
ms.openlocfilehash: 243110e47ab7c98c9fec9b2747fde73ccb775fbc
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Dotazování mezi databází cloudu s různými schématy (preview)
![Dotazování mezi tabulkami v různých databází][1]

Svisle oddíly databáze používají různé sady tabulek na různých databází. To znamená, že schéma se liší v různých databází. Například všechny tabulky pro inventář je na jednu databázi, zatímco všechny tabulky související s monitorování účtů na druhý databáze. 

## <a name="prerequisites"></a>Požadavky
* Uživatel musí mít oprávnění ALTER ANY EXTERNAL DATA SOURCE. Toto oprávnění je součástí oprávnění ALTER DATABASE.
* K odkazování na podkladový zdroj dat jsou potřeba oprávnění ALTER ANY EXTERNAL DATA SOURCE.

## <a name="overview"></a>Přehled

> [!NOTE]
> Na rozdíl od s vodorovné rozdělení do oddílů, tyto příkazy DDL nezávisí na definování datové vrstvy s mapou horizontálního oddílu pomocí klientské knihovny pro elastické databáze.
>

1. [VYTVOŘENÍ HLAVNÍHO KLÍČE](https://msdn.microsoft.com/library/ms174382.aspx)
2. [VYTVOŘENÍ DATABÁZE OBOR PŘIHLAŠOVACÍCH ÚDAJŮ](https://msdn.microsoft.com/library/mt270260.aspx)
3. [VYTVOŘENÍ EXTERNÍHO ZDROJE DAT](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREATE EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Vytvořit hlavní klíč databáze obor a přihlašovací údaje
Přihlašovací údaje se používá v elastické dotazu pro připojení k vzdálené databáze.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Ujistěte se, že `<username>` neobsahuje žádné **"@servername"** příponu. 
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
> Parametr typu musí být nastaven na **RDBMS**. 
>

### <a name="example"></a>Příklad:
Následující příklad ukazuje použití příkazu vytvořit pro externí zdroje dat. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Načíst seznam aktuálních zdrojů dat externí: 

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

Následující příklad ukazuje, jak načíst seznam externí tabulky z aktuální databáze: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Poznámky
Elastické dotazu rozšiřuje stávající syntaxe externí tabulky můžete definovat externí tabulky, které používají externí zdroje dat typu relační. Definici externí tabulky pro vertikální dělení zahrnuje následující aspekty: 

* **Schéma**: externí tabulky DDL definuje schéma, které můžete své dotazy. Schéma, které jsou součástí vaší definici externí tabulky musí odpovídat schématu tabulek v databázi vzdáleného se uloží skutečná data. 
* **Odkaz na vzdálené databáze**: DDL odkazuje na externí zdroj dat externí tabulky. Externí zdroj dat Určuje název logického serveru a název databáze vzdálenou databázi se uloží na skutečná data tabulky. 

Pomocí externího zdroje dat, jak je uvedeno v předchozí části, syntaxe k vytvoření externí tabulky je následující: 

V klauzuli DATA_SOURCE definuje zdroj externích dat (tj. vzdálené databáze v případě vertikální dělení), který se používá pro externí tabulky.  

Klauzule SCHEMA_NAME a OBJECT_NAME poskytují možnost mapovat definici externí tabulky do tabulky v jiné schéma na vzdálenou databázi nebo tabulku s jiným názvem, v uvedeném pořadí. To je užitečné, pokud chcete definovat externí tabulky k zobrazení katalogu nebo DMV na vzdálené databáze - nebo jakékoliv jiné situaci, kde název vzdálené tabulky je už zabraný místně.  

Následující příkaz DDL zahodí existující definici externí tabulky z místního katalogu. Nemělo vliv vzdálené databáze. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Oprávnění pro příkaz CREATE/DROP externí tabulky**: pro externí tabulky DDL, který je také nutný k odkazování na podkladový zdroj dat jsou potřeba oprávnění ALTER ANY EXTERNAL DATA SOURCE.  

## <a name="security-considerations"></a>Aspekty zabezpečení
Uživatelé s přístupem k externí tabulky automaticky získají přístup k základní vzdálených tabulek v části přihlašovací údaje zadané v definici zdrojové externí data. Aby se zabránilo nežádoucí zvýšení oprávnění pomocí přihlašovacích údajů z externího zdroje dat. měli pečlivě spravovat přístup k externí tabulky. Regulární SQL oprávnění lze udělit nebo ODVOLAT přístup k externí tabulku stejně, jako by šlo o běžnou tabulku.  

## <a name="example-querying-vertically-partitioned-databases"></a>Příklad: dotazování svisle na oddíly databáze
Následující dotaz provede třícestný spojení mezi dvěma místní tabulky pro objednávky a řádky určené pořadí a Vzdálená tabulka pro zákazníky. Jedná se o příklad případu použití dat odkaz pro elastické dotaz: 

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


## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Uložené procedury pro vzdálené spuštění T-SQL: sp\_execute_remote
Elastické dotazu také zavádí uložené procedury, která poskytuje přímý přístup ke vzdálené databázi. Uložená procedura je volána [sp\_provést \_vzdáleného](https://msdn.microsoft.com/library/mt703714) a můžete použít ke spuštění vzdálené uložené procedury nebo kód T-SQL na vzdálené databáze. Ji používá následující parametry: 

* Název zdroje dat (nvarchar): název zdroje externích dat typu relační. 
* Dotaz (nvarchar): na vzdálené databáze provedení dotazu T-SQL. 
* Deklarace parametru (nvarchar) - volitelné: řetězec s definice typu dat pro parametry použité v parametru dotazu (např. sp_executesql). 
* Seznam hodnot parametru - volitelné: čárkami oddělený seznam hodnot parametrů (např. sp_executesql).

Sp\_provést\_vzdálené používá externí zdroj dat součástí Parametry vyvolání daný příkaz T-SQL nelze provést na vzdálené databáze. Přihlašovací údaje z externí zdroj dat používá pro připojení ke vzdálené databázi.  

Příklad: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 



## <a name="connectivity-for-tools"></a>Připojení nástroje
Regulární připojovací řetězce SQL Server můžete použít pro připojení k databázím na databázi SQL serveru, který má povolené elastické dotazu a externí tabulky definované vaše integrace nástrojů BI a data. Ujistěte se, že systém SQL Server je podporovaný jako zdroj dat pro vaše nástroje. Potom se podívejte elastické dotaz do databáze a její externí tabulky stejně jako jiné databázi systému SQL Server, který by se připojit k vaší nástrojem. 

## <a name="best-practices"></a>Osvědčené postupy
* Ujistěte se, že koncový bod databáze elastické dotazu udělen přístup ke vzdálené databázi povolením přístupu pro služby Azure v konfiguraci brány firewall SQL DB. Zkontrolujte také, že zadané v definici zdrojové externích dat přihlašovací údaje může úspěšně přihlásit do vzdálené databáze a má oprávnění pro přístup k vzdálené tabulce.  
* Elastické dotazu je nejvhodnější pro dotazy kde lze provést většinu výpočet na vzdálené databáze. Obvykle získáte nejlepší výkon dotazů s predikáty selektivní filtru, které lze vyhodnotit na vzdálené databáze nebo spojení, které lze provést zcela na vzdálené databáze. Ostatní typy dotazů možná muset načíst velké objemy dat od vzdálené databáze a může být špatná. 

## <a name="next-steps"></a>Další postup

* Přehled elastické dotazů najdete v tématu [elastické dotazu přehled](sql-database-elastic-query-overview.md).
* Vertikální dělení kurzu, najdete v části [Začínáme s mezidatabázové dotazu (vertikální dělení)](sql-database-elastic-query-getting-started-vertical.md).
* Podívejte se kurz vodorovné rozdělení do oddílů (horizontálního dělení) [Začínáme s elastické dotazu pro vodorovné rozdělení do oddílů (horizontálního dělení)](sql-database-elastic-query-getting-started.md).
* Syntaxe a ukázkové dotazy pro horizontálně dělenou data, najdete v části [dotazování vodorovně rozdělena na oddíly dat)](sql-database-elastic-query-horizontal-partitioning.md)
* V tématu [sp\_provést \_vzdáleného](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, který spouští příkaz jazyka Transact-SQL na jedné vzdálené databáze SQL Azure nebo sadu databází, které slouží jako horizontálních oddílů v vodorovné schéma rozdělení oddílů.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
