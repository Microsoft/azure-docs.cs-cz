---
title: Vytvoření snímku virtuálního pevného disku v Azure
description: Zjistěte, jak vytvořit kopii virtuálního počítače Azure, která se použije jako záloha nebo pro řešení problémů.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: bc74a3eea1f99de6080788d6f3fddcac823092dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75370897"
---
# <a name="create-a-snapshot"></a>Vytvoření snímku

Snímek je úplná kopie virtuálního pevného disku (VHD) jen pro čtení. Můžete pořizovat snímek virtuálního pevného disku nebo datového disku, který chcete použít jako zálohu nebo k řešení problémů s virtuálním počítačem.

Pokud budete použít snímek k vytvoření nového virtuálního počítače, doporučujeme, abyste před pořízením snímku virtuální ho vyčistili, abyste vyčistili všechny probíhající procesy.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal 

Chcete-li vytvořit snímek, proveďte následující kroky: 
1.  Na [webu Azure Portal](https://portal.azure.com)vyberte **Vytvořit prostředek**.
2. Vyhledejte a vyberte **snímek**.
3. V okně **Snímek** vyberte **Vytvořit**. Zobrazí se okno **Vytvořit snímek.**
4. Zadejte **název** snímku.
5. Vyberte existující [skupinu prostředků](../../azure-resource-manager/management/overview.md#resource-groups) nebo zadejte název nové skupiny. 
6. Vyberte **umístění** datového centra Azure.  
7. V **části Zdrojový disk**vyberte spravovaný disk, který chcete snímek.
8. Vyberte **typ účtu,** který chcete použít k uložení snímku. Pokud **nepotřebujete,** aby byl snímek uložen na vysoce výkonném disku, vyberte Standard_HDD .
9. Vyberte **Vytvořit**.

## <a name="use-powershell"></a>Použití prostředí PowerShell

Následující kroky ukazují, jak zkopírovat disk virtuálního pevného disku a vytvořit konfiguraci snímku. Potom můžete pořizovat snímek disku pomocí rutiny [New-AzSnapshot.](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) 

 

1. Nastavte některé parametry: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Získejte virtuální virtuální měnový virtuální mísu:

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. Vytvořte konfiguraci snímku. V tomto příkladu je snímek disku operačního systému:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Pokud chcete uložit snímek do úložiště odolného proti zónám, vytvořte jej `-SkuName Standard_ZRS` v oblasti, která podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) a zahrnuje parametr.   
   
4. Pořízení snímku:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Další kroky

Vytvořte virtuální počítač ze snímku vytvořením spravovaného disku ze snímku a připojením nového spravovaného disku jako disku operačního systému. Další informace najdete v tématu [ukázka vytvoření virtuálního počítače ze snímku s PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
