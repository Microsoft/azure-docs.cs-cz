---
title: Automatizované zálohování v2 pro virtuální počítače Azure SQL Server 2016 nebo 2017 | Microsoft Docs
description: Vysvětluje funkci automatizované zálohování pro SQL Server 2016 nebo 2017 virtuální počítače běžící v Azure. Tento článek je specifické pro virtuální počítače pomocí Správce prostředků.
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
ms.openlocfilehash: 4619c26e34c90f58702ad286f76a999f83f49cc4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Automatizované zálohování v2 pro virtuální počítače Azure (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016 nebo 2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatizované zálohování v2 automaticky nakonfiguruje [spravovaného zálohování Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pro všechny stávající a nové databáze na virtuální počítač Azure běžet tyto edice SQL Server 2016 nebo 2017 Standard, Enterprise nebo Developer. To umožňuje nakonfigurovat standardní databázi zálohování, které využívají služby odolné Azure blob storage. Automatizované zálohování v2 závisí na [rozšíření agenta systému SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Požadavky
Pokud chcete používat v2 automatizovaného zálohování, zkontrolujte splnění následujících předpokladů:

**Operační systém**:

- Windows Server 2012 R2
- Windows Server 2016

**Verzi nebo edici systému SQL Server**:

- Systému SQL Server 2016: Developer, Standard nebo Enterprise
- Systému SQL Server 2017: Developer, Standard nebo Enterprise

> [!IMPORTANT]
> Automatizované zálohování v2 funguje s SQL Server 2016 nebo vyšší. Pokud používáte SQL Server 2014, můžete zálohovat databáze automatizovaného zálohování v1. Další informace najdete v tématu [automatizované zálohování pro SQL Server 2014 virtuální počítače Azure](virtual-machines-windows-sql-automated-backup.md).

**Konfigurace databáze**:

- Cílové databáze musí mít úplném modelu obnovení. Další informace o vlivu úplném modelu obnovení na zálohování najdete v tématu [zálohování v části the úplný Model obnovení](https://technet.microsoft.com/library/ms190217.aspx).
- Systémové databáze není nutné používat úplném modelu obnovení. Pokud budete potřebovat zálohy protokolu, které mají být provedeny pro Model, nebo databázi MSDB, ale musí používat úplném modelu obnovení.
- Cílové databáze musí být na výchozí instanci SQL serveru. IaaS rozšíření systému SQL Server nepodporuje pojmenované instance.

> [!NOTE]
> Automatizované zálohování spoléhá na **rozšíření agenta systému SQL Server IaaS**. Aktuální SQL bitové kopie virtuálních počítačů Galerie přidejte toto rozšíření ve výchozím nastavení. Další informace najdete v tématu [rozšíření agenta systému SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které mohou být konfigurovány pro automatizované zálohování v2. Skutečné konfiguračních kroků se liší v závislosti na tom, zda používáte portál Azure nebo příkazů prostředí Windows PowerShell pro Azure.

### <a name="basic-settings"></a>Základní nastavení

| Nastavení | Rozsah (výchozí) | Popis |
| --- | --- | --- |
| **Automatizované zálohování** | Povolí nebo zakáže (zakázáno) | Povolí nebo zakáže automatizované zálohování pro virtuální počítač Azure systémem SQL Server 2016 nebo 2017 Developer, Standard nebo Enterprise. |
| **Doba uchování dat** | 1 až 30 dní (30 dní) | Počet dní uchování záloh. |
| **Účet úložiště** | Účet služby Azure Storage | Účet úložiště Azure pro ukládání souborů automatizovaného zálohování do úložiště objektů blob. Kontejner se vytvoří v tomto umístění pro uložení všechny záložní soubory. Zásady vytváření názvů záložní soubor obsahuje date, time a GUID databáze. |
| **Šifrování** |Povolí nebo zakáže (zakázáno) | Povolí nebo zakáže šifrování. Když je povolené šifrování, certifikátů používaných pro obnovení zálohy jsou umístěné v zadaný účet úložiště. Používá stejný **automaticbackup** kontejner s stejné zásady vytváření názvů. Pokud se změní heslo, se toto heslo se vygeneruje nový certifikát, ale pořád starý certifikát pro obnovení předchozí zálohy. |
| **Heslo** |Heslo | Heslo pro šifrovací klíče. Toto heslo je jenom potřeba, pokud je povolené šifrování. Chcete-li obnovit šifrované zálohování, musí mít správné heslo a související certifikátu, který byl použit v době, kdy bylo provedeno zálohování. |

### <a name="advanced-settings"></a>Upřesnit nastavení

| Nastavení | Rozsah (výchozí) | Popis |
| --- | --- | --- |
| **Zálohování databáze systému** | Povolí nebo zakáže (zakázáno) | Když je povolené, tato funkce také zálohuje databáze systému: hlavní server, databázi MSDB a modelu. Pro databázi MSDB a Model databáze ověřte, zda je v režimu úplného obnovení zálohy protokolu, které mají být provedeny, chcete-li. Zálohy protokolů se nikdy provádějí pro hlavní server. A jsou provedeny žádné zálohy pro databázi TempDB. |
| **Plán zálohování** | Ruční nebo automatické (Automated) | Ve výchozím nastavení plán zálohování je automaticky určeno, založené na protokolu růst. Ruční plán zálohování umožňuje uživateli zadat časový interval pro zálohy. V takovém případě zálohování, až na zadané četnosti a během zadaného časového okna pro daný den. |
| **Četnost úplné zálohování** | Denně nebo týdně | Četnost úplné zálohy. V obou případech úplné zálohování začít během okna další naplánovanou dobu. Pokud je vybraná týdně, zálohování může zahrnovat více dní, dokud všechny databáze úspěšně zálohovali. |
| **Čas spuštění úplného zálohování** | 00:00 – 23:00 (01:00) | Počáteční čas daný den, během které úplné zálohování lze provést. |
| **Úplné zálohování časový interval** | 1 – 23 hodin (1 hodina) | Doba trvání časový interval daný den, během které úplné zálohování lze provést. |
| **Četnost záloh protokolu** | 5 – 60 minut (60 minut) | Četnost záloh protokolu. |

## <a name="understanding-full-backup-frequency"></a>Principy četnost úplné zálohování
Je důležité si uvědomit rozdíl mezi denní nebo týdenní úplné zálohování. Vezměte v úvahu následující dva ukázkové scénáře.

### <a name="scenario-1-weekly-backups"></a>Scénář 1: Týdenní zálohy
Máte virtuální počítač na serveru SQL, který obsahuje počet velké databáze.

V pondělí povolíte automatizované zálohování v2 s následujícím nastavením:

- Plán zálohování: **ruční**
- Úplné četnost zálohování: **týdně**
- Úplné zálohování počáteční čas: **01:00**
- Úplné zálohování časové okno: **1 hodina**

To znamená, že dalším dostupném časovém intervalu zálohování je úterý v 1: 00 1 hodinu. V té době začne automatizovaného zálohování, zálohování databází jeden najednou. V tomto scénáři jsou dostatečně velké na to, pro první databáze několika dokončit úplné zálohy databáze. Ale po jedné hodině všechny databáze byly zálohovány.

Pokud k tomu dojde, začne automatizovaného zálohování, zálohování databází zbývající další den středa v 1: 00 pro jednu hodinu. Pokud nejsou všechny databáze byly zálohovány v tento čas, se pokusí znovu další den ve stejnou dobu. Tento postup se opakuje, dokud všechny databáze byly úspěšně zálohovány.

Po znovu se dosáhne úterý, automatizované zálohování začne znovu zálohování všech databází.

Tento scénář popisuje automatizovaného zálohování funguje pouze v rámci určeného časového období, a každé databázi je zálohovat jednou za týden. To také ukazuje, že je možné pro zálohy do zahrnovat více dní v případě, kde není možné dokončit všechny zálohy za jeden den.

### <a name="scenario-2-daily-backups"></a>Scénář 2: Denní zálohy
Máte virtuální počítač na serveru SQL, který obsahuje počet velké databáze.

V pondělí povolíte automatizované zálohování v2 s následujícím nastavením:

- Plán zálohování: ruční
- Úplných záloh četnost: denně
- Úplné zálohování počáteční čas: 22:00
- Úplné zálohování časové okno: 6 hodin

To znamená, že dalším dostupném časovém intervalu zálohování je pondělí na 22: 00 6 hodin. V té době začne automatizovaného zálohování, zálohování databází jeden najednou.

Úterý v 10 6 hodin, úplné zálohy všech databází spusťte znovu.

> [!IMPORTANT]
> Při plánování denní zálohy, doporučujeme, abyste naplánovali široké časové okno zajistit, že všechny databáze lze zálohovat v rámci této doby. To je obzvláště důležité v případě, kdy máte velké množství dat. Chcete-li zálohovat.

## <a name="configure-in-the-portal"></a>Konfigurace na portálu

Na portálu Azure můžete použít ke konfiguraci automatizovaného zálohování v2 při zřizování nebo pro existující virtuální počítače SQL Server 2016 nebo 2017.

## <a name="configure-for-new-vms"></a>Konfigurace pro nové virtuální počítače

Použití portálu Azure ke konfiguraci automatizovaného zálohování v2 při vytváření nové SQL Server 2016 nebo 2017 virtuálního počítače v modelu nasazení Resource Manager.

V **nastavení systému SQL Server** podokně, vyberte **automatizované zálohování**. Následující Azure portálu snímek obrazovky ukazuje **automatizované zálohování SQL** nastavení.

![Konfigurace automatického zálohování SQL na portálu Azure](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Automatizované zálohování v2 ve výchozím nastavení vypnutá.

## <a name="configure-existing-vms"></a>Konfigurace existující virtuální počítače

Pro existující virtuální počítače systému SQL Server vyberte virtuální počítač systému SQL Server. Vyberte **konfigurace systému SQL Server** část virtuálního počítače **nastavení**.

![Automatizované zálohování SQL pro existující virtuální počítače](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)

V **konfigurace systému SQL Server** nastavení, klikněte **upravit** tlačítko v části Automatické zálohování.

![Konfigurace automatického zálohování SQL pro existující virtuální počítače](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration-edit.png)

Po dokončení klikněte **OK** tlačítko v dolní části **konfigurace systému SQL Server** nastavení uložte provedené změny.

Jestliže povolíte automatizované zálohování poprvé, nakonfiguruje Azure IaaS Agent serveru SQL Server na pozadí. Během této doby nemusí zobrazit na portálu Azure, že automatizované zálohování je nakonfigurované. Počkejte několik minut, než agent, který se má nainstalovat, nakonfigurovat. Potom se projeví na portálu Azure nové nastavení.

## <a name="configure-with-powershell"></a>Konfigurace pomocí prostředí PowerShell

Prostředí PowerShell můžete použít ke konfiguraci automatizovaného zálohování v2. Než začnete, musíte provést tyto akce:

- [Stáhněte a nainstalujte nejnovější prostředí Azure PowerShell](http://aka.ms/webpi-azps).
- Otevřete prostředí Windows PowerShell a přidružte ji k účtu s **Connect-AzureRmAccount** příkaz.

### <a name="install-the-sql-iaas-extension"></a>Nainstalujte rozšíření IaaS SQL
Pokud zřízení virtuálního počítače s SQL serverem na portálu Azure IaaS rozšíření systému SQL Server musí být již nainstalován. Můžete určit, pokud je nainstalovaná pro virtuální počítač voláním **Get-AzureRmVM** příkaz a prozkoumání **rozšíření** vlastnost.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Pokud je nainstalovaná rozšíření IaaS agenta systému SQL Server, měli byste vidět, že je uveden jako "SqlIaaSAgent" nebo "SQLIaaSExtension". **Stav zřizování** pro rozšíření by měl také zobrazit, "Succeeded". 

Pokud není nainstalovaná nebo se nepovedlo zřídit, můžete ho nainstalovat pomocí následujícího příkazu. Kromě názvu a prostředek skupiny virtuálních počítačů, je nutné také zadat oblast (**$region**) umístěnou ve virtuálního počítače.

```powershell
$region = “EASTUS2”
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region 
```

### <a id="verifysettings"></a> Ověřte aktuální nastavení
Pokud jste povolili automatizované zálohování při zřizování, můžete použít PowerShell ke kontrole aktuální konfigurace. Spustit **Get-AzureRmVMSqlServerExtension** příkaz a zkontrolujte **AutoBackupSettings** vlastnost:

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Měli byste obdržet výstup podobný následujícímu:

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

Pokud vaše výstup ukazuje, že **povolit** je nastaven na **False**, budete muset povolit automatizované zálohování. Dobrá zpráva je, že můžete povolit a nakonfigurovat automatizovaného zálohování stejným způsobem. Najdete v další části pro tyto informace.

> [!NOTE] 
> Pokud zaškrtnete okamžitě po provedení změny nastavení, je možné, zobrazí se zpět původní hodnoty konfigurace. Počkejte několik minut a zkontrolujte nastavení znovu a ujistěte se, že byly použity změny.

### <a name="configure-automated-backup-v2"></a>Konfigurace automatického zálohování v2
Prostředí PowerShell můžete použít k povolení automatizované zálohování i, kdykoli upravit jeho konfiguraci a chování. 

Nejdřív vyberte nebo vytvořte účet úložiště pro záložní soubory. Následující skript vybere účet úložiště, nebo ji vytvoří, pokud neexistuje.

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
> Automatizované zálohování nepodporuje ukládání záloh v storage úrovně premium, ale může trvat zálohy z disků virtuálních počítačů, které používají úložiště Premium.

Potom pomocí **New-AzureRmVMSqlServerAutoBackupConfig** příkaz povolení a konfigurace nastavení automatizovaného zálohování v2 pro ukládání záloh v účtu úložiště Azure. V tomto příkladu jsou zálohy nastaveny pro zachování 10 dní. Zálohování databáze systému jsou povolené. Úplné zálohy jsou naplánovány pro každý týden s časovým oknem začínající na 20:00 pro dvě hodiny. Zálohy protokolů jsou naplánovány pro každých 30 minut. V druhém příkazu **Set-AzureRmVMSqlServerExtension**, aktualizuje zadaný virtuální počítač Azure s těmito nastaveními.

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

Ho může trvat několik minut k instalaci a konfiguraci IaaS Agent serveru SQL Server. 

Chcete-li povolit šifrování, změňte předchozí skript, který chcete předat **EnableEncryption** společně s heslo (zabezpečený řetězec) pro parametr **CertificatePassword** parametr. Následující skript umožňuje nastavení automatizovaného zálohování v předchozím příkladu a přidá šifrování.

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

### <a name="disable-automated-backup"></a>Zakázat automatizované zálohování
Pokud chcete zakázat automatizovaného zálohování, spusťte stejný skriptu bez **-povolit** parametru **New-AzureRmVMSqlServerAutoBackupConfig** příkaz. Neexistence **-povolit** parametr signály příkaz funkci zakážete. Stejně jako u instalace, se může trvat několik minut zakázat automatizovaného zálohování.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Ukázkový skript
Následující skript představuje sadu proměnných, které můžete přizpůsobit povolit a konfigurovat automatizované zálohování pro virtuální počítač. V váš případ může být nutné přizpůsobit skript podle svých požadavků. Například nutné provést změny, pokud chcete zakázat zálohování databází systému nebo povolit šifrování.

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

Ke sledování automatizovaného zálohování na SQL Server 2016 nebo 2017, máte dvě hlavní možnosti. Protože automatizovaného zálohování používá funkci spravované zálohování systému SQL Server, se stejné techniky monitorování platí pro obě.

Nejprve může dotazovat stav voláním [msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Nebo zadat dotaz [msdb.managed_backup.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) funkce vracející tabulku.

Další možností je využít integrované funkce databázového e-mailu pro oznámení.

1. Volání [msdb.managed_backup.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) uložené procedury přiřadit e-mailovou adresu **SSMBackup2WANotificationEmailIds** parametr. 
1. Povolit [sendgrid vám umožňuje](../../../sendgrid-dotnet-how-to-send-email.md) k odeslání e-mailů z virtuálního počítače Azure.
1. Slouží ke konfiguraci databázového e-mailu SMTP serveru a uživatelské jméno. Databázového e-mailu můžete nakonfigurovat v systému SQL Server Management Studio nebo s příkazy jazyka Transact-SQL. Další informace najdete v tématu [databázového e-mailu](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurace agenta systému SQL Server používat databázového e-mailu](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Ověřte, že je SMTP port povolené přes bránu firewall místní počítač a skupinu zabezpečení sítě pro virtuální počítač.

## <a name="next-steps"></a>Další postup
Automatizované zálohování v2 nakonfiguruje spravovaného zálohování na virtuálních počítačích Azure. Proto je důležité [najdete v dokumentaci pro spravovanou zálohu](https://msdn.microsoft.com/library/dn449496.aspx) pochopit chování a důsledky.

Můžete najít další zálohování a obnovení pokyny pro SQL Server na virtuálních počítačích Azure v následujícím článku: [zálohování a obnovení pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Informace o dalších úlohách, k dispozici automation najdete v tématu [rozšíření agenta systému SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Další informace o spuštění systému SQL Server na virtuálních počítačích Azure najdete v tématu [SQL Server na virtuálních počítačích Azure přehled](virtual-machines-windows-sql-server-iaas-overview.md).

