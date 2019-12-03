---
title: Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a Azure CLI
description: Zjistěte, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí řízení přístupu na základě role (RBAC) Azure a Azure CLI.
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
ms.openlocfilehash: 0351721283df68fde910ae16b16d567954c3e6fb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707901"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] tomto článku se dozvíte, jak přiřadit role pomocí rozhraní příkazového řádku Azure CLI.

## <a name="prerequisites"></a>Předpoklady

Chcete-li přidat nebo odebrat přiřazení rolí, je nutné mít následující:

- oprávnění `Microsoft.Authorization/roleAssignments/write` a `Microsoft.Authorization/roleAssignments/delete`, jako je například správce nebo [vlastník](built-in-roles.md#owner) [přístupu uživatele](built-in-roles.md#user-access-administrator)
- [Bash v Azure Cloud Shell](/azure/cloud-shell/overview) nebo [Azure CLI](/cli/azure)

## <a name="get-object-ids"></a>Získat ID objektů

Pokud chcete přidat nebo odebrat přiřazení rolí, možná budete muset zadat jedinečné ID objektu. ID má formát: `11111111-1111-1111-1111-111111111111`. ID můžete získat pomocí Azure Portal nebo rozhraní příkazového řádku Azure CLI.

### <a name="user"></a>Uživatel

Pokud chcete získat ID objektu pro uživatele Azure AD, můžete použít příkaz [AZ AD User show](/cli/azure/ad/user#az-ad-user-show).

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Skupina

Pokud chcete získat ID objektu pro skupinu Azure AD, můžete použít příkaz [AZ AD Group show](/cli/azure/ad/group#az-ad-group-show) nebo [AZ AD Group list](/cli/azure/ad/group#az-ad-group-list).

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Aplikace

Pokud chcete získat ID objektu pro instanční objekt služby Azure AD (identita, kterou používá aplikace), můžete použít příkaz [AZ AD SP list](/cli/azure/ad/sp#az-ad-sp-list). U instančního objektu použijte ID objektu, **nikoli** ID aplikace.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Přidání přiřazení role

Pokud chcete udělit přístup, přidejte přiřazení role ve RBAC.

### <a name="user-at-a-resource-group-scope"></a>Uživatel v oboru skupiny prostředků

Chcete-li přidat přiřazení role pro uživatele v oboru skupiny prostředků, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

Následující příklad přiřadí roli *Přispěvatel virtuálních počítačů* k *patlong\@uživatele contoso.com* v oboru skupiny prostředků *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Použití jedinečného ID role

V některých případech se může stát, že se název role změní například takto:

- Používáte vlastní roli a Vy se rozhodnete název změnit.
- V názvu používáte roli Preview, která má **(Preview)** . Po uvolnění role se role přejmenuje.

> [!IMPORTANT]
> Verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I když je role přejmenována, ID role se nezmění. Pokud k vytváření přiřazení rolí používáte skripty nebo automatizaci, je vhodné místo názvu role použít jedinečné ID role. Proto pokud je role přejmenována, vaše skripty budou pravděpodobnější, že budou fungovat.

Pokud chcete přidat přiřazení role pomocí jedinečného ID role místo názvu role, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

Následující příklad přiřadí roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) k uživateli *patlong\@contoso.com* v oboru skupiny prostředků *Pharma-Sales* . Pokud chcete získat jedinečné ID role, můžete použít příkaz [AZ role definition list](/cli/azure/role/definition#az-role-definition-list) nebo informace [o předdefinovaných rolích pro prostředky Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Skupina v oboru předplatného

Chcete-li přidat přiřazení role pro skupinu, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Informace o tom, jak získat ID objektu skupiny, najdete v tématu [získání ID objektů](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Následující příklad přiřadí roli *Čtenář* ke skupině *týmu Ann Mack* s ID 22222222-2222-2222-2222-222222222222 v oboru předplatného.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Seskupit v oboru prostředku

Chcete-li přidat přiřazení role pro skupinu, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Informace o tom, jak získat ID objektu skupiny, najdete v tématu [získání ID objektů](#get-object-ids).

Následující příklad přiřadí roli *Přispěvatel virtuálních počítačů* ke skupině *Ann Mack týmu* s ID 22222222-2222-2222-2222-222222222222 v oboru prostředků pro virtuální síť s názvem *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Aplikace v oboru skupiny prostředků

Chcete-li přidat přiřazení role pro aplikaci, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Informace o tom, jak získat ID objektu aplikace, najdete v tématu [získání ID objektů](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

Následující příklad přiřadí roli *Přispěvatel virtuálních počítačů* k aplikaci s ID objektu 44444444-4444-4444-4444-444444444444 v oboru skupiny prostředků *Pharma-Sales* .

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Uživatel v oboru předplatného

Pokud chcete přidat přiřazení role pro uživatele v oboru předplatného, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Pokud chcete získat ID předplatného, najdete ho v okně **předplatná** v Azure Portal nebo můžete použít příkaz [AZ Account list](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

Následující příklad přiřadí roli *Čtenář* k uživateli *annm\@example.com* v oboru předplatného.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Uživatel v oboru skupiny pro správu

Chcete-li přidat přiřazení role pro uživatele v oboru skupiny pro správu, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Chcete-li získat ID skupiny pro správu, můžete ji najít v okně **skupiny pro správu** v Azure Portal nebo můžete použít [příkaz AZ Account Management-Group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Následující příklad přiřadí roli *Čtenář fakturace* pro uživatele *Alain\@example.com* v oboru skupiny pro správu.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Nový instanční objekt

Pokud vytvoříte nový instanční objekt a hned se pokusíte přiřadit roli k tomuto instančnímu objektu, toto přiřazení role může v některých případech selhat. Pokud například použijete skript k vytvoření nové spravované identity a potom se pokusíte přiřadit roli k tomuto instančnímu objektu, přiřazení role se nemusí zdařit. Důvodem této chyby je nejspíš zpoždění replikace. Instanční objekt se vytvoří v jedné oblasti. přiřazení role se ale může vyskytnout v jiné oblasti, která ještě nereplikoval instanční objekt. Chcete-li tento scénář vyřešit, je nutné při vytváření přiřazení role zadat typ objektu zabezpečení.

Chcete-li přidat přiřazení role, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create), zadejte hodnotu pro `--assignee-object-id`a pak nastavte `--assignee-principal-type` na `ServicePrincipal`.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Následující příklad přiřadí roli *Přispěvatel virtuálních počítačů* k spravované identitě *MSI-test* v oboru skupiny prostředků *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

Pokud chcete odebrat přístup, odeberte přiřazení role v RBAC pomocí funkce [AZ role Assignment Delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Následující příklad odebere přiřazení role *Přispěvatel virtuálních počítačů* z uživatele *patlong\@contoso.com* ve skupině prostředků *Pharma-Sales* :

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Následující příklad odebere roli *Čtenář* z *týmové skupiny Ann Mack* s ID 22222222-2222-2222-2222-222222222222 v oboru předplatného. Informace o tom, jak získat ID objektu skupiny, najdete v tématu [získání ID objektů](#get-object-ids).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

Následující příklad odebere roli *Čtenář fakturace* z uživatele *Alain\@example.com* v oboru skupiny pro správu. ID skupiny pro správu můžete získat pomocí ovládacího panelu [AZ Account Management-Group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Další kroky

- [Vypsání přiřazení rolí pomocí Azure RBAC a Azure CLI](role-assignments-list-cli.md)
- [Použití rozhraní příkazového řádku Azure ke správě prostředků a skupin prostředků Azure](../azure-resource-manager/cli-azure-resource-manager.md)
