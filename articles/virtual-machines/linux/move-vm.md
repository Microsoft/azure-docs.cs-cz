---
title: Přesunutí virtuálního počítače pomocí azure cli
description: Přesuňte virtuální počítač do jiného předplatného Nebo skupiny prostředků Azure pomocí azure cli.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: ebcd5f166fd1876f67121787c23d23860c9fa4b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944591"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>Přesunutí virtuálního virtuálního uživatele do jiného předplatného nebo skupiny prostředků
Tento článek vás provede, jak přesunout virtuální počítač (VM) mezi skupinami prostředků nebo odběry. Přesunutí virtuálního počítače mezi předplatnými může být užitečné, pokud jste vytvořili virtuální počítač v osobním předplatném a teď ho chcete přesunout do předplatného vaší společnosti.

> [!IMPORTANT]
>Jako součást přesunu jsou vytvořena nová ID prostředků. Po přesunutí virtuálního počítače budete muset aktualizovat nástroje a skripty, abyste použili nová ID prostředků.
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>Přesunutí virtuálního počítače pomocí azure cli


Než budete moct přesunout virtuální počítač pomocí azure cli, musíte se ujistit, že zdrojové a cílové předplatná existují v rámci stejného klienta. Chcete-li zkontrolovat, zda mají obě předplatná stejné ID klienta, použijte [zobrazení účtu az](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Pokud ID klienta pro zdrojové a cílové předplatná nejsou stejné, musíte kontaktovat [podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) přesunout prostředky do nového klienta.

Chcete-li úspěšně přesunout virtuální hod, musíte přesunout virtuální ho a všechny jeho podpůrné prostředky. Pomocí příkazu [az seznam zdrojů](/cli/azure/resource) můžete vypsat všechny prostředky ve skupině prostředků a jejich ID. Pomáhá pipe výstup tohoto příkazu do souboru, takže můžete zkopírovat a vložit ID do novějších příkazů.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Chcete-li přesunout virtuální hosti a jeho prostředky do jiné skupiny prostředků, použijte [přesunutí prostředků AZ](/cli/azure/resource). Následující příklad ukazuje, jak přesunout virtuální hod a nejběžnější prostředky, které vyžaduje. Použijte parametr **-ids** a předajte seznam ID oddělených čárkami (bez mezer) pro přesun prostředků.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Pokud chcete přesunout virtuální ho virtuálního ms a jeho prostředky do jiného předplatného, přidejte parametr **--destination-subscriptionId** k určení cílového předplatného.

Když budete vyzváni k potvrzení, že chcete přesunout zadané prostředky, zadejte **Y** pro potvrzení.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Další kroky
Můžete přesunout mnoho různých typů prostředků mezi skupinami prostředků a odběry. Další informace naleznete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
