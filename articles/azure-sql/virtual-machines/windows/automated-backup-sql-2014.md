---
title: Automatizované zálohování pro virtuální počítače s SQL Server 2014 Azure
description: Vysvětluje funkci automatického zálohování pro virtuální počítače s SQL Server 2014 běžící v Azure. Tento článek je určený pro virtuální počítače, které používají Správce prostředků.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41add54ce767413982ab0503f7263c58aed4d4e2
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359281"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Automatizované zálohování pro virtuální počítače s SQL Server 2014 (Správce prostředků)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016/2017](automated-backup.md)

Automatizované zálohování automaticky konfiguruje [spravovanou zálohu na Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) pro všechny stávající a nové databáze na virtuálním počítači Azure s SQL Server 2014 Standard nebo Enterprise. To vám umožní nakonfigurovat pravidelné zálohy databází, které využívají trvalé úložiště objektů BLOB v Azure. Automatizované zálohování závisí na [rozšíření agenta SQL Server infrastruktury jako služby (IaaS)](sql-server-iaas-agent-extension-automate-management.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Předpoklady
Pokud chcete používat automatizované zálohování, vezměte v úvahu následující požadavky:


**Operační systém**:

- Windows Server 2012 a vyšší 

**SQL Server verze/edice**:

- Standard SQL Server 2014
- SQL Server 2014 Enterprise

> [!NOTE]
> Informace o SQL 2016 a vyšších najdete v tématu [automatizovaná záloha pro SQL Server 2016](automated-backup.md).

**Konfigurace databáze**:

- Cílové _uživatelské_ databáze musí používat úplný model obnovení. Systémové databáze nemusejí používat úplný model obnovení. Pokud však požadujete, aby byly zálohy protokolů provedeny pro model nebo MSDB, je nutné použít úplný model obnovení. Další informace o dopadu plného modelu obnovení na zálohování najdete v [části zálohování v rámci úplného modelu obnovení](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105)). 
- SQL Server virtuální počítač se zaregistroval s rozšířením agenta SQL IaaS v [režimu úplné správy](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full). 
-  Automatické zálohování spoléhá na úplné [rozšíření agenta SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md). V takovém případě se automatizované zálohování podporuje jenom v cílových databázích z výchozí instance nebo z jedné pojmenované instance. Pokud neexistuje žádná výchozí instance a více pojmenovaných instancí, rozšíření SQL IaaS selže a automatizované zálohování nebude fungovat. 

## <a name="settings"></a>Nastavení

Následující tabulka popisuje možnosti, které je možné nakonfigurovat pro automatizované zálohování. Skutečné kroky konfigurace se liší v závislosti na tom, jestli používáte příkazy Azure Portal nebo Azure Windows PowerShellu.

| Nastavení | Rozsah (výchozí) | Popis |
| --- | --- | --- |
| **Automatizované zálohování** | Povolit/zakázat (zakázáno) | Povolí nebo zakáže automatizované zálohování pro virtuální počítač Azure se systémem SQL Server 2014 Standard nebo Enterprise. |
| **Doba uchování** | 1-30 dní (30 dní) | Počet dnů uchování zálohy. |
| **Účet úložiště** | Účet služby Azure Storage | Účet služby Azure Storage, který se má použít pro ukládání automatizovaných záložních souborů v úložišti objektů BLOB. V tomto umístění se vytvoří kontejner pro uložení všech záložních souborů. Konvence pojmenování záložních souborů zahrnuje datum, čas a název počítače. |
| **Šifrování** | Povolit/zakázat (zakázáno) | Povolí nebo zakáže šifrování. Pokud je povolené šifrování, certifikáty používané k obnovení zálohy se nacházejí v zadaném účtu úložiště ve stejném `automaticbackup` kontejneru pomocí stejných zásad vytváření názvů. Pokud se změní heslo, vygeneruje se nový certifikát s tímto heslem, starý certifikát ale zůstane k obnovení předchozího zálohování. |
| **Heslo** | Text hesla | Heslo pro šifrovací klíče. Tato možnost je nutná pouze v případě, že je povoleno šifrování. Aby bylo možné obnovit šifrované zálohování, musíte mít správné heslo a související certifikát, který byl použit v době pořízení zálohy. |


## <a name="configure-new-vms"></a>Konfigurace nových virtuálních počítačů

Použijte Azure Portal ke konfiguraci automatického zálohování při vytváření nového virtuálního počítače SQL Server 2014 v modelu nasazení Správce prostředků.

Na kartě **nastavení SQL Server** přejděte dolů na **automatizované zálohování** a vyberte **Povolit**. Na následujícím Azure Portal snímku obrazovky vidíte nastavení **automatizovaného zálohování SQL** .

![Konfigurace automatizovaného zálohování SQL v Azure Portal](./media/automated-backup-sql-2014/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Konfigurace existujících virtuálních počítačů

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U existujících virtuálních počítačů s SQL Server můžete povolit nebo zakázat automatizované zálohování, změnit dobu uchování, zadat účet úložiště a povolit šifrování z Azure Portal. 

Přejděte do [prostředku virtuálního počítače SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) pro virtuální počítač s SQL Server 2014 a potom vyberte **zálohování**. 

![Automatizované zálohování SQL pro existující virtuální počítače](./media/automated-backup-sql-2014/azure-sql-rm-autobackup-existing-vms.png)

Po dokončení vyberte v dolní části stránky **zálohy** tlačítko **použít** a uložte provedené změny.

Pokud povolíte automatické zálohování poprvé, Azure nakonfiguruje agenta SQL Server IaaS na pozadí. V tuto chvíli Azure Portal nemusí ukázat, že je nakonfigurované automatické zálohování. Počkejte několik minut, než se agent nainstaluje a nakonfiguruje. Pak Azure Portal projeví nová nastavení.

> [!NOTE]
> Automatické zálohování můžete také nakonfigurovat pomocí šablony. Další informace najdete v tématu [Šablona Azure pro rychlý Start pro automatizované zálohování](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Konfigurace pomocí PowerShellu

K nakonfigurování automatizovaného zálohování můžete použít PowerShell. Než začnete, musíte:

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

Pokud není nainstalovaná nebo se nepovedlo zřídit, můžete si ho nainstalovat pomocí následujícího příkazu. Kromě názvu virtuálního počítače a skupiny prostředků musíte taky určit oblast (**$region**), ve které se virtuální počítač nachází. Zadejte typ licence pro váš virtuální počítač s SQL Server, a to pomocí [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), ať už máte licenci s průběžnými platbami nebo použitím vlastní licence. Další informace o licencování najdete v tématu [licencování modelu](licensing-model-azure-hybrid-benefit-ahb-change.md). 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Pokud rozšíření ještě není nainstalované, instalace rozšíření se restartuje SQL Server.

### <a name="verify-current-settings"></a><a id="verifysettings"></a> Ověřit aktuální nastavení

Pokud jste během zřizování povolili automatické zálohování, můžete ke kontrole aktuální konfigurace použít PowerShell. Spusťte příkaz **Get-AzVMSqlServerExtension** a prověřte vlastnost **AutoBackupSettings** :

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Mělo by se zobrazit výstup podobný následujícímu:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Pokud výstup ukazuje, že **možnost Povolit** je nastavená na **false**, musíte povolit automatizované zálohování. Dobrá zpráva je, že povolíte a nakonfigurujete automatizované zálohování stejným způsobem. Tyto informace najdete v další části.

> [!NOTE] 
> Pokud nastavení zkontrolujete hned po provedení změny, je možné, že se vrátí staré hodnoty konfigurace. Počkejte několik minut a zkontrolujte nastavení znovu, abyste se ujistili, že byly provedeny změny.

### <a name="configure-automated-backup"></a>Konfigurace automatizovaného zálohování
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

Pak pomocí příkazu **New-AzVMSqlServerAutoBackupConfig** povolte a nakonfigurujte nastavení automatického zálohování pro ukládání záloh do účtu úložiště Azure. V tomto příkladu se zálohy uchovávají po dobu 10 dnů. Druhý příkaz **set-AzVMSqlServerExtension** aktualizuje zadaný virtuální počítač Azure pomocí těchto nastavení.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Instalace a konfigurace agenta SQL Server IaaS může trvat několik minut.

> [!NOTE]
> K dispozici jsou další nastavení pro **New-AzVMSqlServerAutoBackupConfig** , která se vztahují pouze na SQL Server 2016 a automatizované zálohování v2. SQL Server 2014 nepodporuje následující nastavení: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours** a **LogBackupFrequencyInMinutes**. Pokud se pokusíte nakonfigurovat tato nastavení na virtuálním počítači s SQL Server 2014, nebude k dispozici žádná chyba, ale nastavení se nepoužije. Pokud chcete tato nastavení použít na virtuálním počítači s SQL Server 2016, přečtěte si téma [automatizovaná záloha v2 pro SQL Server 2016 virtuálních počítačů Azure](automated-backup.md).

Pokud chcete povolit šifrování, upravte předchozí skript tak, aby předával parametr **EnableEncryption** spolu s heslem (zabezpečeným řetězcem) pro parametr **CertificatePassword** . Následující skript povolí nastavení automatizovaného zálohování v předchozím příkladu a přidá šifrování.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

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
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorování

Pokud chcete monitorovat automatizované zálohování na SQL Server 2014, máte dvě hlavní možnosti. Vzhledem k tomu, že automatizované zálohování používá funkci spravovaného zálohování SQL Server, platí stejné postupy monitorování i pro obě.

Nejprve můžete dotazovat stav voláním [msdb. smart_admin. sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Nebo dotaz na funkci vracející tabulku [msdb. smart_admin. fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) .

> [!NOTE]
> Schéma pro spravovanou zálohu v SQL Server 2014 je **msdb.smart_admin**. V SQL Server 2016 se změnila na **msdb.managed_backup** a referenční témata používají toto novější schéma. U SQL Server 2014 ale musíte i nadále používat **smart_admin** schéma pro všechny spravované objekty zálohování.

Další možností je využít integrované funkce Databázová pošta pro oznámení.

1. Zavolejte uloženou proceduru [msdb. smart_admin. sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) , abyste přiřadili e-mailovou adresu parametru **SSMBackup2WANotificationEmailIds** . 
1. Povolí [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) odesílání e-mailů z virtuálního počítače Azure.
1. Pro konfiguraci Databázová pošta použijte server SMTP a uživatelské jméno. Databázová pošta můžete nakonfigurovat v SQL Server Management Studio nebo pomocí příkazů jazyka Transact-SQL. Další informace najdete v tématu [databázová pošta](/sql/relational-databases/database-mail/database-mail).
1. [Nakonfigurujte agenta SQL Server pro použití databázová pošta](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Ověřte, jestli je port SMTP povolený přes místní bránu firewall virtuálního počítače a skupinu zabezpečení sítě pro virtuální počítač.

## <a name="next-steps"></a>Další kroky

Automatizované zálohování konfiguruje spravovanou zálohu na virtuálních počítačích Azure. Proto je důležité [si projít dokumentaci pro spravovanou zálohu na SQL Server 2014](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure).

Další pokyny k zálohování a obnovení pro SQL Server na virtuálních počítačích Azure najdete v následujícím článku: [zálohování a obnovení pro SQL Server na virtuálních počítačích Azure](backup-restore.md).

Informace o dalších dostupných úlohách automatizace najdete v tématu [SQL Server rozšíření agenta IaaS](sql-server-iaas-agent-extension-automate-management.md).

Další informace o spouštění SQL Server na virtuálních počítačích Azure najdete v tématu [přehled SQL Server na virtuálních počítačích Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).