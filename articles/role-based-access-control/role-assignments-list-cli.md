---
title: Vypsání přiřazení rolí pomocí Azure RBAC a Azure CLI
description: Naučte se, jak určit, které prostředky uživatelé, skupiny, instanční objekty nebo spravované identity mají přístup k používání řízení přístupu na základě role (RBAC) Azure a Azure CLI.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 12ecca5873ac7c2c3bfa30d4c73c7d8e268aabfb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355718"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Vypsání přiřazení rolí pomocí Azure RBAC a Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] tomto článku se dozvíte, jak zobrazit seznam přiřazení rolí pomocí rozhraní příkazového řádku Azure CLI.

## <a name="prerequisites"></a>Požadavky

- [Bash v Azure Cloud Shell](/azure/cloud-shell/overview) nebo [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Výpis přiřazení rolí pro uživatele

Chcete-li zobrazit seznam přiřazení rolí pro konkrétního uživatele, použijte příkaz [AZ role Assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Ve výchozím nastavení se zobrazí jenom přiřazení rolí pro aktuální předplatné. Chcete-li zobrazit přiřazení rolí pro aktuální předplatné a níže, přidejte parametr `--all`. Chcete-li zobrazit zděděná přiřazení rolí, přidejte parametr `--include-inherited`.

V následujícím příkladu jsou uvedena přiřazení rolí, která jsou přiřazena přímo uživateli *patlong\@contoso.com* :

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

## <a name="list-role-assignments-for-a-resource-group"></a>Výpis přiřazení rolí pro skupinu prostředků

Pokud chcete zobrazit seznam přiřazení rolí, která existují v oboru skupiny prostředků, použijte [seznam AZ role Assignment](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

V následujícím příkladu jsou uvedena přiřazení rolí pro skupinu prostředků *Pharma-Sales* :

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Výpis přiřazení rolí pro předplatné

Chcete-li zobrazit seznam všech přiřazení rolí v oboru předplatného, použijte příkaz [AZ role Assignment list](/cli/azure/role/assignment#az-role-assignment-list). Pokud chcete získat ID předplatného, najdete ho v okně **předplatná** v Azure Portal nebo můžete použít příkaz [AZ Account list](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Seznam přiřazení rolí pro skupinu pro správu

Chcete-li zobrazit seznam všech přiřazení rolí v oboru skupiny pro správu, použijte příkaz [AZ role Assignment list](/cli/azure/role/assignment#az-role-assignment-list). Chcete-li získat ID skupiny pro správu, můžete ji najít v okně **skupiny pro správu** v Azure Portal nebo můžete použít [příkaz AZ Account Management-Group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Seznam přiřazení rolí pro spravovanou identitu

1. Získejte ID objektu spravované identity přiřazené systémem nebo uživatelem. 

    Pokud chcete získat ID objektu spravované identity přiřazené uživatelem, můžete použít příkaz [AZ AD SP list](/cli/azure/ad/sp#az-ad-sp-list) nebo [AZ identity list](/cli/azure/identity#az-identity-list).

    ```azurecli
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    Pokud chcete získat ID objektu spravované identity přiřazené systémem, můžete použít příkaz [AZ AD SP list](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. K vypsání přiřazení rolí použijte příkaz [AZ role Assignment list](/cli/azure/role/assignment#az-role-assignment-list).

    Ve výchozím nastavení se zobrazí jenom přiřazení rolí pro aktuální předplatné. Chcete-li zobrazit přiřazení rolí pro aktuální předplatné a níže, přidejte parametr `--all`. Chcete-li zobrazit zděděná přiřazení rolí, přidejte parametr `--include-inherited`.

    ```azurecli
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Další kroky

- [Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a Azure CLI](role-assignments-cli.md)
