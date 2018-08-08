---
title: Vytvoření spravovaných virtuálních počítačů Azure z virtuální pevný disk zobecněný místní | Dokumentace Microsoftu
description: Nahrání generalizovaného virtuálního pevného disku do Azure a použít ho k vytvoření nové virtuální počítače v modelu nasazení Resource Manager.
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
ms.openlocfilehash: 8fd88a0e3c5b387ce3ea586f6f23b3643a03e58d
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618163"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Nahrání generalizovaného virtuálního pevného disku a použít ho k vytvoření nové virtuální počítače v Azure

Toto téma vás provede pomocí prostředí PowerShell k nahrání virtuálního pevného disku z generalizovaného virtuálního počítače do Azure, vytvoření image z virtuálního pevného disku a vytvoření nového virtuálního počítače z této image. Můžete nahrát virtuální pevný disk exportovat z nástroj virtualization místní nebo v jiném cloudu. Pomocí [Managed Disks](managed-disks-overview.md) pro nový virtuální počítač ve skupinách usnadňuje správu virtuálních počítačů a poskytuje lepší dostupnost v případě, že virtuální počítač je umístěn ve skupině dostupnosti. 

Pokud chcete použít ukázkový skript, přečtěte si téma [ukázkový skript k nahrání virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="before-you-begin"></a>Než začnete

- Před odesláním jakéhokoli virtuálního pevného disku do Azure, měli byste postupovat podle [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Kontrola [naplánovat migraci do služby Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) před zahájením migrace na [Managed Disks](managed-disks-overview.md).
- Tento článek vyžaduje AzureRM modulu verze 5.6 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM.Compute`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="generalize-the-source-vm-using-sysprep"></a>Zdrojový virtuální počítač generalizovat pomocí programu Sysprep

Nástroj Sysprep kromě jiného odebere všechny informace o vašich osobních účtech a připraví počítač, aby se dal použít jako image. Podrobnosti o nástroji Sysprep najdete v tématu [přehled nástroje Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Ujistěte se, že na počítači spuštěná role serveru jsou podporované pomocí nástroje Sysprep. Další informace najdete v tématu [podpory nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Pokud používáte nástroj Sysprep před nahráním vašeho virtuálního pevného disku do Azure poprvé, ujistěte se, že máte [připravit virtuální počítač](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) před spuštěním nástroje Sysprep. 
> 
> 

1. Přihlaste se k virtuálnímu počítači Windows.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na **%windir%\system32\sysprep**a pak spusťte `sysprep.exe`.
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte **Zobrazit prostředí prvního spuštění počítače** a ujistěte se, že je zaškrtnuté políčko **Generalizovat**.
4. V **možnosti vypnutí**vyberte **vypnutí**.
5. Klikněte na **OK**.
   
    ![Spustit Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po dokončení nástroj Sysprep vypne virtuální počítač. Virtuální počítač nerestartuje.


## <a name="get-the-storage-account"></a>Získat účet úložiště.

Budete potřebovat účet úložiště v Azure k uložení této odeslané image virtuálního počítače. Můžete použít existující účet úložiště nebo vytvořte novou. 

Pokud jste k vytvoření spravovaného disku pro virtuální počítač pomocí virtuálního pevného disku, umístění účtu úložiště musí být stejné umístění, kde se vytváření virtuálního počítače.

Pokud chcete zobrazit účty úložiště k dispozici, zadejte:

```azurepowershell
Get-AzureRmStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Nahrání virtuálního pevného disku do účtu úložiště

Použití [Add-AzureRmVhd](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd) rutiny k nahrání virtuálního pevného disku do kontejneru v účtu úložiště. Tento příklad nahraje soubor *myVHD.vhd* z *"C:\Users\Public\Documents\Virtual pevné disky\"*  na účet úložiště s názvem *mystorageaccount* v *myResourceGroup* skupinu prostředků. Soubor se umístí do kontejneru s názvem *mycontainer* a nový název souboru bude *myUploadedVHD.vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


V případě úspěchu se získat odpověď bude vypadat nějak takto:

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

V závislosti na připojení k síti a velikost souboru virtuálního pevného disku tohoto příkazu může trvat dobu

### <a name="other-options-for-uploading-a-vhd"></a>Další možnosti pro nahrání virtuálního pevného disku
 
Můžete také nahrát VHD do účtu úložiště pomocí jedné z následujících akcí:

- [AzCopy](http://aka.ms/downloadazcopy)
- [Objekt Blob úložiště Azure kopírování rozhraní API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Objekty BLOB služby Azure Storage Explorer nahrávání](https://azurestorageexplorer.codeplex.com/)
- [Reference k rozhraní API REST úložiště importu/exportu služby](https://msdn.microsoft.com/library/dn529096.aspx)
-   Doporučujeme používat službu Import/Export, pokud odhadované nahrávání doba je delší než 7 dní. Můžete použít [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) pro odhad doby z jednotky pro velikost a přenos dat. 
    Import/Export je možné zkopírovat do účtu úložiště úrovně standard. Je potřeba zkopírovat ze standard storage do účtu služby premium storage pomocí některého nástroje, například AzCopy.

> [!IMPORTANT]
> Pokud používáte nástroj AzCopy ukládání vašeho virtuálního pevného disku do Azure, ujistěte se, že jste nastavili [/BlobType:page](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) dřív, než spustíte Nahrát skript. Pokud je cílový objekt blob a není tato možnost zadána, ve výchozím nastavení, AzCopy vytvoří objekt blob bloku.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Vytvoření spravované image z nahraných virtuálního pevného disku 

Vytvoření spravované image Pomocí zobecněný virtuální pevný disk operačního systému. Nahraďte hodnoty svými vlastními informacemi.


Nejprve nastavte některé parametry:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Vytvoření image Pomocí zobecněný virtuální pevný disk operačního systému.

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

Když teď máte image, můžete z ní vytvořit jeden nebo více nových virtuálních počítačů. Tento příklad vytvoří virtuální počítač s názvem *myVM* z *myImage*v *myResourceGroup*.


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

Přihlaste se k novému virtuálnímu počítači. Další informace najdete v tématu [jak se připojit a přihlaste se na virtuálním počítači Azure s Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

