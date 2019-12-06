---
title: Vytvoření spravované image v Azure
description: Vytvořte spravovanou image zobecněného virtuálního počítače nebo virtuálního pevného disku v Azure. Image lze použít k vytvoření více virtuálních počítačů, které používají spravované disky.
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
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 01619027ddc79530dc9541584efa9a3e518f5136
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842054"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Vytvoření spravované image zobecněného virtuálního počítače v Azure

Prostředek spravované image lze vytvořit ze zobecněného virtuálního počítače, který je uložen v účtu úložiště buď jako spravovaný disk, nebo jako nespravovaný disk. Z image je potom možné vytvořit více virtuálních počítačů. Informace o tom, jak se účtují spravované image, najdete v článku [Managed disks ceny](https://azure.microsoft.com/pricing/details/managed-disks/). 

 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizace virtuálního počítače s Windows pomocí nástroje Sysprep

Nástroj Sysprep odebere všechny informace o vašem osobním účtu a zabezpečení a pak připraví počítač, který se má použít jako image. Informace o nástroji Sysprep najdete v tématu [Přehled nástroje Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Ujistěte se, že nástroj Sysprep podporuje role serveru spuštěné v počítači. Další informace najdete v tématu [Podpora nástroje Sysprep pro role serveru](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) a [nepodporované scénáře](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios).

> [!IMPORTANT]
> Po spuštění příkazu Sysprep na virtuálním počítači je tento virtuální počítač považován za *zobecněný* a nelze jej restartovat. Proces generalizace virtuálního počítače je nevrtaný. Pokud potřebujete zachovat fungování původního virtuálního počítače, měli byste vytvořit [kopii virtuálního počítače](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) a zobecnit jeho kopii. 
>
> Pokud máte v úmyslu spustit nástroj Sysprep před prvním nahráním virtuálního pevného disku (VHD) do Azure, ujistěte se, že jste [připravili virtuální počítač](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

K generalizaci virtuálního počítače s Windows použijte následující postup:

1. Přihlaste se k VIRTUÁLNÍmu počítači s Windows.
   
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na%WINDIR%\system32\sysprep a potom spusťte `sysprep.exe`.
   
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte možnost **Zadejte systém do prostředí při spuštění** a zaškrtněte políčko **generalizace** .
   
4. V **Možnosti vypnutí**vyberte **vypnout**.
   
5. Vyberte **OK**.
   
    ![Spustit nástroj Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Po dokončení programu Sysprep se virtuální počítač vypne. Nerestartujte virtuální počítač.

> [!TIP]
> **Volitelné** Pomocí [nástroje DISM](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) optimalizujte svou image a snižte čas prvního spuštění virtuálního počítače.
>
> Pokud chcete image optimalizovat, připojte virtuální pevný disk tak, že na něj dvakrát kliknete v Průzkumníkovi Windows a pak spustíte DISM s parametrem `/optimize-image`.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Kde D: je cesta připojeného virtuálního pevného disku.
>
> Spuštění `DISM /optimize-image` by mělo být Poslední modifikací, kterou provedete na virtuální pevný disk. Pokud provedete jakékoli změny VHD před nasazením, budete muset znovu spustit `DISM /optimize-image`.

## <a name="create-a-managed-image-in-the-portal"></a>Vytvoření spravované image na portálu 

1. Pokud chcete spravovat image virtuálního počítače, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

2. Ze seznamu vyberte svůj virtuální počítač.

3. Na stránce **virtuálního počítače** pro virtuální počítač v horní nabídce vyberte **zachytit**.

   Zobrazí se stránka **vytvořit obrázek** .

4. V poli **název**buď přijměte předem vyplněný název, nebo zadejte název, který chcete použít pro obrázek.

5. V části **Skupina prostředků**vyberte **vytvořit novou** a zadejte název nebo vyberte skupinu prostředků, kterou chcete použít v rozevíracím seznamu.

6. Pokud chcete odstranit zdrojový virtuální počítač po vytvoření image, po vytvoření image vyberte **automaticky odstranit tento virtuální počítač**.

7. Pokud chcete, aby se image používala v libovolné [zóně dostupnosti](../../availability-zones/az-overview.md), vyberte možnost **zapnuto** pro zajištění **odolnosti zóny**.

8. Vyberte **vytvořit** a vytvořte bitovou kopii.

Po vytvoření image je možné ji v seznamu prostředků ve skupině prostředků najít jako prostředek **obrázku** .



## <a name="create-an-image-of-a-vm-using-powershell"></a>Vytvoření image virtuálního počítače pomocí PowerShellu

 

Když vytvoříte image přímo z virtuálního počítače, zajistíte tím, že bitová kopie zahrnuje všechny disky přidružené k virtuálnímu počítači, včetně disku s operačním systémem a všech datových disků. Tento příklad ukazuje, jak vytvořit spravovanou bitovou kopii z virtuálního počítače, který používá spravované disky.

Než začnete, ujistěte se, že máte nejnovější verzi modulu Azure PowerShell. Pokud chcete zjistit verzi, spusťte `Get-Module -ListAvailable Az` v PowerShellu. Pokud potřebujete provést upgrade, přečtěte si téma [instalace Azure PowerShell ve Windows pomocí PowerShellGet](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, spusťte `Connect-AzAccount` pro vytvoření připojení k Azure.


> [!NOTE]
> Pokud chcete uložit image do redundantního úložiště zóny, je nutné ji vytvořit v oblasti, která podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) , a zahrnout parametr `-ZoneResilient` v konfiguraci bitové kopie (`New-AzImageConfig` příkaz).

K vytvoření image virtuálního počítače použijte tento postup:

1. Vytvořte některé proměnné.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Ujistěte se, že byl virtuální počítač uvolněný.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Nastavte stav virtuálního počítače na **zobecněno**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Získejte virtuální počítač. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Vytvořte konfiguraci image.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Vytvořte image.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Vytvoření image ze spravovaného disku pomocí PowerShellu

Pokud chcete vytvořit image jenom disku s operačním systémem, zadejte ID spravovaného disku jako disk s operačním systémem:

    
1. Vytvořte některé proměnné. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Získejte virtuální počítač.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Získejte ID spravovaného disku.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Vytvořte konfiguraci image.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Vytvořte image.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Vytvoření obrázku ze snímku pomocí PowerShellu

Spravovanou bitovou kopii můžete vytvořit ze snímku generalizované virtuální počítače pomocí následujících kroků:

    
1. Vytvořte některé proměnné. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Získejte snímek.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Vytvořte konfiguraci image.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Vytvořte image.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Vytvoření image z virtuálního počítače, který používá účet úložiště

Pokud chcete vytvořit spravovanou image z virtuálního počítače, který nepoužívá spravované disky, budete potřebovat identifikátor URI virtuálního pevného disku s operačním systémem v účtu úložiště, a to v následujícím formátu: https://*mystorageaccount*. blob.core.windows.net/*vhdcontainer*/*vhdfilename. VHD*. V tomto příkladu je virtuální pevný disk v *mystorageaccount*, v kontejneru s názvem *vhdcontainer*a název souboru VHD je *vhdfilename. VHD*.


1.  Vytvořte některé proměnné.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Zastavte nebo zrušte přidělení virtuálního počítače.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Označte virtuální počítač jako zobecněný.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Vytvořte bitovou kopii pomocí zobecněného virtuálního pevného disku s operačním systémem.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Další kroky
- [Vytvořte virtuální počítač ze spravované image](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

