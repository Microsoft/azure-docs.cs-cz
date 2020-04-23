---
title: Vytvoření nespravované image zobecněného virtuálního počítače v Azure
description: Vytvořte unmanged image zobecněného virtuálního počítače s Windows, který se použije k vytvoření několika kopií virtuálního počítače v Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.custom: storage-accounts
ms.openlocfilehash: 130764ad5504ded398a9fdf9fa27d6cb936fbacc
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82099780"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Vytvoření image nespravovaného virtuálního počítače z virtuálního počítače Azure

Tento článek se zabývá používáním účtů úložiště. Doporučujeme místo účtu úložiště používat spravované disky a spravované image. Další informace najdete v tématu [zachycení spravované image zobecněného virtuálního počítače v Azure](capture-image-resource.md).

V tomto článku se dozvíte, jak pomocí Azure PowerShell vytvořit image zobecněného virtuálního počítače Azure pomocí účtu úložiště. Bitovou kopii pak můžete použít k vytvoření dalšího virtuálního počítače. Image obsahuje disk s operačním systémem a datové disky, které jsou připojené k virtuálnímu počítači. Obrázek neobsahuje prostředky virtuální sítě, takže při vytváření nového virtuálního počítače je potřeba tyto prostředky nastavit. 

 

## <a name="generalize-the-vm"></a>Generalizace virtuálního počítače 
V této části se dozvíte, jak zobecnit virtuální počítač s Windows pro použití jako image. Generalizace virtuálního počítače odebere všechny informace o osobním účtu mimo jiné a připraví počítač, který se má použít jako image. Další informace o nástroji Sysprep najdete v článku věnovaném [úvodu do použití nástroje Sysprep](https://technet.microsoft.com/library/bb457073.aspx).

Ujistěte se, že nástroj Sysprep podporuje role serveru spuštěné v počítači. Další informace najdete v tématu [Podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) .

> [!IMPORTANT]
> Pokud virtuální pevný disk nahráváte poprvé do Azure, ujistěte se, že jste před spuštěním nástroje Sysprep [připravili virtuální počítač](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . 
> 
> 

Virtuální počítač se systémem Linux můžete také zobecnit pomocí `sudo waagent -deprovision+user` a potom pomocí prostředí PowerShell zachytit virtuální počítač. Informace o použití rozhraní příkazového řádku k zachycení virtuálního počítače najdete v tématu [postup generalizace a zachycení virtuálního počítače se systémem Linux pomocí rozhraní příkazového řádku Azure CLI](../linux/capture-image.md).


1. Přihlaste se k virtuálnímu počítači s Windows.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na **%WINDIR%\system32\sysprep**a potom spusťte příkaz `sysprep.exe`.
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte **Zobrazit prostředí prvního spuštění počítače** a ujistěte se, že je zaškrtnuté políčko **Generalizovat**.
4. V **Možnosti vypnutí**vyberte **vypnout**.
5. Klikněte na tlačítko **OK**.
   
    ![Spustit nástroj Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po dokončení nástroj Sysprep vypne virtuální počítač. 

> [!IMPORTANT]
> Nerestartujte virtuální počítač, dokud nedokončíte odeslání virtuálního pevného disku do Azure nebo vytvoření image z virtuálního počítače. Pokud se virtuální počítač náhodně restartuje, spusťte Sysprep a znovu ho generalizujte.
> 
> 

## <a name="log-in-to-azure-powershell"></a>Přihlášení do prostředí Azure PowerShell
1. Otevřete Azure PowerShell a přihlaste se ke svému účtu Azure.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Otevře se automaticky otevírané okno, ve kterém můžete zadat svoje přihlašovací údaje k účtu Azure.
2. Získejte ID předplatných pro vaše dostupná předplatná.
   
    ```powershell
    Get-AzSubscription
    ```
3. Nastavte správné předplatné pomocí ID předplatného.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Zrušení přidělení virtuálního počítače a nastavení stavu na generalizované

> [!IMPORTANT] 
> Až bude klíč označený jako zobecněný, nejde ho z virtuálního počítače přidat, upravit ani odebrat. Pokud chcete přidat značku k virtuálnímu počítači, nezapomeňte značky přidat předtím, než je označíte jako zobecněné.
> 

1. Zrušení přidělení prostředků virtuálního počítače
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *Stav* virtuálního počítače ve Azure Portal se změní ze **Zastaveno** na **Zastaveno (přidělení zrušeno)**.
2. Nastavte stav virtuálního počítače na **zobecněno**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Ověřte stav virtuálního počítače. Oddíl **OSState/generalize** pro virtuální počítač by měl mít zobecněnou **DisplayStatus** sadu **virtuálních počítačů**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Vytvoření image

Pomocí tohoto příkazu vytvořte image nespravovaného virtuálního počítače v cílovém kontejneru úložiště. Obrázek se vytvoří ve stejném účtu úložiště jako původní virtuální počítač. `-Path` Parametr uloží kopii šablony JSON pro zdrojový virtuální počítač do místního počítače. `-DestinationContainerName` Parametr je název kontejneru, do kterého chcete uložit obrázky. Pokud kontejner neexistuje, vytvoří se za vás.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Adresu URL obrázku můžete získat ze šablony souboru JSON. Pro úplnou cestu k imagi použijte oddíl **Resources** > **URI** **Image** > **storageProfile** > **osDisk** > . Adresa URL obrázku vypadá takto: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Identifikátor URI můžete také ověřit na portálu. Obrázek se zkopíruje do kontejneru s názvem **System** v účtu úložiště. 

## <a name="create-a-vm-from-the-image"></a>Vytvořte z této image virtuální počítač.

Nyní můžete vytvořit jeden nebo více virtuálních počítačů z nespravované image.

### <a name="set-the-uri-of-the-vhd"></a>Nastavte identifikátor URI virtuálního pevného disku.

Identifikátor URI pro virtuální pevný disk, který se má použít, je ve formátu: https://**mystorageaccount**. blob.Core.Windows.NET/**myContainer**/**MyVhdName**. VHD. V tomto příkladu je virtuální pevný disk s názvem **myVHD** v účtu úložiště **mystorageaccount** v kontejneru **myContainer**.

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
    $location = "West US"
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
Následující prostředí PowerShell dokončí konfigurace virtuálních počítačů a jako zdroj nové instalace používá nespravovanou bitovou kopii.

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

### <a name="verify-that-the-vm-was-created"></a>Ověřte, že byl virtuální počítač vytvořen.
Po dokončení by se měl nově vytvořený virtuální počítač zobrazit v [Azure Portal](https://portal.azure.com) v části **Procházet** > **virtuální počítače**nebo pomocí následujících příkazů PowerShellu:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Další kroky
Pokud chcete spravovat nový virtuální počítač pomocí Azure PowerShell, přečtěte si téma [Správa virtuálních počítačů pomocí Azure Resource Manager a PowerShellu](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


