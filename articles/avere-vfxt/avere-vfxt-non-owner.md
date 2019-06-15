---
title: Alternativní řešení jiných uživatelů vFXT Avere – Azure
description: Alternativní řešení umožňující uživatelům bez oprávnění vlastníka předplatného Avere vFXT nasazení pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: e72e6d969649de09389ee38b94e874fad98ee08f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409205"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Oprávnění k nasazení Avere vFXT pro jiné uživatele než vlastníky

Tyto pokyny jsou alternativní řešení, která umožňuje uživateli bez předplatného oprávnění vlastníka k vytvoření Avere vFXT systému Azure.

(Doporučený způsob, jak nasadit Avere vFXT systému je, aby uživatel s oprávněními vlastníka provést vytvoření kroky, jak je vysvětleno v [Příprava k tvorbě Avere vFXT](avere-vfxt-prereqs.md).)  

Alternativní řešení zahrnuje vytvoření roli další přístup, který poskytuje svým uživatelům dostatečná oprávnění k instalaci clusteru. Je nutné vytvořit roli vlastník předplatného a vlastníka nutné ji přiřadit k příslušným uživatelům. 

Také musí vlastník předplatného [přijměte podmínky použití](avere-vfxt-prereqs.md) pro Avere vFXT marketplace image. 

> [!IMPORTANT] 
> Všechny tyto kroky musí být přijata uživatelem s oprávněními vlastníka předplatného, který se použije pro cluster.

1. Zkopírujte tyto řádky a uložit je do souboru (například `averecreatecluster.json`). Použijte ID vašeho předplatného v `AssignableScopes` příkazu.

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

1. Spusťte tento příkaz pro vytvoření role:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Příklad:
    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Tuto roli přiřaďte uživatele, který se vytvoří cluster:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Po provedení tohoto postupu je žádný uživatel tuto roli nemají přiřazenou následující oprávnění pro předplatné: 

* Vytvoření a konfigurace síťové infrastruktury
* Vytvoření clusteru kontroleru
* Spouštění skriptů pro vytvoření clusteru z clusteru kontroleru k vytvoření clusteru
