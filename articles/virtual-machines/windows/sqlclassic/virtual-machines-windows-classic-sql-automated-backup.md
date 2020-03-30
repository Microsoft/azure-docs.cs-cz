---
title: Automatické zálohování virtuálních počítačů sql serveru (classic) | Dokumenty společnosti Microsoft
description: 'Vysvětluje funkci automatického zálohování pro SQL Server spuštěný ve virtuálních počítačích Azure pomocí Správce prostředků. '
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978103"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatické zálohování pro SQL Server ve virtuálních počítačích Azure (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Classic](../classic/sql-automated-backup.md)
> 
> 

Automatické zálohování automaticky konfiguruje [spravované zálohování do Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pro všechny existující a nové databáze na virtuálním počítači Azure se systémem SQL Server 2014 Standard nebo Enterprise. To umožňuje konfigurovat pravidelné zálohování databáze, které využívají trvalé úložiště objektů blob Azure. Automatické zálohování závisí na [rozšíření agenta serveru SQL Server IaaS](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití modelu klasické nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Pokud chcete zobrazit verzi tohoto článku ve Správci prostředků, [přečtěte si téma Automatické zálohování pro SQL Server ve Správci prostředků virtuálních počítačů Azure](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Požadavky
Chcete-li používat automatické zálohování, zvažte následující předpoklady:

**Operační systém**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Verze/edice serveru SQL Server**:

* Standard SQL Serveru 2014
* SQL Server 2014 Enterprise

> [!NOTE]
> Automatické zálohování pro SQL Server 2016 je podporováno virtuálními počítači Správce prostředků. Další informace najdete [v tématu Automatické zálohování v2 pro SQL Server 2016 Virtuální počítače Azure (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Konfigurace databáze**:

* Cílové databáze musí používat úplný model obnovení.

**Azure PowerShell**:

* [Nainstalujte nejnovější příkazy Azure PowerShellu](/powershell/azure/overview).

**Rozšíření SQL Server IaaS**:

* [Nainstalujte rozšíření SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které lze nakonfigurovat pro automatické zálohování. U klasických virtuálních počítače je nutné k konfiguraci těchto nastavení použít prostředí PowerShell.

| Nastavení | Rozsah (výchozí) | Popis |
| --- | --- | --- |
| **Automatické zálohování** |Povolit/zakázat (zakázáno) |Povolí nebo zakáže automatické zálohování pro virtuální počítač Azure se systémem SQL Server 2014 Standard nebo Enterprise. |
| **Doba uchování** |1-30 dní (30 dní) |Počet dní pro zachování zálohy. |
| **Účet úložiště** |Účet úložiště Azure (účet úložiště vytvořený pro zadaný virtuální počítač) |Účet úložiště Azure, který se má použít pro ukládání souborů automatického zálohování do úložiště objektů blob. Kontejner je vytvořen v tomto umístění pro uložení všech záložních souborů. Konvence pojmenování záložních souborů zahrnuje datum, čas a název počítače. |
| **Šifrování** |Povolit/zakázat (zakázáno) |Povolí nebo zakáže šifrování. Pokud je povoleno šifrování, certifikáty použité k obnovení zálohy jsou umístěny v zadaném účtu úložiště ve stejném kontejneru automatické zálohy pomocí stejné konvence pojmenování. Pokud se heslo změní, je s tímto heslem vygenerován nový certifikát, ale starý certifikát zůstává k obnovení předchozích záloh. |
| **Heslo** |Text hesla (žádný) |Heslo pro šifrovací klíče. To je vyžadováno pouze v případě, že je povoleno šifrování. Chcete-li obnovit šifrovanou zálohu, musíte mít správné heslo a související certifikát, který byl použit v době, kdy byla záloha provedena. |
| **Zálohování systémových databází** | Povolit/zakázat (zakázáno) | Úplné zálohování master, model a MSDB |
| **Konfigurování plánu zálohování** | Ruční/automatizované (automatizované) | Vyberte **Automatizovat,** chcete-li automaticky provést úplné a protokolovat zálohy na základě růstu protokolu. Výběrem **možnosti Ručně** určete plán úplného a protokolování záloh. |

## <a name="configuration-with-powershell"></a>Konfigurace s PowerShellem
V následujícím příkladu prostředí PowerShell je automatické zálohování nakonfigurované pro existující virtuální modul SQL Server 2014. Příkaz **New-AzureVMSqlServerAutoBackupConfig** konfiguruje nastavení automatického zálohování pro ukládání záloh do účtu úložiště Azure určeného proměnnou $storageaccount. Tyto zálohy budou uchovávány po dobu 10 dnů. Příkaz **Set-AzureVMSqlServerExtension** aktualizuje zadaný virtuální počítač Azure s těmito nastaveními.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Instalace a konfigurace agenta SQL Server IaaS agenta serveru SQL Server může trvat několik minut.

Chcete-li povolit šifrování, upravte předchozí skript tak, aby předal parametr EnableEncryption spolu s heslem (zabezpečeným řetězcem) pro parametr CertificatePassword. Následující skript umožňuje nastavení automatického zálohování v předchozím příkladu a přidá šifrování.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Chcete-li zakázat automatické zálohování, spusťte stejný skript bez parametru **-Enable** na **new-AzureVMSqlServerAutoBackupConfig**. Stejně jako při instalaci může zakácet automatické zálohování několik minut.

> [!NOTE]
> Zakázání a odinstalace agenta SERVERU SQL Server IaaS neodebere dříve nakonfigurované nastavení spravovaného zálohování. Před zakázáním nebo odinstalací agenta SERVERU SQL Server IaaS byste měli zakázat automatické zálohování.
> 
> 

## <a name="next-steps"></a>Další kroky
Automatické zálohování konfiguruje spravované zálohování na virtuálních počítačích Azure. Proto je důležité [zkontrolovat dokumentaci pro spravované zálohování](https://msdn.microsoft.com/library/dn449496.aspx) pochopit chování a důsledky.

Další pokyny pro zálohování a obnovení pro SQL Server na virtuálních počítačích Azure najdete v následujícím tématu: [Zálohování a obnovení pro SQL Server ve virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Informace o dalších dostupných úlohách automatizace naleznete v tématu [SQL Server IaaS Agent Extension](../classic/sql-server-agent-extension.md).

Další informace o spuštění SQL Serveru na virtuálních počítačích Azure najdete v tématu [SQL Server ve virtuálních počítačích Azure přehled](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

