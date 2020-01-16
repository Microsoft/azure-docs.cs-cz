---
title: Automatizované zálohování pro SQL Server Virtual Machines (Classic) | Microsoft Docs
description: 'Vysvětluje funkci automatizovaného zálohování pro SQL Server běžící v Azure Virtual Machines pomocí Správce prostředků. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43ff230d4769a23c9007b3da29858d2105366f9f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978103"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatizované zálohování pro SQL Server v Azure Virtual Machines (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Classic](../classic/sql-automated-backup.md)
> 
> 

Automatizované zálohování automaticky konfiguruje [spravovanou zálohu na Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pro všechny stávající a nové databáze na virtuálním počítači Azure s SQL Server 2014 Standard nebo Enterprise. To vám umožní nakonfigurovat pravidelné zálohy databází, které využívají trvalé úložiště objektů BLOB v Azure. Automatizované zálohování závisí na [rozšíření agenta SQL Server IaaS](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Pokud si chcete zobrazit Správce prostředků verzi tohoto článku, přečtěte si téma [automatizované zálohování pro SQL Server v Azure Virtual Machines správce prostředků](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete používat automatizované zálohování, vezměte v úvahu následující požadavky:

**Operační systém**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server verze/edice**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Pro Správce prostředků virtuálních počítačů se podporuje automatizované zálohování pro SQL Server 2016. Další informace najdete v tématu [automatizovaná záloha v2 pro SQL Server 2016 Azure Virtual Machines (Správce prostředků)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Konfigurace databáze**:

* Cílové databáze musí používat úplný model obnovení.

**Azure PowerShell**:

* [Nainstalujte nejnovější příkazy Azure PowerShell](/powershell/azure/overview).

**IaaS rozšíření SQL Server**:

* [Nainstalujte rozšíření SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které je možné nakonfigurovat pro automatizované zálohování. U klasických virtuálních počítačů musíte ke konfiguraci těchto nastavení použít PowerShell.

| Nastavení | Rozsah (výchozí) | Popis |
| --- | --- | --- |
| **Automatizované zálohování** |Povolit/zakázat (zakázáno) |Povolí nebo zakáže automatizované zálohování pro virtuální počítač Azure se systémem SQL Server 2014 Standard nebo Enterprise. |
| **Doba uchování** |1-30 dní (30 dní) |Počet dnů uchování zálohy. |
| **Účet úložiště** |Účet úložiště Azure (účet úložiště vytvořený pro zadaný virtuální počítač) |Účet služby Azure Storage, který se má použít pro ukládání automatizovaných záložních souborů v úložišti objektů BLOB. V tomto umístění se vytvoří kontejner pro uložení všech záložních souborů. Konvence pojmenování záložních souborů zahrnuje datum, čas a název počítače. |
| **Šifrování** |Povolit/zakázat (zakázáno) |Povolí nebo zakáže šifrování. Pokud je povolené šifrování, certifikáty používané k obnovení zálohy se nacházejí v zadaném účtu úložiště ve stejném kontejneru automaticbackup pomocí stejných zásad vytváření názvů. Pokud se změní heslo, vygeneruje se nový certifikát s tímto heslem, starý certifikát ale zůstane k obnovení předchozího zálohování. |
| **Heslo** |Text hesla (žádné) |Heslo pro šifrovací klíče. Tato možnost je nutná pouze v případě, že je povoleno šifrování. Aby bylo možné obnovit šifrované zálohování, musíte mít správné heslo a související certifikát, který byl použit v době pořízení zálohy. |
| **Zálohování systémových databází** | Povolit/zakázat (zakázáno) | Provedení úplných záloh hlavních, modelů a MSDB |
| **Konfigurovat plán zálohování** | Ruční/automatizovaná (automatizovaná) | Možnost **automatizovaná** vyberte, pokud chcete automaticky přijímat úplné a zaprotokolovat zálohy na základě nárůstu protokolu. Vyberte **ručně** a určete plán pro úplné zálohování a zálohování protokolů. |

## <a name="configuration-with-powershell"></a>Konfigurace pomocí PowerShellu
V následujícím příkladu PowerShellu se pro existující virtuální počítač SQL Server 2014 konfiguruje automatické zálohování. Příkaz **New-AzureVMSqlServerAutoBackupConfig** nakonfiguruje nastavení automatizovaného zálohování pro ukládání záloh do účtu služby Azure Storage, který je určený proměnnou $storageaccount. Tyto zálohy se uchovávají po dobu 10 dnů. Příkaz **set-AzureVMSqlServerExtension** aktualizuje zadaný virtuální počítač Azure pomocí těchto nastavení.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Instalace a konfigurace agenta SQL Server IaaS může trvat několik minut.

Pokud chcete povolit šifrování, upravte předchozí skript tak, aby předával parametr EnableEncryption spolu s heslem (zabezpečeným řetězcem) pro parametr CertificatePassword. Následující skript povolí nastavení automatizovaného zálohování v předchozím příkladu a přidá šifrování.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Pokud chcete zakázat automatické zálohování, spusťte stejný skript bez parametru **-Enable** pro rutinu **New-AzureVMSqlServerAutoBackupConfig**. Stejně jako u instalace může trvat několik minut, než se zakáže automatizované zálohování.

> [!NOTE]
> Vypnutí a odinstalace agenta SQL Server IaaS neodebere dříve nakonfigurovaná nastavení spravované zálohy. Před zakázáním nebo odinstalací agenta SQL Server IaaS byste měli zakázat automatizované zálohování.
> 
> 

## <a name="next-steps"></a>Další kroky
Automatizované zálohování konfiguruje spravovanou zálohu na virtuálních počítačích Azure. Proto je důležité [si projít dokumentaci pro spravovanou zálohu](https://msdn.microsoft.com/library/dn449496.aspx) , abyste porozuměli chování a důsledky.

Další pokyny k zálohování a obnovení pro SQL Server na virtuálních počítačích Azure najdete v následujícím tématu: [zálohování a obnovení pro SQL Server ve službě azure Virtual Machines](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Informace o dalších dostupných úlohách automatizace najdete v tématu [SQL Server rozšíření agenta IaaS](../classic/sql-server-agent-extension.md).

Další informace o spuštění SQL Server na virtuálních počítačích Azure najdete v tématu [SQL Server na azure Virtual Machines přehled](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

