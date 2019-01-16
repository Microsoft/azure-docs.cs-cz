---
title: Vytvoření virtuálního počítače s SQL serverem v prostředí Azure PowerShell (Classic) | Dokumentace Microsoftu
description: Poskytuje kroky a skripty prostředí PowerShell pro vytvoření virtuálního počítače Azure s využitím Image z Galerie virtuálních počítačů SQL serveru. Toto téma používá režim nasazení classic.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad8b59a9290c533a3687b5ff8956d8682fb6d9e9
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332281"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Zřízení virtuálního počítače s SQL serverem přes Azure PowerShell (Classic)

Tento článek popisuje kroky pro vytvoření virtuálního počítače s SQL serverem v Azure pomocí rutin prostředí PowerShell.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Verzi tohoto tématu Resource Manageru najdete v části [zřízení virtuálního počítače s SQL serverem pomocí Resource Manageru prostředí Azure PowerShell](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Instalace a konfigurace Powershellu:
1. Pokud účet Azure nemáte, můžete začít používat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Stáhněte a nainstalujte nejnovější Azure PowerShell příkazy](/powershell/azure/overview).
3. Spusťte prostředí Windows PowerShell a připojte ho k předplatnému Azure pomocí **Add-AzureAccount** příkazu.

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Určení vaší cílovou oblastí Azure

Virtuálnímu počítači SQL serveru bude hostovaná v cloudové službě, která se nachází v konkrétní oblasti Azure. Následující postup vám pomůže určit oblasti, účet úložiště a cloudové služby, který se použije pro zbývající část tohoto kurzu.

1. Určení datové centrum, které chcete použít k hostování virtuálního počítače s SQL serverem. Následující příkaz prostředí PowerShell zobrazí seznam názvů dostupných oblastí.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Jakmile identifikujete upřednostňované umístění, nastavte proměnnou s názvem **$dcLocation** pro tuto oblast. Následující příkaz třeba Nastaví oblast pro "Východ USA":

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Nastavte vaše předplatné a účet úložiště

1. Určete předplatné Azure, které se použijí pro nový virtuální počítač.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Přiřaďte cílové předplatné Azure k **$subscr** proměnné. Nastavte tuto vlastnost jako vaše aktuální předplatné Azure.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Pak vyhledejte existující účty úložiště. Tento skript zobrazí všechny účty úložiště, které existují ve vybrané oblasti:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Pokud budete potřebovat nový účet úložiště, název účtu úložiště všechna malá nejprve vytvořte pomocí příkazu New-AzureStorageAccount jako v následujícím příkladu: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Název účtu cílového úložiště pro přiřazení **$staccount**. Pak pomocí **Set-AzureSubscription** nastavte předplatné a aktuální účet úložiště.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Vyberte image virtuálního počítače systému SQL Server

1. Přečtěte si seznam dostupných imagí virtuálních počítačů SQL serveru z galerie. Tyto obrázky mají **ImageFamily** vlastnost, která začíná textem "SQL". Následující dotaz zobrazí dostupné image řady pro vás, na kterém SQL Server, včetně.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Když najdete řadu image virtuálního počítače může být několik publikovaných imagí v této rodině. Pomocí následujícího skriptu k vyhledání názvu nejnovější image virtuálních počítačů publikované pro vaši rodinu vybrané bitové kopie (například **SQL Server 2016 RTM Enterprise na Windows serveru 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

Nakonec vytvořte virtuální počítač v prostředí PowerShell:

1. Vytvořte cloudovou službu k hostování nového virtuálního počítače. Všimněte si, že je také možné místo toho použít existující cloudovou službu. Vytvoření nové proměnné **$svcname** s krátkým názvem cloudové služby.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Zadejte název virtuálního počítače a velikost. Další informace o velikostech virtuálních počítačů najdete v tématu [velikostí virtuálních počítačů pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Zadejte účet místního správce a heslo.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Spusťte následující skript k vytvoření virtuálního počítače.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Další vysvětlení a možnosti konfigurace, najdete v článku **sestavení sady příkaz** tématu [pomocí prostředí Azure PowerShell k vytvoření a předkonfigurace virtuálního počítače se systémem Windows](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Ukázkový skript prostředí PowerShell

Následující skript představuje příklad celý skript, který vytvoří **SQL Server 2016 RTM Enterprise na Windows serveru 2012 R2** virtuálního počítače. Pokud používáte tento skript, je třeba upravit počáteční proměnné podle předchozích kroků v tomto tématu.

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>Připojte se přes vzdálenou plochu

1. Vytvoření souborů protokolu RDP ve složce aktuální uživatel dokumentu spustit tyto virtuální počítače a dokončete nastavení:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. V adresáři dokumenty spusťte soubor RDP. Spojte se s správce uživatelské jméno a heslo, které jste zadali dříve (například, pokud uživatelské jméno bylo VMAdmin, zadejte "\VMAdmin" jako uživatele a zadejte heslo).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Dokončete konfiguraci počítače SQL serveru pro vzdálený přístup

Po přihlášení k počítači pomocí vzdálené plochy, konfigurace systému SQL Server podle pokynů v [kroky pro konfiguraci připojení k SQL serveru na Virtuálním počítači Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Další postup

Můžete najít další pokyny pro zřizování virtuálních počítačů pomocí Powershellu v [dokumentace k virtuálním počítačům](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Dalším krokem v mnoha případech je migrace databází do tohoto nového virtuálního počítače SQL serveru. Pokyny k migraci databáze, najdete v části [migrace databáze do služby SQL Server na Virtuálním počítači Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Pokud vás zajímá také pomocí webu Azure portal k vytvoření virtuálních počítačů SQL naleznete v tématu [zřízení virtuálního počítače SQL serveru v Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Všimněte si, že tento kurz vás provede portál vytvoří virtuální počítače pomocí doporučený model Resource Manager místo klasického modelu použitým v tomto tématu prostředí PowerShell.

Kromě těchto prostředků, doporučujeme, abyste si přečetli [další témata související s SQL serverem na Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
