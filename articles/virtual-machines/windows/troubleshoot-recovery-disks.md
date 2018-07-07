---
title: Použití Windows řešení potíží s virtuálních počítačů pomocí Azure Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak řešit potíže virtuální počítač Windows v Azure s připojením disku s operačním systémem k obnovení virtuálního počítače pomocí Azure Powershellu
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
ms.openlocfilehash: 1e87704e7d8cf3c7cc21e537d36f95a97265061b
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903512"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Odstranění virtuálního počítače s Windows pomocí připojení disku s operačním systémem k obnovení virtuálního počítače pomocí Azure Powershellu
Pokud váš Windows virtuální počítač (VM) v Azure dojde k chybě spouštění nebo disku, budete muset provést postup řešení potíží na samotném virtuálním pevném disku. Běžným příkladem může být aktualizace selhání aplikace, která brání virtuálního počítače moci úspěšně spustil. Tento článek podrobně popisuje, jak připojit virtuální pevný disk k jinému virtuálnímu počítači Windows opravte všechny chyby a pak znovu vytvořit původní virtuální počítač pomocí Azure Powershellu.


## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Proces řešení potíží je následující:

1. Odstranění virtuálního počítače k chybám, zachování virtuálních pevných disků.
2. Připojení a připojte virtuální pevný disk k jinému virtuálnímu počítači s Windows pro účely odstraňování potíží.
3. Připojení k virtuálnímu počítači pro řešení potíží. Upravení souborů nebo spuštění všech nástrojů k opravě potíží na původním virtuálním pevném disku.
4. Odpojení virtuálního pevného disku od virtuálního počítače pro řešení potíží.
5. Vytvoření virtuálního počítače s použitím původního virtuálního pevného disku.

Pro virtuální počítač, který používá spravovaný disk, najdete v článku [vyřešit spravovaný virtuální počítač Disk připojit nový disk s operačním systémem](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).


Ujistěte se, že máte [nejnovější Azure PowerShell](/powershell/azure/overview) nainstalovaný a přihlášení k vašemu předplatnému:

```powershell
Connect-AzureRmAccount
```

V následujících příkladech nahraďte názvy parametrů s vlastními hodnotami. Zahrnout názvy parametrů příklad `myResourceGroup`, `mystorageaccount`, a `myVM`.


## <a name="determine-boot-issues"></a>Určete spouštěcí problémy
Snímek obrazovky virtuálního počítače můžete zobrazit v Azure vám pomůže vyřešit problémy. Tento snímek obrazovky může pomoci určit, proč se virtuální počítač nepovede spustit. Následující příklad získá na snímku obrazovky z virtuálního počítače Windows s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Projděte si snímek obrazovky, chcete-li zjistit, proč tento virtuální počítač se nedaří spustit. Poznámka: všechny specifické chybové zprávy nebo kódy chyb, které jsou k dispozici.


## <a name="view-existing-virtual-hard-disk-details"></a>Zobrazit podrobnosti o stávající virtuální pevný disk
Než budete moct připojit virtuální pevný disk k jinému virtuálnímu počítači, musíte určit název virtuálního pevného disku (VHD).

Následující příklad získá informace o virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Vyhledejte `Vhd URI` v rámci `StorageProfile` část ve výstupu předchozího příkazu. Zkrácen následující příklad výstupu ukazuje `Vhd URI` na konci bloku kódu:

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


## <a name="delete-existing-vm"></a>Odstranění existujícího virtuálního počítače
Virtuální pevné disky a virtuální počítače jsou v Azure dva různé prostředky. Virtuální pevný disk je, kde jsou uloženy samotného operačního systému, aplikací a konfigurace. Virtuální počítač je jenom metadata, která definují velikost nebo umístění a odkazuje na prostředky, jako jsou virtuální pevný disk nebo virtuální síťová karta (NIC). Každý virtuální pevný disk má při připojení k virtuálnímu počítači přiřadí zapůjčení. Přestože datové disky je možné připojovat a odpojovat dokonce i za běhu virtuálního počítače, disk s operačním systémem není možné odpojit, dokud se neodstraní prostředek virtuálního počítače. Zapůjčení se nadále přidružuje disk s operačním systémem v případě virtuálních počítačů i v případě, že je virtuální počítač v zastaveném a uvolněném stavu.

Prvním krokem k obnovení vašeho virtuálního počítače je odstranění samotného prostředku virtuálního počítače. Když odstraníte virtuální počítač, virtuální pevné disky zůstanou ve vašem účtu úložiště. Po odstranění virtuálního počítače, připojit virtuální pevný disk k jinému virtuálnímu počítači odstraňovat potíže a řešit chyby.

Následující příklad odstraní virtuální počítač s názvem `myVM` ze skupiny prostředků s názvem `myResourceGroup`:

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Počkejte, dokud virtuální počítač má bylo dokončeno odstraňování než připojit virtuální pevný disk k jinému virtuálnímu počítači. Zapůjčený virtuální pevný disk, který se přidruží k němu virtuální počítač je potřeba uvolnit, než budete moct připojit virtuální pevný disk k jinému virtuálnímu počítači.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Připojit existující virtuální pevný disk k jinému virtuálnímu počítači
Pro několik dalších kroků použijte jiný virtuální počítač pro účely odstraňování potíží. Připojit existující virtuální pevný disk k tomuto řešení problémů s virtuálnímu počítači na Procházet a upravovat obsah na disku. Tento proces umožňuje opravte případné chyby v konfiguraci, případně si můžete přečíst další aplikace nebo systému souborů protokolu, např. Zvolte nebo vytvořte jiný virtuální počítač určený pro účely odstraňování potíží.

Po připojení existujícího virtuálního pevného disku, zadejte adresu URL na disk, kterou jste získali v předchozím `Get-AzureRmVM` příkazu. Následující příklad připojí k řešení potíží virtuální počítač s názvem existujícího virtuálního pevného disku `myVMRecovery` ve skupině prostředků s názvem `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> Přidání disku vyžaduje, abyste k určení velikosti disku. Jak jsme připojit stávající disk, `-DiskSizeInGB` je zadán jako `$null`. Tato hodnota zajišťuje, že je správně připojený datový disk a aniž by bylo nutné určit skutečnou velikost datového disku.


## <a name="mount-the-attached-data-disk"></a>Připojte přídavný datový disk

1. Připojení RDP k řešení potíží virtuální počítač pomocí příslušných přihlašovacích údajů. Následující příklad stáhne soubor připojení RDP pro virtuální počítač s názvem `myVMRecovery` ve skupině prostředků s názvem `myResourceGroup`a soubory ke stažení na `C:\Users\ops\Documents`"

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Datový disk je automaticky zjistil a připojené. Zobrazení seznamu připojených svazků určit písmeno jednotky následujícím způsobem:

    ```powershell
    Get-Disk
    ```

    Následující příklad výstupu ukazuje virtuální pevný disk připojený disk **2**. (Můžete také použít `Get-Volume` zobrazíte písmeno jednotky):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Oprava problémů na původním virtuálním pevném disku
Pomocí existující virtuální pevný disk připojený můžete teď provádět údržbu a řešení potíží s kroky, podle potřeby. Jakmile vyřešíte problémy, pokračujte následujícími kroky.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odpojení původního virtuálního pevného disku
Po vyřešení chyby odpojte Image a odpojit existující virtuální pevný disk z vašeho řešení potíží virtuálního počítače. Virtuální pevný disk s jakýkoli jiný virtuální počítač nelze použít, dokud se neuvolní zapůjčení virtuální pevný disk se připojuje k řešení potíží virtuální počítač.

1. Z v rámci relace protokolu RDP, odpojte datový disk na váš virtuální počítač pro obnovení. Je třeba číslo disku z předchozího `Get-Disk` rutiny. Potom použijte `Set-Disk` nastavit jako v režimu offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Potvrzení na disku je nyní nastaven jako offline pomocí `Get-Disk` znovu. Následující příklad výstupu ukazuje, že disk je nyní nastaven jako offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Ukončete relaci RDP. Z relace prostředí Azure PowerShell odeberte virtuální pevný disk z virtuálního počítače pro řešení potíží.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Vytvoření virtuálního počítače z původního pevného disku
Vytvoření virtuálního počítače z původního virtuálního pevného disku, použijte [tuto šablonu Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Skutečnou šablonu JSON je na následující odkaz:

- https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json

Šablona nasadí virtuální počítač do existující virtuální sítě pomocí adresy URL virtuálního pevného disku z předchozích příkazu. Následující příklad nasazuje šablony do skupiny prostředků s názvem `myResourceGroup`:

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

Do zobrazených výzev pro šablony, jako je například název virtuálního počítače, typ operačního systému a velikosti virtuálního počítače. `osDiskVhdUri` Je stejné jako dříve použít při připojení existujícího virtuálního pevného disku k virtuálnímu počítači pro řešení potíží.


## <a name="re-enable-boot-diagnostics"></a>Opětovné povolení diagnostiky spouštění

Při vytváření virtuálního počítače z existujícího virtuálního pevného disku, nemusí Diagnostika spouštění automaticky povolená. Následující příklad povolí diagnostické rozšíření na virtuální počítač s názvem `myVMDeployed` ve skupině prostředků s názvem `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Řešení potíží s spravovaný virtuální počítač Disk připojením nový disk operačního systému
1. Zastavte dotčených spravovaného disku Windows virtuální počítač.
2. [Vytvoření snímku spravovaného disku](snapshot-copy-managed-disk.md) z disku s operačním systémem virtuálního počítače spravovaného disku.
3. [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Připojení spravovaného disku jako datového disku virtuálního počítače](attach-disk-ps.md).
5. [Změňte datový disk na disk s operačním systémem z kroku 4](os-disk-swap.md).

## <a name="next-steps"></a>Další postup
Pokud máte problémy s připojením k virtuálnímu počítači, přečtěte si téma [připojení řešení potíží s RDP k virtuálnímu počítači Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači, naleznete v tématu [řešit problémy s připojením aplikace na virtuálním počítači s Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Další informace o použití Resource Manageru najdete v tématu [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
