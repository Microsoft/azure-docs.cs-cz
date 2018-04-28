---
title: Vytvoření spravovaných virtuálních počítačů Azure z virtuální pevný disk zobecněný místní | Microsoft Docs
description: Nahrajte zobecněný virtuální pevný disk do Azure a použít ho k vytvoření nové virtuální počítače, v modelu nasazení Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: cynthn
ms.openlocfilehash: 133c09ad1f81a122d089485f8793d7dddbd1488a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Nahrát zobecněný virtuální pevný disk a použít ho k vytvoření nové virtuální počítače v Azure

Toto téma vás provede pomocí prostředí PowerShell nahrajte virtuální pevný disk zobecněný virtuální počítač do Azure, vytvořte bitovou kopii z disku VHD a vytvoření nového virtuálního počítače z této bitové kopie. Můžete nahrát virtuální pevný disk exportovat z nástroj virtualizace na místní nebo z jiného cloudu. Pomocí [spravované disky](managed-disks-overview.md) pro nový virtuální počítač zjednodušuje správu virtuálních počítačů a poskytuje lepší dostupnost při umístění virtuálního počítače do skupiny dostupnosti. 

Pokud chcete použít ukázkový skript, přečtěte si téma [ukázkový skript nahrání virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="before-you-begin"></a>Než začnete

- Před nahráním jakéhokoli virtuálního pevného disku do Azure, postupujte podle [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Zkontrolujte [plánování migrace na spravované disky](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) před zahájením migrace na [spravované disky](managed-disks-overview.md).
- Tento článek vyžaduje AzureRM verze modulu 5.6 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM.Compute`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="generalize-the-source-vm-using-sysprep"></a>Generalize zdrojového virtuálního počítače pomocí nástroje Sysprep

Nástroj Sysprep odstraní všechny vaše osobní informace o účtu, mimo jiné a připraví počítač, který se má použít jako obrázek. Podrobnosti o nástroji Sysprep najdete v tématu [postup použití nástroje Sysprep: Úvod](http://technet.microsoft.com/library/bb457073.aspx).

Ujistěte se, že role serveru spuštěná na tomto počítači jsou podporovány nástrojem Sysprep. Další informace najdete v tématu [podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Pokud používáte nástroj Sysprep před nahráním svůj disk VHD do Azure poprvé, ujistěte se, máte [připravit virtuální počítač](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) před spuštěním nástroje Sysprep. 
> 
> 

1. Přihlaste se k virtuálnímu počítači Windows.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na **%windir%\system32\sysprep**a poté spusťte `sysprep.exe`.
3. V **nástroj pro přípravu systému** dialogové okno, vyberte **prostředí Out-of-Box zadejte systému (při prvním zapnutí)** a ujistěte se, že **generalizace** je zaškrtnuté políčko.
4. V **možnosti vypnutí**, vyberte **vypnutí**.
5. Klikněte na **OK**.
   
    ![Spusťte nástroj Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po dokončení nástroj Sysprep vypne virtuální počítač. Virtuální počítač nerestartuje.


## <a name="get-the-storage-account"></a>Získat účet úložiště

Potřebujete účet úložiště v Azure k ukládání nahrané image virtuálního počítače. Můžete použít existující účet úložiště, nebo vytvořte novou. 

Pokud budete používat virtuální pevný disk pro vytvoření spravovaného disku pro virtuální počítač, umístění účtu úložiště musí být stejné umístění, kde bude vytvoření virtuálního počítače.

Chcete-li zobrazit účty úložiště k dispozici, zadejte:

```azurepowershell
Get-AzureRmStorageAccount | Format-Table
```

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

### <a name="other-options-for-uploading-a-vhd"></a>Další možnosti pro odesílání virtuálního pevného disku
 
Můžete také nahrát virtuální pevný disk na váš účet úložiště pomocí jedné z následujících akcí:

- [AzCopy](http://aka.ms/downloadazcopy)
- [Objekt Blob služby Azure Storage kopie rozhraní API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Objektů BLOB Azure Storage Explorer odesílání](https://azurestorageexplorer.codeplex.com/)
- [Referenční dokumentace rozhraní API úložiště importu/exportu služby REST](https://msdn.microsoft.com/library/dn529096.aspx)
-   Doporučujeme používat službu Import/Export, pokud je předpokládaná doba odesílání doba je delší než 7 dní. Můžete použít [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) k zjištění přibližné hodnoty času z jednotky velikost a přenášet data. 
    Import a Export lze použít pro kopírování na standardní účet úložiště. Musíte zkopírovat z úložiště standard storage do prémiový účet úložiště pomocí nástroje, například AzCopy.

> [!IMPORTANT]
> Pokud používáte AzCopy odesílání svůj disk VHD do Azure, ujistěte se, jste nastavili [/BlobType:page](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) dřív, než spustíte odeslat skript. Pokud cílový objekt blob a není tato možnost zadána, ve výchozím nastavení, AzCopy vytvoří objekt blob bloku.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Vytvoření bitové kopie spravované z nahraný virtuální pevný disk 

Vytvoření spravované image pomocí vaší zobecněný virtuální pevný disk operačního systému. Nahraďte hodnoty svými vlastními informacemi.


Nastavte nejprve, některé parametry:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Vytvořte bitovou kopii pomocí vaší zobecněný virtuální pevný disk operačního systému.

```powershell
$imageConfig = New-AzureRmImageConfig `
   -Location $location
$imageConfig = Set-AzureRmImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzureRmImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Teď, když máte image, můžete vytvořit jeden nebo více nové virtuální počítače z image. Tento příklad vytvoří virtuální počítač s názvem *Můjvp* z *myImage*v *myResourceGroup*.


```powershell
New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Další postup

K přihlášení do nového virtuálního počítače, přejděte do virtuálního počítače v [portál](https://portal.azure.com), klikněte na tlačítko **Connect**a otevřete soubor Remote Desktop RDP. Pomocí přihlašovacích údajů účtu původní virtuálního počítače pro přihlášení do nového virtuálního počítače. Další informace najdete v tématu [jak se připojit a přihlásit se na virtuálním počítači Azure s Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

