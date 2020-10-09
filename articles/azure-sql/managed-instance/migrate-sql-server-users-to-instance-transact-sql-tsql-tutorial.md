---
title: Migrace SQL Server uživatelů a skupin systému Windows do spravované instance SQL pomocí T-SQL
description: Přečtěte si, jak migrovat uživatele a skupiny Windows v instanci SQL Server do spravované instance Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: f2dd34ab7c6ee5be26836e4abb86960605ee44ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84708645"
---
# <a name="tutorial-migrate-windows-users-and-groups-in-a-sql-server-instance-to-azure-sql-managed-instance-using-t-sql-ddl-syntax"></a>Kurz: Migrace uživatelů a skupin systému Windows v instanci SQL Server do spravované instance Azure SQL pomocí syntaxe jazyka T-SQL DDL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

> [!NOTE]
> Syntaxe, která se používá k migraci uživatelů a skupin do spravované instance SQL v tomto článku, je ve **verzi Public Preview**.

Tento článek vás provede procesem migrace místních uživatelů a skupin systému Windows ve vašem SQL Server do spravované instance Azure SQL pomocí syntaxe T-SQL.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvoření přihlašovacích údajů pro SQL Server
> - Vytvoření testovací databáze pro migraci
> - Vytváření přihlašovacích údajů, uživatelů a rolí
> - Zálohování a obnovení databáze do spravované instance SQL (MI)
> - Ruční migrace uživatelů na MI pomocí příkazu ALTER USER syntax
> - Testování ověřování pomocí nových mapovaných uživatelů

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu platí následující předpoklady:

- Doména Windows je federované s Azure Active Directory (Azure AD).
- Přístup ke službě Active Directory pro vytváření uživatelů nebo skupin.
- Existující SQL Server v místním prostředí.
- Existující spravovaná instance SQL. Viz [rychlý Start: vytvoření spravované instance SQL](instance-create-quickstart.md).
  - `sysadmin`K vytvoření přihlašovacích údajů Azure AD se musí použít ve spravované instanci SQL.
- [Vytvořte správce Azure AD pro spravovanou instanci SQL](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).
- V rámci vaší sítě se můžete připojit ke spravované instanci SQL. Další informace najdete v následujících článcích:
  - [Připojení aplikace ke službě Azure SQL Managed Instance](connect-application-instance.md)
  - [Rychlý Start: Konfigurace připojení typu Point-to-site k spravované instanci Azure SQL z místního prostředí](point-to-site-p2s-configure.md)
  - [Konfigurace veřejného koncového bodu ve spravované instanci Azure SQL](public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>Syntaxe jazyka T-SQL DDL

Níže uvádíme syntaxi T-SQL DDL, která slouží k podpoře migrace uživatelů a skupin systému Windows z SQL Server instance do spravované instance SQL pomocí ověřování Azure AD.

```sql
-- For individual Windows users with logins
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com];

--For individual groups with logins
ALTER USER [domainName\groupName] WITH LOGIN=[groupName]
```

## <a name="arguments"></a>Argumenty

_domainName_</br>
Určuje název domény uživatele.

_Jmen_</br>
Určuje jméno uživatele identifikovaného v rámci databáze.

_= loginName \@ domainName.com_</br>
Přemapuje uživatele na přihlašovací údaje služby Azure AD.

_Parametr_</br>
Určuje název skupiny identifikované v rámci databáze.

## <a name="part-1-create-logins-in-sql-server-for-windows-users-and-groups"></a>Část 1: vytvoření přihlášení v SQL Server pro uživatele a skupiny systému Windows

> [!IMPORTANT]
> Následující syntaxe vytvoří uživatele a přihlášení skupiny ve vašem SQL Server. Před provedením níže uvedené syntaxe se musíte ujistit, že uživatel a skupina existují v rámci služby Active Directory (AD). </br> </br>
> Uživatelé: testUser1, testGroupUser </br>
> Skupina: migrace – testGroupUser musí patřit do skupiny migrace ve službě AD.

Následující příklad vytvoří přihlášení v SQL Server pro účet s názvem _testUser1_ v _aadsqlmi_domény.

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases

use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;
go;

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser].
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;
go;


-- Check logins were created
select * from sys.server_principals;
go;
```

Vytvořte databázi pro tento test.

```sql
-- Create a database called [migration]
create database migration
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Část 2: vytvoření uživatelů a skupin systému Windows a přidání rolí a oprávnění

Pomocí následující syntaxe vytvořte testovacího uživatele.

```sql
use migration;  
go

-- Create Windows user [aadsqlmi\testUser1] with login
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1];
go
```

Ověřte oprávnění uživatele:

```sql
-- Check the user in the Metadata
select * from sys.database_principals;
go

-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions;
go
```

Vytvořte roli a přiřaďte k této roli testovacího uživatele:

```sql
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole;
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole;
go

alter role UserMigrationRole add member [aadsqlmi\testUser1];
go
```

Pomocí následujícího dotazu Zobrazte uživatelská jména přiřazená konkrétní roli:

```sql
-- Display user name assigned to a specific role
SELECT DP1.name AS DatabaseRoleName,
   isnull (DP2.name, 'No members') AS DatabaseUserName
 FROM sys.database_role_members AS DRM
 RIGHT OUTER JOIN sys.database_principals AS DP1
   ON DRM.role_principal_id = DP1.principal_id
 LEFT OUTER JOIN sys.database_principals AS DP2
   ON DRM.member_principal_id = DP2.principal_id
WHERE DP1.type = 'R'
ORDER BY DP1.name;
```

Pomocí následující syntaxe vytvořte skupinu. Pak přidejte skupinu do role `db_owner` .

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration];
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration';
go

--Check the db_owner role for 'aadsqlmi\migration' group
select is_rolemember('db_owner', 'aadsqlmi\migration')
go
-- Output  ( 1 means YES)
```

Vytvořte testovací tabulku a přidejte nějaká data pomocí následující syntaxe:

```sql
-- Create a table and add data
create table test ( a int, b int);
go

insert into test values (1,10)
go

-- Check the table values
select * from test;
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-sql-managed-instance"></a>Část 3: zálohování a obnovení jednotlivých uživatelských databází do spravované instance SQL

Vytvořte zálohu databáze migrace pomocí článku [kopírování databází pomocí zálohování a obnovení](/sql/relational-databases/databases/copy-databases-with-backup-and-restore), nebo použijte následující syntaxi:

```sql
use master;
go
backup database migration to disk = 'C:\Migration\migration.bak';
go
```

Postupujte podle našeho [rychlého startu: obnovení databáze do spravované instance SQL](restore-sample-database-quickstart.md).

## <a name="part-4-migrate-users-to-sql-managed-instance"></a>4. část: Migrace uživatelů do spravované instance SQL

Provedením příkazu ALTER USER dokončíte proces migrace na spravované instanci SQL.

1. Přihlaste se ke spravované instanci SQL pomocí účtu správce Azure AD pro spravovanou instanci SQL. Pak vytvořte přihlašovací údaje služby Azure AD ve spravované instanci SQL pomocí následující syntaxe. Další informace najdete v tématu [kurz: zabezpečení spravované instance SQL v Azure SQL Database pomocí objektů zabezpečení serveru Azure AD (přihlášení)](aad-security-configure-tutorial.md).

    ```sql
    use master
    go

    -- Create login for AAD user [testUser1@aadsqlmi.net]
    create login [testUser1@aadsqlmi.net] from external provider
    go

    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net]
    create login [migration] from external provider
    go

    --Check the two new logins
    select * from sys.server_principals
    go
    ```

1. Ověřte, jestli je vaše migrace správné databáze, tabulky a objekty zabezpečení.

    ```sql
    -- Switch to the database migration that is already restored for MI
    use migration;
    go

    --Check if the restored table test exist and contain a row
    select * from test;
    go

    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals;
    go
    -- the old user aadsqlmi\testUser1 should be there
    -- the old group aadsqlmi\migration should be there
    ```

1. Pomocí syntaxe ALTER USER můžete mapovat místního uživatele na přihlašovací údaje služby Azure AD.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net];
    go

    -- Check the principal
    select * from sys.database_principals;
    go
    -- New user testUser1@aadsqlmi.net should be there instead
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    -- Check a specific role
    -- Display Db user name assigned to a specific role
    SELECT DP1.name AS DatabaseRoleName,
    isnull (DP2.name, 'No members') AS DatabaseUserName
    FROM sys.database_role_members AS DRM
    RIGHT OUTER JOIN sys.database_principals AS DP1
    ON DRM.role_principal_id = DP1.principal_id
    LEFT OUTER JOIN sys.database_principals AS DP2
    ON DRM.member_principal_id = DP2.principal_id
    WHERE DP1.type = 'R'
    ORDER BY DP1.name;
    ```

1. Pomocí syntaxe ALTER USER můžete mapovat místní skupinu na přihlášení k Azure AD.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration];
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals;
    go

    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    --Check the db_owner role for 'aadsqlmi\migration' user
    select is_rolemember('db_owner', 'migration')
    go
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>5. část: testování ověřování uživatelů nebo skupin Azure AD

Otestujte ověřování ve spravované instanci SQL pomocí dříve namapovaného uživatele na přihlašovací údaje Azure AD pomocí syntaxe ALTER USER.

1. Přihlaste se k federovanému virtuálnímu počítači pomocí předplatného Azure SQL Managed instance jako `aadsqlmi\testUser1`
1. Pomocí SQL Server Management Studio (SSMS) se přihlaste ke spravované instanci SQL pomocí **integrovaného ověřování služby Active Directory** a připojte se k databázi `migration` .
    1. Můžete se také přihlásit pomocí testUser1@aadsqlmi.net přihlašovacích údajů s možností SSMS **Active Directory – Universal s podporou vícefaktorového ověřování**. V tomto případě ale nemůžete použít mechanismus jednotného přihlašování a musíte zadat heslo. K přihlášení do spravované instance SQL nebudete muset použít federovaný virtuální počítač.
1. V rámci **výběru**člena role můžete vybrat z `test` tabulky.

    ```sql
    Select * from test  --  and see one row (1,10)
    ```

Otestujte ověřování u spravované instance SQL pomocí členu skupiny systému Windows `migration` . `aadsqlmi\testGroupUser`Před migrací by uživatel měl být přidán do skupiny `migration` .

1. Přihlaste se k federovanému virtuálnímu počítači pomocí předplatného Azure SQL Managed instance jako `aadsqlmi\testGroupUser`
1. Pomocí SSMS s **integrovaným ověřováním Active Directory** se připojte k serveru Azure SQL Managed instance a databázi. `migration`
    1. Můžete se také přihlásit pomocí testGroupUser@aadsqlmi.net přihlašovacích údajů s možností SSMS **Active Directory – Universal s podporou vícefaktorového ověřování**. V tomto případě ale nemůžete použít mechanismus jednotného přihlašování a musíte zadat heslo. K přihlášení do spravované instance SQL nebudete muset použít federovaný virtuální počítač.
1. V rámci `db_owner` role můžete vytvořit novou tabulku.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```

> [!NOTE]
> Kvůli známému problému s návrhem pro Azure SQL Database se příkaz CREATE TABLE, který je spuštěný jako člen skupiny, nezdaří s následující chybou: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> Aktuální alternativní řešení je vytvoření tabulky s existujícím schématem v případě výše <dbo. New>

## <a name="next-steps"></a>Další kroky

[Kurz: migrace SQL Server do Azure Managed instance v režimu offline pomocí DMS](../../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)
