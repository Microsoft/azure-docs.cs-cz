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
ms.date: 02/17/2021
ms.openlocfilehash: 7892b1fe0fcad77d1fde8b44f4a8745b5c7dd334
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654188"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>Migrace databází z SQL Server do spravované instance SQL pomocí služby opětovného přehrání protokolů
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek vysvětluje, jak ručně nakonfigurovat migraci databáze z SQL Server 2008-2019 do spravované instance SQL pomocí služby log Replay (LRS). Toto je cloudová služba povolená pro spravovanou instanci na základě technologie SQL Serverho přenosu protokolů v režimu obnovení. LRS by se mělo použít v případech, kdy se služba migrace dat (DMS) nedá použít, když je potřeba více ovládacích prvků, nebo když existuje nízká tolerance pro výpadky.

## <a name="when-to-use-log-replay-service"></a>Kdy použít službu opětovného přehrání protokolu

V případech, kdy se [Azure DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) nedá použít pro migraci, se dá cloudová služba LRS použít přímo s PowerShellem, rutinami CLI nebo rozhraním API, aby bylo možné ručně vytvořit a orchestrovat migrace databáze do spravované instance SQL. 

Možná budete chtít zvážit použití cloudové služby LRS v některém z těchto případů:
- Pro váš projekt migrace databáze je potřeba více ovládacích prvků.
- U přímou migraci migrace existuje nízká tolerance.
- Spustitelný soubor DMS nejde nainstalovat do vašeho prostředí.
- Spustitelný soubor DMS nemá přístup k souborům pro zálohy databáze.
- K dispozici není žádný přístup k hostitelskému operačnímu systému nebo žádná oprávnění správce.

> [!NOTE]
> Doporučený automatizovaný způsob migrace databází z SQL Server do spravované instance SQL používá službu Azure DMS. Tato služba používá stejnou cloudovou službu LRS v back-endu s přenosem protokolů v režimu bez obnovení. Měli byste zvážit ruční použití LRS k orchestraci migrace v případech, kdy Azure DMS neplně podporuje vaše scénáře.

## <a name="how-does-it-work"></a>Jak to funguje?

Sestavení vlastního řešení pomocí LRS pro migraci databáze do cloudu vyžaduje několik kroků orchestrace zobrazených v diagramu a popsaných v následující tabulce.

Migrace zahrnuje vytváření úplných záloh databáze při SQL Server a kopírování záložních souborů do úložiště objektů BLOB v Azure. LRS se používá k obnovení záložních souborů z úložiště objektů BLOB v Azure do spravované instance SQL. Úložiště objektů BLOB v Azure se používá jako zprostředkující úložiště mezi SQL Server a SQL Managed instance.

LRS bude monitorovat službu Azure Blob Storage pro všechny nové rozdíly nebo zálohy protokolu přidané po obnovení úplného zálohování a automaticky obnoví všechny přidané nové soubory. Průběh zálohování zálohovaných souborů na spravované instanci SQL je možné monitorovat pomocí služby a proces může být v případě potřeby také přerušen. Databáze obnovované během procesu migrace budou v režimu obnovení a nelze je použít ke čtení nebo zápisu do dokončení procesu.

LRS lze spustit v režimu automatického dokončování nebo nepřetržitě. Po spuštění v režimu automatického dokončování se migrace automaticky dokončí, až se obnoví poslední zadaný záložní soubor. Při spuštění v režimu průběžné obnovování služby průběžně obnoví všechny nově přidané záložní soubory a migrace se dokončí jenom na ruční přímou migraci. Poslední krok přímou migraci zpřístupní databáze pro čtení a zápis pro použití ve spravované instanci SQL. 

  ![Vysvětlení kroků orchestrace služby pro opětovné přehrání protokolu pro spravovanou instanci SQL](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Operace | Podrobnosti |
| :----------------------------- | :------------------------- |
| **1. Zkopírujte zálohy databáze z SQL Server do úložiště objektů BLOB v Azure**. | – Kopírování úplných, rozdílových a log záloh z SQL Server do úložiště objektů BLOB v Azure pomocí [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) nebo [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/). <br />– Při migraci několika databází se pro každou databázi vyžaduje samostatná složka. |
| **2. Spusťte službu LRS v cloudu**. | -Služba může být spuštěná s volbou rutin: <br /> Spuštění PowerShellu [– azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> [Rutiny AZ_SQL_MIDB_LOG_REPLAY_START](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)CLI. <br /><br />– Po spuštění bude služba přebírat zálohy z úložiště objektů BLOB v Azure a začne je obnovovat na spravované instanci SQL. <br /> – Jakmile budou všechny původně nahrané zálohy obnoveny, bude služba sledovat všechny nové soubory nahrané do složky a bude průběžně používat protokoly založené na řetězu LSN, dokud se služba nezastaví. |
| **2,1. Sledujte průběh operace**. | -Průběh operace obnovení je možné monitorovat pomocí volby rutiny nebo: <br /> PowerShell [Get – azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> [Rutiny AZ_SQL_MIDB_LOG_REPLAY_SHOW](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)CLI. |
| **2,2. v případě potřeby Stop\abort operaci**. | – V případě, že je potřeba proces migrace přerušit, můžete operaci zastavit s volbou rutin: <br /> PowerShell [stop – azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> Rutiny [AZ_SQL_MIDB_LOG_REPLAY_STOP](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) CLI. <br /><br />– V důsledku toho dojde k odstranění databáze obnovené na spravované instanci SQL. <br />-Po zastavení nemůže LRS pokračovat pro databázi. Proces migrace se musí od začátku restartovat. |
| **3. přímou migraci do cloudu, když je připravený**. | – Jakmile se všechny zálohy obnovily do spravované instance SQL, dokončete přímou migraci spuštěním operace LRS Complete s volbou volání rozhraní API nebo rutinami: <br />PowerShell [dokončen – azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> Rutiny [AZ_SQL_MIDB_LOG_REPLAY_COMPLETE](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) CLI. <br /><br />– To způsobí zastavení služby LRS a obnoví se databáze na spravované instanci. <br />-Přesměruje připojovací řetězec aplikace z SQL Server na spravovanou instanci SQL. <br />– V databázi pro doplňování operací je k dispozici pro operace R/W v cloudu. |

## <a name="requirements-for-getting-started"></a>Požadavky na Začínáme

### <a name="sql-server-side"></a>SQL Server strana
- SQL Server 2008-2019
- Úplná záloha databází (jeden nebo více souborů)
- Rozdílové zálohování (jeden nebo víc souborů)
- Záloha protokolu (není rozdělená do souboru protokolu transakce)
- **Kontrolní součet musí být povolen** jako povinný.

### <a name="azure-side"></a>Strana Azure
-   PowerShell AZ. SQL Module verze 2.16.0 nebo novější ([nainstalujte](https://www.powershellgallery.com/packages/Az.Sql/)nebo použijte Azure [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/))
-   Rozhraní CLI verze 2.19.0 nebo novější ([install](https://docs.microsoft.com/cli/azure/install-azure-cli))
-   Zřizování Azure Blob Storage
-   Token zabezpečení SAS s oprávněními **jen pro čtení** a **seznam** se vygeneroval jenom pro úložiště objektů BLOB.

## <a name="best-practices"></a>Osvědčené postupy

Následující doporučené postupy jsou velmi doporučené:
- Spuštěním [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) ověříte, že vaše databáze nebudou migrovány na spravované instance SQL. 
- Oddělte úplné a rozdílové zálohy na více souborů místo jediného souboru.
- Povolte komprimaci zálohování.
- Pomocí Cloud Shell můžete spouštět skripty, protože se vždycky aktualizují na nejnovější vydané rutiny.
- Naplánujte dokončení migrace během 47 hodin od spuštění služby LRS.

> [!IMPORTANT]
> - Databázi, která se obnovuje pomocí LRS, se nedá použít, dokud se proces migrace nedokončí. Je to proto, že základní technologie je přesouvání protokolu do režimu bez obnovení.
> - V pohotovostním režimu pro přesouvání protokolu není LRS podporovaná kvůli rozdílům v verzích mezi spravovanou instancí SQL a nejnovější verzí SQL Server v rámci trhu.

## <a name="steps-to-execute"></a>Kroky ke spuštění

## <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Kopírování záloh z SQL Server do úložiště objektů BLOB v Azure

K kopírování záloh do úložiště objektů BLOB v migraci databází do spravované instance pomocí LRS se dají využít tyto dva způsoby:
- Použití funkce SQL Server nativního [zálohování na adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) .
- Kopírování záloh do kontejneru objektů BLOB pomocí [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)nebo [Průzkumník služby Azure Storage](https://azure.microsoft.com/en-us/features/storage-explorer). 

## <a name="create-azure-blob-and-sas-authentication-token"></a>Vytvoření ověřovacího tokenu Azure Blob a SAS

Úložiště objektů BLOB v Azure se používá jako zprostředkující úložiště pro záložní soubory mezi SQL Server a SQL Managed instance. Pomocí těchto kroků vytvořte kontejner úložiště objektů BLOB v Azure:

1. [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Crete kontejneru objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) v účtu úložiště

Po vytvoření kontejneru objektů BLOB vygenerujte ověřovací token SAS s oprávněním pro čtení a seznam pouze pomocí následujících kroků:

1. Přístup k účtu úložiště pomocí Azure Portal
2. Přejít na Průzkumník služby Storage
3. Rozbalit kontejnery objektů BLOB
4. Klikněte pravým tlačítkem na kontejner objektů BLOB.
5. Vybrat získání sdíleného přístupového podpisu
6. Vyberte časový rámec vypršení platnosti tokenu. Zajistěte, aby byl token platný pro dobu trvání migrace.
7. Ujistěte se, že jsou vybrána oprávnění jen pro čtení a seznam.
8. Klikněte na vytvořit.
9. Zkopírujte token začínající na "sv =" v identifikátoru URI pro použití ve vašem kódu.

> [!IMPORTANT]
> Oprávnění pro token SAS pro úložiště objektů BLOB v Azure musí být jen pro čtení a seznam. V případě jakýchkoli dalších oprávnění udělených pro ověřovací token SAS se spuštění služby LRS nezdaří. Tyto požadavky na zabezpečení jsou záměrné.

## <a name="log-in-to-azure-and-select-subscription"></a>Přihlaste se k Azure a vyberte předplatné.

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

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoComplete -LastBackupName "last_backup.bak"
```

Spustit LRS v režimu automatického dokončování – příklad rozhraní příkazového řádku:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Spustit LRS v nepřetržitém režimu

Spustit LRS v režimu průběžného čtení – příklad PowerShellu:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Spusťte LRS v režimu průběžného čtení – příklad rozhraní příkazového řádku:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> Po spuštění LRS budou všechny opravy softwaru spravované systémem po dobu dalších 47 hodin zastaveny. Po předání tohoto okna se další automatizovaná Oprava softwaru automaticky zastaví probíhající LRS. V takovém případě nelze migraci obnovit a je třeba ji restartovat od začátku. 

## <a name="monitor-the-migration-progress"></a>Sledování průběhu migrace

Chcete-li monitorovat průběh operace migrace, použijte následující příkaz prostředí PowerShell:

```powershell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Pokud chcete monitorovat průběh operace migrace, použijte následující příkaz CLI:

```cli
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Zastavení migrace

V případě, že potřebujete migraci zastavit, použijte následující rutiny. Zastavením migrace dojde k odstranění obnovované databáze na spravované instanci SQL, protože nebude možné pokračovat v migraci.

K stop\abort procesu migrace použijte následující příkaz prostředí PowerShell:

```powershell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

K stop\abort procesu migrace použijte následující příkaz CLI:

```cli
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Dokončení migrace (průběžný režim)

V případě, že je LRS spuštěný v nepřetržitém režimu, po zajistěte, aby se obnovily všechny zálohy, a iniciování přímou migraci dokončí migraci. Po dokončení přímou migraci bude databáze migrována a připravena pro přístup pro čtení a zápis.

K dokončení procesu migrace v LRS nepřetržitém režimu použijte následující příkaz PowerShellu:

```powershell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" -LastBackupName "last_backup.bak"
```

K dokončení procesu migrace v LRS nepřetržitém režimu použijte následující příkaz CLI:

```cli
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [migraci SQL Server do spravované instance SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Přečtěte si další informace o [rozdílech mezi SQL Server a službou Azure SQL Managed instance](transact-sql-tsql-differences-sql-server.md).
- Přečtěte si další informace o [osvědčených postupech pro náklady a velikost úloh migrovaných do Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
