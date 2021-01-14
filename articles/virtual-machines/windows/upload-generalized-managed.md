---
title: Vytvoření virtuálního počítače z odeslaného zobecněného virtuálního pevného disku
description: Nahrajte zobecněný virtuální pevný disk do Azure a použijte ho k vytvoření nových virtuálních počítačů v modelu nasazení Správce prostředků.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: dc3920ac1e2269f4980ee67e2f5f82a0541ac0c2
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201497"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Nahrání generalizovaného virtuálního pevného disku a jeho použití k vytváření nových virtuálních počítačů v Azure

Tento článek vás provede použitím prostředí PowerShell k nahrání virtuálního pevného disku pro zobecněný virtuální počítač do Azure, vytvoření image z virtuálního pevného disku a vytvoření nového virtuálního počítače z této image. Můžete nahrát virtuální pevný disk exportovaný z místního nástroje virtualizace nebo z jiného cloudu. Použití [Managed disks](../managed-disks-overview.md) pro nový virtuální počítač zjednodušuje správu virtuálních počítačů a zajišťuje lepší dostupnost při umístění virtuálního počítače do skupiny dostupnosti. 

Vzorový skript najdete v tématu [ukázkový skript pro nahrání virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Než začnete

- Před nahráním libovolného virtuálního pevného disku do Azure byste měli postupovat po [přípravě virtuálního pevného disku (VHD) Windows nebo VHDX pro nahrání do Azure](prepare-for-upload-vhd-image.md).
- Než začnete s migrací do [Managed disks](../managed-disks-overview.md), přečtěte si téma [plánování migrace na Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) .

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalizace zdrojového virtuálního počítače pomocí nástroje Sysprep

Pokud jste to ještě neudělali, musíte virtuální počítač Sysprep před nahráním virtuálního pevného disku do Azure. Nástroj Sysprep kromě jiného odebere všechny informace o vašich osobních účtech a připraví počítač, aby se dal použít jako image. Podrobnosti o nástroji Sysprep najdete v tématu [Přehled nástroje Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Ujistěte se, že nástroj Sysprep podporuje role serveru spuštěné v počítači. Další informace najdete v tématu [Podpora nástroje Sysprep pro role serveru](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Pokud máte v úmyslu před prvním nahráním virtuálního pevného disku do Azure spustit nástroj Sysprep, ujistěte se, že jste [připravili virtuální](prepare-for-upload-vhd-image.md)počítač. 
> 
> 

1. Přihlaste se k virtuálnímu počítači s Windows.
1. Otevřete okno příkazového řádku jako správce. 
1. Odstraňte adresář Panther (C:\Windows\Panther).
1. Změňte adresář na%WINDIR%\system32\sysprep a potom spusťte příkaz `sysprep.exe` .
1. V dialogovém okně **Nástroj pro přípravu systému** vyberte možnost spustit **systém při spuštění uživatelského rozhraní (OOBE)** a ujistěte se, že je zaškrtávací políčko **generalize** povoleno.
1. V **Možnosti vypnutí** vyberte **vypnout**.
1. Vyberte **OK**.
   
    ![Spustit nástroj Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
1. Po dokončení programu Sysprep vypne virtuální počítač. Virtuální počítač nerestartujte.


## <a name="upload-the-vhd"></a>Nahrání virtuálního pevného disku 

Virtuální pevný disk teď můžete nahrát přímo do spravovaného disku. Pokyny najdete v tématu [nahrání virtuálního pevného disku do Azure pomocí Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).



Po nahrání virtuálního pevného disku na spravovaný disk musíte použít [příkaz Get-AzDisk](/powershell/module/az.compute/get-azdisk) k získání spravovaného disku.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>Vytvoření image
Vytvořte spravovanou bitovou kopii z zobecněného spravovaného disku s operačním systémem. Nahraďte následující hodnoty vlastními informacemi.

Nejdřív nastavte některé proměnné:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Vytvořte Image pomocí spravovaného disku.

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

## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Když teď máte image, můžete z ní vytvořit jeden nebo více nových virtuálních počítačů. Tento příklad vytvoří virtuální počítač s názvem *myVM* z *myImage* v *myResourceGroup*.


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

Přihlaste se k novému virtuálnímu počítači. Další informace najdete v tématu [jak se připojit a přihlásit se k virtuálnímu počítači Azure s Windows](connect-logon.md).
