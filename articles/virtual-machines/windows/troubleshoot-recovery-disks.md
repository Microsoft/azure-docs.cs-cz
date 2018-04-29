---
title: Použití Windows řešení potíží s virtuálních počítačů v prostředí Azure PowerShell | Microsoft Docs
description: Zjistěte, jak k řešení potíží virtuální počítač s Windows v Azure připojením disk operačního systému k obnovení virtuálního počítače pomocí prostředí Azure PowerShell
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 2201fa48c84aec2c291d8df7e16293a41720ce3e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Řešení potíží s virtuální počítač s Windows pomocí disk operačního systému se připojuje k obnovení virtuálního počítače pomocí prostředí Azure PowerShell
Pokud Windows virtuálního počítače (VM) v prostředí Azure dojde k chybě spouštěcí nebo disk, musíte provést na virtuálním pevném disku, sám sebe pro řešení potíží. Běžným příkladem bude aplikaci, která selhala aktualizace, která brání virtuálního počítače nebudou moct úspěšně spustil. Tento článek podrobné informace o tom, jak pomocí prostředí Azure PowerShell pro připojení k jiným virtuálním Počítačem Windows opravte případné chyby a pak znovu vytvořte původní virtuální počítač virtuální pevný disk.


## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Proces řešení potíží je následující:

1. Odstraňte virtuální počítač, na zjištění problémy, zachovat virtuální pevné disky.
2. Připojte a připojit virtuální pevný disk na jiný virtuální počítač s Windows pro účely odstraňování potíží.
3. Připojení k virtuálnímu počítači pro řešení potíží. Úpravy souborů nebo spustit žádné nástroje na opravte problémy v původní virtuální pevný disk.
4. Odpojení virtuálního pevného disku od virtuálního počítače pro řešení potíží.
5. Vytvoření virtuálního počítače pomocí původní virtuální pevný disk.

Ujistěte se, že máte [nejnovější prostředí Azure PowerShell](/powershell/azure/overview) nainstalován a přihlášení k vašemu předplatnému:

```powershell
Connect-AzureRmAccount
```

V následujících příkladech nahraďte názvy parametrů s vlastními hodnotami. Zahrnout názvy parametrů příklad `myResourceGroup`, `mystorageaccount`, a `myVM`.


## <a name="determine-boot-issues"></a>Určení spouštěcí problémy
Zobrazí se snímek virtuálního počítače v Azure k řešení potíží spouštěcí. Tento snímek obrazovky může pomoct identifikovat, proč virtuální počítač se nepodaří spustit. Následující příklad získá na snímku obrazovky z virtuálního počítače Windows s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Zkontrolujte na snímku obrazovky, chcete-li zjistit, proč se nedaří spustit virtuální počítač. Poznámka: všechny specifické chybové zprávy nebo kódy chyb, které jsou dispozici.


## <a name="view-existing-virtual-hard-disk-details"></a>Zobrazení podrobností existující virtuální pevný disk
Než k jiným virtuálním Počítačem můžete připojit virtuální pevný disk, musíte určit název virtuálního pevného disku (VHD).

Následující příklad načte informace pro virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Vyhledejte `Vhd URI` v rámci `StorageProfile` části z výstupu předchozí příkaz. Zkrácená následující příklad ukazuje výstup `Vhd URI` na konci bloku kódu:

```powershell
RequestId                     : 8a134642-2f01-4e08-bb12-d89b5b81a0a0
StatusCode                    : OK
ResourceGroupName             : myResourceGroup
Id                            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
Name                          : myVM
Type                          : Microsoft.Compute/virtualMachines
...
StorageProfile                :
  ImageReference              :
    Publisher                 : MicrosoftWindowsServer
    Offer                     : WindowsServer
    Sku                       : 2016-Datacenter
    Version                   : latest
  OsDisk                      :
    OsType                    : Windows
    Name                      : myVM
    Vhd                       :
      Uri                     : https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    Caching                   : ReadWrite
    CreateOption              : FromImage
```


## <a name="delete-existing-vm"></a>Odstraňte existující virtuální počítač
Virtuální pevné disky a virtuální počítače jsou v Azure dva různé prostředky. Virtuální pevný disk je, kde jsou uloženy samotného operačního systému, aplikace a konfigurace. Virtuální počítač je jenom metadata, která definuje velikosti či umístění a odkazuje na prostředky, jako je virtuální pevný disk nebo virtuální síťová karta (NIC). Každý virtuální pevný disk má zapůjčení přiřazen při připojen k virtuálnímu počítači. Přestože datové disky je možné připojovat a odpojovat dokonce i za běhu virtuálního počítače, disk s operačním systémem není možné odpojit, dokud se neodstraní prostředek virtuálního počítače. Zapůjčení i nadále i v případě, že tento virtuální počítač je ve stavu Zastaveno a deallocated přidružení disk operačního systému virtuálního počítače.

Prvním krokem k obnovení virtuálního počítače je odstranit samotné prostředků virtuálního počítače. Když odstraníte virtuální počítač, virtuální pevné disky zůstanou ve vašem účtu úložiště. Po odstranění virtuálního počítače připojit virtuální pevný disk k jiným virtuálním Počítačem vyřešit chyby.

Následující příklad odstraní virtuální počítač s názvem `myVM` ze skupiny prostředků s názvem `myResourceGroup`:

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Počkejte, dokud je virtuální počítač dokončí odstraňování před připojit virtuální pevný disk k jiným virtuálním Počítačem. Zapůjčení na virtuální pevný disk, který přidruží k němu virtuální počítač je nutné uvolnit předtím, než k jiným virtuálním Počítačem můžete připojit virtuální pevný disk.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Připojit existující virtuální pevný disk k jiným virtuálním Počítačem
Pro několika dalších krocích použijete jiný počítač pro účely odstraňování potíží. Existující virtuální pevný disk se připojit k řešení potíží VM na Procházet a upravovat obsah na disk. Tento proces umožňuje opravte všechny chyby konfigurace nebo zkontrolujte další aplikace nebo systému souborů protokolu, např. Vyberte nebo vytvořte jiným virtuálním Počítačem používat pro účely odstraňování potíží.

Když připojíte existující virtuální pevný disk, zadejte adresu URL na disk získaných v předchozím `Get-AzureRmVM` příkaz. Následující příklad připojí k řešení potíží virtuální počítač s názvem existujícího virtuálního pevného disku `myVMRecovery` ve skupině prostředků s názvem `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> Přidání disku vyžaduje, abyste zadejte velikost disku. Jak jsme připojit stávající disk, `-DiskSizeInGB` je zadán jako `$null`. Tato hodnota zajistí datový disk je správně připojený a bez nutnosti určit true velikost datový disk.


## <a name="mount-the-attached-data-disk"></a>Připojit disk připojená data

1. K řešení potíží virtuální počítač příslušná pověření pomocí protokolu RDP. Následující příklad soubory ke stažení souboru připojení RDP pro virtuální počítač s názvem `myVMRecovery` ve skupině prostředků s názvem `myResourceGroup`a stáhne jeho `C:\Users\ops\Documents`"

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Datový disk je automaticky zjistil a připojené. Zobrazení seznamu připojených svazků určit písmeno jednotky následujícím způsobem:

    ```powershell
    Get-Disk
    ```

    Následující příklad výstupu zobrazuje virtuální pevný disk připojený disk **2**. (Můžete také použít `Get-Volume` zobrazíte písmeno jednotky):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Vyřešte problémy na původní virtuální pevný disk
S existující virtuální pevný disk připojit teď můžete dělat žádné údržby a řešení potíží s kroky, podle potřeby. Jakmile vyřešíte problémy, pokračujte následujícími kroky.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odpojte Image a odpojit původní virtuální pevný disk
Jakmile jsou vaše chyby vyřešeny, odpojte Image a odpojit existující virtuální pevný disk z virtuálního počítače řešení potíží. Virtuální pevný disk s jiných virtuálních počítačů nelze používat, dokud vydání zapůjčení virtuální pevný disk se připojuje k řešení potíží virtuální počítač.

1. Z v rámci relace RDP, odpojte datový disk na váš virtuální počítač pro obnovení. Je třeba číslo disku z předchozí `Get-Disk` rutiny. Poté použijte `Set-Disk` nastavit jako v režimu offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Zkontrolujte disk je teď nastavená jako offline pomocí `Get-Disk` znovu. Následující příklad výstupu zobrazuje, že disk je teď nastavená jako offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Ukončete relaci protokolu RDP. Z relace prostředí Azure PowerShell odeberte virtuální pevný disk z virtuálního počítače, řešení potíží.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Vytvoření virtuálního počítače z původního pevného disku
Chcete-li vytvořit virtuální počítač z původní virtuální pevný disk, použijte [této šablony Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Skutečné šablona JSON je na následující odkaz:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json

Šablona nasadí virtuální počítač do existující virtuální síť pomocí adresy URL virtuálního pevného disku z dřívějších příkazu. Následující příklad nasadí šablony do skupiny prostředků s názvem `myResourceGroup`:

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

Odpovězte pokynů pro šablonu, například název virtuálního počítače, typ operačního systému a velikost virtuálního počítače. `osDiskVhdUri` Je stejný jako použil při připojení existujícího virtuálního pevného disku k řešení potíží virtuální počítač.


## <a name="re-enable-boot-diagnostics"></a>Opětovné povolení Diagnostika spouštění

Při vytváření virtuálního počítače z existujícího virtuálního pevného disku, nemusí být Diagnostika spouštění automaticky povolené. Následující příklad povolí diagnostiky rozšíření ve virtuálním počítači s názvem `myVMDeployed` ve skupině prostředků s názvem `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Další postup
Pokud máte problémy s připojením k virtuálnímu počítači, přečtěte si téma [připojení řešení potíží s RDP na virtuální počítač Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači najdete v tématu [problémů s připojením aplikace na virtuálním počítači Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Další informace o používání správce prostředků najdete v tématu [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
