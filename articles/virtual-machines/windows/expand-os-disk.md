---
title: Rozšíření jednotky operačního systému virtuálního počítače s Windows v Azure
description: Rozšiřte velikost jednotky operačního systému virtuálního počítače pomocí Azure Powershellu v modelu nasazení Správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: c76f57d15cd4cbdad5ded3b7545aab4d57272a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033499"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Postup rozšíření jednotky operačního systému virtuálního počítače

Když vytvoříte nový virtuální počítač (VM) ve skupině prostředků nasazením image z [Azure Marketplace](https://azure.microsoft.com/marketplace/), výchozí jednotka operačního systému je často 127 GB (některé bitové kopie mají ve výchozím nastavení menší velikosti disku operačního systému). I když je možné k virtuálnímu počítači přidat datové disky (počet závisí na zvolené skladové položce) a navíc se na tyto dodatečné disky doporučuje instalovat aplikace a úlohy náročné na procesor, zákazníci často potřebují rozšířit jednotku operačního systému pro zajištění podpory určitých scénářů, jako jsou například tyto:

- Podpora starších verzí aplikací, které instalují komponenty na jednotku operačního systému.
- Migrace fyzického nebo virtuálního počítače s větší jednotkou operačního systému z místního prostředí.


> [!IMPORTANT]
> Změna velikosti disku operačního systému virtuálního počítače Azure vyžaduje, aby se virtuální počítač naprostředkoval.
>
> Po rozšíření disků je třeba [rozšířit svazek v rámci operačního systému,](#expand-the-volume-within-the-os) abyste využili výhod většího disku.
> 


 


## <a name="resize-a-managed-disk"></a>Změna velikosti spravovaného disku

Otevřete integrované skriptovací prostředí (ISE) v prostředí PowerShell nebo okno PowerShellu v režimu správy a postupujte podle následujících kroků:

1. Přihlaste se ke svému účtu Microsoft Azure v režimu správy prostředků a vyberte předplatné takto:
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Nastavte název skupiny prostředků a název virtuálního počítače následujícím způsobem:
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Získejte odkaz na virtuální počítač následujícím způsobem:
   
   ```powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Před změnou velikosti disku zastavte virtuální počítač následujícím způsobem:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Získejte odkaz na spravovaný disk operačního systému. Nastavte velikost spravovaného disku operačního systému na požadovanou hodnotu a aktualizujte disk následujícím způsobem:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Nová velikost musí být větší než stávající velikost disku. Maximální povolená hodnota je 2048 GB pro disky operačního systému. (Je možné rozbalit objekt blob virtuálního pevného disku nad tuto velikost, ale operační systém bude moci pracovat pouze s prvním 2048 GB prostoru.)
   > 
   > 
6. Aktualizace virtuálního počítače může trvat několik sekund. Jakmile se dokončí provádění příkazu, restartujte virtuální počítač následujícím způsobem:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

A to je vše! Teď se pomocí RDP připojte k virtuálnímu počítači, otevřete Správu počítače (nebo Správu disků) a rozšiřte jednotku s použitím nově přiděleného místa.

## <a name="resize-an-unmanaged-disk"></a>Změna velikosti nespravovaného disku

Otevřete integrované skriptovací prostředí (ISE) v prostředí PowerShell nebo okno PowerShellu v režimu správy a postupujte podle následujících kroků:

1. Přihlaste se ke svému účtu Microsoft Azure v režimu správy prostředků a vyberte předplatné takto:
   
   ```Powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Nastavte název skupiny prostředků a název virtuálního počítače následujícím způsobem:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Získejte odkaz na virtuální počítač následujícím způsobem:
   
   ```Powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Před změnou velikosti disku zastavte virtuální počítač následujícím způsobem:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Nastavte velikost nespravovaného disku operačního systému na požadovanou hodnotu a aktualizujte virtuální modul takto:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Nová velikost musí být větší než stávající velikost disku. Maximální povolená hodnota je 2048 GB pro disky operačního systému. (Je možné rozbalit objekt blob virtuálního pevného disku nad tuto velikost, ale operační systém bude moci pracovat pouze s prvním 2048 GB prostoru.)
   > 
   > 
   
6. Aktualizace virtuálního počítače může trvat několik sekund. Jakmile se dokončí provádění příkazu, restartujte virtuální počítač následujícím způsobem:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Skripty pro disk operačního systému

Níže je uveden úplný skript pro váš odkaz na spravované i nespravované disky:


**Spravované disky**

```Powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Nespravované disky**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Změna velikosti datových disků

Tento článek je zaměřen především na rozšíření disku operačního systému virtuálního počítače, ale skript lze také použít pro rozšíření datové disky připojené k virtuálnímu počítači. Pokud například chcete rozšířit datový disk připojený k virtuálnímu počítači, nahraďte objekt `OSDisk` v části `StorageProfile` polem `DataDisks` a použijte číselný index k získání odkazu na první připojený datový disk, jak je znázorněno níže:

**Spravovaný disk**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Nespravovaný disk**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Podobně můžete odkazovat na jiné datové disky připojené k virtuálnímu počítače, a to buď pomocí indexu, jak je uvedeno výše, nebo **name** vlastnost disku:


**Spravovaný disk**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Nespravovaný disk**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Rozšíření svazku v rámci operačního esu

Po rozšíření disku pro virtuální počítače, budete muset přejít do operačního systému a rozšířit svazek tak, aby zahrnoval a nové místo. Existuje několik metod pro rozbalení oddílu. Tato část popisuje připojení virtuálního počítače pomocí připojení RDP k rozšíření oddílu pomocí **programu DiskPart**.

1. Otevřete připojení RDP k virtuálnímu počítači.

2.  Otevřete příkazový řádek a zadejte **diskpart**.

2.  Na výzvu **DISKPART** zadejte . `list volume` Poznamenejte si svazek, který chcete rozšířit.

3.  Na výzvu **DISKPART** zadejte . `select volume <volumenumber>` Tím vyberete *číslo svazku* svazku, které chcete rozšířit do souvislého prázdného místa na stejném disku.

4.  Na výzvu **DISKPART** zadejte . `extend [size=<size>]` Tím se vybraný svazek rozšíří o *velikost* v megabajtech (MB).


## <a name="next-steps"></a>Další kroky

Disky můžete připojit také pomocí [portálu Azure](attach-managed-disk-portal.md).
