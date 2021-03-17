---
title: Dotaz napříč cloudovou databází s jiným schématem
description: jak nastavit databázové dotazy přes vertikální oddíly
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: c507a4c618713ba83d25b9defa918092db1a3c8e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792085"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Dotazování napříč cloudovou databází s různými schématy (Preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![Dotaz napříč tabulkami v různých databázích][1]

Vertikálně dělené databáze používají různé sady tabulek v různých databázích. To znamená, že schéma se liší v různých databázích. Například všechny tabulky pro inventář jsou v jedné databázi, zatímco všechny tabulky související s monitorováním účtů jsou v druhé databázi.

## <a name="prerequisites"></a>Předpoklady

* Uživatel musí mít oprávnění změnit všechna externí zdrojová DATA. Toto oprávnění je součástí oprávnění ALTER DATABASE.
* Aby bylo možné odkazovat na podkladový zdroj dat, je třeba změnit všechna oprávnění ke zdroji externích dat.

## <a name="overview"></a>Přehled

> [!NOTE]
> Na rozdíl od horizontálního dělení nejsou tyto příkazy DDL závislé na definování datové vrstvy s mapou horizontálních oddílů prostřednictvím klientské knihovny elastické databáze.
>

1. [VYTVOŘIT HLAVNÍ KLÍČ](/sql/t-sql/statements/create-master-key-transact-sql)
2. [VYTVOŘIT DATABÁZI S ROZSAHEM POVĚŘENÍ](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [VYTVOŘIT EXTERNÍ ZDROJ DAT](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [VYTVOŘIT EXTERNÍ TABULKU](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="create-database-scoped-master-key-and-credentials"></a>Vytvoření hlavního klíče a přihlašovacích údajů s rozsahem databáze

Pověření je používáno elastickým dotazem pro připojení ke vzdáleným databázím.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> Zajistěte, aby `<username>` neobsahovala žádnou příponu **" \@ servername"** .

## <a name="create-external-data-sources"></a>Vytvoření externích zdrojů dat

Syntaxe:

<External_Data_Source>:: = vytvořit externí zdroj dat <data_source_name> WITH (TYPE = RDBMS, LOCATION = ' <fully_qualified_server_name> ', DATABASE_NAME = ' <remote_database_name> ',  
    CREDENTIAL = <credential_name>) [;]

> [!IMPORTANT]
> Parametr typu musí být nastaven na **RDBMS** .

### <a name="example"></a>Příklad

Následující příklad ilustruje použití příkazu CREATE pro externí zdroje dat.

```sql
CREATE EXTERNAL DATA SOURCE RemoteReferenceData
   WITH
      (
         TYPE=RDBMS,
         LOCATION='myserver.database.windows.net',
         DATABASE_NAME='ReferenceData',
         CREDENTIAL= SqlUser
      );
```

Načtení seznamu aktuálních externích zdrojů dat:

```sql
select * from sys.external_data_sources;
```

### <a name="external-tables"></a>Externí tabulky

Syntaxe:

VYTVOŘENÍ externí tabulky [database_name. [schema_name]. | schema_name. ] table_name  
    ({<column_definition>} [,... n]) {WITH (<rdbms_external_table_options>)}) [;]

<rdbms_external_table_options>:: = DATA_SOURCE = <External_Data_Source>, [SCHEMA_NAME = N ' nonescaped_schema_name ',] [OBJECT_NAME = N ' nonescaped_object_name ',]

### <a name="example"></a>Příklad

```sql
CREATE EXTERNAL TABLE [dbo].[customer]
   (
      [c_id] int NOT NULL,
      [c_firstname] nvarchar(256) NULL,
      [c_lastname] nvarchar(256) NOT NULL,
      [street] nvarchar(256) NOT NULL,
      [city] nvarchar(256) NOT NULL,
      [state] nvarchar(20) NULL,
      DATA_SOURCE = RemoteReferenceData
   );
```

Následující příklad ukazuje, jak načíst seznam externích tabulek z aktuální databáze:

```sql
select * from sys.external_tables;
```

### <a name="remarks"></a>Poznámky

Elastický dotaz rozšiřuje existující syntax externí tabulky a definuje externí tabulky, které používají externí zdroje dat typu RDBMS. Definice externí tabulky pro vertikální dělení zahrnuje následující aspekty:

* **Schéma** : DDL externí tabulky definuje schéma, které mohou vaše dotazy použít. Schéma zadané v definici externí tabulky musí odpovídat schématu tabulek ve vzdálené databázi, kde jsou uložena skutečná data.
* **Odkaz na vzdálenou databázi** : instance DDL externí tabulky odkazuje na externí zdroj dat. Externí zdroj dat určuje název serveru a databázi vzdálené databáze, ve které jsou uložena skutečná data tabulky.

Pomocí externího zdroje dat, jak je uvedeno v předchozí části, syntaxe pro vytvoření externích tabulek je následující:

Klauzule DATA_SOURCE definuje externí zdroj dat (tj. vzdálenou databázi v případě vertikálního dělení), který se používá pro externí tabulku.  

Klauzule SCHEMA_NAME a OBJECT_NAME poskytují možnost mapovat definici externí tabulky na tabulku v jiném schématu na vzdálené databázi nebo na tabulku s jiným názvem, v uvedeném pořadí. To je užitečné, pokud chcete definovat externí tabulku do zobrazení katalogu nebo DMV ve vzdálené databázi nebo v jakékoli jiné situaci, kdy je název vzdálené tabulky již v místním prostředí.  

Následující příkaz DDL přenechá existující definici externí tabulky z místního katalogu. Nemá vliv na vzdálenou databázi.

```sql
DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  
```

**Oprávnění pro možnost vytvořit/vyřadit externí tabulku** : Změňte všechna oprávnění externích zdrojů dat, která jsou zapotřebí pro externí tabulku DDL, což je také nutné pro odkazování na podkladový zdroj dat.  

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Uživatelé s přístupem k externí tabulce automaticky získají přístup k podkladovým vzdáleným tabulkám v rámci přihlašovacích údajů uvedených v definici externího zdroje dat. Měli byste pečlivě spravovat přístup k externí tabulce, abyste se vyhnuli nežádoucímu zvýšení oprávnění prostřednictvím přihlašovacích údajů externího zdroje dat. Pravidelná oprávnění SQL je možné použít k udělení nebo odvolání přístupu k externí tabulce stejným způsobem, jako by šlo o běžnou tabulku.  

## <a name="example-querying-vertically-partitioned-databases"></a>Příklad: dotazování na vertikálně dělené databáze

Následující dotaz provádí obousměrné spojení mezi dvěma místními tabulkami pro objednávky a řádky objednávky a pro zákazníky ve vzdálené tabulce. Toto je příklad případu použití referenčních dat pro elastický dotaz:

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

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Uložená procedura pro vzdálené spuštění T-SQL: SP \_ execute_remote

Elastický dotaz také zavádí uloženou proceduru, která poskytuje přímý přístup ke vzdálené databázi. Uložená procedura se nazývá [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) a dá se použít ke spouštění vzdálených uložených procedur nebo kódu T-SQL na vzdálené databázi. Má následující parametry:

* Název zdroje dat (nvarchar): název externího zdroje dat typu RDBMS.
* Dotaz (nvarchar): dotaz T-SQL, který má být spuštěn ve vzdálené databázi.
* Deklarace parametru (nvarchar) – volitelné: řetězec s definicemi datových typů pro parametry používané v parametru dotazu (například sp_executesql).
* Seznam hodnot parametrů – volitelné: čárkami oddělený seznam hodnot parametrů (například sp_executesql).

Metoda SP \_ Execute \_ Remote využívá externí zdroj dat, který je k dispozici v parametrech volání, ke spuštění daného příkazu T-SQL ve vzdálené databázi. Pro připojení ke vzdálené databázi používá přihlašovací údaje k externímu zdroji dat.  

Příklad:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Možnosti připojení pro nástroje

Pomocí běžných SQL Serverch připojovacích řetězců můžete připojit nástroje pro BI a integraci dat k databázím na serveru, na kterém je povolený elastický dotaz a na definovaných externích tabulkách. Ujistěte se, že je pro nástroj SQL Server podporována jako zdroj dat. Pak se podívejte na elastické databáze dotazů a jejich externí tabulky stejně jako na jinou SQL Server databázi, ke které se připojíte pomocí nástroje.

## <a name="best-practices"></a>Osvědčené postupy

* Zajistěte, aby byla databáze koncových bodů elastického dotazu udělena přístup ke vzdálené databázi povolením přístupu ke službám Azure ve své Azure SQL Database konfiguraci brány firewall. Také zajistěte, aby se přihlašovací údaje zadané v definici externího zdroje dat úspěšně přihlásily do vzdálené databáze a aby měla oprávnění pro přístup ke vzdálené tabulce.  
* Elastický dotaz funguje nejlépe pro dotazy, kde je možné provést většinu výpočtů ve vzdálených databázích. Obvykle získáte nejlepší výkon dotazu pomocí predikátů selektivního filtru, které lze vyhodnotit na vzdálených databázích nebo v rámci spojení, která lze na vzdálené databázi provést úplně. Jiné vzorce dotazů můžou potřebovat načíst velké objemy dat ze vzdálené databáze a může to mít špatně.

## <a name="next-steps"></a>Další kroky

* Přehled elastického dotazu najdete v tématu [Přehled elastického dotazu](elastic-query-overview.md).
* Kurz pro vertikální dělení najdete v tématu [Začínáme s mezidatabázovým dotazem (vertikální dělení)](elastic-query-getting-started-vertical.md).
* Kurz horizontálního dělení na oddíly (horizontálního dělení) najdete v tématu [Začínáme s elastickým dotazem pro horizontální dělení na oddíly (horizontálního dělení)](elastic-query-getting-started.md).
* Syntaxe a ukázkové dotazy pro horizontálně rozdělená data najdete v tématu [dotazování na horizontálně dělená data](elastic-query-horizontal-partitioning.md) .
* V tématu [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) pro uloženou proceduru, která provádí příkaz Transact-SQL na jednom vzdáleném Azure SQL Database nebo sadě databází, která slouží jako horizontálních oddílů ve vodorovném schématu dělení.

<!--Image references-->
[1]: ./media/elastic-query-vertical-partitioning/verticalpartitioning.png

<!--anchors-->