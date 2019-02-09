---
title: Vytvoření virtuálního počítače s Windows ze specializovaného virtuálního pevného disku v Azure | Dokumentace Microsoftu
description: Vytvořte nový virtuální počítač Windows připojením specializované spravovaného disku jako disku s operačním systémem pomocí modelu nasazení Resource Manager.
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
ms.date: 10/10/2018
ms.author: cynthn
ms.openlocfilehash: 662713a5ef350bd34f25558de69e3cbfd5fc80a3
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982858"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Vytvoření virtuálního počítače s Windows ze specializovaného disku pomocí prostředí PowerShell

Vytvoření nového virtuálního počítače připojením specializované spravovaného disku jako disku s operačním systémem. Specializovaného disku je kopie virtuálního pevného disku (VHD) ze stávajícího virtuálního počítače, který obsahuje uživatelské účty, aplikace a další data o stavu z původního virtuálního počítače. 

Při vytvoření nového virtuálního počítače pomocí specializovaného virtuálního pevného disku, nový virtuální počítač zachová název počítače z původního virtuálního počítače. Další informace specifické pro počítač se také ukládají a v některých případech se tyto duplicitní informace může způsobit problémy. Při kopírování virtuálního počítače, třeba vědět, jaké typy informací specifických pro počítač využívají vaše aplikace.

Máte několik možností:
* [Použití existujícího spravovaného disku](#option-1-use-an-existing-disk). Tato možnost je užitečná, pokud máte virtuální počítač, který nepracuje správně. Můžete odstranit virtuální počítač a pak znovu použít spravovaného disku k vytvoření nového virtuálního počítače. 
* [Nahrání virtuálního pevného disku](#option-2-upload-a-specialized-vhd) 
* [Kopírování existujících virtuálních počítačů Azure pomocí snímků](#option-3-copy-an-existing-azure-vm)

Můžete použít také na webu Azure portal [vytvoření nového virtuálního počítače z specializovaného VHD](create-vm-specialized-portal.md).

V tomto článku se dozvíte, jak používat spravované disky. Pokud máte starší verzi nasazení, která vyžaduje použití účtu úložiště, najdete v článku [vytvoření virtuálního počítače ze specializovaného VHD v účtu úložiště](sa-create-vm-specialized.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="option-1-use-an-existing-disk"></a>Option 1: Použít stávající disk

Pokud jste měli virtuální počítač, který jste odstranili a znovu použít disk s operačním systémem vytvořte nový virtuální počítač, použijte [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk?view=azurermps-6.8.1).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Teď můžete připojit tento disk jako disk s operačním systémem k [nový virtuální počítač](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Option 2: Nahrání specializovaného virtuálního pevného disku

Můžete nahrát virtuální pevný disk ze specializovaného virtuálního počítače vytvoří místní virtualizace nástroj, jako je Hyper-V nebo virtuální počítač exportovat z jiného cloudu.

### <a name="prepare-the-vm"></a>Příprava virtuálního počítače
Použít virtuální pevný disk jako-je vytvoření nového virtuálního počítače. 
  
  * [Příprava virtuálního pevného disku Windows k nahrání do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Ne** generalizace virtuálního počítače pomocí nástroje Sysprep.
  * Odeberte všechny hostované virtualizačních nástrojů a agenty, kteří jsou nainstalováni na virtuálním počítači (například nástroje VMware).
  * Ujistěte se, že virtuální počítač je nakonfigurovaný tak, nastavení DNS IP adresy a ze serveru DHCP. Tím se zajistí, že server při spuštění získá IP adresu v rámci virtuální sítě. 


### <a name="get-the-storage-account"></a>Získat účet úložiště.
Budete potřebovat účet úložiště v Azure k ukládání nahraný virtuální pevný disk. Můžete použít existující účet úložiště nebo vytvořte novou. 

Zobrazit účty úložiště k dispozici.

```powershell
Get-AzStorageAccount
```

Pokud chcete použít existující účet úložiště, pokračujte [nahrát virtuální pevný disk](#upload-the-vhd-to-your-storage-account) oddílu.

Vytvoření účtu úložiště

1. Budete potřebovat název skupiny prostředků, ve kterém se vytvoří účet úložiště. Použijte Get-AzResourceGroup zobrazit všechny skupiny prostředků, které jsou v rámci vašeho předplatného.
   
    ```powershell
    Get-AzResourceGroup
    ```

    Vytvořte skupinu prostředků s názvem *myResourceGroup* v *USA – západ* oblasti.

    ```powershell
    New-AzResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Vytvoření účtu úložiště s názvem *mystorageaccount* v nové skupině prostředků s použitím [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) rutiny.
   
    ```powershell
    New-AzStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>Nahrání virtuálního pevného disku do účtu úložiště 
Použití [přidat AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) rutiny k nahrání virtuálního pevného disku do kontejneru v účtu úložiště. Tento příklad nahraje soubor *myVHD.vhd* z "C:\Users\Public\Documents\Virtual pevné disky\" na účet úložiště s názvem *mystorageaccount* v  *myResourceGroup* skupinu prostředků. Soubor je uložen v kontejneru s názvem *mycontainer* a nový název souboru bude *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Příkazy jsou úspěšné, získáte odpověď bude vypadat nějak takto:

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

V závislosti na připojení k síti a velikost souboru virtuálního pevného disku tohoto příkazu může chvíli trvat.

### <a name="create-a-managed-disk-from-the-vhd"></a>Vytvoření spravovaného disku z virtuálního pevného disku

Vytvoření spravovaného disku ze specializovaného VHD v účtu úložiště pomocí [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). Tento příklad používá *myOSDisk1* pro název disku umístí disku *Standard_LRS* úložiště a použije *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* jako identifikátor URI pro zdrojový virtuální pevný disk.

Vytvořte novou skupinu prostředků pro nový virtuální počítač.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Vytvoření nového disku operačního systému z nahraných virtuálního pevného disku. 

```powershell
$sourceUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
$osDiskName = 'myOsDisk'
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>Možnost 3: Zkopírovat existující virtuální počítač Azure

Můžete vytvořit kopii tohoto virtuálního počítače, který používá spravované disky pořízení snímku virtuálního počítače a pomocí tohoto snímku pro vytvoření nového spravovaného disku a nového virtuálního počítače.


### <a name="take-a-snapshot-of-the-os-disk"></a>Pořízení snímku disku s operačním systémem

Udělat snímek celého virtuálního počítače (včetně všech disků) nebo pouze jedním diskem. Následující kroky ukazují, jak k vytvoření snímku jenom disk s operačním systémem virtuálního počítače pomocí [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) rutiny. 

Nejprve nastavte některé parametry. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Získejte objekt virtuálního počítače.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Získejte název disku operačního systému.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Vytvořte snímek konfigurace. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Snímek.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Pokud chcete použít tento snímek vytvořit virtuální počítač, který má být vysoce výkonné, přidejte parametr `-AccountType Premium_LRS` příkazu New-AzSnapshot. Tento parametr vytvoří snímek tak, aby se ukládá jako spravovaný Disk úrovně Premium. Jsou dražší než Standard Managed Disks úrovně Premium, proto buďte opravdu že Premium budete potřebovat před použitím tohoto parametru.

### <a name="create-a-new-disk-from-the-snapshot"></a>Vytvoření nového disku ze snímku

Vytvoření spravovaného disku ze snímku pomocí [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). Tento příklad používá *myOSDisk* pro název disku.

Vytvořte novou skupinu prostředků pro nový virtuální počítač.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Nastavte název disku operačního systému. 

```powershell
$osDiskName = 'myOsDisk'
```

Vytvoření spravovaného disku.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Vytvoření nového virtuálního počítače 

Vytvoření sítě a další prostředky virtuálních počítačů pro nový virtuální počítač.

### <a name="create-the-subnet-and-virtual-network"></a>Vytvoření podsítě a virtuální sítě

Vytvořte [virtuální sítě](../../virtual-network/virtual-networks-overview.md) a podsíť pro virtuální počítač.

1. Vytvořte podsíť. Tento příklad vytvoří podsíť s názvem *mySubNet*, ve skupině prostředků *myDestinationResourceGroup*a nastaví předponu adresy podsítě na *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Vytvořte virtuální síť. V tomto příkladu nastaví na název virtuální sítě *myVnetName*, umístění pro *USA – západ*a předpony adresy pro virtuální síť k *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvořte skupinu zabezpečení sítě a pravidlo protokolu RDP
Aby bylo možné se přihlásit k virtuálnímu počítači pomocí protokolu vzdálené plochy (RDP), musíte mít pravidlo zabezpečení, které povoluje přístup protokolu RDP na portu 3389. V našem příkladu byl vytvořen virtuální pevný disk pro nový virtuální počítač z existujícího specializovaného virtuálního počítače, proto můžete použít účet, který existoval na zdrojovém virtuálním počítači pro protokol RDP.

V tomto příkladu nastaví název skupinu zabezpečení sítě na *myNsg* a názvu pravidlo protokolu RDP *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Další informace o koncových bodů a pravidel NSG najdete v tématu [otevření portů k virtuálnímu počítači v Azure pomocí prostředí PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Vytvoření veřejné IP adresy a síťové karty
Pokud chcete povolit komunikaci s virtuálním počítačem ve virtuální síti, budete potřebovat [veřejnou IP adresu](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) a síťové rozhraní.

1. Vytvoření veřejné IP adresy. V tomto příkladu je název veřejné IP adresy nastavena *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Vytvořte síťové rozhraní V tomto příkladu je název síťové karty nastavený na *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Nastavte název virtuálního počítače a velikost

V tomto příkladu nastaví na název virtuálního počítače *myVM* a velikost virtuálního počítače do *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Přidání síťového rozhraní
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Přidat disk s operačním systémem 

Přidat disk s operačním systémem v konfiguraci s použitím [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). V tomto příkladu nastaví velikost disku, který se *128 GB* a připojí spravovaného disku jako *Windows* disk s operačním systémem.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Dokončete tento virtuální počítač 

Vytvoření virtuálního počítače pomocí [rutiny New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) s konfiguracemi, které jsme právě vytvořili.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Pokud tento příkaz je úspěšný, zobrazí se výstup podobný tomuto:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Ověřte, že se vytvořil virtuální počítač
Nově vytvořený virtuální počítač by se měla zobrazit buď v [webu Azure portal](https://portal.azure.com) pod **Procházet** > **virtuálních počítačů**, nebo pomocí následujících příkazů Powershellu.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Další postup
Přihlaste se k novému virtuálnímu počítači. Další informace najdete v tématu [jak se připojit a přihlaste se na virtuálním počítači Azure s Windows](connect-logon.md).

