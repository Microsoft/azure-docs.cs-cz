---
title: Vytvoření spravovaného virtuálního počítače Azure z zobecněného místního virtuálního pevného disku
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
ms.openlocfilehash: d0995fed61d169cc173ca01767c2e48f4f798b0d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74067437"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Nahrajte zobecněný virtuální pevný disk a použijte ho k vytvoření nových virtuálních počítačů v Azure.

Tento článek vás provede použitím prostředí PowerShell k nahrání virtuálního pevného disku pro zobecněný virtuální počítač do Azure, vytvoření image z virtuálního pevného disku a vytvoření nového virtuálního počítače z této image. Můžete nahrát virtuální pevný disk exportovaný z místního nástroje virtualizace nebo z jiného cloudu. Použití [Managed disks](managed-disks-overview.md) pro nový virtuální počítač zjednodušuje správu virtuálních počítačů a zajišťuje lepší dostupnost při umístění virtuálního počítače do skupiny dostupnosti. 

Vzorový skript najdete v tématu [ukázkový skript pro nahrání virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Než začnete

- Před nahráním libovolného virtuálního pevného disku do Azure byste měli postupovat po [přípravě virtuálního pevného disku (VHD) Windows nebo VHDX pro nahrání do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Než začnete s migrací do [Managed disks](managed-disks-overview.md), přečtěte si téma [plánování migrace na Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) .

 


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalizace zdrojového virtuálního počítače pomocí nástroje Sysprep

Nástroj Sysprep kromě jiného odebere všechny informace o vašich osobních účtech a připraví počítač, aby se dal použít jako image. Podrobnosti o nástroji Sysprep najdete v tématu [Přehled nástroje Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Ujistěte se, že nástroj Sysprep podporuje role serveru spuštěné v počítači. Další informace najdete v tématu [Podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Pokud máte v úmyslu před prvním nahráním virtuálního pevného disku do Azure spustit nástroj Sysprep, ujistěte se, že jste [připravili virtuální](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)počítač. 
> 
> 

1. Přihlaste se k virtuálnímu počítači s Windows.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na%WINDIR%\system32\sysprep a potom spusťte `sysprep.exe`.
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte možnost spustit **systém při spuštění uživatelského rozhraní (OOBE)** a ujistěte se, že je zaškrtávací políčko **generalize** povoleno.
4. V **Možnosti vypnutí**vyberte **vypnout**.
5. Vyberte **OK**.
   
    ![Spustit nástroj Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po dokončení programu Sysprep vypne virtuální počítač. Nerestartujte virtuální počítač.


## <a name="upload-the-vhd-to-your-storage-account"></a>Nahrání virtuálního pevného disku do účtu úložiště

Virtuální pevný disk teď můžete nahrát přímo do spravovaného disku. Pokyny najdete v tématu [nahrání virtuálního pevného disku do Azure pomocí Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).


## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Vytvoření spravované image z nahraného virtuálního pevného disku 

Vytvořte spravovanou bitovou kopii z zobecněného spravovaného disku s operačním systémem. Nahraďte následující hodnoty vlastními informacemi.


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


## <a name="next-steps"></a>Další kroky

Přihlaste se k novému virtuálnímu počítači. Další informace najdete v tématu [jak se připojit a přihlásit se k virtuálnímu počítači Azure s Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

