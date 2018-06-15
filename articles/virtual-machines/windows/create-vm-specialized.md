---
title: Vytvoření virtuálního počítače s Windows z specializované virtuálního pevného disku v Azure | Microsoft Docs
description: Vytvořte nový virtuální počítač Windows připojením specializované spravované disk jako disk operačního systému, použití v modelu nasazení Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: cynthn
ms.openlocfilehash: be7933b038fb5a648249e9b0c73415bff778930b
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012780"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-using-powershell"></a>Vytvoření virtuálního počítače s Windows z disku specializované pomocí prostředí PowerShell

Vytvoření nového virtuálního počítače připojením specializované spravované disk jako disk operačního systému. Specializované disk je kopie virtuálního pevného disku (VHD) ze stávajícího virtuálního počítače, který uchovává uživatelské účty, aplikace a další data o stavu z vašeho původního virtuálního počítače. 

Pokud použijete specializované virtuálního pevného disku pro vytvoření nového virtuálního počítače, nový virtuální počítač zachová název počítače původní virtuální počítač. Další informace specifické pro počítač se také nacházet a v některých případech tyto duplicitní informace může způsobit problémy. Být vědět, jaké typy informace specifické pro počítač aplikace se spoléhají na při kopírování virtuálního počítače.

Máte několik možností:
* [Použít stávající disk, spravované](#option-1-use-an-existing-disk). To je užitečné, pokud máte virtuální počítač, který není správně funguje. Opakované použití virtuálních počítačů spravovaných disku pro vytvoření nového virtuálního počítače, můžete odstranit. 
* [Nahrání virtuálního pevného disku](#option-2-upload-a-specialized-vhd) 
* [Zkopírujte existující virtuální počítač Azure pomocí snímků](#option-3-copy-an-existing-azure-vm)

Můžete také použít portál Azure [vytvoření nového virtuálního počítače z disku VHD specializované](create-vm-specialized-portal.md).

Toto téma ukazuje, jak používat spravovaného disky. Pokud máte starší verzi nasazení, vyžaduje použití účtu úložiště najdete v tématu [vytvoření virtuálního počítače z specializované virtuálního pevného disku v účtu úložiště](sa-create-vm-specialized.md)

## <a name="before-you-begin"></a>Než začnete
Pokud používáte prostředí PowerShell, ujistěte se, že máte nejnovější verzi modulu prostředí AzureRM.Compute PowerShell. 

```powershell
Install-Module AzureRM -RequiredVersion 6.0.0
```
Další informace najdete v tématu [Azure PowerShell verze](/powershell/azure/overview).

## <a name="option-1-use-an-existing-disk"></a>Možnost 1: Použití existujícího disku

Pokud jste měli virtuální počítač, který jste odstranili a chcete znovu použít disk operačního systému, který chcete vytvořit nový virtuální počítač, použijte [Get-AzureRmDisk](/azure/powershell/get-azurermdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzureRmDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Nyní můžete připojit tento disk jako disk operačního systému [nový virtuální počítač](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Možnost 2: Nahrání specializované virtuálního pevného disku

Můžete nahrát virtuální pevný disk z virtuálního počítače specializované vytvořené pomocí místní virtualizace nástroje, jako je technologie Hyper-V nebo na virtuální počítač exportovat z jiného cloudu.

### <a name="prepare-the-vm"></a>Příprava virtuálního počítače
Pokud máte v úmyslu použít virtuální pevný disk jako-je chcete vytvořit nový virtuální počítač, zkontrolujte následující kroky. 
  
  * [Příprava virtuálního pevného disku Windows nahrát do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Nechcete** generalize virtuální počítač pomocí nástroje Sysprep.
  * Odeberte všechny hosta virtualizačních nástrojů a agentů, které jsou nainstalovány na virtuálním počítači (např. nástroje VMware).
  * Zajistěte, aby že virtuální počítač nakonfigurovaný tak, aby jeho IP adresu a nastavení DNS pomocí protokolu DHCP pro vyžádání obsahu. To zajistí, že server získá IP adresu v rámci virtuální sítě, při spuštění. 


### <a name="get-the-storage-account"></a>Získat účet úložiště
Budete potřebovat účet úložiště v Azure k ukládání nahraný virtuální pevný disk. Můžete použít existující účet úložiště, nebo vytvořte novou. 

Chcete-li zobrazit účty úložiště k dispozici, zadejte:

```powershell
Get-AzureRmStorageAccount
```

Pokud chcete použít existující účet úložiště, pokračujte [nahrávat VHD](#upload-the-vhd-to-your-storage-account) části.

Pokud potřebujete vytvořit účet úložiště, postupujte takto:

1. Je třeba na název skupiny prostředků, kde by měl být vytvořen účet úložiště. Chcete-li zjistit všechny skupiny prostředků, které jsou v rámci vašeho předplatného, zadejte:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Chcete-li vytvořit skupinu prostředků s názvem *myResourceGroup* v *západní USA* oblast, zadejte:

    ```powershell
    New-AzureRmResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Vytvořit účet úložiště s názvem *můj_účet_úložiště* v této skupině prostředků s použitím [AzureRmStorageAccount nový](/powershell/module/azurerm.storage/new-azurermstorageaccount) rutiny:
   
    ```powershell
    New-AzureRmStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>Nahrání virtuálního pevného disku do účtu úložiště 
Použití [přidat AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) rutiny odeslání disku VHD do kontejneru v účtu úložiště. Tento příklad nahrávání souboru *myVHD.vhd* z `"C:\Users\Public\Documents\Virtual hard disks\"` na účet úložiště s názvem *můj_účet_úložiště* v *myResourceGroup* skupinu prostředků. Soubor je uložen v kontejneru s názvem *můj_kontejner* a nový název souboru bude *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Pokud bylo úspěšné, můžete získat odpovědi, která vypadá podobně jako tento:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

V závislosti na připojení k síti a velikost souboru virtuálního pevného disku tento příkaz může trvat nějakou dobu pro dokončení

### <a name="create-a-managed-disk-from-the-vhd"></a>Vytvoření spravovaného disku z virtuálního pevného disku

Vytvoření spravovaného disku z specializované virtuálního pevného disku v účtu úložiště pomocí [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Tento příklad používá **myOSDisk1** pro název disku vloží disku *Standard_LRS* úložiště a používá *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* jako identifikátor URI pro zdrojové virtuální pevný disk.

Vytvořte novou skupinu prostředků pro nový virtuální počítač.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Vytvořte nový disk operačního systému z nahraný virtuální pevný disk. 

```powershell
$sourceUri = (https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd)
$osDiskName = 'myOsDisk'
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>Možnost 3: Zkopírujte existující virtuální počítač Azure

Můžete vytvořit kopii virtuálního počítače, že používá spravuje disky pořízení snímku virtuálního počítače, a potom pomocí tohoto snímku pro vytvoření nového spravovaného disku a vytvoření nového virtuálního počítače.


### <a name="take-a-snapshot-of-the-os-disk"></a>Pořízení snímku disk operačního systému

Můžete provést z snímek a celý virtuální počítač (včetně všech disků) nebo právě jeden disk. Následující kroky ukazují, jak k pořízení snímku právě disk operačního systému z virtuální počítač pomocí [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) rutiny. 

Nastavte některé parametry. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Získejte objekt virtuálního počítače.

```powershell
$vm = Get-AzureRmVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Získáte název disku operačního systému.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Vytvoření snímku konfigurace. 

 ```powershell
$snapshotConfig =  New-AzureRmSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Vytvořte snímek.

```powershell
$snapShot = New-AzureRmSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Pokud máte v úmyslu vytvořit virtuální počítač, který musí být vysoké provádění pomocí snímku, použijte parametr `-AccountType Premium_LRS` pomocí příkazu New-AzureRmSnapshot. Parametr vytvoří snímek tak, aby je uložena jako spravovaný Disk úrovně Premium. Premium spravované disky jsou dražší než Standard. Proto ujistěte se, že je skutečně potřebujete Premium před použitím parametru.

### <a name="create-a-new-disk-from-the-snapshot"></a>Vytvoření nového disku ze snímku

Vytvoření spravovaného disku pomocí snímku [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Tento příklad používá *myOSDisk* pro název disku.

Vytvořte novou skupinu prostředků pro nový virtuální počítač.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Nastavte název disku operačního systému. 

```powershell
$osDiskName = 'myOsDisk'
```

Vytvoření spravovaného disku.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Vytvoření nového virtuálního počítače 

Vytvoření sítě a další prostředky virtuálních počítačů, které má být používána nového virtuálního počítače.

### <a name="create-the-subnet-and-vnet"></a>Vytvoření podsítě a sítě vNet

Vytvořit virtuální síť a podsíť [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

Vytvořte podsíť. Tento příklad vytvoří podsíť s názvem **mySubNet**, ve skupině prostředků **myDestinationResourceGroup**a nastaví předpona adresy podsítě **10.0.0.0/24**.
   
```powershell
$subnetName = 'mySubNet'
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
   -Name $subnetName `
   -AddressPrefix 10.0.0.0/24
```

Vytvořte síť vNet. Tento příklad nastaví název virtuální sítě, který se má **myVnetName**, umístění, do **západní USA**a předponu adresy virtuální sítě, abyste **10.0.0.0/16**. 
   
```powershell
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork `
   -Name $vnetName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $singleSubnet
```    


### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvořte skupinu zabezpečení sítě a pravidlo protokolu RDP
Abyste mohli přihlásit k virtuálnímu počítači pomocí protokolu RDP, musíte mít pravidlo zabezpečení, která umožňuje přístup RDP na portu 3389. Protože virtuálního pevného disku pro nový virtuální počítač byl vytvořen z existující speciální virtuální počítač, můžete použít účet ze zdrojového virtuálního počítače pro protokol RDP.

Tento příklad nastaví název skupiny NSG na **myNsg** a název pravidla protokolu RDP na **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Další informace o koncových bodů a pravidla NSG najdete v tématu [otevřít porty pro virtuální počítač v Azure pomocí prostředí PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Vytvoření veřejné IP adresy a síťové karty
Pokud chcete povolit komunikaci s virtuálním počítačem ve virtuální síti, budete potřebovat [veřejnou adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) a síťové rozhraní.

Vytvoření veřejné IP adresy. V tomto příkladu je název veřejné IP adresy nastavena **myIP**.
   
```powershell
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress `
   -Name $ipName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AllocationMethod Dynamic
```       

Vytvořit síťovou kartu. V tomto příkladu je síťový adaptér je nastavit název **myNicName**.
   
```powershell
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location -SubnetId $vnet.Subnets[0].Id `
   -PublicIpAddressId $pip.Id `
   -NetworkSecurityGroupId $nsg.Id
```



### <a name="set-the-vm-name-and-size"></a>Nastavte název virtuálního počítače a velikosti

Tento příklad nastaví název virtuálního počítače na *Můjvp* a velikost virtuálního počítače na *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Přidání síťového adaptéru
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Přidat disk operačního systému 

Přidat disk operačního systému do konfigurace pomocí [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk). Tento příklad nastaví velikost disku k *128 GB* a připojí spravovaných disků jako *Windows* disk operačního systému.
 
```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Dokončení virtuálního počítače 

Vytvořit virtuální počítač pomocí [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm)konfigurace, které jsme právě vytvořili.

```powershell
New-AzureRmVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Pokud tento příkaz byl úspěšný, zobrazí se výstup takto:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Zkontrolujte, zda byl vytvořen virtuální počítač
Měli byste vidět nově vytvořený virtuální počítač buď v [portál Azure](https://portal.azure.com)v části **Procházet** > **virtuální počítače**, nebo pomocí následujících příkazů prostředí PowerShell:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Další postup
Přihlaste se do nového virtuálního počítače. Další informace najdete v tématu [jak se připojit a přihlásit se na virtuálním počítači Azure s Windows](connect-logon.md).

