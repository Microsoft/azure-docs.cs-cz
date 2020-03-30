---
title: Automatické zálohování virtuálních počítačů Azure v 2016/2017 | Dokumenty společnosti Microsoft
description: Vysvětluje funkci automatického zálohování pro virtuální počítače SQL Server 2016/2017 spuštěné v Azure. Tento článek je specifický pro virtuální chody pomocí Správce prostředků.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 458012982531e228f7c4968f29e79e8b2e29aa48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651423"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Automatické zálohování v2 pro virtuální počítače Azure (Správce prostředků)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatické zálohování v2 automaticky konfiguruje [spravované zálohování do Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pro všechny existující a nové databáze na virtuálním počítači Azure se systémem SQL Server 2016/2017 Standard, Enterprise nebo Developer editions. To umožňuje konfigurovat pravidelné zálohování databáze, které využívají trvalé úložiště objektů blob Azure. Automatické zálohování v2 závisí na [rozšíření agenta SERVERU SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Požadavky
Chcete-li použít automatické zálohování v2, zkontrolujte následující požadavky:

**Operační systém**:

- Windows Server 2012 R2
- Windows Server 2016

**Verze/edice serveru SQL Server**:

- SQL Server 2016: Vývojář, Standard nebo Enterprise
- SQL Server 2017: Vývojář, Standard nebo Enterprise

> [!IMPORTANT]
> Automatické zálohování v2 funguje s SQL Server 2016 nebo vyšší. Pokud používáte SQL Server 2014, můžete použít automatické zálohování v1 k zálohování databází. Další informace najdete [v tématu Automatické zálohování pro virtuální počítače Azure serveru SQL Server 2014](virtual-machines-windows-sql-automated-backup.md).

**Konfigurace databáze**:

- Cílové databáze musí používat úplný model obnovení. Další informace o dopadu modelu úplného obnovení na zálohy naleznete v [tématu Zálohování v rámci modelu úplného obnovení](https://technet.microsoft.com/library/ms190217.aspx).
- Systémové databáze nemusí používat úplný model obnovení. Pokud však požadujete zálohprotokolu, které mají být přijata pro model nebo MSDB, je nutné použít úplný model obnovení.
- Cílové databáze musí být buď na výchozí instanci serveru SQL Server, nebo [správně nainstalované](virtual-machines-windows-sql-server-iaas-faq.md#administration) pojmenované instanci. 

> [!NOTE]
> Automatické zálohování závisí na **rozšíření agenta serveru SQL Server IaaS**. Aktuální sql virtuální počítač galerie obrázky přidat toto rozšíření ve výchozím nastavení. Další informace naleznete v tématu [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které lze nakonfigurovat pro automatické zálohování v2. Skutečné kroky konfigurace se liší v závislosti na tom, jestli používáte portál Azure nebo příkazy Azure Windows PowerShell.

### <a name="basic-settings"></a>Základní nastavení

| Nastavení | Rozsah (výchozí) | Popis |
| --- | --- | --- |
| **Automatické zálohování** | Povolit/zakázat (zakázáno) | Povolí nebo zakáže automatické zálohování pro virtuální počítač Azure se systémem SQL Server 2016/2017 Developer, Standard nebo Enterprise. |
| **Doba uchování** | 1-30 dní (30 dní) | Počet dní pro zachování záloh. |
| **Účet úložiště** | Účet služby Azure Storage | Účet úložiště Azure, který se má použít pro ukládání souborů automatického zálohování do úložiště objektů blob. Kontejner je vytvořen v tomto umístění pro uložení všech záložních souborů. Konvence pojmenování záložních souborů obsahuje identifikátor GUID data, času a databáze. |
| **Šifrování** |Povolit/zakázat (zakázáno) | Povolí nebo zakáže šifrování. Pokud je šifrování povoleno, certifikáty použité k obnovení zálohy jsou umístěny v zadaném účtu úložiště. Používá stejný kontejner **automatického zálohování** se stejnou konvencí pojmenování. Pokud se heslo změní, je s tímto heslem vygenerován nový certifikát, ale starý certifikát zůstává k obnovení předchozích záloh. |
| **Heslo** |Text hesla | Heslo pro šifrovací klíče. Toto heslo je vyžadováno pouze v případě, že je povoleno šifrování. Chcete-li obnovit šifrovanou zálohu, musíte mít správné heslo a související certifikát, který byl použit v době, kdy byla záloha provedena. |

### <a name="advanced-settings"></a>Upřesnit nastavení

| Nastavení | Rozsah (výchozí) | Popis |
| --- | --- | --- |
| **Zálohy systémových databází** | Povolit/zakázat (zakázáno) | Pokud je tato funkce povolena, také zálohuje systémové databáze: Master, MSDB a Model. Pro databáze MSDB a model, ověřte, zda jsou v režimu úplné obnovení, pokud chcete, aby zálohprotokolu, které mají být přijata. Zálohy protokolů nejsou nikdy převzaty pro master. A žádné zálohy jsou převzaty pro TempDB. |
| **Plán zálohování** | Ruční/automatizované (automatizované) | Ve výchozím nastavení je plán zálohování automaticky určen na základě růstu protokolu. Ruční plán zálohování umožňuje uživateli zadat časové okno pro zálohování. V tomto případě zálohy probíhají pouze na zadané frekvenci a během zadaného časového období daného dne. |
| **Úplná frekvence zálohování** | Denně/Týdně | Frekvence úplných záloh. V obou případech úplné zálohy začít během dalšího časového období. Je-li vybrána možnost Týdně, mohou zálohy tužit více dní, dokud nebudou všechny databáze úspěšně zálohovány. |
| **Úplný čas spuštění zálohování** | 00:00 – 23:00 (01:00) | Počáteční čas daného dne, během kterého může probíhat úplné zálohování. |
| **Úplné okno času zálohování** | 1 – 23 hodin (1 hod.) | Doba trvání časového období daného dne, během kterého může probíhat úplné zálohování. |
| **Protokolovat frekvenci zálohování** | 5 – 60 minut (60 minut) | Frekvence záloh protokolu. |

## <a name="understanding-full-backup-frequency"></a>Principy úplné frekvence zálohování
Je důležité pochopit rozdíl mezi denní a týdenní úplné zálohy. Zvažte následující dva příklady scénářů.

### <a name="scenario-1-weekly-backups"></a>Scénář 1: Týdenní zálohy
Máte virtuální modul SQL Server, který obsahuje několik velkých databází.

V pondělí povolíte automatické zálohování v2 s následujícím nastavením:

- Plán zálohování: **Ruční**
- Plná frekvence zálohování: **Týdně**
- Úplný čas spuštění zálohy: **01:00**
- Úplné okno doby zálohování: **1 hodina**

To znamená, že další dostupné záložní okno je úterý v 1:00 po dobu 1 hodiny. V té době automatické zálohování začne zálohovat databáze jeden po druhém. V tomto scénáři databáze jsou dostatečně velké, že úplné zálohy dokončit pro první pár databází. Po jedné hodině však nebyly zálohovány všechny databáze.

V takovém případě začne automatické zálohování zálohování zbývajícídatabáze následující den, ve středu v 1:00 po dobu jedné hodiny. Pokud nebyly všechny databáze zálohovány v té době, pokusí se znovu další den ve stejnou dobu. To pokračuje, dokud všechny databáze byly úspěšně zálohovány.

Poté, co znovu dosáhne úterý, automatické zálohování začne znovu zálohovat všechny databáze.

Tento scénář ukazuje, že automatické zálohování funguje pouze v zadaném časovém okně a každá databáze je zálohována jednou týdně. To také ukazuje, že je možné pro zálohy span více dní v případě, že není možné dokončit všechny zálohy v jednom dni.

### <a name="scenario-2-daily-backups"></a>Scénář 2: Denní zálohy
Máte virtuální modul SQL Server, který obsahuje několik velkých databází.

V pondělí povolíte automatické zálohování v2 s následujícím nastavením:

- Plán zálohování: Ruční
- Plná frekvence zálohování: Denně
- Úplný čas spuštění zálohy: 22:00
- Úplné okno doby zálohování: 6 hodin

To znamená, že další dostupné záložní okno je pondělí v 10 hodin po dobu 6 hodin. V té době automatické zálohování začne zálohovat databáze jeden po druhém.

Pak, v úterý v 10 po dobu 6 hodin, úplné zálohy všech databází začít znovu.

> [!IMPORTANT]
> Při plánování dennízálohy se doporučuje naplánovat široké časové okno, aby bylo zajištěno, že všechny databáze mohou být zálohovány během této doby. To je obzvláště důležité v případě, kdy máte velké množství dat zálohovat.

## <a name="configure-new-vms"></a>Konfigurace nových virtuálních počítače

Na webu Azure Portal nakonfigurujte automatické zálohování v2 při vytváření nového virtuálního počítače SQL Serveru 2016 nebo 2017 v modelu nasazení Správce prostředků.

Na kartě **Nastavení serveru SQL Server** vyberte **povolit v** části Automatické **zálohování**. Následující snímek obrazovky portálu Azure zobrazuje nastavení **automatického zálohování SQL.**

![Konfigurace automatického zálohování SQL na webu Azure Portal](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Automatické zálohování v2 je ve výchozím nastavení zakázáno.

## <a name="configure-existing-vms"></a>Konfigurace existujících virtuálních počítače

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U existujících virtuálních počítačů SQL Serveru přejděte na [prostředek virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) a pak vyberte **Zálohy** pro konfiguraci automatického zálohování.

![Automatické zálohování SQL pro existující virtuální počítače](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)


Po dokončení kliknutím na tlačítko **Použít** v dolní části stránky **Nastavení záloh** uložte změny.

Pokud poprvé povolíte automatické zálohování, Azure nakonfiguruje agenta SQL Server IaaS na pozadí. Během této doby portál Azure nemusí zobrazit, že automatické zálohování je nakonfigurovaný. Počkejte několik minut, než bude agent nainstalován, nakonfigurován. Za to, že portál Azure bude odrážet nové nastavení.

## <a name="configure-with-powershell"></a>Konfigurace pomocí PowerShellu

Pomocí prostředí PowerShell můžete nakonfigurovat automatické zálohování v2. Než začnete, musíte:

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

Pokud není nainstalován nebo se nezdařilo zřízení, můžete jej nainstalovat pomocí následujícího příkazu. Kromě názvu virtuálního počítače a skupiny prostředků musíte také zadat oblast **($region),** ve které se váš virtuální počítač nachází.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a>Ověření aktuálního nastavení
Pokud jste povolili automatické zálohování během zřizování, můžete použít PowerShell ke kontrole aktuální konfigurace. Spusťte příkaz **Get-AzVMSqlServerExtension** a zkontrolujte vlastnost **AutoBackupSettings:**

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Měli byste získat výstup podobný následujícímu:

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

Pokud výstup ukazuje, že **Enable** je nastavena na **False**, pak budete muset povolit automatické zálohování. Dobrou zprávou je, že povolíte a nakonfigurujete automatické zálohování stejným způsobem. Tyto informace naleznete v další části.

> [!NOTE] 
> Pokud zkontrolujete nastavení ihned po provedení změny, je možné, že získáte zpět staré hodnoty konfigurace. Počkejte několik minut a zkontrolujte nastavení znovu, abyste se ujistili, že byly změny použity.

### <a name="configure-automated-backup-v2"></a>Konfigurace automatického zálohování v2
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

Potom pomocí příkazu **New-AzVMSqlServerAutoBackupConfig** povolte a nakonfigurujte nastavení automatického zálohování v2 pro ukládání záloh v účtu úložiště Azure. V tomto příkladu jsou zálohy nastaveny tak, aby byly zachovány po dobu 10 dnů. Zálohy systémových databází jsou povoleny. Úplné zálohování je naplánováno na týdenní s časovým oknem začínajícím ve 20:00 po dobu dvou hodin. Zálohy protokolů jsou naplánovány na každých 30 minut. Druhý příkaz **Set-AzVMSqlServerExtension**aktualizuje zadaný virtuální počítač Azure s těmito nastaveními.

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

Instalace a konfigurace agenta SQL Server IaaS agenta serveru SQL Server může trvat několik minut. 

Chcete-li povolit šifrování, upravte předchozí skript tak, aby předal parametr **EnableEncryption** spolu s heslem (zabezpečeným řetězcem) pro parametr **CertificatePassword.** Následující skript umožňuje nastavení automatického zálohování v předchozím příkladu a přidá šifrování.

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
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

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
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorování

Chcete-li sledovat automatické zálohování na SQL Server 2016/2017, máte dvě hlavní možnosti. Vzhledem k tomu, že automatické zálohování používá funkci spravovaného zálohování serveru SQL Server, platí pro obě techniky stejné monitorování.

Nejprve můžete dotazování stavu voláním [msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Nebo dotaz [msdb.managed_backup.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tabulka-hodnota funkce.

Další možností je využít integrované funkce Pošta databáze pro oznámení.

1. Volání [msdb.managed_backup.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) uloženou proceduru přiřadit e-mailovou adresu **SSMBackup2WANotificationEmailIds** parametr. 
1. Povolit [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) k odesílání e-mailů z virtuálního počítače Azure.
1. Ke konfiguraci služby Database Mail použijte server SMTP a uživatelské jméno. Databázovou poštu můžete konfigurovat ve správě serveru SQL Server nebo pomocí příkazů Transact-SQL. Další informace naleznete v [tématu Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Nakonfigurujte agenta serveru SQL Server tak, aby používal databázovou poštu](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Ověřte, zda je port SMTP povolen prostřednictvím brány firewall místního virtuálního počítače i skupiny zabezpečení sítě pro virtuální počítače.

## <a name="next-steps"></a>Další kroky
Automatické zálohování v2 konfiguruje spravované zálohování na virtuálních počítačích Azure. Proto je důležité [zkontrolovat dokumentaci pro spravované zálohování](https://msdn.microsoft.com/library/dn449496.aspx) pochopit chování a důsledky.

Další pokyny pro zálohování a obnovení pro SQL Server na virtuálních počítačích Azure najdete v následujícím článku: [Zálohování a obnovení pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-backup-recovery.md).

Informace o dalších dostupných úlohách automatizace naleznete v tématu [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

Další informace o spuštění SQL Serveru na virtuálních počítačích Azure najdete v tématu [SQL Server ve virtuálních počítačích Azure přehled](virtual-machines-windows-sql-server-iaas-overview.md).

