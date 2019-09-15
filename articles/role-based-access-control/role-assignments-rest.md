---
title: Správa přístupu k prostředkům Azure pomocí RBAC a REST API – Azure | Microsoft Docs
description: Naučte se spravovat přístup k prostředkům Azure pro uživatele, skupiny a aplikace pomocí řízení přístupu na základě role (RBAC) a REST API. To zahrnuje výpis přístupu, udělení přístupu a odebrání přístupu.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 86ee030e8c97cf3033b9d2d76b8125c64ecf8065
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996474"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Správa přístupu k prostředkům Azure pomocí RBAC a REST API

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, jakým můžete spravovat přístup k prostředkům Azure. Tento článek popisuje, jak spravovat přístup pro uživatele, skupiny a aplikace pomocí RBAC a REST API.

## <a name="list-access"></a>Výpis přístupu

V části RBAC pro vypsání přístupu k seznamu se zobrazí seznam přiřazení rolí. K vypsání přiřazení rolí použijte jedno ze [seznamu přiřazení rolí – seznam](/rest/api/authorization/roleassignments/list) rozhraní REST API. K upřesnění výsledků zadáte obor a volitelný filtr.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete zobrazit seznam přiřazení rolí.

    | Scope | type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    | `subscriptions/{subscriptionId1}` | Předplatné |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

    V předchozím příkladu je Microsoft. Web poskytovatelem prostředků, který odkazuje na instanci App Service. Podobně můžete použít jiné poskytovatele prostředků a zadat obor. Další informace najdete v tématech [poskytovatelé a typy prostředků Azure](../azure-resource-manager/resource-manager-supported-services.md) a podporované [Azure Resource Manager operace poskytovatele prostředků](resource-provider-operations.md).  
     
1. Nahraďte *{Filter}* podmínkou, kterou chcete použít k filtrování seznamu přiřazení role.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=atScope()` | Vypíše přiřazení rolí jenom pro zadaný obor, včetně přiřazení rolí v podoborech. |
    | `$filter=principalId%20eq%20'{objectId}'` | Vypisuje přiřazení rolí pro zadaného uživatele, skupinu nebo instanční objekt. |
    | `$filter=assignedTo('{objectId}')` | Vypisuje přiřazení rolí pro zadaného uživatele nebo instanční objekt. Pokud je uživatel členem skupiny, která má přiřazení role, zobrazí se také toto přiřazení role. Tento filtr je přenosný pro skupiny, což znamená, že pokud je uživatel členem skupiny a tato skupina je členem jiné skupiny, která má přiřazení role, bude toto přiřazení role také uvedené. Tento filtr přijímá pouze ID objektu uživatele nebo instančního objektu. Nelze předat ID objektu pro skupinu. |

## <a name="grant-access"></a>Udělit přístup

V RBAC se přístup uděluje vytvořením přiřazení role. K vytvoření přiřazení role použijte [přiřazení rolí – vytvořit](/rest/api/authorization/roleassignments/create) REST API a zadejte objekt zabezpečení, definici role a obor. K volání tohoto rozhraní API musíte mít přístup k `Microsoft.Authorization/roleAssignments/write` této operaci. Z předdefinovaných rolí má přístup k této operaci udělen pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

1. Použijte REST API [Definice rolí – seznam](/rest/api/authorization/roledefinitions/list) , nebo se podívejte na [předdefinované role](built-in-roles.md) , abyste získali identifikátor definice role, kterou chcete přiřadit.

1. Pomocí nástroje GUID vygenerujte jedinečný identifikátor, který se použije pro identifikátor přiřazení role. Identifikátor má formát:`00000000-0000-0000-0000-000000000000`

1. Začněte s následujícím požadavkem a textem:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem pro přiřazení role.

    | Scope | type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    | `subscriptions/{subscriptionId1}` | Předplatné |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Nahraďte *{roleAssignmentName}* identifikátorem GUID přiřazení role.

1. V textu žádosti nahraďte *{Scope}* oborem pro přiřazení role.

    | Scope | type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    | `subscriptions/{subscriptionId1}` | Předplatné |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Nahraďte *{roleDefinitionId}* identifikátorem definice role.

1. Nahraďte *{principalId}* identifikátorem objektu uživatele, skupiny nebo instančního objektu, ke kterému se má přiřadit role.

## <a name="remove-access"></a>Odebrat přístup

V RBAC se přístup odebírá odebrání přiřazení role. K odebrání přiřazení role použijte [přiřazení rolí – odstranit](/rest/api/authorization/roleassignments/delete) REST API. K volání tohoto rozhraní API musíte mít přístup k `Microsoft.Authorization/roleAssignments/delete` této operaci. Z předdefinovaných rolí má přístup k této operaci udělen pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

1. Získejte identifikátor přiřazení role (GUID). Tento identifikátor se vrátí při prvním vytvoření přiřazení role nebo ho můžete získat pomocí seznamu přiřazení rolí.

1. Začněte s následujícím požadavkem:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem pro odebrání přiřazení role.

    | Scope | type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    | `subscriptions/{subscriptionId1}` | Předplatné |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Nahraďte *{roleAssignmentName}* identifikátorem GUID přiřazení role.

## <a name="next-steps"></a>Další kroky

- [Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)
- [Vytváření vlastních rolí pro prostředky Azure pomocí REST API](custom-roles-rest.md)
