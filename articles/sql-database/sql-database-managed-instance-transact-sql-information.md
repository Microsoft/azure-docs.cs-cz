---
title: Rozdíly ve službě Azure SQL Database Managed Instance T-SQL | Dokumentace Microsoftu
description: Tento článek popisuje rozdíly T-SQL spravované instance Azure SQL Database a SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 5c8a15aa5198983a56a0238c1bb56f9345d07acc
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258598"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Rozdíly ve službě Azure SQL Database Managed Instance T-SQL z SQL serveru

Tento článek shrnuje a popisuje rozdíly v syntaxi a chování mezi Azure SQL Database Managed Instance a místní SQL Server Database Engine. Jsou popsány následujících tématech: <a name="Differences"></a>

- [Dostupnost](#availability) obsahuje rozdíly ve [vždy na](#always-on-availability) a [zálohy](#backup).
- [Zabezpečení](#security) obsahuje rozdíly ve [auditování](#auditing), [certifikáty](#certificates), [pověření](#credential), [zprostředkovatelé kryptografických služeb](#cryptographic-providers), [přihlašovacích údajů a uživatelů](#logins-and-users)a [klíč služby a hlavní klíč služby](#service-key-and-service-master-key).
- [Konfigurace](#configuration) obsahuje rozdíly ve [rozšíření fondu vyrovnávací paměti](#buffer-pool-extension), [kolace](#collation), [úrovně kompatibility](#compatibility-levels), [zrcadlení databáze ](#database-mirroring), [volby databáze](#database-options), [agenta systému SQL Server](#sql-server-agent), a [možnosti tabulky](#tables).
- [Funkce](#functionalities) zahrnuje [HROMADNÉ vložení/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [distribuované transakce](#distributed-transactions), [rozšířených událostí](#extended-events), [externí knihovny](#external-libraries), [filestream a FileTable](#filestream-and-filetable), [sémantické vyhledávání](#full-text-semantic-search), [propojené servery](#linked-servers), [PolyBase](#polybase), [replikace](#replication), [obnovení](#restore-statement), [služby Service Broker](#service-broker), [uložené procedury, funkce a aktivační události](#stored-procedures-functions-and-triggers).
- [Nastavení prostředí](#Environment) jako je například konfigurace virtuální sítě a podsítě.
- [Funkce, které mají různé chování v spravované instance](#Changes).
- [Dočasná omezení a známé problémy](#Issues).

Možnost nasazení Managed Instance poskytuje vysokou kompatibilitu díky místní SQL Server Database Engine. Většina funkce databázového stroje systému SQL Server jsou podporovány v managed instance.

![Migrace](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Dostupnost

### <a name="always-on-availability"></a>Always On

[Vysoká dostupnost](sql-database-high-availability.md) je integrovaná do Managed Instance a nemůže se dá nastavit podle uživatelů. Nejsou podporovány následující příkazy:

- [VYTVOŘENÍ KONCOVÉHO BODU... PRO DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [VYTVOŘENÍ SKUPINY DOSTUPNOSTI](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [PŘÍKAZ ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [PŘETAŽENÍ SKUPINY DOSTUPNOSTI](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) klauzuli [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) – příkaz

### <a name="backup"></a>Backup

Spravované instance mít automatické zálohování, takže uživatelé můžou vytvářet kompletní databáze `COPY_ONLY` zálohy. Rozdílové zálohování, protokol a zálohy snímků souborů nejsou podporovány.

- Managed instance můžete zálohovat databázi instance pouze do účtu úložiště objektů Blob v Azure:
  - Pouze `BACKUP TO URL` je podporována.
  - `FILE`, `TAPE`, a nejsou podporovány zálohovací zařízení.
- Většina Obecné `WITH` možnosti jsou podporovány.
  - `COPY_ONLY` je povinné.
  - `FILE_SNAPSHOT` není podporováno.
  - Možnosti pásky: `REWIND`, `NOREWIND`, `UNLOAD`, a `NOUNLOAD` nejsou podporovány.
  - Možnosti specifické pro protokol: `NORECOVERY`, `STANDBY`, a `NO_TRUNCATE` nejsou podporovány.

Omezení: 

- S managed instance, můžete zálohovat databázi instance pro zálohy s až 32 pruhy, což je dostatečná pro databáze až do 4 TB, pokud se používá komprese záloh.
- Velikost maximální zálohování stripe s použitím `BACKUP` příkaz ve spravované instanci je 195 GB, což je velikost maximální objektu blob. Zvýšíte počet pruhy v příkazu backup můžete snižovat velikost jednotlivých stripe a zůstat v rámci tohoto limitu.

    > [!TIP]
    > Chcete-li toto omezení obejít při zálohování databáze z SQL Server v místním prostředí nebo na virtuálním počítači, můžete:
    >
    > - Zálohování `DISK` místo zálohování `URL`.
    > - Nahrání záložních souborů do úložiště objektů Blob.
    > - Obnovte do spravované instance.
    >
    > `Restore` Příkaz ve spravované instanci podporuje větší velikosti objektu blob v záložní soubory, protože typ jiný objektu blob se používá pro ukládání nahraného záložní soubory.

Informace o zálohách pomocí jazyka T-SQL najdete v tématu [zálohování](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Zabezpečení

### <a name="auditing"></a>Auditování

Hlavní rozdíly mezi auditování v databázích v Azure SQL Database a databází v systému SQL Server jsou:

- Když je možnost nasazení Managed Instance ve službě Azure SQL Database auditování funguje na úrovni serveru. `.xel` Soubory protokolu se ukládají ve službě Azure Blob storage.
- Izolované databáze a elastický fond možnosti nasazení ve službě Azure SQL Database auditování funguje na úrovni databáze.
- V systému SQL Server v místním nebo virtuálním počítačům auditování funguje na úrovni serveru. Události jsou uloženy v systému souborů nebo protokoly událostí Windows.
 
Relace XEvent auditování ve spravované instanci podporuje cíle úložiště objektů Blob v Azure. Protokoly Souborová služba a Windows nejsou podporované.

Klíč rozdíly v `CREATE AUDIT` syntaxe pro auditování objektů Blob v Azure storage jsou:

- Novou syntaxi `TO URL` za předpokladu, že chcete zadat adresu URL kontejneru úložiště objektů Blob v Azure můžete použít ve kterém `.xel` soubory jsou umístěny.
- Syntaxe `TO FILE` se nepodporuje, protože spravovanou instanci nejde přístup ke sdíleným složkám Windows.

Další informace naleznete v tématu: 

- [VYTVOŘIT SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [PŘÍKAZ ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditování](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certifikáty

Managed instance nelze získat přístup k sdílených složek a složek Windows, platí následující omezení:

- `CREATE FROM` / `BACKUP TO` Souboru se nepodporuje pro certifikáty.
- `CREATE` / `BACKUP` Certifikátu `FILE` / `ASSEMBLY` se nepodporuje. Soubory soukromých klíčů nelze použít. 

Zobrazit [vytvořit certifikát](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) a [ZÁLOŽNÍ certifikát](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Alternativní řešení**: Skript pro certifikát nebo privátní klíč, uložit jako soubor .sql a vytvořit z binárního souboru:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Přihlašovací údaj

Pouze Azure Key Vault a `SHARED ACCESS SIGNATURE` identity podporují. Uživatelé Windows se nepodporují.

V tématu [vytvořit pověření](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) a [ALTER pověření](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Zprostředkovatelé kryptografických služeb

Managed instance nemají přístup k souborům, takže zprostředkovatelé kryptografických služeb nelze vytvořit:

- `CREATE CRYPTOGRAPHIC PROVIDER` není podporováno. Zobrazit [vytvořit zprostředkovatele KRYPTOGRAFICKÝCH služeb](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` není podporováno. Zobrazit [Změna zprostředkovatele KRYPTOGRAFICKÝCH služeb](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Přihlášení a uživatelé

- Přihlašovací jména SQL vytvořit pomocí `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, a `FROM SID` jsou podporovány. Zobrazit [vytvořit přihlášení](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD) server objekty zabezpečení (přihlášení) vytvořené pomocí [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) syntaxe nebo [vytvořit uživatele z přihlášení [přihlášení k Azure AD]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) syntaxe jsou podporovány (public preview). Tyto přihlašovací údaje se vytvářejí na úrovni serveru.

    Spravovaná Instance podporuje objekty zabezpečení databáze služby Azure AD se syntaxí `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Tato funkce je také označovaný jako databázi uživatelů Azure AD, které jsou obsažené.

- Přihlašovací údaje Windows vytvořené pomocí `CREATE LOGIN ... FROM WINDOWS` syntaxe nejsou podporovány. Pomocí přihlašovacích údajů Azure Active Directory a uživatelů.
- Obsahuje uživatele služby Azure AD, který vytvořil instanci [neomezená oprávnění správce](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Uživatelé na úrovni databáze bez oprávnění správce služby Azure AD je možné vytvořit pomocí `CREATE USER ... FROM EXTERNAL PROVIDER` syntaxe. Zobrazit [vytvořit uživatele... Z EXTERNÍHO POSKYTOVATELE](sql-database-manage-logins.md#non-administrator-users).
- Azure AD objekty serveru (přihlášení) podporují funkce SQL v rámci pouze jednoho spravované instance. Funkce, které vyžadují různé instance interakce, bez ohledu na to, jestli jsou už v rámci stejné služby Azure AD tenant nebo různých tenantech, nejsou podporovány pro uživatele Azure AD. Mezi tyto funkce patří:

  - Transakční replikace SQL.
  - Propojit server.

- Nastavení přihlášení Azure AD mapované na skupiny Azure AD, vlastník databáze není podporován.
- Je podporováno zosobnění hlavních účtů na úrovni serveru služby Azure AD s použitím jiných objektů zabezpečení Azure AD, jako [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) klauzuli. Omezení EXECUTE AS jsou:

  - Když název se liší od přihlašovací jméno se pro uživatelů Azure AD nepodporuje EXECUTE AS USER. Když uživatel se vytvoří pomocí syntaxe [myAadUser] vytvořit uživatele z přihlášení je například [john@contoso.com] a dojde k pokusu o zosobnění prostřednictvím EXEC AS USER = _myAadUser_. Když vytvoříte **uživatele** z Azure AD server instančního objektu (přihlášení), zadejte uživatelské_jméno jako stejný login_name z **přihlášení**.
  - Pouze SQL Server-level objekty zabezpečení (přihlašovací údaje), které jsou součástí `sysadmin` role můžete provést následující operace, které se zaměřují objekty zabezpečení Azure AD:

    - SPUŠTĚNÍ JAKO UŽIVATEL
    - SPUSTIT JAKO PRO PŘIHLÁŠENÍ

- Omezení veřejné verze preview pro objekty zabezpečení serveru Azure AD (přihlášení):

  - Omezení správce Active Directory pro spravovanou instanci:

    - Správce služby Azure AD použít ke konfiguraci spravované instance nelze použít k vytvoření Azure AD objekt zabezpečení serveru (přihlášení) v rámci spravované instance. Je nutné vytvořit první Azure AD objekt zabezpečení serveru (přihlášení) pomocí účtu systému SQL Server, která `sysadmin` role. Tato dočasná omezení se odeberou po objekty serveru Azure AD (přihlášení) budou obecně dostupné. Pokud se pokusíte vytvořit přihlášení pomocí účtu správce Azure AD, se zobrazí následující chyba: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - V současné době musí být vytvořeny při prvním přihlášení Azure AD v hlavní databázi vytvořit standardní účet serveru SQL Server (jiné než Azure AD), která `sysadmin` role pomocí [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) z EXTERNÍHO poskytovatele. Po obecné dostupnosti toto omezení se odeberou. Můžete vytvořit počáteční přihlášení k Azure AD pomocí Správce služby Active Directory pro spravovanou instanci.
    - Použít s SQL Server Management Studio nebo SqlPackage DacFx (export a import) není podporována pro přihlášení Azure AD. Toto omezení se odebere, až budou obecně dostupné objekty serveru Azure AD (přihlášení).
    - Pomocí služby Azure AD objekty serveru (přihlášení) pomocí aplikace SQL Server Management Studio:

      - Skriptování přihlašovací údaje Azure AD, které používají všechny ověřené přihlašovací jméno se nepodporuje.
      - IntelliSense nerozpozná příkaz Vytvořit z EXTERNÍHO zprostředkovatele přihlášení a zobrazuje červené podtržení.

- Pouze serveru hlavní přihlášení na úrovni, který je vytvořen Managed Instance procesu, členové role serveru, jako například zřizování `securityadmin` nebo `sysadmin`, nebo dalších přihlášení s oprávněními ALTER ANY LOGIN na úrovni serveru můžete vytvořit služby Azure AD objekty serveru (přihlášení) v hlavní databázi pro Managed Instance.
- Pokud je přihlášení objektu zabezpečení SQL, pouze přihlašovací údaje, které jsou součástí `sysadmin` role může používat příkazu pro vytvoření k vytvoření přihlášení k účtu služby Azure AD.
- Přihlášení k Azure AD musí být členem skupiny Azure AD ve stejném adresáři, který se používá pro Azure SQL Database Managed Instance.
- Azure AD objekty serveru (přihlášení) jsou viditelné v Průzkumníku objektů od verze SQL Server Management Studio 18.0 ve verzi preview 5.
- Překrývající se objekty serveru Azure AD (přihlášení) pomocí účtu správce Azure AD je povolená. Azure AD objekty serveru (přihlášení) mít přednost před správce Azure AD při řešení zabezpečení a použít oprávnění pro spravovanou instanci.
- Během ověřování platí následující posloupnost vyřešit ověřování objektu zabezpečení:

    1. Pokud účet Azure AD existuje přímo namapován server instanční objekt Azure AD (přihlášení), která je k dispozici v sys.server_principals jako typ "E", udělení přístupu a použít oprávnění objektu zabezpečení serveru Azure AD (přihlášení).
    2. Pokud je členem skupiny Azure AD, který je namapovaný na Azure AD objekt zabezpečení serveru (přihlášení), která je k dispozici v sys.server_principals jako typ "X", účet služby Azure AD udělit přístup a použít oprávnění skupiny přihlášení k Azure AD.
    3. Pokud účet Azure AD je speciální nakonfigurován portál správce Azure AD pro Managed Instance, který neexistuje v systémových zobrazeních Managed Instance, použít speciální oprava oprávnění správce služby Azure AD pro spravovanou instanci (režim starší verze).
    4. Pokud účet Azure AD existuje jako přímo namapován na uživatele služby Azure AD v databázi, která je k dispozici v sys.database_principals jako typ "E", udělení přístupu a použít oprávnění uživatele databáze služby Azure AD.
    5. Pokud je členem skupiny Azure AD, který je namapovaný na uživatele služby Azure AD v databázi, která je k dispozici v sys.database_principals jako typ "X", účet služby Azure AD udělit přístup a použít oprávnění skupiny přihlášení k Azure AD.
    6. Pokud dojde Azure AD přihlašovací jméno mapované na uživatelský účet služby Azure AD nebo účet skupiny Azure AD, které se překládá na uživatele, který je ověřování, se použijí všechna oprávnění z těchto přihlašovacích údajů Azure AD.

### <a name="service-key-and-service-master-key"></a>Služby a klíče hlavní klíč služby

- [Záloha hlavního klíče](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) se nepodporuje (spravované službou SQL Database).
- [Obnovení hlavního klíče](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) se nepodporuje (spravované službou SQL Database).
- [Záloha hlavního klíče služby](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) se nepodporuje (spravované službou SQL Database).
- [Obnovení hlavního klíče služby](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) se nepodporuje (spravované službou SQL Database).

## <a name="configuration"></a>Konfigurace

### <a name="buffer-pool-extension"></a>Rozšíření fondu vyrovnávací paměti

- [Rozšíření fondu vyrovnávací paměti](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) se nepodporuje.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` není podporováno. Zobrazit [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Kolace

Je výchozí kolace instance `SQL_Latin1_General_CP1_CI_AS` a je možné zadat jako parametr vytváření. Zobrazit [kolace](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Úrovně kompatibility

- Úrovně kompatibility podporované jsou 100, 110, 120, 130 a 140.
- Nejsou podporované úrovně kompatibility nižší než 100.
- Výchozí úroveň kompatibility pro nové databáze je 140. Pro obnovené databáze úroveň kompatibility nemění Pokud se jednalo o 100 a vyšší.

Zobrazit [úroveň kompatibility ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Zrcadlení databáze

Zrcadlení databáze se nepodporuje.

- `ALTER DATABASE SET PARTNER` a `SET WITNESS` možnosti nejsou podporovány.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` není podporováno.

Další informace najdete v tématu [ALTER DATABASE SET PARTNER a SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) a [vytvořit koncový bod... PRO DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Možnosti databáze

- Víc souborů protokolů nejsou podporovány.
- Objekty v paměti nejsou podporovány v rámci úrovně služeb pro obecné účely. 
- Platí limit 280 souborů na jednu instanci obecné účely, který zahrnuje maximálně 280 počet souborů v databázi. Soubory protokolu a data v úrovni General Purpose se počítají do tohoto limitu. [Pro důležité obchodní informace úroveň podporuje 32 767 počet souborů v databázi](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Databáze nesmí obsahovat skupiny souborů, které obsahují filestream data. Obnovení selže, pokud obsahuje .bak `FILESTREAM` data. 
- Každý soubor je umístěn v úložišti objektů Blob v Azure. Vstupně-výstupních operací a propustnosti na soubor závisí na velikosti jednotlivých souborů.

#### <a name="create-database-statement"></a>Příkaz CREATE DATABASE

Následující omezení platí pro `CREATE DATABASE`:

- Soubory a skupiny souborů nelze definovat. 
- `CONTAINMENT` Možnost není podporována. 
- `WITH` možnosti nejsou podporovány. 
   > [!TIP]
   > Jako alternativní řešení použít `ALTER DATABASE` po `CREATE DATABASE` nastavit možnosti databáze k přidání souborů nebo k nastavení členství ve skupině. 

- `FOR ATTACH` Možnost není podporována.
- `AS SNAPSHOT OF` Možnost není podporována.

Další informace najdete v tématu [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Příkaz ALTER DATABASE

Nelze nastavit nebo změnit některé vlastnosti souboru:

- Cesta k souboru nemůže být zadán `ALTER DATABASE ADD FILE (FILENAME='path')` příkazu T-SQL. Odebrat `FILENAME` ze skriptu protože managed instance je automaticky umístí soubory. 
- Název souboru nelze změnit pomocí `ALTER DATABASE` příkazu.

Tyto možnosti jsou ve výchozím nastavení a nedá se změnit:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Nelze změnit následující možnosti:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Další informace najdete v tématu [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>Agent SQL Server

- Nastavení agenta systému SQL Server jsou jen pro čtení. Postup `sp_set_agent_properties` není ve spravované instanci podporováno. 
- Úlohy
  - Podporují se kroky úlohy T-SQL.
  - Podporují se následující úlohy replikace:
    - Čtečky protokolů transakcí
    - Snímek
    - Distributor
  - Kroky úlohy služby SSIS se podporují.
  - Jiné druhy kroky úlohy nejsou aktuálně podporovány:
    - Krok úlohy slučovací replikace nepodporuje. 
    - Čtečky fronty se nepodporuje. 
    - Příkazové okno se ještě nepodporuje.
  - Spravované instance nemá přístup k externím prostředkům, například sdílených síťových složek pomocí příkazu robocopy. 
  - SQL Server Analysis Services nejsou podporovány.
- Oznámení jsou podporovány jen částečně.
- E-mailové oznámení se podporuje, i když ji vyžaduje, abyste nakonfigurovali profil databázového e-mailu. Agent systému SQL Server můžete použít jenom jeden profil databázového e-mailu a musí být volána `AzureManagedInstance_dbmail_profile`. 
  - Operátor není podporován. 
  - Příkazu není podporován.
  - Výstrahy se zatím nepodporují.
  - Nepodporuje proxy servery. 
- Protokol událostí se nepodporuje.

Následující funkce v současné době nejsou podporované, ale bude v budoucnu povoleno:

- Proxy servery
- Plánování úloh na nečinnosti procesoru
- Povolení nebo zakázání agenta
- Výstrahy

Informace o agenta systému SQL Server najdete v tématu [agenta systému SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabulky

Následující tabulky nejsou podporovány:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

Informace o tom, jak vytvořit a upravit tabulky najdete v tématu [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) a [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkce

### <a name="bulk-insert--openrowset"></a>Příkaz Bulk insert / openrowset

Managed instance nelze získat přístup k sdílených složek a složek Windows, tak soubory musí být importovány z úložiště objektů Blob v Azure:

- `DATASOURCE` je nutné v `BULK INSERT` příkaz při importu souborů z úložiště objektů Blob v Azure. Zobrazit [HROMADNÉ vložení](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` je nutné v `OPENROWSET` fungovat-li si přečíst obsah souboru z úložiště objektů Blob v Azure. Zobrazit [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Managed instance nelze získat přístup k sdílených složek a složek Windows, platí následující omezení:

- Pouze `CREATE ASSEMBLY FROM BINARY` je podporována. Zobrazit [vytvořit sestavení z binární](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` není podporováno. Zobrazit [vytvořit sestavení ze souboru](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` nemůže odkazovat na soubory. Zobrazit [změna sestavení](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Nezdokumentovaný příkazů DBCC, u kterých jde v systému SQL Server nepodporuje spravované instance.

- `Trace flags` nejsou podporovány. Zobrazit [příznaky trasování](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` není podporováno. Zobrazit [příkaz DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` není podporováno. Zobrazit [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Distribuované transakce

Služba MSDTC a [elastické transakce](sql-database-elastic-transactions-overview.md) nejsou aktuálně podporované ve spravované instance.

### <a name="extended-events"></a>Rozšířené události

Některé cíle specifické pro Windows pro rozšířených událostí (XEvents) nejsou podporovány:

- `etw_classic_sync` Cíl není podporován. Store `.xel` souborů v úložišti objektů Blob v Azure. See [etw_classic_sync target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file` Cíl není podporován. Store `.xel` souborů v úložišti objektů Blob v Azure. Zobrazit [event_file cílové](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externí knihovny

V databázi R a Python, externí knihovny nejsou ještě podporované. Zobrazit [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream a FileTable

- FileStream data není podporován.
- Databáze nesmí obsahovat skupiny souborů s `FILESTREAM` data.
- `FILETABLE` není podporováno.
- Tabulky nemůže mít `FILESTREAM` typy.
- Nejsou podporovány následující funkce:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Další informace najdete v tématu [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) a [Filetable](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Sémantické vyhledávání

[Sémantické vyhledávání](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) se nepodporuje.

### <a name="linked-servers"></a>Propojené servery

Propojené servery ve spravovaných instancí podporuje omezený počet cílů:

- Podporované cíle jsou SQL Server a SQL Database.
- Cíle, které nejsou podporovány jsou soubory služby Analysis Services a další relační databázový systém.

Operace

- Různé instance zápisu transakce nejsou podporovány.
- `sp_dropserver` platí pro odkazovaný server vyřadit. See [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET` Funkci můžete použít k provádění dotazů pouze na instancích systému SQL Server. Může se jednat buď spravovaných, místně, nebo na virtuálních počítačích. Zobrazit [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE` Funkci můžete použít k provádění dotazů pouze na instancích systému SQL Server. Může se jednat buď spravovaných, místně, nebo na virtuálních počítačích. Pouze `SQLNCLI`, `SQLNCLI11`, a `SQLOLEDB` hodnoty jsou podporovány jako zprostředkovatel. Příklad: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Zobrazit [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>PolyBase

Externí tabulky tento odkaz, nejsou podporované soubory v HDFS nebo Azure Blob storage. Informace o PolyBase najdete v tématu [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikace

Replikace je dostupná ve veřejné verzi preview pro Managed Instance. Informace o replikaci najdete v tématu [replikace systému SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>OBNOVENÍ – příkaz 

- Podporované syntaxe:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Nepodporovaná syntaxe:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Zdroj: 
  - `FROM URL` (Azure Blob storage) je jedinou podporovanou možností.
  - `FROM DISK`/`TAPE`/ zálohovací zařízení se nepodporuje.
  - Zálohovacích skladů nejsou podporovány.
- `WITH` možnosti nejsou podporovány, jako je například Ne `DIFFERENTIAL` nebo `STATS`.
- `ASYNC RESTORE`: Obnovení pokračovat i v případě, že přestane fungovat připojení klienta. Pokud vaše připojení se ukončí, můžete zkontrolovat `sys.dm_operation_status` zobrazení stavu operace obnovení a pro vytvoření a DROP database. Zobrazit [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Následující možnosti databáze jsou nastavena nebo přepsat a není možné později změnit: 

- `NEW_BROKER` Pokud zprostředkovatele není povolené v souboru .bak. 
- `ENABLE_BROKER` Pokud zprostředkovatele není povolené v souboru .bak. 
- `AUTO_CLOSE=OFF` Pokud je databáze v souboru .bak `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` Pokud je databáze v souboru .bak `SIMPLE` nebo `BULK_LOGGED` režimu obnovení.
- Paměťově optimalizované skupiny souborů a je voláno nebylo ve zdrojovém souboru .bak XTP. 
- Všechny existující paměťově optimalizovaných souborů bylo přejmenováno na XTP. 
- `SINGLE_USER` a `RESTRICTED_USER` možnosti jsou převedeny na `MULTI_USER`.

Omezení: 

- `.BAK` nelze obnovit, soubory, které obsahují více sad záloh. 
- `.BAK` nelze obnovit, soubory, které obsahují víc souborů protokolů.
- Obnovení selže, pokud obsahuje .bak `FILESTREAM` data.
- Zálohování, které obsahují databáze, které mají aktivní objektů v paměti, nelze ho obnovit na instanci obecné účely. Informace o příkazech obnovení najdete v tématu [příkazy RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Služba Service broker

Různé instance služby Service broker se nepodporuje:

- `sys.routes`: Předpokladem je je nutné vybrat z sys.routes adresu. Tato adresa musí být místní na každý trasy. Zobrazit [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Nemůžete použít `CREATE ROUTE` s `ADDRESS` jiné než `LOCAL`. Zobrazit [TRASY vytvořit](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Nemůžete použít `ALTER ROUTE` s `ADDRESS` jiné než `LOCAL`. Zobrazit [TRASY ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Uložené procedury, funkce a aktivační události

- `NATIVE_COMPILATION` v úrovni General Purpose nepodporuje.
- Následující [uložené procedury sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) možnosti nejsou podporovány: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` není podporováno. Zobrazit [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` není podporováno. Zobrazit [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` nejsou podporovány, což zahrnuje `sp_addextendedproc`  a `sp_dropextendedproc`. Zobrazit [rozšířené uložené procedury](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db`, a `sp_detach_db` nejsou podporovány. Zobrazit [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), a [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Environment"></a>Omezení Environmet

### <a name="subnet"></a>Podsíť
- V podsíti vyhrazená pro vaši Managed Instance nelze umístit všechny další prostředky (například virtuální počítače). Tyto prostředky umístěte do jiné podsítě.
- Podsíť musí mít dostatečný počet dostupných [IP adresy](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Minimální hodnota je 16, zatímco doporučení je k dispozici alespoň 32 IP adres v podsíti.
- [Koncové body služeb nelze přidružit podsíť spravované instance](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Ujistěte se, že při vytváření virtuální sítě je zakázána možnost koncových bodů služby.
- Počet a typy instancí, které můžete umístit do podsítě mají některé [omezení a omezení](sql-database-managed-instance-resource-limits.md#strategies-for-deploying-mixed-general-purpose-and-business-critical-instances)
- Zde jsou některé [pravidel zabezpečení, která se musí použít na podsítě](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>VNET
- Virtuální síť je možné nasadit pomocí modelu Resource - klasický Model pro virtuální síť se nepodporuje.
- Některé služby, jako jsou App Service Environment, Logic apps a spravované instance (používá se pro geografickou replikaci, transakční replikace, nebo prostřednictvím propojené servery) nelze přistupovat spravovaných instancí v různých oblastech, v případě, že jejich virtuální sítě jsou propojené pomocí [globální partnerský vztah](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Můžete připojit k těmto prostředku přes ExpressRoute nebo připojení typu VNet-to-VNet prostřednictvím bran virtuální sítě.

## <a name="Changes"></a> Změny chování

Následující proměnné, funkce a zobrazení vrátí odlišné výsledky:

- `SERVERPROPERTY('EngineEdition')` Vrátí hodnotu 8. Tato vlastnost jednoznačně identifikuje managed instance. Zobrazit [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Vrátí hodnotu NULL, protože existuje koncept instance, jak to neplatí pro spravovanou instanci SQL serveru. Zobrazit [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Vrátí úplný název DNS "umožňující připojení k", například Moje instance.wcus17662feb9ce98.database.windows.net spravované. Zobrazit [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` Vrátí úplný název DNS "umožňující připojení k", jako například `myinstance.domain.database.windows.net` pro vlastnosti "name" a "data_source." Zobrazit [SYS. SERVERY](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` Vrátí hodnotu NULL, protože koncept služby, protože existuje pro neplatí pro spravovanou instanci SQL serveru. Zobrazit [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` je podporován. Pokud v sys.syslogins nejsou přihlášení k Azure AD, vrátí hodnotu NULL. Zobrazit [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` není podporováno. Se vrátí nesprávná data, která je dočasná známý problém. Zobrazit [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Issues"></a> Známé problémy a omezení

### <a name="tempdb-size"></a>Velikost databáze TEMPDB

Maximální velikost souboru z `tempdb` nemůže být větší než 24 GB na jádro v úrovni General Purpose. Maximální počet `tempdb` velikost na vrstvu pro důležité obchodní informace je omezena s velikostí úložiště instancí. `tempdb` Databáze je vždy rozdělit na 12 datových souborů. Tento maximální velikost jednoho souboru se nedá změnit, a nové soubory nelze přidat do `tempdb`. Některé dotazy může vrátit chybu, pokud je nutné více než 24 GB na jádro v `tempdb`. `tempdb` je vždy znovu vytvořen jako prázdná databáze při spuštění instance nebo převzetí služeb při selhání a jakékoli změně v `tempdb` se nezachová. 

### <a name="cant-restore-contained-database"></a>Nelze obnovit databáze s omezením

Nelze obnovit spravované Instance [databáze s omezením](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Obnovení bodu v čase z existující databáze s omezením nefunguje na Managed Instance. Tento problém se vyřeší brzy. Do té doby doporučujeme odebrat možnost omezení z databází, které se umístí na spravované instanci. Nepoužívejte možnost omezení pro provozních databází. 

### <a name="exceeding-storage-space-with-small-database-files"></a>Překročení prostoru úložiště se soubory malé databáze

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, a `RESTORE DATABASE` příkazů může selhat, protože instance můžete dosáhne limitu služby Azure Storage.

Každá instance spravované obecné účely má až 35 TB vyhrazená místa na disku Azure Premium storage. Každý databázový soubor je umístěn na jiném fyzickém disku. Disky o velikosti může být 128 GB, 256 GB, 512 GB, 1 TB nebo 4 TB. Nevyužité místo na disku se neúčtuje, ale celkový součet velikosti disků typu Premium Azure nemůže být delší než 35 TB. V některých případech může překročit managed instance, která nepotřebuje 8 TB celkem 35 TB Azure omezení velikosti úložiště z důvodu interní fragmentace.

Managed instance pro obecné účely například může mít jeden soubor, který je 1,2 TB velikosti ukládá na disk 4 TB. Také může mít 248 soubory, které jsou každý velikosti 1 GB, které jsou umístěné na různých discích 128 GB. V tomto příkladu:

- Celkové přidělené úložiště velikosti disku je 1 × 4 TB + 248 x 128 GB = 35 TB.
- Celkový počet vyhrazené místo pro databáze na instanci je 1 x 1.2 TB + 248 x 1 GB = 1,4 TB.

Tento příklad ukazuje, že za určitých okolností kvůli konkrétní distribuce souborů, může do spravované instance dosáhne limitu 35 TB, který je vyhrazený pro připojený Disk Premium Azure při nemusí očekávání.

V tomto příkladu nadále fungovat stávající databáze a můžou růst bez problémů, tak dlouho, dokud nejsou přidány nové soubory. Nelze vytvořit ani obnovit, protože není dost místa pro nových diskových jednotek, i když celková velikost všech databází limit velikosti instance nemá přístup do nové databáze. Chyby, ke které se vrátí v takovém případě není jasný.

Je možné [identifikovat počet zbývajících souborů](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) pomocí zobrazení systému. Pokud překročíte tento limit, zkuste [prázdný a některé menší soubory odstranit pomocí příkazu DBCC SHRINKFILE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) nebo přepněte [úroveň pro důležité obchodní informace, které nemá tento limit](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>Nesprávná konfigurace SAS klíč při databáze obnovení

`RESTORE DATABASE` který načte soubor .bak může být neustále opakování pokusu o čtení .bak souboru a vrátí chybu po dlouhou dobu, pokud sdílený přístupový podpis v `CREDENTIAL` je nesprávný. Spusťte RESTORE HEADERONLY před obnovením databáze, ujistěte se, že je správný klíč SAS.
Ujistěte se, že odeberete úvodního `?` z klíče SAS, který je vytvořen pomocí webu Azure portal.

### <a name="tooling"></a>Nástroje

SQL Server Management Studio a SQL Server Data Tools, může mít některé problémy při přístupu k managed instance.

- Použití objektů serveru Azure AD (přihlášení) a uživatele (verze public preview) pomocí nástroje SQL Server Data Tools aktuálně není podporováno.
- Skriptování pro server Azure AD (přihlášení) – objekty zabezpečení a uživatele (verze public preview) se nepodporuje v systému SQL Server Management Studio.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Názvy nesprávné databází v některých zobrazeních, protokoly a zpráv

Několik zobrazení systému, čítače výkonu, chybové zprávy, XEvents a záznamů v protokolu chyb se zobrazí identifikátory GUID databáze namísto názvů skutečné databáze. Nemusíte spoléhat na tyto identifikátory GUID, protože, budete v budoucnu nahradí se názvy skutečné databáze.

### <a name="database-mail"></a>Databázový e-mail

`@query` Parametr [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) postup nebude fungovat.

### <a name="database-mail-profile"></a>Profil databázového e-mailu

Profil databázového e-mailu používá SQL Server Agent musí být volána `AzureManagedInstance_dbmail_profile`. Neexistují žádná omezení pro názvy jiných profil databázového e-mailu.

### <a name="error-logs-arent-persisted"></a>Nejsou zachované protokoly chyb

Nejsou zachované protokoly chyb, které jsou k dispozici v Managed Instance a jejich velikost není součástí limit maximální velikost úložiště. Protokoly chyb může automaticky vymazány, pokud dojde k převzetí služeb při selhání.

### <a name="error-logs-are-verbose"></a>Jsou podrobné protokoly chyb

Spravované instance uvádí podrobné informace v protokolech chyb a většina článku není relevantní. V budoucnu se sníží množství informací v protokolech chyb.

**Alternativní řešení:** Přečtěte si chybové protokoly, které se filtruje některé irelevantní položky pomocí vlastní postupu. Další informace najdete v tématu [Managed Instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Obor transakce ve dvou databázích v rámci stejné instance se nepodporuje.

`TransactionScope` Třídy v rozhraní .NET nefunguje, pokud dva dotazy se odesílají do dvou databází v rámci stejné instance v rámci stejného oboru transakce:

```C#
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

I když tento kód pracuje s daty v rámci stejné instance, vyžaduje příkaz MSDTC.

**Alternativní řešení:** Použít [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) použití jiné databáze v kontextu připojení místo použití dvě připojení.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Moduly CLR a někdy propojené servery nemůže odkazovat na místní IP adresa

Moduly CLR umístí do managed instance a propojené servery nebo distribuované dotazy, které odkazují na aktuální instanci někdy nejde přeložit IP místní instance. Tato chyba je přechodný problém.

**Alternativní řešení:** Pokud je to možné použijte připojení kontextu v modulu CLR.

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>Transparentní šifrování dat šifrované databáze s klíčem spravovaným službou nepodporují uživatelem iniciované zálohování

Nelze provést `BACKUP DATABASE ... WITH COPY_ONLY` na databázi, která je zašifrovaná pomocí spravované službou transparentní šifrování dat (TDE). Transparentní šifrování dat spravovaným službou vynutí zálohy šifrované s vnitřním klíčem TDE. Klíč nelze exportovat, abyste nelze obnovit zálohování.

**Alternativní řešení:** Použít automatické zálohování a obnovení k určitému bodu v čase, nebo [spravovaného zákazníkem (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) místo. Také můžete zakázat šifrování v databázi.

## <a name="next-steps"></a>Další postup

- Další informace o spravované instance najdete v tématu [co je managed instance?](sql-database-managed-instance.md)
- Pro funkce a seznam porovnání, naleznete v tématu [porovnání funkcí Azure SQL Database](sql-database-features.md).
- Rychlý start ukazuje, jak vytvořit nové spravované instance, naleznete v tématu [vytvoření managed instance](sql-database-managed-instance-get-started.md).
