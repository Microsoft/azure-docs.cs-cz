---
title: Přesunutí virtuálního počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Přesuňte virtuální počítač s Linuxem do jiného Azure předplatné nebo skupinu prostředků v modelu nasazení Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
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
ms.openlocfilehash: c451377d6274c50f22e3b1d4cd32fb0f3edd9d9e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220388"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Přesunout na jiné předplatné nebo skupinu prostředků virtuálního počítače s Linuxem
Tento článek vás provede přesun virtuálního počítače s Linuxem (VM) mezi skupinami prostředků nebo předplatných. Přesun virtuálního počítače mezi předplatnými může být užitečné, pokud jste vytvořili virtuální počítač v rámci osobní předplatného a teď chcete přesunout do předplatného ve vaší společnosti.

> [!IMPORTANT]
>V tuto chvíli nelze přesunout Azure Managed Disks. 
>
>Nové ID prostředků vytvořených jako součást přesunu. Po přesunutí virtuálního počítače, je potřeba aktualizovat nástroje a skripty, které používaly nové ID prostředků. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Přesun virtuálních počítačů pomocí Azure CLI


Předtím, než můžete přesunout virtuální počítač pomocí rozhraní příkazového řádku Azure, budete muset ujistěte, že zdrojové a cílové předplatné existuje ve stejném tenantovi. Chcete-li zkontrolovat, že oba odběry obsahují stejné ID tenanta, použijte [zobrazit účet az](/cli/azure/account#az_account_show).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Pokud zdrojové a cílové předplatné ID tenantů se neshodují, obraťte se na [podporují](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) prostředky přesunout do nového tenanta.

Chcete-li úspěšně přesunout virtuální počítač, budete potřebovat přesunout virtuální počítač a všechny její Podpůrné prostředky. Použití [az resource list](/cli/azure/resource#az_resource_list) seznam všech prostředků ve skupině prostředků a jejich ID příkazu. Umožňuje do kanálu výstup tohoto příkazu do souboru, takže můžete zkopírovat a vložit ID novější příkazy.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Chcete-li přesunout virtuální počítač a jejích prostředků do jiné skupiny prostředků, použijte [přesunutí prostředku az](/cli/azure/resource#az_resource_move). Následující příklad ukazuje, jak přesunout virtuální počítač a nejčastěji používaných prostředků, které vyžaduje. Použití **– ID** parametr a předejte jí seznam oddělený čárkami (bez mezer) ID pro prostředky k přesunutí.

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

Pokud chcete přesunout virtuální počítač a její prostředky do jiného předplatného, přidejte **– určení subscriptionId** parametr k určení cílové předplatné.

Pokud budete vyzváni k potvrzení, že chcete přesunout zadané prostředky, zadejte **Y** potvrďte.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Další postup
Mnoho různých typů prostředků můžou přesouvat mezi skupinami prostředků a předplatných. Další informace najdete v tématu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../resource-group-move-resources.md).    

