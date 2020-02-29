---
title: Vytvoření virtuálního počítače s klasickým SQL Server (PowerShell)
description: Poskytuje kroky a skripty PowerShellu pro vytvoření virtuálního počítače Azure s SQL Server imagí z Galerie virtuálních počítačů. Toto téma používá klasický režim nasazení.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 5bfdcfab37091a5f581ce147c0a6af5ccd8147a0
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914785"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Zřízení virtuálního počítače s SQL Server pomocí Azure PowerShell (Classic)

[!INCLUDE [classic-vm-deprecation](../../../../includes/classic-vm-deprecation.md)]

Tento článek popisuje kroky, jak vytvořit virtuální počítač s SQL Server v Azure pomocí rutin PowerShellu.

> [!NOTE] 
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Správce prostředků verzi tohoto tématu najdete v tématu [zřízení virtuálního počítače s SQL Server pomocí Azure PowerShell správce prostředků](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Instalace a konfigurace PowerShellu:
1. Pokud účet Azure nemáte, můžete začít používat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Stáhněte a nainstalujte nejnovější příkazy Azure PowerShell](/powershell/azure/overview).
3. Spusťte Windows PowerShell a připojte ho k předplatnému Azure pomocí příkazu **Add-AzureAccount** .

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Určení cílové oblasti Azure

Váš virtuální počítač s SQL Server bude hostovaný v cloudové službě, která se nachází v konkrétní oblasti Azure. Následující kroky vám pomůžou určit oblast, účet úložiště a cloudovou službu, která se použije pro zbytek kurzu.

1. Určete datové centrum, které chcete použít k hostování SQL Serverho virtuálního počítače. Následující příkaz prostředí PowerShell zobrazí seznam dostupných názvů oblastí.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Jakmile identifikujete preferované umístění, nastavte proměnnou s názvem **$dcLocation** do této oblasti. Například následující příkaz nastaví oblast na "Východní USA":

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Nastavení předplatného a účtu úložiště

1. Určete předplatné Azure, které budete používat pro nový virtuální počítač.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Přiřaďte cílové předplatné Azure k proměnné **$subscr** . Pak ho nastavte jako aktuální předplatné Azure.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Pak zkontrolujte existující účty úložiště. Následující skript zobrazí všechny účty úložiště, které existují ve zvolené oblasti:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Pokud vyžadujete nový účet úložiště, vytvořte nejdřív název účtu úložiště s nižšími písmeny pomocí příkazu New-AzureStorageAccount, jako v následujícím příkladu: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Přiřaďte **$staccount**název cílového účtu úložiště. Pak pomocí **set-AzureSubscription** nastavte předplatné a aktuální účet úložiště.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Výběr image virtuálního počítače s SQL Server

1. Přečtěte si seznam dostupných imagí SQL Server virtuálních počítačů z galerie. Všechny tyto image mají vlastnost **ImageFamily** , která začíná řetězcem "SQL". Následující dotaz zobrazí rodinu image, která je k dispozici pro vás SQL Server předinstalované.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Když najdete rodinu imagí virtuálních počítačů, může být v této rodině více publikovaných imagí. Pomocí následujícího skriptu vyhledejte nejnovější název publikované image virtuálního počítače pro vybranou rodinu imagí (například **SQL Server 2016 RTM Enterprise na Windows serveru 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

Nakonec vytvořte virtuální počítač pomocí prostředí PowerShell:

1. Vytvořte cloudovou službu pro hostování nového virtuálního počítače. Všimněte si, že je taky možné místo toho použít stávající cloudovou službu. Vytvořte novou proměnnou **$svcname** s krátkým názvem cloudové služby.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Zadejte název a velikost virtuálního počítače. Další informace o velikostech virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů v Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Zadejte účet a heslo místního správce.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Spuštěním následujícího skriptu vytvořte virtuální počítač.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Další vysvětlení a možnosti konfigurace naleznete v části **sestavení sady příkazů** v tématu [použití Azure PowerShell k vytvoření a předkonfigurování Virtual Machines se systémem Windows](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Ukázkový skript prostředí PowerShell

Následující skript poskytuje příklad kompletního skriptu, který na virtuálním počítači s **Windows serverem 2012 R2 vytvoří SQL Server 2016 RTM Enterprise** . Použijete-li tento skript, je nutné přizpůsobit počáteční proměnné na základě předchozích kroků v tomto tématu.

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

## <a name="connect-with-remote-desktop"></a>Připojení pomocí vzdálené plochy

1. Vytvořením souborů RDP ve složce dokumentů aktuálního uživatele spusťte tyto virtuální počítače k dokončení instalace:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. V adresáři dokumenty spusťte soubor RDP. Připojte se pomocí uživatelského jména a hesla správce, které jste zadali dříve (například pokud se vaše uživatelské jméno VMAdmin, jako uživatel zadejte "\VMAdmin" a zadejte heslo).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Dokončení konfigurace SQL Server počítače pro vzdálený přístup

Po přihlášení k počítači pomocí vzdálené plochy nakonfigurujte SQL Server podle pokynů v části [Postup konfigurace připojení SQL Server na virtuálním počítači Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Další kroky

Další pokyny k zřizování virtuálních počítačů pomocí PowerShellu najdete v [dokumentaci k virtuálním počítačům](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

V mnoha případech je dalším krokem migrace vašich databází na tento nový SQL Server virtuální počítač. Pokyny k migraci databáze najdete v tématu [migrace databáze do SQL Server na virtuálním počítači Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Pokud vás zajímá také použití Azure Portal k vytvoření Virtual Machines SQL, přečtěte si téma [zřízení SQL Serverho virtuálního počítače v Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Všimněte si, že kurz, který vás provede na portálu, vytvoří virtuální počítače pomocí doporučeného modelu Správce prostředků místo modelu Classic používaného v tomto tématu prostředí PowerShell.

Kromě těchto prostředků doporučujeme zkontrolovat [Další témata související se spouštěním SQL Server ve službě Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
