---
title: Vytvoření klasického virtuálního virtuálního zařízení SQL Server (PowerShell)
description: Poskytuje kroky a skripty Prostředí PowerShell pro vytváření virtuálního počítače Azure s ibi image galerie virtuálních počítačů SQL Server. Toto téma používá klasický režim nasazení.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914785"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Zřízení virtuálního počítače s SQL Serverem přes Azure PowerShell (Classic)

[!INCLUDE [classic-vm-deprecation](../../../../includes/classic-vm-deprecation.md)]

Tento článek obsahuje postup, jak vytvořit virtuální počítač SQL Server v Azure pomocí rutin Prostředí PowerShell.

> [!NOTE] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití modelu klasické nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Verze tohoto tématu správce prostředků najdete v [tématu Zřízení virtuálního počítače SQL Serveru pomocí Azure PowerShell Resource Manager](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Instalace a konfigurace PowerShellu:
1. Pokud účet Azure nemáte, můžete začít používat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Stáhněte a nainstalujte nejnovější příkazy Azure PowerShellu](/powershell/azure/overview).
3. Spusťte Windows PowerShell a připojte ho k předplatnému Azure pomocí příkazu **Přidat azureúčet.**

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Určení cílové oblasti Azure

Virtuální počítač SQL Serveru bude hostovaný v cloudové službě, která sídlí v konkrétní oblasti Azure. Následující kroky vám pomohou určit oblast, účet úložiště a cloudovou službu, které se budou používat pro zbytek kurzu.

1. Určete datové centrum, které chcete použít k hostování virtuálního počítače SQL Server. Následující příkaz Prostředí PowerShell zobrazí seznam dostupných názvů oblastí.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Jakmile určíte preferované umístění, nastavte proměnnou s názvem **$dcLocation** do této oblasti. Například následující příkaz nastaví oblast na "Východní USA":

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Nastavení účtu předplatného a úložiště

1. Určete předplatné Azure, které budete používat pro nový virtuální počítač.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Přiřaďte své cílové předplatné Azure proměnné **$subscr.** Pak to nastavte jako aktuální předplatné Azure.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Pak zkontrolujte existující účty úložiště. Následující skript zobrazuje všechny účty úložiště, které existují ve zvolené oblasti:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Pokud požadujete nový účet úložiště, nejprve vytvořte název účtu úložiště s nižšími písmeny pomocí příkazu New-AzureStorageAccount jako v následujícím příkladu:`New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Přiřaďte název cílového účtu úložiště **$staccount**. Pak použijte **Set-AzureSubscription** k nastavení předplatného a aktuálního účtu úložiště.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Výběr bitové kopie virtuálního počítače serveru SQL Server

1. Seznam dostupných imitací virtuálních počítačů sql serveru najdete v galerii. Všechny tyto obrázky mají **ImageFamily** vlastnost, která začíná "SQL". Následující dotaz zobrazí rodinu bitových obrázků, které jsou k dispozici, které mají PŘEDinstalovaný SQL Server.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Když najdete rodinu obrázků virtuálního počítače, může být v této rodině více publikovaných obrázků. Pomocí následujícího skriptu vyhledejte nejnovější název publikované image virtuálního počítače pro vybranou rodinu bitových bitů **(například SQL Server 2016 RTM Enterprise v systému Windows Server 2012 R2):**

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

Nakonec vytvořte virtuální počítač s PowerShellem:

1. Vytvořte cloudovou službu pro hostování nového virtuálního mísu. Všimněte si, že je také možné použít existující cloudové služby místo. Vytvořte novou proměnnou **$svcname** s krátkým názvem cloudové služby.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Zadejte název virtuálního počítače a velikost. Další informace o velikostech virtuálních strojů najdete [v tématu Velikosti virtuálních strojů pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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

4. Chcete-li vytvořit virtuální počítač, spusťte následující skript.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Další vysvětlení a možnosti konfigurace najdete v části **Vytvoření sady příkazů** v části Vytvoření Prostředí Azure [PowerShell k vytvoření a předkonfiguraci virtuálních počítačů se systémem Windows](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Ukázkový skript PowerShell

Následující skript poskytuje příklad úplného skriptu, který vytvoří **SQL Server 2016 RTM Enterprise ve virtuálním počítači se systémem Windows Server 2012 R2.** Pokud použijete tento skript, je nutné přizpůsobit počáteční proměnné na základě předchozích kroků v tomto tématu.

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

## <a name="connect-with-remote-desktop"></a>Připojení ke vzdálené ploše

1. Vytvořte soubory RDP ve složce dokumentu aktuálního uživatele a spusťte tyto virtuální počítače k dokončení instalace:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. V adresáři dokumentů spusťte soubor RDP. Připojte se k dříve poskytnutému uživatelskému jménu a heslu správce (například pokud bylo uživatelské jméno Správce v systému VMAdmin, zadejte jako uživatele "\VMAdmin" a zadejte heslo).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Dokončení konfigurace počítače SQL Server Pro vzdálený přístup

Po přihlášení k počítači pomocí vzdálené plochy nakonfigurujte SQL Server na základě pokynů v [krocích ke konfiguraci připojení serveru SQL Server ve virtuálním počítači Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Další kroky

Další pokyny pro zřizování virtuálních počítačů pomocí PowerShellu najdete v dokumentaci k [virtuálním počítačům](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

V mnoha případech je dalším krokem migrace databází do tohoto nového virtuálního počítače SQL Server. Pokyny k migraci databáze najdete [v tématu Migrace databáze na SQL Server na virtuálním počítači Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Pokud máte taky zájem o použití portálu Azure k vytvoření virtuálních počítačů SQL, přečtěte si informace [o zřizování virtuálního počítače SQL Serveru v Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Všimněte si, že kurz, který vás provede portálem vytvoří virtuální chodpomocí doporučeného modelu Resource Manager, nikoli klasický model používaný v tomto tématu prostředí PowerShell.

Kromě těchto prostředků doporučujeme zkontrolovat [další témata související se spuštěným SQL Serverem ve virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
