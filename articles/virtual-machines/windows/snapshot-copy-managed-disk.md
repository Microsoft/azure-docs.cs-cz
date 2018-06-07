---
title: Vytvořte snímek virtuálního pevného disku v Azure | Microsoft Docs
description: Naučte se vytvořit kopii virtuálního počítače Azure používat jako zpět nahoru nebo řešení potíží.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 7d45fd749fea4036d944d740541d8b8607553835
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658151"
---
# <a name="create-a-snapshot"></a>Vytvoření snímku

Pořízení snímku operačním systémem nebo datovými disk, který vystavuje virtuálního pevného disku pro zálohování a řešení souvisejících problémů virtuálních počítačů. Snímek je úplná, jen pro čtení kopie virtuálního pevného disku. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Pořízení snímku pomocí portálu Azure 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Spouštění v levém horním, klikněte na tlačítko **vytvořit prostředek** a vyhledejte **snímku**.
3. V okně snímku, klikněte na tlačítko **vytvořit**.
4. Zadejte **název** pro snímku.
5. Vyberte existující [skupinu prostředků](../../azure-resource-manager/resource-group-overview.md#resource-groups) nebo zadejte název nové skupiny prostředků. 
6. Vyberte umístění datového centra Azure.  
7. Pro **zdrojový disk**, vyberte spravované Disk snímek.
8. Vyberte **typ účtu** používat k uložení snímku. Doporučujeme, abyste **Standard_LRS** Pokud to uložená na vysokou provádění disku potřebujete.
9. Klikněte na možnost **Vytvořit**.

## <a name="use-powershell-to-take-a-snapshot"></a>Pořízení snímku pomocí prostředí PowerShell

Následující kroky vám ukážou, jak získat disku VHD zkopírovat, vytvořte snímek konfigurace, a pořízení snímku disku pomocí [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) rutiny. 

Než začnete, ujistěte se, že máte nejnovější verzi modulu AzureRM.Compute prostředí PowerShell. Tento článek vyžaduje AzureRM verze modulu 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

Nastavte některé parametry. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

Získáte virtuální počítač.

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName `
   -Name $vmName
```

Vytvoření snímku konfigurace. V tomto příkladu přidáme snímku disk operačního systému.

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig `
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
   -Location $location `
   -CreateOption copy
```
   
> [!NOTE]
> Pokud chcete uložit snímku do zóny odolná úložiště, budete muset vytvořit v oblasti, která podporuje [dostupnost zóny](../../availability-zones/az-overview.md) a zahrnout `-SkuName Standard_ZRS` parametr.   

   
Vytvořte snímek.

```azurepowershell-interactive
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```




## <a name="next-steps"></a>Další postup

Vytvoření virtuálního počítače ze snímku vytvořením se spravovaným diskem ze snímku a potom nový spravované disk jako disk operačního systému se připojuje. Další informace najdete v tématu [vytvoření virtuálního počítače ze snímku](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) ukázka.
