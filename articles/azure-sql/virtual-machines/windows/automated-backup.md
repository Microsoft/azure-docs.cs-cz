---
title: Automatizované zálohování v2 pro virtuální počítače Azure s SQL Server 2016/2017 | Microsoft Docs
description: Tento článek vysvětluje funkci automatického zálohování pro virtuální počítače s SQL Server 2016/2017 běžící v Azure. Tento článek je určený pro virtuální počítače, které používají Správce prostředků.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f41614d54dc4320f683f406b2882a7b388bb4c3d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358414"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Automatizované zálohování v2 pro virtuální počítače Azure (Správce prostředků)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016 +](automated-backup.md)

Automatizované zálohování v2 automaticky konfiguruje [spravovanou zálohu na Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) pro všechny stávající a nové databáze na virtuálním počítači Azure s SQL Server 2016 nebo novějšími edicemi Standard, Enterprise nebo Developer. To vám umožní nakonfigurovat pravidelné zálohy databází, které využívají trvalé úložiště objektů BLOB v Azure. Automatizované zálohování v2 závisí na [rozšíření agenta SQL Server infrastruktury jako služby (IaaS)](sql-server-iaas-agent-extension-automate-management.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Předpoklady
Pokud chcete používat automatizované zálohování v2, Projděte si následující požadavky:

**Operační systém**:

- Windows Server 2012 R2 nebo novější

**SQL Server verze/edice**:

- SQL Server 2016 nebo vyšší: Developer, Standard nebo Enterprise

> [!NOTE]
> SQL Server 2014 najdete v tématu [automatizované zálohování pro SQL Server 2014](automated-backup-sql-2014.md).

**Konfigurace databáze**:

- Cílové _uživatelské_ databáze musí používat úplný model obnovení. Systémové databáze nemusejí používat úplný model obnovení. Pokud však požadujete, aby byly zálohy protokolů provedeny pro model nebo MSDB, je nutné použít úplný model obnovení. Další informace o dopadu plného modelu obnovení na zálohování najdete v [části zálohování v rámci úplného modelu obnovení](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105)). 
- SQL Server virtuální počítač se zaregistroval s rozšířením agenta SQL IaaS v [režimu úplné správy](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full). 
-  Automatické zálohování spoléhá na úplné [rozšíření agenta SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md). V takovém případě se automatizované zálohování podporuje jenom v cílových databázích z výchozí instance nebo z jedné pojmenované instance. Pokud neexistuje žádná výchozí instance a více pojmenovaných instancí, rozšíření SQL IaaS selže a automatizované zálohování nebude fungovat. 

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které je možné nakonfigurovat pro automatizované zálohování v2. Skutečné kroky konfigurace se liší v závislosti na tom, jestli používáte příkazy Azure Portal nebo Azure Windows PowerShellu.

### <a name="basic-settings"></a>Základní nastavení

| Nastavení | Rozsah (výchozí) | Description |
| --- | --- | --- |
| **Automatizované zálohování** | Povolit/zakázat (zakázáno) | Povolí nebo zakáže automatizované zálohování pro virtuální počítač Azure se systémem SQL Server 2016/2017 Developer, Standard nebo Enterprise. |
| **Doba uchování** | 1-30 dní (30 dní) | Počet dní uchovávání záloh. |
| **Účet úložiště** | Účet služby Azure Storage | Účet služby Azure Storage, který se má použít pro ukládání automatizovaných záložních souborů v úložišti objektů BLOB. V tomto umístění se vytvoří kontejner pro uložení všech záložních souborů. Konvence pojmenování záložních souborů zahrnuje datum, čas a GUID databáze. |
| **Šifrování** |Povolit/zakázat (zakázáno) | Povolí nebo zakáže šifrování. Pokud je povolené šifrování, certifikáty používané k obnovení zálohy se nacházejí v zadaném účtu úložiště. Používá stejný kontejner **automatického zálohování** se stejnou konvencí vytváření názvů. Pokud se změní heslo, vygeneruje se nový certifikát s tímto heslem, starý certifikát ale zůstane k obnovení předchozího zálohování. |
| **Heslo** |Text hesla | Heslo pro šifrovací klíče. Toto heslo je nutné pouze v případě, že je povoleno šifrování. Aby bylo možné obnovit šifrované zálohování, musíte mít správné heslo a související certifikát, který byl použit v době pořízení zálohy. |

### <a name="advanced-settings"></a>Upřesnit nastavení

| Nastavení | Rozsah (výchozí) | Description |
| --- | --- | --- |
| **Zálohy systémových databází** | Povolit/zakázat (zakázáno) | Když je tato funkce povolená, zálohuje taky systémové databáze: Master, MSDB a model. Pro databáze MSDB a model ověřte, zda jsou v režimu úplného obnovení, pokud chcete provádět zálohy protokolu. Zálohy protokolů se nikdy neprovádí pro hlavní server. A pro databázi TempDB nejsou vyřízeny žádné zálohy. |
| **Plán zálohování** | Ruční/automatizovaná (automatizovaná) | Ve výchozím nastavení se plán zálohování automaticky určí na základě nárůstu protokolu. Plán ručního zálohování umožňuje uživateli zadat časový interval pro zálohování. V takovém případě zálohy probíhají pouze v zadaném intervalu a v určeném časovém intervalu daného dne. |
| **Frekvence úplného zálohování** | Denně/týdně | Frekvence úplných záloh. V obou případech začíná úplné zálohování během dalšího naplánovaného časového intervalu. Pokud je vybrán týden, zálohování může zahrnovat několik dní, dokud nebudou všechny databáze úspěšně zálohovány. |
| **Čas spuštění úplného zálohování** | 00:00 – 23:00 (01:00) | Čas začátku daného dne, během kterého může probíhat úplné zálohování. |
| **Časový interval úplného zálohování** | 1 – 23 hodin (1 hodina) | Doba trvání časového intervalu daného dne, během kterého může probíhat úplné zálohování. |
| **Frekvence zálohování protokolu** | 5 – 60 minut (60 min.) | Frekvence zálohování protokolů. |

## <a name="understanding-full-backup-frequency"></a>Porozumění četnosti úplného zálohování
Je důležité pochopit rozdíl mezi každodenním a týdenním úplným zálohováním. Vezměte v úvahu následující dva ukázkové scénáře.

### <a name="scenario-1-weekly-backups"></a>Scénář 1: týdenní zálohování
Máte SQL Server virtuální počítač, který obsahuje velký počet velkých databází.

V pondělí povolíte automatizované zálohování v2 s následujícími nastaveními:

- Plán zálohování: **Ruční**
- Frekvence úplného zálohování: **týdně**
- Čas spuštění úplného zálohování: **01:00**
- Časový interval úplného zálohování: **1 hodina**

To znamená, že další dostupné okno zálohování je úterý 1. až 1 hodinu. V tuto chvíli začne automatické zálohování zálohovat databáze po jednom. V tomto scénáři jsou vaše databáze dostatečně velké, než se úplné zálohování dokončí pro první pár databází. Nicméně po jedné hodině nejsou všechny databáze zálohovány.

Pokud k tomu dojde, začne automatizované zálohování po dobu jedné hodiny zálohovat zbývající databáze do dalšího dne. Pokud v tuto chvíli nejsou všechny databáze zálohované, zkusí to znovu další den ve stejnou dobu. To pokračuje, dokud nebudou všechny databáze úspěšně zálohovány.

Jakmile znovu dosáhne úterý, začne automatické zálohování zálohovat všechny databáze znovu.

Tento scénář ukazuje, že automatizovaná záloha funguje pouze v rámci zadaného časového okna a každá databáze je zálohována jednou za týden. To také ukazuje, že je možné, že zálohy budou zahrnovat více dní v případě, kdy není možné dokončit všechny zálohy za jeden den.

### <a name="scenario-2-daily-backups"></a>Scénář 2: denní zálohy
Máte SQL Server virtuální počítač, který obsahuje velký počet velkých databází.

V pondělí povolíte automatizované zálohování v2 s následujícími nastaveními:

- Plán zálohování: ruční
- Frekvence úplného zálohování: denně
- Čas spuštění úplného zálohování: 22:00
- Časový interval úplného zálohování: 6 hodin

To znamená, že další dostupné okno zálohování je v pondělí po dobu 6 hodin od pondělí do 10 hodin. V tuto chvíli začne automatické zálohování zálohovat databáze po jednom.

Potom v úterý 10 až 6 hodin se znovu spustí úplné zálohování všech databází.

> [!IMPORTANT]
> Při plánování každodenních záloh se doporučujeme naplánovat celé časové okno, abyste zajistili, že se všechny databáze budou zálohovat v této době. To je obzvláště důležité v případě, kdy máte k zálohování velké množství dat.

## <a name="configure-new-vms"></a>Konfigurace nových virtuálních počítačů

Použijte Azure Portal ke konfiguraci automatického zálohování v2 při vytváření nového virtuálního počítače SQL Server 2016 nebo 2017 v modelu nasazení Správce prostředků.

Na kartě **nastavení SQL Server** v části **automatizovaná záloha** vyberte **Povolit** . Na následujícím Azure Portal snímku obrazovky vidíte nastavení **automatizovaného zálohování SQL** .

![Konfigurace automatizovaného zálohování SQL v Azure Portal](./media/automated-backup/automated-backup-blade.png)

> [!NOTE]
> Automatické zálohování v2 je ve výchozím nastavení zakázané.

## <a name="configure-existing-vms"></a>Konfigurace existujících virtuálních počítačů

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U stávajících virtuálních počítačů s SQL Server otevřete [prostředek SQL Virtual Machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) a pak vyberte **zálohování** pro konfiguraci automatických záloh.

![Automatizované zálohování SQL pro existující virtuální počítače](./media/automated-backup/sql-server-configuration.png)


Po dokončení klikněte na tlačítko **použít** v dolní části stránky nastavení **zálohování** a uložte provedené změny.

Pokud povolíte automatické zálohování poprvé, Azure nakonfiguruje agenta SQL Server IaaS na pozadí. V tuto chvíli Azure Portal nemusí ukázat, že je nakonfigurované automatické zálohování. Počkejte několik minut, než se agent nainstaluje a nakonfiguruje. Pak Azure Portal projeví nová nastavení.

## <a name="configure-with-powershell"></a>Konfigurace pomocí PowerShellu

Pro konfiguraci automatizovaného zálohování v2 můžete použít PowerShell. Než začnete, musíte:

- [Stáhněte a nainstalujte nejnovější Azure PowerShell](https://aka.ms/webpi-azps).
- Otevřete Windows PowerShell a přidružte ho ke svému účtu pomocí příkazu **Connect-AzAccount** .

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-server-iaas-extension"></a>Instalace rozšíření SQL Server IaaS
Pokud jste zřídili SQL Server virtuální počítač z Azure Portal, mělo by již být nainstalováno rozšíření SQL Server IaaS. Můžete určit, jestli je pro váš virtuální počítač nainstalovaný, voláním příkazu **Get-AzVM** a prozkoumáním vlastnosti **Extensions** .

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Pokud je nainstalované rozšíření agenta SQL Server IaaS, mělo by se zobrazit jako "SqlIaaSAgent" nebo "SQLIaaSExtension". **ProvisioningState** pro rozšíření by se taky měl zobrazovat zpráva úspěšná. 

Pokud není nainstalovaná nebo se nepovedlo zřídit, můžete si ho nainstalovat pomocí následujícího příkazu. Kromě názvu virtuálního počítače a skupiny prostředků musíte taky určit oblast (**$region**), ve které se virtuální počítač nachází.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a> Ověřit aktuální nastavení
Pokud jste během zřizování povolili automatické zálohování, můžete ke kontrole aktuální konfigurace použít PowerShell. Spusťte příkaz **Get-AzVMSqlServerExtension** a prověřte vlastnost **AutoBackupSettings** :

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Mělo by se zobrazit výstup podobný následujícímu:

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Pokud výstup ukazuje, že **možnost Povolit** je nastavená na **false**, musíte povolit automatizované zálohování. Dobrá zpráva je, že povolíte a nakonfigurujete automatizované zálohování stejným způsobem. Tyto informace najdete v další části.

> [!NOTE] 
> Pokud nastavení zkontrolujete hned po provedení změny, je možné, že se vrátí staré hodnoty konfigurace. Počkejte několik minut a zkontrolujte nastavení znovu, abyste se ujistili, že byly provedeny změny.

### <a name="configure-automated-backup-v2"></a>Konfigurace automatizovaného zálohování v2
Pomocí PowerShellu můžete povolit automatizované zálohování a kdykoli upravit jeho konfiguraci a chování. 

Nejprve vyberte nebo vytvořte účet úložiště pro záložní soubory. Následující skript vybere účet úložiště, nebo ho vytvoří, pokud neexistuje.

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> Automatizované zálohování nepodporuje ukládání záloh do Premium Storage, ale může přebírat zálohy z disků virtuálních počítačů, které používají Premium Storage.

Pak pomocí příkazu **New-AzVMSqlServerAutoBackupConfig** povolte a nakonfigurujte nastavení automatizovaného zálohování v2 pro ukládání záloh do účtu služby Azure Storage. V tomto příkladu jsou zálohy nastavené tak, aby se zachovaly po dobu 10 dnů. Zálohy systémových databází jsou povoleny. Úplné zálohování je naplánováno týdně s časovým intervalem od 20:00 do 2 hodin. Zálohy protokolu se naplánují každých 30 minut. Druhý příkaz **set-AzVMSqlServerExtension** aktualizuje zadaný virtuální počítač Azure pomocí těchto nastavení.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Instalace a konfigurace agenta SQL Server IaaS může trvat několik minut. 

Pokud chcete povolit šifrování, upravte předchozí skript tak, aby předával parametr **EnableEncryption** spolu s heslem (zabezpečeným řetězcem) pro parametr **CertificatePassword** . Následující skript povolí nastavení automatizovaného zálohování v předchozím příkladu a přidá šifrování.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Chcete-li potvrdit použití nastavení, [Ověřte konfiguraci automatizovaného zálohování](#verifysettings).

### <a name="disable-automated-backup"></a>Zakázat automatizované zálohování
Chcete-li zakázat automatizované zálohování, spusťte stejný skript bez parametru **-Enable** příkazu **New-AzVMSqlServerAutoBackupConfig** . Pokud parametr **-Enable** nesignalizuje, příkaz tuto funkci zakáže. Stejně jako u instalace může trvat několik minut, než se zakáže automatizované zálohování.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Ukázkový skript
Následující skript poskytuje sadu proměnných, které můžete přizpůsobit, aby bylo možné povolit a nakonfigurovat automatizované zálohování pro váš virtuální počítač. V takovém případě může být nutné přizpůsobit skript podle svých požadavků. Například byste museli provést změny, pokud jste chtěli zakázat zálohování systémových databází nebo povolit šifrování.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL Server IaaS Extension 

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorování

Pokud chcete monitorovat automatizované zálohování na SQL Server 2016/2017, máte dvě hlavní možnosti. Vzhledem k tomu, že automatizované zálohování používá funkci spravovaného zálohování SQL Server, platí stejné postupy monitorování i pro obě.

Nejprve můžete dotazovat stav voláním [msdb. managed_backup. sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Nebo dotaz na funkci vracející tabulku [msdb. managed_backup. fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) .

Další možností je využít integrované funkce Databázová pošta pro oznámení.

1. Zavolejte uloženou proceduru [msdb. managed_backup. sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) , abyste přiřadili e-mailovou adresu parametru **SSMBackup2WANotificationEmailIds** . 
1. Povolí [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) odesílání e-mailů z virtuálního počítače Azure.
1. Pro konfiguraci Databázová pošta použijte server SMTP a uživatelské jméno. Databázová pošta můžete nakonfigurovat v SQL Server Management Studio nebo pomocí příkazů jazyka Transact-SQL. Další informace najdete v tématu [databázová pošta](/sql/relational-databases/database-mail/database-mail).
1. [Nakonfigurujte agenta SQL Server pro použití databázová pošta](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Ověřte, jestli je port SMTP povolený přes místní bránu firewall virtuálního počítače a skupinu zabezpečení sítě pro virtuální počítač.

## <a name="next-steps"></a>Další kroky
Automatizované zálohování v2 konfiguruje spravovanou zálohu na virtuálních počítačích Azure. Proto je důležité [si projít dokumentaci pro spravovanou zálohu](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) , abyste porozuměli chování a důsledky.

Další pokyny k zálohování a obnovení pro SQL Server na virtuálních počítačích Azure najdete v následujícím článku: [zálohování a obnovení pro SQL Server na virtuálních počítačích Azure](backup-restore.md).

Informace o dalších dostupných úlohách automatizace najdete v tématu [SQL Server rozšíření agenta IaaS](sql-server-iaas-agent-extension-automate-management.md).

Další informace o spouštění SQL Server na virtuálních počítačích Azure najdete v tématu [přehled SQL Server na virtuálních počítačích Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).