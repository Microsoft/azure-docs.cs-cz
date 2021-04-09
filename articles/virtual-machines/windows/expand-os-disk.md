---
title: Rozšíření jednotky operačního systému Windows na virtuálním počítači v Azure
description: Zvětšete velikost jednotky operačního systému virtuálního počítače pomocí Azure PowerShell v modelu nasazení Správce prostředků.
services: virtual-machines
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: af57bc396349286c3edcaefc8385ddbaec066226
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199471"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Postup rozšíření jednotky operačního systému virtuálního počítače

Při vytváření nového virtuálního počítače ve skupině prostředků nasazením image z [Azure Marketplace](https://azure.microsoft.com/marketplace/)je výchozí jednotka operačního systému často 127 GB (některé image mají ve výchozím nastavení menší velikosti disků operačního systému). I když je možné přidat datové disky k virtuálnímu počítači (Toto číslo závisí na SKU, které jste zvolili), a doporučujeme na těchto discích doplňku instalovat aplikace a úlohy náročné na procesor, často si zákazníci musí rozšířit jednotku operačního systému na podporu konkrétních scénářů:

- Pro podporu starších verzí aplikací, které instalují součásti na jednotku operačního systému.
- Migrace fyzického počítače nebo virtuálního počítače z místního počítače na větší jednotku operačního systému.

> [!IMPORTANT]
> Změna velikosti operačního systému nebo datového disku virtuálního počítače Azure vyžaduje, aby se virtuální počítač nadělil.
>
> Zmenšení stávajícího disku se nepodporuje a může potenciálně vést ke ztrátě dat.
> 
> Po rozbalení disků je potřeba [rozšířit svazek v operačním systému](#expand-the-volume-within-the-os) a využít tak větší disk.

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>Změna velikosti spravovaného disku v Azure Portal

1. V [Azure Portal](https://portal.azure.com)přejdete do virtuálního počítače, ve kterém chcete disk rozbalit. Kliknutím na tlačítko **zastavit zrušíte** přidělení virtuálního počítače.
2. Po zastavení virtuálního počítače v nabídce vlevo v části **Nastavení** vyberte **disky**.

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="Snímek obrazovky, který zobrazuje možnost disky vybrané v části nastavení v nabídce":::

 
3. V části **název disku** vyberte disk, u kterého chcete změnit velikost.

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="Snímek obrazovky, který zobrazuje podokno disky s vybraným názvem disku":::

4. V nabídce vlevo v části **Nastavení** vyberte **Velikost + výkon**.

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="Snímek obrazovky, který zobrazuje možnost velikost a výkon zvolenou v části nastavení v nabídce":::

5. V části **Velikost + výkon** vyberte velikost disku, kterou chcete.
   
   > [!WARNING]
   > Nová velikost musí být větší než stávající velikost disku. Maximální povolená hodnota je 2 048 GB pro disky s operačním systémem. (Objekt BLOB VHD můžete rozšířit i po této velikosti, ale operační systém funguje jenom s prvním 2 048 GB místa.)
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="Snímek obrazovky, který zobrazuje podokno velikost a výkon s vybranou velikostí disku.":::

6. V dolní části stránky vyberte **změnit velikost** .

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="Snímek obrazovky, který zobrazuje podokno velikost a výkon s vybraným tlačítkem pro změnu velikosti":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>Změna velikosti spravovaného disku pomocí prostředí PowerShell

Otevřete okno PowerShell ISE nebo PowerShellu v režimu správy a postupujte podle následujících kroků:

1. Přihlaste se ke svému účtu Microsoft Azure v režimu správy prostředků a vyberte své předplatné:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Nastavte název skupiny prostředků a název virtuálního počítače:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Získejte odkaz na váš virtuální počítač:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Před změnou velikosti disku zastavte virtuální počítač:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Získejte odkaz na spravovaný disk s operačním systémem. Nastavte velikost spravovaného disku s operačním systémem na požadovanou hodnotu a aktualizujte disk:
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > Nová velikost musí být větší než stávající velikost disku. Maximální povolená hodnota je 2 048 GB pro disky s operačním systémem. (Objekt BLOB VHD můžete rozšířit i po této velikosti, ale operační systém funguje jenom s prvním 2 048 GB místa.)
    > 
         
6. Aktualizace virtuálního počítače může trvat několik sekund. Po dokončení provádění příkazu restartujte virtuální počítač:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

A je to hotovo. Teď se pomocí RDP připojte k virtuálnímu počítači, otevřete Správu počítače (nebo Správu disků) a rozšiřte jednotku s použitím nově přiděleného místa.

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>Změna velikosti nespravovaného disku pomocí PowerShellu

Otevřete okno PowerShell ISE nebo PowerShellu v režimu správy a postupujte podle následujících kroků:

1. Přihlaste se ke svému účtu Microsoft Azure v režimu správy prostředků a vyberte své předplatné:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Nastavte název skupiny prostředků a názvy virtuálních počítačů:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Získejte odkaz na váš virtuální počítač:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Před změnou velikosti disku zastavte virtuální počítač:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Nastavte velikost nespravovaného disku s operačním systémem na požadovanou hodnotu a aktualizujte virtuální počítač:
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > Nová velikost musí být větší než stávající velikost disku. Maximální povolená hodnota je 2 048 GB pro disky s operačním systémem. (Objekt BLOB VHD můžete rozšířit i po této velikosti, ale operační systém bude moct pracovat jenom s prvním 2 048 GB místa.)
    > 
    > 
   
6. Aktualizace virtuálního počítače může trvat několik sekund. Po dokončení provádění příkazu restartujte virtuální počítač:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>Skripty pro disk s operačním systémem

Níže je uvedený úplný skript pro váš odkaz na spravované i nespravované disky:


**Spravované disky**

```powershell
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

Tento článek se zaměřuje hlavně na rozšíření disku operačního systému virtuálního počítače, ale skript se dá použít i k rozšíření datových disků připojených k virtuálnímu počítači. Pokud například chcete rozšířit datový disk připojený k virtuálnímu počítači, nahraďte objekt `OSDisk` v části `StorageProfile` polem `DataDisks` a použijte číselný index k získání odkazu na první připojený datový disk, jak je znázorněno níže:

**Spravovaný disk**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

**Nespravovaný disk**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```

Podobně můžete odkazovat na jiné datové disky připojené k virtuálnímu počítači, a to buď pomocí indexu, jak je uvedeno výše, nebo vlastnosti **název** disku:


**Spravovaný disk**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Nespravovaný disk**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Rozšířit svazek v operačním systému

Po rozbalení disku pro virtuální počítač musíte přejít do operačního systému a rozšířit svazek tak, aby zahrnoval nové místo. Existuje několik způsobů, jak rozšířit oddíl. Tato část popisuje připojení virtuálního počítače pomocí připojení RDP, které umožňuje rozšířit oddíl pomocí **nástroje DiskPart**.

1. Otevřete připojení RDP k vašemu VIRTUÁLNÍmu počítači.

2. Otevřete příkazový řádek a zadejte příkaz **DiskPart**.

3. Do příkazového řádku **DiskPart** zadejte `list volume` . Poznamenejte si svazek, který chcete zvětšit.

4. Do příkazového řádku **DiskPart** zadejte `select volume <volumenumber>` . Tím se vybere *volumenumber* svazku, který chcete v jednom disku zvětšit do souvislého prázdného místa.

5. Do příkazového řádku **DiskPart** zadejte `extend [size=<size>]` . Tím se rozšíří vybraný svazek o *Velikost* v megabajtech (MB).


## <a name="next-steps"></a>Další kroky

Disky můžete připojit také pomocí [Azure Portal](attach-managed-disk-portal.md).
