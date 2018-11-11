---
title: Vytvoření nespravované image zobecněného virtuálního počítače v Azure | Dokumentace Microsoftu
description: Vytvořte unmanged image generalizovaného virtuálního počítače s Windows pomocí vytvářet více kopií virtuálního počítače v Azure.
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
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 63fdf9cf24c7e412533f15ff0701bc8fb481602a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240609"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Vytvoření nespravované image virtuálního počítače z virtuálního počítače Azure

Tento článek se týká použití účtů úložiště. Doporučujeme použít spravované disky a Image spravované místo účtu úložiště. Další informace najdete v tématu [zachycení spravované image zobecněného virtuálního počítače v Azure](capture-image-resource.md).

Tento článek popisuje, jak používat Azure PowerShell k vytvoření image generalizovaného virtuálního počítače Azure pomocí účtu úložiště. Potom můžete image k vytvoření virtuálního počítače s jinou. Na obrázku obsahuje disk s operačním systémem a datové disky, které jsou připojené k virtuálnímu počítači. Na obrázku neobsahuje prostředky virtuální sítě, proto musíte nastavit tyto prostředky při vytváření nového virtuálního počítače. 

## <a name="prerequisites"></a>Požadavky
Musíte mít verzi Azure Powershellu 1.0.x nebo novější nainstalován. Pokud jste ještě nenainstalovali Powershellu, přečtěte si [instalace a konfigurace Azure Powershellu](/powershell/azure/overview) kroky instalace.

## <a name="generalize-the-vm"></a>Generalizace virtuálního počítače 
Tato část ukazuje, jak zobecnit pro použití jako image virtuálního počítače Windows. Zobecňuje se virtuální počítač odebere všechny osobní informace o vašem účtu, mimo jiné a připraví počítač, který má být použit jako obrázek. Další informace o nástroji Sysprep najdete v článku věnovaném [úvodu do použití nástroje Sysprep](https://technet.microsoft.com/library/bb457073.aspx).

Ujistěte se, že na počítači spuštěná role serveru jsou podporované pomocí nástroje Sysprep. Další informace najdete v tématu [podpory nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Pokud nahráváte vašeho virtuálního pevného disku do Azure poprvé, ujistěte se, že máte [připravit virtuální počítač](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) před spuštěním nástroje Sysprep. 
> 
> 

Můžete také zobecnit virtuální počítač s Linuxem pomocí `sudo waagent -deprovision+user` a následné použití Powershellu k zachycení virtuálního počítače. Informace o zachycení virtuálního počítače pomocí rozhraní příkazového řádku najdete v tématu [generalize a zachytit virtuální počítač s Linuxem pomocí Azure CLI ](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Přihlášení do prostředí Azure PowerShell
1. Otevřete prostředí Azure PowerShell a přihlaste se ke svému účtu Azure.
   
    ```powershell
    Connect-AzureRmAccount
    ```
   
    K zadání přihlašovacích údajů k účtu Azure se otevře automaticky otevírané okno.
2. Získejte ID předplatného pro předplatné k dispozici.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Nastavit správné předplatné pomocí ID předplatného.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Uvolněte virtuální počítač a nastavit stav na generalizovaný

> [!IMPORTANT] 
> Nelze přidat, upravit nebo odebírat značky z virtuálního počítače, když ho označí jako zobecněný. Pokud chcete přidat značku k virtuálnímu počítači, ujistěte se, že přidáte značky před jeho označení za generalizovaný.
> 

1. Uvolnit prostředky virtuálních počítačů.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *Stav* pro virtuální počítač v Azure portal se změní z **Zastaveno** k **zastaveno (přidělení zrušeno)**.
2. Nastavte stav virtuálního počítače na **zobecněno**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Zkontrolujte stav virtuálního počítače. **OSState/zobecněný** části pro virtuální počítač by měl mít **DisplayStatus** nastavena na **virtuální počítač generalizovaný**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Vytvoření image

Vytvoření nespravovaného virtuálního počítače image v cílový kontejner úložiště pomocí tohoto příkazu. Image se vytvoří ve stejném účtu úložiště jako původní virtuální počítač. `-Path` Parametr uloží kopii šablony JSON pro zdrojový virtuální počítač do místního počítače. `-DestinationContainerName` Parametr je název, který chcete pro uložení Image kontejneru. Pokud kontejner neexistuje, vytvoří se pro vás.
   
```powershell
Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Můžete získat adresu URL image ze souboru šablony JSON. Přejděte **prostředky** > **storageProfile** > **osDisk** > **image**  >  **uri** část vaší image pro úplnou cestu. Adresa URL obrázku vypadá jako: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Můžete si taky ověřit identifikátor URI na portálu. Na obrázku je zkopírován do kontejneru s názvem **systému** ve vašem účtu úložiště. 

## <a name="create-a-vm-from-the-image"></a>Vytvoření virtuálního počítače z image

Nyní můžete vytvořit jeden nebo více virtuálních počítačů z nespravované image.

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
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Vytvořte virtuální síť. Následující příklad vytvoří virtuální síť s názvem **myVnet** v **USA – západ** umístění s předponou adresy **10.0.0.0/16**.  
   
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
2. Vytvořte síťové rozhraní Tento příklad vytvoří síťové rozhraní s názvem **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvořte skupinu zabezpečení sítě a pravidlo protokolu RDP
Abyste mohli pro přihlášení k virtuálnímu počítači pomocí protokolu RDP, budete muset mít pravidlo zabezpečení, které povoluje přístup protokolu RDP na portu 3389. 

Tento příklad vytvoří skupinu zabezpečení sítě s názvem **myNsg** , který obsahuje pravidlo s názvem **myRdpRule** , která umožní provoz protokolu RDP přes port 3389. Další informace o skupinách Nsg najdete v tématu [otevření portů k virtuálnímu počítači v Azure pomocí Powershellu](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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
Vytvořte proměnnou pro dokončené virtuální sítě. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Vytvořte virtuální počítač.
Následující příkaz Powershellu dokončení konfigurace virtuálních počítačů a nespravované image použije jako zdroj pro novou instalaci.

</br>

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

### <a name="verify-that-the-vm-was-created"></a>Ověřte, že se vytvořil virtuální počítač
Jakmile budete hotovi, zobrazí se nově vytvořenému virtuálnímu počítači v [webu Azure portal](https://portal.azure.com) pod **Procházet** > **virtuálních počítačů**, nebo použijte následující příkaz Powershellu příkazy:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Další postup
Ke správě vašeho nového virtuálního počítače pomocí Azure Powershellu, najdete v článku [Správa virtuálních počítačů pomocí Azure Resource Manageru a Powershellu](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


