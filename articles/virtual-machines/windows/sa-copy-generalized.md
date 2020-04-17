---
title: Vytvoření nespravované image generalizovaného virtuálního počítače v Azure
description: Vytvořte nemanged image generalizovaného virtuálního počítače s Windows použít k vytvoření více kopií virtuálního počítače v Azure.
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
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 1f44f30666ead84b2bd2fc63d8a8eb624f70c85c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458075"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Jak vytvořit nespravovanou image virtuálního počítače z virtuálního počítače Azure

Tento článek popisuje použití účtů úložiště. Doporučujeme používat spravované disky a spravované bitové kopie místo účtu úložiště. Další informace najdete [v tématu Zachycení spravované bitové kopie generalizovaného virtuálního počítače v Azure](capture-image-resource.md).

Tento článek ukazuje, jak pomocí Azure PowerShellu vytvořit image generalizovaného virtuálního počítače Azure pomocí účtu úložiště. Potom můžete použít image k vytvoření jiného virtuálního uživatele. Bitová kopie obsahuje disk operačního systému a datové disky, které jsou připojeny k virtuálnímu počítači. Image neobsahuje prostředky virtuální sítě, takže je potřeba nastavit tyto prostředky při vytváření nového virtuálního počítače. 

 

## <a name="generalize-the-vm"></a>Zobecnění virtuálního virtuálního montovana 
V této části se zobrazí, jak zobecnit virtuální počítač se systémem Windows pro použití jako bitová kopie. Generalizace virtuálního počítače odstraní mimo jiné všechny informace o vašem osobním účtu a připraví zařízení, které se má použít jako obrázek. Další informace o nástroji Sysprep najdete v článku věnovaném [úvodu do použití nástroje Sysprep](https://technet.microsoft.com/library/bb457073.aspx).

Ujistěte se, že role serveru spuštěné v počítači jsou podporovány programem Sysprep. Další informace naleznete v [tématu Podpora sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Pokud nahráváte virtuální pevný disk do Azure poprvé, ujistěte se, že jste před spuštěním spřípravku Sysprep [připravili virtuální počítač.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
> 
> 

Můžete také zobecnit virtuální `sudo waagent -deprovision+user` počítač s Linuxem pomocí a pak použít PowerShell k zachycení virtuálního počítače. Informace o použití příkazového příkazového příkazu k zachycení virtuálního počítače najdete v [tématu Jak zobecnit a zachytit virtuální počítač Linux pomocí azure CLI](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Přihlášení do prostředí Azure PowerShell
1. Otevřete Azure PowerShell a přihlaste se ke svému účtu Azure.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Otevře se vyskakovací okno, kde můžete zadat přihlašovací údaje k účtu Azure.
2. Získejte ID předplatného pro vaše dostupná předplatná.
   
    ```powershell
    Get-AzSubscription
    ```
3. Nastavte správné předplatné pomocí ID předplatného.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Navrátit virtuální ho svícen a nastavit stav na generalizované

> [!IMPORTANT] 
> Značky z virtuálního aplikace nelze přidávat, upravovat ani odebírat, jakmile je označen jako zobecněný. Pokud chcete přidat značku do virtuálního virtuálního mě, nezapomeňte je přidat, než je označíte jako zobecněné.
> 

1. Navrátit prostředky virtuálního soudu.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *Stav* virtuálního počítače na portálu Azure se změní z **Zastaveno** **zastaveno (nabyté navrácené).**
2. Nastavte stav virtuálního počítače na **Generalized**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Zkontrolujte stav virtuálního virtuálního soudu. **Část OSState/generalizovaná** pro virtuální ho svícen by měla mít **displaystatus** nastavený na **zobecněný virtuální hod**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Vytvoření image

Pomocí tohoto příkazu vytvořte nespravovanou bitovou kopii virtuálního počítače v cílovém kontejneru úložiště. Bitová kopie se vytvoří ve stejném účtu úložiště jako původní virtuální počítač. Parametr `-Path` uloží kopii šablony JSON pro zdrojový virtuální počítač do místního počítače. Parametr `-DestinationContainerName` je název kontejneru, který chcete držet obrázky. Pokud kontejner neexistuje, je vytvořen pro vás.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Adresu URL obrázku můžete získat ze šablony souboru JSON. Přejděte do úložiště > **prostředkůProfil** > **osDisk** > **obraz** > **uri** části pro úplnou cestu k obrázku. **resources** Adresa URL obrázku vypadá `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`takto: .
   
Můžete také ověřit identifikátor URI na portálu. Bitová kopie se zkopíruje do kontejneru s názvem **systému** ve vašem účtu úložiště. 

## <a name="create-a-vm-from-the-image"></a>Vytvořte z této image virtuální počítač.

Teď můžete vytvořit jeden nebo více virtuálních virtuálních hrach z nespravované image.

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
Následující prostředí PowerShell dokončí konfigurace virtuálního počítače a použije nespravovanou bitovou kopii jako zdroj pro novou instalaci.

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

### <a name="verify-that-the-vm-was-created"></a>Ověření, že byl virtuální virtuální hotel vytvořen
Po dokončení byste měli vidět nově vytvořený virtuální počítač na [webu Azure portal](https://portal.azure.com) v části **Procházet** > **virtuální počítače**nebo pomocí následujících příkazů Prostředí PowerShell:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Další kroky
Pokud chcete spravovat nový virtuální počítač pomocí Azure PowerShellu, [přečtěte si, že najdete v tématu Správa virtuálních počítačů pomocí Azure Resource Manageru a PowerShellu](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


