---
title: Nahrát generalizace virtuálního pevného disku pro vytvoření několika virtuálními počítači v Azure | Microsoft Docs
description: Nahrajte zobecněný virtuální pevný disk do účtu úložiště Azure k vytvoření virtuálního počítače s Windows pro použití s modelem nasazení Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 28caca06dd756732314f758e6acb4629f857dd91
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Nahrajte zobecněný virtuální pevný disk do Azure k vytvoření nového virtuálního počítače

Toto téma popisuje zobecněný nespravované disk se nahrávají na účet úložiště a pak vytvořit nový virtuální počítač pomocí nahrané disku. Bitovou kopii zobecněný virtuální pevný disk má měly všechny vaše osobní účet informace odebrat pomocí nástroje Sysprep. 

Pokud chcete vytvořit virtuální počítač z specializované virtuálního pevného disku v účtu úložiště, najdete v části [vytvoření virtuálního počítače z disku VHD specializované](sa-create-vm-specialized.md).

Toto téma popisuje používání účtů úložiště, ale doporučujeme zákazníkům přesunutí místo toho použít spravované disky. Kompletní návod jak připravit, odeslání a vytvoření nového virtuálního počítače pomocí spravovaných disků, najdete v části [vytvořit nový virtuální počítač z zobecněný virtuální pevný disk nahrán do Azure pomocí spravované disky](upload-generalized-managed.md).



## <a name="prepare-the-vm"></a>Příprava virtuálního počítače

Zobecněný virtuální pevný disk má měly všechny vaše osobní účet informace odebrat pomocí nástroje Sysprep. Pokud máte v úmyslu použít virtuální pevný disk jako bitovou kopii k vytvoření nové virtuální počítače z, proveďte následující kroky:
  
  * [Příprava virtuálního pevného disku Windows nahrát do Azure](prepare-for-upload-vhd-image.md). 
  * Virtuální počítač pomocí nástroje Sysprep generalize

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalize virtuálního počítače s Windows pomocí nástroje Sysprep
V této části se dozvíte, jak ke generalizaci virtuálního počítače Windows pro použití jako obrázek. Nástroj Sysprep odstraní všechny vaše osobní informace o účtu, mimo jiné a připraví počítač, který se má použít jako obrázek. Podrobnosti o nástroji Sysprep najdete v tématu [postup použití nástroje Sysprep: Úvod](http://technet.microsoft.com/library/bb457073.aspx).

Ujistěte se, že role serveru spuštěná na tomto počítači jsou podporovány nástrojem Sysprep. Další informace najdete v tématu [podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Pokud používáte nástroj Sysprep před nahráním svůj disk VHD do Azure poprvé, ujistěte se, máte [připravit virtuální počítač](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) před spuštěním nástroje Sysprep. 
> 
> 

1. Přihlaste se k virtuálnímu počítači Windows.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na **%windir%\system32\sysprep**a poté spusťte `sysprep.exe`.
3. V **nástroj pro přípravu systému** dialogové okno, vyberte **prostředí Out-of-Box zadejte systému (při prvním zapnutí)**a ujistěte se, že **generalizace** je zaškrtnuté políčko.
4. V **možnosti vypnutí**, vyberte **vypnutí**.
5. Klikněte na **OK**.
   
    ![Spusťte nástroj Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po dokončení nástroj Sysprep vypne virtuální počítač. 

> [!IMPORTANT]
> Virtuální počítač nerestartuje, až do dokončení odesílání virtuálního pevného disku do Azure nebo vytvoření bitové kopie z virtuálního počítače. Pokud omylem získá restartování virtuálního počítače, spusťte nástroj Sysprep ke generalizaci ho znovu.
> 
> 


## <a name="upload-the-vhd"></a>Nahrání virtuálního pevného disku

Nahrání virtuálního pevného disku do účtu úložiště Azure.

### <a name="log-in-to-azure"></a>Přihlášení k Azure
Pokud ještě nemáte prostředí PowerShell verze 1.4 nebo vyšší nainstalovaná, přečtěte si [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

1. Otevřete prostředí Azure PowerShell a přihlaste se k účtu Azure. Automaticky otevírané okno otevře zadat přihlašovací údaje účtu Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Pořiďte si předplatné ID pro dostupných předplatných.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Nastavit správné předplatné pomocí ID předplatného. Nahraďte `<subscriptionID>` s ID správné předplatné.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Získat účet úložiště
Potřebujete účet úložiště v Azure k ukládání nahrané image virtuálního počítače. Můžete použít existující účet úložiště, nebo vytvořte novou. 

Chcete-li zobrazit účty úložiště k dispozici, zadejte:

```powershell
Get-AzureRmStorageAccount
```

Pokud chcete použít existující účet úložiště, pokračujte [nahrajte image virtuálního počítače](#upload-the-vm-vhd-to-your-storage-account) části.

Pokud potřebujete vytvořit účet úložiště, postupujte takto:

1. Je třeba na název skupiny prostředků, kde by měl být vytvořen účet úložiště. Chcete-li zjistit všechny skupiny prostředků, které jsou v rámci vašeho předplatného, zadejte:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Chcete-li vytvořit skupinu prostředků s názvem **myResourceGroup** v **západní USA** oblast, zadejte:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Vytvořit účet úložiště s názvem **můj_účet_úložiště** v této skupině prostředků s použitím [AzureRmStorageAccount nový](/powershell/module/azurerm.storage/new-azurermstorageaccount) rutiny:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Spusťte 

Použití [přidat AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) rutiny Odeslat bitovou kopii do kontejneru v účtu úložiště. Tento příklad nahrávání souboru **myVHD.vhd** z `"C:\Users\Public\Documents\Virtual hard disks\"` na účet úložiště s názvem **můj_účet_úložiště** v **myResourceGroup** skupinu prostředků. Soubor se umístí do kontejner s názvem **můj_kontejner** a nový název souboru bude **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
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

V závislosti na připojení k síti a velikost souboru virtuálního pevného disku tento příkaz může trvat nějakou dobu pro dokončení.


## <a name="create-a-new-vm"></a>Vytvoření nového virtuálního počítače 

Nyní můžete nahraný virtuální pevný disk pro vytvoření nového virtuálního počítače. 

### <a name="set-the-uri-of-the-vhd"></a>Nastavit identifikátor URI virtuálního pevného disku

Identifikátor URI pro VHD, který chcete používat je ve formátu: https://**můj_účet_úložiště**.blob.core.windows.net/**můj_kontejner**/**MyVhdName**VHD. V tomto příkladu virtuální pevný disk s názvem **myVHD** je v účtu úložiště **můj_účet_úložiště** v kontejneru **můj_kontejner**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořit virtuální síť a podsíť [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

1. Vytvořte podsíť. Následující příklad vytvoří podsíť s názvem **mySubnet** ve skupině prostředků **myResourceGroup** s předponou adresy z **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Vytvořte virtuální síť. Následující ukázka vytvoří virtuální síť s názvem **myVnet** v **západní USA** umístění s předponou adresy z **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Vytvoření veřejné IP adresy a síťového rozhraní
Pokud chcete povolit komunikaci s virtuálním počítačem ve virtuální síti, budete potřebovat [veřejnou adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) a síťové rozhraní.

1. Vytvoření veřejné IP adresy. Tento příklad vytvoří veřejnou IP adresu s názvem **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Vytvořit síťovou kartu. Tento příklad vytvoří síťový adaptér s názvem **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvořte skupinu zabezpečení sítě a pravidlo protokolu RDP
Abyste mohli přihlásit k virtuálnímu počítači pomocí protokolu RDP, musíte mít pravidlo zabezpečení, která umožňuje přístup RDP na portu 3389. 

Tento příklad vytvoří skupinu NSG s názvem **myNsg** obsahující pravidlo názvem **myRdpRule** přes port 3389 umožňuje provoz protokolu RDP. Další informace o skupinách Nsg najdete v tématu [otevřít porty pro virtuální počítač v Azure pomocí prostředí PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Vytvořte proměnnou pro virtuální síť
Vytvořte proměnnou pro dokončené virtuální síť. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Vytvořte virtuální počítač.
Následující skript prostředí PowerShell ukazuje, jak nastavit konfiguraci virtuálního počítače a použít nahrané image virtuálního počítače jako zdroj pro novou instalaci.



```powershell
# Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Zkontrolujte, zda byl vytvořen virtuální počítač
Po dokončení byste měli vidět nově vytvořený virtuální počítač v [portál Azure](https://portal.azure.com) pod **Procházet** > **virtuální počítače**, nebo pomocí následujících příkazů prostředí PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Další postup
Ke správě nového virtuálního počítače v prostředí Azure PowerShell najdete [Správa virtuálních počítačů pomocí Azure Resource Manageru a prostředí PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


