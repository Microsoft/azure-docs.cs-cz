---
title: Vytvoření spravovaného virtuálního počítače Azure z zobecněného místního virtuálního pevného disku | Microsoft Docs
description: Nahrajte zobecněný virtuální pevný disk do Azure a použijte ho k vytvoření nových virtuálních počítačů v modelu nasazení Správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: be3ccfd0c562763d0968398ddb042dc5f07dbdcf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101562"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Nahrajte zobecněný virtuální pevný disk a použijte ho k vytvoření nových virtuálních počítačů v Azure.

Tento článek vás provede použitím prostředí PowerShell k nahrání virtuálního pevného disku pro zobecněný virtuální počítač do Azure, vytvoření image z virtuálního pevného disku a vytvoření nového virtuálního počítače z této image. Můžete nahrát virtuální pevný disk exportovaný z místního nástroje virtualizace nebo z jiného cloudu. Použití [Managed disks](managed-disks-overview.md) pro nový virtuální počítač zjednodušuje správu virtuálních počítačů a zajišťuje lepší dostupnost při umístění virtuálního počítače do skupiny dostupnosti. 

Vzorový skript najdete v tématu [ukázkový skript pro nahrání virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Před zahájením

- Před nahráním libovolného virtuálního pevného disku do Azure byste měli postupovat po [přípravě virtuálního pevného disku (VHD) Windows nebo VHDX pro nahrání do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Než začnete s migrací do [Managed disks](managed-disks-overview.md), přečtěte si téma [plánování migrace na Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) .

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalizace zdrojového virtuálního počítače pomocí nástroje Sysprep

Nástroj Sysprep kromě jiného odebere všechny informace o vašich osobních účtech a připraví počítač, aby se dal použít jako image. Podrobnosti o nástroji Sysprep najdete v tématu [Přehled nástroje Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Ujistěte se, že nástroj Sysprep podporuje role serveru spuštěné v počítači. Další informace najdete v tématu [Podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Pokud máte v úmyslu před prvním nahráním virtuálního pevného disku do Azure spustit nástroj Sysprep, ujistěte se, že jste připravili [virtuální](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)počítač. 
> 
> 

1. Přihlaste se k virtuálnímu počítači s Windows.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na%WINDIR%\system32\sysprep a potom spusťte příkaz `sysprep.exe`.
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte možnost spustit **systém při spuštění uživatelského rozhraní (OOBE)** a ujistěte se, že je zaškrtávací políčko **generalize** povoleno.
4. V **Možnosti vypnutí**vyberte **vypnout**.
5. Vyberte **OK**.
   
    ![Spustit nástroj Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po dokončení programu Sysprep vypne virtuální počítač. Nerestartujte virtuální počítač.


## <a name="get-a-storage-account"></a>Získání účtu úložiště

K uložení nahrané image virtuálního počítače budete potřebovat účet úložiště v Azure. Můžete buď použít existující účet úložiště, nebo vytvořit nový. 

Pokud k vytvoření spravovaného disku pro virtuální počítač použijete virtuální pevný disk, umístění účtu úložiště musí být stejné jako místo, ve kterém budete virtuální počítač vytvářet.

Pokud chcete zobrazit dostupné účty úložiště, zadejte:

```azurepowershell
Get-AzStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Nahrání virtuálního pevného disku do účtu úložiště

Pomocí rutiny [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) nahrajte VHD do kontejneru v účtu úložiště. Tento příklad nahraje soubor *myVHD. VHD* z *\\ pevných disků C:\Users\Public\Documents\Virtual* do účtu úložiště s názvem *mystorageaccount* ve skupině prostředků *myResourceGroup* . Soubor se umístí do kontejneru s názvem *myContainer* a nový název souboru bude *myUploadedVHD. VHD*.

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

### <a name="other-options-for-uploading-a-vhd"></a>Další možnosti pro nahrání VHD
 
Virtuální pevný disk můžete také nahrát do svého účtu úložiště pomocí jedné z následujících akcí:

- [AzCopy](https://aka.ms/downloadazcopy)
- [Azure Storage kopírovat rozhraní BLOB API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Průzkumník služby Azure Storage nahrávání objektů BLOB](https://azurestorageexplorer.codeplex.com/)
- [Referenční informace o REST API služby Import/Export úložiště](https://msdn.microsoft.com/library/dn529096.aspx)
-   Službu import/export doporučujeme používat, pokud je odhadovaný čas odeslání delší než 7 dní. Pomocí [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) můžete odhadnout čas od velikosti dat a jednotky přenosu. 
    Import/export se dá použít ke zkopírování do standardního účtu úložiště. Pomocí nástroje, jako je AzCopy, budete muset zkopírovat ze standardního úložiště na účet Premium Storage.

> [!IMPORTANT]
> Pokud používáte AzCopy k nahrání virtuálního pevného disku do Azure, ujistěte se, že jste před spuštěním skriptu pro nahrávání nastavili [**stránku/BlobType:** ](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs#upload-a-file) . Pokud je cílem objekt BLOB a tato možnost není zadaná, ve výchozím nastavení AzCopy vytvoří objekt blob bloku.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Vytvoření spravované image z nahraného virtuálního pevného disku 

Vytvořte spravovanou image z zobecněného virtuálního pevného disku s operačním systémem. Nahraďte následující hodnoty vlastními informacemi.


Nejdřív nastavte některé parametry:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Vytvořte Image pomocí zobecněného virtuálního pevného disku s operačním systémem.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Když teď máte image, můžete z ní vytvořit jeden nebo více nových virtuálních počítačů. Tento příklad vytvoří virtuální počítač s názvem *myVM* z *myImage*v *myResourceGroup*.


```powershell
New-AzVm `
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

Přihlaste se k novému virtuálnímu počítači. Další informace najdete v tématu [jak se připojit a přihlásit se k virtuálnímu počítači Azure s Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

