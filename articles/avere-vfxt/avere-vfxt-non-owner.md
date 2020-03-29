---
title: Avere vFXT řešení bez vlastníka - Azure
description: Řešení umožňující uživatelům bez oprávnění vlastníka předplatného k nasazení Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 1b411fe465a67f8ea5421ac0dc93348b4e92e8ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153271"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Oprávnění k nasazení Avere vFXT pro jiné uživatele než vlastníky

Tyto pokyny jsou řešení, které umožňuje uživateli bez oprávnění vlastníka předplatného vytvořit systém Avere vFXT pro Azure.

(Doporučený způsob nasazení systému Avere vFXT je mít uživatele s oprávněními vlastníka provést kroky vytváření, jak je vysvětleno v [připravte se na vytvoření Avere vFXT](avere-vfxt-prereqs.md).)  

Toto zástupné řešení zahrnuje vytvoření další přístupové role, která poskytuje uživatelům dostatečná oprávnění k instalaci clusteru. Role musí být vytvořena vlastníkem předplatného a vlastník ji musí přiřadit příslušným uživatelům.

Vlastník předplatného musí také [přijmout podmínky použití](avere-vfxt-prereqs.md) pro image tržiště Avere vFXT.

> [!IMPORTANT]
> Všechny tyto kroky musí být provedeny uživatelem s oprávněními vlastníka v předplatném, které bude použito pro cluster.

1. Zkopírujte tyto řádky a uložte je `averecreatecluster.json`do souboru (například). V příkazu `AssignableScopes` použijte ID předplatného.

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

1. Chcete-li vytvořit roli, spusťte tento příkaz:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Příklad:

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Přiřaďte tuto roli uživateli, který vytvoří cluster:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Po dokončení tohoto procesu, role dává každému uživateli přiřazena následující oprávnění pro odběr:

* Vytvoření a konfigurace síťové infrastruktury
* Vytvoření řadiče clusteru
* Spuštění skriptů pro vytváření clusteru z řadiče clusteru k vytvoření clusteru
