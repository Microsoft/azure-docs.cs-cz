---
title: Vlastní role pro prostředky Azure | Microsoft Docs
description: Naučte se vytvářet vlastní role pomocí řízení přístupu na základě role (RBAC) pro detailní řízení přístupu k prostředkům Azure.
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
ms.date: 02/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8c5db13b343783a86dc04b84e09746bc4406186b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660695"
---
# <a name="custom-roles-for-azure-resources"></a>Vlastní role pro prostředky Azure

Pokud [předdefinované role pro prostředky Azure](built-in-roles.md) nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní role. Stejně jako předdefinované role můžete přiřadit vlastní role uživatelům, skupinám a instančním objektům v předplatném, skupině prostředků a oborech prostředků.

Vlastní role se dají sdílet mezi předplatnými, která důvěřují stejnému adresáři služby Azure AD. Pro každý adresář je povolený limit **5 000** vlastních rolí. (Pro specializované cloudy, jako je Azure Government, Azure Německo a Azure Čína 21Vianet, je limit 2 000 vlastních rolí.) Vlastní role se dají vytvořit pomocí Azure Portal (Preview), Azure PowerShell, Azure CLI nebo REST API.

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
    "/subscriptions/{subscriptionId3}"
  ]
}
```

Když vytvoříte vlastní roli, zobrazí se v Azure Portal s ikonou oranžového prostředku.

![Ikona vlastní role](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Postup vytvoření vlastní role

1. Rozhodněte, jak chcete vytvořit vlastní roli.

    Vlastní role můžete vytvořit pomocí [Azure Portal](custom-roles-portal.md) (Preview), [Azure POWERSHELL](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md)nebo [REST API](custom-roles-rest.md).

1. Určete potřebná oprávnění.

    Při vytváření vlastní role potřebujete znát operace poskytovatele prostředků, které jsou k dispozici pro definování vašich oprávnění. Chcete-li zobrazit seznam operací, přečtěte si téma [operace poskytovatele prostředků Azure Resource Manager](resource-provider-operations.md). Přidáte operace do vlastností `Actions` nebo `NotActions` [definice role](role-definitions.md). Pokud máte datové operace, přidejte je do vlastností `DataActions` nebo `NotDataActions`.

1. Vytvoření vlastní role

    Obvykle začínáte stávající integrovanou rolí a pak ji upravíte podle svých potřeb. Pak pomocí příkazu [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) nebo [AZ role definition Create](/cli/azure/role/definition#az-role-definition-create) vytvořte vlastní roli. Pokud chcete vytvořit vlastní roli, musíte mít oprávnění `Microsoft.Authorization/roleDefinitions/write` pro všechny `AssignableScopes`, jako je například [vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator).

1. Test vlastní role

    Jakmile máte vlastní roli, musíte ji otestovat, abyste ověřili, že funguje podle očekávání. Pokud potřebujete provést úpravy později, můžete aktualizovat vlastní roli.

Podrobný návod, jak vytvořit vlastní roli, najdete v tématu [kurz: Vytvoření vlastní role pomocí Azure PowerShell](tutorial-custom-role-powershell.md) nebo [kurzu: Vytvoření vlastní role pomocí rozhraní příkazového řádku Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Vlastnosti vlastní role

Vlastní role má následující vlastnosti.

| Vlastnost | Požaduje se | Typ | Popis |
| --- | --- | --- | --- |
| `Name` | Ano | Řetězec | Zobrazované jméno vlastní role I když je definice role prostředkem na úrovni předplatného, dá se použít definice role ve více předplatných, která sdílejí stejný adresář služby Azure AD. Tento zobrazovaný název musí být jedinečný v oboru adresáře služby Azure AD. Může obsahovat písmena, číslice, mezery a speciální znaky. Maximální počet znaků je 128. |
| `Id` | Ano | Řetězec | Jedinečné ID vlastní role Pro Azure PowerShell a Azure CLI se toto ID automaticky vygeneruje při vytvoření nové role. |
| `IsCustom` | Ano | Řetězec | Označuje, zda se jedná o vlastní roli. Pro vlastní role nastavte `true`. |
| `Description` | Ano | Řetězec | Popis vlastní role Může obsahovat písmena, číslice, mezery a speciální znaky. Maximální počet znaků je 1024. |
| `Actions` | Ano | Řetězec [] | Pole řetězců, které určuje operace správy, které může role provést. Další informace najdete v tématu [Akce](role-definitions.md#actions). |
| `NotActions` | Ne | Řetězec [] | Pole řetězců, které určuje operace správy, které jsou vyloučeny z povolených `Actions`. Další informace najdete v tématu [NotActions](role-definitions.md#notactions). |
| `DataActions` | Ne | Řetězec [] | Pole řetězců, které určuje datové operace, které může role provádět na vašich datech v rámci daného objektu. Další informace naleznete v tématu [Dataactions](role-definitions.md#dataactions). |
| `NotDataActions` | Ne | Řetězec [] | Pole řetězců, které určuje operace s daty, které jsou vyloučeny z povolených `DataActions`. Další informace najdete v tématu [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Ano | Řetězec [] | Pole řetězců, které určuje rozsahy, které jsou k dispozici pro přiřazení vlastní role. U vlastních rolí aktuálně nemůžete nastavit `AssignableScopes` do kořenového oboru (`"/"`) nebo do oboru skupiny pro správu. Další informace najdete v tématu [AssignableScopes](role-definitions.md#assignablescopes) a [uspořádání prostředků pomocí skupin pro správu Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kdo může vytvořit, odstranit, aktualizovat nebo zobrazit vlastní roli

Stejně jako předdefinované role určuje vlastnost `AssignableScopes` rozsahy, které je role k dispozici pro přiřazení. Vlastnost `AssignableScopes` pro vlastní roli také řídí, kdo může vytvořit, odstranit, aktualizovat nebo zobrazit vlastní roli.

| Úkol | Operace | Popis |
| --- | --- | --- |
| Vytvoření nebo odstranění vlastní role | `Microsoft.Authorization/ roleDefinitions/write` | Uživatelé, kterým je tato operace udělena na všech `AssignableScopes` vlastní role, mohou vytvořit (nebo odstranit) vlastní role pro použití v těchto oborech. Například [vlastníci](built-in-roles.md#owner) a [Správci přístupu uživatelů](built-in-roles.md#user-access-administrator) k předplatným, skupinám prostředků a prostředkům. |
| Aktualizace vlastní role | `Microsoft.Authorization/ roleDefinitions/write` | Uživatelé, kterým je tato operace udělená na všech `AssignableScopes` vlastní role, můžou aktualizovat vlastní role v těchto oborech. Například [vlastníci](built-in-roles.md#owner) a [Správci přístupu uživatelů](built-in-roles.md#user-access-administrator) k předplatným, skupinám prostředků a prostředkům. |
| Zobrazení vlastní role | `Microsoft.Authorization/ roleDefinitions/read` | Uživatelé, kterým je tato operace udělená v oboru, můžou zobrazit vlastní role, které jsou k dispozici pro přiřazení v daném oboru. Všechny předdefinované role umožňují, aby byly vlastní role k dispozici pro přiřazení. |

## <a name="next-steps"></a>Další kroky
- [Vytvoření nebo aktualizace vlastních rolí Azure pomocí Azure Portal (Preview)](custom-roles-portal.md)
- [Pochopení definic rolí pro prostředky Azure](role-definitions.md)
- [Řešení potíží s RBAC pro prostředky Azure](troubleshooting.md)
