---
title: Přesunutí prostředku virtuálního počítače Windows v Azure | Dokumentace Microsoftu
description: Přesunutí virtuálního počítače s Windows do jiného Azure předplatné nebo skupinu prostředků v modelu nasazení Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 1daf04e3f878d0748bfa0904259c7b7187481843
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580478"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Přesunout do jiného Azure předplatné nebo skupinu prostředků virtuálního počítače s Windows
Tento článek vás provede postupy přesunout virtuální počítač s Windows (VM) mezi skupinami prostředků nebo předplatná. Přesun mezi předplatnými může být užitečné, pokud původně vytvořili virtuální počítač v rámci osobní předplatného a teď chcete přesunout do předplatného vaší společnosti chcete-li pokračovat v práci.

> [!IMPORTANT]
>V tuto chvíli nelze přesunout Azure Managed Disks. 
>
>Nové ID prostředků vytvořených jako součást přesunu. Po přesunutí virtuálního počítače, je potřeba aktualizovat nástroje a skripty, které používaly nové ID prostředků. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Přesun virtuálního počítače pomocí Powershellu

Chcete-li přesunout virtuální počítač do jiné skupiny prostředků, ujistěte se, že také přesunout všechny závislé prostředky. Chcete-li získat seznam s ID prostředku, každý z těchto prostředků, použijte [Get-AzureRMResource](/powershell/module/azurerm.resources/get-azurermresource) rutiny.

```azurepowershell-interactive
 Get-AzureRMResource -ResourceGroupName <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Výstup předchozího příkazu, můžete použít jako čárkami oddělený seznam ID prostředku pro [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) přesunout do cílového umístění každého prostředku. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Prostředky přesunout do jiného předplatného, zahrňte **- DestinationSubscriptionId** parametru. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Pokud budete vyzváni k potvrzení, že chcete přesunout zadané prostředky, zadejte **Y** potvrďte.

## <a name="next-steps"></a>Další postup
Mnoho různých typů prostředků můžou přesouvat mezi skupinami prostředků a předplatných. Další informace najdete v tématu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../resource-group-move-resources.md).    

