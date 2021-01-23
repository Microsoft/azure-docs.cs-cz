---
title: Rozdíly v jazyce T-SQL mezi SQL Server & spravované instance Azure SQL
description: Tento článek popisuje rozdíly v jazyce Transact-SQL (T-SQL) mezi spravovanou instancí Azure SQL a SQL Server.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 11/10/2020
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 6634ab3521fee3062ecee465eaf6dcda80ee6ff8
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98699510"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>Rozdíly v jazyce T-SQL mezi SQL Server & spravované instance Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek shrnuje a vysvětluje rozdíly mezi syntaxí a chováním mezi spravovanou instancí Azure SQL a SQL Server. 


Spravovaná instance SQL poskytuje vysokou kompatibilitu s SQL Server databázovým strojem a většina funkcí je podporovaná ve spravované instanci SQL.

![Snadná migrace z SQL Server](./media/transact-sql-tsql-differences-sql-server/migration.png)

Existují některá omezení PaaS, která se zavádějí do spravované instance SQL, a některé změny chování v porovnání s SQL Server. Rozdíly jsou rozděleny do následujících kategorií: <a name="Differences"></a>

- [Dostupnost](#availability) zahrnuje rozdíly v případě [záloh](#backup)a [skupin dostupnosti Always On](#always-on-availability-groups) .
- [Zabezpečení](#security) zahrnuje rozdíly v [auditování](#auditing), [certifikáty](#certificates), [přihlašovací údaje](#credential), [zprostředkovatele kryptografických](#cryptographic-providers)služeb, [přihlášení a uživatele](#logins-and-users)a [klíč služby a hlavní klíč služby](#service-key-and-service-master-key).
- [Konfigurace](#configuration) zahrnuje rozdíly v [rozšíření fondu vyrovnávací paměti](#buffer-pool-extension), [řazení](#collation), [úrovně kompatibility](#compatibility-levels), [zrcadlení databáze](#database-mirroring), [Možnosti databáze](#database-options), [SQL Server agenta](#sql-server-agent)a [Možnosti tabulky](#tables).
- Mezi [funkce](#functionalities) patří [Bulk INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [distribuované transakce](#distributed-transactions), [Rozšířené události](#extended-events), [externí knihovny](#external-libraries), [FileStream a Souborová](#filestream-and-filetable) [sémantika, fulltextové vyhledávání](#full-text-semantic-search), [propojené servery](#linked-servers), [základní](#polybase), [replikace](#replication), [obnovení](#restore-statement), [Service Broker](#service-broker), [uložené procedury, funkce a triggery](#stored-procedures-functions-and-triggers).
- [Nastavení prostředí](#Environment) , jako jsou například virtuální sítě a konfigurace podsítí.

Většina těchto funkcí je omezení architektury a představuje funkce služby.

Dočasné známé problémy, které jsou zjištěny ve spravované instanci SQL a budou vyřešeny v budoucnu, jsou popsány na [stránce poznámky k verzi](../database/doc-changes-updates-release-notes.md).

## <a name="availability"></a>Dostupnost

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Skupiny dostupnosti AlwaysOn

[Vysoká dostupnost](../database/high-availability-sla.md) je integrována do spravované instance SQL a nemůže být řízena uživateli. Následující příkazy nejsou podporovány:

- [VYTVOŘIT KONCOVÝ BOD... PRO DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [VYTVOŘIT SKUPINU DOSTUPNOSTI](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ZMĚNIT SKUPINU DOSTUPNOSTI](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [ODPOJIT SKUPINU DOSTUPNOSTI](/sql/t-sql/statements/drop-availability-group-transact-sql)
- Klauzule [set hadr](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) příkazu [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

Spravovaná instance SQL má automatické zálohování, takže uživatelé můžou vytvářet úplné `COPY_ONLY` zálohy databáze. Zálohy rozdílů, protokolů a snímků souborů se nepodporují.

- Pomocí spravované instance SQL můžete zálohovat databázi instancí jenom do účtu Azure Blob Storage:
  - `BACKUP TO URL`Podporuje se jenom.
  - `FILE``TAPE`a zálohovací zařízení nejsou podporována.
- Většina obecných `WITH` možností je podporovaná.
  - `COPY_ONLY` je povinné.
  - `FILE_SNAPSHOT` není podporováno.
  - Možnosti pásky: `REWIND` , `NOREWIND` , `UNLOAD` a `NOUNLOAD` nejsou podporovány.
  - Možnosti specifické pro protokol: `NORECOVERY` , `STANDBY` a `NO_TRUNCATE` nejsou podporovány.

Omezení: 

- Pomocí spravované instance SQL můžete zálohovat databázi instance do zálohy s až 32 proužky, které jsou pro databáze až 4 TB v případě, že se používá zálohování zálohy, v případě, že je použita komprese záloh.
- Nemůžete provést `BACKUP DATABASE ... WITH COPY_ONLY` na databázi, která je zašifrovaná pomocí transparentní šifrování dat pro správu spravovaných službou (TDE). TDE spravované službou vynutí šifrování záloh pomocí interního TDE klíče. Klíč nelze exportovat, takže nelze obnovit zálohu. Použijte automatické zálohování a obnovení k bodu v čase nebo použijte místo toho [TDE spravované zákazníkem (BYOK)](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) . Šifrování můžete také zakázat v databázi.
- Nativní zálohy provedené na spravované instanci nelze obnovit do SQL Server. Důvodem je to, že spravovaná instance má vyšší verzi interní databáze v porovnání s libovolnou verzí SQL Server.
- Maximální velikost proložení při zálohování pomocí `BACKUP` příkazu ve spravované instanci SQL je 195 GB, což je maximální velikost objektu BLOB. Zvyšte počet pruhů v příkazu Backup, abyste snížili velikost jednotlivých pruhů a zůstali v rámci tohoto limitu.

    > [!TIP]
    > Pokud chcete toto omezení obejít, můžete při zálohování databáze z libovolného SQL Server v místním prostředí nebo ve virtuálním počítači:
    >
    > - Zálohování na `DISK` místo zálohování do `URL` .
    > - Nahrajte záložní soubory do úložiště objektů BLOB.
    > - Obnovit do spravované instance SQL
    >
    > `Restore`Příkaz ve spravované instanci SQL podporuje v záložních souborech větší velikosti objektů blob, protože pro ukládání nahraných záložních souborů se používá jiný typ objektu BLOB.

Informace o zálohování pomocí T-SQL najdete v tématu [zálohování](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Zabezpečení

### <a name="auditing"></a>Auditování

Mezi hlavní rozdíly mezi auditováním v Microsoft Azure SQL a v SQL Server patří:

- Pomocí spravované instance SQL funguje auditování na úrovni serveru. `.xel`Soubory protokolu se ukládají v úložišti objektů BLOB v Azure.
- S Azure SQL Database auditování funguje na úrovni databáze. `.xel`Soubory protokolu se ukládají v úložišti objektů BLOB v Azure.
- Při SQL Server místních i virtuálních počítačích funguje auditování na úrovni serveru. Události se ukládají do systému souborů nebo protokolů událostí systému Windows.
 
Auditování XEvent ve spravované instanci SQL podporuje cíle služby Azure Blob Storage. Soubory a protokoly systému Windows nejsou podporovány.

Hlavní rozdíly v `CREATE AUDIT` syntaxi pro auditování do úložiště objektů BLOB v Azure jsou:

- `TO URL`K dispozici je nová syntaxe, kterou můžete použít k zadání adresy URL kontejneru úložiště objektů BLOB v Azure, ve kterém `.xel` jsou soubory umístěné.
- Syntaxe `TO FILE` není podporována, protože spravovaná instance SQL nemůže přistupovat ke sdíleným složkám souborů systému Windows.

Další informace naleznete v tématu: 

- [VYTVOŘIT AUDIT SERVERU](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditování](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certifikáty

Spravovaná instance SQL nemůže přistupovat ke sdíleným složkám souborů a složkám systému Windows, proto platí následující omezení:

- `CREATE FROM` / `BACKUP TO` Soubor není pro certifikáty podporovaný.
- `CREATE` / `BACKUP` Certifikát od `FILE` / `ASSEMBLY` není podporovaný. Soubory privátních klíčů nelze použít. 

Viz [Vytvoření certifikátu](/sql/t-sql/statements/create-certificate-transact-sql) a [záložního certifikátu](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Alternativní řešení**: místo vytváření zálohy certifikátu a obnovení zálohy [Získejte binární obsah certifikátu a privátní klíč, uložte ho jako soubor. SQL a vytvořte ho z binárního souboru](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Přihlašovací údaj

Jsou podporovány pouze Azure Key Vault a `SHARED ACCESS SIGNATURE` identity. Uživatelé systému Windows nejsou podporováni.

Viz [Vytvoření přihlašovacích údajů](/sql/t-sql/statements/create-credential-transact-sql) a [Změna přihlašovacích údajů](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Zprostředkovatelé kryptografických služeb

Spravovaná instance SQL nemá přístup k souborům, takže zprostředkovatele kryptografických služeb nelze vytvořit:

- `CREATE CRYPTOGRAPHIC PROVIDER` není podporováno. Viz [vytvořit zprostředkovatele kryptografických služeb](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` není podporováno. Přečtěte si téma [Změna zprostředkovatele kryptografických služeb](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Přihlášení a uživatelé

- Přihlášení SQL vytvořená pomocí `FROM CERTIFICATE` , `FROM ASYMMETRIC KEY` a `FROM SID` jsou podporovaná. Viz [Vytvoření přihlašovacích](/sql/t-sql/statements/create-login-transact-sql)údajů.
- Jsou podporovány základní objekty (přihlašovací údaje) serveru služby Azure Active Directory (Azure AD) vytvořené pomocí syntaxe [Vytvoření přihlášení](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) nebo [Vytvoření uživatele z přihlášení [přihlášení Azure AD]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) . Tato přihlášení jsou vytvořena na úrovni serveru.

    Spravovaná instance SQL podporuje objekty zabezpečení databáze Azure AD se syntaxí `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` . Tato funkce se označuje taky jako uživatelé databáze s omezením Azure AD.

- Přihlášení Windows vytvořená pomocí `CREATE LOGIN ... FROM WINDOWS` syntaxe nejsou podporovaná. Používejte Azure Active Directory přihlašovacích údajů a uživatelů.
- Uživatel Azure AD, který vytvořil instanci, má [neomezená oprávnění správce](../database/logins-create-manage.md).
- Uživatele na úrovni databáze služby Azure AD, kteří nejsou správci, se dají vytvořit pomocí `CREATE USER ... FROM EXTERNAL PROVIDER` syntaxe. Viz [vytvořit uživatele... OD externího poskytovatele](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).
- Objekty zabezpečení serveru Azure AD (přihlášení) podporují funkce SQL jenom v rámci jedné spravované instance SQL. Funkce, které vyžadují interakci mezi instancemi, bez ohledu na to, jestli jsou ve stejném tenantovi Azure AD nebo v různých klientech, se pro uživatele Azure AD nepodporují. Příklady takových funkcí:

  - Transakční replikace SQL.
  - Propojit Server.

- Nastavení přihlašovacích údajů Azure AD namapované na skupinu Azure AD, protože vlastník databáze není podporovaný.
- Je podporováno zosobnění objektů zabezpečení na úrovni serveru Azure AD pomocí jiných objektů zabezpečení Azure AD, jako je například klauzule [Execute as](/sql/t-sql/statements/execute-as-transact-sql) . Spustit jako omezení jsou:

  - Příkaz Spustit jako uživatel není podporován pro uživatele Azure AD, pokud se název liší od přihlašovacího jména. Příkladem je, že uživatel je vytvořen pomocí syntaxe CREATE USER [myAadUser] FROM LOGIN [ john@contoso.com ] a při pokusu o zosobnění se provádí pomocí příkazu EXEC jako uživatel = _myAadUser_. Když vytváříte **uživatele** z objektu zabezpečení serveru Azure AD (přihlášení), zadejte user_name jako stejný Login_name od **přihlášení**.
  - Jenom objekty zabezpečení na úrovni SQL Server (přihlášení), které jsou součástí `sysadmin` role, můžou spouštět následující operace, které cílí na objekty zabezpečení Azure AD:

    - SPUSTIT JAKO UŽIVATEL
    - SPUSTIT JAKO PŘIHLAŠOVACÍ ÚDAJE

  - K zosobnění uživatele pomocí příkazu Spustit jako musí být uživatel namapován přímo na objekt zabezpečení serveru Azure AD (přihlášení). Uživatelé, kteří jsou členy skupin Azure AD mapovaných na objekty zabezpečení Azure AD serveru, se nedají efektivně zosobnit pomocí příkazu Spustit jako, i když volající má pro zadané uživatelské jméno oprávnění k zosobnění.

- Pro uživatele Azure AD ve spravované instanci SQL se podporuje export a import databáze pomocí souborů BacPac buď [SSMS v 18.4 nebo novější](/sql/ssms/download-sql-server-management-studio-ssms), nebo [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - Následující konfigurace jsou podporovány pomocí souboru databáze BacPac: 
    - Exportujte/Importujte databázi mezi různými spravovanými instancemi v rámci stejné domény služby Azure AD.
    - Exportujte databázi ze spravované instance SQL a importujte ji do SQL Database v rámci stejné domény služby Azure AD. 
    - Exportujte databázi z SQL Database a importujte ji do spravované instance SQL ve stejné doméně Azure AD.
    - Exportujte databázi ze spravované instance SQL a importujte ji do SQL Server (verze 2012 nebo novější).
      - V této konfiguraci jsou všichni uživatelé Azure AD vytvořeni jako SQL Server objekty databáze (uživatelé) bez přihlášení. Typ uživatelů je uveden jako `SQL` a je viditelný jako `SQL_USER` v sys.database_principals). Jejich oprávnění a role zůstávají v SQL Server metadatech databáze a lze je použít pro zosobnění. Nedají se ale použít k přístupu k SQL Server a k jejich přihlášení pomocí svých přihlašovacích údajů.

- Pouze hlavní přihlášení na úrovni serveru, které je vytvořeno procesem zřizování spravované instance SQL, členové rolí serveru, jako je například `securityadmin` nebo `sysadmin` , nebo jiná přihlášení s OPRÁVNĚNÍMI změnit libovolné přihlašovací oprávnění na úrovni serveru, mohou vytvořit objekty zabezpečení serveru Azure AD (přihlášení) v hlavní databázi pro SPRAVOVANOU instanci SQL.
- Pokud je přihlášení objektem zabezpečení SQL, `sysadmin` můžou k vytvoření přihlašovacích údajů pro účet Azure AD používat jenom přihlášení, která jsou součástí této role.
- Přihlášení Azure AD musí být členem služby Azure AD v rámci stejného adresáře, který se používá pro spravovanou instanci Azure SQL.
- Objekty zabezpečení serveru Azure AD (přihlášení) se zobrazují v Průzkumník objektů počínaje verzí SQL Server Management Studio 18,0 Preview 5.
- Překrývající se objekty zabezpečení serveru Azure AD (přihlášení) s účtem správce Azure AD jsou povolené. Objekty zabezpečení serveru Azure AD (přihlášení) mají přednost před správcem Azure AD při řešení zabezpečení a uplatnění oprávnění pro spravovanou instanci SQL.
- Při ověřování se pro řešení ověřování objektu zabezpečení používá následující sekvence:

    1. Pokud účet Azure AD existuje přímo namapovaný na objekt zabezpečení serveru Azure AD (přihlášení), který se nachází v sys.server_principals jako typ "E", udělte přístup a uplatní se oprávnění objektu zabezpečení serveru Azure AD (přihlášení).
    2. Pokud je účet Azure AD členem skupiny Azure AD, která je namapovaná na objekt zabezpečení serveru Azure AD (přihlášení), který se nachází v sys.server_principals jako typ "X", "udělit přístup a použít oprávnění pro přihlášení ke skupině Azure AD.
    3. Pokud je účet Azure AD zvláštním portálem konfigurovaným pro správce Azure AD pro spravovanou instanci SQL, který neexistuje v systémových zobrazeních spravované instance SQL, použijte zvláštní pevná oprávnění správce Azure AD pro spravovanou instanci SQL (starší režim).
    4. Pokud účet Azure AD existuje jako přímo namapovaný k uživateli Azure AD v databázi, která je k dispozici v sys.database_principals jako typ "E", udělte přístup a uplatní se oprávnění uživatele databáze Azure AD.
    5. Pokud je účet Azure AD členem skupiny služby Azure AD, která je namapovaná na uživatele Azure AD v databázi, která je k dispozici v sys.database_principals jako typ "X", "udělit přístup a použít oprávnění pro přihlášení ke skupině Azure AD.
    6. Pokud máte přihlášení ke službě Azure AD namapované na uživatelský účet Azure AD nebo na účet skupiny Azure AD, který se přeloží na uživatele, který ověřuje, budou použita všechna oprávnění z tohoto přihlášení služby Azure AD.

### <a name="service-key-and-service-master-key"></a>Klíč služby a hlavní klíč služby

- [Záloha hlavního klíče](/sql/t-sql/statements/backup-master-key-transact-sql) není podporována (spravováno službou SQL Database).
- [Obnovení hlavního klíče](/sql/t-sql/statements/restore-master-key-transact-sql) se nepodporuje (spravuje služba SQL Database).
- [Záloha hlavního klíče služby](/sql/t-sql/statements/backup-service-master-key-transact-sql) není podporovaná (spravovaná službou SQL Database).
- [Obnovení hlavního klíče služby](/sql/t-sql/statements/restore-service-master-key-transact-sql) se nepodporuje (spravuje služba SQL Database).

## <a name="configuration"></a>Konfigurace

### <a name="buffer-pool-extension"></a>Rozšíření fondu vyrovnávací paměti

- [Rozšíření fondu vyrovnávací paměti](/sql/database-engine/configure-windows/buffer-pool-extension) se nepodporuje.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` není podporováno. Viz [ALTER Server Configuration](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Kolace

Výchozí kolace instance je `SQL_Latin1_General_CP1_CI_AS` a může být zadána jako parametr vytvoření. Viz [kolace](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Úrovně kompatibility

- Podporované úrovně kompatibility jsou 100, 110, 120, 130, 140 a 150.
- Úrovně kompatibility nižší než 100 nejsou podporovány.
- Výchozí úroveň kompatibility pro nové databáze je 140. V případě obnovených databází zůstane úroveň kompatibility beze změny, pokud byla 100 a vyšší.

Viz část [ALTER DATABASE Compatibility Level](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Zrcadlení databáze

Zrcadlení databáze se nepodporuje.

- `ALTER DATABASE SET PARTNER` a `SET WITNESS` Možnosti nejsou podporovány.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` není podporováno.

Další informace najdete v tématu věnovaném [příkazu ALTER DATABASE set partner a nastavení určujícího disku](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) a [Vytvoření koncového bodu... PRO DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Možnosti databáze

- Více souborů protokolu není podporováno.
- Objekty v paměti nejsou podporovány v Pro obecné účely úrovni služby. 
- Počet 280 souborů na instanci Pro obecné účely, což implikuje maximálně 280 souborů na databázi. Do tohoto limitu se počítají data i soubory protokolů v Pro obecné účely vrstvě. [Úroveň pro důležité obchodní informace podporuje 32 767 souborů na databázi](./resource-limits.md#service-tier-characteristics).
- Databáze nemůže obsahovat skupiny souborů, které obsahují data FILESTREAM. Obnovení se nezdařilo, pokud. bak obsahuje `FILESTREAM` data. 
- Každý soubor je umístěný v úložišti objektů BLOB v Azure. Vstupně-výstupní operace a propustnost na soubor závisí na velikosti každého jednotlivého souboru.

#### <a name="create-database-statement"></a>Příkaz CREATE DATABASE

Následující omezení platí pro `CREATE DATABASE` :

- Soubory a skupiny souborů nelze definovat. 
- `CONTAINMENT`Možnost není podporována. 
- `WITH` možnosti nejsou podporovány. 
   > [!TIP]
   > Alternativním řešením je použít `ALTER DATABASE` po `CREATE DATABASE` Nastavení možností databáze k přidávání souborů nebo k nastavení zahrnutí. 

- `FOR ATTACH`Možnost není podporována.
- `AS SNAPSHOT OF`Možnost není podporována.

Další informace najdete v tématu [Vytvoření databáze](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>ALTER DATABASE – příkaz

Některé vlastnosti souboru nejde nastavit nebo změnit:

- V příkazu t-SQL nejde zadat cestu k souboru `ALTER DATABASE ADD FILE (FILENAME='path')` . Odeberte `FILENAME` ze skriptu, protože spravované instance SQL soubory automaticky umístí. 
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

Některé `ALTER DATABASE` příkazy (například [Nastavení zahrnutí](https://docs.microsoft.com/sql/relational-databases/databases/migrate-to-a-partially-contained-database?#converting-a-database-to-partially-contained-using-transact-sql)) můžou být v přechodném případě neúspěšné, například během automatické zálohy databáze nebo hned po vytvoření databáze. V tomto případě `ALTER DATABASE` by se měl opakovat příkaz. Další informace o souvisejících chybových zprávách najdete v [části poznámky](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current&preserve-view=true&tabs=sqlpool#remarks-2).

Další informace najdete v tématu [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>Agent SQL Server

- Povolení a zakázání agenta SQL Server v současnosti není ve spravované instanci SQL podporováno. Agent SQL je vždy spuštěný.
- Nastavení agenta SQL Server jsou jen pro čtení. Procedura `sp_set_agent_properties` není podporována ve spravované instanci SQL. 
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
  - Spravovaná instance SQL nemá přístup k externím prostředkům, například ke sdíleným složkám sítě prostřednictvím nástroje Robocopy. 
  - Služba Analysis Services serveru SQL se nepodporuje.
- Oznámení jsou částečně podporovaná.
- E-mailové oznámení je podporované, i když vyžaduje, abyste nakonfigurovali profil Databázová pošta. Agent SQL Server může používat jenom jeden profil Databázová pošta a musí se volat `AzureManagedInstance_dbmail_profile` . 
  - Pager není podporován.
  - NetSend se nepodporuje.
  - Výstrahy ještě nejsou podporované.
  - Proxy servery nejsou podporovány.
- Protokol událostí se nepodporuje.
- Aby bylo možné vytvořit, upravit nebo spustit úlohy agenta SQL, musí být uživatel přímo namapován na objekt zabezpečení serveru Azure AD (přihlášení). Uživatelé, kteří nejsou přímo namapováni, například uživatelé patřící do skupiny Azure AD s právy k vytváření, úpravám nebo provádění úloh agenta SQL, nebudou moci tyto akce provádět efektivně. Důvodem je zosobnění spravované instance a [spuštění jako omezení](#logins-and-users).

Následující funkce agenta SQL momentálně nejsou podporované:

- Proxy
- Plánování úloh na nečinném procesoru
- Povolení nebo zakázání agenta
- Výstrahy

Informace o agentovi SQL Server najdete v tématu [agent SQL Server](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabulky

Následující typy tabulek nejsou podporovány:

- [SOUBOREM](/sql/relational-databases/blob/filestream-sql-server)
- [OBJEKTU FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [Externí tabulka](/sql/t-sql/statements/create-external-table-transact-sql) (základ)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (Nepodporováno pouze v pro obecné účely úrovni)

Informace o tom, jak vytvářet a měnit tabulky, najdete v tématu [Create Table](/sql/t-sql/statements/create-table-transact-sql) a [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkce

### <a name="bulk-insert--openrowset"></a>Hromadné vložení/OPENROWSET

Spravovaná instance SQL nemůže přistupovat ke sdíleným složkám souborů a složkám Windows, takže soubory musí být naimportované z úložiště objektů BLOB v Azure:

- `DATASOURCE` v příkazu se vyžaduje `BULK INSERT` při importu souborů z úložiště objektů BLOB v Azure. Viz [Bulk INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` se ve funkci vyžaduje `OPENROWSET` při čtení obsahu souboru z úložiště objektů BLOB v Azure. Viz [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` dá se použít ke čtení dat z Azure SQL Database, spravované instance Azure SQL nebo instancí SQL Server. Jiné zdroje, například databáze Oracle nebo excelové soubory, nejsou podporovány.

### <a name="clr"></a>CLR

Spravovaná instance SQL nemůže přistupovat ke sdíleným složkám souborů a složkám systému Windows, proto platí následující omezení:

- `CREATE ASSEMBLY FROM BINARY`Podporuje se jenom. Viz [vytvoření sestavení z binárního souboru](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` není podporováno. Viz [vytvořit sestavení ze souboru](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` nejde odkazovat na soubory. Viz [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Databázová pošta (db_mail)
 - `sp_send_dbmail` přílohy nelze odeslat pomocí @file_attachments parametru. Tento postup nemá přístup k místnímu systému souborů a externím sdíleným složkám nebo k Azure Blob Storage.
 - Podívejte se na známé problémy související s `@query` parametrem a ověřením.
 
### <a name="dbcc"></a>NÁSTROJI

Nedokumentované příkazy DBCC povolené v SQL Server nejsou ve spravované instanci SQL podporovány.

- Podporován je pouze omezený počet globálních příznaků trasování. Úroveň relace `Trace flags` není podporována. Viz [příznaky trasování](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF kterým](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) a [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) fungují s omezeným počtem globálních příznaků Trace-Flags.
- [Příkaz DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) s možnostmi REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST a REPAIR_REBUILD nelze použít, protože databázi nelze nastavit v `SINGLE_USER` režimu – viz téma [ALTER DATABASE rozdíl](#alter-database-statement). Potenciální poškození databáze zpracovává tým podpory Azure. Pokud nějaký náznak poškození databáze neexistují, obraťte se na podporu Azure.

### <a name="distributed-transactions"></a>Distribuované transakce

Částečná podpora [distribuovaných transakcí](../database/elastic-transactions-overview.md) je v současnosti ve verzi Public Preview. Podporované scénáře:
* Transakce, kde se účastní jenom spravované instance Azure SQL, které jsou součástí [skupiny důvěryhodných serverů](./server-trust-group-overview.md).
* Transakce iniciované z rozhraní .NET (třída TransactionScope) a jazyka Transact-SQL.

Azure SQL Managed instance v současné době nepodporuje další scénáře, které jsou pravidelně podporované službou MSDTC místně nebo v Azure Virtual Machines.

### <a name="extended-events"></a>Rozšířené události

Rozšířené události (XEvents) nepodporují některé cíle specifické pro Windows:

- `etw_classic_sync`Cíl není podporován. Ukládejte `.xel` soubory v úložišti objektů BLOB v Azure. Viz [cíl etw_classic_sync](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file`Cíl není podporován. Ukládejte `.xel` soubory v úložišti objektů BLOB v Azure. Viz [cíl event_file](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externí knihovny

Externí knihovny in-Database R a Python jsou podporované ve verzi Public Preview s omezením. Viz [Machine Learning Services ve spravované instanci SQL Azure (Preview)](machine-learning-services-overview.md).

### <a name="filestream-and-filetable"></a>FileStream a FileTable

- Data FILESTREAM nejsou podporována.
- Databáze nemůže obsahovat skupiny souborů s `FILESTREAM` daty.
- `FILETABLE` není podporováno.
- Tabulky nemůžou mít `FILESTREAM` typy.
- Následující funkce nejsou podporovány:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Další informace najdete v tématu [tabulky](/sql/relational-databases/blob/filetables-sql-server) [FileStream](/sql/relational-databases/blob/filestream-sql-server) a soubory.

### <a name="full-text-semantic-search"></a>Fulltextové vyhledávání na základě textu

[Sémantické vyhledávání](/sql/relational-databases/search/semantic-search-sql-server) není podporováno.

### <a name="linked-servers"></a>Propojené servery

Propojené servery ve spravované instanci SQL podporují omezený počet cílů:

- Podporované cíle jsou spravované instance SQL, SQL Database, Azure synapse bez SQL [serveru](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) a vyhrazené fondy a SQL Server instance. 
- Distribuované transakce s možností zápisu jsou možné jenom mezi spravovanými instancemi. Další informace najdete v tématu [distribuované transakce](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview). Služba MS DTC ale není podporovaná.
- Nepodporované cíle jsou soubory, Analysis Services a další RDBMS. Zkuste použít nativní Import souborů CSV z Azure Blob Storage použití `BULK INSERT` nebo `OPENROWSET` jako alternativu pro import souborů nebo načtěte soubory pomocí [serveru SQL bez serveru v Azure synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/).

Operace: 

- Transakce zápisu [mezi instancemi](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview) se podporují jenom pro spravované instance.
- `sp_dropserver` se podporuje pro vyřazení odkazovaného serveru. Viz [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET`Funkci lze použít ke spouštění dotazů pouze u instancí SQL Server. Můžou být spravované, místní nebo virtuální počítače. Viz [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE`Funkci lze použít ke spouštění dotazů pouze u instancí SQL Server. Můžou být spravované, místní nebo virtuální počítače. Pouze `SQLNCLI` hodnoty, `SQLNCLI11` a `SQLOLEDB` jsou podporovány jako zprostředkovatel. Příklad: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Viz [OpenDataSource](/sql/t-sql/functions/opendatasource-transact-sql).
- Propojené servery nelze použít ke čtení souborů (Excel, CSV) ze sdílených síťových složek. Zkuste použít [Bulk INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file), [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) , která čte soubory CSV z Azure Blob Storage, nebo [odkazovaný server, který odkazuje na SQL fond bez serveru v synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/). Sledovat tyto žádosti u [položky zpětné vazby spravované instance SQL](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Jediný dostupný typ externího zdroje je RDBMS (ve verzi Public Preview) do služby Azure SQL Database, spravované instance Azure SQL a fondu Azure synapse. Můžete použít [externí tabulku, která odkazuje na SQL fond bez serveru v synapse Analytics](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/) jako alternativní řešení pro základní externí tabulky, které přímo čtou z Azure Storage. Ve spravované instanci Azure SQL můžete propojené servery použít pro [neserverový fond SQL ve synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) nebo SQL Server ke čtení dat Azure Storage.
Informace o bázi základů naleznete v části [základ](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikace

- Podporují se typy snímků a obousměrné replikace. Slučovací replikace, replikace peer-to-peer a aktualizovatelné odběry nejsou podporovány.
- [Transakční replikace](replication-transactional-overview.md) je k dispozici pro verzi Public Preview na spravované instanci SQL s některými omezeními:
    - Všechny typy účastníků replikace (vydavatel, distributor, předplatitelé pro vyžádání obsahu a nabízený předplatitelé) je možné umístit do spravované instance SQL, ale Vydavatel a distributor musí být v cloudu i v místním prostředí.
    - Spravovaná instance SQL může komunikovat s nejnovějšími verzemi SQL Server. Další informace najdete v [matici podporované verze](replication-transactional-overview.md#supportability-matrix) .
    - Transakční replikace obsahuje některé [Další požadavky na síť](replication-transactional-overview.md#requirements).

Další informace o konfiguraci transakční replikace najdete v následujících kurzech:
- [Replikace mezi vydavatelem SQL MI a předplatitelem SQL MI](replication-between-two-instances-configure-tutorial.md)
- [Replikace mezi vydavatelem SQL MI, distributorem SQL MI a předplatitelem SQL Server](replication-two-instances-and-sql-server-configure-tutorial.md)

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
  - `FROM URL` (Azure Blob Storage) je jedinou podporovanou možností.
  - `FROM DISK`/`TAPE`zařízení/Backup se nepodporuje.
  - Zálohovací sklady nejsou podporované.
- `WITH` možnosti nejsou podporovány. Pokusy o obnovení, včetně `WITH` Like `DIFFERENTIAL` , `STATS` , atd `REPLACE` ., selžou.
- `ASYNC RESTORE`: Obnovení pokračuje i v případě, že dojde k přerušení připojení klienta. Pokud je připojení vyřazeno, můžete se podívat na `sys.dm_operation_status` stav operace obnovení a pro databázi pro vytvoření a odstranění. Viz [Sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Následující možnosti databáze jsou nastaveny nebo přepsány a nelze je změnit později: 

- `NEW_BROKER` Pokud zprostředkovatel není v souboru. bak povolený. 
- `ENABLE_BROKER` Pokud zprostředkovatel není v souboru. bak povolený. 
- `AUTO_CLOSE=OFF` Pokud databáze v souboru. bak obsahuje `AUTO_CLOSE=ON` . 
- `RECOVERY FULL` v případě, že databáze v souboru. bak má nebo je v `SIMPLE` `BULK_LOGGED` režimu obnovení.
- Skupina souborů optimalizované pro paměť se přidá a zavolá XTP, pokud nebyla v souboru source. bak. 
- Stávající paměťově optimalizovaná skupina souborů se přejmenuje na XTP. 
- `SINGLE_USER` a `RESTRICTED_USER` Možnosti jsou převedeny na `MULTI_USER` .

Omezení: 

- Zálohování poškozených databází může být obnoveno v závislosti na typu poškození, ale automatizované zálohování nebude provedeno, dokud nebude poškození opraveno. Ujistěte se, že běží `DBCC CHECKDB` na zdrojové instanci SQL spravované, a pokud `WITH CHECKSUM` chcete tomuto problému zabránit, použijte zálohování.
- Obnovení `.BAK` souboru databáze, která obsahuje jakákoli omezení popsaná v tomto dokumentu (například `FILESTREAM` nebo `FILETABLE` objekty), nelze obnovit ve spravované instanci SQL.
- `.BAK` soubory, které obsahují víc zálohovacích skladů, se nedají obnovit. 
- `.BAK` soubory, které obsahují více souborů protokolu, nelze obnovit.
- Zálohy obsahující databáze větší než 8 TB, aktivní objekty OLTP v paměti nebo počet souborů, které by překročily 280 souborů na instanci, nelze obnovit v instanci Pro obecné účely. 
- Zálohy, které obsahují databáze větší než 4 TB nebo objekty OLTP v paměti s celkovou velikostí větší, než je velikost popsaná v části [omezení prostředků](resource-limits.md) , nelze obnovit v instanci pro důležité obchodní informace.
Informace o příkazech Restore naleznete v tématu [Restore restatements](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Stejné omezení platí pro předdefinovanou operaci obnovení k bodu v čase. Například Pro obecné účely databáze větší než 4 TB nemůže být v instanci Pro důležité obchodní informace obnovena. Pro důležité obchodní informace databáze s OLTP soubory v paměti nebo více než 280 souborů nelze v Pro obecné účely instanci obnovit.

### <a name="service-broker"></a>Služba Service Broker

Služba Service Broker mezi instancemi není podporována:

- `sys.routes`: Jako požadavek musíte vybrat adresu z sys. Routes. Adresa musí být místní v každé trase. Viz [Sys. Routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Nemůžete použít `CREATE ROUTE` s `ADDRESS` jinými než `LOCAL` . Viz [vytvoření trasy](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Nemůžete použít `ALTER ROUTE` s `ADDRESS` jinými než `LOCAL` . Viz [ALTER Route](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Uložené procedury, funkce a triggery

- `NATIVE_COMPILATION` není podporován na Pro obecné účely vrstvě.
- Následující možnosti [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) nejsou podporovány: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` není podporováno. Viz [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` není podporováno. Viz [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` nepodporuje se, což zahrnuje `sp_addextendedproc` a `sp_dropextendedproc` . Viz [rozšířené uložené procedury](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` a `sp_detach_db` nejsou podporovány. Viz [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)a [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Systémové funkce a proměnné

Následující proměnné, funkce a zobrazení vrací různé výsledky:

- `SERVERPROPERTY('EngineEdition')` Vrátí hodnotu 8. Tato vlastnost jednoznačně identifikuje spravovanou instanci SQL. Viz [ServerProperty](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Vrátí hodnotu NULL, protože koncept instance, která existuje pro SQL Server, se nevztahuje na spravovanou instanci SQL. Viz [SERVERPROPERTY (' InstanceName ')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Vrátí úplný název DNS "připojitelné", například my-managed-instance.wcus17662feb9ce98.database.windows.net. Viz [@ @SERVERNAME ](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` Vrátí úplný název DNS "připojitelné", například `myinstance.domain.database.windows.net` pro vlastnosti "název" a "data_source". Viz [Sys. SERVERY](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` Vrátí hodnotu NULL, protože koncept služby, která existuje pro SQL Server, se nevztahuje na spravovanou instanci SQL. Viz [@ @SERVICENAME ](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` je podporováno. Vrátí hodnotu NULL, pokud přihlášení Azure AD není v sys.syspřihlašovacích údajů. Viz [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` není podporováno. Vrátí se nesprávná data, což je dočasný známý problém. Viz [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Omezení prostředí

### <a name="subnet"></a>Podsíť
-  V podsíti, ve které jste nasadili spravovanou instanci SQL, nemůžete umístit žádné další prostředky (například virtuální počítače). Nasaďte tyto prostředky pomocí jiné podsítě.
- Podsíť musí mít dostatečný počet dostupných [IP adres](connectivity-architecture-overview.md#network-requirements). Minimum je 16, ale doporučujeme mít minimálně 32 IP adres v podsíti.
- [Koncové body služby nelze přidružit k podsíti spravované instance SQL](connectivity-architecture-overview.md#network-requirements). Ujistěte se, že je při vytváření virtuální sítě možnost koncové body služby zakázaná.
- Počet virtuální jádra a typů instancí, které můžete nasadit v oblasti, mají některá [omezení a omezení](resource-limits.md#regional-resource-limitations).
- Existují některá [pravidla zabezpečení, která je nutné použít v podsíti](connectivity-architecture-overview.md#network-requirements).

### <a name="vnet"></a>Virtuální síť
- Virtuální síť se dá nasadit pomocí modelu prostředků – model klasický pro virtuální síť se nepodporuje.
- Po vytvoření spravované instance SQL se nepodporují přesunutí spravované instance SQL nebo virtuální sítě do jiné skupiny prostředků nebo předplatného.
- Některé služby, jako jsou App Service prostředí, Logic Apps a SQL Managed instance (používané pro geografickou replikaci, transakční replikaci nebo přes propojené servery), nemají přístup ke spravované instanci SQL v různých oblastech, pokud jsou jejich virtuální sítě připojené pomocí [globálního partnerského vztahu](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). K těmto prostředkům se můžete připojit prostřednictvím ExpressRoute nebo VNet-to-VNet prostřednictvím bran virtuální sítě.

### <a name="failover-groups"></a>Skupiny převzetí služeb při selhání
Systémové databáze nejsou replikovány do sekundární instance ve skupině převzetí služeb při selhání. Proto by scénáře závislé na objektech ze systémových databází nemohly být na sekundární instanci možné, pokud nejsou objekty ručně vytvořeny na sekundárním objektu.

### <a name="tempdb"></a>DATABÁZE
- Maximální velikost souboru `tempdb` nemůže být větší než 24 GB na jádro na úrovni pro obecné účely. Maximální `tempdb` velikost vrstvy pro důležité obchodní informace je omezená velikostí úložiště spravované instance SQL. `Tempdb` velikost souboru protokolu je omezena na 120 GB na úrovni Pro obecné účely. Některé dotazy mohou vracet chybu, pokud vyžadují více než 24 GB na jádro v `tempdb` nebo pokud vydávají více než 120 GB dat protokolu.
- `Tempdb` je vždy rozdělen do 12 datových souborů: 1 primární, označovaný také jako Master, datový soubor a 11 neprimárních datových souborů. Strukturu souborů nelze změnit a nelze do ní přidat nové soubory `tempdb` . 
- [Paměťově optimalizovaná `tempdb` metadata](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15#memory-optimized-tempdb-metadata), nová funkce databáze SQL Server 2019 v paměti, není podporována.
- Objekty vytvořené v databázi modelů nelze automaticky vytvořit v nástroji `tempdb` po restartování nebo převzetí služeb při selhání, protože `tempdb` nezíská svůj počáteční seznam objektů z databáze modelů. Objekty musíte `tempdb` po každém restartování nebo převzetí služeb při selhání vytvořit ručně.

### <a name="msdb"></a>MSDB

Následující schémata MSDB ve spravované instanci SQL musí vlastnit jejich příslušné předdefinované role:

- Obecné role
  - TargetServersRole
- [Pevné databázové role](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - Role uživatele agenta SQL
  - Role čtenáře agenta SQL
  - Role operátora agenta SQL
- [DatabaseMail role](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - DatabaseMailUserRole
- [Role integračních služeb](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15):
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Změna předdefinovaných názvů rolí, názvy schémat a vlastníci schémat budou mít vliv na běžnou činnost služby. Jakékoli změny, které v nich provedete, se vrátí zpět na předdefinované hodnoty, jakmile se zjistí, nebo při další aktualizaci služby na nejnovější verzi, aby se zajistila normální operace služby.

### <a name="error-logs"></a>Protokoly chyb

Spravovaná instance SQL umístí podrobné informace do protokolů chyb. K dispozici je mnoho interních systémových událostí, které jsou zaznamenány v protokolu chyb. Pomocí vlastního postupu si můžete přečíst protokoly chyb, které odfiltrují některé nedůležité položky. Další informace najdete v tématu [spravovaná instance SQL – sp_readmierrorlog](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) nebo [rozšíření spravované instance SQL (preview)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) pro Azure Data Studio.

## <a name="next-steps"></a>Další kroky

- Další informace o spravované instanci SQL najdete v tématu [co je Managed instance SQL?](sql-managed-instance-paas-overview.md)
- Seznam funkcí a porovnání najdete v tématu [porovnání funkcí spravované instance Azure SQL](../database/features-comparison.md).
- Informace o aktualizacích vydaných verzí a známých problémech najdete v tématu [poznámky k verzi spravované instance SQL](../database/doc-changes-updates-release-notes.md) .
- Rychlý Start, který ukazuje, jak vytvořit novou spravovanou instanci SQL, najdete v tématu [Vytvoření spravované instance SQL](instance-create-quickstart.md).
