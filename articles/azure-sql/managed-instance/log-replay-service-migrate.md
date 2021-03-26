---
title: Migrace databází do spravované instance SQL pomocí služby pro opětovné přehrání protokolů
description: Naučte se migrovat databáze z SQL Server do spravované instance SQL pomocí služby log Replay.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 1b2a3f018b16258622b817648cb00e230313bf49
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564513"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Migrace databází z SQL Server do spravované instance SQL pomocí služby log Replay (Preview)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek vysvětluje, jak ručně nakonfigurovat migraci databáze z SQL Server 2008-2019 do spravované instance Azure SQL pomocí služby log Replay Service (LRS), která je aktuálně ve verzi Public Preview. LRS je cloudová služba, která je povolená pro spravovanou instanci SQL a vychází z SQL Server technologie pro přenos protokolů. 

[Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) a LRS používají stejnou základní technologii migrace a stejná rozhraní API. Vyvoláním LRS ještě více umožníme komplexní vlastní migrace a hybridní architekturu mezi místními SQL Server a SQL Managed instance.

## <a name="when-to-use-log-replay-service"></a>Kdy použít službu opětovného přehrání protokolu

Pokud nemůžete použít Azure Database Migration Service pro migraci, můžete LRS použít přímo s PowerShellem, rutinami Azure CLI nebo rozhraními API k ručnímu sestavení a orchestraci migrace databáze do spravované instance SQL. 

Použití LRS můžete zvážit v následujících případech:
- Pro projekt migrace databáze potřebujete větší kontrolu.
- Přímou migraci migrace má malou toleranci.
- Ve vašem prostředí nejde nainstalovat spustitelný soubor Database Migration Service.
- Spustitelný soubor Database Migration Service nemá přístup k souborům pro zálohy databáze.
- K hostitelskému operačnímu systému není k dispozici žádný přístup nebo nejsou k dispozici žádná oprávnění správce.
- Síťové porty z vašeho prostředí nemůžete otevřít do Azure.
- Zálohy se ukládají přímo do Azure Blob Storage prostřednictvím `TO URL` Možnosti.
- Je nutné použít rozdílové zálohy.

> [!NOTE]
> Migraci databází z SQL Server do spravované instance SQL doporučujeme automatizovat pomocí Database Migration Service. Tato služba používá stejnou cloudovou službu LRS v back-endu s přenosem protokolů v `NORECOVERY` režimu. Zvažte ruční použití LRS k orchestraci migrace, když Database Migration Service plně nepodporují vaše scénáře.

## <a name="how-it-works"></a>Jak to funguje

Vytvoření vlastního řešení pomocí LRS k migraci databází do cloudu vyžaduje několik kroků orchestrace, jak je znázorněno v diagramu a tabulce dále v této části.

Migrace spočívá v tom, že při SQL Server s `CHECKSUM` povolenými a kopírovanými záložními soubory do Azure Blob Storage se zavedly úplné zálohy databáze. LRS se používá k obnovení záložních souborů z Blob Storage do spravované instance SQL. Blob Storage je zprostředkující úložiště mezi SQL Server a spravovanou instancí SQL.

LRS monitoruje Blob Storage pro všechna nová rozdílová nebo protokolová zálohování, která se přidala po obnovení úplného zálohování. LRS pak tyto nové soubory automaticky obnoví. Službu můžete použít ke sledování postupu obnovování záložních souborů v rámci SQL spravované instance a v případě potřeby můžete proces zastavit.

LRS nevyžaduje pro záložní soubory specifickou konvenci pojmenování. Kontroluje všechny soubory, které jsou umístěné na Blob Storage, a sestaví řetězec zálohování z čtení pouze hlaviček souboru. Databáze jsou během procesu migrace ve stavu "obnovení". Databáze se obnoví v režimu [NORECOVERY](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) , takže se nedají použít pro čtení nebo zápis, dokud se proces migrace nedokončí. 

Pokud migrujete několik databází, budete potřebovat:
 
- Zálohujte všechny databáze v samostatné složce na Blob Storage.
- Spusťte LRS samostatně pro každou databázi.
- Zadejte různé cesty k oddělení Blob Storage složky. 

LRS můžete spustit buď v režimu *automatického dokončování* , nebo v *nepřetržitém* režimu. Když ho spustíte v režimu automatického dokončování, migrace se dokončí automaticky, až se obnoví poslední ze zadaných záložních souborů. Když LRS spustíte v nepřetržitém režimu, služba průběžně obnoví všechny nově přidané záložní soubory a migrace se dokončí jenom na manuální přímou migraci. 

Doporučujeme, abyste ručně vyděleni po pořízení finální zálohy protokolu, která se zobrazí jako obnovená na spravované instanci SQL. Poslední krok přímou migraci zajistí, že databáze bude online a bude dostupná pro čtení a zápis na spravované instanci SQL.

Až se LRS zastaví, ať už automaticky prostřednictvím automatického dokončování, nebo ručně prostřednictvím přímou migraci, nemůžete obnovit proces obnovení pro databázi, která se nastala online na spravované instanci SQL. Chcete-li obnovit další záložní soubory po dokončení migrace prostřednictvím funkce AutoComplete nebo přímou migraci, je nutné databázi odstranit. Také je potřeba obnovit celý řetězec zálohování od začátku restartováním LRS.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Diagram, který vysvětluje kroky orchestrace služby přehrání protokolu pro spravovanou instanci SQL" border="false":::
    
| Operace | Podrobnosti |
| :----------------------------- | :------------------------- |
| **1. Zkopírujte zálohy databáze z SQL Server do BLOB Storage**. | Kopírování úplných, rozdílových a protokolových záloh z SQL Server do kontejneru Blob Storage pomocí [AzCopy](../../storage/common/storage-use-azcopy-v10.md) nebo [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/). <br /><br />Použijte libovolné názvy souborů. LRS nevyžaduje konkrétní konvenci pro pojmenovávání souborů.<br /><br />Při migraci několika databází potřebujete samostatnou složku pro každou databázi. |
| **2. Spusťte LRS v cloudu**. | Službu můžete restartovat s volbou rutin: PowerShell ([Start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) nebo Azure CLI ([az_sql_midb_log_replay_start rutiny](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)). <br /><br /> Spusťte LRS samostatně pro každou databázi, která odkazuje na složku zálohy na Blob Storage. <br /><br /> Po spuštění služby bude trvat zálohování z kontejneru Blob Storage a začít je obnovovat na spravované instanci SQL.<br /><br /> Pokud jste LRS spustili v nepřetržitém režimu, po obnovení všech původně nahraných záloh bude služba sledovat všechny nové soubory nahrané do této složky. Služba bude průběžně používat protokoly založené na řetězci pořadového čísla (LSN) protokolu, dokud se nezastaví. |
| **2,1. Sledujte průběh operace**. | Průběh operace obnovení můžete sledovat volbou rutin: PowerShell ([Get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) nebo Azure CLI ([az_sql_midb_log_replay_show rutiny](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2,2. Pokud je to potřeba, zastavte operaci**. | Pokud potřebujete zastavit proces migrace, máte možnost vybrat si rutiny: PowerShell ([stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) nebo Azure CLI ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> Zastavením operace dojde k odstranění databáze, kterou obnovujete na spravované instanci SQL. Po zastavení operace nebude možné obnovit LRS pro databázi. Musíte restartovat proces migrace od začátku. |
| **3. až budete připraveni, vyjmutí do cloudu**. | Zastavte aplikaci a úlohu. Využijte poslední zálohu protokolu a nahrajte ji do Azure Blob Storage.<br /><br /> Dokončete přímou migraci inicializací operace LRS `complete` s volbou rutiny: PowerShell ([Complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) nebo Azure CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Tato operace zastaví LRS a způsobí, že se databáze pro čtení a zápis do spravované instance SQL převede do online režimu.<br /><br /> Přesměrujte připojovací řetězec aplikace z SQL Server na spravovanou instanci SQL. |

## <a name="requirements-for-getting-started"></a>Požadavky na Začínáme

### <a name="sql-server-side"></a>SQL Server strana
- SQL Server 2008-2019
- Úplná záloha databází (jeden nebo více souborů)
- Rozdílové zálohování (jeden nebo víc souborů)
- Zálohování protokolu (není rozdělené do souboru protokolu transakcí)
- `CHECKSUM` povoleno pro zálohování (povinné)

### <a name="azure-side"></a>Strana Azure
- PowerShell AZ. SQL Module verze 2.16.0 nebo novější ([nainstalováno](https://www.powershellgallery.com/packages/Az.Sql/) nebo otevřeno prostřednictvím [Azure Cloud Shell](/azure/cloud-shell/))
- Azure CLI verze 2.19.0 nebo novější ([nainstalováno](/cli/azure/install-azure-cli))
- Zřízený kontejner Azure Blob Storage
- Token zabezpečení sdíleného přístupového podpisu (SAS) s oprávněním pro čtení a seznam generovaným pro kontejner Blob Storage

### <a name="migration-of-multiple-databases"></a>Migrace více databází
Záložní soubory pro různé databáze musíte umístit do samostatných složek na Blob Storage.

Pro každou databázi spusťte LRS samostatně, a to tak, že přejdete na příslušnou složku na Blob Storage. LRS může podporovat až 100 simultánních procesů obnovení na jednu spravovanou instanci.

### <a name="azure-rbac-permissions"></a>Oprávnění Azure RBAC
Spuštění LRS prostřednictvím poskytnutých klientů vyžaduje jednu z následujících rolí Azure:
- Role vlastníka předplatného
- Role [přispěvatele spravované instance](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)
- Vlastní role s následujícím oprávněním: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Osvědčené postupy

Doporučujeme následující osvědčené postupy:
- Spusťte [Data Migration Assistant](/sql/dma/dma-overview) a ověřte, zda jsou vaše databáze připravené k migraci do spravované instance SQL. 
- Oddělte úplné a rozdílové zálohy do více souborů místo použití jednoho souboru.
- Povolte komprimaci zálohování.
- Pomocí Cloud Shell můžete spouštět skripty, protože se vždycky aktualizují na nejnovější vydané rutiny.
- Naplánujte dokončení migrace během 47 hodin po spuštění LRS. Toto je lhůta odkladu, která brání instalaci oprav softwaru spravovaných systémem.

> [!IMPORTANT]
> - Databázi, která se obnovuje pomocí LRS, nemůžete použít, dokud proces migrace nedokončí. 
> - LRS v průběhu migrace nepodporuje přístup k databázím jen pro čtení.
> - Po dokončení migrace je proces migrace finalizován, protože LRS nepodporuje obnovení procesu obnovení.

## <a name="steps-to-execute"></a>Kroky ke spuštění

### <a name="make-backups-of-sql-server"></a>Provedení zálohování SQL Server

Zálohy SQL Server můžete provádět pomocí kterékoli z následujících možností:

- Pokud vaše prostředí omezuje přímé zálohování do Blob Storage, zálohujte na místní diskové úložiště a pak nahrajte soubory do Azure Blob Storage.
- `TO URL`Pokud to vaše prostředí a bezpečnostní postupy umožňují, zálohujte se přímo na BLOB Storage s možností v T-SQL. 

Nastavte databáze, které chcete migrovat do režimu úplného obnovení, aby se povolily zálohy protokolů.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

K ručnímu vytvoření úplných, rozdílových a log záloh databáze v místním úložišti použijte následující ukázkové skripty T-SQL. Zajistěte, aby byla `CHECKSUM` možnost povolena, protože je povinná pro LRS.

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Služba Azure Blob Storage slouží jako zprostředkující úložiště pro záložní soubory mezi SQL Server a spravovanou instancí SQL. Pokud chcete vytvořit nový účet úložiště a kontejner objektů BLOB v účtu úložiště, postupujte podle těchto kroků:

1. [Vytvořte účet úložiště](../../storage/common/storage-account-create.md?tabs=azure-portal).
2. [Crete kontejner objektů BLOB](../../storage/blobs/storage-quickstart-blobs-portal.md) v účtu úložiště.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Kopírovat zálohy z SQL Server do Blob Storage

Při migraci databází do spravované instance pomocí LRS můžete pomocí následujících přístupů nahrát zálohy do Blob Storage:
- Použití funkce SQL Server nativního [zálohování na adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)
- Nahrávání záloh do kontejneru objektů BLOB pomocí [AzCopy](../../storage/common/storage-use-azcopy-v10.md) nebo [Průzkumník služby Azure Storage](https://azure.microsoft.com/en-us/features/storage-explorer)
- Použití Průzkumník služby Storage v Azure Portal

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Vytvoření zálohy z SQL Server přímo do Blob Storage
Pokud to vaše firemní a síťové zásady umožňují, je alternativou zálohování z SQL Server přímo do Blob Storage pomocí možnosti SQL Server Nativní [zálohování na adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . Pokud můžete tuto možnost využít, nemusíte vytvářet zálohy v místním úložišti a nahrajte je do Blob Storage.

V prvním kroku tato operace vyžaduje, abyste vygenerovali ověřovací token SAS pro Blob Storage a pak naimportovali token do SQL Server. Druhým krokem je udělat zálohy s `TO URL` možností v T-SQL. Zajistěte, aby se u všech záloh vytvořila `CHEKSUM` možnost s povolenou možností.

Pro referenci se v následujícím ukázkovém kódu provede zálohování Blob Storage. Tento příklad neobsahuje pokyny k importu tokenu SAS. Podrobné pokyny, včetně postupu pro vygenerování a import tokenu SAS pro SQL Server, najdete v kurzu [použití Azure Blob Storage s SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Vygenerovat ověřovací token SAS Blob Storage pro LRS

Služba Azure Blob Storage slouží jako zprostředkující úložiště pro záložní soubory mezi SQL Server a spravovanou instancí SQL. Musíte vygenerovat ověřovací token SAS, který má pro LRS jenom oprávnění list a čtení. Token umožní LRS získat přístup k Blob Storage a použít záložní soubory k jejich obnovení na spravované instanci SQL. 

Pomocí těchto kroků vygenerujte token:

1. Otevřete Průzkumník služby Storage z Azure Portal.
2. Rozbalte **kontejnery objektů BLOB**.
3. Klikněte pravým tlačítkem na kontejner objektů BLOB a vyberte **získat sdílený přístupový podpis**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Snímek obrazovky zobrazující výběry pro vytvoření ověřovacího tokenu S A S":::

4. Vyberte časový rámec pro vypršení platnosti tokenu. Zajistěte, aby byl token platný po dobu trvání migrace.
5. Vyberte časové pásmo pro token: UTC nebo místní čas.
    
   > [!IMPORTANT]
   > Časové pásmo tokenu a vaší spravované instance se může neshodovat. Zajistěte, aby měl token SAS odpovídající dobu platnosti a aby se zohlednila časová pásma. Pokud je to možné, nastavte časové pásmo na dřívější a pozdější čas plánovaného migračního okna.
6. Vyberte pouze oprávnění **číst** a **Zobrazit seznam** .

   > [!IMPORTANT]
   > Nevybírejte žádná další oprávnění. Pokud to uděláte, LRS se nespustí. Tento požadavek zabezpečení je záměrné.
7. Vyberte **Vytvořit**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Snímek obrazovky, který zobrazuje možnosti pro vypršení platnosti tokenu a S, časové pásmo a oprávnění, společně s tlačítkem vytvořit.":::

Ověřování pomocí SAS se vygeneruje s dobou platnosti, kterou jste zadali. Potřebujete verzi tokenu URI, jak je znázorněno na následujícím snímku obrazovky.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Snímek obrazovky, který ukazuje příklad verze U tokenu S A S, který je ve verzi R.":::

### <a name="copy-parameters-from-the-sas-token"></a>Kopírovat parametry z tokenu SAS

Předtím, než použijete token SAS ke spuštění LRS, je potřeba pochopit jeho strukturu. Identifikátor URI generovaného tokenu SAS se skládá ze dvou částí oddělených otazníkem ( `?` ), jak je znázorněno v následujícím příkladu:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Příklad U R I pro vygenerovaný token S A S pro službu opětovného přehrání protokolů" border="false":::

První část, počínaje `https://` až do otazníku ( `?` ), se použije pro `StorageContainerURI` parametr, který je podáván jako vstup do LRS. Poskytuje LRS informace o složce, ve které jsou uložené soubory zálohy databáze.

Druhá část, která začíná po otazníku ( `?` ) a projde všemi způsoby až do konce řetězce, je `StorageContainerSasToken` parametr. Toto je skutečný podepsaný ověřovací token, který je platný po dobu určenou časem. Tato část nemusí nutně vyžadovat spuštění `sp=` , jak je znázorněno v příkladu. Váš případ se může lišit.

Zkopírujte parametry následujícím způsobem:

1. Zkopírujte první část tokenu a začněte od `https://` všech možností až do otazníku ( `?` ). Použijte ji jako `StorageContainerUri` parametr v PowerShellu nebo rozhraní příkazového řádku Azure pro spuštění LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Snímek obrazovky, který ukazuje kopírování první části tokenu.":::

2. Zkopírujte druhou část tokenu počínaje otazníkem ( `?` ) až do konce řetězce. Použijte ji jako `StorageContainerSasToken` parametr v PowerShellu nebo rozhraní příkazového řádku Azure pro spuštění LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Snímek obrazovky, který ukazuje kopírování druhé části tokenu.":::
   
> [!NOTE]
> Nepoužívejte otazník při kopírování části tokenu.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Přihlaste se k Azure a vyberte předplatné.

K přihlášení do Azure použijte následující rutinu PowerShellu:

```powershell
Login-AzAccount
```

Vyberte příslušné předplatné, ve kterém se vaše spravovaná instance nachází, pomocí následující rutiny PowerShellu:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Spuštění migrace

Migraci spustíte spuštěním LRS. Službu můžete spustit buď v režimu automatického dokončování, nebo v nepřetržitém režimu. 

Když použijete režim automatického dokončování, migrace se dokončí automaticky, až se obnoví poslední ze zadaných záložních souborů. Tato možnost vyžaduje, aby spouštěcí příkaz určil název souboru posledního záložního souboru. 

Když použijete průběžný režim, služba průběžně obnoví všechny nově přidané záložní soubory. Migrace se dokončí jenom na ruční přímou migraci. 

### <a name="start-lrs-in-autocomplete-mode"></a>Spustit LRS v režimu automatického dokončování

Pokud chcete začít LRS v režimu automatického dokončování, použijte následující příkazy PowerShellu nebo rozhraní příkazového řádku Azure. Pomocí parametru zadejte název posledního záložního souboru `-LastBackupName` . Po obnovení posledního zadaného záložního souboru bude služba automaticky iniciovat přímou migraci.

Tady je příklad spuštění LRS v režimu automatického dokončování pomocí prostředí PowerShell:

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

Tady je příklad spuštění LRS v režimu automatického dokončování pomocí Azure CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Spustit LRS v nepřetržitém režimu

Tady je příklad spuštění LRS v nepřetržitém režimu pomocí prostředí PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Tady je příklad spuštění LRS v nepřetržitém režimu pomocí Azure CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Klienti PowerShellu a rozhraní příkazového řádku pro spuštění LRS v nepřetržitém režimu jsou synchronní. To znamená, že klienti budou čekat na odpověď rozhraní API, aby nahlásila úspěch nebo neúspěšné spuštění úlohy. 

Během tohoto čekání příkaz nevrátí řízení do příkazového řádku. Pokud provádíte skriptování v prostředí migrace a potřebujete, aby vám příkaz LRS Start mohl hned vrátit řízení, abyste mohli pokračovat ve zbytku skriptu, můžete spustit PowerShell jako úlohu na pozadí s `-AsJob` přepínačem. Například:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Když zahájíte úlohu na pozadí, objekt úlohy se vrátí okamžitě, i když dokončení úlohy trvá delší dobu. V relaci můžete pokračovat v práci bez přerušení, zatímco úloha běží. Podrobnosti o spuštění PowerShellu jako úlohy na pozadí najdete v dokumentaci k [prostředí PowerShell Start-Job](/powershell/module/microsoft.powershell.core/start-job#description) .

Podobně pokud chcete spustit příkaz Azure CLI na platformě Linux jako proces na pozadí, použijte ampersand ( `&` ) na konci LRS příkazu Start:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Po spuštění LRS budou všechny opravy softwaru spravované systémem po dobu 47 hodin zastaveny. Po tomto okně se další automatizovaná Oprava softwaru automaticky zastaví LRS. Pokud k tomu dojde, nemůžete pokračovat v migraci a musíte ji restartovat znovu od začátku. 

## <a name="monitor-the-migration-progress"></a>Sledování průběhu migrace

Pokud chcete monitorovat průběh migrace prostřednictvím PowerShellu, použijte následující příkaz:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Pokud chcete monitorovat průběh migrace prostřednictvím rozhraní příkazového řádku Azure CLI, použijte následující příkaz:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Zastavení migrace

Pokud potřebujete migraci zastavit, použijte následující rutiny. Zastavením migrace dojde k odstranění obnovované databáze na spravované instanci SQL, takže obnovení migrace nebude možné.

Chcete-li zastavit proces migrace pomocí prostředí PowerShell, použijte následující příkaz:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Pokud chcete proces migrace zastavit přes rozhraní příkazového řádku Azure CLI, použijte následující příkaz:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Dokončení migrace (průběžný režim)

Pokud jste LRS v nepřetržitém režimu, po zajistěte, aby se obnovily všechna zálohování, a iniciování přímou migraci dokončí migraci. Po přímou migraci bude databáze migrována a připravena pro přístup pro čtení a zápis.

K dokončení procesu migrace v LRS nepřetržitém režimu pomocí prostředí PowerShell použijte následující příkaz:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

K dokončení procesu migrace v LRS nepřetržitém režimu prostřednictvím rozhraní příkazového řádku Azure použijte následující příkaz:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Funkční omezení

Funkční omezení LRS jsou:
- Databázi, kterou obnovujete, nelze v průběhu procesu migrace použít pro přístup jen pro čtení.
- Opravy softwaru spravované systémem jsou po dobu 47 hodin po spuštění LRS blokované. Po uplynutí tohoto časového období se LRS další aktualizace softwaru zastaví. Pak musíte restartovat LRS od začátku.
- LRS vyžaduje, aby byly databáze na SQL Server zálohovány s `CHECKSUM` povolenou možností.
- Token SAS, který LRS použije, musí být vygenerovaný pro celý kontejner Azure Blob Storage a musí mít jenom oprávnění číst a zobrazit seznam.
- Záložní soubory pro různé databáze musí být umístěny v samostatných složkách na Blob Storage.
- LRS se musí spustit samostatně pro každou databázi, která odkazuje na samostatné složky se soubory zálohy na Blob Storage.
- LRS může podporovat až 100 simultánních procesů obnovení na jednu spravovanou instanci.

## <a name="troubleshooting"></a>Řešení potíží

Po spuštění LRS se podívejte na stav operace pomocí rutiny Monitoring ( `get-azsqlinstancedatabaselogreplay` nebo `az_sql_midb_log_replay_show` ). Pokud se LRS po nějaké době nepovede spustit a zobrazí se chyba, podívejte se na nejčastější problémy:

- Má existující databáze na spravované instanci SQL stejný název jako ten, který se pokoušíte migrovat z SQL Server? Tento konflikt vyřešte přejmenováním jedné z databází.
- Bylo zálohování databáze v SQL Server provedeno prostřednictvím `CHECKSUM` Možnosti?
- Mají oprávnění k tokenu SAS jenom čtení a seznam pro LRS?
- Zkopírovali jste token SAS pro LRS po otazníku ( `?` ) s obsahem, který začíná takto: `sv=2020-02-10...` ? 
- Platí doba platnosti tokenu SAS pro časové období spuštění a dokončení migrace? Může se jednat o neshody z důvodu různých časových pásem používaných pro spravovanou instanci SQL a tokenu SAS. Zkuste znovu vygenerovat token SAS a prodloužit platnost tokenu v časovém intervalu před a po aktuálním datu.
- Je název databáze, název skupiny prostředků a název spravované instance napsán správně?
- Pokud jste začali LRS v režimu automatického dokončování, byl pro poslední zadaný soubor zálohy platný název souboru?

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [migraci SQL Server do spravované instance SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Přečtěte si další informace o [rozdílech mezi SQL Server a SQL Managed instance](transact-sql-tsql-differences-sql-server.md).
- Přečtěte si další informace o [osvědčených postupech pro náklady a velikost úloh migrovaných do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).