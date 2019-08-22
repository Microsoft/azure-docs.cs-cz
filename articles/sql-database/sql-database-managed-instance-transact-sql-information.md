---
title: Azure SQL Databasech rozdílů v jazyce T-SQL spravované instance | Microsoft Docs
description: Tento článek popisuje rozdíly v T-SQL mezi spravovanou instancí v Azure SQL Database a SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 08/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 8ed9b86f8dd4f255a6ea8420ef27fbb131df91a9
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69644882"
---
# <a name="managed-instance-t-sql-differences-limitations-and-known-issues"></a>Rozdíly v jazyce T-SQL spravované instance, omezení a známé problémy

Tento článek shrnuje a vysvětluje rozdíly v syntaxi a chování mezi Azure SQL Database spravované instance a místním databázovým strojem SQL Server. Možnost nasazení Managed instance poskytuje vysokou kompatibilitu s místními SQL Server databázovým strojem. Většina funkcí databázového stroje SQL Server je ve spravované instanci podporovaná.

![Migrace](./media/sql-database-managed-instance/migration.png)

Existují některá omezení PaaS, která se zavádějí do spravované instance, a některé změny chování v porovnání s SQL Server. Rozdíly jsou rozděleny do následujících kategorií:<a name="Differences"></a>

- [Dostupnost](#availability) zahrnuje rozdíly v části [vždy zapnuto](#always-on-availability) a [zálohování](#backup).
- [Zabezpečení](#security) zahrnuje rozdíly v [auditování](#auditing), [certifikáty](#certificates), [přihlašovací údaje](#credential), [zprostředkovatele kryptografických](#cryptographic-providers)služeb, [přihlášení a uživatele](#logins-and-users)a [klíč služby a hlavní klíč služby](#service-key-and-service-master-key).
- [Konfigurace](#configuration) zahrnuje rozdíly v [rozšíření fondu vyrovnávací paměti](#buffer-pool-extension), [řazení](#collation), [úrovně kompatibility](#compatibility-levels), [zrcadlení databáze](#database-mirroring), [Možnosti databáze](#database-options), [SQL Server agenta](#sql-server-agent)a [Možnosti tabulky](#tables).
- Mezi [funkce](#functionalities) patří [Bulk INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [distribuované transakce](#distributed-transactions), [Rozšířené události](#extended-events), [externí knihovny](#external-libraries), [FileStream a Souborová](#filestream-and-filetable)vlastnost, fulltextový zápis. [ Sémantické vyhledávání](#full-text-semantic-search), [propojené servery](#linked-servers), [základní](#polybase), [replikace](#replication), [obnovení](#restore-statement), [Service Broker](#service-broker), [uložené procedury, funkce a triggery](#stored-procedures-functions-and-triggers).
- [Nastavení prostředí](#Environment) , jako jsou například virtuální sítě a konfigurace podsítí.

Většina těchto funkcí je omezení architektury a představuje funkce služby.

Tato stránka také vysvětluje [dočasné známé problémy](#Issues) , které jsou zjištěny ve spravované instanci, které budou v budoucnu vyřešeny.

## <a name="availability"></a>Dostupnost

### <a name="always-on-availability"></a>Vždy zapnuto

[Vysoká dostupnost](sql-database-high-availability.md) je integrována do spravované instance a nemůže být řízena uživateli. Následující příkazy nejsou podporovány:

- [VYTVOŘIT KONCOVÝ BOD... PRO DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [VYTVOŘIT SKUPINU DOSTUPNOSTI](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ZMĚNIT SKUPINU DOSTUPNOSTI](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [ODPOJIT SKUPINU DOSTUPNOSTI](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- Klauzule [set hadr](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) příkazu [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Zálohovat

Spravované instance mají automatické zálohování, takže uživatelé můžou vytvářet úplné zálohy `COPY_ONLY` databáze. Zálohy rozdílů, protokolů a snímků souborů se nepodporují.

- Se spravovanou instancí můžete zálohovat databázi instance pouze do účtu služby Azure Blob Storage:
  - Podporuje `BACKUP TO URL` se jenom.
  - `FILE``TAPE`a zálohovací zařízení nejsou podporována.
- Většina obecných `WITH` možností je podporovaná.
  - `COPY_ONLY`je povinné.
  - `FILE_SNAPSHOT`není podporováno.
  - Možnosti pásky: `REWIND`, `NOREWIND`, `UNLOAD`a `NOUNLOAD` nejsou podporovány.
  - Možnosti specifické pro protokol: `NORECOVERY`, `STANDBY`a `NO_TRUNCATE` nejsou podporovány.

Určitá 

- Se spravovanou instancí můžete zálohovat databázi instance do zálohy s až 32 proužky, které jsou pro databáze až 4 TB v případě, že se používá zálohování zálohy, v případě, že je použita komprese záloh.
- Nemůžete `BACKUP DATABASE ... WITH COPY_ONLY` provést na databázi, která je zašifrovaná pomocí transparentní šifrování dat pro správu spravovaných službou (TDE). TDE spravované službou vynutí šifrování záloh pomocí interního TDE klíče. Klíč nelze exportovat, takže nelze obnovit zálohu. Použijte automatické zálohování a obnovení k bodu v čase nebo použijte místo toho [TDE spravované zákazníkem (BYOK)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) . Šifrování můžete také zakázat v databázi.
- Maximální velikost záložního proužku pomocí `BACKUP` příkazu ve spravované instanci je 195 GB, což je maximální velikost objektu BLOB. Zvyšte počet pruhů v příkazu Backup, abyste snížili velikost jednotlivých pruhů a zůstali v rámci tohoto limitu.

    > [!TIP]
    > Pokud chcete toto omezení obejít, můžete při zálohování databáze z libovolného SQL Server v místním prostředí nebo ve virtuálním počítači:
    >
    > - Zálohování na `DISK` místo zálohování do `URL`.
    > - Nahrajte záložní soubory do úložiště objektů BLOB.
    > - Obnovit do spravované instance.
    >
    > `Restore` Příkaz ve spravované instanci podporuje v záložních souborech větší velikosti objektů blob, protože pro ukládání souborů zálohy se používá jiný typ objektu BLOB.

Informace o zálohování pomocí T-SQL najdete v tématu [zálohování](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Zabezpečení

### <a name="auditing"></a>Auditování

Hlavní rozdíly mezi auditováním v databázích v Azure SQL Database a databázích v SQL Server jsou:

- S možností nasazení Managed instance v Azure SQL Database funguje auditování na úrovni serveru. Soubory `.xel` protokolu se ukládají v úložišti objektů BLOB v Azure.
- S možností nasazení jedna databáze a elastický fond v Azure SQL Database funguje auditování na úrovni databáze.
- V SQL Server místních nebo virtuálních počítačích funguje auditování na úrovni serveru. Události se ukládají do systému souborů nebo protokolů událostí systému Windows.
 
Auditování XEvent ve spravované instanci podporuje cíle služby Azure Blob Storage. Soubory a protokoly systému Windows nejsou podporovány.

Hlavní rozdíly v `CREATE AUDIT` syntaxi pro auditování do úložiště objektů BLOB v Azure jsou:

- K dispozici `TO URL` je nová syntaxe, kterou můžete použít k zadání adresy URL kontejneru úložiště objektů BLOB v Azure, `.xel` ve kterém jsou soubory umístěné.
- Syntaxe `TO FILE` není podporována, protože spravovaná instance nemůže přistupovat ke sdíleným složkám souborů systému Windows.

Další informace naleznete v tématu: 

- [VYTVOŘIT AUDIT SERVERU](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditování](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certifikáty

Spravovaná instance nemůže přistupovat ke sdíleným složkám souborů a složkám systému Windows, proto platí následující omezení:

- Souborneníprocertifikáty`CREATE FROM`podporovaný. / `BACKUP TO`
- `CREATE` Certifikátod`ASSEMBLY` není podporovaný. `FILE` / `BACKUP` / Soubory privátních klíčů nelze použít. 

Viz [Vytvoření certifikátu](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) a [záložního certifikátu](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Alternativní řešení**: Skript pro certifikát nebo privátní klíč, Uložit jako soubor. SQL a vytvořit z binárního souboru:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Pověření

Jsou podporovány pouze `SHARED ACCESS SIGNATURE` Azure Key Vault a identity. Uživatelé systému Windows nejsou podporováni.

Viz [Vytvoření přihlašovacích údajů](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) a [Změna přihlašovacích údajů](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Zprostředkovatelé kryptografických služeb

Spravovaná instance nemá přístup k souborům, takže nejde vytvořit zprostředkovatele kryptografických služeb:

- `CREATE CRYPTOGRAPHIC PROVIDER`není podporováno. Viz [vytvořit zprostředkovatele kryptografických služeb](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER`není podporováno. Přečtěte si téma [Změna zprostředkovatele kryptografických služeb](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Přihlášení a uživatelé

- Přihlášení SQL vytvořená pomocí `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`a `FROM SID` jsou podporovaná. Viz [Vytvoření přihlašovacích](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)údajů.
- Jsou podporovány základní objekty (přihlašovací údaje) serveru služby Azure Active Directory (Azure AD) vytvořené pomocí syntaxe pro [Vytvoření přihlášení](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) nebo [Vytvoření uživatele z přihlášení [přihlášení Azure AD]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) (verze Public Preview). Tato přihlášení jsou vytvořena na úrovni serveru.

    Spravovaná instance podporuje objekty zabezpečení databáze Azure AD se syntaxí `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Tato funkce se označuje taky jako uživatelé databáze s omezením Azure AD.

- Přihlášení Windows vytvořená pomocí `CREATE LOGIN ... FROM WINDOWS` syntaxe nejsou podporovaná. Používejte Azure Active Directory přihlašovacích údajů a uživatelů.
- Uživatel Azure AD, který vytvořil instanci, má [neomezená oprávnění správce](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Uživatele na `CREATE USER ... FROM EXTERNAL PROVIDER` úrovni databáze služby Azure AD, kteří nejsou správci, se dají vytvořit pomocí syntaxe. Viz [vytvořit uživatele... OD EXTERNÍHO POSKYTOVATELE](sql-database-manage-logins.md#non-administrator-users).
- Objekty zabezpečení serveru Azure AD (přihlášení) podporují jenom funkce SQL v rámci jedné spravované instance. Funkce, které vyžadují interakci mezi instancemi, bez ohledu na to, jestli jsou ve stejném tenantovi Azure AD nebo v různých klientech, se pro uživatele Azure AD nepodporují. Příklady takových funkcí:

  - Transakční replikace SQL.
  - Propojit Server.

- Nastavení přihlašovacích údajů Azure AD namapované na skupinu Azure AD, protože vlastník databáze není podporovaný.
- Je podporováno zosobnění objektů zabezpečení na úrovni serveru Azure AD pomocí jiných objektů zabezpečení Azure AD, jako je například klauzule [Execute as](/sql/t-sql/statements/execute-as-transact-sql) . Spustit jako omezení jsou:

  - Příkaz Spustit jako uživatel není podporován pro uživatele Azure AD, pokud se název liší od přihlašovacího jména. Příkladem je, že uživatel je vytvořen pomocí syntaxe CREATE USER [myAadUser] from Login [john@contoso.com] a při pokusu o zosobnění se provádí pomocí příkazu EXEC jako uživatel = _myAadUser_. Když vytváříte uživatele z objektu zabezpečení serveru Azure AD (přihlášení), zadejte **uživatelské** jméno jako stejné Login_name od **přihlášení**.
  - Jenom objekty zabezpečení na úrovni SQL Server (přihlášení), které jsou součástí `sysadmin` role, můžou spouštět následující operace, které cílí na objekty zabezpečení Azure AD:

    - SPUSTIT JAKO UŽIVATEL
    - SPUSTIT JAKO PŘIHLAŠOVACÍ ÚDAJE

- Omezení veřejné verze Preview pro objekty zabezpečení serveru Azure AD (přihlášení):

  - Omezení správce služby Active Directory pro spravovanou instanci:

    - Správce Azure AD, který se používá k nastavení spravované instance, se nedá použít k vytvoření objektu zabezpečení serveru Azure AD (přihlášení) v rámci spravované instance. První objekt zabezpečení serveru Azure AD (přihlášení) musíte vytvořit pomocí účtu SQL Server, který je `sysadmin` rolí. Toto dočasné omezení se odebere po zpřístupnění objektů zabezpečení serveru Azure AD (přihlášení) všeobecně dostupných. Pokud se pokusíte vytvořit přihlášení pomocí účtu správce Azure AD, zobrazí se následující chyba:`Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - V současné době musí být první přihlášení služby Azure AD vytvořené v hlavní databázi vytvořeno standardním účtem SQL Server (mimo Azure AD), který je `sysadmin` rolí, pomocí [Vytvoření přihlašovacích](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) údajů z externího poskytovatele. Po obecné dostupnosti se toto omezení odebere. Pak můžete vytvořit počáteční přihlášení k Azure AD pomocí Správce služby Active Directory pro spravovanou instanci.
    - DacFx (export/import) používané s SQL Server Management Studio nebo SqlPackage se pro přihlášení k Azure AD nepodporují. Toto omezení se odebere po zpřístupnění objektů zabezpečení serveru Azure AD (přihlášení) všeobecně dostupných.
    - Použití objektů zabezpečení serveru Azure AD (přihlášení) s SQL Server Management Studio:

      - Skriptování přihlašovacích údajů Azure AD, které používají jakékoli ověřené přihlášení, se nepodporuje.
      - IntelliSense nerozpozná příkaz CREATE LOGIN z externího poskytovatele a zobrazí červené podtržení.

- Azure AD může vytvořit jenom přihlášení objektu na úrovni serveru, které je vytvořené procesem zřizování spravované instance, členové rolí serveru, jako `securityadmin` jsou nebo `sysadmin`, nebo jiná přihlášení s oprávněním změnit libovolné přihlašovací údaje na úrovni serveru. objekty zabezpečení serveru (přihlášení) v hlavní databázi pro spravovanou instanci.
- Pokud je přihlášení objektem zabezpečení SQL, můžou k vytvoření přihlašovacích údajů `sysadmin` pro účet Azure AD používat jenom přihlášení, která jsou součástí této role.
- Přihlášení Azure AD musí být členem služby Azure AD v rámci stejného adresáře, který se používá pro Azure SQL Database spravovanou instanci.
- Objekty zabezpečení serveru Azure AD (přihlášení) se zobrazují v Průzkumník objektů počínaje verzí SQL Server Management Studio 18,0 Preview 5.
- Překrývající se objekty zabezpečení serveru Azure AD (přihlášení) s účtem správce Azure AD jsou povolené. Objekty zabezpečení serveru Azure AD (přihlášení) mají přednost před správcem Azure AD při vyřešení objektu zabezpečení a uplatnění oprávnění pro spravovanou instanci.
- Při ověřování se pro řešení ověřování objektu zabezpečení používá následující sekvence:

    1. Pokud účet Azure AD existuje přímo namapovaný na objekt zabezpečení serveru Azure AD (přihlášení), který se nachází v zobrazení sys. server_principals jako typ "E", udělte přístup a uplatní se oprávnění pro objekt zabezpečení serveru Azure AD (přihlášení).
    2. Pokud je účet Azure AD členem skupiny Azure AD, která je namapovaná na objekt zabezpečení serveru Azure AD (přihlášení), který se nachází v zobrazení sys. server_principals jako typ X, udělte přístup a uplatní se oprávnění pro přihlášení ke skupině Azure AD.
    3. Pokud je účet Azure AD zvláštní portálem konfigurovaný správce Azure AD pro spravovanou instanci, která neexistuje v zobrazeních systému spravované instance, použijte zvláštní pevná oprávnění správce Azure AD pro spravovanou instanci (starší režim).
    4. Pokud účet Azure AD existuje jako přímo mapovaný k uživateli Azure AD v databázi, která je k dispozici v zobrazení sys. database_principals jako typ "E", udělte přístup a uplatní se oprávnění uživatele databáze Azure AD.
    5. Pokud je účet Azure AD členem skupiny Azure AD, která je namapovaná na uživatele Azure AD v databázi, která je k dispozici v zobrazení sys. database_principals jako Type "X", udělte přístup a uplatní se oprávnění pro přihlášení ke skupině Azure AD.
    6. Pokud máte přihlášení ke službě Azure AD namapované na uživatelský účet Azure AD nebo na účet skupiny Azure AD, který se přeloží na uživatele, který ověřuje, budou použita všechna oprávnění z tohoto přihlášení služby Azure AD.

### <a name="service-key-and-service-master-key"></a>Klíč služby a hlavní klíč služby

- [Záloha hlavního klíče](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) není podporována (spravováno službou SQL Database).
- [Obnovení hlavního klíče](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) se nepodporuje (spravuje služba SQL Database).
- [Záloha hlavního klíče služby](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) není podporovaná (spravovaná službou SQL Database).
- [Obnovení hlavního klíče služby](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) se nepodporuje (spravuje služba SQL Database).

## <a name="configuration"></a>Konfiguraci

### <a name="buffer-pool-extension"></a>Rozšíření fondu vyrovnávací paměti

- [Rozšíření fondu vyrovnávací paměti](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) se nepodporuje.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`není podporováno. Viz [ALTER Server Configuration](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Kolace

Výchozí kolace instance je `SQL_Latin1_General_CP1_CI_AS` a může být zadána jako parametr vytvoření. Viz [kolace](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Úrovně kompatibility

- Podporované úrovně kompatibility jsou 100, 110, 120, 130 a 140.
- Úrovně kompatibility nižší než 100 nejsou podporovány.
- Výchozí úroveň kompatibility pro nové databáze je 140. V případě obnovených databází zůstane úroveň kompatibility beze změny, pokud byla 100 a vyšší.

Viz část [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Zrcadlení databáze

Zrcadlení databáze se nepodporuje.

- `ALTER DATABASE SET PARTNER`a `SET WITNESS` možnosti nejsou podporovány.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`není podporováno.

Další informace najdete v tématu věnovaném [příkazu ALTER DATABASE set partner a nastavení určujícího disku](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) a [Vytvoření koncového bodu... PRO DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Možnosti databáze

- Více souborů protokolu není podporováno.
- Objekty v paměti nejsou podporovány v Pro obecné účely úrovni služby. 
- Počet 280 souborů na instanci Pro obecné účely, což implikuje maximálně 280 souborů na databázi. Do tohoto limitu se počítají data i soubory protokolů v Pro obecné účely vrstvě. [Úroveň pro důležité obchodní informace podporuje 32 767 souborů na databázi](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Databáze nemůže obsahovat skupiny souborů, které obsahují data FILESTREAM. Obnovení se nezdařilo, pokud `FILESTREAM` . bak obsahuje data. 
- Každý soubor je umístěný v úložišti objektů BLOB v Azure. Vstupně-výstupní operace a propustnost na soubor závisí na velikosti každého jednotlivého souboru.

#### <a name="create-database-statement"></a>Příkaz CREATE DATABASE

Následující omezení platí pro `CREATE DATABASE`:

- Soubory a skupiny souborů nelze definovat. 
- `CONTAINMENT` Možnost není podporována. 
- `WITH`možnosti nejsou podporovány. 
   > [!TIP]
   > Alternativním řešením je použít `ALTER DATABASE` po `CREATE DATABASE` nastavení možností databáze k přidávání souborů nebo k nastavení zahrnutí. 

- `FOR ATTACH` Možnost není podporována.
- `AS SNAPSHOT OF` Možnost není podporována.

Další informace najdete v tématu [Vytvoření databáze](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>ALTER DATABASE – příkaz

Některé vlastnosti souboru nejde nastavit nebo změnit:

- V `ALTER DATABASE ADD FILE (FILENAME='path')` příkazu t-SQL nejde zadat cestu k souboru. Odeberte `FILENAME` ze skriptu, protože spravované instance automaticky umístí soubory. 
- Název souboru se nedá změnit pomocí `ALTER DATABASE` příkazu.

Ve výchozím nastavení jsou nastaveny následující možnosti a nelze je změnit:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Následující možnosti nelze upravit:

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

- Povolení a zakázání agenta SQL Server v současnosti není ve spravované instanci podporováno. Agent SQL je vždycky spuštěný.
- Nastavení agenta SQL Server jsou jen pro čtení. Procedura `sp_set_agent_properties` není ve spravované instanci podporována. 
- Úlohy
  - Kroky úlohy T-SQL jsou podporovány.
  - Podporovány jsou následující úlohy replikace:
    - Čtečka protokolu transakcí
    - Snímek
    - Rozdělovač
  - Jsou podporovány kroky úlohy SSIS.
  - Jiné typy kroků úlohy se momentálně nepodporují:
    - Krok úlohy sloučení replikace se nepodporuje. 
    - Čtečka fronty není podporována. 
    - Příkazové prostředí se zatím nepodporuje.
  - Spravované instance nemůžou přistupovat k externím prostředkům, třeba ke sdíleným složkám prostřednictvím nástroje Robocopy. 
  - SQL Server Analysis Services nejsou podporovány.
- Oznámení jsou částečně podporovaná.
- E-mailové oznámení je podporované, i když vyžaduje, abyste nakonfigurovali profil Databázová pošta. Agent SQL Server může používat jenom jeden profil Databázová pošta a musí se volat `AzureManagedInstance_dbmail_profile`. 
  - Pager není podporován.
  - NetSend se nepodporuje.
  - Výstrahy ještě nejsou podporované.
  - Proxy servery nejsou podporovány.
- Protokol událostí se nepodporuje.

Následující funkce agenta SQL momentálně nejsou podporované:

- Proxy servery
- Plánování úloh na nečinném procesoru
- Povolení nebo zakázání agenta
- Upozornění

Informace o agentovi SQL Server najdete v tématu [agent SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabulky

Následující typy tabulek nejsou podporovány:

- [SOUBOREM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server)
- [OBJEKTU FILETABLE](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)
- [externí tabulka](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) PolyBase
- [MEMORY_OPTIMIZED](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (Nepodporováno pouze v Pro obecné účely vrstvě)

Informace o tom, jak vytvářet a měnit tabulky, najdete v tématu [Create Table](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) a [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkce

### <a name="bulk-insert--openrowset"></a>Hromadné vložení/OPENROWSET

Spravovaná instance nemůže přistupovat ke sdíleným složkám souborů a složkám Windows, takže soubory musí být naimportované z úložiště objektů BLOB v Azure:

- `DATASOURCE`v `BULK INSERT` příkazu se vyžaduje při importu souborů z úložiště objektů BLOB v Azure. Viz [Bulk INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`se ve `OPENROWSET` funkci vyžaduje při čtení obsahu souboru z úložiště objektů BLOB v Azure. Viz [OpenRowset](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Spravovaná instance nemůže přistupovat ke sdíleným složkám souborů a složkám systému Windows, proto platí následující omezení:

- Podporuje `CREATE ASSEMBLY FROM BINARY` se jenom. Viz [vytvoření sestavení z binárního souboru](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE`není podporováno. Viz [vytvořit sestavení ze souboru](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY`nejde odkazovat na soubory. Viz [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Neuvedené příkazy DBCC, které jsou povolené v SQL Server nejsou ve spravovaných instancích podporované.

- Podporován je pouze omezený počet globálních `Trace flags` hodnot. Úroveň `Trace flags` relace není podporována. Viz [příznaky trasování](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF kterým](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) a [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) fungují s omezeným počtem globálních příznaků Trace-Flags.
- [Příkaz DBCC CHECKDB](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) s možnostmi REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST a REPAIR_REBUILD nelze použít, protože databázi nelze nastavit v `SINGLE_USER` režimu – viz téma [ALTER DATABASE rozdíl](#alter-database-statement). Potenciální poškození databáze zpracovává tým podpory Azure. Pokud všímáte poškození databáze, které by mělo být opraveno, obraťte se na podporu Azure.

### <a name="distributed-transactions"></a>Distribuované transakce

MSDTC a [elastické transakce](sql-database-elastic-transactions-overview.md) aktuálně nejsou ve spravovaných instancích podporovány.

### <a name="extended-events"></a>Rozšířené události

Některé cíle specifické pro systém Windows pro rozšířené události (XEvents) nejsou podporovány:

- `etw_classic_sync` Cíl není podporován. Ukládejte `.xel` soubory v úložišti objektů BLOB v Azure. Viz [cíl etw_classic_sync](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file` Cíl není podporován. Ukládejte `.xel` soubory v úložišti objektů BLOB v Azure. Viz [cíl event_file](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externí knihovny

V-Database R a Pythonu se zatím nepodporují externí knihovny. Viz [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream a FileTable

- Data FILESTREAM nejsou podporována.
- Databáze nemůže obsahovat skupiny souborů s `FILESTREAM` daty.
- `FILETABLE`není podporováno.
- Tabulky nemůžou `FILESTREAM` mít typy.
- Následující funkce nejsou podporovány:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Další informace najdete v tématu [tabulky](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server) [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) a soubory.

### <a name="full-text-semantic-search"></a>Fulltextové vyhledávání na základě textu

[Sémantické vyhledávání](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) není podporováno.

### <a name="linked-servers"></a>Propojené servery

Propojené servery ve spravovaných instancích podporují omezený počet cílů:

- Podporované cíle jsou spravované instance, samostatné databáze a instance SQL Server. 
- Propojené servery nepodporují distribuované transakce s možností zápisu (MS DTC).
- Nepodporované cíle jsou soubory, Analysis Services a další RDBMS. Zkuste použít nativní Import souborů CSV z Azure Blob Storage použití `BULK INSERT` nebo `OPENROWSET` jako alternativu pro import souborů.

Operace

- Transakce zápisu mezi instancemi nejsou podporované.
- `sp_dropserver`se podporuje pro vyřazení odkazovaného serveru. See [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET` Funkci lze použít ke spouštění dotazů pouze u instancí SQL Server. Můžou být spravované, místní nebo virtuální počítače. Viz [OpenRowset](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE` Funkci lze použít ke spouštění dotazů pouze u instancí SQL Server. Můžou být spravované, místní nebo virtuální počítače. Pouze hodnoty `SQLNCLI`, `SQLNCLI11` a`SQLOLEDB` jsou podporovány jako zprostředkovatel. Příklad: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Viz [OpenDataSource](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).
- Propojené servery nelze použít ke čtení souborů (Excel, CSV) ze sdílených síťových složek. Zkuste použít [Bulk INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) nebo [OpenRowset](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) , které čtou soubory CSV z Azure Blob Storage. Sledovat tyto žádosti u [položky zpětné vazby spravované instance](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Externí tabulky, které odkazují na soubory v HDFS nebo Azure Blob Storage, se nepodporují. Informace o bázi základů naleznete v části [základ](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikace

- Podporují se typy snímků a obousměrné replikace. Slučovací replikace, replikace peer-to-peer a aktualizovatelné odběry nejsou podporovány.
- [Transakční replikace](sql-database-managed-instance-transactional-replication.md) je k dispozici pro veřejnou verzi Preview spravované instance s některými omezeními:
    - Všechny typy účastníků replikace (vydavatel, distributor, předplatitelé pro vyžádání obsahu a nabízený předplatitelé) se dají umístit na spravované instance, ale vydavatele a distributora nejde umístit na jiné instance.
    - Spravované instance můžou komunikovat s nejnovějšími verzemi SQL Server. [Tady](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems)najdete podporované verze.
    - Transakční replikace obsahuje některé [Další požadavky na síť](sql-database-managed-instance-transactional-replication.md#requirements).

Informace o konfiguraci replikace najdete v [kurzu replikace](replication-with-sql-database-managed-instance.md).


Pokud je replikace povolená v databázi ve [skupině převzetí služeb při selhání](sql-database-auto-failover-group.md), musí správce spravované instance vyčistit všechny publikace na staré primární primární databázi a po převzetí služeb při selhání je znovu nakonfigurovat na nové primární databázi. V tomto scénáři jsou potřeba následující aktivity:

1. Zastavte všechny úlohy replikace běžící v databázi, pokud existují.
2. Z vydavatele vyřaďte metadata odběru spuštěním následujícího skriptu v databázi vydavatele:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Odkládací metadata odběru od odběratele. Spusťte následující skript v databázi předplatného na instanci předplatitele:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Vynuceně vyřaďte všechny replikační objekty od vydavatele spuštěním následujícího skriptu v publikované databázi:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Nuceně odstranit starého distributora z původní primární instance (Pokud dojde k převzetí služeb při selhání do staré primární služby, která se použila k distributorovi). Spusťte následující skript v hlavní databázi v původní spravované instanci distributora:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

### <a name="restore-statement"></a>Příkaz Restore 

- Podporovaná syntaxe:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Nepodporovaná syntaxe:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Zdroj: 
  - `FROM URL`(Azure Blob Storage) je jedinou podporovanou možností.
  - `FROM DISK`/`TAPE`zařízení/Backup se nepodporuje.
  - Zálohovací sklady nejsou podporované.
- `WITH`možnosti nejsou podporovány, například ne `DIFFERENTIAL` nebo. `STATS`
- `ASYNC RESTORE`: Obnovení pokračuje i v případě, že dojde k přerušení připojení klienta. Pokud je připojení vyřazeno, můžete `sys.dm_operation_status` se podívat na stav operace obnovení a pro databázi pro vytvoření a odstranění. Viz [Sys. DM _operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Následující možnosti databáze jsou nastaveny nebo přepsány a nelze je změnit později: 

- `NEW_BROKER`Pokud zprostředkovatel není v souboru. bak povolený. 
- `ENABLE_BROKER`Pokud zprostředkovatel není v souboru. bak povolený. 
- `AUTO_CLOSE=OFF`Pokud databáze v souboru. bak obsahuje `AUTO_CLOSE=ON`. 
- `RECOVERY FULL`v případě, že databáze v souboru. bak `SIMPLE` má `BULK_LOGGED` nebo je v režimu obnovení.
- Skupina souborů optimalizované pro paměť se přidá a zavolá XTP, pokud nebyla v souboru source. bak. 
- Stávající paměťově optimalizovaná skupina souborů se přejmenuje na XTP. 
- `SINGLE_USER`a `RESTRICTED_USER` možnosti jsou převedeny na `MULTI_USER`.

Určitá 

- Zálohování poškozených databází může být obnoveno v závislosti na typu poškození, ale automatizované zálohování nebude provedeno, dokud nebude poškození opraveno. Zajistěte, aby `DBCC CHECKDB` byl spuštěn na instanci zdroje a aby `WITH CHECKSUM` se zabránilo tomuto problému, použijte zálohování.
- Obnovení souboru databáze, která obsahuje jakákoli omezení popsaná v tomto dokumentu ( `FILESTREAM` například nebo `FILETABLE` objekty), nelze obnovit ve spravované instanci. `.BAK`
- `.BAK`soubory, které obsahují víc zálohovacích skladů, se nedají obnovit. 
- `.BAK`soubory, které obsahují více souborů protokolu, nelze obnovit.
- Zálohy obsahující databáze větší než 8TB, aktivní objekty OLTP v paměti nebo více než 280 souborů nemohou být obnoveny v instanci Pro obecné účely. 
- Zálohy, které obsahují databáze větší než 4 TB nebo objekty OLTP v paměti s celkovou velikostí větší, než je velikost popsaná v části [omezení prostředků](sql-database-managed-instance-resource-limits.md) , nelze obnovit v instanci pro důležité obchodní informace.
Informace o příkazech Restore naleznete [](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)v tématu Restore restatements.

### <a name="service-broker"></a>Služba Service Broker

Služba Service Broker mezi instancemi není podporována:

- `sys.routes`: Jako požadavek musíte vybrat adresu z sys. Routes. Adresa musí být místní v každé trase. Viz [Sys. Routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Nemůžete `CREATE ROUTE` použít `ADDRESS` s jinými `LOCAL`než. Viz [vytvoření trasy](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Nemůžete `ALTER ROUTE` použít `ADDRESS` s jinými `LOCAL`než. Viz [ALTER Route](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Uložené procedury, funkce a triggery

- `NATIVE_COMPILATION`není podporován na Pro obecné účely vrstvě.
- Následující možnosti [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) nejsou podporované: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`není podporováno. Viz [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell`není podporováno. Viz [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`nepodporuje se, což zahrnuje `sp_addextendedproc`  a `sp_dropextendedproc`. Viz [rozšířené uložené procedury](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` a`sp_detach_db` nejsou podporovány. Viz [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)a [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Systémové funkce a proměnné

Následující proměnné, funkce a zobrazení vrací různé výsledky:

- `SERVERPROPERTY('EngineEdition')`Vrátí hodnotu 8. Tato vlastnost jednoznačně identifikuje spravovanou instanci. Viz [ServerProperty](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`Vrátí hodnotu NULL, protože koncept instance, která existuje pro SQL Server, se nevztahuje na spravovanou instanci. Viz [SERVERPROPERTY (' InstanceName ')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`Vrátí úplný název DNS "připojitelné", například my-managed-instance.wcus17662feb9ce98.database.windows.net. Viz [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS`Vrátí úplný název DNS "připojitelné", například `myinstance.domain.database.windows.net` pro vlastnosti "název" a "data_source". Viz [Sys. SERVERY](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME`Vrátí hodnotu NULL, protože koncept služby, která existuje pro SQL Server, se nevztahuje na spravovanou instanci. Viz [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID`je podporováno. Vrátí hodnotu NULL, pokud se přihlášení Azure AD nenachází v tabulce sys. syslogins. Viz [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID`není podporováno. Vrátí se nesprávná data, což je dočasný známý problém. Viz [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Environment"></a>Omezení prostředí

### <a name="subnet"></a>Subnet
-  V podsíti, do které jste nasadili spravovanou instanci, nemůžete umístit žádné další prostředky (například virtuální počítače). Nasaďte tyto prostředky pomocí jiné podsítě.
- Podsíť musí mít dostatečný počet dostupných [IP adres](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Minimum je 16, ale doporučujeme mít minimálně 32 IP adres v podsíti.
- [Koncové body služby nelze přidružit k podsíti spravované instance](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Ujistěte se, že je při vytváření virtuální sítě možnost koncové body služby zakázaná.
- Počet virtuální jádra a typů instancí, které můžete nasadit v oblasti, mají některá [omezení a omezení](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
- Existují některá [pravidla zabezpečení, která je nutné použít v podsíti](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>VNET
- Virtuální síť se dá nasadit pomocí modelu prostředků – model klasický pro virtuální síť se nepodporuje.
- Po vytvoření spravované instance se nepodporují přesunutí spravované instance nebo virtuální sítě do jiné skupiny prostředků nebo předplatného.
- Některé služby, jako jsou App Service prostředí, Logic Apps a spravované instance (používané pro geografickou replikaci, transakční replikaci nebo prostřednictvím odkazovaných serverů), nemají přístup ke spravovaným instancím v různých oblastech, pokud jsou jejich virtuální sítě připojené pomocí [globální. partnerský vztah](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). K těmto prostředkům se můžete připojit prostřednictvím ExpressRoute nebo VNet-to-VNet prostřednictvím bran virtuální sítě.

### <a name="tempdb"></a>DATABÁZE

Maximální velikost `tempdb` souboru nemůže být větší než 24 GB na jádro na úrovni pro obecné účely. Maximální `tempdb` velikost vrstvy pro důležité obchodní informace je omezená velikostí úložiště instance. `Tempdb`velikost souboru protokolu je omezena na 120 GB na úrovni Pro obecné účely i Pro důležité obchodní informace. Některé dotazy mohou vracet chybu, pokud vyžadují více než 24 GB na jádro v `tempdb` nebo pokud vydávají více než 120 GB dat protokolu.

### <a name="error-logs"></a>Protokoly chyb

Spravovaná instance umísťuje podrobné informace v protokolech chyb. K dispozici je mnoho interních systémových událostí, které jsou zaznamenány v protokolu chyb. Pomocí vlastního postupu si můžete přečíst protokoly chyb, které odfiltrují některé nedůležité položky. Další informace najdete v tématu [spravovaná instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

## <a name="Issues"></a>Známé problémy

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Dialogová okna mezidatabázového Service Broker se musí po upgradu na úrovni služby znovu inicializovat.

**Datum** Srpna 2019

Dialogy Service Broker mezi databázemi ukončí doručování zpráv do služeb v jiných databázích po provedení operace změny úrovně služby. Zprávy nejsou **ztraceny** a je možné je najít ve frontě odesílatelů. Jakákoli změna velikosti úložiště virtuální jádra nebo instance ve spravované instanci způsobí, že `service_broke_guid` se hodnota v zobrazení [Sys. databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) změní pro všechny databáze. Jakékoli `DIALOG` vytvoření pomocí příkazu [Begin dialog](https://docs.microsoft.com/en-us/sql/t-sql/statements/begin-dialog-conversation-transact-sql) , který odkazuje na zprostředkovatele služby v jiné databázi, přestane předávat zprávy do cílové služby.

**Odstraníte** Před aktualizací úrovně služby zastavte všechny aktivity, které používají konverzaci mezi Service Brokermi databázemi, a potom je znovu inicializujte. Pokud jsou zbývající zprávy nedoručené po změně úrovně služeb, přečtěte si zprávy ze zdrojové fronty a znovu je odešlete do cílové fronty.

### <a name="impersonification-of-aad-login-types-is-not-supported"></a>Impersonification typů přihlášení AAD se nepodporuje.

**Datum** Červenec 2019

Zosobnění pomocí `EXECUTE AS USER` nebo `EXECUTE AS LOGIN` z následujících objektů zabezpečení AAD není podporované:
-   Uživatelé AAD s aliasem V tomto případě `15517`se vrátí následující chyba.
- Přihlášení AAD a uživatelé na základě aplikací AAD nebo instančních objektů. V tomto případě `15517` se vrátí následující chyby a `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryparametr není v sp_send_db_mail podporován.

**Datum** Duben 2019

Parametr v proceduře sp_send_db_mail nefunguje. [](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) `@query`

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Po geografickém převzetí služeb při selhání je potřeba znovu nakonfigurovat transakční replikaci.

**Datum** Březen 2019

Pokud je transakční replikace povolená v databázi ve skupině automatického převzetí služeb při selhání, musí správce spravované instance vyčistit všechny publikace na staré primární primární databázi a po převzetí služeb při selhání do jiné oblasti je znovu nakonfigurovat na nové primární úrovni. Další podrobnosti najdete v tématu [replikace](#replication) .

### <a name="aad-logins-and-users-are-not-supported-in-tools"></a>Přihlášení AAD a uživatelé nejsou v nástrojích podporované.

**Datum** Leden 2019

SQL Server Management Studio a SQL Server Data Tools Fuly podporují přihlášení a uživatele v Azure ACCT-Directory.
- Používání objektů zabezpečení serveru Azure AD (přihlášení) a uživatelů (Public Preview) s nástroji SQL Server Data Tools aktuálně není podporováno.
- V SQL Server Management Studio se nepodporuje skriptování pro objekty zabezpečení serveru Azure AD (přihlášení) a uživatele (ve verzi Public Preview).

### <a name="tempdb-structure-and-content-is-re-created"></a>Struktura a obsah TEMPDB se znovu vytvoří.

`tempdb` Databáze je vždy rozdělena do 12 datových souborů a struktura souborů nemůže být změněna. Maximální velikost na soubor nelze změnit a nelze přidat nové soubory do `tempdb`. `Tempdb`je vždy znovu vytvořen jako prázdná databáze při spuštění nebo převzetí služeb při selhání a jakékoli změny provedené v `tempdb` nezůstanou zachovány.

### <a name="exceeding-storage-space-with-small-database-files"></a>Překročení úložného prostoru s malými databázovými soubory

`CREATE DATABASE`příkazy `ALTER DATABASE ADD FILE`, a `RESTORE DATABASE` mohou selhat, protože instance může dosáhnout limitu Azure Storage.

Každá Pro obecné účely spravovaná instance má až 35 TB úložiště rezervovaného pro místo na disku Azure Premium. Každý databázový soubor je umístěn na samostatném fyzickém disku. Velikosti disků můžou být 128 GB, 256 GB, 512 GB, 1 TB nebo 4 TB. Nevyužité místo na disku se neúčtuje, ale celkový součet velikostí disků Azure Premium nesmí překročit 35 TB. V některých případech může spravovaná instance, která nepotřebuje 8 TB celkem, překročit 35 TB Azure na velikost úložiště kvůli vnitřní fragmentaci.

Například Pro obecné účely spravovaná instance může mít jeden velký soubor o velikosti 1,2 TB umístěný na 4 TB disku. Může taky mít 248 souborů o velikosti 1 GB, které jsou umístěné na samostatných discích 128-GB. V tomto příkladu:

- Celková přidělená velikost diskového úložiště je 1 × 4 TB + 248 × 128 GB = 35 TB.
- Celkové rezervované místo pro databáze v instanci je 1 × 1,2 TB + 248 × 1 GB = 1,4 TB.

Tento příklad ilustruje, že za určitých okolností, v důsledku konkrétní distribuce souborů, může spravovaná instance dosáhnout limitu 35-TB, který je vyhrazený pro připojenou jednotku Azure Premium, pokud ji neočekáváte.

V tomto příkladu existující databáze fungují i nadále a můžou růst bez jakýchkoli potíží, pokud se nepřidá nové soubory. Nové databáze nelze vytvořit ani obnovit, protože není dostatek místa pro nové diskové jednotky, ani v případě, že celková velikost všech databází nedosáhne limitu velikosti instance. Chyba, která se vrátí v tomto případě, není jasná.

[Počet zbývajících souborů můžete identifikovat](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) pomocí systémových zobrazení. Pokud dosáhnete tohoto limitu, zkuste [vyprázdnit a odstranit některé menší soubory pomocí příkazu DBCC SHRINKFILE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) nebo přepněte na [pro důležité obchodní informaceovou vrstvu, která nemá toto omezení](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Hodnoty GUID, které se zobrazují místo názvů databází

Několik systémových zobrazení, čítače výkonu, chybové zprávy, XEvents a položky protokolu chyb zobrazují identifikátory databáze identifikátorů GUID místo skutečných názvů databází. Nespoléhejte na tyto identifikátory GUID, protože se v budoucnu nahrazují skutečnými názvy databází.

### <a name="error-logs-arent-persisted"></a>Protokoly chyb nejsou trvalé

Protokoly chyb, které jsou k dispozici ve spravované instanci, nejsou trvale uložené a jejich velikost není zahrnuta v maximálním limitu úložiště. Pokud dojde k převzetí služeb při selhání, můžou se protokoly chyb automaticky vymazat. Historie protokolu chyb může obsahovat mezery, protože spravovaná instance byla na několika virtuálních počítačích přesunuta několikrát.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Obor transakce ve dvou databázích v rámci stejné instance není podporovaný.

`TransactionScope` Třída v rozhraní .NET nefunguje, pokud jsou dva dotazy odesílány do dvou databází v rámci stejné instance v rámci stejného oboru transakce:

```csharp
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

I když tento kód funguje s daty v rámci stejné instance, vyžaduje MSDTC.

**Odstraníte** Použijte [SqlConnection. ChangeDatabase (String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) pro použití jiné databáze v kontextu připojení namísto použití dvou připojení.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Moduly CLR a propojené servery někdy nemůžou odkazovat na místní IP adresu.

Moduly CLR umístění do spravované instance a propojené servery nebo distribuované dotazy, které odkazují na aktuální instanci, někdy nemůžou přeložit IP adresu místní instance. Tato chyba je přechodný problém.

**Odstraníte** Pokud je to možné, použijte připojení kontextu v modulu CLR.

## <a name="next-steps"></a>Další postup

- Další informace o spravovaných instancích najdete v tématu [co je spravovaná instance?](sql-database-managed-instance.md) .
- Seznam funkcí a porovnání najdete v tématu [Azure SQL Database porovnání funkcí](sql-database-features.md).
- Rychlý Start, který ukazuje, jak vytvořit novou spravovanou instanci, najdete v tématu [Vytvoření spravované instance](sql-database-managed-instance-get-started.md).
