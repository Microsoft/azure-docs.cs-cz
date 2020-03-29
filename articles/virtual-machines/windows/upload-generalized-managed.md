---
title: Vytvoření virtuálního virtuálního virtuálního virtuálního montovazy z nahraného generalizovaného virtuálního pevného disku
description: Nahrajte zobecněný virtuální pevný disk do Azure a použijte ho k vytvoření nových virtuálních počítačů v modelu nasazení Správce prostředků.
services: virtual-machines-windows
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 3c482caf2407c89ffdb6c55c9184c31e2e3197c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464950"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Nahrání generalizovaného virtuálního pevného disku a jeho použití k vytváření nových virtuálních počítačů v Azure

Tento článek vás provede pomocí PowerShellu k nahrání virtuálního počítače zobecněného virtuálního počítače do Azure, vytvoření image z virtuálního pevného disku a vytvoření nového virtuálního počítače z této bitové kopie. Virtuální pevný disk exportovaný z místního virtualizačního nástroje nebo z jiného cloudu můžete nahrát. Použití [spravovaných disků](managed-disks-overview.md) pro nový virtuální počítače zjednodušuje správu virtuálních počítačů a poskytuje lepší dostupnost, když je virtuální modul umístěn v sadě dostupnosti. 

Ukázkový skript najdete v [tématu Ukázkový skript pro nahrání virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Než začnete

- Před nahráním jakéhokoli v hd disku do Azure byste měli postupovat podle [pokynů Připravit virtuální pevný disk windows nebo VHDX k nahrání do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Před zahájením migrace na [spravované disky](managed-disks-overview.md)zkontrolujte plán migrace na spravované [disky](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) .

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalize zdrojového virtuálního virtuálního aplikace pomocí sysprepu

Pokud jste to ještě neudělali, musíte před nahráním virtuálního počítače do Azure vytvořit sysprep virtuálního počítače. Nástroj Sysprep kromě jiného odebere všechny informace o vašich osobních účtech a připraví počítač, aby se dal použít jako image. Podrobnosti o sysprepu naleznete v [přehledu sysprepu](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Ujistěte se, že role serveru spuštěné v počítači jsou podporovány programem Sysprep. Další informace naleznete v [tématu Podpora sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Pokud plánujete spustit sysprep před prvním nahráním virtuálního pevného disku do Azure, ujistěte se, že jste [připravili virtuální počítač](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Přihlaste se k virtuálnímu počítači s Windows.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na %windir%\system32\sysprep `sysprep.exe`a spusťte program .
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte možnost Zadat prostředí pro **neschod (OOBE)** a ujistěte se, že je povoleno políčko **Generalizovat.**
4. V **části Možnosti vypnutí**vyberte příkaz **Vypnout**.
5. Vyberte **OK**.
   
    ![Spuštění sysprepu](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po dokončení nástroje Sysprep se virtuální počítač vypne. Virtuální počítač nerestartujte.


## <a name="upload-the-vhd"></a>Nahrání virtuálního pevného disku 

Nyní můžete nahrát virtuální pevný disk přímo na spravovaný disk. Pokyny najdete [v tématu Upload a VHD to Azure using Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).



Jakmile je virtuální disk odeslán na spravovaný disk, musíte použít [Get-AzDisk,](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) abyste získali spravovaný disk.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>Vytvoření image
Vytvořte spravovanou bitovou kopii z generalizovaného spravovaného disku operačního systému. Nahraďte následující hodnoty vlastními informacemi.

Nejprve nastavte některé proměnné:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Vytvořte bitovou kopii pomocí spravovaného disku.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Vytvořte image.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Když teď máte image, můžete z ní vytvořit jeden nebo více nových virtuálních počítačů. Tento příklad vytvoří virtuální hod s názvem *myVM* z *myImage*, v *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Další kroky

Přihlaste se k novému virtuálnímu počítači. Další informace najdete v tématu [Jak se připojit a přihlásit k virtuálnímu počítači Azure se systémem Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

