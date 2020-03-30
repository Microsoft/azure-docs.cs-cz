---
title: Automatické zálohování pro virtuální počítače Azure serveru SQL Server 2014
description: Vysvětluje funkci automatického zálohování pro virtuální počítače SQL Server 2014 spuštěné v Azure. Tento článek je specifický pro virtuální chody pomocí Správce prostředků.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c7dea85d8de17a0f65e6e73b5b5fbe619d464d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650320"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Automatické zálohování pro virtuální počítače SQL Serveru 2014 (Správce prostředků)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatické zálohování automaticky konfiguruje [spravované zálohování do Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pro všechny existující a nové databáze na virtuálním počítači Azure se systémem SQL Server 2014 Standard nebo Enterprise. To umožňuje konfigurovat pravidelné zálohování databáze, které využívají trvalé úložiště objektů blob Azure. Automatické zálohování závisí na [rozšíření agenta serveru SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Požadavky
Chcete-li používat automatické zálohování, zvažte následující předpoklady:

**Operační systém**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Verze/edice serveru SQL Server**:

- Standard SQL Serveru 2014
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Automatické zálohování funguje s SQL Server 2014. Pokud používáte SQL Server 2016/2017, můžete použít automatické zálohování v2 k zálohování databází. Další informace najdete [v tématu Automatické zálohování v2 pro SQL Server 2016 Virtuální počítače Azure](virtual-machines-windows-sql-automated-backup-v2.md).

**Konfigurace databáze**:

- Cílové databáze musí používat úplný model obnovení. Další informace o dopadu modelu úplného obnovení na zálohy naleznete v [tématu Zálohování v rámci modelu úplného obnovení](https://technet.microsoft.com/library/ms190217.aspx).
- Cílové databáze musí být na výchozí instanci serveru SQL Server. Rozšíření SQL Server IaaS nepodporuje pojmenované instance.

> [!NOTE]
> Automatické zálohování závisí na rozšíření agenta SERVERU SQL Server IaaS. Aktuální sql virtuální počítač galerie obrázky přidat toto rozšíření ve výchozím nastavení. Další informace naleznete v tématu [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Nastavení

Následující tabulka popisuje možnosti, které lze nakonfigurovat pro automatické zálohování. Skutečné kroky konfigurace se liší v závislosti na tom, jestli používáte portál Azure nebo příkazy Azure Windows PowerShell.

| Nastavení | Rozsah (výchozí) | Popis |
| --- | --- | --- |
| **Automatické zálohování** | Povolit/zakázat (zakázáno) | Povolí nebo zakáže automatické zálohování pro virtuální počítač Azure se systémem SQL Server 2014 Standard nebo Enterprise. |
| **Doba uchování** | 1-30 dní (30 dní) | Počet dní pro zachování zálohy. |
| **Účet úložiště** | Účet služby Azure Storage | Účet úložiště Azure, který se má použít pro ukládání souborů automatického zálohování do úložiště objektů blob. Kontejner je vytvořen v tomto umístění pro uložení všech záložních souborů. Konvence pojmenování záložních souborů zahrnuje datum, čas a název počítače. |
| **Šifrování** | Povolit/zakázat (zakázáno) | Povolí nebo zakáže šifrování. Pokud je šifrování povoleno, certifikáty použité k obnovení zálohy jsou `automaticbackup` umístěny v zadaném účtu úložiště ve stejném kontejneru pomocí stejné konvence pojmenování. Pokud se heslo změní, je s tímto heslem vygenerován nový certifikát, ale starý certifikát zůstává k obnovení předchozích záloh. |
| **Heslo** | Text hesla | Heslo pro šifrovací klíče. To je vyžadováno pouze v případě, že je povoleno šifrování. Chcete-li obnovit šifrovanou zálohu, musíte mít správné heslo a související certifikát, který byl použit v době, kdy byla záloha provedena. |


## <a name="configure-new-vms"></a>Konfigurace nových virtuálních počítače

Na webu Azure Portal nakonfigurujte automatické zálohování při vytváření nového virtuálního počítače SQL Serveru 2014 v modelu nasazení Správce prostředků.

Na kartě **Nastavení serveru SQL Server** přejděte dolů na Automatické **zálohování** a vyberte **Povolit**. Následující snímek obrazovky portálu Azure zobrazuje nastavení **automatického zálohování SQL.**

![Konfigurace automatického zálohování SQL na webu Azure Portal](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Konfigurace existujících virtuálních počítače

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U existujících virtuálních počítačů SQL Serveru můžete povolit a zakázat automatické zálohování, změnit dobu uchování, zadat účet úložiště a povolit šifrování z portálu Azure. 

Přejděte na [prostředek virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) pro váš virtuální počítač SQL Server 2014 a vyberte **Zálohy**. 

![Automatické zálohování SQL pro existující virtuální počítače](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Po dokončení vyberte tlačítko **Použít** v dolní části stránky **Zálohy** a uložte změny.

Pokud poprvé povolíte automatické zálohování, Azure nakonfiguruje agenta SQL Server IaaS na pozadí. Během této doby portál Azure nemusí zobrazit, že automatické zálohování je nakonfigurovaný. Počkejte několik minut, než bude agent nainstalován a nakonfigurován. Za to, že portál Azure bude odrážet nové nastavení.

> [!NOTE]
> Automatické zálohování můžete také nakonfigurovat pomocí šablony. Další informace naleznete v [tématu Azure quickstart šablona pro automatické zálohování](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Konfigurace pomocí PowerShellu

Pomocí prostředí PowerShell můžete nakonfigurovat automatické zálohování. Než začnete, musíte:

- [Stáhněte a nainstalujte nejnovější Azure PowerShell](https://aka.ms/webpi-azps).
- Otevřete prostředí Windows PowerShell a přidrdejte ho ke svému účtu k příkazu **Connect-AzAccount.** 

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Instalace rozšíření SQL IaaS
Pokud jste zřídit sql server virtuální počítač z portálu Azure, sql server IaaS rozšíření by již nainstalovat. Můžete určit, pokud je nainstalován pro váš virtuální počítač voláním **příkazu Get-AzVM** a zkoumání **extensions** vlastnost.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Pokud je nainstalováno rozšíření agenta SQL Server IaaS, měli byste vidět, že jsou uvedeny jako "SqlIaaSAgent" nebo "SQLIaaSExtension". **ProvisioningState** pro rozšíření by měl také zobrazit "Úspěšné".

Pokud není nainstalován nebo se nezdařilo zřízení, můžete jej nainstalovat pomocí následujícího příkazu. Kromě názvu virtuálního počítače a skupiny prostředků musíte také zadat oblast **($region),** ve které se váš virtuální počítač nachází. Zadejte typ licence pro váš virtuální počítač SQL Server a vyberte si mezi průběžnou platbou nebo vlastní licencí prostřednictvím [hybridní výhody Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Další informace o licencování naleznete v tématu [licenční model](virtual-machines-windows-sql-ahb.md). 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Pokud rozšíření ještě není nainstalováno, instalace rozšíření restartuje službu SQL Server.

### <a name="verify-current-settings"></a><a id="verifysettings"></a>Ověření aktuálního nastavení

Pokud jste povolili automatické zálohování během zřizování, můžete použít PowerShell ke kontrole aktuální konfigurace. Spusťte příkaz **Get-AzVMSqlServerExtension** a zkontrolujte vlastnost **AutoBackupSettings:**

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Měli byste získat výstup podobný následujícímu:

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

Pokud výstup ukazuje, že **Enable** je nastavena na **False**, pak budete muset povolit automatické zálohování. Dobrou zprávou je, že povolíte a nakonfigurujete automatické zálohování stejným způsobem. Tyto informace naleznete v další části.

> [!NOTE] 
> Pokud zkontrolujete nastavení ihned po provedení změny, je možné, že získáte zpět staré hodnoty konfigurace. Počkejte několik minut a zkontrolujte nastavení znovu, abyste se ujistili, že byly změny použity.

### <a name="configure-automated-backup"></a>Konfigurace automatického zálohování
Pomocí prostředí PowerShell můžete povolit automatické zálohování a také kdykoli upravit jeho konfiguraci a chování.

Nejprve vyberte nebo vytvořte účet úložiště pro záložní soubory. Následující skript vybere účet úložiště nebo jej vytvoří, pokud neexistuje.

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
> Automatické zálohování nepodporuje ukládání záloh v úložišti premium, ale může trvat zálohy z disků virtuálních počítačů, které používají úložiště Premium.

Potom pomocí příkazu **New-AzVMSqlServerAutoBackupConfig** povolte a nakonfigurujte nastavení automatického zálohování pro ukládání záloh v účtu úložiště Azure. V tomto příkladu jsou zálohy zachovány po dobu 10 dnů. Druhý příkaz **Set-AzVMSqlServerExtension**aktualizuje zadaný virtuální počítač Azure s těmito nastaveními.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Instalace a konfigurace agenta SQL Server IaaS agenta serveru SQL Server může trvat několik minut.

> [!NOTE]
> Existují další nastavení pro **New-AzVMSqlServerAutoBackupConfig,** které platí pouze pro SQL Server 2016 a automatické zálohování v2. SQL Server 2014 nepodporuje následující nastavení: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**a **LogBackupFrequencyInMinutes**. Pokud se pokusíte nakonfigurovat tato nastavení na virtuálním počítači SQL Server 2014, nedojde k žádné chybě, ale nastavení se nepoužijí. Pokud chcete použít tato nastavení na virtuálním počítači SQL Server 2016, přečtěte si informace [o automatickém zálohování v2 pro virtuální počítače Azure 2016.](virtual-machines-windows-sql-automated-backup-v2.md)

Chcete-li povolit šifrování, upravte předchozí skript tak, aby předal parametr **EnableEncryption** spolu s heslem (zabezpečeným řetězcem) pro parametr **CertificatePassword.** Následující skript umožňuje nastavení automatického zálohování v předchozím příkladu a přidá šifrování.

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

Chcete-li ověřit, zda jsou nastavení použita, [ověřte konfiguraci automatického zálohování](#verifysettings).

### <a name="disable-automated-backup"></a>Zakázat automatické zálohování

Chcete-li funkci Automatické zálohování zakázat, spusťte stejný skript bez parametru **-Enable** příkazu **New-AzVMSqlServerAutoBackupConfig.** Absence parametru **-Enable** signalizuje příkaz k zakázání funkce. Stejně jako při instalaci může zakácet automatické zálohování několik minut.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Ukázkový skript

Následující skript obsahuje sadu proměnných, které můžete přizpůsobit tak, aby povolalo a nakonfigurovalo automatické zálohování pro váš virtuální počítač. Ve vašem případě může být nutné přizpůsobit skript na základě vašich požadavků. Například byste museli provést změny, pokud chcete zakázat zálohování systémových databází nebo povolit šifrování.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

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

Chcete-li sledovat automatické zálohování na SQL Server 2014, máte dvě hlavní možnosti. Vzhledem k tomu, že automatické zálohování používá funkci spravovaného zálohování serveru SQL Server, platí pro obě techniky stejné monitorování.

Nejprve můžete dotazování stavu voláním [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Nebo dotaz [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tabulka oceňují funkce.

> [!NOTE]
> Schéma spravované zálohy na serveru SQL Server 2014 je **msdb.smart_admin**. V SQL Server 2016 to změnilo na **msdb.managed_backup**a referenční témata použít toto novější schéma. Ale pro SQL Server 2014 je nutné nadále používat **schéma smart_admin** pro všechny objekty spravované zálohy.

Další možností je využít integrované funkce Pošta databáze pro oznámení.

1. Volání [msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) uložená procedura přiřadit e-mailovou adresu **SSMBackup2WANotificationEmailIds** parametr. 
1. Povolit [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) k odesílání e-mailů z virtuálního počítače Azure.
1. Ke konfiguraci služby Database Mail použijte server SMTP a uživatelské jméno. Databázovou poštu můžete konfigurovat ve správě serveru SQL Server nebo pomocí příkazů Transact-SQL. Další informace naleznete v [tématu Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Nakonfigurujte agenta serveru SQL Server tak, aby používal databázovou poštu](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Ověřte, zda je port SMTP povolen prostřednictvím brány firewall místního virtuálního počítače i skupiny zabezpečení sítě pro virtuální počítače.

## <a name="next-steps"></a>Další kroky

Automatické zálohování konfiguruje spravované zálohování na virtuálních počítačích Azure. Proto je důležité [zkontrolovat dokumentaci pro spravované zálohování na SQL Server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Další pokyny pro zálohování a obnovení pro SQL Server na virtuálních počítačích Azure najdete v následujícím článku: [Zálohování a obnovení pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-backup-recovery.md).

Informace o dalších dostupných úlohách automatizace naleznete v tématu [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

Další informace o spuštění SQL Serveru na virtuálních počítačích Azure najdete v tématu [SQL Server ve virtuálních počítačích Azure přehled](virtual-machines-windows-sql-server-iaas-overview.md).
