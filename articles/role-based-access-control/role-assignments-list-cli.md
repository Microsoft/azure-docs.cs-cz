---
title: Seznam přiřazení rolí pomocí Azure RBAC a Azure CLI
description: Zjistěte, jak určit, k jakým prostředkům mají uživatelé, skupiny, instanční objekty nebo spravované identity přístup pomocí řízení přístupu na základě rolí Azure (RBAC) a Azure CLI.
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5716e7bb89d017866bd1575256e2d119bb7acbe5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385057"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Seznam přiřazení rolí pomocí Azure RBAC a Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Tento článek popisuje, jak seznam přiřazení rolí pomocí Azure CLI.

> [!NOTE]
> Pokud vaše organizace zadala funkce správy poskytovateli služeb, který používá [azure delegovanou správu prostředků](../lighthouse/concepts/azure-delegated-resource-management.md), přiřazení rolí autorizovaná tímto poskytovatelem služeb se zde nezobrazí.

## <a name="prerequisites"></a>Požadavky

- [Bash v Azure Cloud Shell](/azure/cloud-shell/overview) nebo Azure [CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Výpis přiřazení rolí pro uživatele

Chcete-li vypsat přiřazení rolí pro konkrétního uživatele, použijte [seznam přiřazení role az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli-interactive
az role assignment list --assignee <assignee>
```

Ve výchozím nastavení se zobrazí pouze přiřazení rolí pro aktuální předplatné. Chcete-li zobrazit přiřazení rolí pro aktuální `--all` předplatné a níže, přidejte parametr. Chcete-li zobrazit zděděná `--include-inherited` přiřazení rolí, přidejte parametr.

V následujícím příkladu jsou uvedeny přiřazení rolí, která jsou přiřazena přímo *uživateli contoso.com\@patlong:*

```azurecli-interactive
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
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

Chcete-li vypsat přiřazení rolí, která existují v oboru skupiny zdrojů, použijte [seznam přiřazení role AZ](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

V následujícím příkladu jsou uvedena přiřazení rolí pro skupinu *zdrojů pharma-sales:*

```azurecli-interactive
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
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

Chcete-li vypsat všechna přiřazení rolí v oboru předplatného, použijte [seznam přiřazení rolí az](/cli/azure/role/assignment#az-role-assignment-list). Chcete-li získat ID předplatného, najdete ho na okně **Předplatná** na webu Azure Portal nebo můžete použít [seznam účtů az](/cli/azure/account#az-account-list).

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

Příklad:

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Seznam přiřazení rolí pro skupinu pro správu

Chcete-li vypsat všechna přiřazení rolí v oboru skupiny pro správu, použijte [seznam přiřazení role AZ](/cli/azure/role/assignment#az-role-assignment-list). Chcete-li získat ID skupiny pro správu, najdete ho v okně **Skupiny pro správu** na webu Azure Portal nebo můžete použít [seznam skupin pro správu účtů az](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Příklad:

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Seznam přiřazení rolí pro spravovanou identitu

1. Získejte ID objektu spravované identity přiřazené nebo uživatelem.

    Chcete-li získat ID objektu spravované identity přiřazené uživateli, můžete použít [seznam az ad ad sp](/cli/azure/ad/sp#az-ad-sp-list) nebo [az seznam identit](/cli/azure/identity#az-identity-list).

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    Chcete-li získat ID objektu spravované identity přiřazené k systému, můžete použít [seznam az ad ad sp](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. Chcete-li uvést přiřazení rolí, použijte [seznam přiřazení role AZ](/cli/azure/role/assignment#az-role-assignment-list).

    Ve výchozím nastavení se zobrazí pouze přiřazení rolí pro aktuální předplatné. Chcete-li zobrazit přiřazení rolí pro aktuální `--all` předplatné a níže, přidejte parametr. Chcete-li zobrazit zděděná `--include-inherited` přiřazení rolí, přidejte parametr.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Další kroky

- [Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a Azure CLI](role-assignments-cli.md)
