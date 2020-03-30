---
title: Rozdíly t-SQL spravované instance
description: Tento článek popisuje rozdíly T-SQL mezi spravovanou instancí v Azure SQL Database a SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 03/11/2020
ms.custom: seoapril2019
ms.openlocfilehash: e01c61ca4f415ffbb46c86034d4b7441bc2617d9
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365481"
---
# <a name="managed-instance-t-sql-differences-and-limitations"></a>Spravovaná instance T-SQL rozdíly a omezení

Tento článek shrnuje a vysvětluje rozdíly v syntaxi a chování mezi instancí spravované službou Azure SQL Database a místním databázovým strojem SQL Server. Možnost nasazení spravované instance poskytuje vysokou kompatibilitu s místním databázovým strojem SQL Server. Většina funkcí databázového stroje SQL Server je podporována ve spravované instanci.

![Migrace](./media/sql-database-managed-instance/migration.png)

Existují některá omezení PaaS, které jsou zavedeny ve spravované instanci a některé změny chování ve srovnání s SQL Server. Rozdíly jsou rozděleny do následujících kategorií:<a name="Differences"></a>

- [Dostupnost](#availability) zahrnuje rozdíly ve [skupinách dostupnosti vždy zapnuté](#always-on-availability-groups) a [zálohy](#backup).
- [Zabezpečení](#security) zahrnuje rozdíly v [auditování](#auditing), [certifikátech](#certificates), [pověřeních](#credential), [zprostředkovatelích kryptografických služeb](#cryptographic-providers), [přihlášení a uživatelích](#logins-and-users)a [klíči služby a hlavním klíči služby](#service-key-and-service-master-key).
- [Konfigurace](#configuration) zahrnuje rozdíly v [rozšíření fondu vyrovnávací paměti](#buffer-pool-extension), [řazení](#collation), [úrovně kompatibility](#compatibility-levels), [zrcadlení databáze](#database-mirroring), [možnosti databáze](#database-options), SQL Server [Agent](#sql-server-agent)a [tabulky možnosti](#tables).
- [Mezi funkce](#functionalities) patří [hromadné vložení/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [distribuované transakce](#distributed-transactions), rozšířené [události](#extended-events), [externí knihovny](#external-libraries), [filestream a FileTable](#filestream-and-filetable), [fulltextové sémantické vyhledávání](#full-text-semantic-search), propojené [servery](#linked-servers), [PolyBase](#polybase), [Replikace](#replication), [OBNOVENÍ](#restore-statement), [Service Broker](#service-broker), [uložené procedury, funkce a aktivační události](#stored-procedures-functions-and-triggers).
- [Nastavení prostředí,](#Environment) jako jsou virtuální sítě a konfigurace podsítí.

Většina těchto funkcí jsou architektonická omezení a představují funkce služby.

Dočasné známé problémy, které jsou zjištěny ve spravované instanci a které budou vyřešeny v budoucnu, jsou popsány na [stránce poznámek k verzi](sql-database-release-notes.md).

## <a name="availability"></a>Dostupnost

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Vždy na skupinách dostupnosti

[Vysoká dostupnost](sql-database-high-availability.md) je integrována do spravované instance a nemůže být řízena uživateli. Následující příkazy nejsou podporovány:

- [VYTVOŘIT KONCOVÝ BOD ... Pro DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [VYTVOŘIT SKUPINU DOSTUPNOSTI](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ZMĚNIT SKUPINU DOSTUPNOSTI](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [SKUPINA DOSTUPNOSTI PŘETAŽENÍ](/sql/t-sql/statements/drop-availability-group-transact-sql)
- Klauzule [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) příkazu [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Zálohování

Spravované instance mají automatické zálohování, takže uživatelé mohou vytvářet úplné zálohy databáze. `COPY_ONLY` Diferenciální, log a snímek souborů zálohy nejsou podporovány.

- S spravovanou instancí můžete zálohovat databázi instancí jenom na účet úložiště objektů blob Azure:
  - Je `BACKUP TO URL` podporována pouze.
  - `FILE`, `TAPE`a zálohovací zařízení nejsou podporována.
- Většina obecných `WITH` možností je podporována.
  - `COPY_ONLY`je povinné.
  - `FILE_SNAPSHOT`není podporována.
  - Možnosti `REWIND`pásky: `UNLOAD`, `NOUNLOAD` `NOREWIND`, , a nejsou podporovány.
  - Možnosti specifické `NORECOVERY`pro `STANDBY`protokol: , , a `NO_TRUNCATE` nejsou podporovány.

Omezení: 

- Se spravovanou instancí můžete zálohovat databázi instancí do zálohy s až 32 pruhy, což je dost pro databáze s kapacitou až 4 TB, pokud se používá komprese záloh.
- Nelze spustit `BACKUP DATABASE ... WITH COPY_ONLY` v databázi, která je zašifrována pomocí služby spravované transparentní šifrování dat (TDE). TDE spravované službou vynutí šifrování záloh pomocí interního klíče TDE. Klíč nelze exportovat, takže zálohu nelze obnovit. Použijte automatické zálohování a obnovení v čase nebo místo toho použijte [tde spravované zákazníkem (BYOK).](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) Můžete také zakázat šifrování v databázi.
- Maximální velikost záložního proužek `BACKUP` pomocí příkazu ve spravované instanci je 195 GB, což je maximální velikost objektu blob. Zvyšte počet pruhů v příkazu zálohování, abyste zmenšili velikost jednotlivých pruhů a zůstali v rámci tohoto limitu.

    > [!TIP]
    > Chcete-li toto omezení obejít, můžete při zálohování databáze z serveru SQL Server v místním prostředí nebo ve virtuálním počítači:
    >
    > - Zálohovat `DISK` na místo zálohování `URL`do .
    > - Nahrajte záložní soubory do úložiště objektů Blob.
    > - Obnovení do spravované instance.
    >
    > `Restore` Příkaz ve spravované instanci podporuje větší velikosti objektů blob v záložních souborech, protože pro ukládání nahraných záložních souborů se používá jiný typ objektu blob.

Informace o zálohách pomocí t-SQL naleznete v tématu [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Zabezpečení

### <a name="auditing"></a>Auditování

Hlavní rozdíly mezi auditováním v databázích v Azure SQL Database a databázemi na SQL Serveru jsou:

- S možností nasazení spravované instance v Azure SQL Database funguje auditování na úrovni serveru. Soubory `.xel` protokolu jsou uloženy v úložišti objektů Blob Azure.
- S možností nasazení jednotné databáze a elastického fondu v Azure SQL Database funguje auditování na úrovni databáze.
- V místním nebo virtuálním počítači sql serveru funguje auditování na úrovni serveru. Události jsou uloženy v systému souborů nebo protokolech událostí systému Windows.
 
XEvent auditování ve spravované instanci podporuje cíle úložiště objektů Blob Azure. Protokoly souborů a systému Windows nejsou podporovány.

Hlavní rozdíly v `CREATE AUDIT` syntaxi pro auditování úložiště objektů blob Azure jsou:

- Nová syntaxe `TO URL` je za předpokladu, že můžete použít k určení `.xel` adresy URL kontejneru úložiště Azure Blob, kde jsou umístěny soubory.
- Syntaxe `TO FILE` není podporována, protože spravovaná instance nemá přístup ke sdíleným položkům systému Windows.

Další informace naleznete v tématu: 

- [VYTVOŘIT AUDIT SERVERU](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ZMĚNIT AUDIT SERVERU](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditování](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certifikáty

Spravovaná instance nemá přístup ke sdíleným složkám souborů a složkám systému Windows, takže platí následující omezení:

- `CREATE FROM` / Soubor `BACKUP TO` není podporován pro certifikáty.
- `CREATE` / Certifikát `BACKUP` `FILE` od / není podporován. `ASSEMBLY` Soubory soukromého klíče nelze použít. 

Viz [VYTVOŘENÍ CERTIFIKÁTU](/sql/t-sql/statements/create-certificate-transact-sql) A [ZÁLOHOVACÍHO CERTIFIKÁTU](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Řešení**: Namísto vytvoření zálohy certifikátu a obnovení zálohy [získáte binární obsah certifikátu a soukromý klíč, uložte jej jako soubor .sql a vytvořte z binárního souboru](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Přihlašovací údaj

Podporovány jsou `SHARED ACCESS SIGNATURE` pouze Azure Key Vault a identity. Uživatelé systému Windows nejsou podporováni.

Viz [VYTVOŘENÍ POVĚŘENÍ](/sql/t-sql/statements/create-credential-transact-sql) a ALTER [POVĚŘENÍ](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Zprostředkovatelé kryptografických služeb

Spravovaná instance nemá přístup k souborům, takže nelze vytvořit zprostředkovatele kryptografických služeb:

- `CREATE CRYPTOGRAPHIC PROVIDER`není podporována. Viz [VYTVOŘENÍ KRYPTOGRAFICKÉHO ZPROSTŘEDKOVATELE](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER`není podporována. Viz [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Přihlášení a uživatelé

- Přihlášení SQL vytvořená `FROM CERTIFICATE` `FROM ASYMMETRIC KEY`pomocí `FROM SID` aplikace , a jsou podporována. Viz [VYTVOŘENÍ PŘIHLÁŠENÍ](/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD) server ové objekty (přihlášení) vytvořené pomocí syntaxe [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) nebo [vytvořit uživatele z přihlášení [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) syntaxe jsou podporovány. Tato přihlášení jsou vytvořena na úrovni serveru.

    Spravovaná instance podporuje objekty `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`databáze Azure AD se syntaxí . Tato funkce se také označuje jako Azure AD obsažené uživatele databáze.

- Přihlášení systému Windows `CREATE LOGIN ... FROM WINDOWS` vytvořená pomocí syntaxe nejsou podporována. Používejte přihlášení a uživatele služby Azure Active Directory.
- Uživatel Azure AD, který instanci vytvořil, má [neomezená oprávnění správce](sql-database-manage-logins.md).
- Uživatelé na úrovni databáze Azure AD, kteří `CREATE USER ... FROM EXTERNAL PROVIDER` nejsou správcem, lze vytvořit pomocí syntaxe. Viz [VYTVOŘIT UŽIVATELE ... OD EXTERNÍHO POSKYTOVATELE](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
- Objekty serveru Azure AD (přihlášení) podporují funkce SQL v rámci jenom spravované instance. Funkce, které vyžadují interakci mezi instancemi, bez ohledu na to, zda jsou v rámci stejného klienta Azure AD nebo různých klientů, nejsou podporovány pro uživatele Azure AD. Příklady těchto funkcí jsou:

  - Transakční replikace SQL.
  - Linkový server.

- Nastavení přihlášení Azure AD mapované na skupinu Azure AD jako vlastník databáze není podporována.
- Zosobnění objektů na úrovni serveru Azure AD pomocí jiných objektů zabezpečení Azure AD je podporováno, jako je například klauzule [EXECUTE AS.](/sql/t-sql/statements/execute-as-transact-sql) Omezení provedení AS jsou:

  - EXECUTE AS USER není podporována pro uživatele Azure AD, pokud se název liší od přihlašovacího jména. Příkladem je, když je uživatel vytvořen pomocí syntaxe CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] a zosobnění je pokus prostřednictvím EXEC AS USER = _myAadUser_. Při vytváření **UŽIVATELE** z hlavního povinného serveru Azure AD (přihlášení), zadejte user_name jako stejné login_name z **LOGIN**.
  - Pouze objekty na úrovni serveru SQL Server (přihlášení), které jsou součástí `sysadmin` role můžete provést následující operace, které se zaměřují na objekty Azure AD:

    - SPUSTIT JAKO UŽIVATEL
    - PROVÉST JAKO PŘIHLÁŠENÍ

- Export a import databáze pomocí bacpac soubory jsou podporovány pro uživatele Azure AD ve spravované instanci pomocí [SSMS V18.4 nebo novější](/sql/ssms/download-sql-server-management-studio-ssms)nebo [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - Pomocí databázového souboru bacpac jsou podporovány následující konfigurace: 
    - Exportnebo import databáze mezi různými instancemi správy v rámci stejné domény Azure AD.
    - Exportujte databázi ze spravované instance a importujte do databáze SQL ve stejné doméně Azure AD. 
    - Exportujte databázi z databáze SQL a importujte do spravované instance ve stejné doméně Azure AD.
    - Exportujte databázi ze spravované instance a importujte na SQL Server (verze 2012 nebo novější).
      - V této konfiguraci jsou všichni uživatelé Azure AD vytvořeni jako objekty databáze SQL (uživatelé) bez přihlášení. Typ uživatelů jsou uvedeny jako SQL (viditelné jako SQL_USER v sys.database_principals). Jejich oprávnění a role zůstávají v metadatech databáze serveru SQL Server a lze je použít pro zosobnění. Však nemohou být použity pro přístup a přihlášení k serveru SQL Server pomocí jejich pověření.

- Pouze hlavní přihlášení na úrovni serveru, které je vytvořeno procesem zřizování `securityadmin` spravované `sysadmin`instance, členové rolí serveru, jako jsou nebo , nebo jiná přihlášení s oprávněním ALTER ANY LOGIN na úrovni serveru můžete vytvořit objekty serveru Azure AD (přihlášení) v hlavní databázi pro spravovanou instanci.
- Pokud je přihlášení sql objekt, pouze přihlášení, `sysadmin` které jsou součástí role můžete použít příkaz vytvořit k vytvoření přihlášení pro účet Azure AD.
- Přihlášení Služby Azure AD musí být členem služby Azure AD ve stejném adresáři, který se používá pro spravanou instanci Azure SQL Database.
- Objekty serveru Azure AD (přihlášení) jsou viditelné v Průzkumníkovi objektů počínaje SQL Server Management Studio 18.0 náhled 5.
- Překrývající se objekty serveru Azure AD (přihlášení) s účtem správce Azure AD je povoleno. Objekty serveru Azure AD (přihlášení) mají přednost před správcem Azure AD při překladu hlavního povinného a použít oprávnění pro spravovanou instanci.
- Během ověřování se k vyřešení ověřovacího objektu použije následující posloupnost:

    1. Pokud účet Azure AD existuje jako přímo namapované na hlavní server Azure AD (login), který je k dispozici v sys.server_principals jako typ "E", udělit přístup a použít oprávnění hlavního serveru Azure AD (přihlášení).
    2. Pokud je účet Azure AD členem skupiny Azure AD, která je namapována na hlavní server Azure AD (login), který je k dispozici v sys.server_principals jako typ "X", udělit přístup a použít oprávnění přihlášení skupiny Azure AD.
    3. Pokud je účet Azure AD speciální správce Azure AD nakonfigurovaný portálem pro spravovanou instanci, která neexistuje ve spravovaných zobrazeních systému instancí, použijte speciální pevná oprávnění správce Azure AD pro spravovanou instanci (starší režim).
    4. Pokud účet Azure AD existuje jako přímo namapované na uživatele Azure AD v databázi, která je k dispozici v sys.database_principals jako typ "E", udělit přístup a použít oprávnění uživatele databáze Azure AD.
    5. Pokud je účet Azure AD členem skupiny Azure AD, která je namapována na uživatele Azure AD v databázi, která je k dispozici v sys.database_principals jako typ "X", udělit přístup a použít oprávnění přihlášení skupiny Azure AD.
    6. Pokud je přihlášení Azure AD mapována buď uživatelský účet Azure AD nebo účet skupiny Azure AD, který řeší na uživatele, který je ověřování, všechna oprávnění z tohoto přihlášení Azure AD se použijí.

### <a name="service-key-and-service-master-key"></a>Klíč služby a hlavní klíč služby

- [Zálohování hlavního klíče](/sql/t-sql/statements/backup-master-key-transact-sql) není podporováno (spravované službou SQL Database).
- [Obnovení hlavního klíče](/sql/t-sql/statements/restore-master-key-transact-sql) není podporováno (spravované službou SQL Database).
- [Zálohování hlavního klíče služby](/sql/t-sql/statements/backup-service-master-key-transact-sql) není podporováno (spravované službou SQL Database).
- [Obnovení hlavního klíče služby](/sql/t-sql/statements/restore-service-master-key-transact-sql) není podporováno (spravované službou SQL Database).

## <a name="configuration"></a>Konfigurace

### <a name="buffer-pool-extension"></a>Rozšíření fondu vyrovnávací paměti

- [Rozšíření fondu vyrovnávací paměti](/sql/database-engine/configure-windows/buffer-pool-extension) není podporováno.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`není podporována. Viz [ALTER konfigurace serveru](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Kolace

Výchozí řazení instancí je `SQL_Latin1_General_CP1_CI_AS` a může být zadáno jako parametr vytvoření. Viz [Kolace](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Úrovně kompatibility

- Podporované úrovně kompatibility jsou 100, 110, 120, 130, 140 a 150.
- Úrovně kompatibility pod 100 nejsou podporovány.
- Výchozí úroveň kompatibility pro nové databáze je 140. U obnovených databází zůstane úroveň kompatibility nezměněna, pokud byla 100 a vyšší.

Viz [Úroveň kompatibility databáze ALTER](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Zrcadlení databáze

Zrcadlení databáze není podporováno.

- `ALTER DATABASE SET PARTNER`a `SET WITNESS` možnosti nejsou podporovány.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`není podporována.

Další informace naleznete [v tématu ALTER DATABASE SET PARTNER a SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) a CREATE [ENDPOINT ... PRO DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Možnosti databáze

- Více souborů protokolu není podporováno.
- Objekty v paměti nejsou podporovány ve vrstvě služby pro obecné účely. 
- Existuje limit 280 souborů na instanci pro obecné účely, což znamená maximálně 280 souborů na databázi. Do tohoto limitu se započítávají soubory dat i soubory protokolu ve vrstvě obecnéúčely. [Úroveň Business Critical podporuje 32 767 souborů na databázi](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Databáze nemůže obsahovat filegroups, které obsahují data filestream. Obnovení se nezdaří, pokud .bak obsahuje `FILESTREAM` data. 
- Každý soubor se umístí do úložiště objektů blob Azure. Vo a propustnost na soubor závisí na velikosti každého jednotlivého souboru.

#### <a name="create-database-statement"></a>Příkaz VYTVOŘIT DATABÁZI

Následující omezení se `CREATE DATABASE`vztahují na :

- Soubory a skupiny souborů nelze definovat. 
- Tato `CONTAINMENT` možnost není podporována. 
- `WITH`možnosti nejsou podporovány. 
   > [!TIP]
   > Jako řešení použijte `ALTER DATABASE` po `CREATE DATABASE` nastavení možností databáze pro přidání souborů nebo nastavení omezení. 

- Tato `FOR ATTACH` možnost není podporována.
- Tato `AS SNAPSHOT OF` možnost není podporována.

Další informace naleznete v tématu [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Příkaz ALTER DATABASE

Některé vlastnosti souboru nelze nastavit ani změnit:

- Cestu k souboru nelze zadat `ALTER DATABASE ADD FILE (FILENAME='path')` v příkazu T-SQL. Odeberte `FILENAME` ze skriptu, protože spravovaná instance automaticky umístí soubory. 
- Název souboru nelze změnit pomocí `ALTER DATABASE` příkazu.

Následující možnosti jsou ve výchozím nastavení nastaveny a nelze je změnit:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Následující možnosti nelze změnit:

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

Další informace naleznete v tématu [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>Agent SQL Server

- Povolení a zakázání agenta serveru SQL Server není aktuálně podporováno ve spravované instanci. Agent SQL je vždy spuštěn.
- Nastavení agenta serveru SQL Server jsou pouze pro čtení. Procedura `sp_set_agent_properties` není ve spravované instanci podporována. 
- Úlohy
  - Jsou podporovány kroky úlohy T-SQL.
  - Podporovány jsou následující úlohy replikace:
    - Čtečka protokolů transakcí
    - Snímek
    - Distributor
  - Jsou podporovány kroky úlohy SSIS.
  - Jiné typy kroků úlohy nejsou aktuálně podporovány:
    - Krok úlohy slučovací replikace není podporován. 
    - Program pro čtení front není podporován. 
    - Příkazové prostředí ještě není podporováno.
  - Spravované instance nemají přístup k externím prostředkům, například síťové sdílené složky prostřednictvím robocopy. 
  - Služba SQL Server Analysis Services není podporována.
- Oznámení jsou částečně podporována.
- E-mailové oznámení je podporováno, i když vyžaduje konfiguraci profilu pošty databáze. Agent serveru SQL Server může používat pouze jeden `AzureManagedInstance_dbmail_profile`profil pošty databáze a musí být volána . 
  - Pager není podporován.
  - Program NetSend není podporován.
  - Výstrahy ještě nejsou podporovány.
  - Proxy servery nejsou podporovány.
- EventLog není podporován.

Následující funkce sql agenta nejsou aktuálně podporovány:

- Proxy
- Plánování úloh na nečinným procesoru
- Povolení nebo zakázání agenta
- Výstrahy

Informace o agentovi serveru SQL Server naleznete v tématu [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabulky

Následující typy tabulek nejsou podporovány:

- [Filestream](/sql/relational-databases/blob/filestream-sql-server)
- [SOUBOROVÁ TABULKA](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNÍ TABULKA](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (není podporováno pouze ve vrstvě pro obecné účely)

Informace o vytváření a změně tabulek naleznete v tématu [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) and [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkce

### <a name="bulk-insert--openrowset"></a>Hromadná vložka / OPENROWSET

Spravovaná instance nemá přístup ke sdíleným složkám souborů a složkám Windows, takže soubory musí být importovány z úložiště objektů Blob Azure:

- `DATASOURCE`je vyžadováno `BULK INSERT` v příkazu při importu souborů z úložiště objektů blob Azure. Viz [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`je vyžadována `OPENROWSET` ve funkci při čtení obsahu souboru z úložiště objektů blob Azure. Viz [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET`slouží ke čtení dat z jiných databází Azure SQL, spravovaných instancí nebo instancí SERVERU SQL Server. Jiné zdroje, například databáze Oracle nebo soubory aplikace Excel, nejsou podporovány.

### <a name="clr"></a>CLR

Spravovaná instance nemá přístup ke sdíleným složkám souborů a složkám systému Windows, takže platí následující omezení:

- Je `CREATE ASSEMBLY FROM BINARY` podporována pouze. Viz [CREATE ASSEM BLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE`není podporována. Viz [VYTVOŘENÍ SESTAVENÍ Ze SOUBORU](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY`nelze odkazovat na soubory. Viz [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Databáze pošty (db_mail)
 - `sp_send_dbmail`aplikace nemůže odesílat přílohy pomocí @file_attachments parametru. Místní systém souborů a externí sdílené složky nebo Azure Blob Storage nejsou přístupné z tohoto postupu.
 - Podívejte se na `@query` známé problémy související s parametrem a ověřováním.
 
### <a name="dbcc"></a>Dbcc

Nedokumentované příkazy DBCC, které jsou povoleny v SQL Server nejsou podporovány ve spravovaných instancích.

- Podporován je pouze omezený počet příznaků globálního trasování. Úroveň `Trace flags` relace není podporována. Viz [Příznaky trasování](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) a [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) pracují s omezeným počtem globálních příznaků trasování.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) s možnostmi REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST a REPAIR_REBUILD nelze `SINGLE_USER` použít, protože databázi nelze nastavit v režimu - viz [rozdíly v databázi ALTER](#alter-database-statement). Potenciální poškození databáze zpracovává tým podpory Azure. Obraťte se na podporu Azure, pokud jste si všiml poškození databáze, která by měla být opravena.

### <a name="distributed-transactions"></a>Distribuované transakce

MSDTC a [elastické transakce](sql-database-elastic-transactions-overview.md) aktuálně nejsou podporovány ve spravovaných instancích.

### <a name="extended-events"></a>Rozšířené události

Některé cíle specifické pro Windows pro rozšířené události (XEvents) nejsou podporovány:

- Cíl `etw_classic_sync` není podporován. Uklápěte `.xel` soubory do úložiště objektů blob Azure. Viz [cíl etw_classic_sync](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Cíl `event_file` není podporován. Uklápěte `.xel` soubory do úložiště objektů blob Azure. Viz [cíl event_file](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externí knihovny

V databázi R a Python, externí knihovny ještě nejsou podporovány. Viz [SQL Server Machine Learning Services](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>Filestream a Tabulka souborů

- Data datového proudu souborů nejsou podporována.
- Databáze nemůže obsahovat skupiny `FILESTREAM` souborů s daty.
- `FILETABLE`není podporována.
- Tabulky nemohou mít `FILESTREAM` typy.
- Následující funkce nejsou podporovány:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Další informace naleznete [v tématech FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) a [FileTables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Fulltextové sémantické vyhledávání

[Sémantické hledání](/sql/relational-databases/search/semantic-search-sql-server) není podporováno.

### <a name="linked-servers"></a>Propojené servery

Propojené servery ve spravovaných instancích podporují omezený počet cílů:

- Podporované cíle jsou spravované instance, jednoduché databáze a instance serveru SQL Server. 
- Propojené servery nepodporují distribuované zapisovatelné transakce (MS DTC).
- Cíle, které nejsou podporovány, jsou soubory, Analysis Services a další RDBMS. Zkuste použít nativní import CSV z `BULK INSERT` `OPENROWSET` Azure Blob Storage pomocí nebo jako alternativu pro import souborů.

Provoz

- Transakce zápisu mezi instancemi nejsou podporovány.
- `sp_dropserver`je podporován pro vyhotovení propojeného serveru. Viz [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Funkci `OPENROWSET` lze použít ke spuštění dotazů pouze na instancích serveru SQL Server. Mohou být spravované, místní nebo ve virtuálních počítačích. Viz [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- Funkci `OPENDATASOURCE` lze použít ke spuštění dotazů pouze na instancích serveru SQL Server. Mohou být spravované, místní nebo ve virtuálních počítačích. Jako `SQLNCLI`zprostředkovatel `SQLNCLI11`jsou `SQLOLEDB` podporovány pouze hodnoty , a . Příklad: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Viz [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Propojené servery nelze použít ke čtení souborů (Excel, CSV) ze sdílených síťových složek. Zkuste použít [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) nebo [OPENROWSET,](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) který čte soubory CSV z azure blob storage. Sledování těchto požadavků u [položky zpětné vazby spravované instance](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Externí tabulky, které odkazují na soubory v HDFS nebo azure blob úložiště nejsou podporovány. Informace o PolyBase naleznete v tématu [PolyBase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikace

- Jsou podporovány typy snímek a obousměrné replikace. Slučovací replikace, replikace peer-to-peer a aktualizovatelná předplatná nejsou podporována.
- [Transakční replikace](sql-database-managed-instance-transactional-replication.md) je k dispozici pro public preview na spravované instanci s některými omezeními:
    - Všechny typy účastníků replikace (Vydavatel, Distributor, Vyžádat odběratel a Push odběratel) lze umístit na spravované instance, ale vydavatel a distributor musí být buď v cloudu nebo oba místní.
    - Spravované instance mohou komunikovat s nejnovějšími verzemi serveru SQL Server. Další informace naleznete v [matici podporovaných verzí.](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems)
    - Transakční replikace má některé [další síťové požadavky](sql-database-managed-instance-transactional-replication.md#requirements).

Další informace o konfiguraci transakční replikace naleznete v následujících kurzech:
- [Replikace mezi vydavatelem mi a odběratelem](replication-with-sql-database-managed-instance.md)
- [Replikace mezi vydavatelem MI, distributorem MI a odběratelem serveru SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)

### <a name="restore-statement"></a>Příkaz RESTORE 

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
  - `FROM URL`(Azure Blob storage) je jediná podporovaná možnost.
  - `FROM DISK`/`TAPE`/backup device není podporováno.
  - Zálohovací sady nejsou podporovány.
- `WITH`možnosti nejsou podporovány, `DIFFERENTIAL` například ne nebo `STATS`.
- `ASYNC RESTORE`: Obnovení pokračuje i v případě, že se připojení klienta přeruší. Pokud je připojení přerušeno, `sys.dm_operation_status` můžete zkontrolovat zobrazení stavu operace obnovení a pro create a drop databáze. Viz [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Následující možnosti databáze jsou nastaveny nebo přepsány a nelze je později změnit: 

- `NEW_BROKER`pokud makléř není povolen v souboru .bak. 
- `ENABLE_BROKER`pokud makléř není povolen v souboru .bak. 
- `AUTO_CLOSE=OFF`pokud databáze v souboru `AUTO_CLOSE=ON`.bak obsahuje . 
- `RECOVERY FULL`pokud databáze v souboru `SIMPLE` .bak má nebo `BULK_LOGGED` režim obnovy.
- Paměť-optimalizované filegroup je přidán a volal XTP, pokud to nebylo ve zdroji .bak souboru. 
- Všechny existující skupiny souborů optimalizované pro paměť jsou přejmenovány na XTP. 
- `SINGLE_USER`a `RESTRICTED_USER` možnosti jsou `MULTI_USER`převedeny na .

Omezení: 

- Zálohy poškozených databází může být obnovena v závislosti na typu poškození, ale automatické zálohy nebudou přijata, dokud poškození je opraveno. Ujistěte se, `DBCC CHECKDB` že spustíte na `WITH CHECKSUM` zdrojové instance a použít zálohování, aby se zabránilo tomuto problému.
- Obnovení `.BAK` souboru databáze, která obsahuje omezení popsané v tomto `FILESTREAM` `FILETABLE` dokumentu (například nebo objekty), nelze obnovit ve spravované instanci.
- `.BAK`soubory, které obsahují více zálohovacích sad, nelze obnovit. 
- `.BAK`soubory, které obsahují více souborů protokolu nelze obnovit.
- Zálohy, které obsahují databáze větší než 8 TB, aktivní objekty OLTP v paměti nebo počet souborů, které by překročily 280 souborů na instanci, nelze obnovit v instanci General Purpose. 
- Zálohy, které obsahují databáze větší než 4 TB nebo v paměti OLTP objekty s celkovou velikostí větší než velikost popsaná v [omezení prostředků](sql-database-managed-instance-resource-limits.md) nelze obnovit na business critical instance.
Informace o příkazech obnovení naleznete v tématu [RESTORE statements](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Stejná omezení platí pro předdefinovanou operaci obnovení bodu v čase. Jako příklad obecné účely databáze větší než 4 TB nelze obnovit na business critical instance. Kritická obchodní databáze se soubory OLTP v paměti nebo více než 280 soubory nelze obnovit v instanci General Purpose.

### <a name="service-broker"></a>Zprostředkovatel služeb

Zprostředkovatel služeb mezi instancemi není podporován:

- `sys.routes`: Jako předpoklad je nutné vybrat adresu ze sys.routes. Adresa musí být místní na každé trase. Viz [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Nelze použít `CREATE ROUTE` s `ADDRESS` jiným `LOCAL`než . Viz [VYTVOŘENÍ TRASY](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Nelze použít `ALTER ROUTE` s `ADDRESS` jiným `LOCAL`než . Viz [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Uložené procedury, funkce a aktivační události

- `NATIVE_COMPILATION`není podporována ve vrstvě Obecné účely.
- Následující [možnosti sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) nejsou podporovány: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`není podporována. Viz [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell`není podporována. Viz [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`nejsou podporovány, což `sp_addextendedproc`   `sp_dropextendedproc`zahrnuje a . Viz [Rozšířené uložené procedury](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db`a `sp_detach_db` nejsou podporovány. Viz [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)a [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Systémové funkce a proměnné

Následující proměnné, funkce a zobrazení vrátí různé výsledky:

- `SERVERPROPERTY('EngineEdition')`vrátí hodnotu 8. Tato vlastnost jednoznačně identifikuje spravovanou instanci. Viz [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`vrátí hodnotu NULL, protože koncept instance, protože existuje pro SQL Server, se nevztahuje na spravovanou instanci. Viz [SERVERPROPERTY('Instancename')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`vrátí úplný název DNS "připojitelný", například my-managed-instance.wcus17662feb9ce98.database.windows.net. Viz [@SERVERNAME@](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS`vrátí úplný název DNS "připojitelný", například `myinstance.domain.database.windows.net` pro vlastnosti "název" a "data_source". Viz [Sys. SERVERY](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME`vrátí hodnotu NULL, protože koncept služby tak, jak existuje pro SQL Server, se nevztahuje na spravovanou instanci. Viz [@SERVICENAME@](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID`je podporována. Vrátí hodnotu NULL, pokud přihlášení Azure AD není v sys.syslogins. Viz [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID`není podporována. Jsou vrácena nesprávná data, což je dočasný známý problém. Viz [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Omezení prostředí

### <a name="subnet"></a>Podsíť
-  Do podsítě, do které jste nasadili spravovanou instanci, nelze umístit žádné jiné prostředky (například virtuální počítače). Nasaďte tyto prostředky pomocí jiné podsítě.
- Podsíť musí mít dostatečný počet [dostupných adres IP](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Minimum je 16, zatímco doporučení má mít v podsíti alespoň 32 adres IP.
- [Koncové body služby nelze přidružit k podsíti spravované instance](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Ujistěte se, že možnost koncových bodů služby je zakázána při vytváření virtuální sítě.
- Počet virtuálních jader a typy instancí, které můžete nasadit v oblasti, mají určitá [omezení a omezení](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
- V [podsíti musí být použita](sql-database-managed-instance-connectivity-architecture.md#network-requirements)některá pravidla zabezpečení .

### <a name="vnet"></a>Virtuální síť pro virtuální měn
- Virtuální síť se dá nasadit pomocí modelu prostředků – klasický model pro virtuální síť není podporovaný.
- Po vytvoření spravované instance není přesunutí spravované instance nebo virtuální sítě do jiné skupiny prostředků nebo předplatného podporováno.
- Některé služby, jako jsou prostředí služby App Service, aplikace logiky a spravované instance (používané pro geografickou replikaci, transakční replikaci nebo prostřednictvím propojených serverů), nemají přístup ke spravovaným instancím v různých oblastech, pokud jsou jejich virtuální sítě propojeny pomocí [globálního partnerského vztahu](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). K těmto prostředkům se můžete připojit prostřednictvím ExpressRoute nebo virtuální sítě k virtuální síti prostřednictvím bran virtuální sítě.

### <a name="tempdb"></a>Tempdb

Maximální velikost souboru `tempdb` nesmí být větší než 24 GB na jádro na úrovni pro obecné účely. Maximální `tempdb` velikost na úrovni Business Critical je omezena velikostí úložiště instance. `Tempdb`velikost souboru protokolu je omezena na 120 GB na úrovni obecného použití. Některé dotazy mohou vrátit chybu, pokud potřebují více než `tempdb` 24 GB na jádro nebo pokud produkují více než 120 GB dat protokolu.

### <a name="msdb"></a>Msdb

Následující schémata MSDB ve spravované instanci musí být vlastněna jejich předdefinovanými rolemi:

- Obecné role
  - Cílová serversrole
- [Pevné databázové role](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [Role DatabaseMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - DatabaseMailUserRole
- [Role integračních služeb](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15):
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Změna předdefinovaných názvů rolí, názvů schémat a vlastníků schémat u zákazníků bude mít vliv na normální provoz služby. Všechny změny provedené v těchto budou vráceny zpět na předdefinované hodnoty, jakmile zjistíte, nebo nejpozději při další aktualizaci služby, aby byl zajištěn normální provoz služby.

### <a name="error-logs"></a>Protokoly chyb

Spravovaná instance umístí podrobné informace do protokolů chyb. Existuje mnoho interních systémových událostí, které jsou zaznamenány v protokolu chyb. Pomocí vlastního postupu můžete číst protokoly chyb, které filtrují některé irelevantní položky. Další informace najdete v tématu [spravované instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) nebo rozšíření spravované instance [(preview)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) pro Azure Data Studio.

## <a name="next-steps"></a>Další kroky

- Další informace o spravovaných instancích najdete [v tématu Co je spravovaná instance?](sql-database-managed-instance.md)
- Funkce a seznam porovnání najdete v tématu [porovnání funkcí Databáze Azure SQL](sql-database-features.md).
- Aktualizace verzí a stav známých problémů najdete v tématu [POZNÁMKY k verzi databáze SQL](sql-database-release-notes.md)
- Rychlý start, který ukazuje, jak vytvořit novou spravovanou instanci, najdete [v tématu Vytvoření spravované instance](sql-database-managed-instance-get-started.md).
