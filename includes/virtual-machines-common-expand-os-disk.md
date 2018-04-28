## <a name="overview"></a>Přehled
Při vytváření nového virtuálního počítače (VM) ve skupině prostředků nasazením bitové kopie z [Azure Marketplace](https://azure.microsoft.com/marketplace/), výchozí jednotce operačního systému je často 127 GB (některé obrázky, mívají menší velikost disku operačního systému ve výchozím nastavení). I když je možné k virtuálnímu počítači přidat datové disky (počet závisí na zvolené skladové položce) a navíc se na tyto dodatečné disky doporučuje instalovat aplikace a úlohy náročné na procesor, zákazníci často potřebují rozšířit jednotku operačního systému pro zajištění podpory určitých scénářů, jako jsou například tyto:

1. Podpora starších verzí aplikací, které instalují komponenty na jednotku operačního systému.
2. Migrace fyzického nebo virtuálního počítače s větší jednotkou operačního systému z místního prostředí.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: Resource Manager a Classic. Tento článek se týká použití modelu Resource Manager. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> 
> 
> [!WARNING]
> Změna velikosti disku operačního systému virtuálním počítači Azure způsobí, že k restartování.
>

## <a name="resize-the-os-drive"></a>Změna velikosti jednotky operačního systému
V tomto článku provedeme změnu velikosti jednotky operačního systému pomocí modulů Resource Manageru v [Azure PowerShellu](/powershell/azureps-cmdlets-docs). Ukážeme Změna velikosti disku operačního systému pro Unamanged a spravované disky vzhledem k tomu, že přístup ke změně velikosti disků, na které se liší oba typy disku.

### <a name="for-resizing-unmanaged-disks"></a>Pro změnu velikosti nespravované disky:

Otevřete integrované skriptovací prostředí (ISE) v prostředí PowerShell nebo okno PowerShellu v režimu správy a postupujte podle následujících kroků:

1. Přihlaste se ke svému účtu Microsoft Azure v režimu správy prostředků a následujícím způsobem vyberte své předplatné:
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Nastavte název skupiny prostředků a název virtuálního počítače následujícím způsobem:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Získejte odkaz na virtuální počítač následujícím způsobem:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Před změnou velikosti disku zastavte virtuální počítač následujícím způsobem:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. A tady přichází moment, na který jsme čekali! Nastavit velikost disku nespravované operačního systému na požadovanou hodnotu a aktualizovat virtuální počítač následujícím způsobem:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Nová velikost musí být větší než stávající velikost disku. Maximální povolený počet je 2048 GB pro disky operačního systému. (Je možné rozšířit nad rámec této velikosti objektu blob virtuálního pevného disku, ale pouze operačního systému bude moct pracovat s první 2048 GB místa.)
   > 
   > 
6. Aktualizace virtuálního počítače může trvat několik sekund. Jakmile se dokončí provádění příkazu, restartujte virtuální počítač následujícím způsobem:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

### <a name="for-resizing-managed-disks"></a>Pro změnu velikosti disků spravovat:

Otevřete integrované skriptovací prostředí (ISE) v prostředí PowerShell nebo okno PowerShellu v režimu správy a postupujte podle následujících kroků:

1. Přihlaste se ke svému účtu Microsoft Azure v režimu správy prostředků a následujícím způsobem vyberte své předplatné:
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Nastavte název skupiny prostředků a název virtuálního počítače následujícím způsobem:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Získejte odkaz na virtuální počítač následujícím způsobem:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Před změnou velikosti disku zastavte virtuální počítač následujícím způsobem:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Získejte odkaz na disk spravovaný operačního systému. Nastavit velikost disku spravované operačního systému na požadovanou hodnotu a aktualizovat Disk následujícím způsobem:
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Nová velikost musí být větší než stávající velikost disku. Maximální povolený počet je 2048 GB pro disky operačního systému. (Je možné rozšířit nad rámec této velikosti objektu blob virtuálního pevného disku, ale pouze operačního systému bude moct pracovat s první 2048 GB místa.)
   > 
   > 
6. Aktualizace virtuálního počítače může trvat několik sekund. Jakmile se dokončí provádění příkazu, restartujte virtuální počítač následujícím způsobem:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

A to je vše! Teď se pomocí RDP připojte k virtuálnímu počítači, otevřete Správu počítače (nebo Správu disků) a rozšiřte jednotku s použitím nově přiděleného místa.

## <a name="summary"></a>Souhrn
V tomto článku jsme pomocí modulů Azure Resource Manageru v PowerShellu rozšířili jednotku operačního systému virtuálního počítače IaaS. Opakuje níže je celý skript pro vaši informaci pro nespravované a spravované disky:

Unamanged disky:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
Managed Disks:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRMVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="next-steps"></a>Další kroky
Když v tomto článku jsme zaměřuje především na rozšiřování Unamanged/spravované OS disku virtuálního počítače, může vyvinuté skriptu použít také pro rozšíření datových disků připojených k virtuálnímu počítači. Pokud například chcete rozšířit datový disk připojený k virtuálnímu počítači, nahraďte objekt ```OSDisk``` v části ```StorageProfile``` polem ```DataDisks``` a použijte číselný index k získání odkazu na první připojený datový disk, jak je znázorněno níže:

Unamanged Disk:
```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Spravovaný Disk:
```Powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

Podobným způsobem můžete odkazovat na další datové disky připojené k virtuálnímu počítači, buď pomocí výše uvedeného indexu, nebo pomocí vlastnosti ```Name``` disku, jak je znázorněno níže:

Unamanged Disk:
```Powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```
Spravovat Disk:
```Powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

Informace o připojování disků k virtuálnímu počítači Azure Resource Manageru najdete v tomto [článku](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

