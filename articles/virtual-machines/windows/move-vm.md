---
title: Přesunutí prostředku virtuálního počítače s Windows v Azure
description: Přesuňte virtuální počítač s Windows do jiného předplatného Azure nebo skupiny prostředků v modelu nasazení Správce prostředků.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: c5d3445144178ce855a5ce904b42d6e68a8e7d29
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555240"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Přesun virtuálního počítače s Windows do jiného předplatného nebo jiné skupiny prostředků Azure
Tento článek vás provede postupem přesunutí virtuálního počítače s Windows mezi skupinami prostředků nebo odběry. Přesunutí mezi předplatnými může být užitečné, pokud jste původně vytvořili virtuální počítač v osobním předplatném a chcete ho teď přesunout do předplatného vaší společnosti, abyste mohli pokračovat v práci. Virtuální počítač není potřeba zastavit, aby ho bylo možné přesunout a měl by během přesunu běžet dál.

> [!IMPORTANT]
>V rámci přesunutí se vytvoří nová ID prostředků. Po přesunutí virtuálního počítače budete muset aktualizovat nástroje a skripty, aby používaly nová ID prostředků.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Použití PowerShellu k přesunu virtuálního počítače

Pokud chcete přesunout virtuální počítač do jiné skupiny prostředků, musíte se ujistit, že také přesunete všechny závislé prostředky. Seznam s ID prostředku každého z těchto prostředků získáte pomocí rutiny [Get-AzResource](/powershell/module/az.resources/get-azresource) .

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

Pomocí výstupu z předchozího příkazu můžete vytvořit seznam oddělený čárkami, který se má [přesunout – AzResource](/powershell/module/az.resources/move-azresource) a přesunout jednotlivé prostředky do cílového umístění.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
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
Mezi skupinami prostředků a předplatnými můžete přesunout mnoho různých typů prostředků. Další informace najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
