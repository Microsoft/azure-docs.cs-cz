---
title: Přidání nebo odebrání přiřazení rolí Azure pomocí Azure CLI – Azure RBAC
description: Naučte se, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí Azure CLI a řízení přístupu na základě role Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 10060e0dc6595a4d59f3968fa324ca40c91a7a39
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033795"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-cli"></a>Přidání nebo odebrání přiřazení rolí v Azure pomocí Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Tento článek popisuje, jak přiřadit role pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat nebo odebrat přiřazení rolí, je nutné mít následující:

- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, jako je například správce nebo [vlastník](built-in-roles.md#owner) [přístupu uživatele](built-in-roles.md#user-access-administrator)
- [Bash v Azure Cloud Shell](../cloud-shell/overview.md) nebo [Azure CLI](/cli/azure)

## <a name="steps-to-add-a-role-assignment"></a>Kroky pro přidání přiřazení role

Když v Azure RBAC udělíte přístup, přidáte přiřazení role. Přiřazení role se skládá ze tří prvků: objekt zabezpečení, definice role a obor. Chcete-li přidat přiřazení role, postupujte podle těchto kroků.

### <a name="step-1-determine-who-needs-access"></a>Krok 1: určení toho, kdo potřebuje přístup

Roli můžete přiřadit uživateli, skupině, objektu služby nebo spravované identitě. Chcete-li přidat přiřazení role, bude pravděpodobně nutné zadat jedinečné ID objektu. ID má formát: `11111111-1111-1111-1111-111111111111` . ID můžete získat pomocí Azure Portal nebo rozhraní příkazového řádku Azure CLI.

**Uživatel**

Pro uživatele Azure AD Získejte hlavní název uživatele, například *patlong \@ contoso.com* nebo ID objektu uživatele. Chcete-li získat ID objektu, můžete použít příkaz [AZ AD User show](/cli/azure/ad/user#az_ad_user_show).

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**Skupina**

Pro skupinu Azure AD potřebujete ID objektu skupiny. Chcete-li získat ID objektu, můžete použít příkaz [AZ AD Group show](/cli/azure/ad/group#az_ad_group_show) nebo [AZ AD Group list](/cli/azure/ad/group#az_ad_group_list).

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**Instanční objekt**

Pro instanční objekt služby Azure AD (identita používaný aplikací) potřebujete ID instančního objektu. Chcete-li získat ID objektu, můžete použít příkaz [AZ AD SP list](/cli/azure/ad/sp#az_ad_sp_list). U instančního objektu použijte ID objektu, **nikoli** ID aplikace.

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**Spravovaná identita**

Pro spravovanou identitu přiřazenou systémem nebo uživatelem budete potřebovat ID objektu. Chcete-li získat ID objektu, můžete použít příkaz [AZ AD SP list](/cli/azure/ad/sp#az_ad_sp_list).

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

Pokud chcete jenom vypsat spravované identity přiřazené uživateli, můžete použít příkaz [AZ identity list](/cli/azure/identity#az_identity_list).

```azurecli
az identity list
```
    
### <a name="step-2-find-the-appropriate-role"></a>Krok 2: vyhledání příslušné role

Oprávnění se seskupují do rolí. Můžete vybrat ze seznamu několika [předdefinovaných rolí Azure](built-in-roles.md) nebo můžete použít vlastní role. Osvědčeným postupem je udělit přístup s nejmenším možným oprávněním, takže se nepřiřazují širší role.

Chcete-li zobrazit seznam rolí a získat jedinečné ID role, můžete použít příkaz [AZ role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

Tady je postup, jak zobrazit podrobnosti konkrétní role.

```azurecli
az role definition list --name "{roleName}"
```

Další informace najdete v tématu [seznam definic rolí Azure](role-definitions-list.md#azure-cli).
 
### <a name="step-3-identify-the-needed-scope"></a>Krok 3: určení potřebného oboru

Azure poskytuje čtyři úrovně rozsahu: prostředek, [Skupina prostředků](../azure-resource-manager/management/overview.md#resource-groups), předplatné a [skupina pro správu](../governance/management-groups/overview.md). Osvědčeným postupem je udělit přístup s nejmenším možným oprávněním, takže se nepřiřazujte role v širším rozsahu. Další informace o rozsahu najdete v tématu [pochopení oboru](scope-overview.md).

**Obor prostředku**

V případě oboru prostředků potřebujete ID prostředku pro daný prostředek. ID prostředku najdete tak, že si prohlížíte vlastnosti prostředku ve Azure Portal. ID prostředku má následující formát.

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**Rozsah skupiny prostředků**

Pro rozsah skupiny prostředků potřebujete název skupiny prostředků. Název najdete na stránce **skupiny prostředků** v Azure Portal nebo můžete použít [seznam AZ Group list](/cli/azure/group#az_group_list).

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**Rozsah předplatného** 

Pro rozsah předplatného potřebujete ID předplatného. ID můžete najít na stránce **předplatná** v Azure Portal nebo můžete použít [AZ Account list](/cli/azure/account#az_account_list).

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**Rozsah skupiny pro správu** 

Pro rozsah skupiny pro správu potřebujete název skupiny pro správu. Název najdete na stránce **skupiny pro správu** v Azure Portal nebo můžete použít [příkaz AZ Account Management-Group list](/cli/azure/account/management-group#az_account_management_group_list).

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-add-role-assignment"></a>Krok 4: přidání přiřazení role

Přiřazení role přidáte pomocí příkazu [AZ role Assignment Create](/cli/azure/role/assignment#az_role_assignment_create) . V závislosti na rozsahu má příkaz obvykle jeden z následujících formátů.

**Obor prostředku**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**Rozsah skupiny prostředků**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**Rozsah předplatného** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**Rozsah skupiny pro správu** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

Následující příklad ukazuje příklad výstupu při přiřazení role [Přispěvatel virtuálního počítače](built-in-roles.md#virtual-machine-contributor) uživateli v oboru skupiny prostředků.

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="add-role-assignment-examples"></a>Přidat příklady přiřazení rolí

#### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Přidání přiřazení role pro všechny kontejnery objektů BLOB v oboru prostředků účtu úložiště

Přiřadí roli [Přispěvatel dat objektu BLOB služby Storage](built-in-roles.md#storage-blob-data-contributor) k instančnímu objektu s ID *55555555-5555-5555-5555-555555555555* v oboru prostředků pro účet úložiště s názvem *storage12345*.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

#### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>Přidání přiřazení role pro konkrétní obor prostředku kontejneru objektů BLOB

Přiřadí roli [Přispěvatel dat objektu BLOB služby Storage](built-in-roles.md#storage-blob-data-contributor) k instančnímu objektu s ID *55555555-5555-5555-5555-555555555555* v oboru prostředků pro kontejner objektů BLOB s názvem *BLOB-Container-01*.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

#### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Přidání přiřazení role pro skupinu v konkrétním oboru prostředků virtuální sítě

Přiřadí roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) ke skupině *týmu Ann Mack* s ID 22222222-2222-2222-2222-222222222222 v oboru prostředků pro virtuální síť s názvem *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

#### <a name="add-role-assignment-for-a-user-at-a-resource-group-scope"></a>Přidání přiřazení role pro uživatele v oboru skupiny prostředků

Přiřadí roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) , aby *patlong uživatele \@ contoso.com* v oboru skupiny prostředků *Pharma-Sales* .

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Přidání přiřazení role pro uživatele pomocí jedinečného ID role v oboru skupiny prostředků

V některých případech se může stát, že se název role změní například takto:

- Používáte vlastní roli a Vy se rozhodnete název změnit.
- V názvu používáte roli Preview, která má **(Preview)** . Po uvolnění role se role přejmenuje.

I když je role přejmenována, ID role se nezmění. Pokud k vytváření přiřazení rolí používáte skripty nebo automatizaci, je vhodné místo názvu role použít jedinečné ID role. Proto pokud je role přejmenována, vaše skripty budou pravděpodobnější, že budou fungovat.

Následující příklad přiřadí roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) k uživateli *patlong \@ contoso.com* v oboru skupiny prostředků *Pharma-Sales* .

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-all-blob-containers-at-a-resource-group-scope"></a>Přidání přiřazení role pro všechny kontejnery objektů BLOB v oboru skupiny prostředků

Přiřadí roli [Přispěvatel dat objektu BLOB služby Storage](built-in-roles.md#storage-blob-data-contributor) k instančnímu objektu s ID *55555555-5555-5555-5555-555555555555* v oboru skupiny prostředků *RG úložiště* .

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

Alternativně můžete zadat plně kvalifikovanou skupinu prostředků s `--scope` parametrem:

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

#### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>Přidání přiřazení role pro aplikaci do oboru skupiny prostředků

Přiřadí roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) k aplikaci s ID objektu zabezpečení služby 44444444-4444-4444-4444-444444444444 v oboru skupiny prostředků *Pharma-Sales* .

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope"></a>Přidání přiřazení role pro nový instanční objekt v oboru skupiny prostředků

Pokud vytvoříte nový instanční objekt a hned se pokusíte přiřadit roli k tomuto instančnímu objektu, toto přiřazení role může v některých případech selhat. Pokud například použijete skript k vytvoření nové spravované identity a potom se pokusíte přiřadit roli k tomuto instančnímu objektu, přiřazení role se nemusí zdařit. Důvodem této chyby je nejspíš zpoždění replikace. Instanční objekt se vytvoří v jedné oblasti. přiřazení role se ale může vyskytnout v jiné oblasti, která ještě nereplikoval instanční objekt. Chcete-li tento scénář vyřešit, je nutné při vytváření přiřazení role zadat typ objektu zabezpečení.

Chcete-li přidat přiřazení role, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az_role_assignment_create), zadejte hodnotu pro `--assignee-object-id` a pak nastavte `--assignee-principal-type` na `ServicePrincipal` .

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

Následující příklad přiřadí roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) k spravované identitě *MSI-test* v oboru skupiny prostředků *Pharma-Sales* :

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>Přidání přiřazení role pro uživatele v oboru předplatného

Přiřadí roli [Čtenář](built-in-roles.md#reader) k uživateli *annm \@ example.com* v oboru předplatného.

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-a-group-at-a-subscription-scope"></a>Přidání přiřazení role pro skupinu v oboru předplatného

Přiřadí roli [čtenáře](built-in-roles.md#reader) ke skupině *týmu Ann Mack* s ID 22222222-2222-2222-2222-222222222222 v oboru předplatného.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-all-blob-containers-at-a-subscription-scope"></a>Přidání přiřazení role pro všechny kontejnery objektů BLOB v oboru předplatného

Přiřadí roli [čtečky dat objektů BLOB úložiště](built-in-roles.md#storage-blob-data-reader) k *Alain \@ example.com* uživatele v oboru předplatného.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>Přidání přiřazení role pro uživatele v oboru skupiny pro správu

Přiřadí roli [Čtenář fakturace](built-in-roles.md#billing-reader) pro uživatele *Alain \@ example.com* v oboru skupiny pro správu.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

Chcete-li v Azure RBAC odebrat přístup, odeberte přiřazení role pomocí funkce [AZ role Assignment Delete](/cli/azure/role/assignment#az_role_assignment_delete).

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

## <a name="next-steps"></a>Další kroky

- [Vypsání přiřazení rolí Azure pomocí Azure CLI](role-assignments-list-cli.md)
- [Použití rozhraní příkazového řádku Azure ke správě prostředků a skupin prostředků Azure](../azure-resource-manager/management/manage-resources-cli.md)
