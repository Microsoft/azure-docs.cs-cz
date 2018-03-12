---
title: "Vytvořte bitovou kopii spravovaných v Azure | Microsoft Docs"
description: "Vytvořte bitovou kopii spravované zobecněný virtuální počítač nebo virtuální pevný disk v Azure. Obrázky lze použít k vytvoření více virtuálních počítačů, které používají spravovaný disky."
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
ms.date: 03/06/2018
ms.author: cynthn
ms.openlocfilehash: f18defbec79638cf2c3363e6429ddcac01b1502e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Vytvořte bitovou kopii spravované zobecněný virtuálního počítače v Azure

Z zobecněný virtuální počítač, který je uložený jako spravovaných disků nebo diskem nespravované v účtu úložiště můžete vytvořit prostředek spravované bitové kopie. Obrázek pak lze vytvořit víc virtuálních počítačů. 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalize virtuální počítač s Windows pomocí nástroje Sysprep

Nástroj Sysprep odstraní všechny vaše osobní informace o účtu, mimo jiné a připraví počítač, který se má použít jako obrázek. Podrobnosti o nástroji Sysprep najdete v tématu [postup použití nástroje Sysprep: Úvod](http://technet.microsoft.com/library/bb457073.aspx).

Ujistěte se, že role serveru spuštěná na tomto počítači jsou podporovány nástrojem Sysprep. Další informace najdete v tématu [podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Po spuštění nástroje sysprep ve virtuálním počítači bude považován za *zobecněn* a nelze ji restartovat. Proces generalizací virtuálního počítače není reverzibilního. Pokud potřebujete zachovat původní virtuální počítač fungovat, měli byste podniknout [kopie virtuálního počítače](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) a generalize kopie. 
>
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


## <a name="create-a-managed-image-in-the-portal"></a>Vytvoření bitové kopie spravované na portálu 

1. Otevřete [portál](https://portal.azure.com).
2. V nabídce na levé straně klikněte na virtuální počítače a potom vyberte virtuální počítač ze seznamu.
3. Na stránce pro virtuální počítač, v horní nabídce klikněte na tlačítko **zaznamenat**.
3. V **název**, zadejte název, který chcete použít pro bitovou kopii.
4. V **skupiny prostředků** vyberte buď **vytvořit nový** a zadejte název, nebo vyberte **použít existující** a vyberte skupinu prostředků z rozevíracího seznamu.
5. Pokud chcete odstranit zdrojového virtuálního počítače po image vytvořené, vyberte **automaticky odstranit tento virtuální počítač po vytvoření image**.
6. Až budete hotoví, klikněte na **Vytvořit**.
16. Po vytvoření image se bude zobrazovat jako **Image** prostředku v seznamu prostředků ve skupině prostředků.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Vytvořit bitovou kopii virtuálního počítače pomocí prostředí Powershell

Vytvoření bitové kopie přímo z virtuálního počítače zajistí, že image obsahuje všechny disky, které jsou spojené s virtuálním Počítačem, včetně disku operačního systému a všech datových disků. Tento příklad ukazuje, jak vytvořit spravované obrázek z virtuálního počítače, aby používá ke správě disků.


Než začnete, ujistěte se, že máte nejnovější verzi modulu AzureRM.Compute prostředí PowerShell. Spusťte následující příkaz k její instalaci. (Použití `Get-Module` pro kontrolu verze, jaké máte.)

```azurepowershell-interactive
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Další informace najdete v tématu [Azure PowerShell verze](/powershell/azure/overview).


1. Vytvořte některé proměnné.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Zajistěte, aby že bylo zrušeno přidělení virtuálního počítače.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Nastavte stav virtuálního počítače na **zobecněno**. 
   
    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Získáte virtuální počítač. 

    ```azurepowershell-interactive
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Vytvořte konfiguraci bitové kopie.

    ```azurepowershell-interactive
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. Vytvoření bitové kopie.

    ```azurepowershell-interactive
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 
## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Vytvořit bitovou kopii z disku spravované pomocí prostředí PowerShell

Pokud chcete vytvořit bitovou kopii disku operačního systému, můžete také vytvořit bitovou kopii tak, že zadáte ID spravovaného disku jako disk operačního systému.

    
1. Vytvořte některé proměnné. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Získáte virtuální počítač.

   ```azurepowershell-interactive
   $vm = Get-AzureRmVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Získání ID spravovaného disku.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Vytvořte konfiguraci bitové kopie.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Vytvoření bitové kopie.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Vytvoření bitové kopie ze snímku pomocí prostředí Powershell

Můžete vytvořit bitovou kopii spravované ze snímku zobecněný virtuálního počítače.

    
1. Vytvořte některé proměnné. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Pořízení snímku.

   ```azurepowershell-interactive
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Vytvořte konfiguraci bitové kopie.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Vytvoření bitové kopie.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-image-from-a-vhd-in-a-storage-account"></a>Vytvoření bitové kopie z disku VHD v účtu úložiště

Vytvoření bitové kopie spravované z disku VHD zobecněný operačního systému v účtu úložiště. Budete potřebovat identifikátor URI virtuálního pevného disku v účtu úložiště, která je ve formátu https://*můj_účet_úložiště*.blob.core.windows.net/*kontejneru*/*vhd_filename.vhd*. V tomto příkladu je virtuální pevný disk, který se používá v *můj_účet_úložiště* v kontejneru nazvaném *vhdcontainer* a název souboru virtuálního pevného disku je *osdisk.vhd*.


1.  Nastavte nejprve, běžné parametry:

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. Step\deallocate virtuálního počítače.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Virtuální počítač označte jako zobecněn.

    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  Vytvořte bitovou kopii pomocí vaší zobecněný virtuální pevný disk operačního systému.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Další postup
- Teď můžete [vytvořte virtuální počítač z bitové kopie zobecněný spravované](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

