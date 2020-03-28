---
title: Migrace uživatelů a skupin sql windows do spravované instance pomocí T-SQL
description: Informace o migraci místních uživatelů a skupin systému Windows serveru SQL Server do spravované instance
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: 2c8d7252b4e4ca8caa465727c0d2328c4aafaefb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227922"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>Kurz: Migrace místních uživatelů a skupin systému Windows serveru SQL Server do spravované instance Azure SQL Database pomocí syntaxe T-SQL DDL

> [!NOTE]
> Syntaxe použitá k migraci uživatelů a skupin do spravované instance v tomto článku je ve **verzi Public Preview**.

Tento článek vás provede procesem migrace místních uživatelů a skupin systému Windows na serveru SQL Server na existující instanci spravované službou Azure SQL Database pomocí syntaxe T-SQL.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvoření přihlášení pro SQL Server
> - Vytvoření testovací databáze pro migraci
> - Vytváření přihlašovacích údajů, uživatelů a rolí
> - Zálohování a obnovení databáze do spravované instance (MI)
> - Ruční migrace uživatelů do MI pomocí syntaxe ALTER USER
> - Testování ověřování s novými namapovanými uživateli

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu platí následující požadavky:

- Doména Windows je federovaná pomocí Služby Azure Active Directory (Azure AD).
- Přístup ke službě Active Directory k vytvoření uživatelů nebo skupin.
- Existující SQL Server ve vašem místním prostředí.
- Existující spravovaná instance. Viz [Úvodní příručka: Vytvoření spravované instance Azure SQL Database](sql-database-managed-instance-get-started.md).
  - A `sysadmin` ve spravované instanci musí být použitk k vytvoření přihlášení Azure AD.
- [Vytvořte správce Azure AD pro spravovanou instanci](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).
- Ke spravované instanci se můžete připojit v rámci sítě. Další informace naleznete v následujících článcích: 
    - [Připojení aplikace ke spravované instanci Azure SQL Database](sql-database-managed-instance-connect-app.md)
    - [Úvodní příručka: Konfigurace připojení point-to-site ke spravované instanci Azure SQL Database ze místní hospo-](sql-database-managed-instance-configure-p2s.md)
    - [Konfigurace veřejného koncového bodu ve spravované instanci Azure SQL Database](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>Syntaxe DDL T-SQL

Níže je syntaxe T-SQL DDL používaná k podpoře místních uživatelů systému Windows sql serveru a migrace skupin do spravované instance pomocí ověřování Azure AD.

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>Argumenty

_Název_domény_</br>
Určuje název domény uživatele.

_Username_</br>
Určuje jméno uživatele identifikovaného uvnitř databáze.

_= loginName\@domainName.com_</br>
Přemapuje uživatele na přihlášení Azure AD

_Groupname_</br>
Určuje název skupiny identifikované uvnitř databáze.

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>Část 1: Vytvoření přihlášení pro místní uživatele a skupiny serveru SQL Server

> [!IMPORTANT]
> Následující syntaxe vytvoří přihlášení uživatele a skupiny na serveru SQL Server. Před spuštěním níže uvedené syntaxe se musíte ujistit, že uživatel a skupina existují ve službě Active Directory (AD). </br> </br>
> Uživatelé: testUser1, testGroupUser </br>
> Skupina: migrace - testGroupUser musí patřit do skupiny migrace ve ad

Níže uvedený příklad vytvoří přihlášení v SQL Server pro účet s názvem _testUser1_ pod doménou _aadsqlmi_. 

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

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Část 2: Vytvoření uživatelů a skupin systému Windows, přidání rolí a oprávnění

K vytvoření testovacího uživatele použijte následující syntaxi.

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

Zkontrolujte uživatelská oprávnění:

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

Vytvořte roli a přiřaďte testovacího uživatele k této roli:

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

Následující dotaz slouží k zobrazení uživatelských jmen přiřazených k určité roli:

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

K vytvoření skupiny použijte následující syntaxi. Potom přidejte skupinu `db_owner`do role .

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

Vytvořte testovací tabulku a přidejte některá data pomocí následující syntaxe:

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

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>Část 3: Zálohování a obnovení databáze jednotlivých uživatelů na spravovanou instanci

Vytvořte zálohu databáze migrace pomocí článku [Kopírovat databáze se zálohováním a obnovením](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)nebo použijte následující syntaxi:

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

Postupujte podle našeho [úvodního startu: Obnovení databáze do spravované instance](sql-database-managed-instance-get-started-restore.md).

## <a name="part-4-migrate-users-to-managed-instance"></a>Část 4: Migrace uživatelů do spravované instance

> [!NOTE]
> Správce Azure AD pro funkce spravované instance po změně vytvoření. Další informace najdete [v tématu Nové funkce správce Azure AD pro MI](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi).

Spusťte příkaz ALTER USER k dokončení procesu migrace na spravované instanci.

1. Přihlaste se ke spravované instanci pomocí účtu správce Azure AD pro spravovanou instanci. Pak vytvořte přihlášení Azure AD ve spravované instanci pomocí následující syntaxe. Další informace najdete [v tématu Kurz: Zabezpečení spravované instance v Azure SQL Database pomocí objektů zabezpečení serveru Azure AD (přihlášení)](sql-database-managed-instance-aad-security-tutorial.md).

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

1. Zkontrolujte, zda migrace neobsahuje správnou databázi, tabulku a objekty zabezpečení.

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

1. Syntaxe ALTER USER namapujte místního uživatele na přihlášení azure ad.

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

1. Syntaxe ALTER USER namapujte místní skupinu na přihlášení Azure AD.

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

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Část 5: Testování ověřování uživatelů nebo skupin azure ad

Otestujte ověřování spravované instance pomocí uživatele, který byl dříve namapován na přihlášení služby Azure AD pomocí syntaxe ALTER USER.
 
1. Přihlaste se do federovaného virtuálního počítače pomocí předplatného MI jako`aadsqlmi\testUser1`
1. Pomocí služby SQL Server Management Studio (SSMS) se přihlaste ke `migration`spravované instanci pomocí integrovaného ověřování **služby Active Directory** a připojujte se k databázi .
    1. Můžete se také přihlásit pomocí testUser1@aadsqlmi.net přihlašovacích údajů pomocí možnosti SSMS Active Directory – Universal s podporou **vícefaktorové pomoci**. V tomto případě však nelze použít mechanismus jednotného přihlášení a je nutné zadat heslo. K přihlášení ke spravované instanci nebudete muset používat federovaný virtuální počítač.
1. Jako součást člena role **SELECT**můžete vybrat `test` z tabulky

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


Otestujte ověřování spravované instanci pomocí člena `migration`skupiny Systému Windows . Uživatel `aadsqlmi\testGroupUser` měl být přidán do `migration` skupiny před migrací.

1. Přihlaste se do federovaného virtuálního počítače pomocí předplatného MI jako`aadsqlmi\testGroupUser` 
1. Pomocí služby SSMS s **integrovaným ověřováním služby Active Directory** se připojte k serveru MI a databázi`migration`
    1. Můžete se také přihlásit pomocí testGroupUser@aadsqlmi.net přihlašovacích údajů pomocí možnosti SSMS Active Directory – Universal s podporou **vícefaktorové pomoci**. V tomto případě však nelze použít mechanismus jednotného přihlášení a je nutné zadat heslo. K přihlášení ke spravované instanci nebudete muset používat federovaný virtuální počítač. 
1. Jako součást `db_owner` role můžete vytvořit novou tabulku.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> Z důvodu známého problému s návrhem pro Azure SQL DB se příkaz vytvořit v tabulce spouštěný jako člen skupiny nezdaří s následující chybou: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> Aktuálním zástupným řešení je vytvoření tabulky s existujícím schématem ve výše uvedeném případě <dbo.new>

## <a name="next-steps"></a>Další kroky

- [Kurz: Migrace SQL Serveru do instance spravované databází Azure SQL database offline pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)