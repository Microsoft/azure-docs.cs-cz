---
title: Nahrání generalizovat Virtuální pevný disk pro vytvoření více virtuálních počítačů v Azure
description: Nahrajte zobecněný virtuální pevný disk do účtu úložiště Azure a vytvořte virtuální počítač s Windows, který se bude používat s modelem nasazení Správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 7a5aa05a9045548e15aba667fdcdbd14fc8990e6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460303"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Nahrání generalizovaného virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače

Toto téma popisuje nahrání generalizovaného nespravovaného disku do účtu úložiště a vytvoření nového virtuálního počítače pomocí nahraného disku. V generalizované mši virtuálního pevného disku byly odstraněny všechny informace o vašem osobním účtu pomocí sysprepu. 

Pokud chcete vytvořit virtuální ho dchod ze specializovaného virtuálního pevného disku v účtu úložiště, přečtěte si informace [o vytvoření virtuálního virtuálního účtu ze specializovaného virtuálního pevného disku](sa-create-vm-specialized.md).

Toto téma se zabývá používáním účtů úložiště, ale doporučujeme zákazníkům přejít na použití spravovaných disků. Úplný návod, jak připravit, nahrát a vytvořit nový virtuální počítač pomocí spravovaných disků, najdete v článku [Vytvoření nového virtuálního počítače z generalizovaného virtuálního pevného disku nahraného do Azure pomocí spravovaných disků](upload-generalized-managed.md).

 

## <a name="prepare-the-vm"></a>Příprava virtuálního počítače

V generalizovaném virtuálním pevném disku byly odstraněny všechny informace o vašem osobním účtu pomocí sysprepu. Pokud máte v úmyslu použít virtuální pevný disk jako image k vytvoření nových virtuálních ms z, měli byste:
  
  * [Příprava virtuálního pevného disku windows pro nahrání do Azure](prepare-for-upload-vhd-image.md). 
  * Generalizace virtuálního počítače pomocí nástroje Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalizace virtuálního počítače se systémem Windows pomocí programu Sysprep
V této části se zobrazí, jak zobecnit virtuální počítač se systémem Windows pro použití jako bitová kopie. Nástroj Sysprep kromě jiného odebere všechny informace o vašich osobních účtech a připraví počítač, aby se dal použít jako image. Další informace o nástroji Sysprep najdete v článku věnovaném [úvodu do použití nástroje Sysprep](https://technet.microsoft.com/library/bb457073.aspx).

Ujistěte se, že role serveru spuštěné v počítači jsou podporovány programem Sysprep. Další informace naleznete v [tématu Podpora sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Pokud před prvním nahráním virtuálního pevného disku do Azure používáte smyk, ujistěte se, že jste před spuštěním sady Sysprep [připravili virtuální počítač.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
> 
> 

1. Přihlaste se k virtuálnímu počítači s Windows.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na **%windir%\system32\sysprep** `sysprep.exe`a spusťte program .
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte **Zobrazit prostředí prvního spuštění počítače** a ujistěte se, že je zaškrtnuté políčko **Generalizovat**.
4. V **části Možnosti vypnutí**vyberte příkaz **Vypnout**.
5. Klikněte na tlačítko **OK**.
   
    ![Spuštění sysprepu](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po dokončení nástroj Sysprep vypne virtuální počítač. 

> [!IMPORTANT]
> Nerestartujte virtuální počítač, dokud neskončíte s nahráváním virtuálního pevného disku do Azure nebo vytvořením image z virtuálního počítače. Pokud virtuální ho neúmyslně restartuje, spusťte sysprep generalizovat znovu.
> 
> 


## <a name="upload-the-vhd"></a>Nahrání virtuálního pevného disku

Nahrajte virtuální pevný disk do účtu úložiště Azure.

### <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Pokud ještě nemáte nainstalovanou powershellovou verzi 1.4 nebo vyšší, přečtěte [si článek Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

1. Otevřete Azure PowerShell a přihlaste se ke svému účtu Azure. Otevře se vyskakovací okno, kde můžete zadat přihlašovací údaje k účtu Azure.
   
    ```powershell
    Connect-AzAccount
    ```
2. Získejte ID předplatného pro vaše dostupná předplatná.
   
    ```powershell
    Get-AzSubscription
    ```
3. Nastavte správné předplatné pomocí ID předplatného. Nahraďte `<subscriptionID>` id správného předplatného.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Získání účtu úložiště
K uložení nahrané image virtuálního počítače potřebujete účet úložiště v Azure. Můžete buď použít existující účet úložiště, nebo vytvořit nový. 

Chcete-li zobrazit dostupné účty úložiště, zadejte:

```powershell
Get-AzStorageAccount
```

Pokud chcete použít existující účet úložiště, přejděte k části Nahrát obrázek virtuálního zařízení.

Pokud potřebujete vytvořit účet úložiště, postupujte takto:

1. Potřebujete název skupiny prostředků, kde by měl být vytvořen účet úložiště. Chcete-li zjistit všechny skupiny prostředků, které jsou ve vašem předplatném, zadejte:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Chcete-li vytvořit skupinu prostředků s názvem **myResourceGroup** v oblasti **Západní USA,** zadejte:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Vytvořte účet úložiště s názvem **mystorageaccount** v této skupině prostředků pomocí rutiny [New-AzStorageAccount:](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Zahájení nahrávání 

Pomocí rutiny [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) nahrajte obrázek do kontejneru ve vašem účtu úložiště. Tento příklad odešle soubor **myVHD.vhd** z `"C:\Users\Public\Documents\Virtual hard disks\"` účtu úložiště s názvem **mystorageaccount** ve skupině prostředků **myResourceGroup.** Soubor bude umístěn do kontejneru s názvem **mycontainer** a nový název souboru bude **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Pokud je úspěšná, dostanete odpověď, která vypadá podobně jako toto:

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

V závislosti na síťovém připojení a velikosti souboru VHD může dokončení tohoto příkazu chvíli trvat.


## <a name="create-a-new-vm"></a>Vytvořte nový virtuální počítač. 

Teď můžete použít nahrané virtuální pevné disky k vytvoření nového virtuálního virtuálního mísu. 

### <a name="set-the-uri-of-the-vhd"></a>Nastavení identifikátoru URI virtuálního pevného disku

Identifikátor URI pro virtuální pevný disk je ve formátu: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. V tomto příkladu je virtuální pevný disk s názvem **myVHD** v účtu úložiště **mystorageaccount** v kontejneru **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť a podsíť [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

1. Vytvořte podsíť. Následující ukázka vytvoří podsíť s názvem **mySubnet** ve skupině prostředků **myResourceGroup** s předponou adresy **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Vytvořte virtuální síť. Následující ukázka vytvoří virtuální síť s názvem **myVnet** v umístění **v USA v USA v USA** s předponou adresy **10.0.0.0/16**.  
   
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
2. Vytvořte nic. Tento příklad vytvoří nic s názvem **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvoření skupiny zabezpečení sítě a pravidla protokolu RDP
Chcete-li mít možnost přihlásit se k virtuálnímu počítači pomocí protokolu RDP, musíte mít pravidlo zabezpečení, které umožňuje přístup k protokolu RDP na portu 3389. 

Tento příklad vytvoří skupinu nsg s názvem **myNsg,** která obsahuje pravidlo s názvem **myRdpRule,** které umožňuje přenos RDP přes port 3389. Další informace o sítích zabezpečení zabezpečení zabezpečení najdete [v tématu otevírání portů k virtuálnímu počítači v Azure pomocí PowerShellu](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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
Následující skript prostředí PowerShell ukazuje, jak nastavit konfigurace virtuálního počítače a použít nahranou bitovou kopii virtuálního počítače jako zdroj pro novou instalaci.



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

## <a name="verify-that-the-vm-was-created"></a>Ověření, že byl virtuální virtuální hotel vytvořen
Po dokončení byste měli vidět nově vytvořený virtuální počítač na [webu Azure portal](https://portal.azure.com) v části **Procházet** > **virtuální počítače**nebo pomocí následujících příkazů Prostředí PowerShell:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Další kroky
Pokud chcete spravovat nový virtuální počítač pomocí Azure PowerShellu, [přečtěte si, že najdete v tématu Správa virtuálních počítačů pomocí Azure Resource Manageru a PowerShellu](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


