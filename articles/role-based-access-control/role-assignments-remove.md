---
title: Odebrání přiřazení rolí Azure – Azure RBAC
description: Naučte se, jak odebrat přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure nebo služby REST API.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 7a3e4853d6dffa7eb2c5cf80846f6f1bd6beba03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100561378"
---
# <a name="remove-azure-role-assignments"></a>Odebrání přiřazení rolí Azure

[Řízení přístupu na základě role Azure (Azure RBAC)](../../articles/role-based-access-control/overview.md) je autorizační systém, který používáte ke správě přístupu k prostředkům Azure. Pokud chcete odebrat přístup z prostředku Azure, odeberete přiřazení role. Tento článek popisuje, jak odebrat přiřazení rolí pomocí Azure Portal, Azure PowerShell, Azure CLI a REST API.

## <a name="prerequisites"></a>Požadavky

Pokud chcete odebrat přiřazení rolí, musíte mít:

- `Microsoft.Authorization/roleAssignments/delete`oprávnění, jako je například správce nebo [vlastník](../../articles/role-based-access-control/built-in-roles.md#owner) [přístupu uživatele](../../articles/role-based-access-control/built-in-roles.md#user-access-administrator)

## <a name="azure-portal"></a>portál Azure

1. Otevřete **řízení přístupu (IAM)** v oboru, jako je například skupina pro správu, předplatné, skupina prostředků nebo prostředek, kde chcete odebrat přístup.

1. Kliknutím na kartu **přiřazení rolí** zobrazíte všechna přiřazení rolí v tomto oboru.

1. V seznamu přiřazení rolí přidejte značku zaškrtnutí vedle objektu zabezpečení s přiřazením role, které chcete odebrat.

   ![Přiřazení role vybrané k odebrání](./media/role-assignments-remove/rg-role-assignments-select.png)

1. Klikněte na **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-remove/remove-role-assignment.png)

1. Ve zprávě odebrat přiřazení role, která se zobrazí, klikněte na **Ano**.

    Pokud se zobrazí zpráva, že se zděděná přiřazení rolí nedají odebrat, pokoušíte se odebrat přiřazení role v podřízeném oboru. Měli byste otevřít řízení přístupu (IAM) v oboru, kde byla role přiřazena, a akci opakovat. Rychlý způsob, jak otevřít řízení přístupu (IAM) ve správném oboru, je podívat se na sloupec **Rozsah** a kliknout na odkaz vedle **(Zděděno)**.

   ![Odebrat zprávu přiřazení role pro zděděná přiřazení rolí](./media/role-assignments-remove/remove-role-assignment-inherited.png)

## <a name="azure-powershell"></a>Azure PowerShell

V Azure PowerShell odeberete přiřazení role pomocí funkce [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

Následující příklad odebere přiřazení role [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) z uživatele *patlong \@ contoso.com* ve skupině prostředků *Pharma-Sales* :

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Odebere roli [Čtenář](built-in-roles.md#reader) z týmové skupiny *Ann Mack* s ID 22222222-2222-2222-2222-222222222222 v oboru předplatného.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Odebere roli [Čtenář fakturace](built-in-roles.md#billing-reader) z uživatele *Alain \@ example.com* v oboru skupiny pro správu.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Pokud se zobrazí chybová zpráva: "zadané informace nejsou namapovány na přiřazení role", ujistěte se, že jste zadali také `-Scope` `-ResourceGroupName` parametry nebo. Další informace najdete v tématu [řešení potíží s Azure RBAC](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="azure-cli"></a>Azure CLI

V Azure CLI odeberete přiřazení role pomocí funkce [AZ role Assignment Delete](/cli/azure/role/assignment#az_role_assignment_delete).

Následující příklad odebere přiřazení role [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) z uživatele *patlong \@ contoso.com* ve skupině prostředků *Pharma-Sales* :

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Odebere roli [Čtenář](built-in-roles.md#reader) z týmové skupiny *Ann Mack* s ID 22222222-2222-2222-2222-222222222222 v oboru předplatného.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Odebere roli [Čtenář fakturace](built-in-roles.md#billing-reader) z uživatele *Alain \@ example.com* v oboru skupiny pro správu.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="rest-api"></a>REST API

V REST API odeberete přiřazení role pomocí [přiřazení rolí – odstranit](/rest/api/authorization/roleassignments/delete).

1. Získejte identifikátor přiřazení role (GUID). Tento identifikátor se vrátí při prvním vytvoření přiřazení role nebo ho můžete získat pomocí seznamu přiřazení rolí.

1. Začněte s následujícím požadavkem:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem pro odebrání přiřazení role.

    > [!div class="mx-tableFixed"]
    > | Obor | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleAssignmentId}* identifikátorem GUID přiřazení role.

Následující požadavek Odstraní zadané přiřazení role v oboru předplatného:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

Následující příklad ukazuje příklad výstupu:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="arm-template"></a>Šablona ARM

Neexistuje způsob, jak odebrat přiřazení role pomocí šablony Azure Resource Manager (šablona ARM). Chcete-li odebrat přiřazení role, je nutné použít jiné nástroje, například Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure nebo REST API.

## <a name="next-steps"></a>Další kroky

- [Vypsání přiřazení rolí Azure pomocí Azure Portal](role-assignments-list-portal.md)
- [Vypsání přiřazení rolí Azure pomocí Azure PowerShell](role-assignments-list-powershell.md)
- [Řešení potíží s Azure RBAC](troubleshooting.md)
