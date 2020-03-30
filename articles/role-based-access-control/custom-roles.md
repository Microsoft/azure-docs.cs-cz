---
title: Vlastní role pro prostředky Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit vlastní role pomocí řízení přístupu na základě rolí (RBAC) pro jemně odstupňovanou správu přístupu k prostředkům Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062173"
---
# <a name="custom-roles-for-azure-resources"></a>Vlastní role pro prostředky Azure

> [!IMPORTANT]
> Přidání skupiny `AssignableScopes` pro správu do aplikace je aktuálně ve verzi Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud [předdefinované role pro prostředky Azure](built-in-roles.md) nesplňují specifické potřeby vaší organizace, můžete si vytvořit vlastní role. Stejně jako předdefinované role můžete přiřadit vlastní role uživatelům, skupinám a instančním objektům ve skupině pro správu, předplatném a oborech skupiny prostředků.

Vlastní role lze sdílet mezi předplatnými, které důvěřují stejnému adresáři Azure AD. Existuje limit **5 000** vlastních rolí na adresář. (Pro Azure Germany a Azure China 21Vianet je limit 2 000 vlastních rolí.) Vlastní role se můžou vytvářet pomocí portálu Azure (Preview), Azure PowerShellu, rozhraní API Azure nebo rozhraní REST API.

## <a name="custom-role-example"></a>Příklad vlastní role

Následující ukazuje, jak vypadá vlastní role, jak je zobrazena ve formátu JSON. Tuto vlastní roli lze použít pro monitorování a restartování virtuálních počítačů.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Když vytvoříte vlastní roli, zobrazí se na webu Azure portal s oranžovou ikonou prostředku.

![Ikona vlastní role](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Postup vytvoření vlastní role

1. Rozhodněte se, jak chcete vytvořit vlastní roli

    Vlastní role můžete vytvořit pomocí [portálu Azure Portal](custom-roles-portal.md) (Preview), [Azure PowerShellu](custom-roles-powershell.md), [rozhraní API Azure](custom-roles-cli.md)nebo rozhraní [REST API](custom-roles-rest.md).

1. Určení potřebných oprávnění

    Při vytváření vlastní role je třeba znát operace zprostředkovatele prostředků, které jsou k dispozici pro definování vašich oprávnění. Seznam operací najdete v tématu [Operace zprostředkovatele prostředků Správce prostředků Azure](resource-provider-operations.md). Přidáte operace do `Actions` vlastností `NotActions` nebo [definice role](role-definitions.md). Pokud máte datové operace, přidáte je `DataActions` `NotDataActions` do vlastností nebo.

1. Vytvoření vlastní role

    Obvykle začnete s existující předdefinovanou roli a pak ji upravit podle svých potřeb. Potom použijete příkazy pro vytvoření vlastní role [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) nebo [definice role az.](/cli/azure/role/definition#az-role-definition-create) Chcete-li vytvořit vlastní roli, `Microsoft.Authorization/roleDefinitions/write` musíte `AssignableScopes`mít oprávnění pro všechny , například [vlastníka](built-in-roles.md#owner) nebo [správce přístupu uživatelů](built-in-roles.md#user-access-administrator).

1. Testování vlastní role

    Jakmile budete mít vlastní roli, budete muset otestovat, aby ověřil, že to funguje podle očekávání. Pokud potřebujete provést úpravy později, můžete aktualizovat vlastní roli.

Podrobný návod, jak vytvořit vlastní roli, najdete [v tématu Kurz: Vytvoření vlastní role pomocí Azure PowerShellu](tutorial-custom-role-powershell.md) nebo [Kurz: Vytvoření vlastní role pomocí azure cli](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Vlastní vlastnosti role

Vlastní role má následující vlastnosti.

| Vlastnost | Požaduje se | Typ | Popis |
| --- | --- | --- | --- |
| `Name` | Ano | Řetězec | Zobrazovaný název vlastní role. Zatímco definice role je prostředek pro správu nebo prostředek na úrovni předplatného, definice role se dá použít ve více předplatných, které sdílejí stejný adresář Azure AD. Tento zobrazovaný název musí být jedinečný v oboru adresáře Azure AD. Může obsahovat písmena, číslice, mezery a speciální znaky. Maximální počet znaků je 128. |
| `Id` | Ano | Řetězec | Jedinečné ID vlastní role. Pro Azure PowerShell a Azure CLI se toto ID automaticky vygeneruje při vytváření nové role. |
| `IsCustom` | Ano | Řetězec | Označuje, zda se jedná o vlastní roli. Nastaveno `true` na pro vlastní role. |
| `Description` | Ano | Řetězec | Popis vlastní role. Může obsahovat písmena, číslice, mezery a speciální znaky. Maximální počet znaků je 1024. |
| `Actions` | Ano | Řetězec[] | Pole řetězců, které určuje operace správy, které role umožňuje provádět. Další informace naleznete v [tématu Akce](role-definitions.md#actions). |
| `NotActions` | Ne | Řetězec[] | Pole řetězců, které určuje operace správy, které jsou `Actions`vyloučeny z povolené . Další informace naleznete v tématu [NotActions](role-definitions.md#notactions). |
| `DataActions` | Ne | Řetězec[] | Pole řetězců, které určuje datové operace, které role umožňuje provádět na data v rámci tohoto objektu. Pokud vytvoříte vlastní `DataActions`roli s , nelze tuto roli přiřadit v oboru skupiny pro správu. Další informace naleznete v tématu [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | Ne | Řetězec[] | Pole řetězců, které určuje datové operace, které jsou `DataActions`vyloučeny z povolené . Další informace naleznete v tématu [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Ano | Řetězec[] | Pole řetězců, které určuje obory, které vlastní role je k dispozici pro přiřazení. Ve vlastní roli můžete `AssignableScopes` definovat pouze jednu skupinu pro správu. Přidání skupiny `AssignableScopes` pro správu do aplikace je aktuálně ve verzi Preview. Další informace naleznete v tématu [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kdo může vytvořit, odstranit, aktualizovat nebo zobrazit vlastní roli

Stejně jako předdefinované `AssignableScopes` role, vlastnost určuje obory, které role je k dispozici pro přiřazení. Vlastnost `AssignableScopes` pro vlastní roli také určuje, kdo může vytvořit, odstranit, aktualizovat nebo zobrazit vlastní roli.

| Úkol | Operace | Popis |
| --- | --- | --- |
| Vytvoření nebo odstranění vlastní role | `Microsoft.Authorization/ roleDefinitions/write` | Uživatelé, kterým je tato `AssignableScopes` operace udělena ve všech vlastních rolích, mohou vytvářet (nebo odstraňovat) vlastní role pro použití v těchto oborech. Například [vlastníci](built-in-roles.md#owner) a [správci přístupu uživatelů](built-in-roles.md#user-access-administrator) skupin pro správu, předplatných a skupin prostředků. |
| Aktualizace vlastní role | `Microsoft.Authorization/ roleDefinitions/write` | Uživatelé, kterým je tato `AssignableScopes` operace udělena ve všech vlastních rolích, mohou aktualizovat vlastní role v těchto oborech. Například [vlastníci](built-in-roles.md#owner) a [správci přístupu uživatelů](built-in-roles.md#user-access-administrator) skupin pro správu, předplatných a skupin prostředků. |
| Zobrazení vlastní role | `Microsoft.Authorization/ roleDefinitions/read` | Uživatelé, kterým je tato operace udělena v oboru, mohou zobrazit vlastní role, které jsou k dispozici pro přiřazení v tomto oboru. Všechny předdefinované role umožňují vlastní role, které mají být k dispozici pro přiřazení. |

## <a name="custom-role-limits"></a>Omezení vlastních rolí

Následující seznam popisuje omezení pro vlastní role.

- Každý adresář může mít až **5000** vlastních rolí.
- Azure Germany a Azure China 21Vianet můžou mít pro každý adresář až 2000 vlastních rolí.
- Nelze nastavit `AssignableScopes` kořenový obor`"/"`( ).
- Ve vlastní roli můžete `AssignableScopes` definovat pouze jednu skupinu pro správu. Přidání skupiny `AssignableScopes` pro správu do aplikace je aktuálně ve verzi Preview.
- Vlastní role `DataActions` s nelze přiřadit v oboru skupiny pro správu.
- Azure Resource Manager neověřuje existenci skupiny pro správu v přiřaditelném oboru definice role.

Další informace o vlastních rolích a skupinách pro správu najdete [v tématu Uspořádání prostředků pomocí skupin pro správu Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="next-steps"></a>Další kroky
- [Vytvoření nebo aktualizace vlastních rolí Azure pomocí portálu Azure (Preview)](custom-roles-portal.md)
- [Principy definic rolí pro prostředky Azure](role-definitions.md)
- [Poradce při potížích s RBAC pro prostředky Azure](troubleshooting.md)
