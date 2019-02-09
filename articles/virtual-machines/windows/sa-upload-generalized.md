---
title: Nahrát generalizace virtuálního pevného disku pro vytvoření několika virtuálních počítačů v Azure | Dokumentace Microsoftu
description: Nahrání generalizovaného virtuálního pevného disku do účtu úložiště Azure k vytvoření virtuálního počítače s Windows pomocí modelu nasazení Resource Manager.
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
ms.openlocfilehash: a8aa00a3bc74c811d7c57db878df0758aa054bb9
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978642"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Nahrání generalizovaného virtuálního pevného disku do Azure a vytvořte nový virtuální počítač

Toto téma popisuje nahrání generalizovaného nespravovaného disku do účtu úložiště a pak vytvořit nový virtuální počítač pomocí nahrané disku. Generalizované image virtuálního pevného disku byla všechny informace vašeho osobního účtu odebrat, pomocí nástroje Sysprep. 

Pokud chcete k vytvoření virtuálního počítače ze specializovaného VHD v účtu úložiště, přečtěte si téma [vytvoření virtuálního počítače ze specializovaného VHD](sa-create-vm-specialized.md).

Toto téma popisuje používání účtů úložiště, ale doporučujeme zákazníkům přesunutí místo toho použít Managed Disks. Kompletní návod, jak o tom, jak připravit, nahrání a vytvoření nového virtuálního počítače pomocí spravované disky, najdete v části [vytvoření nového virtuálního počítače z generalizovaného virtuálního pevného disku nahráli do Azure pomocí Managed Disks](upload-generalized-managed.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prepare-the-vm"></a>Příprava virtuálního počítače

Generalizovaného virtuálního pevného disku byla všechny informace vašeho osobního účtu odebrat, pomocí nástroje Sysprep. Pokud máte v úmyslu použít virtuální pevný disk jako image k vytvoření nové virtuální počítače z, měli byste:
  
  * [Příprava virtuálního pevného disku Windows k nahrání do Azure](prepare-for-upload-vhd-image.md). 
  * Generalizace virtuálního počítače pomocí nástroje Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalizace virtuálního počítače s Windows pomocí nástroje Sysprep
Tato část ukazuje, jak zobecnit pro použití jako image virtuálního počítače Windows. Nástroj Sysprep kromě jiného odebere všechny informace o vašich osobních účtech a připraví počítač, aby se dal použít jako image. Podrobnosti o nástroji Sysprep najdete v tématu [použití nástroje Sysprep: Úvod](https://technet.microsoft.com/library/bb457073.aspx).

Ujistěte se, že na počítači spuštěná role serveru jsou podporované pomocí nástroje Sysprep. Další informace najdete v tématu [podpory nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Pokud používáte nástroj Sysprep před nahráním vašeho virtuálního pevného disku do Azure poprvé, ujistěte se, že máte [připravit virtuální počítač](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) před spuštěním nástroje Sysprep. 
> 
> 

1. Přihlaste se k virtuálnímu počítači Windows.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na **%windir%\system32\sysprep**a pak spusťte `sysprep.exe`.
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte **Zobrazit prostředí prvního spuštění počítače** a ujistěte se, že je zaškrtnuté políčko **Generalizovat**.
4. V **možnosti vypnutí**vyberte **vypnutí**.
5. Klikněte na **OK**.
   
    ![Spustit Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po dokončení nástroj Sysprep vypne virtuální počítač. 

> [!IMPORTANT]
> Virtuální počítač nerestartuje, dokud nedokončíte nahrání virtuálního pevného disku do Azure nebo vytvoření image z virtuálního počítače. Pokud omylem získá restartování virtuálního počítače, spusťte nástroj Sysprep k zobecnění znovu.
> 
> 


## <a name="upload-the-vhd"></a>Nahrání virtuálního pevného disku

Nahrání virtuálního pevného disku do účtu služby Azure storage.

### <a name="log-in-to-azure"></a>Přihlášení k Azure
Pokud ještě nemáte PowerShell verze 1.4 nebo vyšší nainstalovaný, přečtěte si [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

1. Otevřete prostředí Azure PowerShell a přihlaste se ke svému účtu Azure. K zadání přihlašovacích údajů k účtu Azure se otevře automaticky otevírané okno.
   
    ```powershell
    Connect-AzAccount
    ```
2. Získejte ID předplatného pro předplatné k dispozici.
   
    ```powershell
    Get-AzSubscription
    ```
3. Nastavit správné předplatné pomocí ID předplatného. Nahraďte `<subscriptionID>` s ID správné předplatné.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Získat účet úložiště.
Budete potřebovat účet úložiště v Azure k uložení této odeslané image virtuálního počítače. Můžete použít existující účet úložiště nebo vytvořte novou. 

Pokud chcete zobrazit účty úložiště k dispozici, zadejte:

```powershell
Get-AzStorageAccount
```

Pokud chcete použít existující účet úložiště, pokračujte k nahrávání oddíl image virtuálního počítače.

Pokud potřebujete vytvořit účet úložiště, postupujte podle těchto kroků:

1. Budete potřebovat název skupiny prostředků, ve kterém by měl vytvořit účet úložiště. Pokud chcete zjistit všechny skupiny prostředků, které jsou ve vašem předplatném, zadejte:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Chcete-li vytvořit skupinu prostředků s názvem **myResourceGroup** v **USA – západ** oblast, typ:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Vytvoření účtu úložiště s názvem **mystorageaccount** v této skupině prostředků s použitím [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) rutiny:
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Spustit nahrávání 

Použití [přidat AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) rutiny pro nahrání obrázku do kontejneru v účtu úložiště. Tento příklad nahraje soubor **myVHD.vhd** z `"C:\Users\Public\Documents\Virtual hard disks\"` na účet úložiště s názvem **mystorageaccount** v **myResourceGroup** skupinu prostředků. Soubor se umístí do kontejneru s názvem **mycontainer** a nový název souboru bude **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


V případě úspěchu se získat odpověď bude vypadat nějak takto:

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

V závislosti na připojení k síti a velikost souboru virtuálního pevného disku tohoto příkazu může trvat dobu.


## <a name="create-a-new-vm"></a>Vytvoření nového virtuálního počítače 

Nahraný virtuální pevný disk můžete teď použít k vytvoření nového virtuálního počítače. 

### <a name="set-the-uri-of-the-vhd"></a>Nastavte identifikátor URI virtuálního pevného disku

Identifikátor URI pro VHD, který chcete použít je ve formátu: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**VHD. V tomto příkladu virtuální pevný disk s názvem **myVHD** je v účtu úložiště **mystorageaccount** v kontejneru **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořit virtuální síť a podsíť [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

1. Vytvořte podsíť. Následující příklad vytvoří podsíť s názvem **mySubnet** ve skupině prostředků **myResourceGroup** s předponou adresy **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Vytvořte virtuální síť. Následující příklad vytvoří virtuální síť s názvem **myVnet** v **USA – západ** umístění s předponou adresy **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Vytvoření veřejné IP adresy a síťového rozhraní
Pokud chcete povolit komunikaci s virtuálním počítačem ve virtuální síti, budete potřebovat [veřejnou adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) a síťové rozhraní.

1. Vytvoření veřejné IP adresy. Tento příklad vytvoří veřejnou IP adresu s názvem **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Vytvořte síťové rozhraní Tento příklad vytvoří síťové rozhraní s názvem **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvořte skupinu zabezpečení sítě a pravidlo protokolu RDP
Abyste mohli pro přihlášení k virtuálnímu počítači pomocí protokolu RDP, budete muset mít pravidlo zabezpečení, které povoluje přístup protokolu RDP na portu 3389. 

Tento příklad vytvoří skupinu zabezpečení sítě s názvem **myNsg** , který obsahuje pravidlo s názvem **myRdpRule** , která umožní provoz protokolu RDP přes port 3389. Další informace o skupinách Nsg najdete v tématu [otevření portů k virtuálnímu počítači v Azure pomocí Powershellu](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Vytvořte proměnnou pro virtuální síť
Vytvořte proměnnou pro dokončené virtuální sítě. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Vytvořte virtuální počítač.
Následující skript prostředí PowerShell ukazuje, jak nastavit konfigurací virtuálních počítačů a používat této odeslané image virtuálního počítače jako zdroj pro novou instalaci.



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
    $storageAcc = Get-AzStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Ověřte, že se vytvořil virtuální počítač
Jakmile budete hotovi, zobrazí se nově vytvořenému virtuálnímu počítači v [webu Azure portal](https://portal.azure.com) pod **Procházet** > **virtuálních počítačů**, nebo použijte následující příkaz Powershellu příkazy:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Další postup
Ke správě vašeho nového virtuálního počítače pomocí Azure Powershellu, najdete v článku [Správa virtuálních počítačů pomocí Azure Resource Manageru a Powershellu](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


