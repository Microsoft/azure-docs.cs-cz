---
title: Automatizované zálohování pro SQL Server virtuální počítače (Classic) | Dokumentace Microsoftu
description: 'Vysvětluje funkci automatizované zálohování pro SQL Server běžící na virtuálních počítačích Azure pomocí Resource Manageru. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
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
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3aba118354c51285d714bb127e6f5984f8a50057
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329748"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatizovaná záloha pro SQL Server na virtuálních počítačích Azure (klasické)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Classic](../classic/sql-automated-backup.md)
> 
> 

Automatizované zálohování automaticky nakonfiguruje [spravovaného zálohování Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pro všechny stávající i nové databáze na Virtuálním počítači Azure s SQL Server 2014 Standard nebo Enterprise. To umožňuje nakonfigurovat pravidelné zálohy, které využívají úložiště objektů blob v Azure odolné. Automatizované zálohování závisí [rozšíření agenta SQL Server IaaS](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Chcete-li zobrazit Resource Manageru verze tohoto článku, najdete v článku [automatizované zálohování pro SQL Server v Azure virtuální počítače Resource Manageru](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Požadavky
Chcete-li pomocí automatizovaného zálohování, zvažte následující požadavky:

**Operační systém**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Verze a edice systému SQL Server**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Automatizovaná záloha pro SQL Server 2016 podporuje virtuální počítače Resource Manageru. Další informace najdete v tématu [v2 automatizované zálohování pro SQL Server 2016 Azure Virtual Machines (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Konfigurace databáze**:

* Cílová databáze musí použít model úplného obnovení.

**Azure PowerShell**:

* [Nainstalujte nejnovější Azure PowerShell příkazy](/powershell/azure/overview).

**Rozšíření systému SQL Server IaaS**:

* [Instalace rozšíření SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které je možné nakonfigurovat pro automatizované zálohování. Pro klasické virtuální počítače musíte použít PowerShell k nakonfigurování těchto nastavení.

| Nastavení | Rozsah (výchozí) | Popis |
| --- | --- | --- |
| **Automatizované zálohování** |Povolí nebo zakáže (zakázáno) |Povolí nebo zakáže automatické zálohování pro virtuální počítač Azure s SQL Server 2014 Standard nebo Enterprise. |
| **Doba uchování** |1 – 30 dnů (30 dnů) |Počet dní uchování zálohy. |
| **Účet úložiště** |Účet úložiště Azure (pro zadaný virtuální počítač vytvořit účet úložiště) |Účet úložiště Azure k ukládání souborů automatizovaného zálohování ve službě blob storage. Kontejner se vytvoří v tomto umístění pro uložení všechny záložní soubory. Zásady vytváření názvů záložní soubor obsahuje data, času a název počítače. |
| **Šifrování** |Povolí nebo zakáže (zakázáno) |Povolí nebo zakáže šifrování. Když je povoleno šifrování, certifikátů používaných pro obnovení zálohy jsou umístěny v zadaný účet úložiště ve stejném kontejneru automaticbackup pomocí stejné zásady vytváření názvů. Pokud se změní heslo, se toto heslo se vygeneruje nový certifikát, ale starý certifikát zůstane k obnovení předchozího záloh. |
| **Heslo** |Text heslo, (žádné) |Heslo šifrovacích klíčů. Toto je jenom nutné, pokud je povolené šifrování. Aby bylo možné obnovit šifrované zálohování, musíte mít správné heslo a související certifikát, který byl použit v době, kdy bylo provedeno zálohování. | **Zálohování systémových databází** | Povolí nebo zakáže (zakázáno) | Provést úplnou zálohu Master, Model a databázi MSDB |
| **Konfigurování plánu zálohování** | Ruční nebo automatizované (automatizované) | Vyberte **automatizovaná** k automaticky provede úplnou zálohu a zálohu založené na protokolu růst protokolu. Vyberte **ruční** a zadejte plán pro úplnou zálohu a zálohu protokolu. |

## <a name="configuration-with-powershell"></a>Konfigurace pomocí Powershellu
V následujícím příkladu Powershellu automatizovaného zálohování je nakonfigurovaný pro existující virtuální počítač SQL Server 2014. **New-AzureVMSqlServerAutoBackupConfig** příkaz nakonfiguruje nastavení automatizovaného zálohování pro ukládání záloh v účtu úložiště Azure, které jsou určeny proměnnou $storageaccount. Tyto zálohy se uchovávají po dobu 10 dnů. **Set-AzureVMSqlServerExtension** příkaz aktualizuje zadaný virtuální počítač Azure s těmito nastaveními.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

To může trvat několik minut, instalaci a konfiguraci agenta SQL Server IaaS.

Pokud chcete povolit šifrování, upravte předchozí skriptu předat parametr EnableEncryption spolu s pro parametr CertificatePassword heslo (zabezpečený řetězec). Následující skript povolí nastavení automatizovaného zálohování v předchozím příkladu a přidá šifrování.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Chcete-li zakázat automatické zálohování, spusťte stejný skript bez **-povolit** parametr **AzureVMSqlServerAutoBackupConfig nový**. Stejně jako u instalace to může trvat několik minut zakázat automatizovaného zálohování.

> [!NOTE]
> Zakázání a odinstalaci agenta SQL Server IaaS neodebere dřív nakonfigurovaná nastavení spravovaného zálohování systému. Měli byste zakázat automatizovaného zálohování před zakázání nebo odinstalaci agenta SQL Server IaaS.
> 
> 

## <a name="next-steps"></a>Další postup
Automatizované zálohování nakonfiguruje spravovaného zálohování na virtuálních počítačích Azure. Proto je důležité [najdete v dokumentaci spravovaného zálohování systému](https://msdn.microsoft.com/library/dn449496.aspx) vám pomohou pochopit chování a důsledky.

Můžete najít další zálohování a obnovení doprovodné materiály pro SQL Server na virtuálních počítačích Azure v následujícím tématu: [Zálohování a obnovení pro SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Informace o dalších úlohách dostupných automation najdete v tématu [rozšíření agenta SQL Server IaaS](../classic/sql-server-agent-extension.md).

Další informace o spuštění SQL serveru na virtuálních počítačích Azure najdete v tématu [systému SQL Server na Azure Virtual Machines – přehled](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

