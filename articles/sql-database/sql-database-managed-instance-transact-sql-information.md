---
title: Databáze Azure SQL spravované Instance T-SQL rozdíly | Microsoft Docs
description: Tento článek popisuje T-SQL rozdíly mezi spravované Instance serveru Azure SQL Database a SQL Server.
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab, bonova
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: 95eca05d695e039f59b71caa4d730f4e1f84fc97
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337944"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL Database spravované Instance T-SQL rozdíly v systému SQL Server 

SQL databáze spravované Instance Azure (preview) poskytuje vysokou kompatibilitu s místními databázového stroje SQL Server. Většina funkcí databázového stroje SQL Server jsou podporovány ve spravované Instance. Vzhledem k tomu, že stále existují určité rozdíly v chování a syntaxe, tento článek shrnuje a popisuje tyto rozdíly.
 - [Rozdíly T-SQL a nepodporované funkce](#Differences)
 - [Funkce, které mají různé chování v spravované Instance](#Changes)
 - [Dočasné omezení a známé problémy](#Issues)

## <a name="Differences"></a> T-SQL rozdíly v systému SQL Server 

Tento oddíl shrnuje hlavní rozdíly v syntaxi T-SQL a chování mezi spravované Instance a místní databázového stroje SQL Server, jakož i nepodporované funkce.

### <a name="always-on-availability"></a>Always On dostupnosti

[Vysoká dostupnost](sql-database-high-availability.md) je integrována do spravované Instance a nejde kontrolovat uživatelé. Nejsou podporovány následující příkazy:
 - [VYTVOŘENÍ KONCOVÉHO BODU... PRO DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
 - [VYTVOŘENÍ SKUPINY DOSTUPNOSTI](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
 - [PŘÍKAZ ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
 - [VYŘAZENÍ SKUPINY DOSTUPNOSTI](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
 - [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) klauzuli [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) – příkaz

### <a name="auditing"></a>Auditování 
 
Hlavní rozdíly mezi SQL auditu v spravované Instance, Azure SQL Database a SQL Server v místním jsou:
- Ve spravované Instance SQL Audit pracuje na úrovni serveru a úložišť `.xel` soubory v Azure blob účet úložiště.  
- V databázi SQL Azure SQL Audit funguje na úrovni databáze.
- V systému SQL Server na místě / virtuálního počítače SQL Audit funguje na úrovni serveru, ale uloží události na protokoly událostí systémové soubory.  
  
XEvent auditování ve spravované Instance podporuje cíle úložiště objektů blob v Azure. Protokoly souboru a windows nejsou podporovány.    
 
Klíč rozdíly v `CREATE AUDIT` syntaxe pro auditování do Azure blob storage jsou:
- Nové syntaxe `TO URL` je k dispozici a umožňuje zadat adresu URL kontejneru objektů blob v Azure Storage, kde `.xel` budou umístěné soubory 
- Syntaxe `TO FILE` není podporována, protože spravované Instance nelze přistupovat ke sdíleným složkám systému Windows. 
 
Další informace naleznete v tématu:  
- [VYTVOŘIT SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [PŘÍKAZ ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql) 
- [Auditování](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)     

### <a name="backup"></a>Backup 

Spravované Instance má automatické zálohování a umožňuje uživatelům vytvářet úplné databáze `COPY_ONLY` zálohy. Rozdíl, log a zálohy snímků souborů nejsou podporovány.  
- Spravované Instance můžete zálohovat databázi pouze k účtu Azure Blob Storage: 
 - Pouze `BACKUP TO URL` je podporováno 
 - `FILE`, `TAPE`, a nejsou podporovány zálohovací zařízení  
- Většina Obecné `WITH` možnosti jsou podporovány. 
 - `COPY_ONLY` je povinné.
 - `FILE_SNAPSHOT` Nepodporuje se  
 - Možnosti pásky: `REWIND`, `NOREWIND`, `UNLOAD`, a `NOUNLOAD` nejsou podporovány 
 - Možnosti specifické pro protokol: `NORECOVERY`, `STANDBY`, a `NO_TRUNCATE` nejsou podporovány 
 
Omezení:  
- Spravované Instance může zálohovat databázi zálohy s až 32 rozděluje, která je dost pro databáze až 4 TB, pokud se používá kompresi zálohy.
- Maximální velikost zálohování stripe je 195 GB (maximální velikost objektu blob). Zvyšte počet rozděluje v zálohování příkazu ke snížení velikosti jednotlivých stripe zůstat v rámci tohoto limitu. 

> [!TIP]
> Chcete-li vyřešit tento omezení na místních počítačích zálohování na `DISK` místo zálohování na `URL`, nahrát záložní soubor do objektu blob a potom obnovit. Podporuje větší soubory obnovte, protože typ jiný objektu blob se používá.  

Informace o zálohování pomocí T-SQL najdete v tématu [zálohování](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

### <a name="buffer-pool-extension"></a>Rozšíření fondu vyrovnávací paměti 
 
- [Rozšíření fondu vyrovnávací paměti](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) není podporován.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` není podporována. V tématu [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql). 
 
### <a name="bulk-insert--openrowset"></a>Příkaz Bulk insert nebo openrowset

Spravované Instance nelze přístup sdílené složky a složky, Windows, aby soubory musí být import z Azure blob storage.
- `DATASOURCE` je nutné v `BULK INSERT` příkazu při importu souborů z Azure blob storage. V tématu [příkaz BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` je nutné v `OPENROWSET` funkce při čtení obsahu souboru z Azure blob storage. V tématu [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
 
### <a name="certificates"></a>Certifikáty 

Spravované Instance se nejde dostat sdílené složky a složky systému Windows, platí následující omezení: 
- `CREATE FROM`/`BACKUP TO` soubor není podporován pro certifikáty
- `CREATE`/`BACKUP` certifikát z `FILE` / `ASSEMBLY` není podporován. Soubory privátních klíčů nelze použít.  
 
V tématu [vytvořit certifikát](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) a [zálohování certifikátu](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
> [!TIP]
> Alternativní řešení: skript certifikátu a privátního klíče, uložte jako soubor .sql a vytvořit z binárního souboru: 
> 
> ``` 
CREATE CERTIFICATE  
 FROM BINARY = asn_encoded_certificate    
WITH PRIVATE KEY ( <private_key_options> ) 
>```   
 
### <a name="clr"></a>CLR 

Spravované Instance se nejde dostat sdílené složky a složky systému Windows, platí následující omezení: 
- Pouze `CREATE ASSEMBLY FROM BINARY` je podporována. V tématu [vytvořit sestavení z BINÁRNÍHO](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` není podporována. V tématu [vytvořit sestavení ze souboru](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` nesmí odkazovat na soubory. V tématu [změna sestavení](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).
 
### <a name="compatibility-levels"></a>Úrovně kompatibility 
 
- Úrovně kompatibility podporované jsou: 100, 110, 120, 130, 140  
- Úrovně kompatibility nižší než 100 nejsou podporovány. 
- Výchozí úroveň kompatibility pro nové databáze je 140. Pro obnovené databáze zůstanou nezměněna úroveň kompatibility i pokud se jednalo o 100 a vyšší.

V tématu [úroveň kompatibility databáze ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
 
### <a name="credential"></a>Přihlašovací údaj 
 
Jenom Azure Key Vault a `SHARED ACCESS SIGNATURE` identity jsou podporovány. Uživatelé systému Windows nejsou podporovány.
 
V tématu [vytvořit pověření](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) a [ALTER pověření](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql). 
 
### <a name="cryptographic-providers"></a>Zprostředkovatelů kryptografických služeb

Spravované Instance nelze získat přístup k souborům, takže zprostředkovatelů kryptografických služeb nelze vytvořit:
- `CREATE CRYPTOGRAPHIC PROVIDER` není podporována. V tématu [zprostředkovatele KRYPTOGRAFICKÝCH služeb vytvořit](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` není podporována. V tématu [zprostředkovatele KRYPTOGRAFICKÝCH služeb ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql). 

### <a name="collation"></a>Kolace 
 
Kolace serveru `SQL_Latin1_General_CP1_CI_AS` a nelze je změnit. V tématu [kolace](https://docs.microsoft.com/sql/t-sql/statements/collations).
 
### <a name="database-options"></a>Možnosti databáze 
 
- Více souborů protokolu není podporováno. 
- Objekty v paměti nejsou podporovány ve vrstvě služeb obecné účely.  
- Existuje omezení 280 souborů na jednu instanci zdání maximální 280 souborů v databázi. Soubory protokolu a data, se počítají do tohoto limitu.  
- Databáze nesmí obsahovat skupiny souborů, který obsahuje filestream data.  Obnovení se nezdaří, pokud obsahuje .bak `FILESTREAM` data.  
- Každý soubor je umístěn v Azure Premium storage. Vstupně-výstupních operací a propustnosti na soubor závisí na velikost každého jednotlivého souboru stejným způsobem jako u disků Azure Premium Storage. V tématu [výkon disku Azure Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)  
 
#### <a name="create-database-statement"></a>Příkaz CREATE DATABASE

Následují `CREATE DATABASE` omezení: 
- Soubory a skupiny souborů nelze definovat.  
- `CONTAINMENT` možnost není podporována.  
- `WITH`možnosti nejsou podporovány.  
   > [!TIP]
   > Jako alternativní řešení, použít `ALTER DATABASE` po `CREATE DATABASE` nastavit možnosti databáze pro přidání souborů nebo nastavit členství ve skupině.  

- `FOR ATTACH` možnost nepodporuje. 
- `AS SNAPSHOT OF` možnost nepodporuje. 

Další informace najdete v tématu [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Příkaz ALTER DATABASE

Některé vlastnosti souboru nelze nastavit nebo změnit:
- Cesta k souboru nelze zadat v `ALTER DATABASE ADD FILE (FILENAME='path')` příkaz T-SQL. Odebrat `FILENAME` ze skriptu protože spravované Instance automaticky umístí soubory.  
- Název souboru nelze změnit pomocí `ALTER DATABASE` příkaz.

Tyto možnosti jsou ve výchozím nastavení a nedá se změnit: 
- `MULTI_USER` 
- `ENABLE_BROKER ON` 
- `AUTO_CLOSE OFF` 

Nemůže být upraven následující možnosti: 
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

Upravit název není podporován.

Další informace najdete v tématu [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="database-mirroring"></a>Zrcadlení databáze

Zrcadlení databáze není podporována.
 - `ALTER DATABASE SET PARTNER` a `SET WITNESS` možnosti nejsou podporovány.
 - `CREATE ENDPOINT … FOR DATABASE_MIRRORING` není podporována.

Další informace najdete v tématu [ALTER DATABASE SET PARTNER a SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) a [CREATE ENDPOINT... PRO DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="dbcc"></a>DBCC 
 
Nezdokumentovaný DBCC příkazy, které jsou povolené v systému SQL Server nepodporuje spravované Instance.
- `Trace Flags` nejsou podporovány. V tématu [příznaky trasování](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` není podporována. V tématu [příkaz DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` není podporována. V tématu [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Distribuované transakce

Ani jedna služba MSDTC ani [elastické transakce](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-transactions-overview) jsou aktuálně podporovány ve spravované Instance.

### <a name="extended-events"></a>Rozšířené události 

Některé specifické pro systém Windows cíle pro události Xevent nepodporuje:
- `etw_classic_sync target` není podporována. Úložiště `.xel` soubory v Azure blob storage. V tématu [etw_classic_sync cíl](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etwclassicsynctarget-target). 
- `event_file target`není podporována. Úložiště `.xel` soubory v Azure blob storage. V tématu [event_file cíl](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#eventfile-target).

### <a name="external-libraries"></a>Externí knihovny

V databázi R a Python externí knihovny se ještě nepodporují. V tématu [SQL serveru strojového učení služby](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream a objektu Filetable

- FileStream data není podporována. 
- Databáze nesmí obsahovat skupiny souborů s `FILESTREAM` dat
- `FILETABLE` není podporováno
- Tabulky nemůže mít `FILESTREAM` typy
- Nejsou podporovány následující funkce:
 - `GetPathLocator()` 
 - `GET_FILESTREAM_TRANSACTION_CONTEXT()` 
 - `PathName()` 
 - `GetFileNamespacePath()` 
 - `FileTableRootPath()` 

Další informace najdete v tématu [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) a [Filetable](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Sémantické fulltextové vyhledávání

[Sémantické vyhledávání](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) není podporován.

### <a name="linked-servers"></a>Propojené servery
 
Odkazované servery ve spravované Instance podporují omezený počet cíle: 
- Podporované cíle: SQL Server a databáze SQL
- Není podporováno cíle: soubory, služby Analysis Services a dalších relační.

Operace

- Zápis instance mezi transakce nejsou podporovány.
- `sp_dropserver` je podporován pro vyřazení odkazovaný server. V tématu [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET` funkce slouží k provádění dotazů pouze na instance systému SQL Server (buď spravované, místně nebo ve virtuálních počítačích). V tématu [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE` funkce slouží k provádění dotazů pouze na instance systému SQL Server (buď spravované, místně nebo ve virtuálních počítačích). Pouze `SQLNCLI`, `SQLNCLI11`, a `SQLOLEDB` hodnoty jsou podporovány jako zprostředkovatel. Například: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. V tématu [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).
 
### <a name="logins--users"></a>Přihlášení / uživatelé 

- Vytvoření přihlášení SQL `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, a `FROM SID` jsou podporovány. V tématu [vytvořit přihlášení](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Přihlášení systému Windows, které jsou vytvořené pomocí `CREATE LOGIN ... FROM WINDOWS` syntaxe nejsou podporovány.
- Azure Active Directory (Azure AD) uživatele, který vytvořil instanci má [neomezený oprávnění správce](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#unrestricted-administrative-accounts).
- Uživatelé úroveň databáze Neadministrátorský účet služby Azure Active Directory (Azure AD) můžete vytvořit pomocí `CREATE USER ... FROM EXTERNAL PROVIDER` syntaxe. V tématu [vytvořit uživatele... Z EXTERNÍHO POSKYTOVATELE](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users)
 
### <a name="polybase"></a>Polybase

Odkazování na soubory v HDFS nebo Azure blob storage externí tabulky nejsou podporovány. Informace o používání funkce Polybase najdete v tématu [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikace 
 
Replikace se ještě nepodporuje. Informace o replikaci najdete v tématu [replikace SQL serveru](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication).
 
### <a name="restore-statement"></a>OBNOVENÍ – příkaz 
 
- Podporované syntaxe  
   - `RESTORE DATABASE` 
   - `RESTORE FILELISTONLY ONLY` 
   - `RESTORE HEADER ONLY` 
   - `RESTORE LABELONLY ONLY` 
   - `RESTORE VERIFYONLY ONLY` 
- Nepodporované syntaxe 
   - `RESTORE LOG ONLY` 
   - `RESTORE REWINDONLY ONLY`
- Zdroj  
 - `FROM URL` (Úložiště objektů blob v azure) je pouze podporované možnosti.
 - `FROM DISK`/`TAPE`/ zálohovací zařízení není podporován.
 - Zálohovací sklady nejsou podporovány. 
- `WITH` možnosti nejsou podporovány (ne `DIFFERENTIAL`, `STATS`atd.)     
- `ASYNC RESTORE` -Obnovení pokračovat i v případě, že dělí připojení klienta. Pokud vaše připojení se ukončí, můžete zkontrolovat `sys.dm_operation_status` zobrazení stavu operace obnovení (a také vytvořit a vyřazení databáze). V tématu [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  
 
Následující možnosti databáze jsou sady nebo přepsat a není možné později změnit:  
- `NEW_BROKER` (Pokud broker není povolena v souboru .bak)  
- `ENABLE_BROKER` (Pokud broker není povolena v souboru .bak)  
- `AUTO_CLOSE=OFF` (Pokud je databáze v souboru .bak `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (Pokud je databáze v souboru .bak `SIMPLE` nebo `BULK_LOGGED` režimu obnovení)
- Paměťově optimalizovaných souborů a je volána XTP, pokud nebyla zdrojový soubor .bak  
- Všechny existující paměťově optimalizovaných souborů je přejmenován na XTP  
- `SINGLE_USER` a `RESTRICTED_USER` možnosti jsou převedeny na `MULTI_USER`   
Omezení:  
- `.BAK` nelze obnovit soubory obsahující více zálohovací sklady. 
- `.BAK` nelze obnovit soubory obsahující více souborů protokolu. 
- Obnovení se nezdaří, pokud obsahuje .bak `FILESTREAM` data.
- Zálohy obsahující databáze, které mají aktivní v paměti objekty nelze nyní obnovit.  
- Zálohování obsahující databáze, kde v určitém okamžiku existovaly objektům v paměti nelze nyní obnovit.   
- Zálohování obsahující databáze v režimu jen pro čtení nelze nyní obnovit. Toto omezení se odeberou brzy k dispozici.   
 
Informace o příkazech obnovení najdete v tématu [obnovit příkazy](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Služba Service broker 
 
- Mezi instance služba service broker není podporován. 
 - `sys.routes` -Předpoklad: Vyberte adresu z sys.routes. Adresa musí být místní na každý trasy. V tématu [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
 - `CREATE ROUTE` -Nemůžete `CREATE ROUTE` s `ADDRESS` jiné než `LOCAL`. V tématu [vytvořit TRASU](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
 - `ALTER ROUTE` nelze `ALTER ROUTE` s `ADDRESS` jiné než `LOCAL`. V tématu [ALTER TRASY](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  
 
### <a name="service-key-and-service-master-key"></a>Služby a klíče hlavní klíč služby 
 
- [Zálohování hlavního klíče](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) nepodporuje (spravované službou SQL Database) 
- [Obnovení hlavního klíče](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) nepodporuje (spravované službou SQL Database) 
- [Zálohování hlavního klíče služby](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) nepodporuje (spravované službou SQL Database) 
- [Obnovení hlavního klíče služby](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) nepodporuje (spravované službou SQL Database) 
 
### <a name="stored-procedures-functions-triggers"></a>Uložené procedury, funkce, aktivační události 
 
- `NATIVE_COMPILATION` není aktuálně podporováno. 
- Následující [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) možnosti nejsou podporovány: 
 - `allow polybase export` 
 - `allow updates` 
 - `filestream_access_level` 
 - `max text repl size` 
 - `remote data archive` 
 - `remote proc trans` 
- `sp_execute_external_scripts` není podporována. V tématu [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` není podporována. V tématu [procedury xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` nejsou podporovány, včetně `sp_addextendedproc` a `sp_dropextendedproc`. V tématu [rozšířené uložené procedury](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- `sp_attach_db`, `sp_attach_single_file_db`, a `sp_detach_db` nejsou podporovány. V tématu [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), a [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).
- `sp_renamedb` není podporována. V tématu [sp_renamedb](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-renamedb-transact-sql).

### <a name="sql-server-agent"></a>Agent SQL Server 
 
- Nastavení agenta SQL jsou jen pro čtení. Postup `sp_set_agent_properties` není podporována v spravované Instance.  
- Úlohy - pouze kroky úlohy T-SQL jsou aktuálně podporované (Další kroky se přidají verzi public Preview).
 - SSIS není dosud podporováno. 
 - Replikace se ještě nepodporuje.  
  - Čtečky protokolů transakcí není dosud podporováno.  
  - Snímek se ještě nepodporuje.  
  - Distributor není dosud podporováno.  
  - Sloučení není podporována.  
  - Čtečky fronty není podporována.  
 - Příkazové prostředí není dosud podporováno. 
  - Spravované Instance nemají přístup k externím prostředkům (například sdílené síťové složky prostřednictvím nástroje robocopy).  
 - Prostředí PowerShell není dosud podporováno.
 - Služba Analysis Services nejsou podporována.  
- Oznámení jsou podporovány jen částečně.
 - E-mailových oznámení je podporováno, vyžaduje konfiguraci profil databázového e-mailu. Může existovat pouze jedna databáze profil e-mailu a musí být voláno `AzureManagedInstance_dbmail_profile` ve verzi public preview (dočasné omezení).  
 - Pager není podporována.  
 - Příkazu není podporována. 
 - Výstrahy nejsou zatím podporovány.
 - Servery proxy nepodporují.  
- Protokol událostí není podporována. 
 
Následující funkce aktuálně nejsou podporované, ale bude povolená v budoucnosti:  
- Proxy
- Plánování úloh na nečinnosti procesoru 
- Povolení nebo zákaz agenta
- Výstrahy

Informace o agenta systému SQL Server najdete v tématu [agenta systému SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).
 
### <a name="tables"></a>Tabulky 

Nejsou podporovány následující: 
- `FILESTREAM` 
- `FILETABLE` 
- `EXTERNAL TABLE` 
- `MEMORY_OPTIMIZED`  

Informace o vytváření a změny tabulek najdete v tématu [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) a [příkaz ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).
 
## <a name="Changes"></a> Změny chování 
 
Následující proměnné, funkce a zobrazení vrátí odlišné výsledky:  
- `SERVERPROPERTY('EngineEdition')` Vrátí hodnotu 8. Tato vlastnost jednoznačně identifikuje spravované Instance. V tématu [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Vrátí hodnotu NULL, protože koncept instance, protože existuje pro nevztahuje se na spravované Instance systému SQL Server. V tématu [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Vrátí úplný, umožňující připojení, název DNS, například Moje instance.wcus17662feb9ce98.database.windows.net spravované. V tématu [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -Vrátí úplné, umožňující připojení, název DNS, jako například `myinstance.domain.database.windows.net` pro vlastnosti 'name' a 'data_source'. V tématu [SYS. SERVERY](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql). 
- `@@SERVICENAME` Vrátí hodnotu NULL, protože koncept služby, protože existuje pro nevztahuje se na spravované Instance systému SQL Server. V tématu [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).   
- `SUSER_ID` je podporováno. Vrátí hodnotu NULL, pokud není v sys.syslogins AAD přihlášení. V tématu [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` není podporována. Vrátí chybná data (dočasný známý problém). V tématu [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 
- `GETDATE()` a další funkce integrované datum a čas vždy vrátí čas v časovém pásmu UTC. V tématu [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a> Známé problémy a omezení

### <a name="tempdb-size"></a>Velikost databáze TEMPDB

`tempdb` je rozdělená do 12 soubory s maximální velikost 14 GB na soubor. Nelze změnit této maximální velikosti na soubor a nové soubory nelze přidat do `tempdb`. Toto omezení se odeberou brzy k dispozici. Některé dotazy může způsobit chybu, pokud potřebují více než 168 GB `tempdb`.

### <a name="exceeding-storage-space-with-small-database-files"></a>Překročení prostoru úložiště s malou databází soubory

Každá Instance spravované musel vyhrazená místa na disku Azure Premium storage 35 TB a každý databázový soubor je umístěn na jiném fyzickém disku. Velikosti disků může být 128 GB, 256 GB, 512 GB, 1 TB nebo 4 TB. Není účtován nevyužité místo na disku, ale celkového součtu velikostí disku Azure Premium nesmí být delší než 35 TB. V některých případech může překročit spravované Instance, který nemusí 8 TB celkem 35 TB Azure limit velikost úložiště, z důvodu vnitřní fragmentace. 

Například do spravované Instance může mít jeden soubor s velikostí 1.2 TB, který používá disk 4 TB a 248 soubory s 1 GB, každý, které jsou umístěny na 248 disky o velikosti 128 GB. V tomto příkladu je velikost úložiště disku celkem 1 x 4 TB + 248 × 128 GB = 35 TB. Celkový počet vyhrazenou instanci velikost databází je však 1 x 1.2 TB + 248 1 GB = 1,4 TB. To ukazuje, že za určitých okolností, z důvodu velmi konkrétní distribuční souborů, může do spravované Instance dosažení limit úložiště Azure Premium disku, kde nemusí být jeho. 

Na existující databáze by se žádná chyba a můžou růst bez jakékoli potíže, pokud nejsou přidány nové soubory, ale nové databáze nelze vytvořit nebo obnovit, protože není dostatek místa pro nové diskové jednotky, i když celková velikost všech databází nedorazily t zadá instanci omezení velikosti. Chyba, která je vrácena v takovém případě není zrušte.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Nesprávná konfigurace klíče SAS během databáze obnovení

`RESTORE DATABASE` který čte soubor .bak může být neustále pokus o čtení soubor .bak a chybovému po dlouhou dobu, pokud sdíleného přístupového podpisu v `CREDENTIAL` je nesprávný. Spusťte obnovení HEADERONLY před obnovením databáze do Ujistěte se, zda je správný klíč SAS.
Ujistěte se, že odeberete úvodní `?` z SAS klíč, který generuje pomocí portálu Azure.

### <a name="tooling"></a>Nástrojů

SQL Server Management Studio a SQL Server Data Tools může mít některé problémy při přístupu k spravované Instance. Všechny problémy s nástroji budou řešeny před obecné dostupnosti.

### <a name="incorrect-database-names"></a>Názvy nesprávný databází

Spravované Instance může zobrazovat hodnota identifikátoru guid místo názvu databáze během obnovení nebo v některé chybové zprávy. Tyto problémy budou opraveny před obecné dostupnosti.

### <a name="database-mail-profile"></a>Profil databázové pošty
Může existovat pouze jedna databáze profil e-mailu a musí být voláno `AzureManagedInstance_dbmail_profile`. Jedná se o dočasné omezení, který bude brzy odebrán.

## <a name="next-steps"></a>Další postup

- Podrobnosti o spravovaných instanci najdete v tématu [co je spravované Instance?](sql-database-managed-instance.md)
- Pro funkce a porovnání seznamu, najdete v části [společné funkce SQL](sql-database-features.md).
- Kurz ukazuje, jak vytvořit novou instanci spravované, najdete v části [vytvoření Instance spravované](sql-database-managed-instance-create-tutorial-portal.md).
