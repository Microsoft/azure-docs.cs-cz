---
title: Vytvoření spravované image v Azure
description: Vytvořte spravovanou bitovou kopii generalizovaného virtuálního počítače nebo virtuálního pevného disku v Azure. Bitové kopie lze použít k vytvoření více virtuálních počítačů, které používají spravované disky.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 258bddec85e4ab182ff0b07c49cdc93f92264f95
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084460"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Vytvoření spravované image generalizovaného virtuálního počítače v Azure

Prostředek spravované image lze vytvořit ze zobecněného virtuálního počítače, který je uložen v účtu úložiště buď jako spravovaný disk, nebo jako nespravovaný disk. Z image je potom možné vytvořit více virtuálních počítačů. Informace o tom, jak se fakturují spravované bitové kopie, naleznete v [tématu Ceny spravovaných disků](https://azure.microsoft.com/pricing/details/managed-disks/). 

 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizace virtuálního počítače s Windows pomocí nástroje Sysprep

Program Sysprep odebere všechny osobní informace o účtu a zabezpečení a připraví počítač k použití jako bitová kopie. Informace o sysprepu naleznete v [přehledu sysprepu](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Ujistěte se, že role serveru spuštěné v počítači jsou podporovány programem Sysprep. Další informace naleznete v [tématu Podpora sysprep pro role serveru](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) a [nepodporované scénáře](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios).

> [!IMPORTANT]
> Po spuštění sysprep u virtuálního počítače, že virtuální počítač je považován za *generalizované* a nelze restartovat. Proces generalizace virtuálního počítače je nevrtaný. Pokud potřebujete zachovat původní virtuální hod funguje, měli byste vytvořit [kopii virtuálního virtuálního soudu](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) a zobecnit jeho kopii. 
>
> Pokud plánujete spustit program Sysprep před prvním nahráním virtuálního pevného disku (VHD) do Azure, ujistěte se, že jste [připravili virtuální počítač](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Chcete-li virtuální počítač s Windows zobecnit, postupujte takto:

1. Přihlaste se k virtuálnímu počítači s Windows.
   
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na %windir%\system32\sysprep `sysprep.exe`a spusťte program .
   
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte **Možnost zadat prostředí mimo systém (OOBE)** a zaškrtněte políčko **Generalizovat.**
   
4. V **části Možnosti vypnutí**vyberte příkaz **Vypnout**.
   
5. Vyberte **OK**.
   
    ![Spuštění sysprepu](./media/upload-generalized-managed/sysprepgeneral.png)

6. Po dokončení sysprep, vypne virtuální ho. Virtuální počítač nerestartujte.

> [!TIP]
> **Nepovinné** Pomocí [DISM](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) můžete optimalizovat image a zkrátit dobu prvního spuštění virtuálního počítače.
>
> Chcete-li optimalizovat bitovou kopii, připojte virtuální pevný disk poklepáním na `/optimize-image` něj v průzkumníku Windows a pak spusťte DISM s parametrem.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Kde D: je připojená cesta VHD.
>
> Spuštění `DISM /optimize-image` by mělo být poslední změnou, kterou provedete v pevném disku. Pokud před nasazením provedete nějaké změny v virtuálním `DISM /optimize-image` pevném disku, budete muset znovu spustit.

## <a name="create-a-managed-image-in-the-portal"></a>Vytvoření spravované bitové kopie na portálu 

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte image virtuálního počítače. Vyhledejte a vyberte **virtuální počítače**.

2. Vyberte virtuální počítač ze seznamu.

3. Na stránce **Virtuální počítač** pro virtuální počítač v horní nabídce vyberte **Zachytit**.

   Zobrazí se stránka **Vytvořit obrázek.**

4. V **pojmenujete název**, přijměte předem vyplněný název nebo zadejte název, který chcete pro bitovou kopii použít.

5. Ve **skupině Prostředků**vyberte buď možnost Vytvořit **nový** a zadejte název, nebo vyberte skupinu prostředků, kterou chcete použít z rozevíracího seznamu.

6. Pokud chcete odstranit zdrojový virtuální počítač po vytvoření bitové kopie, vyberte **Automaticky odstranit tento virtuální počítač po vytvoření bitové kopie**.

7. Pokud chcete, aby bylo co vidět v libovolné [zóně dostupnosti](../../availability-zones/az-overview.md), vyberte **možnost Zapnuto** pro **odolnost zóny**.

8. Vyberte **Vytvořit,** chcete-li vytvořit obraz.

Po vytvoření obrázku ji můžete najít jako prostředek **obrázku** v seznamu prostředků ve skupině prostředků.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Vytvoření image virtuálního virtuálního virtuálního aplikace pomocí Powershellu

 

Vytvoření image přímo z virtuálního počítače zajišťuje, že image zahrnuje všechny disky přidružené k virtuálnímu počítače, včetně disku operačního systému a všechny datové disky. Tento příklad ukazuje, jak vytvořit spravovanou bitovou kopii z virtuálního počítače, který používá spravované disky.

Než začnete, ujistěte se, že máte nejnovější verzi modulu Azure PowerShell. Chcete-li najít `Get-Module -ListAvailable Az` verzi, spusťte v PowerShellu. Pokud potřebujete upgradovat, [přečtěte si tématu Instalace Azure PowerShellu v systému Windows pomocí PowerShellu .](/powershell/azure/install-az-ps) Pokud používáte PowerShell místně, `Connect-AzAccount` spusťte a vytvořte připojení s Azure.


> [!NOTE]
> Pokud chcete uložit bitovou kopii do zónově redundantního úložiště, musíte ji vytvořit `-ZoneResilient` v oblasti,`New-AzImageConfig` která podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) a zahrnout parametr do konfigurace bitové kopie ( příkaz).

Pokud chcete vytvořit image virtuálního virtuálního montovana, postupujte takto:

1. Vytvořte některé proměnné.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Ujistěte se, že virtuální město bylo navráceno.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Nastavte stav virtuálního počítače na **Generalized**. 
   
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

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Vytvoření bitové kopie ze spravovaného disku pomocí Prostředí PowerShell

Pokud chcete vytvořit bitovou kopii pouze disku operačního systému, zadejte ID spravovaného disku jako disk operačního systému:

    
1. Vytvořte některé proměnné. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Získejte virtuální hod.

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


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Vytvoření obrázku ze snímku pomocí Powershellu

Spravanou bitovou kopii můžete vytvořit ze snímku generalizovaného virtuálního počítače takto:

    
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


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Vytvoření image z virtuálního zařízení, který používá účet úložiště

Chcete-li vytvořit spravovanou bitovou kopii z virtuálního počítače, který nepoužívá spravované disky, potřebujete identifikátor URI operačního virtuálního počítače v účtu úložiště v následujícím formátu: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd*. V tomto příkladu je virtuální pevný disk v *účtu mystorageaccount*, v kontejneru s názvem *vhdcontainer*a název souboru VHD je *vhdfilename.vhd*.


1.  Vytvořte některé proměnné.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Zastavit/navrátit virtuální ho.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Označte virtuální ho jako zobecněný.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Vytvořte obrázek pomocí generalizovaného virtuálního pevného disku operačního systému.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Další kroky
- [Vytvořte virtuální hod ze spravované bitové kopie](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

