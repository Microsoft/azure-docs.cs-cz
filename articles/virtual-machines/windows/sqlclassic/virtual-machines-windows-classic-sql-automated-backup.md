---
title: Automatizované zálohování pro virtuální počítače serveru SQL (klasické) | Microsoft Docs
description: 'Vysvětluje funkci automatizované zálohování pro SQL Server běžící ve virtuálních počítačích Azure pomocí Resource Manager. '
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: jroth
ms.openlocfilehash: 3bca1c6c357527a32de499ac9207b1bb734dad7b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatizované zálohování pro SQL Server na virtuálních počítačích Azure (klasický)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Classic](../classic/sql-automated-backup.md)
> 
> 

Automatizované zálohování automaticky nakonfiguruje [spravovaného zálohování Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pro všechny stávající a nové databáze na virtuální počítač Azure SQL Server 2014 Standard nebo Enterprise. To umožňuje nakonfigurovat standardní databázi zálohování, které využívají služby odolné Azure blob storage. Automatizované zálohování závisí na [rozšíření agenta systému SQL Server IaaS](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Resource Manager verzi v tomto článku najdete v tématu [automatizované zálohování pro SQL Server ve službě Správce prostředků virtuálních počítačů Azure](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Požadavky
Pomocí automatizovaného zálohování, zvažte následující požadavky:

**Operační systém**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Verzi nebo edici systému SQL Server**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Automatizované zálohování pro SQL Server 2016 je podporována u virtuálních počítačů Resource Manager. Další informace najdete v tématu [v2 automatizované zálohování pro SQL Server 2016 virtuální počítače Azure (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Konfigurace databáze**:

* Cílové databáze musí mít úplném modelu obnovení.

**Prostředí Azure PowerShell**:

* [Nainstalujte nejnovější příkazy prostředí Azure PowerShell](/powershell/azure/overview).

**Rozšíření systému SQL Server IaaS**:

* [Nainstalujte rozšíření SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které lze konfigurovat pro automatizované zálohování. Pro klasické virtuální počítače musíte použít PowerShell k nakonfigurování těchto nastavení.

| Nastavení | Rozsah (výchozí) | Popis |
| --- | --- | --- |
| **Automatizované zálohování** |Povolí nebo zakáže (zakázáno) |Povolí nebo zakáže automatizované zálohování pro virtuální počítač Azure SQL Server 2014 Standard nebo Enterprise. |
| **Doba uchování dat** |1 až 30 dní (30 dní) |Počet dní, které chcete zachovat zálohu. |
| **Účet úložiště** |Účet úložiště Azure (pro zadaný virtuální počítač vytvořit účet úložiště) |Účet úložiště Azure pro ukládání souborů automatizovaného zálohování do úložiště objektů blob. Kontejner se vytvoří v tomto umístění pro uložení všechny záložní soubory. Zásady vytváření názvů záložní soubor obsahuje datum, čas a název počítače. |
| **Šifrování** |Povolí nebo zakáže (zakázáno) |Povolí nebo zakáže šifrování. Když je povolené šifrování, certifikátů používaných pro obnovení zálohy jsou umístěné v zadaný účet úložiště ve stejném kontejneru automaticbackup pomocí stejné zásady vytváření názvů. Pokud se změní heslo, se toto heslo se vygeneruje nový certifikát, ale pořád starý certifikát pro obnovení předchozí zálohy. |
| **Heslo** |Text heslo, (None) |Heslo pro šifrovací klíče. Toto je pouze vyžaduje, pokud je povolené šifrování. Chcete-li obnovit šifrované zálohování, musí mít správné heslo a související certifikátu, který byl použit v době, kdy bylo provedeno zálohování. | **Systém zálohování databáze** | Povolí nebo zakáže (zakázáno) | Proveďte úplné zálohování Master, Model a databázi MSDB |
| **Konfigurovat plán zálohování** | Ruční nebo automatické (Automated) | Vyberte **automatizovaná** automatické provést úplné a zálohy založené na protokolu růst protokolování. Vyberte **ruční** zadat plán pro úplnou a protokolu zálohování. |

## <a name="configuration-with-powershell"></a>Konfigurace pomocí prostředí PowerShell
V následujícím příkladu prostředí PowerShell automatizované zálohování je nakonfigurován pro existující virtuální počítač SQL Server 2014. **New-AzureVMSqlServerAutoBackupConfig** příkaz nakonfiguruje nastavení automatizovaného zálohování pro ukládání záloh v určené proměnnou $storageaccount účtu úložiště Azure. Tyto zálohy bude uchovávat 10 dní. **Set-AzureVMSqlServerExtension** příkaz aktualizuje zadaný virtuální počítač Azure s těmito nastaveními.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Ho může trvat několik minut k instalaci a konfiguraci IaaS Agent serveru SQL Server.

Pokud chcete povolit šifrování, upravte předchozí skript k předání parametru EnableEncryption společně s pro parametr CertificatePassword heslo (zabezpečený řetězec). Následující skript umožňuje nastavení automatizovaného zálohování v předchozím příkladu a přidá šifrování.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Chcete-li zakázat automatické zálohování, spusťte stejný skript bez **-povolit** parametru **New-AzureVMSqlServerAutoBackupConfig**. Stejně jako u instalace, se může trvat několik minut zakázat automatizovaného zálohování.

> [!NOTE]
> Zakázání a odinstalace agenta systému SQL Server IaaS neodebere dřív nakonfigurovaná nastavení spravovaného zálohování. Měli byste zakázat automatizovaného zálohování před zakázáním nebo odinstalace agenta systému SQL Server IaaS.
> 
> 

## <a name="next-steps"></a>Další postup
Automatizované zálohování nakonfiguruje spravovaného zálohování na virtuálních počítačích Azure. Proto je důležité [najdete v dokumentaci pro spravovanou zálohu](https://msdn.microsoft.com/library/dn449496.aspx) pochopit chování a důsledky.

Můžete najít další zálohování a obnovení pokyny pro SQL Server na virtuálních počítačích Azure v následujícím tématu: [zálohování a obnovení pro SQL Server v Azure Virtual Machines](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Informace o dalších úlohách, k dispozici automation najdete v tématu [rozšíření agenta systému SQL Server IaaS](../classic/sql-server-agent-extension.md).

Další informace o spuštění systému SQL Server na virtuálních počítačích Azure najdete v tématu [SQL Server na virtuálních počítačích Azure přehled](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

