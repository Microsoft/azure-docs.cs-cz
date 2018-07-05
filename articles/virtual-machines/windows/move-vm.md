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
ms.date: 12/06/2017
ms.author: cynthn
ms.openlocfilehash: 168ba57399b2649af29820f7321dd0151618346e
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436476"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Přesunout do jiného Azure předplatné nebo skupinu prostředků virtuálního počítače s Windows
Tento článek vás provede přesun virtuálního počítače s Windows mezi skupinami prostředků nebo předplatných. Přesun mezi předplatnými může být užitečné, pokud původně vytvořili virtuální počítač v rámci osobní předplatného a teď chcete přesunout do předplatného vaší společnosti chcete-li pokračovat v práci.

> [!IMPORTANT]
>V tuto chvíli nelze přesunout Managed Disks. 
>
>Nové ID prostředků vytvořených jako součást přesunu. Po přesunutí virtuálního počítače je potřeba aktualizovat nástroje a skripty, které používaly nové ID prostředků. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Přesun virtuálního počítače pomocí Powershellu

Chcete-li přesunout virtuální počítač do jiné skupiny prostředků, ujistěte se, že také přesunout všechny závislé prostředky. Chcete-li použijte rutinu Move-AzureRMResource, musíte ResourceId jednotlivých prostředků. Můžete získat seznam ID prostředku pro použití [Get-AzureRMResource](/powershell/module/azurerm.resources/get-azurermresource) rutiny.

```azurepowershell-interactive
 Get-AzureRMResource -ResourceGroupName <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Pro přesun virtuálního počítače potřebujeme k přesunutí více prostředků. Můžeme použít výstup Get-AzureRMResource vytvořte seznam oddělený čárkami ResourceId a předat ho do [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) přesunout do cílového umístění. 

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


Zobrazí výzva k potvrzení, že chcete přesunout zadané prostředky. 

## <a name="next-steps"></a>Další postup
Mnoho různých typů prostředků můžou přesouvat mezi skupinami prostředků a předplatných. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../../resource-group-move-resources.md).    

