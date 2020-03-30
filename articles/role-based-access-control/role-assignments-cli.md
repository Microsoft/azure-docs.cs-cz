---
title: Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a Azure CLI
description: Zjistěte, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí řízení přístupu na základě rolí Azure (RBAC) a Azure CLI.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b32df50715d5e7276861e0696df1bd6ceb3f684e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245665"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Tento článek popisuje, jak přiřadit role pomocí rozhraní příkazového příkazu Azure.

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat nebo odebrat přiřazení rolí, musíte mít:

- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, například [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) nebo [Vlastník](built-in-roles.md#owner)
- [Bash v Azure Cloud Shell](/azure/cloud-shell/overview) nebo Azure [CLI](/cli/azure)

## <a name="get-object-ids"></a>Získání ID objektů

Chcete-li přidat nebo odebrat přiřazení rolí, může být nutné zadat jedinečné ID objektu. ID má formát: `11111111-1111-1111-1111-111111111111`. ID můžete získat pomocí portálu Azure nebo Azure CLI.

### <a name="user"></a>Uživatel

Chcete-li získat ID objektu pro uživatele Azure AD, můžete použít [zobrazení az reklamy](/cli/azure/ad/user#az-ad-user-show).

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Skupina

Pokud chcete získat ID objektu pro skupinu Azure AD, můžete použít [zobrazení az reklamní sestavy](/cli/azure/ad/group#az-ad-group-show) nebo seznam [reklamních sekt az](/cli/azure/ad/group#az-ad-group-list).

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Aplikace

Chcete-li získat ID objektu pro instanční objekt služby Azure AD (identita používaná aplikací), můžete použít [seznam az ad ad sp](/cli/azure/ad/sp#az-ad-sp-list). Pro instanční objekt použijte ID objektu a **ne** ID aplikace.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

V RBAC, chcete-li udělit přístup, přidáte přiřazení role.

### <a name="user-at-a-resource-group-scope"></a>Uživatel v oboru skupiny prostředků

Chcete-li přidat přiřazení role pro uživatele v oboru skupiny zdrojů, použijte [vytvoření přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

Následující příklad přiřadí roli *přispěvatele virtuálního počítače* *uživateli patlong\@contoso.com* v oboru skupiny prostředků *pharma-sales:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Použití jedinečného ID role

Existuje několik případů, kdy se název role může změnit, například:

- Používáte vlastní roli a rozhodnete se změnit název.
- Používáte roli náhledu, která má **(Náhled)** v názvu. Po vydání role je role přejmenována.

> [!IMPORTANT]
> Verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I v případě, že je role přejmenována, ID role se nezmění. Pokud k vytvoření přiřazení rolí používáte skripty nebo automatizaci, je osvědčeným postupem použít jedinečné ID role namísto názvu role. Proto pokud role je přejmenován, skripty jsou více pravděpodobné, že pracovat.

Chcete-li přidat přiřazení role pomocí jedinečného ID role namísto názvu role, použijte [vytvoření přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

Následující příklad přiřadí roli [přispěvatele virtuálního počítače](built-in-roles.md#virtual-machine-contributor) *uživateli patlong\@contoso.com* v oboru skupiny prostředků *pharma-sales.* Chcete-li získat jedinečné ID role, můžete použít [seznam definic rolí az](/cli/azure/role/definition#az-role-definition-list) nebo zobrazit [seznam předdefinovaných rolí pro prostředky Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Skupina v oboru předplatného

Chcete-li přidat přiřazení role pro skupinu, použijte [vytvoření přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create). Informace o tom, jak získat ID objektu skupiny, naleznete v tématu [Získání ID objektu](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Následující příklad přiřadí roli *čtenáře* skupině *Ann Mack Team* s ID 22222222-2222-2222-2222-2222-22222222222222 v oboru předplatného.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Seskupení v oboru prostředků

Chcete-li přidat přiřazení role pro skupinu, použijte [vytvoření přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create). Informace o tom, jak získat ID objektu skupiny, naleznete v tématu [Získání ID objektu](#get-object-ids).

Následující příklad přiřadí roli *přispěvatele virtuálního počítače* skupině *Ann Mack Team* s ID 22222222-2222-2222-2222-22222222222222 v oboru prostředků pro virtuální síť s názvem *pharma-sales-project-network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Aplikace v oboru skupiny prostředků

Chcete-li přidat přiřazení role pro aplikaci, použijte [vytvoření přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create). Informace o tom, jak získat ID objektu aplikace, naleznete v tématu [Získání ID objektu](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

Následující příklad přiřadí roli *přispěvatele virtuálního počítače* aplikaci s ID objektu 44444444-4444-4444-4444444444444v oboru *pharma-sales* resource group.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Uživatel v oboru předplatného

Chcete-li přidat přiřazení role pro uživatele v oboru předplatného, použijte [vytvoření přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create). Chcete-li získat ID předplatného, najdete ho na okně **Předplatná** na webu Azure Portal nebo můžete použít [seznam účtů az](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

Následující příklad přiřadí roli *čtenáře* *uživateli\@annm example.com* v oboru předplatného.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Uživatel v oboru skupiny pro správu

Chcete-li přidat přiřazení role pro uživatele v oboru skupiny pro správu, použijte [vytvoření přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create). Chcete-li získat ID skupiny pro správu, najdete ho v okně **Skupiny pro správu** na webu Azure Portal nebo můžete použít [seznam skupin pro správu účtů az](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Následující příklad přiřadí roli *Čtenář fakturace* *\@uživateli alain example.com* v oboru skupiny pro správu.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Nový instanční objekt

Pokud vytvoříte nový instanční objekt a okamžitě se pokusíte přiřadit roli tomuto instančnímu objektu, může toto přiřazení role v některých případech selhat. Pokud například použijete skript k vytvoření nové spravované identity a potom se pokusíte přiřadit roli tomuto instančnímu objektu, může přiřazení role selhat. Důvodem této chyby je pravděpodobně zpoždění replikace. Instanční objekt je vytvořen v jedné oblasti; přiřazení role však může dojít v jiné oblasti, která dosud replikovat instanční objekt. Chcete-li tento scénář vyřešit, měli byste při vytváření přiřazení role zadat hlavní typ.

Chcete-li přidat přiřazení role, použijte [vytvoření přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create), zadejte hodnotu pro `--assignee-object-id`a nastavte `--assignee-principal-type` na . `ServicePrincipal`

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Následující příklad přiřadí roli *přispěvatele virtuálního počítače* spravované identitě *msi-test* v oboru skupiny *prostředků pharma-sales:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

Chcete-li odebrat přístup v rbac, odeberte přiřazení role pomocí [odstranění přiřazení role az](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Následující příklad odebere přiřazení role *přispěvatele virtuálního počítače* z *\@patlong contoso.com* uživatele mj. *pharma-sales*

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Následující příklad odebere roli *čtenáře* ze skupiny *Ann Mack Team* s ID 22222222-2222-2222-2222-2222-2222222222222 v oboru předplatného. Informace o tom, jak získat ID objektu skupiny, naleznete v tématu [Získání ID objektu](#get-object-ids).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

Následující příklad odebere roli *čtenář fakturace* z *uživatele example.com v\@* oboru skupiny pro správu. Chcete-li získat ID skupiny pro správu, můžete použít [seznam skupin pro správu účtů az](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Další kroky

- [Seznam přiřazení rolí pomocí Azure RBAC a Azure CLI](role-assignments-list-cli.md)
- [Použití azure cli ke správě prostředků a skupin prostředků Azure](../azure-resource-manager/cli-azure-resource-manager.md)
