---
title: Přesunutí prostředku virtuálního počítače s Windows v Azure | Microsoft Docs
description: Přesuňte virtuální počítač s Windows do jiné předplatné nebo prostředek skupiny Azure v modelu nasazení Resource Manager.
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
ms.date: 12/06/2017
ms.author: cynthn
ms.openlocfilehash: b98b8c947fb34b60c7bd27b006672e0e9d923d3b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Přesunout virtuální počítač s Windows do Azure jiné předplatné nebo prostředek skupiny
Tento článek vás provede jak přesunout virtuální počítač s Windows mezi skupinami prostředků nebo předplatných. Přesouvání mezi předplatnými může být užitečné, pokud jste původně vytvořili virtuální počítač v odběru osobní a chcete ho přesunout do předplatného vaší společnosti chcete-li pokračovat v práci.

> [!IMPORTANT]
>V tuto chvíli nelze přesunout spravované disky. 
>
>Nové ID prostředků jsou vytvořené jako součást přesunutí. Po přesunutí virtuálního počítače je potřeba aktualizovat nástroje a skripty, které pomocí nového ID prostředku. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Přesunout virtuální počítač pomocí prostředí Powershell

Chcete-li přesunout virtuální počítač do jiné skupiny prostředků, ujistěte se, že také přesunout všechny závislé prostředky. Chcete-li použijte rutinu Move-AzureRMResource, je třeba ResourceId jednotlivých prostředků. Můžete získat seznam ResourceId pomocí [najít AzureRMResource](/powershell/module/azurerm.resources/find-azurermresource) rutiny.

```azurepowershell-interactive
 Find-AzureRMResource -ResourceGroupNameContains <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Přesunout virtuální počítač je potřeba přesunout více prostředků. Výstup najít AzureRMResource jsme vám pomůže vytvořte seznam oddělený čárkami ResourceIds a předejte to [přesunutí AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) přesunout do cílové. 

```azurepowershell-interactive

Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Chcete-li přesunout prostředky do jiného předplatného, zahrňte **- DestinationSubscriptionId** parametr. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Jste vyzváni k potvrzení, že chcete přesunout zadané prostředky. 

## <a name="next-steps"></a>Další postup
Mnoho různých typů prostředků můžete přesouvat mezi skupinami prostředků a předplatná. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../../resource-group-move-resources.md).    

