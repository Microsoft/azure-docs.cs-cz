---
title: Vytvoření snímku virtuálního pevného disku v Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit kopii tohoto virtuálního počítače Azure používat jako zálohování směrem nahoru nebo pro řešení potíží.
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
ms.date: 10/08/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 1015e6774dac1258820e3ca4b3d06786046a8554
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980852"
---
# <a name="create-a-snapshot"></a>Vytvoření snímku

Snímek je kopie virtuálního pevného disku (VHD) úplné, jen pro čtení. Vytvoření snímku disku operačního systému nebo datového virtuálního pevného disku jako zálohu, nebo k řešení potíží virtuální počítač (VM).

Pokud se chystáte pomocí tohoto snímku rychle vytvořit nový virtuální počítač, doporučujeme, že je čistě vypnout virtuální počítač před pořízení snímku, vymažte všechny procesy, které jsou v průběhu.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **vytvořit prostředek**a poté vyhledejte a vyberte **snímku**.
3. V **snímku** okně **vytvořit**. **Vytvořit snímek** zobrazí se okno.
4. Zadejte **název** snímku.
5. Vyberte existující [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md#resource-groups) nebo zadejte název nové. 
6. Vyberte **umístění** datového centra Azure.  
7. Pro **zdrojový disk**, vyberte spravovaného disku do snímku.
8. Vyberte **typ účtu** používat k uložení snímku. Vyberte **Standard_HDD**, pokud potřebujete snímek uloží na disku pro vysoce výkonné.
9. Vyberte **Vytvořit**.

## <a name="use-powershell"></a>Použití prostředí PowerShell

Následující kroky ukazují, jak zkopírujte virtuální pevný disk, vytvořte snímek konfigurace a pořízení snímku disku s použitím [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) rutiny. 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

1. Nastavení některých parametrů: 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

2. Získání virtuálního počítače:

 ```azurepowershell-interactive
$vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
```

3. Vytvořte snímek konfigurace. V tomto příkladu snímek je disk s operačním systémem:

 ```azurepowershell-interactive
$snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
```
   
   > [!NOTE]
   > Pokud chcete uložit snímek do zóny odolná úložiště, vytvořte ji v oblasti, která podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) a zahrnout `-SkuName Standard_ZRS` parametru.   
   
4. Vytvořte snímek:

 ```azurepowershell-interactive
New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
```


## <a name="next-steps"></a>Další postup

Vytvoření virtuálního počítače ze snímku pomocí vytvoření spravovaného disku ze snímku a pak připojení nového spravovaného disku jako disku s operačním systémem. Další informace najdete v ukázce v [vytvoření virtuálního počítače ze snímku pomocí Powershellu](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
