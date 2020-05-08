---
title: Vlastní role Azure – Azure RBAC
description: Naučte se vytvářet vlastní role Azure pomocí řízení přístupu na základě role Azure (Azure RBAC) pro detailní řízení přístupu k prostředkům Azure.
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
ms.date: 04/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5030fb50313e1db2173990c55930c22fdf58f559
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734786"
---
# <a name="azure-custom-roles"></a>Vlastní role Azure

> [!IMPORTANT]
> Přidání skupiny pro správu do `AssignableScopes` je aktuálně ve verzi Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud [předdefinované role Azure](built-in-roles.md) nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní role. Stejně jako předdefinované role můžete uživatelům, skupinám a instančním objektům přiřadit vlastní role na úrovni skupiny pro správu, předplatného a skupiny prostředků.

Vlastní role se dají sdílet mezi předplatnými, která důvěřují stejnému adresáři služby Azure AD. Pro každý adresář je povolený limit **5 000** vlastních rolí. (Pro Azure Německo a Azure Čína 21Vianet je limit 2 000 vlastních rolí.) Vlastní role se dají vytvořit pomocí Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure nebo REST API.

## <a name="custom-role-example"></a>Příklad vlastní role

Následující text ukazuje, jak vlastní role vypadá jako zobrazený ve formátu JSON. Tato vlastní role se dá použít k monitorování a restartování virtuálních počítačů.

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

Když vytvoříte vlastní roli, zobrazí se v Azure Portal s ikonou oranžového prostředku.

![Ikona vlastní role](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Postup vytvoření vlastní role

1. Rozhodněte, jak chcete vytvořit vlastní roli.

    Vlastní role můžete vytvořit pomocí [Azure Portal](custom-roles-portal.md), [Azure PowerShell](custom-roles-powershell.md), rozhraní příkazového [řádku Azure](custom-roles-cli.md)nebo [REST API](custom-roles-rest.md).

1. Určete potřebná oprávnění.

    Při vytváření vlastní role potřebujete znát operace poskytovatele prostředků, které jsou k dispozici pro definování vašich oprávnění. Chcete-li zobrazit seznam operací, přečtěte si téma [operace poskytovatele prostředků Azure Resource Manager](resource-provider-operations.md). Operace přidáte do vlastností `Actions` nebo `NotActions` v [definici role](role-definitions.md). Pokud máte datové operace, přidejte je do vlastností `DataActions` nebo. `NotDataActions`

1. Vytvoření vlastní role

    Obvykle začínáte stávající integrovanou rolí a pak ji upravíte podle svých potřeb. Pak pomocí příkazu [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) nebo [AZ role definition Create](/cli/azure/role/definition#az-role-definition-create) vytvořte vlastní roli. Pokud chcete `Microsoft.Authorization/roleDefinitions/write` vytvořit vlastní roli, musíte mít oprávnění pro všechny `AssignableScopes`, jako je [vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator).

1. Test vlastní role

    Jakmile máte vlastní roli, musíte ji otestovat, abyste ověřili, že funguje podle očekávání. Pokud potřebujete provést úpravy později, můžete aktualizovat vlastní roli.

Podrobný návod, jak vytvořit vlastní roli, najdete v tématu [kurz: Vytvoření vlastní role Azure pomocí Azure PowerShell](tutorial-custom-role-powershell.md) nebo [kurzu: Vytvoření vlastní role Azure pomocí Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Vlastnosti vlastní role

Vlastní role má následující vlastnosti.

| Vlastnost | Požaduje se | Typ | Popis |
| --- | --- | --- | --- |
| `Name` | Ano | Řetězec | Zobrazované jméno vlastní role Zatímco definice role je skupina pro správu nebo prostředek na úrovni předplatného, můžete definici role použít ve více předplatných, která sdílejí stejný adresář služby Azure AD. Tento zobrazovaný název musí být jedinečný v oboru adresáře služby Azure AD. Může obsahovat písmena, číslice, mezery a speciální znaky. Maximální počet znaků je 128. |
| `Id` | Ano | Řetězec | Jedinečné ID vlastní role Pro Azure PowerShell a Azure CLI se toto ID automaticky vygeneruje při vytvoření nové role. |
| `IsCustom` | Ano | Řetězec | Označuje, zda se jedná o vlastní roli. Nastavte na `true` pro vlastní role. |
| `Description` | Ano | Řetězec | Popis vlastní role Může obsahovat písmena, číslice, mezery a speciální znaky. Maximální počet znaků je 1024. |
| `Actions` | Ano | Řetězec [] | Pole řetězců, které určuje operace správy, které může role provést. Další informace najdete v tématu [Akce](role-definitions.md#actions). |
| `NotActions` | No | Řetězec [] | Pole řetězců, které určují operace správy, které jsou vyloučeny z povolených `Actions`. Další informace najdete v tématu [NotActions](role-definitions.md#notactions). |
| `DataActions` | No | Řetězec [] | Pole řetězců, které určuje datové operace, které může role provádět na vašich datech v rámci daného objektu. Pokud vytvoříte vlastní roli s `DataActions`, tuto roli nelze přiřadit v oboru skupiny pro správu. Další informace naleznete v tématu [Dataactions](role-definitions.md#dataactions). |
| `NotDataActions` | No | Řetězec [] | Pole řetězců, které určují operace s daty, které jsou vyloučeny z povolených `DataActions`. Další informace najdete v tématu [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Ano | Řetězec [] | Pole řetězců, které určuje rozsahy, které jsou k dispozici pro přiřazení vlastní role. V `AssignableScopes` rámci vlastní role můžete definovat jenom jednu skupinu pro správu. Přidání skupiny pro správu do `AssignableScopes` je aktuálně ve verzi Preview. Další informace najdete v tématu [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kdo může vytvořit, odstranit, aktualizovat nebo zobrazit vlastní roli

Stejně jako předdefinované role určuje `AssignableScopes` vlastnost obory, které je role k dispozici pro přiřazení. `AssignableScopes` Vlastnost vlastní role také určuje, kdo může vytvořit, odstranit, aktualizovat nebo zobrazit vlastní roli.

| Úkol | Operace | Popis |
| --- | --- | --- |
| Vytvoření nebo odstranění vlastní role | `Microsoft.Authorization/ roleDefinitions/write` | Uživatelé, kterým je tato operace udělena na všech `AssignableScopes` vlastních rolích, mohou vytvořit (nebo odstranit) vlastní role pro použití v těchto oborech. Například [vlastníci](built-in-roles.md#owner) a [Správci přístupu uživatelů](built-in-roles.md#user-access-administrator) skupiny pro správu, předplatná a skupiny prostředků. |
| Aktualizace vlastní role | `Microsoft.Authorization/ roleDefinitions/write` | Uživatelé, kterým je tato operace udělena na všech `AssignableScopes` vlastních rolích, mohou aktualizovat vlastní role v těchto oborech. Například [vlastníci](built-in-roles.md#owner) a [Správci přístupu uživatelů](built-in-roles.md#user-access-administrator) skupiny pro správu, předplatná a skupiny prostředků. |
| Zobrazení vlastní role | `Microsoft.Authorization/ roleDefinitions/read` | Uživatelé, kterým je tato operace udělená v oboru, můžou zobrazit vlastní role, které jsou k dispozici pro přiřazení v daném oboru. Všechny předdefinované role umožňují, aby byly vlastní role k dispozici pro přiřazení. |

## <a name="custom-role-limits"></a>Omezení vlastních rolí

Následující seznam popisuje omezení pro vlastní role.

- Každý adresář může mít až **5000** vlastních rolí.
- Azure Německo a Azure Čína 21Vianet můžou mít pro každý adresář až 2000 vlastních rolí.
- Nelze nastavit `AssignableScopes` do kořenového oboru (`"/"`).
- V `AssignableScopes` rámci vlastní role můžete definovat jenom jednu skupinu pro správu. Přidání skupiny pro správu do `AssignableScopes` je aktuálně ve verzi Preview.
- Vlastní role se `DataActions` nedají přiřadit v oboru skupiny pro správu.
- Azure Resource Manager neověřuje existenci skupiny pro správu v oboru přiřazení definice role.

Další informace o vlastních rolích a skupinách pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="next-steps"></a>Další kroky
- [Pomocí Azure Portal vytvořit nebo aktualizovat vlastní role Azure](custom-roles-portal.md)
- [Vysvětlení definic rolí Azure](role-definitions.md)
- [Řešení potíží s Azure RBAC](troubleshooting.md)
