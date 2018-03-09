---
title: "Vytvoření spravovaných virtuálních počítačů Azure z virtuální pevný disk zobecněný místní | Microsoft Docs"
description: "Nahrajte zobecněný virtuální pevný disk do Azure a použít ho k vytvoření nové virtuální počítače, v modelu nasazení Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: cynthn
ms.openlocfilehash: 2e78ecf6bd281bd5d30f59413789eb1e6fc7b5bc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Nahrát zobecněný virtuální pevný disk a použít ho k vytvoření nové virtuální počítače v Azure

Toto téma vás provede pomocí prostředí PowerShell nahrajte virtuální pevný disk zobecněný virtuální počítač do Azure, vytvořte bitovou kopii z disku VHD a vytvoření nového virtuálního počítače z této bitové kopie. Můžete nahrát virtuální pevný disk exportovat z nástroj virtualizace na místní nebo z jiného cloudu. Pomocí [spravované disky](managed-disks-overview.md) pro nový virtuální počítač zjednodušuje správu virtuálních počítačů a poskytuje lepší dostupnost při umístění virtuálního počítače do skupiny dostupnosti. 

Pokud chcete použít ukázkový skript, přečtěte si téma [ukázkový skript nahrání virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="before-you-begin"></a>Než začnete

- Před nahráním jakéhokoli virtuálního pevného disku do Azure, postupujte podle [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Zkontrolujte [plánování migrace na spravované disky](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) před zahájením migrace na [spravované disky](managed-disks-overview.md).
- Ujistěte se, že máte nejnovější verzi modulu AzureRM.Compute prostředí PowerShell. Spusťte následující příkaz k její instalaci.

    ```powershell
    Install-Module AzureRM.Compute -RequiredVersion 2.6.0
    ```
    Další informace najdete v tématu [Azure PowerShell verze](/powershell/azure/overview).


## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalize virtuální počítač s Windows pomocí nástroje Sysprep

Nástroj Sysprep odstraní všechny vaše osobní informace o účtu, mimo jiné a připraví počítač, který se má použít jako obrázek. Podrobnosti o nástroji Sysprep najdete v tématu [postup použití nástroje Sysprep: Úvod](http://technet.microsoft.com/library/bb457073.aspx).

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
6. Po dokončení nástroj Sysprep vypne virtuální počítač. Virtuální počítač nerestartuje.



## <a name="log-in-to-azure"></a>Přihlášení k Azure
Pokud ještě nemáte prostředí PowerShell verze 1.4 nebo vyšší nainstalovaná, přečtěte si [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

1. Otevřete prostředí Azure PowerShell a přihlaste se k účtu Azure. Automaticky otevírané okno otevře zadat přihlašovací údaje účtu Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Pořiďte si předplatné ID pro dostupných předplatných.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Nastavit správné předplatné pomocí ID předplatného. Nahraďte  *<subscriptionID>*  s ID správné předplatné.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Získat účet úložiště
Potřebujete účet úložiště v Azure k ukládání nahrané image virtuálního počítače. Můžete použít existující účet úložiště, nebo vytvořte novou. 

Pokud budete používat virtuální pevný disk pro vytvoření spravovaného disku pro virtuální počítač, umístění účtu úložiště musí být stejné umístění, kde bude vytvoření virtuálního počítače.

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

    Chcete-li vytvořit skupinu prostředků s názvem **myResourceGroup** v **východní USA** oblast, zadejte:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "East US"
    ```

2. Vytvořit účet úložiště s názvem **můj_účet_úložiště** v této skupině prostředků s použitím [AzureRmStorageAccount nový](/powershell/module/azurerm.storage/new-azurermstorageaccount) rutiny:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "East US"`
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    Platné hodnoty - SkuName jsou:
   
   * **Standard_LRS** -místně redundantní úložiště. 
   * **Standard_ZRS** -zónu redundantní úložiště.
   * **Standard_GRS** -geograficky redundantní úložiště. 
   * **Standard_RAGRS** -geograficky redundantní úložiště s přístupem pro čtení. 
   * **Premium_LRS** -místně redundantního úložiště Premium. 

## <a name="upload-the-vhd-to-your-storage-account"></a>Nahrání virtuálního pevného disku do účtu úložiště

Použití [přidat AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) rutiny odeslání disku VHD do kontejneru v účtu úložiště. Tento příklad nahrávání souboru *myVHD.vhd* z *"C:\Users\Public\Documents\Virtual pevné disky\"*  na účet úložiště s názvem *můj_účet_úložiště* v *myResourceGroup* skupinu prostředků. Soubor se umístí do kontejner s názvem *můj_kontejner* a nový název souboru bude *myUploadedVHD.vhd*.

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

V závislosti na připojení k síti a velikost souboru virtuálního pevného disku tento příkaz může trvat nějakou dobu pro dokončení

Uložit **identifikátor URI pro cíl** cestu pro pozdější použití, pokud se chystáte vytvořit spravovaných disků nebo nového virtuálního počítače pomocí nahraný virtuální pevný disk.

### <a name="other-options-for-uploading-a-vhd"></a>Další možnosti pro odesílání virtuálního pevného disku
 
 
Můžete také nahrát virtuální pevný disk na váš účet úložiště pomocí jedné z následujících akcí:

- [AzCopy](http://aka.ms/downloadazcopy)
- [Objekt Blob služby Azure Storage kopie rozhraní API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Objektů BLOB Azure Storage Explorer odesílání](https://azurestorageexplorer.codeplex.com/)
- [Referenční dokumentace rozhraní API úložiště importu/exportu služby REST](https://msdn.microsoft.com/library/dn529096.aspx)
-   Doporučujeme používat službu Import/Export, pokud je předpokládaná doba odesílání doba je delší než 7 dní. Můžete použít [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) k zjištění přibližné hodnoty času z jednotky velikost a přenášet data. 
    Import a Export lze použít pro kopírování na standardní účet úložiště. Musíte zkopírovat z úložiště standard storage do prémiový účet úložiště pomocí nástroje, například AzCopy.

> [!IMPORTANT]
> Pokud používáte AzCopy odesílání svůj disk VHD do Azure, ujistěte se, jste nastavili [/BlobType:page](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) dřív, než spustíte odeslat skript. Pokud cílový objekt blob a není tato možnost zadána, ve výchozím nastavení, AzCopy vytvoří objekt blob bloku.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Vytvoření bitové kopie spravované z nahraný virtuální pevný disk 

Vytvoření spravované image pomocí vaší zobecněný virtuální pevný disk operačního systému. Nahraďte hodnoty svými vlastními informacemi.


1.  Nastavte nejprve, běžné parametry:

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    $vmSize = "Standard_DS1_v2"
    $location = "East US" 
    $imageName = "yourImageName"
    ```

4.  Vytvořte bitovou kopii pomocí vaší zobecněný virtuální pevný disk operačního systému.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $urlOfUploadedImageVhd
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořit virtuální síť a podsíť [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

1. Vytvořte podsíť. Tento příklad vytvoří podsíť s názvem *mySubnet* s předponou adresy z *10.0.0.0/24*.  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Vytvořte virtuální síť. Tento příklad vytvoří virtuální síť s názvem *myVnet* s předponou adresy z *10.0.0.0/16*.  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Vytvoření veřejné IP adresy a síťového rozhraní

Pokud chcete povolit komunikaci s virtuálním počítačem ve virtuální síti, budete potřebovat [veřejnou adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) a síťové rozhraní.

1. Vytvoření veřejné IP adresy. Tento příklad vytvoří veřejnou IP adresu s názvem *myPip*. 
   
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

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvořte skupinu zabezpečení sítě a pravidlo protokolu RDP

Abyste mohli přihlásit k virtuálnímu počítači pomocí protokolu RDP, musíte mít pravidla zabezpečení sítě (NSG), který umožňuje přístup RDP na portu 3389. 

Tento příklad vytvoří skupinu NSG s názvem *myNsg* obsahující pravidlo názvem *myRdpRule* přes port 3389 umožňuje provoz protokolu RDP. Další informace o skupinách Nsg najdete v tématu [otevřít porty pro virtuální počítač v Azure pomocí prostředí PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Vytvořte proměnnou pro virtuální síť

Vytvořte proměnnou pro dokončené virtuální síť. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>Získání přihlašovacích údajů pro virtuální počítač.

Následující rutiny otevře okno, kde bude zadejte nové uživatelské jméno a heslo pro použití jako účet místního správce pro vzdálený přístup k virtuálnímu počítači. 

```powershell
$cred = Get-Credential
```

## <a name="add-the-vm-name-and-size-to-the-vm-configuration"></a>Přidejte název virtuálního počítače a velikost ke konfiguraci virtuálních počítačů.

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Nastavit image virtuálního počítače jako zdroj bitové kopie pro nový virtuální počítač

Nastavte zdrojové bitové kopie pomocí ID spravované image virtuálního počítače.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>Nastavení konfigurace operačního systému a přidejte na síťový adaptér.

Zadejte typ úložiště (PremiumLRS nebo StandardLRS) a velikost disku operačního systému. Tento příklad nastaví typ účtu *PremiumLRS*, velikost disku na *128 GB* a ukládání do mezipaměti na disku ke *ReadWrite*.

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvoření nového virtuálního počítače pomocí konfigurace uložené v **$vm** proměnné.

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>Zkontrolujte, zda byl vytvořen virtuální počítač
Po dokončení byste měli vidět nově vytvořený virtuální počítač v [portál Azure](https://portal.azure.com) pod **Procházet** > **virtuální počítače**, nebo pomocí následujících příkazů prostředí PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Další postup

K přihlášení do nového virtuálního počítače, přejděte do virtuálního počítače v [portál](https://portal.azure.com), klikněte na tlačítko **Connect**a otevřete soubor Remote Desktop RDP. Pomocí přihlašovacích údajů účtu původní virtuálního počítače pro přihlášení do nového virtuálního počítače. Další informace najdete v tématu [jak se připojit a přihlásit se na virtuálním počítači Azure s Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

