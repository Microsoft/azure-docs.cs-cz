---
title: Automatizované zálohování v2 pro virtuální počítače Azure s SQL Server 2016 a 2017 | Dokumentace Microsoftu
description: Vysvětluje funkci automatizované zálohování pro SQL Server 2016 a 2017 virtuální počítače spuštěné v Azure. Tento článek je určený speciálně pro virtuální počítače pomocí Resource Manageru.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: jroth
ms.openlocfilehash: 664a0036b8aa753de9636688d22afff0163f031f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246816"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Automatizované zálohování v2 pro Azure Virtual Machines (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatizované zálohování v2 automaticky nakonfiguruje [spravovaného zálohování Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pro všechny stávající i nové databáze na Virtuálním počítači Azure s edicemi SQL serveru 2016 a 2017 Standard, Enterprise nebo pro vývojáře. To umožňuje nakonfigurovat pravidelné zálohy, které využívají úložiště objektů blob v Azure odolné. Automatizované zálohování v2 závisí [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Požadavky
Pomocí automatizovaného zálohování v2, projděte si následující požadavky:

**Operační systém**:

- Windows Server 2012 R2
- Windows Server 2016

**Verze a edice systému SQL Server**:

- Systému SQL Server 2016: Developer, Standard nebo Enterprise
- Systému SQL Server 2017: Developer, Standard nebo Enterprise

> [!IMPORTANT]
> Automatizované zálohování v2 funguje s SQL serverem 2016 nebo novější. Pokud používáte SQL Server 2014, můžete použít v1 automatizovaného zálohování k zálohování databází. Další informace najdete v tématu [automatizované zálohování pro SQL Server 2014 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

**Konfigurace databáze**:

- Cílová databáze musí použít model úplného obnovení. Další informace o dopadu úplném modelu obnovení na zálohování, naleznete v tématu [zálohování v části the úplný Model obnovení](https://technet.microsoft.com/library/ms190217.aspx).
- Systémové databáze není potřeba použít model úplného obnovení. Pokud budete potřebovat zálohy protokolů, které mají být provedeny pro Model nasazení nebo databázi MSDB, však musíte použít model úplného obnovení.
- Cílová databáze musí být na výchozí instanci SQL serveru. Rozšíření SQL Server IaaS nepodporuje pojmenované instance.

> [!NOTE]
> Automatizované zálohování se může spolehnout **rozšíření agenta SQL Server IaaS**. Aktuální Image SQL z Galerie virtuálních počítačů přidat toto rozšíření ve výchozím nastavení. Další informace najdete v tématu [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které je možné nakonfigurovat pro automatizované zálohování v2. Skutečný konfigurační kroky se liší v závislosti na tom, zda používáte portál Azure portal nebo příkazů Windows Powershellu Azure.

### <a name="basic-settings"></a>Základní nastavení

| Nastavení | Rozsah (výchozí) | Popis |
| --- | --- | --- |
| **Automatizované zálohování** | Povolí nebo zakáže (zakázáno) | Povolí nebo zakáže automatické zálohování pro virtuální počítač Azure s SQL serverem 2016 a 2017 Developer, Standard nebo Enterprise. |
| **Doba uchování** | 1 – 30 dnů (30 dnů) | Počet dní uchování záloh. |
| **Účet úložiště** | Účet služby Azure Storage | Účet úložiště Azure k ukládání souborů automatizovaného zálohování ve službě blob storage. Kontejner se vytvoří v tomto umístění pro uložení všechny záložní soubory. Zásady vytváření názvů záložní soubor obsahuje data, času a GUID databáze. |
| **Šifrování** |Povolí nebo zakáže (zakázáno) | Povolí nebo zakáže šifrování. Když je povoleno šifrování, certifikátů používaných pro obnovení zálohy jsou umístěny v zadaný účet úložiště. Používá stejný **automaticbackup** kontejner se stejné zásady vytváření názvů. Pokud se změní heslo, se toto heslo se vygeneruje nový certifikát, ale starý certifikát zůstane k obnovení předchozího záloh. |
| **Heslo** |Heslo text | Heslo šifrovacích klíčů. Toto heslo je jenom nutné, pokud je povolené šifrování. Aby bylo možné obnovit šifrované zálohování, musíte mít správné heslo a související certifikát, který byl použit v době, kdy bylo provedeno zálohování. |

### <a name="advanced-settings"></a>Upřesnit nastavení

| Nastavení | Rozsah (výchozí) | Popis |
| --- | --- | --- |
| **Zálohování databáze systému** | Povolí nebo zakáže (zakázáno) | Pokud povolena, tato funkce také vytvoří zálohu systémové databáze: hlavní server, databázi MSDB a modelu. Databáze MSDB a Model ověřte, že jsou v režimu úplného obnovení, pokud chcete, aby Povolí provedení záloh protokolů. Zálohy protokolů se nikdy neobsadila. pro hlavní server. A žádné zálohy jsou prováděny pro databázi TempDB. |
| **Plán zálohování** | Ruční nebo automatizované (automatizované) | Ve výchozím nastavení plán zálohování automaticky závisí na protokolu růst. Ruční plán zálohování umožňuje uživateli zadat časový interval pro zálohy. V takovém případě zálohy provádět pouze v zadaných intervalech a během zadaného časového intervalu daného dne. |
| **Frekvence úplného zálohování** | Denně nebo týdně | Frekvence úplného zálohování. V obou případech úplné zálohování začít během časového intervalu pro dalším naplánovaném čase. Při výběru týdenních záloh může trvají více dní, dokud všechny databáze úspěšně zálohovali. |
| **Čas spuštění úplného zálohování** | 00:00 – 23:00 (01:00) | Počáteční čas daného dne, během které úplné zálohování lze provést. |
| **Časový interval úplného zálohování** | 1 – 23 hodin (1 hodina) | Doba trvání časového okna daného dne, během které úplné zálohování lze provést. |
| **Frekvence zálohování protokolu** | 5 – 60 minut (60 minut) | Četnost záloh protokolu. |

## <a name="understanding-full-backup-frequency"></a>Principy frekvence úplného zálohování
Je důležité porozumět rozdílu mezi denní nebo týdenní úplné zálohování. Vezměte v úvahu následující dva příklady.

### <a name="scenario-1-weekly-backups"></a>Scénář 1: Týdenní zálohy
Máte virtuálního počítače SQL serveru, který obsahuje počet velkých databází.

V pondělí povolíte automatizované zálohování v2 s následujícím nastavením:

- Plán zálohování: **ruční**
- Úplné četnost záloh: **týdenní**
- Čas spuštění zálohování úplné: **01:00**
- Časový interval úplného zálohování: **1 hodina**

To znamená, že dalšího intervalu zálohování k dispozici je úterý v 1: 00 1 hodinu. V tu chvíli začne automatizovaného zálohování, zálohování databází jeden po druhém. V tomto scénáři vaše databáze jsou dostatečně velký, úplné zálohy dokončit první několik databází. Ale po jedné hodině všechny databáze byly zálohovány.

Pokud k tomu dojde, začne automatizovaného zálohování, zálohování zbývající databází dalšího dne, středu v 1: 00 na jednu hodinu. Pokud ne všechny databáze byly zálohovány v daném čase, pokusí znovu následující den ve stejnou dobu. Tento postup se opakuje, dokud se všechny databáze byly úspěšně zálohovány.

Po nebude dosaženo úterý automatizovaného zálohování začne znovu zálohování všech databází.

Tento scénář ukazuje, že automatizovaného zálohování pracuje pouze v rámci zadaného časového okna a každá databáze je zálohovat jednou za týden. To také ukazuje, že je možné pro zálohy trvají více dní v případě, kdy není možné dokončit všechny zálohy za jeden den.

### <a name="scenario-2-daily-backups"></a>Scénář 2: Denní zálohy
Máte virtuálního počítače SQL serveru, který obsahuje počet velkých databází.

V pondělí povolíte automatizované zálohování v2 s následujícím nastavením:

- Plán zálohování: ruční
- Úplné četnost záloh: denně
- Čas spuštění zálohování úplné: 22:00
- Časový interval úplného zálohování: 6 hodin

To znamená, že dalšího intervalu zálohování k dispozici od pondělí ve 22: 00, 6 hodin. V tu chvíli začne automatizovaného zálohování, zálohování databází jeden po druhém.

Úterý v 10 6 hodin, úplné zálohování všech databází spusťte znovu.

> [!IMPORTANT]
> Při plánování denní zálohy, doporučujeme, abyste naplánovali široké časové okno k zajištění, že se že v tuto chvíli je možné zálohovat všechny databáze. To je obzvláště důležité v případě, kdy máte velký objem dat pro zálohování.

## <a name="configure-in-the-portal"></a>Konfigurace na portálu

Na webu Azure portal můžete použít ke konfiguraci automatizovaného zálohování v2 během zřizování nebo pro stávající virtuální počítače SQL serveru 2016. 2017.

## <a name="configure-for-new-vms"></a>Konfigurace pro nové virtuální počítače

Konfigurace automatizovaného zálohování v2, když vytvoříte nový SQL Server 2016 nebo 2017 virtuálního počítače v modelu nasazení Resource Manageru pomocí webu Azure portal.

V **nastavení systému SQL Server** vyberte **automatizované zálohování**. Ukazuje následující snímek obrazovky Azure portal **automatizované zálohování SQL** nastavení.

![Konfigurace automatizovaného zálohování SQL na webu Azure portal](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Automatizované zálohování v2 je ve výchozím nastavení zakázána.

## <a name="configure-existing-vms"></a>Konfigurace existujících virtuálních počítačů

Existující virtuální počítače systému SQL Server vyberte virtuální počítač systému SQL Server. Vyberte **konfigurace systému SQL Server** části virtuální počítač **nastavení**.

![Automatizované zálohování SQL pro stávající virtuální počítače](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)

V **konfigurace systému SQL Server** nastavení, klikněte na tlačítko **upravit** tlačítko v části pro automatizovaná zálohování.

![Konfigurace automatizovaného zálohování SQL pro stávající virtuální počítače](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration-edit.png)

Až budete hotovi, klikněte na tlačítko **OK** tlačítko v dolní části **konfigurace systému SQL Server** nastavení uložte provedené změny.

Pokud povolíte automatizované zálohování pro první, Azure nakonfiguruje agenta SQL Server IaaS na pozadí. Během této doby nemusí zobrazit na webu Azure portal, automatické zálohování je nakonfigurované. Počkejte několik minut, než agent nainstalován, nakonfigurován. Na webu Azure portal, projeví se nová nastavení.

## <a name="configure-with-powershell"></a>Konfigurovat pomocí Powershellu

Prostředí PowerShell můžete použít ke konfiguraci automatizovaného zálohování v2. Než začnete, musíte mít:

- [Stáhněte a nainstalujte nejnovější Azure PowerShell](https://aka.ms/webpi-azps).
- Otevřete prostředí Windows PowerShell a přidružíte ho k účtu se **Connect-AzureRmAccount** příkazu.

### <a name="install-the-sql-iaas-extension"></a>Instalace rozšíření SQL IaaS
Zřízení virtuálního počítače s SQL serverem na webu Azure Portal by již nainstalována rozšíření SQL Server IaaS. Můžete určit, pokud je nainstalován pro váš virtuální počítač pomocí volání **Get-AzureRmVM** příkazu a podíváte **rozšíření** vlastnost.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Pokud je nainstalovaná rozšíření agenta SQL Server IaaS, měli byste vidět, že je uveden jako "SqlIaaSAgent" nebo "SQLIaaSExtension". **Stav zřizování** pro rozšíření by měl také zobrazí "ÚSPĚCH". 

Pokud není nainstalovaná nebo se nepovedlo zřídit, můžete ho nainstalovat pomocí následujícího příkazu. Kromě virtuální počítač a skupinou prostředků, musíte zadat také oblast (**$region**), který váš virtuální počítač se nachází v.

```powershell
$region = “EASTUS2”
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region 
```

### <a id="verifysettings"></a> Ověřit aktuální nastavení
Pokud jste povolili automatizované zálohování během zřizování, můžete pomocí prostředí PowerShell zkontrolovat konfiguraci vašeho aktuální ho. Spustit **Get-AzureRmVMSqlServerExtension** příkaz a zkontrolujte **AutoBackupSettings** vlastnost:

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

By měl získat výstup podobný následujícímu:

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

Pokud výstup ukazuje, že **povolit** je nastavena na **False**, budete muset povolit automatické zálohování. Dobrou zprávou je, že můžete povolit a nakonfigurovat automatizované zálohování stejným způsobem. V části Další informace.

> [!NOTE] 
> Pokud zaškrtnete okamžitě po provedení změny nastavení, je možné, že se dostanete zpět původní hodnoty konfigurace. Počkejte pár minut a zkontrolujte nastavení znovu a ujistěte se, že vaše změny byly použity.

### <a name="configure-automated-backup-v2"></a>Konfigurace automatizovaného zálohování v2
Prostředí PowerShell můžete povolit automatické zálohování i či kdykoli upravit jeho konfiguraci a chování. 

Nejprve vyberte nebo vytvořte účet úložiště pro zálohování souborů. Následující skript vybere účtu úložiště nebo ji vytvoří, pokud neexistuje.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> Automatizované zálohování nepodporuje ukládání záloh ve službě storage úrovně premium, ale může trvat zálohování disků virtuálních počítačů, které používají službu Premium Storage.

Potom použijte **New-AzureRmVMSqlServerAutoBackupConfig** příkaz pro povolení a konfigurace nastavení automatizovaného zálohování v2 a ukládat zálohy v účtu úložiště Azure. V tomto příkladu jsou zálohy nastaveny na uchovávají po dobu 10 dnů. Zálohování databáze systému jsou povolené. Úplné zálohy jsou naplánovány pro každý týden s časovým intervalem začínají na 20:00 pro dvě hodiny. Zálohy protokolů jsou naplánovány pro každých 30 minut. Druhý příkaz **Set-AzureRmVMSqlServerExtension**, aktualizuje zadaný virtuální počítač Azure s těmito nastaveními.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

To může trvat několik minut, instalaci a konfiguraci agenta SQL Server IaaS. 

Pokud chcete povolit šifrování, upravit předchozí skript k předání **EnableEncryption** parametr spolu s heslem (zabezpečený řetězec) **CertificatePassword** parametru. Následující skript povolí nastavení automatizovaného zálohování v předchozím příkladu a přidá šifrování.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Potvrďte nastavení se použijí, [ověřit konfiguraci automatizovaného zálohování](#verifysettings).

### <a name="disable-automated-backup"></a>Zakázat automatické zálohování
Chcete-li zakázat automatické zálohování, spusťte stejný skript bez **-povolit** parametr **AzureRmVMSqlServerAutoBackupConfig nový** příkaz. Chybí **-povolit** parametr signály příkazu zakažte funkci. Stejně jako u instalace to může trvat několik minut zakázat automatizovaného zálohování.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Ukázkový skript
Následující skript představuje sadu proměnných, které si můžete přizpůsobit pro povolení a konfigurace automatizovaného zálohování pro virtuální počítač. Ve vašem případě potřebujete vlastní skript na základě vašich požadavků. Například je třeba provést změny, pokud chcete zakázat zálohování systémových databází nebo povolit šifrování.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension 

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorování

K monitorování automatizovaného zálohování na SQL Server 2016 a 2017, máte dvě hlavní možnosti. Protože automatizovaného zálohování použije funkci spravované zálohování SQL serveru, se stejné techniky monitorování platit pro oboje.

Nejprve můžete dotazovat stav voláním [msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Nebo dotazu [msdb.managed_backup.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) funkce vracející tabulku.

Další možností je využít integrované funkce databázového e-mailu pro oznámení.

1. Volání [msdb.managed_backup.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) uložené procedury k e-mailovou adresu k přiřazení **SSMBackup2WANotificationEmailIds** parametru. 
1. Povolit [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) odesílat e-maily z virtuálního počítače Azure.
1. Umožňuje nakonfigurovat databázového e-mailu SMTP serveru a uživatelské jméno. Databázového e-mailu můžete nakonfigurovat v SQL Server Management Studio nebo pomocí příkazů jazyka Transact-SQL. Další informace najdete v tématu [databázového e-mailu](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurace agenta systému SQL Server k použití databázového e-mailu](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Ověřte, jestli je SMTP port povoleno přes místní bránu firewall virtuálního počítače a skupiny zabezpečení sítě pro virtuální počítač.

## <a name="next-steps"></a>Další postup
Automatizované zálohování v2 nakonfiguruje spravovaného zálohování na virtuálních počítačích Azure. Proto je důležité [najdete v dokumentaci spravovaného zálohování systému](https://msdn.microsoft.com/library/dn449496.aspx) vám pomohou pochopit chování a důsledky.

Můžete najít další zálohování a obnovení doprovodné materiály pro SQL Server na virtuálních počítačích Azure v následujícím článku: [zálohování a obnovení pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Informace o dalších úlohách dostupných automation najdete v tématu [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Další informace o spuštění SQL serveru na virtuálních počítačích Azure najdete v tématu [systému SQL Server na Azure Virtual Machines – přehled](virtual-machines-windows-sql-server-iaas-overview.md).

