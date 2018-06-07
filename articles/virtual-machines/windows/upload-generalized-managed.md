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
ms.openlocfilehash: 9ebe1f67c7c662af6d9e1888580149834a007200
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657467"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Nahrát zobecněný virtuální pevný disk a použít ho k vytvoření nové virtuální počítače v Azure

Toto téma vás provede pomocí prostředí PowerShell nahrajte virtuální pevný disk zobecněný virtuální počítač do Azure, vytvořte bitovou kopii z disku VHD a vytvoření nového virtuálního počítače z této bitové kopie. Můžete nahrát virtuální pevný disk exportovat z nástroj virtualizace na místní nebo z jiného cloudu. Pomocí [spravované disky](managed-disks-overview.md) pro nový virtuální počítač zjednodušuje správu virtuálních počítačů a poskytuje lepší dostupnost při umístění virtuálního počítače do skupiny dostupnosti. 

Pokud chcete použít ukázkový skript, přečtěte si téma [ukázkový skript nahrání virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="before-you-begin"></a>Než začnete

- Před nahráním jakéhokoli virtuálního pevného disku do Azure, postupujte podle [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Zkontrolujte [plánování migrace na spravované disky](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) před zahájením migrace na [spravované disky](managed-disks-overview.md).
- Tento článek vyžaduje AzureRM verze modulu 5.6 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM.Compute`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="generalize-the-source-vm-using-sysprep"></a>Generalize zdrojového virtuálního počítače pomocí nástroje Sysprep

Nástroj Sysprep kromě jiného odebere všechny informace o vašich osobních účtech a připraví počítač, aby se dal použít jako image. Podrobnosti o nástroji Sysprep najdete v tématu [přehled nástroje Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Ujistěte se, že role serveru spuštěná na tomto počítači jsou podporovány nástrojem Sysprep. Další informace najdete v tématu [podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Pokud používáte nástroj Sysprep před nahráním svůj disk VHD do Azure poprvé, ujistěte se, máte [připravit virtuální počítač](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) před spuštěním nástroje Sysprep. 
> 
> 

1. Přihlaste se k virtuálnímu počítači Windows.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na **%windir%\system32\sysprep**a poté spusťte `sysprep.exe`.
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte **Zobrazit prostředí prvního spuštění počítače** a ujistěte se, že je zaškrtnuté políčko **Generalizovat**.
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

Když teď máte image, můžete z ní vytvořit jeden nebo více nových virtuálních počítačů. Tento příklad vytvoří virtuální počítač s názvem *Můjvp* z *myImage*v *myResourceGroup*.


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

Přihlaste se do nového virtuálního počítače. Další informace najdete v tématu [jak se připojit a přihlásit se na virtuálním počítači Azure s Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

