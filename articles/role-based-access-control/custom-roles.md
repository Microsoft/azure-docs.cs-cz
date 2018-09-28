---
title: Vlastní role v Azure | Dokumentace Microsoftu
description: Zjistěte, jak definovat vlastní role se řízení přístupu Azure na základě rolí (RBAC) pro správu prostředků v Azure velice přesně kontrolovat přístup.
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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 281c426170985d43401a13988218126ea3951634
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405610"
---
# <a name="custom-roles-in-azure"></a>Vlastní role v Azure

Pokud [předdefinované role](built-in-roles.md) nesplňují konkrétní požadavky vaší organizace, můžete vytvořit své vlastní role. Stejně jako předdefinované role můžete přiřadit vlastní role pro uživatele, skupiny nebo instanční objekty na předplatné, skupinu prostředků a prostředků obory. Vlastní role jsou uloženy v adresáři služby Azure Active Directory (Azure AD) a je možné sdílet napříč předplatnými. Každý adresář můžete mít až 2000 vlastní role. Můžete vytvořit vlastní role pomocí Azure Powershellu, rozhraní příkazového řádku Azure nebo rozhraní REST API.

## <a name="custom-role-example"></a>Příklad vlastní roli

Následuje ukázka, jak vlastní roli vypadá jako je zobrazena ve formátu JSON. Tuto vlastní roli je možné pro sledování a restartování virtuálních počítačů.

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
                  "Microsoft.Storage/*/read",
                  "Microsoft.Network/*/read",
                  "Microsoft.Compute/*/read",
                  "Microsoft.Compute/virtualMachines/start/action",
                  "Microsoft.Compute/virtualMachines/restart/action",
                  "Microsoft.Authorization/*/read",
                  "Microsoft.Resources/subscriptions/resourceGroups/read",
                  "Microsoft.Insights/alertRules/*",
                  "Microsoft.Insights/diagnosticSettings/*",
                  "Microsoft.Support/*"
  ],
  "NotActions":  [

                 ],
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

Když vytvoříte vlastní roli, se zobrazí na webu Azure Portal s ikona oranžové prostředků.

![Ikona vlastní role](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Postup vytvoření vlastní role

1. Určit oprávnění, které potřebujete

    Když vytvoříte vlastní roli, musíte znát prostředek operace poskytovatele, které je možné definovat oprávnění. Chcete-li zobrazit seznam operací, můžete použít [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) nebo [seznam operací az provider](/cli/azure/provider/operation#az-provider-operation-list) příkazy.
    Chcete-li zadat oprávnění pro vlastní roli, přidáte operace, které se `Actions` nebo `NotActions` vlastnosti [definice role](role-definitions.md). Pokud máte operace s daty, můžete přidat do `DataActions` nebo `NotDataActions` vlastnosti.

2. Vytvořit vlastní roli

    Prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure můžete použít k vytvoření vlastní role. Obvykle začněte s existující předdefinovanou roli a upravit ji pro vaše potřeby. Použít [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) nebo [az role definition vytvořit](/cli/azure/role/definition#az-role-definition-create) příkazy k vytvoření vlastní role. Chcete-li vytvořit vlastní roli, musíte mít `Microsoft.Authorization/roleDefinitions/write` oprávnění ve všech `AssignableScopes`, jako například [vlastníka](built-in-roles.md#owner) nebo [správce uživatelských přístupů](built-in-roles.md#user-access-administrator).

3. Testování vlastní roli

    Jakmile budete mít vlastní roli, musíte otestovat a ověřit, že funguje podle očekávání. Pokud je potřeba provést úpravy později, můžete aktualizovat vlastní roli.

Podrobný kurz o tom, jak vytvořit vlastní roli, najdete v tématu [kurz: vytvoření vlastní role pomocí prostředí Azure PowerShell](tutorial-custom-role-powershell.md) nebo [kurz: vytvoření vlastní role pomocí Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Vlastnosti vlastní role

Vlastní role má následující vlastnosti.

| Vlastnost | Požaduje se | Typ | Popis |
| --- | --- | --- | --- |
| `Name` | Ano | Řetězec | Zobrazovaný název vlastní role. Definice role je prostředek úrovni předplatného, definice role je možné v rámci více předplatných, které sdílejí stejný adresář Azure AD. Toto zobrazované jméno musí být jedinečný v oboru v adresáři Azure AD. Může obsahovat písmena, číslice, mezery a speciální znaky. Maximální počet znaků je 128. |
| `Id` | Ano | Řetězec | Jedinečné ID vlastní roli. Pro prostředí Azure PowerShell a rozhraní příkazového řádku Azure je toto ID automaticky vygeneruje při vytvoření nové role. |
| `IsCustom` | Ano | Řetězec | Označuje, zda se jedná o vlastní roli. Nastavte na `true` pro vlastní role. |
| `Description` | Ano | Řetězec | Popis vlastní role. Může obsahovat písmena, číslice, mezery a speciální znaky. Maximální počet znaků je 1024. |
| `Actions` | Ano | Řetězec] | Pole řetězců, která určuje, které role umožňuje provádět operace správy. Další informace najdete v tématu [akce](role-definitions.md#actions). |
| `NotActions` | Ne | Řetězec] | Pole řetězců, které určuje operace správy, které jsou vyloučené z povolených `Actions`. Další informace najdete v tématu [NotActions](role-definitions.md#notactions). |
| `DataActions` | Ne | Řetězec] | Pole řetězců, který určuje datové operace, které povoluje roli mají být provedeny ke svým datům v rámci daného objektu. Další informace najdete v tématu [DataActions (Preview)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Ne | Řetězec] | Pole řetězců, které určuje datové operace, které jsou vyloučené z povolených `DataActions`. Další informace najdete v tématu [NotDataActions (Preview)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Ano | Řetězec] | Pole řetězců, která určuje, že je k dispozici pro přiřazení vlastní role obory. Momentálně nemůže být nastavena na kořenového oboru (`"/"`) nebo obor skupiny správy. Další informace najdete v tématu [AssignableScopes](role-definitions.md#assignablescopes) a [uspořádání prostředků se skupinami pro správu Azure](../governance/management-groups/index.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kdo může vytvořit, odstranit, aktualizovat nebo zobrazit vlastní roli

Předdefinované role, stejně jako `AssignableScopes` vlastnost určuje, že je k dispozici pro přiřazení role obory. `AssignableScopes` Také určuje vlastnost, pro vlastní roli, můžete vytvořit, odstranit, aktualizovat nebo zobrazit vlastní roli.

| Úkol | Operace | Popis |
| --- | --- | --- |
| Vytvořit/odstranit vlastní roli | `Microsoft.Authorization/ roleDefinition/write` | Uživatelé, kteří jsou udělena tato operace na všech `AssignableScopes` vlastní role můžete vytvořit (nebo odstranění) vlastních rolí pro použití v těchto oborech. Například [vlastníky](built-in-roles.md#owner) a [správci přístupu uživatelů](built-in-roles.md#user-access-administrator) předplatná, skupiny prostředků a prostředků. |
| Aktualizace vlastní role | `Microsoft.Authorization/ roleDefinition/write` | Uživatelé, kteří jsou udělena tato operace na všech `AssignableScopes` vlastní role můžete aktualizovat vlastní role v těchto oborech. Například [vlastníky](built-in-roles.md#owner) a [správci přístupu uživatelů](built-in-roles.md#user-access-administrator) předplatná, skupiny prostředků a prostředků. |
| Zobrazit vlastní roli | `Microsoft.Authorization/ roleDefinition/read` | Uživatelé, kteří jsou udělena tato operace v oboru můžete zobrazit vlastní role, které jsou k dispozici pro přiřazení v daném oboru. Všechny vestavěné role povolit být k dispozici pro přiřazení vlastní role. |

## <a name="next-steps"></a>Další postup
- [Vytvoření vlastní role pomocí Azure Powershellu](custom-roles-powershell.md)
- [Vytváření vlastních rolí pomocí CLI](custom-roles-cli.md)
- [Vysvětlení definice rolí](role-definitions.md)
