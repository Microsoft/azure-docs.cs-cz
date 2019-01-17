---
title: Vytvoření spravované image v Azure | Dokumentace Microsoftu
description: Vytvoření spravované image zobecněného virtuálního počítače nebo virtuální pevný disk v Azure. Image je možné vytvořit několik virtuálních počítačů používat spravované disky.
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
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 9dc57876467deb24a763a3c3772209e3e2e0ca0d
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360033"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Vytvoření spravované image zobecněného virtuálního počítače v Azure

Prostředek spravované image můžete vytvořit z generalizovaného virtuálního počítače (VM), která je uložena jako spravovaný disk nebo nespravovaný disk v účtu úložiště. Bitovou kopii poté slouží k vytvoření několika virtuálních počítačů. Informace o spravovaných bille Image, najdete v části [cenami služby Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizace virtuálního počítače s Windows pomocí nástroje Sysprep

Nástroj Sysprep odebere všechny osobní účet a informace o zabezpečení a pak připraví počítač, který má být použit jako obrázek. Informace o nástroji Sysprep najdete v tématu [přehled nástroje Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Ujistěte se, že na počítači spuštěná role serveru jsou podporované pomocí nástroje Sysprep. Další informace najdete v tématu [podpory nástroje Sysprep pro role serveru](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Po spuštění nástroje Sysprep na virtuálním počítači, tento virtuální počítač se považuje za *zobecněný* a nelze ji restartovat. Proces generalizace virtuálního počítače je nevrtaný. Pokud potřebujete zachovat původní virtuální počítač funguje, měli byste vytvořit [kopii virtuálního počítače](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) a generalizace jeho kopie. 
>
> Pokud chcete spustit nástroj Sysprep před nahráním virtuálního pevného disku (VHD) do Azure poprvé, ujistěte se, že máte [připravit virtuální počítač](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Generalizace virtuálního počítače Windows, postupujte podle těchto kroků:

1. Přihlaste se k vaší Windows virtuální počítač.
   
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na % windir%\system32\sysprep a pak spusťte `sysprep.exe`.
   
3. V **nástroj pro přípravu systému** dialogu **zadejte systému Out-of-Box zapnutí** a vyberte **generalizace** zaškrtávací políčko.
   
4. Pro **možnosti vypnutí**vyberte **vypnutí**.
   
5. Vyberte **OK**.
   
    ![Spustit Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Po dokončení nástroj Sysprep vypne virtuální počítač. Virtuální počítač nerestartuje.


## <a name="create-a-managed-image-in-the-portal"></a>Vytvoření spravované image na portálu 

1. Otevřete web [Azure Portal](https://portal.azure.com).

2. V nabídce na levé straně vyberte **virtuálních počítačů** a potom vyberte virtuální počítač ze seznamu.

3. V **virtuálního počítače** stránce pro virtuální počítač, v horní nabídce vyberte **zachycení**.

   **Vytvoření image** se zobrazí stránka.

4. Pro **název**, přijměte předvyplněný název nebo zadejte název, který chcete použít pro bitovou kopii.

5. Pro **skupiny prostředků**, vyberte buď **vytvořit nový** a zadejte název, nebo vyberte **použít existující** a vyberte skupinu prostředků z rozevíracího seznamu.

6. Pokud chcete odstranit zdrojový virtuální počítač po vytvořili, vyberte bitovou kopii **automaticky odstranit tento virtuální počítač po vytvoření image**.

7. Pokud chcete mít možnost použít bitovou kopii v libovolném [zóna dostupnosti](../../availability-zones/az-overview.md)vyberte **na** pro **odolnost zóny**.

8. Vyberte **vytvořit** k vytvoření této image.

9. Po vytvoření image, najdete ho jako **Image** prostředku v seznamu prostředků ve skupině prostředků.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Vytvoření image virtuálního počítače pomocí Powershellu

Vytvoření bitové kopie přímo z virtuálního počítače se zajistí, že na obrázku obsahuje všechny disky přidružené k virtuálnímu počítači, včetně disk s operačním systémem a všechny datové disky. Tento příklad ukazuje postup vytvoření spravované image z virtuálního počítače, že používá spravované disky.


Než začnete, ujistěte se, že máte nejnovější verzi modul AzureRM.Compute Powershellu, který musí být verze 5.7.0 nebo novější. Pokud chcete zjistit verzi, spusťte `Get-Module -ListAvailable AzureRM.Compute` v prostředí PowerShell. Pokud potřebujete upgrade, přečtěte si téma [nainstalujte prostředí Azure PowerShell ve Windows pomocí Správce balíčků PowerShellGet](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, spusťte `Connect-AzureRmAccount` vytvořit připojení k Azure.


> [!NOTE]
> Pokud chcete ukládat image v zónově redundantní úložiště, budete muset vytvořit v oblasti, která podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) a zahrnout `-ZoneResilient` parametr v konfiguraci bitové kopie (`New-AzureRmImageConfig` příkaz).

Pokud chcete vytvořit image virtuálního počítače, postupujte takto:

1. Vytvořte několik proměnných.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Ujistěte se, že virtuální počítač bylo zrušeno.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Nastavte stav virtuálního počítače na **zobecněno**. 
   
    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Získejte virtuální počítač. 

    ```azurepowershell-interactive
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Vytvořte konfiguraci image.

    ```azurepowershell-interactive
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Vytvořte image.

    ```azurepowershell-interactive
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Vytvořit image ze spravovaného disku s použitím prostředí PowerShell

Pokud chcete vytvořit image disku operačního systému, určete ID spravovaného disku jako disku s operačním systémem:

    
1. Vytvořte několik proměnných. 

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

3. Získejte ID spravovaného disku.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Vytvořte konfiguraci image.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Vytvořte image.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Vytvořit image ze snímku pomocí Powershellu

Spravované image můžete vytvořit ze snímku generalizovaného virtuálního počítače pomocí následujících kroků:

    
1. Vytvořte několik proměnných. 

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
   
3. Vytvořte konfiguraci image.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Vytvořte image.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vhd-in-a-storage-account"></a>Vytvořit image ze souboru VHD v účtu úložiště

Vytvoření spravované image z generalizovaného VHD operačního systému v účtu úložiště. Budete potřebovat identifikátor URI virtuálního pevného disku v účtu úložiště, který se nachází v následujícím formátu: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer* /  *vhdfilename.VHD*. V tomto příkladu je virtuální pevný disk v *mystorageaccount*, v kontejneru nazvaném *vhdcontainer*, a název souboru virtuálního pevného disku je *vhdfilename.vhd*.


1.  Vytvořte několik proměnných.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Zastavit/uvolněte virtuálního počítače.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Označte virtuální počítač za generalizovaný.

    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  Vytvořte bitovou kopii pomocí zobecněný virtuální pevný disk operačního systému.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Další postup
- [Vytvoření virtuálního počítače ze spravované image](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

