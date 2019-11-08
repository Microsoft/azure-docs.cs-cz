---
title: Nahrání virtuálního pevného disku pomocí generalizace pro vytvoření více virtuálních počítačů v Azure | Microsoft Docs
description: Nahrajte zobecněný virtuální pevný disk do účtu služby Azure Storage a vytvořte virtuální počítač s Windows pro použití s modelem nasazení Správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 24ed7b75dfa8cb09c530a3f4a896aa9ff9aa92b5
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749176"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Nahrání zobecněného virtuálního pevného disku do Azure za účelem vytvoření nového virtuálního počítače

Toto téma popisuje nahrání zobecněného nespravovaného disku do účtu úložiště a následné vytvoření nového virtuálního počítače pomocí nahraného disku. K generalizované imagi virtuálního pevného disku se odebraly všechny informace o vašem osobním účtu pomocí nástroje Sysprep. 

Pokud chcete vytvořit virtuální počítač ze specializovaného virtuálního pevného disku v účtu úložiště, přečtěte si téma [Vytvoření virtuálního počítače ze specializovaného virtuálního pevného disku](sa-create-vm-specialized.md).

Toto téma se věnuje používání účtů úložiště, ale doporučujeme zákazníkům, aby se místo toho přesunuli pomocí Managed Disks. Kompletní návod, jak připravit, nahrát a vytvořit nový virtuální počítač pomocí spravovaných disků, najdete v tématu [Vytvoření nového virtuálního počítače z zobecněného virtuálního pevného disku nahraného do Azure pomocí Managed disks](upload-generalized-managed.md).

 

## <a name="prepare-the-vm"></a>Příprava virtuálního počítače

Zobecněný virtuální pevný disk měl odebrat všechny informace o osobním účtu pomocí nástroje Sysprep. Pokud máte v úmyslu použít VHD jako image k vytváření nových virtuálních počítačů z nástroje, měli byste:
  
  * [Připravte si virtuální pevný disk Windows, který se má nahrát do Azure](prepare-for-upload-vhd-image.md). 
  * Generalizace virtuálního počítače pomocí nástroje Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalizace virtuálního počítače s Windows pomocí nástroje Sysprep
V této části se dozvíte, jak zobecnit virtuální počítač s Windows pro použití jako image. Nástroj Sysprep kromě jiného odebere všechny informace o vašich osobních účtech a připraví počítač, aby se dal použít jako image. Další informace o nástroji Sysprep najdete v článku věnovaném [úvodu do použití nástroje Sysprep](https://technet.microsoft.com/library/bb457073.aspx).

Ujistěte se, že nástroj Sysprep podporuje role serveru spuštěné v počítači. Další informace najdete v tématu [Podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) .

> [!IMPORTANT]
> Pokud spouštíte nástroj Sysprep před prvním nahráním virtuálního pevného disku do Azure, ujistěte se, že jste před spuštěním nástroje Sysprep [připravili virtuální počítač](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . 
> 
> 

1. Přihlaste se k virtuálnímu počítači s Windows.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na **%WINDIR%\system32\sysprep**a potom spusťte `sysprep.exe`.
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte **Zobrazit prostředí prvního spuštění počítače** a ujistěte se, že je zaškrtnuté políčko **Generalizovat**.
4. V **Možnosti vypnutí**vyberte **vypnout**.
5. Klikněte na tlačítko **OK**.
   
    ![Spustit nástroj Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po dokončení nástroj Sysprep vypne virtuální počítač. 

> [!IMPORTANT]
> Nerestartujte virtuální počítač, dokud nedokončíte odeslání virtuálního pevného disku do Azure nebo vytvoření image z virtuálního počítače. Pokud se virtuální počítač náhodně restartuje, spusťte Sysprep a znovu ho generalizujte.
> 
> 


## <a name="upload-the-vhd"></a>Nahrání virtuálního pevného disku

Nahrajte virtuální pevný disk do účtu služby Azure Storage.

### <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Pokud ještě nemáte nainstalované prostředí PowerShell verze 1,4 nebo novější, přečtěte si, [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

1. Otevřete Azure PowerShell a přihlaste se ke svému účtu Azure. Otevře se automaticky otevírané okno, ve kterém můžete zadat svoje přihlašovací údaje k účtu Azure.
   
    ```powershell
    Connect-AzAccount
    ```
2. Získejte ID předplatných pro vaše dostupná předplatná.
   
    ```powershell
    Get-AzSubscription
    ```
3. Nastavte správné předplatné pomocí ID předplatného. Nahraďte `<subscriptionID>` číslem správného předplatného.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Získat účet úložiště
K uložení nahrané image virtuálního počítače potřebujete účet úložiště v Azure. Můžete buď použít existující účet úložiště, nebo vytvořit nový. 

Chcete-li zobrazit dostupné účty úložiště, zadejte:

```powershell
Get-AzStorageAccount
```

Pokud chcete použít existující účet úložiště, přejděte do části nahrání image virtuálního počítače.

Pokud potřebujete vytvořit účet úložiště, použijte následující postup:

1. Potřebujete název skupiny prostředků, ve které se má účet úložiště vytvořit. Pokud chcete zjistit všechny skupiny prostředků ve vašem předplatném, zadejte:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Pokud chcete v **západní USA** oblasti vytvořit skupinu prostředků s názvem **myResourceGroup** , zadejte:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Vytvořte účet úložiště s názvem **mystorageaccount** v této skupině prostředků pomocí rutiny [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) :
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Spustit nahrávání 

Pomocí rutiny [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) nahrajte image do kontejneru v účtu úložiště. Tento příklad nahraje soubor **myVHD. VHD** z `"C:\Users\Public\Documents\Virtual hard disks\"` do účtu úložiště s názvem **mystorageaccount** ve skupině prostředků **myResourceGroup** . Soubor se umístí do kontejneru s názvem **myContainer** a nový název souboru bude **myUploadedVHD. VHD**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


V případě úspěchu získáte odpověď, která vypadá nějak takto:

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

V závislosti na připojení k síti a velikosti souboru VHD může dokončení tohoto příkazu chvíli trvat.


## <a name="create-a-new-vm"></a>Vytvořte nový virtuální počítač. 

K vytvoření nového virtuálního počítače teď můžete použít nahraný virtuální pevný disk. 

### <a name="set-the-uri-of-the-vhd"></a>Nastavte identifikátor URI virtuálního pevného disku.

Identifikátor URI pro virtuální pevný disk, který se má použít, je ve formátu: https://**mystorageaccount**. blob.core.windows.net/**myContainer**/**MyVhdName**. VHD. V tomto příkladu je virtuální pevný disk s názvem **myVHD** v účtu úložiště **mystorageaccount** v kontejneru **myContainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť a podsíť [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

1. Vytvořte podsíť. Následující příklad vytvoří podsíť s názvem **mySubnet** ve skupině prostředků **myResourceGroup** s předponou adresy **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Vytvořte virtuální síť. Následující ukázka vytvoří virtuální síť s názvem **myVnet** v umístění **západní USA** s předponou adresy **10.0.0.0/16**.  
   
    ```powershell
    $location = "WestUS"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Vytvoření veřejné IP adresy a síťového rozhraní
Pokud chcete povolit komunikaci s virtuálním počítačem ve virtuální síti, budete potřebovat [veřejnou adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) a síťové rozhraní.

1. Vytvořte veřejnou IP adresu. Tento příklad vytvoří veřejnou IP adresu s názvem **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Vytvořte síťovou kartu. Tento příklad vytvoří síťovou kartu s názvem **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvoření skupiny zabezpečení sítě a pravidla protokolu RDP
Abyste se mohli přihlásit ke svému VIRTUÁLNÍmu počítači pomocí protokolu RDP, musíte mít bezpečnostní pravidlo, které umožňuje přístup protokolu RDP na portu 3389. 

Tento příklad vytvoří NSG s názvem **myNsg** , který obsahuje pravidlo s názvem **myRdpRule** , které umožňuje provoz protokolu RDP přes port 3389. Další informace o skupin zabezpečení sítě najdete v tématu [otevření portů k virtuálnímu počítači v Azure pomocí PowerShellu](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Vytvoření proměnné pro virtuální síť
Vytvořte proměnnou pro dokončenou virtuální síť. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Vytvořte virtuální počítač.
Následující skript prostředí PowerShell ukazuje, jak nastavit konfigurace virtuálních počítačů a použít nahranou image virtuálního počítače jako zdroj pro novou instalaci.



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

## <a name="verify-that-the-vm-was-created"></a>Ověřte, že byl virtuální počítač vytvořen.
Po dokončení by se měl nově vytvořený virtuální počítač zobrazit v [Azure Portal](https://portal.azure.com) v části **Procházet** > **virtuální počítače**nebo pomocí následujících příkazů PowerShellu:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Další kroky
Pokud chcete spravovat nový virtuální počítač pomocí Azure PowerShell, přečtěte si téma [Správa virtuálních počítačů pomocí Azure Resource Manager a PowerShellu](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


