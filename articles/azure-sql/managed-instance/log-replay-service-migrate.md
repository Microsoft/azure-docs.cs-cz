---
title: Migrace databází do spravované instance SQL pomocí služby pro opětovné přehrání protokolů
description: Naučte se migrovat databáze z SQL Server do spravované instance SQL pomocí služby opětovného přehrání protokolů.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 74403b7ec1469ce7cdaadc9931eb5ac95f55f6f5
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096832"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service-preview"></a>Migrace databází z SQL Server do spravované instance SQL pomocí služby opětovného přehrání protokolů (Preview)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek vysvětluje, jak ručně nakonfigurovat migraci databáze z SQL Server 2008-2019 do spravované instance SQL pomocí služby log Replay (LRS), která je aktuálně ve verzi Public Preview. Toto je cloudová služba povolená pro spravovanou instanci na základě technologie SQL Serverho přenosu protokolů. LRS by se mělo použít v případech, kdy existují složité vlastní migrace a hybridní architektury, pokud existuje větší tolerance pro výpadky nebo když se služba Azure Data Migration Service (DMS) nedá použít.

DMS i LRS používají stejnou základní technologii migrace a stejná rozhraní API. S vydáním LRS ještě více umožníme komplexní vlastní migrace a hybridní architekturu mezi Prem. SQL Server a spravované instance SQL.

## <a name="when-to-use-log-replay-service"></a>Kdy použít službu opětovného přehrání protokolu

V případech, kdy se [Azure DMS](/azure/dms/tutorial-sql-server-to-managed-instance.md) nedá použít pro migraci, se dá cloudová služba LRS použít přímo s PowerShellem, rutinami CLI nebo rozhraním API, aby bylo možné ručně vytvořit a orchestrovat migrace databáze do spravované instance SQL. 

Možná budete chtít zvážit použití cloudové služby LRS v některém z těchto případů:
- Pro váš projekt migrace databáze je potřeba více ovládacích prvků.
- U přímou migraci migrace existuje nízká tolerance.
- Spustitelný soubor DMS nejde nainstalovat do vašeho prostředí.
- Spustitelný soubor DMS nemá přístup k souborům pro zálohy databáze.
- K dispozici není žádný přístup k hostitelskému operačnímu systému nebo žádná oprávnění správce.
- Nepovedlo se otevřít síťové porty z vašeho prostředí do Azure.
- Zálohy se ukládají přímo do Azure Blob Storage pomocí možnosti adresa URL.
- Existuje potřeba použít rozdílové zálohy.

> [!NOTE]
> Doporučený automatizovaný způsob migrace databází z SQL Server do spravované instance SQL používá službu Azure DMS. Tato služba používá stejnou cloudovou službu LRS v back-endu s přenosem protokolů v režimu NORECOVERY. Měli byste zvážit ruční použití LRS k orchestraci migrace v případech, kdy Azure DMS neplně podporuje vaše scénáře.

## <a name="how-does-it-work"></a>Jak to funguje?

Vytvoření vlastního řešení s použitím LRS k migraci databází do cloudu vyžaduje několik kroků orchestrace zobrazených v diagramu a popsaných v následující tabulce.

Migrace se skládá z toho, aby se na SQL Server s povoleným KONTROLNÍm SOUČTem a kopírováním záložních souborů do Azure Blob Storage provádělo úplné zálohování databáze. LRS se používá k obnovení záložních souborů z Azure Blob Storage do spravované instance SQL. Azure Blob Storage slouží jako zprostředkující úložiště mezi SQL Server a spravovanou instancí SQL.

LRS bude monitorovat službu Azure Blob Storage pro všechny nové rozdíly nebo zálohy protokolů přidané po obnovení úplného zálohování a automaticky obnoví všechny přidané nové soubory. Průběh zálohování zálohovaných souborů na spravované instanci SQL je možné monitorovat pomocí služby a proces může být v případě potřeby také přerušen.

LRS nevyžaduje konkrétní konvenci pojmenování záložních souborů, protože kontroluje všechny soubory umístěné v Azure Blob Storage a vytvoří řetězec zálohování z čtení pouze hlaviček souboru. Databáze jsou ve stavu "obnovení" během procesu migrace, protože jsou obnoveny v režimu [NORECOVERY](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) a nelze je použít pro čtení nebo zápis do úplného dokončení procesu migrace. 

Při migraci několika databází je nutné zálohy pro každou databázi umístit do samostatné složky v Azure Blob Storage. LRS se musí spustit samostatně pro každou databázi a musí se zadat jiné cesty pro oddělené složky Azure Blob Storage. 

LRS lze spustit v režimu automatického dokončování nebo nepřetržitě. Po spuštění v režimu automatického dokončování se migrace automaticky dokončí, až se obnoví poslední zadaný název záložního souboru. Při spuštění v režimu průběžné obnovování služby průběžně obnoví všechny nově přidané záložní soubory a migrace se dokončí jenom na ruční přímou migraci. Doporučuje se, aby se ruční přímou migraci prováděl až po pořízení finální zálohy protokolu, která se zobrazuje jako obnovená na spravované instanci SQL. Poslední krok přímou migraci zajistí, že databáze bude online a bude dostupná pro čtení a zápis na spravované instanci SQL.

Jakmile se LRS zastaví automaticky při automatickém dokončování nebo ručně v přímou migraci, proces obnovení se nedá obnovit pro databázi, která se napravila online na spravované instanci SQL. Aby bylo možné obnovit další záložní soubory po dokončení migrace prostřednictvím automatického dokončování, nebo ručně v přímou migraci, je nutné databázi odstranit a celý řetěz pro zálohování je nutné obnovit od nuly restartováním LRS.

   :::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Vysvětlení kroků orchestrace služby pro opětovné přehrání protokolu pro spravovanou instanci SQL" border="false":::
    
| Operace | Podrobnosti |
| :----------------------------- | :------------------------- |
| **1. Zkopírujte zálohy databáze z SQL Server do Azure Blob Storage**. | – Kopírování úplných, rozdílových a protokolových záloh z SQL Server do služby Azure Blob Storage Container pomocí [AzCopy](/azure/storage/common/storage-use-azcopy-v10)nebo [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/). <br />– Použijte všechny názvy souborů, protože LRS nevyžaduje konkrétní konvenci pojmenovávání souborů.<br />– Při migraci několika databází se pro každou databázi vyžaduje samostatná složka. |
| **2. Spusťte službu LRS v cloudu**. | -Služba může být spuštěná s volbou rutin: <br /> Spuštění PowerShellu [– azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> [Rutiny AZ_SQL_MIDB_LOG_REPLAY_START](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)CLI. <br /> – Spusťte LRS samostatně pro každou jinou databázi, která odkazuje na jinou složku zálohy v Azure Blob Storage. <br />-Po spuštění bude služba přebírat zálohy z kontejneru Azure Blob Storage a začne je obnovovat na spravované instanci SQL.<br /> -V případě, že LRS bylo spuštěno v nepřetržitém režimu, po obnovení všech původně nahraných záloh bude služba sledovat všechny nové soubory nahrané do složky a bude průběžně používat protokoly na základě řetězce LSN, dokud nebude služba zastavena. |
| **2,1. Sledujte průběh operace**. | -Průběh operace obnovení je možné monitorovat pomocí volby rutiny nebo: <br /> PowerShell [Get – azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> [Rutiny AZ_SQL_MIDB_LOG_REPLAY_SHOW](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)CLI. |
| **2,2. v případě potřeby Stop\abort operaci**. | – V případě, že je potřeba proces migrace přerušit, můžete operaci zastavit s volbou rutin: <br /> PowerShell [Stop-azsqlinstancedatabaselogreplay]/PowerShell/Module/AZ.SQL/stop-azsqlinstancedatabaselogreplay) <br /> Rutiny [AZ_SQL_MIDB_LOG_REPLAY_STOP](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) CLI. <br /><br />– To způsobí odstranění databáze, která se obnovuje na spravované instanci SQL. <br />-Po zastavení nelze LRS pro databázi obnovit. Proces migrace se musí od začátku restartovat. |
| **3. přímou migraci do cloudu, když je připravený**. | – Zastavte aplikaci a úlohu. Využijte poslední zálohu log-Tail a nahrajte ji do Azure Blob Storage.<br /> – Dokončete přímou migraci tak, že zahájíte operaci LRS Complete s volbou rutin: <br />PowerShell [dokončen – azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> Rutiny [AZ_SQL_MIDB_LOG_REPLAY_COMPLETE](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) CLI. <br /><br />– To způsobí zastavení služby LRS a připojení databáze do režimu online pro čtení a zápis na spravované instanci SQL.<br /> -Přesměruje připojovací řetězec aplikace z SQL Server na spravovanou instanci SQL. |

## <a name="requirements-for-getting-started"></a>Požadavky na Začínáme

### <a name="sql-server-side"></a>SQL Server strana
- SQL Server 2008-2019
- Úplná záloha databází (jeden nebo více souborů)
- Rozdílové zálohování (jeden nebo víc souborů)
- Záloha protokolu (není rozdělená do souboru protokolu transakce)
- Pro zálohy **musí být povolený kontrolní součet** (povinné).

### <a name="azure-side"></a>Strana Azure
- PowerShell AZ. SQL Module verze 2.16.0 nebo novější ([nainstalujte](https://www.powershellgallery.com/packages/Az.Sql/)nebo použijte Azure [Cloud Shell](/azure/cloud-shell/))
- Rozhraní CLI verze 2.19.0 nebo novější ([install](/cli/azure/install-azure-cli))
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
- Spusťte [Data Migration Assistant](/sql/dma/dma-overview) k ověření, jestli jsou vaše databáze připravené k migraci do spravované instance SQL. 
- Oddělte úplné a rozdílové zálohy na více souborů místo jediného souboru.
- Povolte komprimaci zálohování.
- Pomocí Cloud Shell můžete spouštět skripty, protože se vždycky aktualizují na nejnovější vydané rutiny.
- Naplánujte dokončení migrace během 47 hodin od spuštění služby LRS. Toto je doba odkladu zabraňující softwarovým opravám spravovaným po spuštění LRS.

> [!IMPORTANT]
> - Databázi, která se obnovuje pomocí LRS, se nedá použít, dokud se proces migrace nedokončí.
> - V LRS není podporován přístup k databázím jen pro čtení.
> - Po dokončení migrace se proces migrace dokončuje, protože LRS obnovení nepodporuje.

## <a name="steps-to-execute"></a>Kroky ke spuštění

### <a name="make-backups-on-the-sql-server"></a>Vytvoření zálohy na SQL Server

Zálohy na SQL Server lze provést pomocí některé z následujících dvou možností:

- Proveďte zálohování na místní diskové úložiště a pak nahrajte soubory do Azure Blob Storage v případě, že vaše prostředí má omezující přímé zálohování do Azure Blob Storage.
- Zálohování přímo do Azure Blob Storage s možností "na adresu URL" v T-SQL, pro případ, že vám vaše prostředí a postupy zabezpečení umožňují. 

Nastavte databáze, které chcete migrovat do režimu úplného obnovení, aby se povolily zálohy protokolů.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Chcete-li ručně vytvořit úplnou, rozdílovou a protokolovou zálohu databáze v místním úložišti, použijte níže uvedené ukázkové skripty T-SQL. Ujistěte se, že je povolená možnost KONTROLNÍho SOUČTu, protože se jedná o povinný požadavek pro LRS.

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-azure-blob-storage"></a>Vytvoření úložiště objektů BLOB v Azure

Služba Azure Blob Storage slouží jako zprostředkující úložiště pro záložní soubory mezi SQL Server a SQL Managed instance. Pokud chcete vytvořit nový účet úložiště a kontejner objektů BLOB v účtu úložiště, postupujte podle těchto kroků:

1. [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Crete kontejneru objektů BLOB](../../storage/blobs/storage-quickstart-blobs-portal.md) v účtu úložiště

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Kopírování záloh z SQL Server do Azure Blob Storage

Některé z následujících přístupů je možné využít k nahrávání záloh do úložiště objektů BLOB v části migrace databází do spravované instance pomocí LRS:
- Pomocí [AzCopy](/azure/storage/common/storage-use-azcopy-v10)nebo [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer) nahrávání záloh do kontejneru objektů BLOB.
- Použití Průzkumník služby Storage v Azure Portal.

### <a name="make-backups-from-sql-server-directly-to-azure-blob-storage"></a>Vytvoření zálohy z SQL Server přímo do Azure Blob Storage

V případě, že podniková a síťová zásada to umožňuje, je alternativním způsobem zálohování z SQL Server přímo do Azure Blob Storage pomocí možnosti SQL Server Nativní [zálohování na adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . Pokud můžete tuto možnost využít, není nutné vytvářet zálohy v místním úložišti a odesílat je do služby Azure Blob Storage.

V prvním kroku tato operace vyžaduje, aby se vygeneroval ověřovací token SAS pro Azure Blob Storage, a token se musí naimportovat do SQL Server. Druhým krokem je udělat v T-SQL zálohy s možností "na adresu URL". Zajistěte, aby se všechny zálohy vytvořily s povolenou možností CHEKSUM.

Vzorový kód pro vytvoření zálohy do Azure Blob Storage je uvedený níže. Tento příklad neobsahuje pokyny k importu tokenu SAS. Podrobné pokyny, včetně toho, jak vygenerovat a importovat token SAS pro SQL Server, jsou uvedené v následujícím kurzu: [použití služby Azure Blob Storage s SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example on how to make full database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-azure-blob-storage-sas-authentication-for-lrs"></a>Vygenerovat ověřování Azure Blob Storage SAS pro LRS

Služba Azure Blob Storage slouží jako zprostředkující úložiště pro záložní soubory mezi SQL Server a SQL Managed instance. Ověřovací token SAS s oprávněním list a jen pro čtení musí být vygenerován pro použití službou LRS. To umožní službě LRS přistupovat k Azure Blob Storage a pomocí záložních souborů je obnovit ve spravované instanci SQL. Pomocí těchto kroků vygenerujte ověřování SAS pro LRS použití:

1. Přístup k Průzkumník služby Storage z Azure Portal

2. Rozbalit kontejnery objektů BLOB

3. Klikněte pravým tlačítkem na kontejner objektů BLOB a vyberte získat sdílený přístupový podpis.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Služba pro opětovné přehrání protokolů – získání sdíleného přístupového podpisu":::

4. Vyberte časový rámec vypršení platnosti tokenu. Zajistěte, aby byl token platný pro dobu trvání migrace.

5. Vyberte časové pásmo pro token – UTC nebo místní čas.

   - Časové pásmo tokenu a vaše spravovaná instance SQL se můžou neshodovat. Ujistěte se, že token SAS má patřičnou dobu platnosti, která bere v úvahu časová pásma. Pokud je to možné, nastavte časové pásmo na dřívější a pozdější čas plánovaného migračního okna.

6. Výběr pouze oprávnění jen pro čtení a seznam

   - Není nutné vybrat žádná další oprávnění, jinak LRS nebude možné spustit. Tento požadavek zabezpečení je záměrné.

7. Klikněte na tlačítko vytvořit.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Služba pro opětovné přehrání protokolů – vygenerování ověřovacího tokenu SAS":::

   Ověřování pomocí SAS se vygeneruje s dobou platnosti, kterou jste zadali dříve. Budete potřebovat verzi identifikátoru URI generovaného tokenu, jak je znázorněno na snímku obrazovky níže.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Služba pro opětovné přehrání protokolů – kopírování sdíleného přístupového podpisu URI":::

### <a name="copy-parameters-from-sas-token-generated"></a>Kopírovat parametry z generovaného tokenu SAS

Aby bylo možné správně použít token SAS ke spuštění LRS, musíme pochopit jeho strukturu. Identifikátor URI generovaného tokenu SAS se skládá ze dvou částí:
- StorageContainerUri a 
- StorageContainerSasToken, oddělený otazníkem (?), jak je znázorněno na obrázku níže.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Příklad identifikátoru URI ověřování SAS generovaných službou Log" border="false":::

- První část začínající řetězcem "https://", dokud se nepoužije otazník (?) pro parametr StorageContainerURI, který je zadáván jako vstup do LRS. To poskytuje LRS informace o složce, ve které jsou uložené soubory zálohy databáze.
- Druhá část, která začíná po otazníku (?), v příkladu "SP =" a všech způsobem až do konce řetězce je StorageContainerSasToken parametr. Toto je skutečný podepsaný ověřovací token platný po dobu určenou v čase. Tato součást nemusí nutně začínat na "SP =", jak je znázorněno, a že se váš případ může lišit.

Parametry zkopírujte následujícím způsobem:

1. Zkopírujte první část tokenu, která začíná https://, až do otazníku (?) a použijete ho jako parametr StorageContainerUri v PowerShellu nebo rozhraní příkazového řádku pro spuštění LRS, jak je znázorněno na snímku obrazovky níže.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="StorageContainerUri parametr kopírování služby pro opětovné přehrání protokolu":::

2. Zkopírujte druhou část tokenu začínající otazníkem (?), a to až do konce řetězce, a použijte ho jako parametr StorageContainerSasToken v PowerShellu nebo CLI pro spuštění LRS, jak je znázorněno na snímku obrazovky níže.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="StorageContainerSasToken parametr kopírování služby pro opětovné přehrání protokolu":::

> [!IMPORTANT]
> - Oprávnění pro token SAS pro Azure Blob Storage musí být jen pro čtení a seznam. Pokud jsou pro ověřovací token SAS udělená jiná oprávnění, spuštění služby LRS se nezdaří. Tyto požadavky na zabezpečení jsou záměrné.
> - Token musí mít odpovídající dobu platnosti. Zajistěte, aby časová pásma mezi tokenem a spravovanou instancí byla vzata v potaz.
> - Zajistěte, aby byl parametr StorageContainerUri pro PowerShell nebo rozhraní příkazového řádku zkopírovaný z identifikátoru URI vygenerovaného tokenu, od https://, až po označení otázek (?). Nezahrnujte otazník.
> Zajistěte, aby byl StorageContainerSasToken parametr PowerShellu pro rozhraní příkazového řádku zkopírovaný z identifikátoru URI vygenerovaného tokenu, od otazníku (?) až po konec řetězce. Nezahrnujte otazník.

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
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Spustit LRS v režimu automatického dokončování – příklad rozhraní příkazového řádku:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Spustit LRS v nepřetržitém režimu

Spustit LRS v režimu průběžného čtení – příklad PowerShellu:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Spusťte LRS v režimu průběžného čtení – příklad rozhraní příkazového řádku:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="scripting-lrs-start-in-continuous-mode"></a>LRS skriptování začíná v nepřetržitém režimu

Klienti PowerShellu a rozhraní příkazového řádku pro spuštění LRS v nepřetržitém režimu jsou synchronní. To znamená, že klienti budou čekat na odpověď rozhraní API, aby nahlásila úspěch nebo neúspěšné spuštění úlohy. Během tohoto čekání příkaz nevrátí ovládací prvek zpět do příkazového řádku. V případě, že zadáváte prostředí migrace a vyžadujete, aby příkaz LRS Start ihned poskytoval kontrolu, abyste mohli pokračovat ve zbytku skriptu, můžete spustit PowerShell jako úlohu na pozadí s přepínačem-AsJob. Například:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Když zahájíte úlohu na pozadí, objekt úlohy se vrátí okamžitě, i když dokončení úlohy trvá delší dobu. V relaci můžete pokračovat v práci bez přerušení, zatímco úloha běží. Podrobnosti o spuštění PowerShellu jako úlohy na pozadí najdete v dokumentaci k [prostředí PowerShell Start-Job](/powershell/module/microsoft.powershell.core/start-job#description) .

Podobně pokud chcete spustit příkaz CLI na platformě Linux jako proces na pozadí, použijte symbol ampersand (&) na konci příkazu LRS Start.

```CLI
az sql midb log-replay start <required parameters> &
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

## <a name="functional-limitations"></a>Funkční omezení

Funkční omezení služby LRS (log replay) jsou:
- Obnovenou databázi nelze použít pro přístup jen pro čtení během procesu migrace.
- Opravy softwaru spravované systémem se zablokují po dobu 47 hodin od spuštění LRS. Po vypršení tohoto časového intervalu se další aktualizace softwaru zastaví LRS. V takovém případě je potřeba restartovat LRS od začátku.
- LRS vyžaduje, aby byly databáze na SQL Server zálohovány s povolenou možností KONTROLNÍho SOUČTu.
- Token SAS pro použití v LRS musí být vygenerovaný pro celý kontejner Azure Blob Storage a musí mít jenom oprávnění číst a zobrazit seznam.
- Záložní soubory pro různé databáze se musí umístit do samostatných složek v Azure Blob Storage.
- LRS se musí spustit samostatně pro každou databázi, která odkazuje na samostatné složky se soubory zálohy na Azure Blob Storage.
- LRS může podporovat až 100 simultánních procesů obnovení na jednu spravovanou instanci SQL.

## <a name="troubleshooting"></a>Řešení potíží

Po spuštění LRS použijte rutiny monitorování (Get-azsqlinstancedatabaselogreplay nebo az_sql_midb_log_replay_show) a zobrazte stav operace. Pokud po určitém čase LRS nepovede s chybou, vyhledejte některé z nejběžnějších problémů:
- Již existuje databáze se stejným názvem na SQL MI, kterou se pokoušíte migrovat z SQL Server? Tento konflikt vyřešte přejmenováním jedné z databází.
- Použila se záloha databáze u SQL Server pomocí možnosti **kontrolního součtu** ?
- Mají oprávnění k **čtení** a **vypsání** tokenu SAS jenom pro službu LRS?
- Byl token SAS pro LRS kopírovaný od otazníku "?" s obsahem, který začíná podobně jako hodnota "sv = 2020-02-10..."? 
- Platí doba **platnosti tokenu** SAS pro časové období spuštění a dokončení migrace? Je možné, že došlo k neshodě z důvodu různých **časových pásem** použitých pro SPRAVOVANOU instanci SQL a tokenu SAS. Zkuste znovu vygenerovat token SAS s rozšířením platnosti tokenu časového intervalu před a po aktuálním datu.
- Jsou název databáze, název skupiny prostředků a název spravované instance zadány správně?
- Pokud byl LRS spuštěn v režimu automatického dokončování, byl pro poslední zadaný soubor zálohy platný název souboru?

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [migraci SQL Server do spravované instance SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Přečtěte si další informace o [rozdílech mezi SQL Server a službou Azure SQL Managed instance](transact-sql-tsql-differences-sql-server.md).
- Přečtěte si další informace o [osvědčených postupech pro náklady a velikost úloh migrovaných do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
