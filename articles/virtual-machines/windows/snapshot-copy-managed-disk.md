---
title: Vytvoření snímku virtuálního pevného disku pomocí portálu nebo PowerShellu
description: Naučte se, jak vytvořit kopii virtuálního počítače Azure, který se použije jako záložní nebo pro řešení potíží pomocí portálu nebo PowerShellu.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/08/2018
ms.author: rogarana
ms.openlocfilehash: 9070b69ac4c6b85791ff3dd4662273e75a3cd22c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556056"
---
# <a name="create-a-snapshot-using-the-portal-or-powershell"></a>Vytvoření snímku pomocí portálu nebo PowerShellu

Snímek je plná kopie virtuálního pevného disku jen pro čtení (VHD). Můžete pořídit snímek virtuálního pevného disku s operačním systémem nebo datovým diskem, který se má použít jako záloha, nebo řešit problémy s virtuálním počítačem (VM).

Pokud se chystáte použít snímek k vytvoření nového virtuálního počítače, doporučujeme před pořizováním snímku vyčistit virtuální počítač a odstranit tak všechny procesy, které probíhají.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal 

Chcete-li vytvořit snímek, proveďte následující kroky: 
1.  V [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**.
2. Vyhledejte a vyberte **snímek**.
3. V okně **snímku** vyberte **vytvořit**. Zobrazí se okno **vytvořit snímek** .
4. Zadejte **název** snímku.
5. Vyberte existující [skupinu prostředků](../../azure-resource-manager/management/overview.md#resource-groups) nebo zadejte název nového. 
6. Vyberte **umístění** datového centra Azure.  
7. Pro **zdrojový disk** vyberte spravovaný disk, který se má snímek.
8. Vyberte **typ účtu** , který chcete použít k uložení snímku. Vyberte **Standard_HDD**, pokud nepotřebujete, aby byl snímek uložen na disku s vysokým výkonem.
9. Vyberte **Vytvořit**.

## <a name="use-powershell"></a>Použití prostředí PowerShell

Následující kroky ukazují, jak zkopírovat disk VHD a vytvořit konfiguraci snímku. Pak můžete pořídit snímek disku pomocí rutiny [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) . 

 

1. Nastavte některé parametry: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Získat virtuální počítač:

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

3. Vytvořte konfiguraci snímku. V tomto příkladu je snímek disk s operačním systémem:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```
   
   > [!NOTE]
   > Pokud chcete snímek Uložit do odolného úložiště s využitím zóny, vytvořte ho v oblasti, která podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) , a tento `-SkuName Standard_ZRS` parametr přidejte.   
   
4. Pořídit snímek:

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Další kroky

Vytvořte virtuální počítač ze snímku vytvořením spravovaného disku ze snímku a připojením nového spravovaného disku jako disku s operačním systémem. Další informace najdete v ukázce v tématu [Vytvoření virtuálního počítače ze snímku pomocí PowerShellu](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot).