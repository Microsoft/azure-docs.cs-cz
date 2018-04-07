---
title: Přesunout virtuální počítač s Linuxem v Azure | Microsoft Docs
description: Přesuňte virtuální počítač s Linuxem do jiné předplatné nebo prostředek skupiny Azure v modelu nasazení Resource Manager.
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
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: a4a7dd5541fe298675232ffa803f749e71f6a03f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Přesunout virtuální počítač s Linuxem do jiné skupiny pro předplatné nebo prostředek
Tento článek vás provede postup přesunutí virtuálního počítače s Linuxem mezi skupinami prostředků nebo předplatných. Přesunutí virtuálního počítače mezi předplatnými může být užitečné, když vytvoříte virtuální počítač v odběru osobní a chcete ho přesunout do předplatného ve vaší společnosti.

> [!IMPORTANT]
>V tuto chvíli nelze přesunout spravované disky. 
>
>Nové ID prostředků jsou vytvořené jako součást přesunutí. Po přesunutí virtuálního počítače je potřeba aktualizovat nástroje a skripty, které pomocí nového ID prostředku. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Použijte rozhraní příkazového řádku Azure k přesunutí virtuálního počítače


Před přesunutím virtuálního počítače pomocí rozhraní příkazového řádku, budete muset Ujistěte se, že zdrojový a cílový odběry existovat v rámci stejné klienta. Chcete-li zkontrolovat, že oba odběry obsahují stejné ID klienta, použijte [az účet zobrazit](/cli/azure/account#az_account_show).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Pokud klient ID pro zdrojové a cílové předplatné nejsou stejné, obraťte se na [podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) pro přesun prostředků do nového klienta.

Chcete-li úspěšně přesunout virtuální počítač, musíte přesunout virtuální počítač a všechny její Podpůrné prostředky. Použití [seznam zdrojů az](/cli/azure/resource#az_resource_list) seznam všechny prostředky v skupinu prostředků a jejich ID příkazu. Pomáhá výstup tohoto příkazu do souboru, můžete zkopírovat a vložit ID do novější příkazy.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Chcete-li přesunout virtuální počítač a jeho prostředků do jiné skupině prostředků, použijte [přesunutí prostředku az](/cli/azure/resource#az_resource_move). Následující příklad ukazuje, jak přesunout virtuální počítač a nejběžnější prostředky, které vyžaduje. Použití **-ID** parametr a předejte jí seznam oddělený čárkami (bez mezer) ID pro prostředky. Chcete-li přesunout.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageacountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Pokud chcete přesunout virtuální počítač a jeho prostředků do jiného předplatného, přidejte **– ID cílového předplatného** parametr k určení cílového odběru.

Pokud se zobrazí výzva k potvrzení, že chcete přesunout zadaný prostředek. Typ **Y** potvrďte, že chcete přesunout prostředky.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Další postup
Mnoho různých typů prostředků můžete přesouvat mezi skupinami prostředků a předplatná. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../../resource-group-move-resources.md).    

