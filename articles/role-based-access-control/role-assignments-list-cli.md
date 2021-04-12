---
title: Vypsání přiřazení rolí Azure pomocí Azure CLI – Azure RBAC
description: Naučte se, jak určit, které prostředky uživatelé, skupiny, instanční objekty nebo spravované identity mají přístup k používání Azure CLI a řízení přístupu na základě role Azure (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cc64e314a8acb035736df0521987cb78a7297326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556915"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Vypsání přiřazení rolí Azure pomocí Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Tento článek popisuje, jak zobrazit seznam přiřazení rolí pomocí rozhraní příkazového řádku Azure CLI.

> [!NOTE]
> Pokud má vaše organizace samoobslužné funkce správy pro poskytovatele služeb, který používá [správu delegovaných prostředků Azure](../lighthouse/concepts/azure-delegated-resource-management.md), tady se nezobrazí přiřazení rolí autorizovaných tímto poskytovatelem služeb.

## <a name="prerequisites"></a>Požadavky

- [Bash v Azure Cloud Shell](../cloud-shell/overview.md) nebo [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Výpis přiřazení rolí pro uživatele

Chcete-li zobrazit seznam přiřazení rolí pro konkrétního uživatele, použijte příkaz [AZ role Assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee {assignee}
```

Ve výchozím nastavení se zobrazí jenom přiřazení rolí pro aktuální předplatné. Chcete-li zobrazit přiřazení rolí pro aktuální předplatné a níže, přidejte `--all` parametr. Chcete-li zobrazit zděděná přiřazení rolí, přidejte `--include-inherited` parametr.

V následujícím příkladu jsou uvedena přiřazení rolí, která jsou přiřazena přímo uživateli *patlong \@ contoso.com* :

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  }
]
```

## <a name="list-role-assignments-for-a-resource-group"></a>Výpis přiřazení rolí pro skupinu prostředků

Pokud chcete zobrazit seznam přiřazení rolí, která existují v oboru skupiny prostředků, použijte [seznam AZ role Assignment](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group {resourceGroup}
```

V následujícím příkladu jsou uvedena přiřazení rolí pro skupinu prostředků *Pharma-Sales* :

```azurecli
az role assignment list --resource-group pharma-sales --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  
  ...

]
```

## <a name="list-role-assignments-for-a-subscription"></a>Výpis přiřazení rolí pro předplatné

Chcete-li zobrazit seznam všech přiřazení rolí v oboru předplatného, použijte příkaz [AZ role Assignment list](/cli/azure/role/assignment#az-role-assignment-list). Pokud chcete získat ID předplatného, najdete ho v okně **předplatná** v Azure Portal nebo můžete použít příkaz [AZ Account list](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

Příklad:

```azurecli
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "Subscription Admins",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },

  ...

]
```

## <a name="list-role-assignments-for-a-management-group"></a>Seznam přiřazení rolí pro skupinu pro správu

Chcete-li zobrazit seznam všech přiřazení rolí v oboru skupiny pro správu, použijte příkaz [AZ role Assignment list](/cli/azure/role/assignment#az-role-assignment-list). Chcete-li získat ID skupiny pro správu, můžete ji najít v okně **skupiny pro správu** v Azure Portal nebo můžete použít [příkaz AZ Account Management-Group list](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

Příklad:

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/sales-group --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  }
]
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Seznam přiřazení rolí pro spravovanou identitu

1. Získat ID objektu zabezpečení pro spravovanou identitu přiřazenou systémem nebo uživatelem

    Pokud chcete získat hlavní ID spravované identity přiřazené uživatelem, můžete použít příkaz [AZ AD SP list](/cli/azure/ad/sp#az-ad-sp-list) nebo [AZ identity list](/cli/azure/identity#az-identity-list).

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    Pokud chcete získat ID objektu zabezpečení spravované identity přiřazené systémem, můžete použít příkaz [AZ AD SP list](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. K vypsání přiřazení rolí použijte příkaz [AZ role Assignment list](/cli/azure/role/assignment#az-role-assignment-list).

    Ve výchozím nastavení se zobrazí jenom přiřazení rolí pro aktuální předplatné. Chcete-li zobrazit přiřazení rolí pro aktuální předplatné a níže, přidejte `--all` parametr. Chcete-li zobrazit zděděná přiřazení rolí, přidejte `--include-inherited` parametr.

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure pomocí Azure CLI](role-assignments-cli.md)