---
title: Migrace databází do spravované instance SQL pomocí služby pro opětovné přehrání protokolů
description: Naučte se migrovat databáze z SQL Server do spravované instance SQL pomocí služby opětovného přehrání protokolů.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 02/23/2021
ms.openlocfilehash: 73963763716d7e18b757b5ade8998f23cc589fdb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661354"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>Migrace databází z SQL Server do spravované instance SQL pomocí služby opětovného přehrání protokolů
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek vysvětluje, jak ručně nakonfigurovat migraci databáze z SQL Server 2008-2019 do spravované instance SQL pomocí služby log Replay (LRS). Toto je cloudová služba povolená pro spravovanou instanci na základě technologie SQL Serverho přenosu protokolů. LRS by se mělo použít v případech, kdy se služba Azure Data Migration Service (DMS) nedá použít, když je potřeba více ovládacích prvků, nebo když existuje nízká tolerance pro výpadky.

## <a name="when-to-use-log-replay-service"></a>Kdy použít službu opětovného přehrání protokolu

V případech, kdy se [Azure DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) nedá použít pro migraci, se dá cloudová služba LRS použít přímo s PowerShellem, rutinami CLI nebo rozhraním API, aby bylo možné ručně vytvořit a orchestrovat migrace databáze do spravované instance SQL. 

Možná budete chtít zvážit použití cloudové služby LRS v některém z těchto případů:
- Pro váš projekt migrace databáze je potřeba více ovládacích prvků.
- U přímou migraci migrace existuje nízká tolerance.
- Spustitelný soubor DMS nejde nainstalovat do vašeho prostředí.
- Spustitelný soubor DMS nemá přístup k souborům pro zálohy databáze.
- K dispozici není žádný přístup k hostitelskému operačnímu systému nebo žádná oprávnění správce.
- Nepovedlo se otevřít síťové porty z vašeho prostředí do Azure.

> [!NOTE]
> Doporučený automatizovaný způsob migrace databází z SQL Server do spravované instance SQL používá službu Azure DMS. Tato služba používá stejnou cloudovou službu LRS v back-endu s přenosem protokolů v režimu NORECOVERY. Měli byste zvážit ruční použití LRS k orchestraci migrace v případech, kdy Azure DMS neplně podporuje vaše scénáře.

## <a name="how-does-it-work"></a>Jak to funguje?

Vytvoření vlastního řešení s použitím LRS k migraci databází do cloudu vyžaduje několik kroků orchestrace zobrazených v diagramu a popsaných v následující tabulce.

Migrace se skládá z toho, aby se na SQL Server s povoleným KONTROLNÍm SOUČTem a kopírováním záložních souborů do Azure Blob Storage provádělo úplné zálohování databáze. LRS se používá k obnovení záložních souborů z Azure Blob Storage do spravované instance SQL. Azure Blob Storage slouží jako zprostředkující úložiště mezi SQL Server a spravovanou instancí SQL.

LRS bude monitorovat službu Azure Blob Storage pro všechny nové rozdíly nebo zálohy protokolů přidané po obnovení úplného zálohování a automaticky obnoví všechny přidané nové soubory. Průběh zálohování zálohovaných souborů na spravované instanci SQL je možné monitorovat pomocí služby a proces může být v případě potřeby také přerušen.

LRS nevyžaduje konkrétní konvenci pojmenování záložních souborů, protože kontroluje všechny soubory umístěné v Azure Blob Storage a vytvoří řetězec zálohování z čtení pouze hlaviček souboru. Databáze jsou ve stavu "obnovení" během procesu migrace, protože jsou obnoveny v režimu [NORECOVERY](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=sql-server-ver15#comparison-of-recovery-and-norecovery) a nelze je použít pro čtení nebo zápis do úplného dokončení procesu migrace. 

Při migraci několika databází je nutné zálohy pro každou databázi umístit do samostatné složky v Azure Blob Storage. LRS se musí spustit samostatně pro každou databázi a musí se zadat jiné cesty pro oddělené složky Azure Blob Storage. 

LRS lze spustit v režimu automatického dokončování nebo nepřetržitě. Po spuštění v režimu automatického dokončování se migrace automaticky dokončí, až se obnoví poslední zadaný název záložního souboru. Při spuštění v režimu průběžné obnovování služby průběžně obnoví všechny nově přidané záložní soubory a migrace se dokončí jenom na ruční přímou migraci. Doporučuje se, aby se ruční přímou migraci prováděl až po pořízení finální zálohy protokolu, která se zobrazuje jako obnovená na spravované instanci SQL. Poslední krok přímou migraci zajistí, že databáze bude online a bude dostupná pro čtení a zápis na spravované instanci SQL.

Jakmile se LRS zastaví automaticky při automatickém dokončování nebo ručně v přímou migraci, proces obnovení se nedá obnovit pro databázi, která se napravila online na spravované instanci SQL. Aby bylo možné obnovit další záložní soubory po dokončení migrace prostřednictvím automatického dokončování, nebo ručně v přímou migraci, je nutné databázi odstranit a celý řetěz pro zálohování je nutné obnovit od nuly restartováním LRS.

  ![Vysvětlení kroků orchestrace služby pro opětovné přehrání protokolu pro spravovanou instanci SQL](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Operace | Podrobnosti |
| :----------------------------- | :------------------------- |
| **1. Zkopírujte zálohy databáze z SQL Server do Azure Blob Storage**. | – Kopírování úplných, rozdílových a protokolových záloh z SQL Server do služby Azure Blob Storage Container pomocí [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)nebo [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/). <br />– Použijte všechny názvy souborů, protože LRS nevyžaduje konkrétní konvenci pojmenovávání souborů.<br />– Při migraci několika databází se pro každou databázi vyžaduje samostatná složka. |
| **2. Spusťte službu LRS v cloudu**. | -Služba může být spuštěná s volbou rutin: <br /> Spuštění PowerShellu [– azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> [Rutiny AZ_SQL_MIDB_LOG_REPLAY_START](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)CLI. <br /> – Spusťte LRS samostatně pro každou jinou databázi, která odkazuje na jinou složku zálohy v Azure Blob Storage. <br />-Po spuštění bude služba přebírat zálohy z kontejneru Azure Blob Storage a začne je obnovovat na spravované instanci SQL.<br /> -V případě, že LRS bylo spuštěno v nepřetržitém režimu, po obnovení všech původně nahraných záloh bude služba sledovat všechny nové soubory nahrané do složky a bude průběžně používat protokoly na základě řetězce LSN, dokud nebude služba zastavena. |
| **2,1. Sledujte průběh operace**. | -Průběh operace obnovení je možné monitorovat pomocí volby rutiny nebo: <br /> PowerShell [Get – azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> [Rutiny AZ_SQL_MIDB_LOG_REPLAY_SHOW](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)CLI. |
| **2,2. v případě potřeby Stop\abort operaci**. | – V případě, že je potřeba proces migrace přerušit, můžete operaci zastavit s volbou rutin: <br /> PowerShell [stop – azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> Rutiny [AZ_SQL_MIDB_LOG_REPLAY_STOP](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) CLI. <br /><br />– To způsobí odstranění databáze, která se obnovuje na spravované instanci SQL. <br />-Po zastavení nelze LRS pro databázi obnovit. Proces migrace se musí od začátku restartovat. |
| **3. přímou migraci do cloudu, když je připravený**. | – Jakmile se všechny zálohy obnovily do spravované instance SQL, dokončete přímou migraci tím, že zahájíte operaci LRS Complete s volbou rutin: <br />PowerShell [dokončen – azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> Rutiny [AZ_SQL_MIDB_LOG_REPLAY_COMPLETE](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) CLI. <br /><br />– To způsobí zastavení služby LRS a připojení databáze do režimu online pro čtení a zápis na spravované instanci SQL.<br /> -Přesměruje připojovací řetězec aplikace z SQL Server na spravovanou instanci SQL. |

## <a name="requirements-for-getting-started"></a>Požadavky na Začínáme

### <a name="sql-server-side"></a>SQL Server strana
- SQL Server 2008-2019
- Úplná záloha databází (jeden nebo více souborů)
- Rozdílové zálohování (jeden nebo víc souborů)
- Záloha protokolu (není rozdělená do souboru protokolu transakce)
- Pro zálohy **musí být povolený kontrolní součet** (povinné).

### <a name="azure-side"></a>Strana Azure
- PowerShell AZ. SQL Module verze 2.16.0 nebo novější ([nainstalujte](https://www.powershellgallery.com/packages/Az.Sql/)nebo použijte Azure [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/))
- Rozhraní CLI verze 2.19.0 nebo novější ([install](https://docs.microsoft.com/cli/azure/install-azure-cli))
- Zřízený kontejner Azure Blob Storage
- Token zabezpečení SAS s oprávněními **jen pro čtení** a **seznamem** je vygenerovaný jenom pro kontejner úložiště objektů BLOB.

### <a name="migrating-multiple-databases"></a>Migrace více databází
- Záložní soubory pro různé databáze se musí umístit do samostatných složek v Azure Blob Storage.
- LRS se musí spustit samostatně pro každou databázi, která odkazuje na příslušnou složku na Azure Blob Storage.
- LRS může podporovat až 100 simultánních procesů obnovení na jednu spravovanou instanci SQL.

### <a name="azure-rbac-permissions-required"></a>Vyžaduje se oprávnění Azure RBAC.
Provádění LRS prostřednictvím poskytnutých klientů vyžaduje jednu z následujících rolí Azure:
- Role vlastníka předplatného nebo
- Role [přispěvatele spravované instance](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) nebo
- Vlastní role s následujícím oprávněním:
  - `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Osvědčené postupy

Následující doporučené postupy jsou velmi doporučené:
- Spusťte [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) k ověření, jestli jsou vaše databáze připravené k migraci do spravované instance SQL. 
- Oddělte úplné a rozdílové zálohy na více souborů místo jediného souboru.
- Povolte komprimaci zálohování.
- Pomocí Cloud Shell můžete spouštět skripty, protože se vždycky aktualizují na nejnovější vydané rutiny.
- Naplánujte dokončení migrace během 47 hodin od spuštění služby LRS. Toto je doba odkladu zabraňující softwarovým opravám spravovaným po spuštění LRS.

> [!IMPORTANT]
> - Databázi, která se obnovuje pomocí LRS, se nedá použít, dokud se proces migrace nedokončí. Je to proto, že základní technologie se obnoví v NORECOVERY režimu.
> - Režim obnovení v POHOTOVOSTNÍm režimu umožňující přístup k databázím jen pro čtení v průběhu migrace není v LRS podporován z důvodu rozdílů verzí mezi spravovanou instancí SQL a místními servery SQL.
> - Po dokončení migrace buď prostřednictvím automatického dokončování, nebo ručního přímou migraci, se proces migrace dokončuje, protože LRS nepodporuje obnovení obnovit.

## <a name="steps-to-execute"></a>Kroky ke spuštění

### <a name="make-backups-on-the-sql-server"></a>Vytvoření zálohy na SQL Server

Zálohy na SQL Server lze provést pomocí některé z následujících dvou možností:

- Proveďte zálohování na místní diskové úložiště a pak nahrajte soubory do Azure Blob Storage v případě, že vaše prostředí má omezující přímé zálohování do Azure Blob Storage.
- Zálohování přímo do Azure Blob Storage s možností "na adresu URL" v T-SQL, pro případ, že vám vaše prostředí a postupy zabezpečení umožňují. 

Nastavte databáze, které chcete migrovat do režimu úplného obnovení, aby se povolily zálohy protokolů.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery model.
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Chcete-li ručně vytvořit úplnou, rozdílovou a protokolovou zálohu databáze v místním úložišti, použijte níže uvedené ukázkové skripty T-SQL. Ujistěte se, že je povolená možnost KONTROLNÍho SOUČTu, protože se jedná o povinný požadavek pro LRS.

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full_14_43.bak',
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff_14_44.bak',
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the log backup
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log_14_45.bak',
WITH CHECKSUM
GO
```

Soubory zálohované do místního úložiště se budou muset nahrát do Blob Storage Azure. V případě, že podniková zásada to umožňuje, můžete alternativní způsob, jak provádět zálohování přímo do Azure Blob Storage, zdokumentovat v následujícím kurzu: [použití služby Azure Blob Storage s SQL Server](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). Pokud používáte tento alternativní přístup, zajistěte, aby se všechny zálohy vytvořily s povolenou možností KONTROLNÍho SOUČTu.

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Kopírování záloh z SQL Server do Azure Blob Storage

Některé z následujících přístupů je možné využít k nahrávání záloh do úložiště objektů BLOB v části migrace databází do spravované instance pomocí LRS:
- Použití funkce SQL Server nativního [zálohování na adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) .
- Pomocí [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)nebo [Průzkumník služby Azure Storage](https://azure.microsoft.com/en-us/features/storage-explorer) nahrávání záloh do kontejneru objektů BLOB.
- Použití Průzkumník služby Storage v Azure Portal.

### <a name="create-azure-blob-and-sas-authentication-token"></a>Vytvoření ověřovacího tokenu Azure Blob a SAS

Služba Azure Blob Storage slouží jako zprostředkující úložiště pro záložní soubory mezi SQL Server a SQL Managed instance. Pomocí těchto kroků vytvořte kontejner služby Azure Blob Storage:

1. [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Crete kontejneru objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) v účtu úložiště

Po vytvoření kontejneru objektů BLOB vygenerujte ověřovací token SAS s oprávněním pro čtení a seznam pouze pomocí následujících kroků:

1. Přístup k účtu úložiště pomocí Azure Portal
2. Přejít na Průzkumník služby Storage
3. Rozbalit kontejnery objektů BLOB
4. Klikněte pravým tlačítkem na kontejner objektů BLOB.
5. Vybrat získání sdíleného přístupového podpisu
6. Vyberte časový rámec vypršení platnosti tokenu. Zajistěte, aby byl token platný pro dobu trvání migrace.
    - Všimněte si, že časové pásmo tokenu a vaše spravovaná instance SQL mohou nesouhlasí. Ujistěte se, že token SAS má patřičnou dobu platnosti, která bere v úvahu časová pásma. Pokud je to možné, nastavte časové pásmo na dřívější a pozdější čas plánovaného migračního okna.
8. Ujistěte se, že jsou vybrána oprávnění jen pro čtení a seznam.
9. Klikněte na vytvořit.
10. Zkopírujte token za otazníkem "?" a dále. Token SAS se obvykle začíná řetězcem "sv = 2020-10" v identifikátoru URI pro použití ve vašem kódu.

> [!IMPORTANT]
> - Oprávnění pro token SAS pro Azure Blob Storage musí být jen pro čtení a seznam. V případě jakýchkoli dalších oprávnění udělených pro ověřovací token SAS se spuštění služby LRS nezdaří. Tyto požadavky na zabezpečení jsou záměrné.
> - Token musí mít odpovídající dobu platnosti. Ujistěte se prosím, že se berou v úvahu časová pásma mezi tokenem a spravovanou instancí.
> - Ujistěte se prosím, že se token zkopíroval od "sv = 2020-10..." až do konce řetězce.

### <a name="log-in-to-azure-and-select-subscription"></a>Přihlaste se k Azure a vyberte předplatné.

K přihlášení do Azure použijte následující rutinu PowerShellu:

```powershell
Login-AzAccount
```

Vyberte příslušné předplatné, ve kterém se vaše spravovaná instance SQL nachází, a to pomocí následující rutiny PowerShellu:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Spuštění migrace

Migrace se spustí spuštěním služby LRS. Službu je možné spustit v automatickém dokončování nebo v nepřetržitém režimu. Po spuštění v režimu automatického dokončování se migrace automaticky dokončí, až se obnoví poslední zadaný záložní soubor. Tato možnost vyžaduje, aby spouštěcí příkaz určil název souboru posledního záložního souboru. Když se LRS spustí v nepřetržitém režimu, služba průběžně obnoví všechny nově přidané záložní soubory a migrace se dokončí jenom na manuální přímou migraci. 

### <a name="start-lrs-in-autocomplete-mode"></a>Spustit LRS v režimu automatického dokončování

Pokud chcete spustit službu LRS v režimu automatického dokončování, použijte následující příkazy PowerShellu nebo rozhraní příkazového řádku. Zadejte název souboru poslední zálohy s parametrem-LastBackupName. Po obnovení zadaného názvu posledního záložního souboru bude služba automaticky iniciovat přímou migraci.

Spustit LRS v režimu automatického dokončování – příklad PowerShellu:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Spustit LRS v režimu automatického dokončování – příklad rozhraní příkazového řádku:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Spustit LRS v nepřetržitém režimu

Spustit LRS v režimu průběžného čtení – příklad PowerShellu:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Spusťte LRS v režimu průběžného čtení – příklad rozhraní příkazového řádku:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> Po spuštění LRS budou všechny opravy softwaru spravované systémem po dobu dalších 47 hodin zastaveny. Po předání tohoto okna se další automatizovaná Oprava softwaru automaticky zastaví probíhající LRS. V takovém případě nelze migraci obnovit a je třeba ji restartovat od začátku. 

## <a name="monitor-the-migration-progress"></a>Sledování průběhu migrace

Chcete-li monitorovat průběh operace migrace, použijte následující příkaz prostředí PowerShell:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Pokud chcete monitorovat průběh operace migrace, použijte následující příkaz CLI:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Zastavení migrace

V případě, že potřebujete migraci zastavit, použijte následující rutiny. Zastavením migrace dojde k odstranění obnovované databáze na spravované instanci SQL, protože nebude možné pokračovat v migraci.

K stop\abort procesu migrace použijte následující příkaz prostředí PowerShell:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

K stop\abort procesu migrace použijte následující příkaz CLI:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Dokončení migrace (průběžný režim)

V případě, že je LRS spuštěný v nepřetržitém režimu, po zajistěte, aby se obnovily všechny zálohy, a iniciování přímou migraci dokončí migraci. Po dokončení přímou migraci bude databáze migrována a připravena pro přístup pro čtení a zápis.

K dokončení procesu migrace v LRS nepřetržitém režimu použijte následující příkaz PowerShellu:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

K dokončení procesu migrace v LRS nepřetržitém režimu použijte následující příkaz CLI:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="troubleshooting"></a>Řešení potíží

Po spuštění LRS použijte rutiny monitorování (Get-azsqlinstancedatabaselogreplay nebo az_sql_midb_log_replay_show) a zobrazte stav operace. Pokud po určitém čase LRS nepovede s chybou, zkontrolujte prosím některé z nejběžnějších problémů:
- Použila se záloha databáze u SQL Server pomocí možnosti **kontrolního součtu** ?
- Mají oprávnění k **čtení** a **vypsání** tokenu SAS jenom pro službu LRS?
- Byl token SAS pro LRS kopírovaný od otazníku "?" s obsahem, který začíná podobně jako hodnota "sv = 2020-02-10..."? 
- Platí doba **platnosti tokenu** SAS pro časové období spuštění a dokončení migrace? Všimněte si, že by mohlo dojít k neshodě z důvodu různých **časových pásem** použitých pro SPRAVOVANOU instanci SQL a tokenu SAS. Zkuste znovu vygenerovat token SAS s rozšířením platnosti tokenu časového intervalu před a po aktuálním datu.
- Jsou název databáze, název skupiny prostředků a název spravované instance zadány správně?
- Pokud byl LRS spuštěn v režimu automatického dokončování, byl pro poslední zadaný soubor zálohy platný název souboru?

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [migraci SQL Server do spravované instance SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Přečtěte si další informace o [rozdílech mezi SQL Server a službou Azure SQL Managed instance](transact-sql-tsql-differences-sql-server.md).
- Přečtěte si další informace o [osvědčených postupech pro náklady a velikost úloh migrovaných do Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
