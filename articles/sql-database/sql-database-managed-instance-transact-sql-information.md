---
title: Azure SQL Database Managed Instance T-SQL ve službě | Dokumentace Microsoftu
description: Tento článek popisuje rozdíly T-SQL ve službě Azure SQL Database Managed Instance a SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: b633c6a8ccbf9f29b93314bb9391215031d523eb
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893057"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Rozdíly ve službě Azure SQL Database Managed Instance T-SQL z SQL serveru

Možnost nasazení Managed Instance poskytuje vysokou kompatibilitu díky místní SQL Server Database Engine. Většina funkce databázového stroje systému SQL Server jsou podporovány ve spravované instanci.

![Migrace](./media/sql-database-managed-instance/migration.png)

Protože stále existují určité rozdíly v syntaxi a chování, tento článek shrnuje a popisuje tyto rozdíly. <a name="Differences"></a>

- [Dostupnost](#availability) včetně rozdílů v [vždy na](#always-on-availability) a [zálohy](#backup),
- [Zabezpečení](#security) včetně rozdílů v [auditování](#auditing), [certifikáty](#certificates), [pověření](#credential), [zprostředkovatelé kryptografických služeb](#cryptographic-providers), [Přihlášení / uživatelé](#logins--users), [klíče a hlavní klíč služby služby](#service-key-and-service-master-key),
- [Konfigurace](#configuration) včetně rozdílů v [rozšíření fondu vyrovnávací paměti](#buffer-pool-extension), [kolace](#collation), [úrovně kompatibility](#compatibility-levels),[databáze zrcadlení](#database-mirroring), [volby databáze](#database-options), [agenta systému SQL Server](#sql-server-agent), [možnosti tabulky](#tables),
- [Funkce](#functionalities) včetně [HROMADNÉ vložení/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [distribuované transakce](#distributed-transactions), [ Rozšířené události](#extended-events), [externí knihovny](#external-libraries), [Filestream a Filetable](#filestream-and-filetable), [sémantické vyhledávání](#full-text-semantic-search), [propojené servery](#linked-servers), [Polybase](#polybase), [replikace](#replication), [obnovení](#restore-statement), [služby Service Broker](#service-broker), [ Uložené procedury, funkce a aktivační události](#stored-procedures-functions-triggers),
- [Funkce, které mají různé chování v Managed instance](#Changes)
- [Dočasná omezení a známé problémy](#Issues)

## <a name="availability"></a>Dostupnost

### <a name="always-on-availability"></a>Always On

[Vysoká dostupnost](sql-database-high-availability.md) je integrovaná do Managed Instance a nemůže se dá nastavit podle uživatelů. Nejsou podporovány následující příkazy:

- [VYTVOŘENÍ KONCOVÉHO BODU... PRO DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [VYTVOŘENÍ SKUPINY DOSTUPNOSTI](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [PŘÍKAZ ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [PŘETAŽENÍ SKUPINY DOSTUPNOSTI](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) klauzuli [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) – příkaz

### <a name="backup"></a>Backup

Spravované instance mají automatické zálohování a povolí uživatelům vytvářet kompletní databáze `COPY_ONLY` zálohy. Rozdílové zálohování, protokol a zálohy snímků souborů nejsou podporovány.

- Managed Instance můžete zálohovat databázi instance jenom pro účet služby Azure Blob Storage:
  - Pouze `BACKUP TO URL` je podporováno
  - `FILE`, `TAPE`, a nejsou podporovány zálohovací zařízení  
- Většina Obecné `WITH` možnosti jsou podporovány.
  - `COPY_ONLY` je povinné.
  - `FILE_SNAPSHOT` Nepodporuje se
  - Možnosti pásky: `REWIND`, `NOREWIND`, `UNLOAD`, a `NOUNLOAD` nejsou podporovány
  - Možnosti specifické pro protokol: `NORECOVERY`, `STANDBY`, a `NO_TRUNCATE` nejsou podporovány

Omezení:  

- Managed instance, můžete zálohovat databázi instance pro zálohy s až 32 pruhy, což je dostatečná pro databáze až do 4 TB, pokud se používá komprese záloh.
- Maximální počet záloh stripe velikost pomocí `BACKUP` příkaz ve spravované instanci je 195 GB (objekt blob maximální velikost). Zvýšíte počet pruhy v příkazu backup můžete snižovat velikost jednotlivých stripe a zůstat v rámci tohoto limitu.

    > [!TIP]
    > Toto omezení obejít, při zálohování databáze z SQL Server v místním prostředí nebo na virtuálním počítači, máte následující:
    >
    > - Zálohování na `DISK` místo zálohování `URL`
    > - Nahrání záložních souborů do úložiště objektů Blob
    > - Obnovit do spravované instance
    >
    > `Restore` Příkaz ve spravované instance podporuje větší velikosti objektu blob v záložní soubory, protože typ jiný objektu blob se používá pro ukládání nahraného záložní soubory.

Informace o zálohách pomocí jazyka T-SQL najdete v tématu [zálohování](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Zabezpečení

### <a name="auditing"></a>Auditování

Hlavní rozdíly mezi auditování v databázích v Azure SQL Database a databází v systému SQL Server jsou:

- S Managed Instance možností nasazení ve službě Azure SQL Database, auditování funguje na úrovni serveru nebo úložišti `.xel` soubory protokolů ve službě Azure Blob storage.
- Izolované databáze a elastický fond možnosti nasazení ve službě Azure SQL Database auditování funguje na úrovni databáze.
- V místním SQL serverem / virtuální počítače, audit funguje na serveru úrovně, ale ukládá události do protokolů událostí systému a soubory.
  
Relace XEvent auditování ve spravované instanci podporuje cíle úložiště objektů Blob v Azure. Protokolování souborů a systému windows nejsou podporované.

Klíč rozdíly v `CREATE AUDIT` syntaxe pro auditování pro Azure Blob storage jsou:

- Novou syntaxi `TO URL` je k dispozici a umožní vám zadat adresu URL kontejneru objektů blob v Azure Storage, ve kterém `.xel` budou umístěné soubory
- Syntaxe `TO FILE` se nepodporuje, protože Managed Instance nemá přístup ke sdílené složky Windows.

Další informace naleznete v tématu:  

- [VYTVOŘIT SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [PŘÍKAZ ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditování](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certifikáty

Managed Instance nemůže přistupovat k sdílených složek a složek Windows, platí následující omezení:

- `CREATE FROM`/`BACKUP TO` soubor není podporovaný pro certifikáty
- `CREATE`/`BACKUP` certifikát z `FILE` / `ASSEMBLY` se nepodporuje. Soubory soukromých klíčů nelze použít.  

Zobrazit [vytvořit certifikát](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) a [ZÁLOŽNÍ certifikát](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
**Alternativní řešení**: skript certifikátu a privátního klíče, uložit jako soubor .sql a vytvořit z binárního souboru:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Přihlašovací údaj

Pouze Azure Key Vault a `SHARED ACCESS SIGNATURE` identity podporují. Uživatelé Windows se nepodporují.

V tématu [vytvořit pověření](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) a [ALTER pověření](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Zprostředkovatelé kryptografických služeb

Managed Instance nemají přístup k souborům, takže zprostředkovatelé kryptografických služeb nelze vytvořit:

- `CREATE CRYPTOGRAPHIC PROVIDER` není podporováno. Zobrazit [vytvořit zprostředkovatele KRYPTOGRAFICKÝCH služeb](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` není podporováno. Zobrazit [Změna zprostředkovatele KRYPTOGRAFICKÝCH služeb](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins--users"></a>Přihlášení / uživatelé

- Vytvoření přihlášení SQL `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, a `FROM SID` jsou podporovány. Zobrazit [vytvořit přihlášení](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD) server objekty zabezpečení (přihlášení) vytvořené pomocí [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) syntaxe nebo [vytvořit uživatele z přihlášení [přihlášení k Azure AD]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) syntaxe jsou podporovány (**ve verzi public preview** ). Jedná se o přihlášení, které jsou vytvářeny na úrovni serveru.

    Spravovaná Instance podporuje objekty zabezpečení databáze služby Azure AD se syntaxí `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Toto je také označovaný jako databázi uživatelů Azure AD, které jsou obsažené.

- Přihlašovací údaje Windows vytvořené pomocí `CREATE LOGIN ... FROM WINDOWS` syntaxe nejsou podporovány. Pomocí přihlašovacích údajů Azure Active Directory a uživatelů.
- Obsahuje uživatele Azure AD, který vytvořil instanci [neomezená oprávnění správce](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Je možné vytvářet uživatelé bez oprávnění správce služby Azure Active Directory (Azure AD) úrovni databáze pomocí `CREATE USER ... FROM EXTERNAL PROVIDER` syntaxe. Zobrazit [vytvořit uživatele... Z EXTERNÍHO POSKYTOVATELE](sql-database-manage-logins.md#non-administrator-users).
- Azure AD objekty serveru (přihlášení) podporují funkce SQL v rámci pouze jednoho MI instance. Funkce, které vyžadují různé instance interakce, bez ohledu na to, pokud v rámci stejné služby Azure AD tenant nebo jiného tenanta se nepodporuje pro uživatele Azure AD. Mezi tyto funkce patří:

  - Transakční replikace SQL a
  - Propojit Server

- Nastavení přihlášení Azure AD mapované na skupiny Azure AD, vlastník databáze není podporován.
- Je podporováno zosobnění hlavních účtů na úrovni serveru služby Azure AD pomocí další hlavní služby Azure AD, jako [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) klauzuli. SPUŠTĚNÍ jako omezení:

  - Když název se liší od přihlašovací jméno se pro uživatelů Azure AD nepodporuje EXECUTE AS USER. Například při vytvoření uživatele pomocí syntaxe přihlášení vytvořit uživatele [myAadUser] z [john@contoso.com], a dojde k pokusu o zosobnění prostřednictvím EXEC AS USER = _myAadUser_. Při vytváření **uživatele** z Azure AD server instančního objektu (přihlášení), zadejte uživatelské_jméno jako stejný login_name z **přihlášení**.
  - Pouze SQL serverové objekty zabezpečení (přihlašovací údaje), které jsou součástí `sysadmin` role můžete provádět následující operace cílení na objekty zabezpečení Azure AD:

    - SPUŠTĚNÍ JAKO UŽIVATEL
    - SPUSTIT JAKO PRO PŘIHLÁŠENÍ

- **Ve verzi Public preview** omezení pro objekty zabezpečení serveru Azure AD (přihlášení):

  - Active Directory správce omezení pro spravovanou instanci:

    - Správce služby Azure AD použít ke konfiguraci spravované instanci nelze použít k vytvoření Azure AD objekt zabezpečení serveru (přihlášení) v rámci Managed Instance. Je nutné vytvořit první Azure AD objekt zabezpečení serveru (přihlášení) pomocí účtu systému SQL Server, který je `sysadmin`. Jedná se o dočasné omezení, která bude odebrána po zavedení všeobecné dostupnosti budou objekty serveru Azure AD (přihlášení) Pokud se pokusíte vytvořit přihlášení pomocí účtu správce Azure AD, zobrazí se následující chyba: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - V současné době musí první přihlášení k Azure AD vytvoří v hlavní databázi vytvořit standardní účet systému SQL Server (bez Azure AD), který je `sysadmin` pomocí [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) z EXTERNÍHO poskytovatele. Příspěvek GA, toto omezení budou odebrány a počáteční přihlášení k Azure AD lze vytvořit pomocí Správce Active Directory pro spravovanou instanci.
    - Použít s SQL Server Management Studio (SSMS) nebo SqlPackage DacFx (export a Import) se nepodporuje pro přihlášení Azure AD. Toto omezení se odeberou po zavedení všeobecné dostupnosti budou objekty serveru Azure AD (přihlášení)
    - Objekty zabezpečení serveru Azure AD (přihlášení) pomocí aplikace SSMS

      - Skriptování přihlášení Azure AD (s použitím libovolné ověřeného přihlášení) se nepodporuje.
      - IntelliSense nerozpozná **vytvořit z EXTERNÍHO zprostředkovatele přihlášení** příkaz a zobrazí se červené podtržení.

- Pouze hlavní přihlášení na úrovni serveru (vytvořený ve spravované instanci procesu zřizování), členové role serveru (`securityadmin` nebo `sysadmin`), nebo dalších přihlášení s oprávněními ALTER ANY LOGIN na úrovni serveru můžete vytvořit server Azure AD objekty zabezpečení (přihlášení) v hlavní databázi pro Managed Instance.
- Pokud je přihlášení objektu zabezpečení SQL, pouze přihlašovací údaje, které jsou součástí `sysadmin` role může používat příkazu pro vytvoření k vytvoření přihlášení k účtu služby Azure AD.
- Přihlášení k Azure AD musí být členem skupiny Azure AD v rámci stejného adresáře pro spravované Instance Azure SQL.
- Azure AD objekty serveru (přihlášení) jsou viditelné v Průzkumníku objektů rovnou začít tématem SSMS 18.0 ve verzi preview 5.
- Překrývající se objekty serveru Azure AD (přihlášení) pomocí účtu správce Azure AD je povolená. Azure AD objekty serveru (přihlášení) mít přednost před správce Azure AD při určování oprávnění instančního objektu a vztahující se k Managed Instance.
- Během ověřování následující pořadí se použije k vyřešení ověřování objektu zabezpečení:

    1. Pokud účet Azure AD existuje jako přímo připojené k Azure AD objekt zabezpečení serveru (přihlášení) (k dispozici v sys.server_principals jako typ "E"), udělit přístup a použít oprávnění objektu zabezpečení serveru Azure AD (přihlášení).
    2. Pokud účet Azure AD je členem skupiny Azure AD, který je namapovaný na Azure AD objekt zabezpečení serveru (přihlášení) (k dispozici v sys.server_principals psaní "X"), udělit přístup a oprávnění skupiny přihlášení k Azure AD použít.
    3. Pokud účet Azure AD je speciální nakonfigurován portál správce Azure AD pro spravovanou instanci (neexistuje v systémových zobrazeních Managed Instance), použít speciální oprava oprávnění správce služby Azure AD pro spravovanou instanci (režim starší verze).
    4. Pokud účet Azure AD existuje jako přímo namapován na uživatele služby Azure AD v databázi (v sys.database_principals jako typ "E"), udělit přístup a použít oprávnění uživatele databáze služby Azure AD.
    5. Pokud účet Azure AD je členem skupiny Azure AD, který je namapovaný na uživatele služby Azure AD v databázi (v sys.database_principals jako typ "X"), udělit přístup a oprávnění skupiny přihlášení k Azure AD použít.
    6. Pokud dojde Azure AD přihlašovací jméno mapované na uživatelský účet služby Azure AD nebo účet skupiny Azure AD, řešení pro ověřování uživatele, všechna oprávnění z těchto přihlašovacích údajů Azure AD se použijí.

### <a name="service-key-and-service-master-key"></a>Služby a klíče hlavní klíč služby

- [Záloha hlavního klíče](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) se nepodporuje (spravované službou SQL Database)
- [Obnovení hlavního klíče](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) se nepodporuje (spravované službou SQL Database)
- [Záloha hlavního klíče služby](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) se nepodporuje (spravované službou SQL Database)
- [Obnovení hlavního klíče služby](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) se nepodporuje (spravované službou SQL Database)

## <a name="configuration"></a>Konfigurace

### <a name="buffer-pool-extension"></a>Rozšíření fondu vyrovnávací paměti

- [Rozšíření fondu vyrovnávací paměti](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) se nepodporuje.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` není podporováno. Zobrazit [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Kolace

Je výchozí kolace instance `SQL_Latin1_General_CP1_CI_AS` a je možné zadat jako parametr vytváření. Zobrazit [kolace](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Úrovně kompatibility

- Úrovně kompatibility podporované jsou: 100, 110, 120, 130, 140  
- Nejsou podporované úrovně kompatibility nižší než 100.
- Výchozí úroveň kompatibility pro nové databáze je 140. Pro obnovenou databází zůstane beze změny úrovní kompatibility i pokud se jednalo o 100 a vyšší.

Zobrazit [úroveň kompatibility ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Zrcadlení databáze

Zrcadlení databáze se nepodporuje.

- `ALTER DATABASE SET PARTNER` a `SET WITNESS` možnosti nejsou podporovány.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` není podporováno.

Další informace najdete v tématu [ALTER DATABASE SET PARTNER a SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) a [vytvořit koncový bod... PRO DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Možnosti databáze

- Víc souborů protokolů nejsou podporovány.
- Objekty v paměti nejsou podporovány v rámci úrovně služeb pro obecné účely.  
- Platí limit 280 souborů na jednu instanci obecné zdání maximální 280 počet souborů v databázi. Protokolu a data souborů obecně účel úrovně se počítají do tohoto limitu. [Úroveň pro důležité obchodní podporuje 32 767 počet souborů v databázi](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Databáze nemůže obsahovat příkaz, který obsahuje filestream data.  Obnovení se nezdaří, pokud obsahuje .bak `FILESTREAM` data.  
- Každý soubor je umístěn v úložišti objektů Blob v Azure. Vstupně-výstupních operací a propustnosti na soubor závisí na velikosti jednotlivých souborů.  

#### <a name="create-database-statement"></a>Příkaz CREATE DATABASE

Toto jsou `CREATE DATABASE` omezení:

- Soubory a skupiny souborů nelze definovat.  
- `CONTAINMENT` možnost není podporována.  
- `WITH`možnosti nejsou podporovány.  
   > [!TIP]
   > Jako alternativní řešení, použijte `ALTER DATABASE` po `CREATE DATABASE` nastavit možnosti databáze k přidání souborů nebo k nastavení členství ve skupině.  

- `FOR ATTACH` možnost není podporována.
- `AS SNAPSHOT OF` možnost není podporována.

Další informace najdete v tématu [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Příkaz ALTER DATABASE

Nelze nastavit nebo změnit některé vlastnosti souboru:

- Cesta k souboru nemůže být zadán `ALTER DATABASE ADD FILE (FILENAME='path')` příkazu T-SQL. Odebrat `FILENAME` ze skriptu protože Managed Instance automaticky umístí soubory.  
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

- Nastavení agenta SQL jsou jen pro čtení. Postup `sp_set_agent_properties` není ve spravované instanci podporováno.  
- Úlohy
  - Podporují se kroky úlohy T-SQL.
  - Podporují se následující úlohy replikace:
    - Čtečky protokolů transakcí
    - Snímek
    - Distributor
  - Kroky úlohy služby SSIS se podporují.
  - Jiné druhy kroky úlohy se aktuálně nepodporují, včetně:
    - Krok úlohy slučovací replikace nepodporuje.  
    - Čtečky fronty se nepodporuje.  
    - Příkazové okno se ještě nepodporuje.
  - Spravované instance nemá přístup k externím prostředkům (například sdílené síťové složky prostřednictvím nástroje robocopy).  
  - Prostředí PowerShell se ještě nepodporuje.
  - Analysis Services nejsou podporovány.
- Oznámení jsou podporovány jen částečně.
- E-mailových oznámení je podporováno, vyžaduje konfiguraci profil databázového e-mailu. Může existovat pouze jedna databáze profil e-mailu a musí být volána `AzureManagedInstance_dbmail_profile` ve verzi public preview (dočasné omezení).  
  - Operátor není podporován.  
  - Příkazu není podporován.
  - Výstrahy se zatím nepodporují.
  - Nepodporuje proxy servery.  
- Protokol událostí se nepodporuje.

Následující funkce nejsou aktuálně podporovány, ale bude v budoucnu povoleno:

- Proxy
- Plánování úloh na nečinnosti procesoru
- Povolení nebo zakázání agenta
- Výstrahy

Informace o agenta systému SQL Server najdete v tématu [agenta systému SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabulky

Není podporované toto:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED`  

Informace o vytváření a změny tabulek naleznete v tématu [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) a [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkce

### <a name="bulk-insert--openrowset"></a>Příkaz Bulk insert / openrowset

Managed Instance nelze získat přístup k sdílených složek a složek Windows, tak soubory musí být importovány z úložiště objektů Blob v Azure:

- `DATASOURCE` je nutné v `BULK INSERT` příkaz při importu souborů z úložiště objektů Blob v Azure. Zobrazit [HROMADNÉ vložení](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` je nutné v `OPENROWSET` fungovat-li si přečíst obsah souboru z úložiště objektů Blob v Azure. Zobrazit [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Managed Instance nemůže přistupovat k sdílených složek a složek Windows, platí následující omezení:

- Pouze `CREATE ASSEMBLY FROM BINARY` je podporována. Zobrazit [vytvořit sestavení z binární](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` není podporováno. Zobrazit [vytvořit sestavení ze souboru](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` nemůže odkazovat na soubory. Zobrazit [změna sestavení](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Managed instance nepodporuje nedokumentované příkazů DBCC, u kterých jde v systému SQL Server.

- `Trace Flags` nejsou podporovány. Zobrazit [příznaky trasování](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` není podporováno. Zobrazit [příkaz DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` není podporováno. Zobrazit [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Distribuované transakce

Ani jedna služba MSDTC ani [elastické transakce](sql-database-elastic-transactions-overview.md) jsou aktuálně podporované ve spravovaných instancí.

### <a name="extended-events"></a>Rozšířené události

Některé cíle Windows specifické pro události Xevent nepodporuje:

- `etw_classic_sync target` není podporováno. Store `.xel` souborů v Azure blob storage. See [etw_classic_sync target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file target` není podporováno. Store `.xel` souborů v Azure blob storage. Zobrazit [event_file cílové](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externí knihovny

V databázi R a Python externí knihovny se zatím nepodporují. Zobrazit [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream a Filetable

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

- Podporované cíle: SQL Server a SQL Database
- Nepodporuje cíle: soubory služby Analysis Services a další relační databázový systém.

Operace

- Různé instance zápisu transakce nejsou podporovány.
- `sp_dropserver` platí pro odkazovaný server vyřadit. See [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET` funkci můžete použít k provádění dotazů pouze na instancích systému SQL Server (buď spravovaných, místně nebo ve virtuálních počítačích). Zobrazit [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE` funkci můžete použít k provádění dotazů pouze na instancích systému SQL Server (buď spravovaných, místně nebo ve virtuálních počítačích). Pouze `SQLNCLI`, `SQLNCLI11`, a `SQLOLEDB` hodnoty jsou podporovány jako zprostředkovatel. Například: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Zobrazit [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>Polybase

Odkazování na soubory v HDFS nebo Azure Blob storage externí tabulky nejsou podporovány. Informace o Polybase najdete v tématu [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikace

Replikace je dostupná ve veřejné verzi preview pro Managed Instance. Informace o replikaci najdete v tématu [replikace systému SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>OBNOVENÍ – příkaz

- Podporované syntaxe
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Nepodporovaná syntaxe
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Zdroj  
  - `FROM URL` (Úložiště objektů Blob v azure) je pouze podporované možnosti.
  - `FROM DISK`/`TAPE`/ zálohovací zařízení se nepodporuje.
  - Zálohovacích skladů nejsou podporovány.
- `WITH` možnosti nejsou podporovány (ne `DIFFERENTIAL`, `STATS`atd.)
- `ASYNC RESTORE` – Obnovení pokračovat i v případě, že přestane fungovat připojení klienta. Pokud vaše připojení se ukončí, můžete zkontrolovat `sys.dm_operation_status` zobrazení stavu operace obnovení (stejně jako pro vytvoření a DROP database). Zobrazit [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  

Následující možnosti databáze jsou set/přepsat a není možné později změnit:  

- `NEW_BROKER` (Pokud je zprostředkovatel není povolen v souboru .bak)  
- `ENABLE_BROKER` (Pokud je zprostředkovatel není povolen v souboru .bak)  
- `AUTO_CLOSE=OFF` (Pokud je databáze v souboru .bak `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (Pokud je databáze v souboru .bak `SIMPLE` nebo `BULK_LOGGED` režimu obnovení)
- Paměťově optimalizované skupiny souborů a je volána XTP, pokud nebylo ve zdrojovém souboru .bak  
- Žádné existující skupiny paměťově optimalizovaných souborů bylo přejmenováno na XTP  
- `SINGLE_USER` a `RESTRICTED_USER` možnosti jsou převedeny na `MULTI_USER`

Omezení:  

- `.BAK` nelze obnovit, soubory, které obsahují více sad záloh.
- `.BAK` nelze obnovit, soubory, které obsahují víc souborů protokolů.
- Obnovení se nezdaří, pokud obsahuje .bak `FILESTREAM` data.
- Nelze obnovit, zálohy obsahující databáze, které máte aktuálně aktivní v paměti objektů.  
- Zálohování obsahující databáze, kde v určitém okamžiku objektů v paměti existoval aktuálně není možné obnovit.
- Zálohování databáze v režimu jen pro čtení obsahující aktuálně není možné obnovit. Toto omezení bude brzy odebráno.

Informace o příkazech obnovení najdete v tématu [obnovit příkazy](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Služba Service broker

Různé instance služby Service broker se nepodporuje:

- `sys.routes` -Předpoklad: Vyberte adresu sys.routes. Adresa musí být místní na každý trasy. Zobrazit [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE` – nemůžete použít `CREATE ROUTE` s `ADDRESS` jiné než `LOCAL`. Zobrazit [TRASY vytvořit](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE` nelze `ALTER ROUTE` s `ADDRESS` jiné než `LOCAL`. Zobrazit [TRASY ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  

### <a name="stored-procedures-functions-triggers"></a>Uložené procedury, funkce, aktivační události

- `NATIVE_COMPILATION` v úrovni General Purpose nepodporuje.
- Následující [uložené procedury sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) možnosti nejsou podporovány:
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` není podporováno. Zobrazit [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` není podporováno. Zobrazit [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` nejsou podporované. zahrnuje `sp_addextendedproc` a `sp_dropextendedproc`. Zobrazit [rozšířené uložené procedury](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- `sp_attach_db`, `sp_attach_single_file_db`, a `sp_detach_db` nejsou podporovány. Zobrazit [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), a [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Changes"></a> Změny chování

Následující proměnné, funkce a zobrazení vrátí odlišné výsledky:

- `SERVERPROPERTY('EngineEdition')` Vrátí hodnotu 8. Tato vlastnost jednoznačně identifikuje Managed Instance. Zobrazit [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Vrátí hodnotu NULL, protože pro SQL Server se nevztahuje na Managed Instance existuje koncept instance, jak ho. Zobrazit [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Vrátí úplný "umožňující připojení k" název DNS, třeba Moje instance.wcus17662feb9ce98.database.windows.net spravované. Zobrazit [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -vrátí úplnou "umožňující připojení k" název DNS, jako například `myinstance.domain.database.windows.net` pro vlastnosti "name" a "data_source". Zobrazit [SYS. SERVERY](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` Vrátí hodnotu NULL, protože koncept služby, protože existuje pro SQL Server se nevztahuje na Managed Instance. Zobrazit [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` je podporován. Vrátí hodnotu NULL, pokud není v sys.syslogins přihlášení k Azure AD. Zobrazit [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` není podporováno. Vrátí nesprávná data (dočasný známý problém). Zobrazit [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql).
- `GETDATE()` a další předdefinované datum a čas funkce vždy vrátí čas v časovém pásmu UTC. Zobrazit [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a> Známé problémy a omezení

### <a name="tempdb-size"></a>Velikost databáze TEMPDB

Maximální velikost souboru `tempdb` nemůže být větší než 24 GB/core v úrovni General Purpose. Maximální počet `tempdb` limitují velikost na úrovni pro důležité obchodní informace s velikostí úložiště instance. `tempdb` vždy je rozdělený do 12 datových souborů. Tento maximální velikost jednoho souboru nelze změnit, a nové soubory mohou být přidány do `tempdb`. Některé dotazy může vrátit chybu, pokud je nutné více než 24GB za jádro v `tempdb`.

### <a name="cannot-restore-contained-database"></a>Nelze obnovit databáze s omezením

Nelze obnovit spravované Instance [databáze s omezením](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Obnovení bodu v čase z existující databáze s omezením nefungují na Managed Instance. Tento problém bude brzy odebráno a do té doby doporučujeme k odebrání možnost omezení databáze, které se umístí na spravované instanci a nepoužívejte možnost členství ve skupině pro produkční databáze.

### <a name="exceeding-storage-space-with-small-database-files"></a>Překročení prostoru úložiště se soubory malé databáze

Každý obecné účely mi má vyhrazené pro místa na disku Azure Premium storage 35 TB a každý databázový soubor je umístěn na jiném fyzickém disku. Disky o velikosti může být 128 GB, 256 GB, 512 GB, 1 TB nebo 4 TB. Nevyužité místo na disku se neúčtuje, ale celkový součet velikosti disků typu Premium Azure nemůže být delší než 35 TB. V některých případech může překročit Managed Instance, která nepotřebuje 8 TB celkem 35 TB Azure omezí velikost úložiště, z důvodu interní fragmentace.

Obecné účely Managed Instance může mít například jeden soubor 1,2 TB velikosti, který je umístěn na disku 4 TB a 248 soubory (každý 1 GB velikost), které jsou umístěné na různých discích 128 GB. V tomto příkladu:

- Celkové přidělené úložiště velikosti disku je 1 × 4 TB + 248 x 128 GB = 35 TB.
- Celkový počet vyhrazené místo pro databáze na instanci je 1 x 1.2 TB + 248 x 1 GB = 1,4 TB.

To ukazuje, že za určitých okolností kvůli konkrétní distribuce souborů, Managed Instance může kontaktovat 35 TB vyhrazené pro připojený Disk Premium Azure při nemusí očekávání.

V tomto příkladu budou nadále fungovat stávající databáze a můžou růst bez problémů, tak dlouho, dokud nejsou přidány nové soubory. Nové databáze však nelze vytvořit ani obnovit, protože není dostatek místa pro nových diskových jednotek, i když celková velikost všech databází: nebylo dosaženo omezení velikosti instance. Chyba, která je vrácena v takovém případě není jasný.

Je možné [identifikovat počet zbývajících souborů](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) pomocí zobrazení systému. Pokud se snažíte tohoto omezení pokusí [prázdný a některé menší souborů pomocí příkazu DBCC SHRINKFILE odstranit](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) nebo shitch k [úroveň pro důležité obchodní informace, nemusí se tento limit](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Nesprávná konfigurace SAS klíč při databáze obnovení

`RESTORE DATABASE` který čte soubor .bak může být neustále opakování pokusu o číst soubor .bak a vrátí chybové po dlouhou dobu, pokud sdíleného přístupového podpisu v `CREDENTIAL` je nesprávný. Před obnovením databáze, ujistěte se, že se klíč SAS pro správné spuštění RESTORE HEADERONLY.
Ujistěte se, že odeberete úvodního `?` z klíče SAS, který je generován pomocí webu Azure portal.

### <a name="tooling"></a>Nástroje

SQL Server Management Studio (SSMS) a SQL Server Data Tools (SSDT) může mít některé problémy při přístupu k Managed Instance.

- Pomocí serveru Azure AD (přihlášení) – objekty zabezpečení a uživatele (**ve verzi public preview**) s rozšířením SSDT v tuto chvíli nepodporuje.
- Skriptování pro server Azure AD (přihlášení) – objekty zabezpečení a uživatele (**ve verzi public preview**) nejsou podporovány v aplikaci SSMS.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Názvy nesprávné databází v některých zobrazeních, protokoly a zpráv

Několik zobrazení systému, čítače výkonu, chybové zprávy, XEvents a záznamů v protokolu chyb se zobrazí identifikátory GUID databáze namísto názvů skutečné databáze. Nespoléhejte na tyto identifikátory GUID vzhledem k tomu, že by měl být nahrazen názvy skutečné databáze v budoucnu.

### <a name="database-mail-profile"></a>Profil databázového e-mailu

Profil databázové pošty používat SQL Agent musí být volána `AzureManagedInstance_dbmail_profile`.

### <a name="error-logs-are-not-persisted"></a>Protokoly chyb jsou trvalé not

Protokoly chyb, které jsou k dispozici ve spravované instanci nejsou trvalé a jejich velikost není součástí limit maximální velikosti úložiště. Protokoly chyb, může v případě převzetí služeb při selhání automaticky vymaže.

### <a name="error-logs-are-verbose"></a>Jsou podrobné protokoly chyb

Podrobné informace o Managed Instance umístí protokoly chyb a mnoho z nich nejsou relevantní. V budoucnu bude možné snížit množství informací v protokolech chyb.

**Alternativní řešení**: Vlastní procedura použita pro čtení protokoly chyb, které filtr na více instancí některých – příslušné položky. Podrobnosti najdete v tématu [Managed Instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Obor transakce ve dvou databázích v rámci stejné instance se nepodporuje.

`TransactionScope` třídy v rozhraní .NET nefunguje, pokud dva dotazy se odesílají do dvou databází v rámci stejné instance v rámci stejného oboru transakce:

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

Přestože tento kód pracuje s daty v rámci stejné instance nezbytné MSDTC.

**Alternativní řešení**: Použít [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) použití jiné databáze v kontextu připojení místo použití dvě připojení.

### <a name="clr-modules-and-linked-servers-sometime-cant-reference-local-ip-address"></a>Moduly CLR a nějakou dobu propojené servery nemůže odkazovat na místní IP adresa

Moduly CLR umístí do Managed Instance a propojené servery pro/distribuované dotazy, které se odkazuje na aktuální instanci nějakou dobu nelze přeložit IP místní instance. Tato chyba je přechodný problém.

**Alternativní řešení**: Pokud je to možné použijte připojení kontextu v modulu CLR.

### <a name="tde-encrypted-databases-dont-support-user-initiated-backups"></a>Transparentní šifrování dat šifrované databáze nepodporují zálohování iniciovaná uživatelem

Nelze provést `BACKUP DATABASE ... WITH COPY_ONLY` na databázi, která je zašifrovaná pomocí transparentního šifrování dat (TDE). Transparentní šifrování dat vynutí zálohy šifrované pomocí interní klíče TDE a nedá se exportovat klíč, proto nebudete moci obnovit zálohu.

**Alternativní řešení**: Použít automatické zálohování a obnovení k určitému bodu v čase, nebo zakázat šifrování v databázi.

## <a name="next-steps"></a>Další postup

- Podrobnosti o spravovaných instancí najdete v tématu [co je Managed Instance?](sql-database-managed-instance.md)
- Pro funkce a seznam porovnání, naleznete v tématu [běžné funkce SQL](sql-database-features.md).
- Rychlý start ukazuje, jak vytvořit nový Managed Instance, naleznete v tématu [vytvoření Managed Instance](sql-database-managed-instance-get-started.md).
