---
title: Přesunutí virtuálního počítače se systémem Linux v Azure
description: Přesuňte virtuální počítač se systémem Linux do jiného předplatného Azure nebo do skupiny prostředků v modelu nasazení Správce prostředků.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 8fc04b4689ea8707ac2c605e3e4242a117773151
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035633"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Přesunutí virtuálního počítače se systémem Linux do jiného předplatného nebo skupiny prostředků
Tento článek vás seznámí s postupem přesunutí virtuálního počítače se systémem Linux mezi skupinami prostředků nebo odběry. Přesunutí virtuálního počítače mezi předplatnými může být užitečné, pokud jste vytvořili virtuální počítač v osobním předplatném a teď ho chcete přesunout do předplatného vaší společnosti.

> [!IMPORTANT]
>V tuto chvíli nejde přesunout službu Azure Managed Disks. 
>
>V rámci přesunutí se vytvoří nová ID prostředků. Po přesunutí virtuálního počítače budete muset aktualizovat nástroje a skripty, aby používaly nová ID prostředků. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Přesunutí virtuálního počítače pomocí rozhraní příkazového řádku Azure


Než budete moct virtuální počítač přesunout pomocí rozhraní příkazového řádku Azure, musíte se ujistit, že ve stejném tenantovi existují zdrojové a cílové odběry. Pokud chcete ověřit, že obě předplatná mají stejné ID tenanta, použijte příkaz [AZ Account show](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Pokud ID tenanta pro zdrojové a cílové odběry nejsou stejné, musíte kontaktovat [podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , aby se prostředky přesunuly do nového tenanta.

K úspěšnému přesunu virtuálního počítače je potřeba přesunout virtuální počítač a všechny jeho podpůrné prostředky. Pomocí příkazu [AZ Resource list](/cli/azure/resource) zobrazíte seznam všech prostředků ve skupině prostředků a jejich ID. Pomáhá přesměrovat výstup tohoto příkazu do souboru, aby bylo možné kopírovat a vkládat ID do pozdějších příkazů.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Pokud chcete přesunout virtuální počítač a jeho prostředky do jiné skupiny prostředků, použijte příkaz [AZ Resource Move](/cli/azure/resource). Následující příklad ukazuje, jak přesunout virtuální počítač a nejběžnější prostředky, které vyžaduje. Použijte parametr **-IDS** a předejte seznam oddělený čárkami (bez mezer) pro prostředky, které se mají přesunout.

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

Pokud chcete přesunout virtuální počítač a jeho prostředky do jiného předplatného, přidejte parametr **--Destination-SubscriptionId** pro určení cílového předplatného.

Po zobrazení výzvy k potvrzení, že chcete přesunout zadané prostředky, potvrďte zadáním **Y** .

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Další kroky
Mezi skupinami prostředků a předplatnými můžete přesunout mnoho různých typů prostředků. Další informace najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../resource-group-move-resources.md).    

