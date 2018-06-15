---
title: Vytvoření virtuálního počítače s SQL serverem v prostředí Azure PowerShell (klasické) | Microsoft Docs
description: Obsahuje kroky a skriptů prostředí PowerShell pro vytvoření virtuálního počítače Azure s obrázky Galerie virtuálního počítače systému SQL Server. Toto téma používá režim nasazení classic.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: jroth
ms.openlocfilehash: 66f44e27562f33373e0b67fe6e0ebf9c6bf99e03
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399657"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Zřízení virtuálního počítače s SQL serverem pomocí Azure PowerShell (klasické)

Tento článek obsahuje kroky pro vytvoření virtuálního počítače s SQL serverem v Azure pomocí rutin prostředí PowerShell.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Resource Manager verzi tohoto tématu naleznete v části [zřízení virtuálního počítače s SQL serverem pomocí Azure PowerShell Resource Manager](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Instalace a konfigurace prostředí PowerShell:
1. Pokud účet Azure nemáte, můžete začít používat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Stáhněte a nainstalujte nejnovější příkazy prostředí Azure PowerShell](/powershell/azure/overview).
3. Spusťte prostředí Windows PowerShell a připojte ho k předplatnému Azure s **Add-AzureAccount** příkaz.

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Určení cílových oblast Azure

Váš virtuální počítač SQL Server bude hostován v rámci cloudové služby, který se nachází v určité oblasti Azure. Následující postup vám pomůže určit oblasti, účet úložiště a cloudové služby, který se použije pro zbytek tohoto kurzu.

1. Určuje datové centrum, které chcete použít k hostování virtuální počítač s SQL serverem. Následující příkaz prostředí PowerShell zobrazí seznam názvů dostupné oblasti.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Jakmile jste zformulovali upřednostňované umístění, nastavení proměnné s názvem **$dcLocation** do této oblasti. Následující příkaz třeba nastaví oblasti, kterou chcete "Východ USA":

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Nastavit vaše předplatné a účet úložiště

1. Určete předplatné, které chcete použít pro nový virtuální počítač.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Přiřadit cílových předplatného Azure k **$subscr** proměnné. Potom nastavením jako vaším aktuálním předplatným Azure.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Zkontrolujte pro existující účty úložiště. Následující skript zobrazí všechny účty úložiště, které existují ve vaší vybrané oblasti:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Pokud budete potřebovat nový účet úložiště, název účtu úložiště všechna malá nejprve vytvořte pomocí příkazu New-AzureStorageAccount jako v následujícím příkladu: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Přiřadit název cílového účtu úložiště k **$staccount**. Potom pomocí **Set-AzureSubscription** nastavte předplatné a aktuální účet úložiště.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Vyberte bitovou kopii virtuálního počítače systému SQL Server

1. Získat seznam dostupných imagí SQL serveru virtuálních počítačů z galerie. Tyto Image jsou vybavené **ImageFamily** vlastnost, která začíná "SQL". Následující dotaz zobrazí pro předinstalována systému SQL Server, které vám k dispozici bitovou kopii rodiny.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Pokud zjistíte, rodina bitové kopie virtuálního počítače, může být více bitových kopií publikované v této rodině. Pomocí následujícího skriptu najít nejnovější název bitové kopie publikované virtuálního počítače pro vybranou image rodinu (například **SQL Server 2016 RTM Enterprise na Windows Server 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

Nakonec vytvořte virtuální počítač pomocí prostředí PowerShell:

1. Vytvořte cloudovou službu k hostování nového virtuálního počítače. Všimněte si, že je také možné místo toho použít stávající cloudovou službu. Vytvoření nové proměnné **$svcname** s krátký název cloudové služby.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Zadejte název virtuálního počítače a velikost. Další informace o velikosti virtuálních počítačů najdete v tématu [velikostí virtuálních počítačů pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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

4. Spusťte následující skript pro vytvoření virtuálního počítače.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Další vysvětlení a možnosti konfigurace najdete v tématu **sestavení vaší sadu příkazů** kapitoly [použití Azure PowerShell k vytvoření a nastavení virtuálních počítačích se systémem Windows](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Ukázkový skript prostředí PowerShell

Následující skript představuje příklad dokončení skriptu, který vytvoří **SQL Server 2016 RTM Enterprise na Windows Server 2012 R2** virtuálního počítače. Pokud chcete použít tento skript, je nutné přizpůsobit počáteční proměnné podle předchozích kroků v tomto tématu.

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

## <a name="connect-with-remote-desktop"></a>Připojit pomocí vzdálené plochy

1. Soubory protokolu RDP vytvořte ve složce dokumentu stávajícího uživatele ke spuštění těchto virtuálních počítačů k dokončení instalace:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. V adresáři dokumenty spusťte soubor RDP. Spojte se s správce uživatelské jméno a heslo zadané dříve (například pokud vaše uživatelské jméno je VMAdmin, zadejte "\VMAdmin" jako uživatel a zadejte heslo).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Dokončení konfigurace počítače systému SQL Server pro vzdálený přístup

Po přihlášení k počítači pomocí vzdálené plochy, nakonfigurujte podle pokynů v systému SQL Server [kroky pro konfiguraci připojení k systému SQL Server ve virtuálním počítači Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Další postup

Můžete najít další pokyny pro zřizování virtuálních počítačů pomocí prostředí PowerShell ve [virtuální počítače dokumentaci](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

V mnoha případech je dalším krokem je migrace databáze do tohoto nového virtuálního počítače SQL serveru. Pokyny pro migraci databáze najdete v tématu [migrace databáze do systému SQL Server na virtuálním počítači Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Pokud vás zajímá také pomocí portálu Azure vytvořit virtuální počítače se systémem SQL, najdete v článku [zřizování virtuálního počítače systému SQL Server na platformě Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Všimněte si, že kurz, který vás provede portálu vytvoří virtuální počítače pomocí modelu Resource Manager doporučené místo klasického modelu použitým v tomto tématu prostředí PowerShell.

Kromě těchto prostředků, doporučujeme, abyste si prošli [další témata související s SQL serverem v Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
