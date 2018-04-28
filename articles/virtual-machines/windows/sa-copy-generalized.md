---
title: Vytvoření image nespravované zobecněný virtuálního počítače v Azure | Microsoft Docs
description: Vytvoření image unmanged zobecněný virtuální počítač s Windows pomocí vytvářet více kopií virtuálního počítače v Azure.
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
ms.openlocfilehash: 3737ea08e593ae1018489633e23e80e1099296ae
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Postup vytvoření nespravované image virtuálního počítače z virtuálního počítače Azure

Tento článek popisuje používání účtů úložiště. Doporučujeme místo účtu úložiště používat spravované disky a spravované bitové kopie. Další informace najdete v tématu [zaznamenat bitovou kopii spravované zobecněný virtuálního počítače v Azure](capture-image-resource.md).

Tento článek ukazuje, jak pomocí prostředí Azure PowerShell k vytvoření bitové kopie zobecněný virtuální počítač Azure pomocí účtu úložiště. Potom můžete image vytvořit jiným virtuálním Počítačem. Obrázek obsahuje disk operačního systému a datové disky, které jsou připojené k virtuálnímu počítači. Bitovou kopii neobsahuje virtuální síťové prostředky, takže budete muset nastavit tyto prostředky při vytváření nového virtuálního počítače. 

## <a name="prerequisites"></a>Požadavky
Je potřeba mít prostředí Azure PowerShell verze 1.0.x nebo novější. Pokud jste ještě nenainstalovali prostředí PowerShell, přečtěte si [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) kroky instalace.

## <a name="generalize-the-vm"></a>Generalize virtuálního počítače 
V této části se dozvíte, jak ke generalizaci virtuálního počítače Windows pro použití jako obrázek. Generalizací virtuálního počítače odebere všechny osobní informace o účtu, mimo jiné a připraví počítač, který se má použít jako obrázek. Podrobnosti o nástroji Sysprep najdete v tématu [postup použití nástroje Sysprep: Úvod](http://technet.microsoft.com/library/bb457073.aspx).

Ujistěte se, že role serveru spuštěná na tomto počítači jsou podporovány nástrojem Sysprep. Další informace najdete v tématu [podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Pokud nahráváte svůj disk VHD do Azure poprvé, zajistěte, aby byla [připravit virtuální počítač](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) před spuštěním nástroje Sysprep. 
> 
> 

Můžete také generalize virtuálního počítače s Linuxem pomocí `sudo waagent -deprovision+user` a potom pomocí prostředí PowerShell pro zachycení virtuálního počítače. Informace o zachycení virtuálního počítače pomocí rozhraní příkazového řádku najdete v tématu [generalize a zachycení virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure ](../linux/capture-image.md).


1. Přihlaste se k virtuálnímu počítači Windows.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na **%windir%\system32\sysprep**a poté spusťte `sysprep.exe`.
3. V **nástroj pro přípravu systému** dialogové okno, vyberte **prostředí Out-of-Box zadejte systému (při prvním zapnutí)** a ujistěte se, že **generalizace** je zaškrtnuté políčko.
4. V **možnosti vypnutí**, vyberte **vypnutí**.
5. Klikněte na **OK**.
   
    ![Spusťte nástroj Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po dokončení nástroj Sysprep vypne virtuální počítač. 

> [!IMPORTANT]
> Virtuální počítač nerestartuje, až do dokončení odesílání virtuálního pevného disku do Azure nebo vytvoření bitové kopie z virtuálního počítače. Pokud omylem získá restartování virtuálního počítače, spusťte nástroj Sysprep ke generalizaci ho znovu.
> 
> 

## <a name="log-in-to-azure-powershell"></a>Přihlášení do prostředí Azure PowerShell
1. Otevřete prostředí Azure PowerShell a přihlaste se k účtu Azure.
   
    ```powershell
    Connect-AzureRmAccount
    ```
   
    Automaticky otevírané okno otevře zadat přihlašovací údaje účtu Azure.
2. Pořiďte si předplatné ID pro dostupných předplatných.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Nastavit správné předplatné pomocí ID předplatného.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Zrušit přidělení virtuálního počítače a nastavit stav, který má zobecněné
1. Deallocate prostředky virtuálních počítačů.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *Stav* pro virtuální počítač ve službě Azure portal změní z **Zastaveno** k **zastaveném (nepřiřazeném)**.
2. Nastavte stav virtuálního počítače na **zobecněno**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Zkontrolujte stav virtuálního počítače. **OSState/zobecněn** části pro virtuální počítač by měl mít **DisplayStatus** nastavena na **zobecněný virtuální počítač**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Vytvoření image

Vytvoření image nespravované virtuální počítač v cílový kontejner úložiště použití tohoto příkazu. Obrázek se vytvoří ve stejném účtu úložiště jako původní virtuální počítač. `-Path` Parametr uloží kopii šablona JSON pro zdrojový virtuální počítač do místního počítače. `-DestinationContainerName` Parametr je název kontejneru, který má být blokování obrázků. Pokud kontejner neexistuje, vytvoří se pro vás.
   
```powershell
Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Můžete získat adresu URL bitové kopie ze souboru šablony JSON. Přejděte na **prostředky** > **storageProfile** > **osDisk** > **image**  >  **uri** části pro kompletní cesta bitové kopie. Adresa URL obrázku, vypadá takto: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Můžete si taky ověřit identifikátor URI na portálu. Obrázek se zkopíruje na kontejner s názvem **systému** ve vašem účtu úložiště. 

## <a name="create-a-vm-from-the-image"></a>Vytvořte virtuální počítač z bitové kopie

Nyní můžete vytvořit jeden nebo více virtuálních počítačů z nespravovaných bitové kopie.

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
Následující PowerShell dokončí konfigurací virtuálních počítačů a nespravované image se používá jako zdroj pro novou instalaci.

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

### <a name="verify-that-the-vm-was-created"></a>Zkontrolujte, zda byl vytvořen virtuální počítač
Po dokončení byste měli vidět nově vytvořený virtuální počítač v [portál Azure](https://portal.azure.com) pod **Procházet** > **virtuální počítače**, nebo pomocí následujících příkazů prostředí PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Další postup
Ke správě nového virtuálního počítače v prostředí Azure PowerShell najdete [Správa virtuálních počítačů pomocí Azure Resource Manageru a prostředí PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


