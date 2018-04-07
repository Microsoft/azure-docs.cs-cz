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
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: c5f4c7224e04b601d7d3fe4da7d8f5f0c02c7039
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
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

Ujistěte se, že máte nejnovější verzi instalace modulu prostředí AzureRM.Compute PowerShell. Spusťte následující příkaz k její instalaci.

```
Install-Module AzureRM.Compute -MinimumVersion 2.6.0
```
Další informace najdete v tématu [Azure PowerShell verze](/powershell/azure/overview).


1. Nastavte některé parametry. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. Získáte virtuální pevný disk disku ke kopírování.

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Vytvořte snímek konfigurace. 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Vytvořte snímek.

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Pokud máte v plánu používat k vytvoření disku spravované a připojte ji virtuální počítač, který musí být vysoké provádění snímku, použijte parametr `-AccountType Premium_LRS` pomocí příkazu New-AzureRmSnapshot. Parametr vytvoří snímek tak, aby je uložena jako spravovaný Disk úrovně Premium. Premium spravované disky jsou dražší než Standard. Proto ujistěte se, že je skutečně potřebujete Premium před použitím tohoto parametru.

## <a name="next-steps"></a>Další postup

Vytvoření virtuálního počítače ze snímku vytvořením se spravovaným diskem ze snímku a potom nový spravované disk jako disk operačního systému se připojuje. Další informace najdete v tématu [vytvoření virtuálního počítače ze snímku](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) ukázka.
