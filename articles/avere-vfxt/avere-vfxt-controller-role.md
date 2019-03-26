---
title: Vlastní kontroler přístup role - Avere vFXT pro Azure
description: Jak vytvořit vlastní přístup role kontroleru Avere vFXT clusteru
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 2a0f4a628764aaa561a5567d3435a42da804a994
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417833"
---
# <a name="customized-controller-access-role"></a>Přizpůsobená role s přístupem kontroleru

VFXT Avere pro kontroler clusteru Azure používá spravované identity a řízení přístupu na základě role (RBAC) aby mohla vytvářet a spravovat cluster. 

Ve výchozím nastavení, je přiřazen řadič clusteru [předdefinovanou roli vlastníka](../role-based-access-control/built-in-roles.md#owner). Také přístup kontroleru působí na jeho skupina prostředků - ho nelze změnit prvky mimo skupinu prostředků clusteru.

Tento článek vysvětluje, jak vytvořit vlastní role přístupu pro cluster kontroler místo použití výchozí nastavení. 

## <a name="edit-the-role-prototype"></a>Upravit roli prototypu

Spustit z prototypu role k dispozici na <https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereContributor.txt>.

```json
{
  "AssignableScopes": [
    "/subscriptions/YOUR SUBSCRIPTION ID HERE"
  ],
  "Name": "Avere custom contributor",
  "IsCustom": true,
  "Description": "Can create and manage an Avere vFXT cluster.",
  "NotActions": [],
  "Actions": [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/disks/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/*/read",
    "Microsoft.Network/networkInterfaces/*",
    "Microsoft.Network/virtualNetworks/read",
    "Microsoft.Network/virtualNetworks/subnets/join/action",
    "Microsoft.Network/virtualNetworks/subnets/read",
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Support/*"
  ],
  "DataActions": []
}
```

ID předplatného pro vFXT Avere pro nasazení Azure přidejte v AssignableScopes příkazu. Upravit název a přidat nebo změnit definice podle potřeby. 

Buďte opatrní, je-li omezit oprávnění. Vytvoření clusteru může selhat, pokud zařízení nemá dostatečný přístup. 

Pro vysvětlení, co oprávnění správce clusteru je potřeba vytvořit cluster, [vytvořit lístek podpory](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt). 

Uložte jako soubor .json vaší definice vlastních rolí. 

## <a name="define-the-role"></a>Definice role 

Použijte následující postup k předplatnému přidat definice vlastních rolí. 

1. Otevřete Azure Cloud Shell na webu Azure Portal nebo vyhledejte [ https://shell.azure.com ](https://shell.azure.com).

1. Přejděte ke svému předplatnému vFXT pomocí příkazového řádku Azure:

   ```azurecli
   az account set --subscription YOUR_SUBSCRIPTION_ID
   ```

1. Vytvoření role:

   ```azurecli
   az role definition create --role-definition /avere-contributor-custom.json
   ```

   Použít název souboru a cestu místo ```/avere-contributor-custom.json``` v tomto příkladu. 

Uložte si výstup příkazu definice rolí – obsahuje identifikátor role, které je třeba zadat šablonu pro vytvoření clusteru. 

## <a name="find-the-role-id"></a>Najít role ID

Šablona nasazení vFXT Avere potřebuje role globálně jedinečný identifikátor (GUID) přiřazení vlastní role kontroleru. 

Role GUID je 32znakový řetězec v tomto formuláři: 8e3af657-a8ff-443c-a75c-2fe8c4bcb635

K vyhledání identifikátoru GUID vaší role, použít tento příkaz s názvem role v ```--name``` parametru.

```azurecli
az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
```
Zadejte tento řetězec v **role ID pro vytvoření clusteru Avere** pole při nasazování Avere vFXT pro Azure.

## <a name="next-steps"></a>Další postup

Přečtěte si postup pro Azure v nasazení Avere vFXT [vFXT cluster nasadit](avere-vfxt-deploy.md)
