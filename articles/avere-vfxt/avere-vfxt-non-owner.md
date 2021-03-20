---
title: Alternativní řešení avere vFXT, které není vlastníkem – Azure
description: Alternativní řešení umožňující uživatelům bez oprávnění vlastníka předplatného nasazovat avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 0d9b1060ee35af6cbc2e1b95b0f7813072c52d2e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85505371"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Oprávnění k nasazení Avere vFXT pro jiné uživatele než vlastníky

Tyto pokyny představují alternativní řešení, které umožňuje uživateli bez oprávnění vlastníka předplatného vytvářet avere vFXT pro systém Azure.

(Doporučený způsob, jak nasadit systém avere vFXT, je mít uživatele s oprávněním vlastníka udělat kroky vytváření, jak je vysvětleno v tématu [Příprava na vytvoření avere vFXT](avere-vfxt-prereqs.md).)  

Alternativní řešení zahrnuje vytvoření další role přístupu, která poskytne svým uživatelům dostatečná oprávnění k instalaci clusteru. Role musí být vytvořena vlastníkem předplatného a vlastník ji musí přiřadit odpovídajícím uživatelům.

Vlastník předplatného musí také [přijmout podmínky použití](avere-vfxt-prereqs.md) pro Image avere vFXT Marketplace.

> [!IMPORTANT]
> Všechny tyto kroky musí provést uživatel s oprávněním vlastníka v předplatném, které se bude používat pro cluster.

1. Zkopírujte tyto řádky a uložte je do souboru (například `averecreatecluster.json` ). V příkazu použijte své ID předplatného `AssignableScopes` .

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",

           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. Spuštěním tohoto příkazu vytvořte roli:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Příklad:

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Přiřaďte tuto roli uživateli, který vytvoří cluster:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Po dokončení tohoto procesu poskytne role každému uživateli přiřazenému pro toto předplatné následující oprávnění:

* Vytvoření a konfigurace síťové infrastruktury
* Vytvoření řadiče clusteru
* Spusťte skripty pro vytvoření clusteru z řadiče clusteru a vytvořte cluster.
