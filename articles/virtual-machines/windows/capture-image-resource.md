---
title: Vytvoření spravované image v Azure
description: Vytvořte spravovanou image zobecněného virtuálního počítače nebo virtuálního pevného disku v Azure. Image lze použít k vytvoření více virtuálních počítačů, které používají spravované disky.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: 9128c44b7f446ab849d2afac055005a1b5fb3fcb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562227"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Vytvoření spravované image zobecněného virtuálního počítače v Azure

Prostředek spravované image lze vytvořit ze zobecněného virtuálního počítače, který je uložen v účtu úložiště buď jako spravovaný disk, nebo jako nespravovaný disk. Z image je potom možné vytvořit více virtuálních počítačů. Informace o tom, jak se účtují spravované image, najdete v článku [Managed disks ceny](https://azure.microsoft.com/pricing/details/managed-disks/). 

Jedna spravovaná bitová kopie podporuje až 20 současných nasazení. Při pokusu o vytvoření více než 20 virtuálních počítačů současně ze stejné spravované image může docházet k vypršení časového limitu zřizování z důvodu omezení výkonu úložiště u jednoho virtuálního pevného disku. Pokud chcete vytvořit více než 20 virtuálních počítačů současně, použijte image [Galerie sdílených imagí](../shared-image-galleries.md) nakonfigurovanou s 1 replikou pro každé 20 souběžných nasazení virtuálních počítačů.

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizace virtuálního počítače s Windows pomocí nástroje Sysprep

Nástroj Sysprep odebere všechny informace o vašem osobním účtu a zabezpečení a pak připraví počítač, který se má použít jako image. Informace o nástroji Sysprep najdete v tématu [Přehled nástroje Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Ujistěte se, že nástroj Sysprep podporuje role serveru spuštěné v počítači. Další informace najdete v tématu [Podpora nástroje Sysprep pro role serveru](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) a [nepodporované scénáře](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios). 

> [!IMPORTANT]
> Po spuštění příkazu Sysprep na virtuálním počítači je tento virtuální počítač považován za *zobecněný* a nelze jej restartovat. Proces generalizace virtuálního počítače je nevrtaný. Pokud potřebujete zachovat fungování původního virtuálního počítače, měli byste vytvořit [kopii virtuálního počítače](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) a zobecnit jeho kopii. 
>
>Nástroj Sysprep vyžaduje, aby jednotky byly plně dešifrovány. Pokud jste na svém VIRTUÁLNÍm počítači povolili šifrování, před spuštěním nástroje Sysprep zakažte šifrování.
>
> Pokud máte v úmyslu spustit nástroj Sysprep před prvním nahráním virtuálního pevného disku (VHD) do Azure, ujistěte se, že jste [připravili virtuální počítač](prepare-for-upload-vhd-image.md).  
> 
> 

K generalizaci virtuálního počítače s Windows použijte následující postup:

1. Přihlaste se k VIRTUÁLNÍmu počítači s Windows.
   
2. Otevřete okno příkazového řádku jako správce. 

3. Odstraňte adresář Panther (C:\Windows\Panther). Pak změňte adresář na%WINDIR%\system32\sysprep a potom spusťte `sysprep.exe` .
   
4. V dialogovém okně **Nástroj pro přípravu systému** vyberte možnost **Zadejte systém do prostředí při spuštění** a zaškrtněte políčko **generalizace** .
   
5. V **Možnosti vypnutí** vyberte **vypnout**.
   
6. Vyberte **OK**.
   
    ![Spustit nástroj Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Po dokončení programu Sysprep se virtuální počítač vypne. Virtuální počítač nerestartujte.

> [!TIP]
> **Volitelné** Pomocí [nástroje DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) optimalizujte svou image a snižte čas prvního spuštění virtuálního počítače.
>
> Pokud chcete image optimalizovat, připojte virtuální pevný disk tak, že na něj dvakrát kliknete v Průzkumníkovi Windows, a pak spusťte DISM s `/optimize-image` parametrem.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Kde D: je cesta připojeného virtuálního pevného disku.
>
> `DISM /optimize-image`Mělo by se jednat o poslední změnu, kterou provedete na virtuální pevný disk. Pokud provedete jakékoli změny VHD před nasazením, budete muset `DISM /optimize-image` znovu spustit.

## <a name="create-a-managed-image-in-the-portal"></a>Vytvoření spravované image na portálu 

1. Pokud chcete spravovat image virtuálního počítače, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

2. Ze seznamu vyberte svůj virtuální počítač.

3. Na stránce **virtuálního počítače** pro virtuální počítač v horní nabídce vyberte **zachytit**.

   Zobrazí se stránka **vytvořit obrázek** .

4. V poli **název** buď přijměte předem vyplněný název, nebo zadejte název, který chcete použít pro obrázek.

5. V části **Skupina prostředků** vyberte **vytvořit novou** a zadejte název nebo vyberte skupinu prostředků, kterou chcete použít v rozevíracím seznamu.

6. Pokud chcete odstranit zdrojový virtuální počítač po vytvoření image, po vytvoření image vyberte **automaticky odstranit tento virtuální počítač**.

7. Pokud chcete, aby se image používala v libovolné [zóně dostupnosti](../../availability-zones/az-overview.md), vyberte možnost **zapnuto** pro zajištění **odolnosti zóny**.

8. Výběrem **Vytvořit** image vytvořte.

Po vytvoření image je možné ji v seznamu prostředků ve skupině prostředků najít jako prostředek **obrázku** .



## <a name="create-an-image-of-a-vm-using-powershell"></a>Vytvoření image virtuálního počítače pomocí PowerShellu

 

Když vytvoříte image přímo z virtuálního počítače, zajistíte tím, že bitová kopie zahrnuje všechny disky přidružené k virtuálnímu počítači, včetně disku s operačním systémem a všech datových disků. Tento příklad ukazuje, jak vytvořit spravovanou bitovou kopii z virtuálního počítače, který používá spravované disky.

Než začnete, ujistěte se, že máte nejnovější verzi modulu Azure PowerShell. Pokud chcete zjistit verzi, spusťte `Get-Module -ListAvailable Az` v PowerShellu. Pokud potřebujete provést upgrade, přečtěte si téma [instalace Azure PowerShell ve Windows pomocí PowerShellGet](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, spusťte příkaz `Connect-AzAccount` a vytvořte připojení k Azure.


> [!NOTE]
> Pokud chcete uložit image do redundantního úložiště zóny, je potřeba ji vytvořit v oblasti, která podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) , a zahrnout `-ZoneResilient` parametr do konfigurace Image ( `New-AzImageConfig` příkaz).

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

Pokud chcete vytvořit spravovanou image z virtuálního počítače, který nepoužívá spravované disky, budete potřebovat identifikátor URI virtuálního pevného disku s operačním systémem v účtu úložiště, a to v následujícím formátu: https://*mystorageaccount*. blob.Core.Windows.NET/*vhdcontainer* / *vhdfilename. VHD*. V tomto příkladu je virtuální pevný disk v *mystorageaccount*, v kontejneru s názvem *vhdcontainer* a název souboru VHD je *vhdfilename. VHD*.


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
- [Vytvořte virtuální počítač ze spravované image](create-vm-generalized-managed.md). 