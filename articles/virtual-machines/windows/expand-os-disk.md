---
title: Rozšířit jednotku operačního systému virtuálního počítače s Windows v Azure | Dokumentace Microsoftu
description: Zvětšení velikosti jednotky operačního systému virtuálního počítače pomocí Azure Powershellu v modelu nasazení Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.openlocfilehash: f7ff1f2f27806c92c1ac887ce3c3343b96339745
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888945"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Tom, jak rozšířit jednotku operačního systému virtuálního počítače

Když vytvoříte nový virtuální počítač (VM) ve skupině prostředků nasazením image z [Azure Marketplace](https://azure.microsoft.com/marketplace/), výchozí jednotka operačního systému je často 127 GB (některé obrázky mívají menší velikost disku operačního systému ve výchozím nastavení). I když je možné k virtuálnímu počítači přidat datové disky (počet závisí na zvolené skladové položce) a navíc se na tyto dodatečné disky doporučuje instalovat aplikace a úlohy náročné na procesor, zákazníci často potřebují rozšířit jednotku operačního systému pro zajištění podpory určitých scénářů, jako jsou například tyto:

- Podpora starších verzí aplikací, které instalují komponenty na jednotku operačního systému.
- Migrace fyzického nebo virtuálního počítače s větší jednotkou operačního systému z místního prostředí.


> [!IMPORTANT]
> Změna velikosti disku s operačním systémem virtuálnímu počítači Azure způsobí, že k restartování.
>
> Po rozbalení disky, budete muset [rozšíření svazku v rámci operačního systému](#expand-the-volume-within-the-os) využívat větší disk.
> 

## <a name="resize-a-managed-disk"></a>Změna velikosti spravovaného disku

Otevřete integrované skriptovací prostředí (ISE) v prostředí PowerShell nebo okno PowerShellu v režimu správy a postupujte podle následujících kroků:

1. Přihlaste se ke svému účtu Microsoft Azure v režimu správy prostředků a vyberte své předplatné takto:
   
   ```powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Nastavte název skupiny prostředků a název virtuálního počítače následujícím způsobem:
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Získejte odkaz na virtuální počítač následujícím způsobem:
   
   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Před změnou velikosti disku zastavte virtuální počítač následujícím způsobem:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Získáte odkaz na spravovaný disk s operačním systémem. Nastavte velikost spravovaného disku s operačním systémem na požadovanou hodnotu a aktualizujte Disk následujícím způsobem:
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Nová velikost musí být větší než stávající velikost disku. Maximální povolený počet je 2 048 GB pro disky s operačním systémem. (Je možné rozšířit nad rámec této velikosti objektu blob VHD, ale pouze operační systém bude moct pracovat s prvních 2 048 GB místa.)
   > 
   > 
6. Aktualizace virtuálního počítače může trvat několik sekund. Jakmile se dokončí provádění příkazu, restartujte virtuální počítač následujícím způsobem:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

A to je vše! Teď se pomocí RDP připojte k virtuálnímu počítači, otevřete Správu počítače (nebo Správu disků) a rozšiřte jednotku s použitím nově přiděleného místa.

## <a name="resize-an-unmanaged-disk"></a>Změna velikosti nespravovaný disk

Otevřete integrované skriptovací prostředí (ISE) v prostředí PowerShell nebo okno PowerShellu v režimu správy a postupujte podle následujících kroků:

1. Přihlaste se ke svému účtu Microsoft Azure v režimu správy prostředků a vyberte své předplatné takto:
   
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
5. Nastavte velikost nespravovaného disku s operačním systémem na požadovanou hodnotu a aktualizujte virtuální počítač následujícím způsobem:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Nová velikost musí být větší než stávající velikost disku. Maximální povolený počet je 2 048 GB pro disky s operačním systémem. (Je možné rozšířit nad rámec této velikosti objektu blob VHD, ale pouze operační systém bude moct pracovat s prvních 2 048 GB místa.)
   > 
   > 
   
6. Aktualizace virtuálního počítače může trvat několik sekund. Jakmile se dokončí provádění příkazu, restartujte virtuální počítač následujícím způsobem:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Skripty pro disk s operačním systémem

Níže je celý skript pro vaši informaci pro spravované i nespravované disky:


**Spravované disky**

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

**Nespravované disky**

```powershell
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

## <a name="resizing-data-disks"></a>Změna velikosti datových disků

Tento článek se zaměřuje hlavně na rozšíření disku s operačním systémem virtuálního počítače, ale skript lze také použít pro rozšíření datových disků připojených k virtuálnímu počítači. Pokud například chcete rozšířit datový disk připojený k virtuálnímu počítači, nahraďte objekt `OSDisk` v části `StorageProfile` polem `DataDisks` a použijte číselný index k získání odkazu na první připojený datový disk, jak je znázorněno níže:

**Spravovaný disk**

```powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Nespravovaný disk**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Podobně mohou odkazovat další datové disky připojené k virtuálnímu počítači, buď pomocí indexu, jak je znázorněno výše nebo **název** vlastnost disku:


**Spravovaný disk**

```powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Nespravovaný disk**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Rozšíření svazku v rámci operačního systému

Po rozbalení tento disk pro virtuální počítač, budete muset přejít do operačního systému a rozšíření svazku zahrnuje nové místo. Existuje několik metod pro rozšíření oddílu. Tato část se věnuje připojení virtuálního počítače pomocí připojení ke vzdálené ploše rozbalte oddíl pomocí **DiskPart**.

1. Otevřete připojení RDP k virtuálnímu počítači.

2.  Otevřete příkazový řádek a zadejte **diskpart**.

2.  Na **DISKPART** zadejte `list volume`. Poznamenejte si na svazek, který chcete rozšířit.

3.  Na **DISKPART** zadejte `select volume <volumenumber>`. Tato možnost vybere svazku *volumenumber* , který chcete rozšířit do souvislé, prázdného prostoru na stejném disku.

4.  Na **DISKPART** zadejte `extend [size=<size>]`. Tato zásada rozšiřuje vybraný svazek *velikost* v megabajtech (MB).


##<a name="next-steps"></a>Další postup

Můžete také připojit pomocí disků [webu Azure portal](attach-managed-disk-portal.md).



