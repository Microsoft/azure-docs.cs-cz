---
title: Přesunutí prostředku virtuálního počítače s Windows v Azure
description: Přesuňte virtuální počítač s Windows do jiného předplatného nebo skupiny prostředků Azure v modelu nasazení Správce prostředků.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: 0c3f5541405d1fd983bbf988b99d2b4e10d8908c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865668"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Přesun virtuálního počítače s Windows do jiného předplatného nebo jiné skupiny prostředků Azure
Tento článek vás provede přesunutím virtuálního počítače (VM) Windows mezi skupinami prostředků nebo odběry. Přechod mezi předplatnými může být užitečné, pokud jste původně vytvořili virtuální počítač v osobním předplatném a teď ho chcete přesunout do předplatného vaší společnosti, abyste mohli pokračovat ve své práci. Není nutné spustit virtuální ho, aby se přesunout a měl by pokračovat v běhu během přesunutí.

> [!IMPORTANT]
>Jako součást přesunu jsou vytvořena nová ID prostředků. Po přesunutí virtuálního počítače budete muset aktualizovat nástroje a skripty, abyste použili nová ID prostředků.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Přesunutí virtuálního virtuálního mísy pomocí Powershellu

Chcete-li přesunout virtuální počítač do jiné skupiny prostředků, musíte se ujistit, že také přesunete všechny závislé prostředky. Chcete-li získat seznam s ID prostředku každého z těchto prostředků, použijte rutinu [Get-AzResource.](https://docs.microsoft.com/powershell/module/az.resources/get-azresource)

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

Výstup předchozího příkazu můžete použít k vytvoření seznamu ID prostředků oddělených čárkami pro [move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) k přesunutí každého prostředku do cíle.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Chcete-li přesunout prostředky do jiného předplatného, zadejte parametr **-DestinationSubscriptionId.**

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Když budete vyzváni k potvrzení, že chcete přesunout zadané prostředky, zadejte **Y** pro potvrzení.

## <a name="next-steps"></a>Další kroky
Můžete přesunout mnoho různých typů prostředků mezi skupinami prostředků a odběry. Další informace naleznete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
