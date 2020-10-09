---
title: Vysvětlení oboru pro Azure RBAC
description: Přečtěte si o oboru řízení přístupu na základě role Azure (Azure RBAC) a o tom, jak určit rozsah prostředku.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/08/2020
ms.author: rolyon
ms.openlocfilehash: ad906e3665c6ffc354cf6292c2559d1184037594
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856142"
---
# <a name="understand-scope-for-azure-rbac"></a>Vysvětlení oboru pro Azure RBAC

*Obor* je sada prostředků, na které se přistupuje. Když přiřadíte roli, je důležité pochopit rozsah, abyste mohli objektu zabezpečení udělit jenom přístup, který skutečně potřebuje. Omezením oboru omezíte, jaké prostředky budou ohroženy, pokud dojde k ohrožení zabezpečení objektu zabezpečení.

## <a name="scope-levels"></a>Úrovně rozsahu

V Azure můžete zadat obor na čtyřech úrovních: [skupina pro správu](../governance/management-groups/overview.md), předplatné, [Skupina prostředků](../azure-resource-manager/management/overview.md#resource-groups)a prostředek. Obory jsou strukturovány ve vztahu nadřazený-podřízený obor. Jednotlivé úrovně hierarchie usnadňují rozsah konkrétního rozsahu. Role můžete přiřadit na kterékoli z těchto úrovní rozsahu. Úroveň, kterou vyberete, určuje, jak široce je role použita. Nižší úrovně dědí oprávnění role z vyšších úrovní. 

![Obor přiřazení role](./media/scope-overview/rbac-scope-no-label.png)

Skupiny pro správu jsou úrovní rozsahu nad odběry, ale skupiny pro správu podporují složitější hierarchie. Následující diagram znázorňuje příklad hierarchie skupin pro správu a předplatných, které můžete definovat. Další informace o skupinách pro správu najdete v tématu [co jsou skupiny pro správu Azure](../governance/management-groups/overview.md).

![Skupina pro správu a hierarchie předplatných](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>Formát oboru

Pokud přidáte přiřazení rolí pomocí příkazového řádku, musíte zadat obor. Pro nástroje příkazového řádku je rozsah potenciálně dlouhý řetězec, který identifikuje přesný rozsah přiřazení role. V Azure Portal je tento obor obvykle uveden jako *ID prostředku*.

Obor se skládá z řady identifikátorů oddělených znakem lomítka (/). Tento řetězec si můžete představit jako vyjadřující následující hierarchii, kde text bez zástupných znaků ( `{}` ) jsou pevné identifikátory:

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` je ID předplatného, které se má použít (identifikátor GUID).
- `{resourcesGroupName}` je název obsahující skupiny prostředků.
- `{providerName}` je název [poskytovatele prostředků](../azure-resource-manager/management/azure-services-resource-providers.md) , který zpracovává prostředek, pak `{resourceType}` a `{resourceSubType*}` identifikuje další úrovně v rámci tohoto poskytovatele prostředků.
- `{resourceName}` je poslední část řetězce, která identifikuje konkrétní prostředek.

Skupiny pro správu jsou vyšší úrovně předplatných a mají nejširší (nejméně konkrétní) rozsah. Přiřazení rolí na této úrovni se vztahují na předplatná v rámci skupiny pro správu. Rozsah pro skupinu pro správu má následující formát:

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>Příklady oboru

> [!div class="mx-tableFixed"]
> | Rozsah | Příklad |
> | --- | --- |
> | Skupina pro správu | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | Předplatné | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Skupina prostředků | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | Prostředek | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>Jak určit rozsah prostředku

Určení rozsahu skupiny pro správu, předplatného nebo skupiny prostředků je poměrně jednoduché. Stačí znát název a ID předplatného. Určení rozsahu pro prostředek ale trvá trochu víc práce. Tady je několik způsobů, jak můžete určit rozsah prostředku.

- V Azure Portal otevřete prostředek a potom se podívejte na vlastnosti. Prostředek by měl uvést **ID prostředku** , kde můžete obor určit. Tady jsou například ID prostředků pro účet úložiště.

    ![ID prostředků pro účet úložiště v Azure Portal](./media/scope-overview/scope-resource-id.png)

- Další možností je použít Azure Portal k dočasnému přiřazení role v oboru prostředků a potom použít [Azure PowerShell](role-assignments-list-powershell.md) nebo [Azure CLI](role-assignments-list-cli.md) k vypsání přiřazení role. Ve výstupu bude obor uveden jako vlastnost.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="next-steps"></a>Další kroky

- [Kroky pro přidání přiřazení role](role-assignments-steps.md)
- [Poskytovatelé prostředků pro služby Azure](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Co jsou skupiny pro správu Azure?](../governance/management-groups/overview.md)
