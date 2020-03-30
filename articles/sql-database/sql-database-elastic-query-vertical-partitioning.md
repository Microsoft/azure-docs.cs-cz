---
title: Dotazování napříč cloudovými databázemi s různým schématem
description: jak nastavit dotazy mezi databázemi přes svislé oddíly
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: d5983d25685242a696300f293231bbf987e8442d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823732"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Dotaz napříč cloudovými databázemi s různými schématy (náhled)

![Dotazování napříč tabulkami v různých databázích][1]

Databáze s vztyčně rozdělenými oddíly používají různé sady tabulek v různých databázích. To znamená, že schéma se liší v různých databázích. Například všechny tabulky pro zásoby jsou v jedné databázi, zatímco všechny tabulky související s účtováním jsou na druhé databázi. 

## <a name="prerequisites"></a>Požadavky

* Uživatel musí mít oprávnění ALTER any external data source. Toto oprávnění je součástí oprávnění ALTER DATABASE.
* ZMĚNIT všechna oprávnění externího zdroje dat jsou potřebné k odkazování na základní zdroj dat.

## <a name="overview"></a>Přehled

> [!NOTE]
> Na rozdíl od horizontální dělení tyto příkazy DDL nezávisí na definování datové vrstvy s mapou horizontálního oddílu prostřednictvím knihovny klienta elastické databáze.
>

1. [VYTVOŘIT HLAVNÍ KLÍČ](https://msdn.microsoft.com/library/ms174382.aspx)
2. [Vytvořit pověření s rozsahem databáze](https://msdn.microsoft.com/library/mt270260.aspx)
3. [VYTVOŘENÍ EXTERNÍHO ZDROJE DAT](https://msdn.microsoft.com/library/dn935022.aspx)
4. [VYTVOŘIT EXTERNÍ TABULKU](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Vytvoření hlavního klíče a pověření s oborem databáze

Pověření se používá elastické dotaz pro připojení ke vzdáleným databázím.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Ujistěte `<username>` se, že neobsahuje žádnou příponu **\@"název_serveru".** 
>

## <a name="create-external-data-sources"></a>Vytvoření externích zdrojů dat

Syntaxe:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> Parametr TYPE musí být nastaven na **RDBMS**. 
>

### <a name="example"></a>Příklad

Následující příklad ilustruje použití příkazu CREATE pro externí zdroje dat. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Chcete-li načíst seznam aktuálních externích zdrojů dat: 

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

### <a name="example"></a>Příklad

```sql
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
```

Následující příklad ukazuje, jak načíst seznam externích tabulek z aktuální databáze: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Poznámky

Elastický dotaz rozšiřuje existující syntaxi externí tabulky a definuje externí tabulky, které používají externí zdroje dat typu RDBMS. Externí definice tabulky pro vertikální dělení zahrnuje následující aspekty: 

* **Schéma**: Externí tabulka DDL definuje schéma, které mohou použít vaše dotazy. Schéma poskytované v definici externí tabulky musí odpovídat schématu tabulek ve vzdálené databázi, kde jsou uložena skutečná data. 
* **Odkaz na vzdálenou databázi**: Externí tabulka DDL odkazuje na externí zdroj dat. Externí zdroj dat určuje název serveru SQL Database a název databáze vzdálené databáze, ve které jsou uložena skutečná data tabulky. 

Syntaxe pro vytvoření externích tabulek při použití externího zdroje dat, jak je uvedeno v předchozí části, je následující: 

Klauzule DATA_SOURCE definuje externí zdroj dat (tj. vzdálenou databázi v případě vertikálního dělení), který se používá pro externí tabulku.  

Klauzule SCHEMA_NAME a OBJECT_NAME poskytují možnost mapovat definici externí tabulky na tabulku v jiném schématu ve vzdálené databázi nebo na tabulku s jiným názvem. To je užitečné, pokud chcete definovat externí tabulku zobrazení katalogu nebo DMV ve vzdálené databázi - nebo jakékoli jiné situace, kdy je název vzdálené tabulky již místně.  

Následující příkaz DDL klesne existující definici externí tabulky z místního katalogu. Nemá vliv na vzdálenou databázi. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Oprávnění pro vytvořit/drop externí tabulka:** ZMĚNIT všechna oprávnění externí zdroj dat jsou potřebné pro externí tabulky DDL, který je také potřeba odkazovat na základní zdroj dat.  

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Uživatelé s přístupem k externí tabulce automaticky získají přístup k podkladovým vzdáleným tabulkám pod pověřením uvedeným v definici externího zdroje dat. Měli byste pečlivě spravovat přístup k externí tabulce, aby se zabránilo nežádoucímu zvýšení oprávnění prostřednictvím pověření externího zdroje dat. Běžná oprávnění SQL lze použít k udělení nebo odvolání přístupu k externí tabulce, stejně jako by se jednalo o běžnou tabulku.  

## <a name="example-querying-vertically-partitioned-databases"></a>Příklad: dotazování vertikálně rozdělených databází

Následující dotaz provádí třícestné spojení mezi dvěma místními tabulkami pro objednávky a řádky objednávky a vzdálenou tabulkou pro zákazníky. Toto je příklad případu použití referenčních dat pro elastický dotaz: 

```sql
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
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Uložená procedura pro vzdálené\_spuštění T-SQL: sp execute_remote

Elastický dotaz také zavádí uloženou proceduru, která poskytuje přímý přístup ke vzdálené databázi. Uložená procedura se nazývá [sp\_spustit \_vzdálené](https://msdn.microsoft.com/library/mt703714) a lze jej použít ke spuštění vzdálené uložené procedury nebo T-SQL kód ve vzdálené databázi. To trvá následující parametry: 

* Název zdroje dat (nvarchar): Název externího zdroje dat typu RDBMS. 
* Dotaz (nvarchar): Dotaz T-SQL, který má být proveden ve vzdálené databázi. 
* Deklarace parametru (nvarchar) - volitelné: Řetězec s definicemi datového typu pro parametry použité v parametru Dotaz (například sp_executesql). 
* Seznam hodnot parametrů - volitelné: Seznam hodnot parametrů oddělených čárkami (například sp_executesql).

Vzdálené\_spuštění\_sp používá externí zdroj dat uvedený v parametrech vyvolání k provedení daného příkazu T-SQL ve vzdálené databázi. Používá pověření externího zdroje dat pro připojení ke vzdálené databázi.  

Příklad: 

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 
```

## <a name="connectivity-for-tools"></a>Konektivita pro nástroje

Běžné připojovací řetězce serveru SQL Server můžete použít k připojení nástrojů pro integraci BI a dat k databázím na serveru SQL DB, který má povolený elastický dotaz a jsou definovány externí tabulky. Ujistěte se, že SQL Server je podporován jako zdroj dat pro váš nástroj. Potom odkazovat na databázi elastických dotazů a jeho externí tabulky, stejně jako všechny ostatní sql server databáze, které by se připojit pomocí nástroje. 

## <a name="best-practices"></a>Osvědčené postupy

* Ujistěte se, že databáze koncového bodu elastického dotazu získala přístup ke vzdálené databázi povolením přístupu pro služby Azure services v konfiguraci brány firewall SQL DB. Také se ujistěte, že pověření uvedená v definici externího zdroje dat se mohou úspěšně přihlásit do vzdálené databáze a mají oprávnění pro přístup ke vzdálené tabulce.  
* Elastický dotaz funguje nejlépe pro dotazy, kde většinu výpočtu lze provést ve vzdálených databázích. Obvykle získáte nejlepší výkon dotazu s predikáty selektivnífiltr, které mohou být vyhodnoceny na vzdálené databáze nebo spojení, které lze provést zcela na vzdálené databázi. Jiné vzorky dotazu může být nutné načíst velké množství dat ze vzdálené databáze a může pracovat špatně. 

## <a name="next-steps"></a>Další kroky

* Přehled elastického dotazu najdete v [tématu Přehled elastického dotazu](sql-database-elastic-query-overview.md).
* Kurz vertikálního dělení naleznete v [tématu Začínáme s dotazem mezi databázemi (vertikální dělení)](sql-database-elastic-query-getting-started-vertical.md).
* Kurz horizontálního dělení (horizontálního dělení) naleznete [v tématu Začínáme s elastickým dotazem pro horizontální dělení (horizontální dělení)](sql-database-elastic-query-getting-started.md).
* Syntaxe a ukázkové dotazy pro horizontálně dělená data naleznete v [tématu Dotazování horizontálně dělených dat)](sql-database-elastic-query-horizontal-partitioning.md)
* Viz [\_sp \_spustit vzdálené](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, která provede příkaz Transact-SQL na jednom vzdáleném Azure SQL Database nebo sadu databází, které slouží jako horizontální oddíly v horizontálním dělení schéma.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
