---
title: Přesunutí prostředku virtuálního počítače s Windows v Azure
description: Přesuňte virtuální počítač s Windows do jiného předplatného Azure nebo skupiny prostředků v modelu nasazení Správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: 9264e36922cb88c541ba9fb2fe54a9606f371b72
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033177"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Přesunutí virtuálního počítače s Windows do jiného předplatného nebo skupiny prostředků Azure
Tento článek vás provede postupem přesunutí virtuálního počítače s Windows mezi skupinami prostředků nebo odběry. Přesunutí mezi předplatnými může být užitečné, pokud jste původně vytvořili virtuální počítač v osobním předplatném a chcete ho teď přesunout do předplatného vaší společnosti, abyste mohli pokračovat v práci. Virtuální počítač není nutné spouštět, aby jej bylo možné přesunout a měl by během přesunu pokračovat v běhu.

> [!IMPORTANT]
>V rámci přesunutí se vytvoří nová ID prostředků. Po přesunutí virtuálního počítače budete muset aktualizovat nástroje a skripty, aby používaly nová ID prostředků. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Použití PowerShellu k přesunu virtuálního počítače

Pokud chcete přesunout virtuální počítač do jiné skupiny prostředků, musíte se ujistit, že také přesunete všechny závislé prostředky. Seznam s ID prostředku každého z těchto prostředků získáte pomocí rutiny [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) .

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-list -wrap -Property ResourceId 
```

Výstup předchozího příkazu můžete použít jako čárkami oddělený seznam ID prostředků pro [Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) pro přesun jednotlivých prostředků do cílového umístění. 

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Pokud chcete přesunout prostředky do jiného předplatného, zadejte parametr **-DestinationSubscriptionId** . 

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Po zobrazení výzvy k potvrzení, že chcete přesunout zadané prostředky, potvrďte zadáním **Y** .

## <a name="next-steps"></a>Další kroky
Mezi skupinami prostředků a předplatnými můžete přesunout mnoho různých typů prostředků. Další informace najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../resource-group-move-resources.md).    

