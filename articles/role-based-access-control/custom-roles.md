---
title: Vlastní role v Azure | Microsoft Docs
description: Další informace jak definovat vlastní role pomocí řízení přístupu Azure na základě rolí (RBAC) pro přesnou správu přístupu prostředků v Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 074c305cb15bc1fb25dfa5cfc52dcce53b661a7e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321175"
---
# <a name="custom-roles-in-azure"></a>Vlastní role v Azure

Pokud [předdefinované role](built-in-roles.md) nemáte splnili specifické požadavky vaší organizace, můžete vytvořit vlastní role. Stejně jako předdefinované role můžete přiřadit vlastní role pro uživatele, skupiny a objekty služby v odběru, skupinu prostředků a prostředků obory. Vlastní role se ukládají do klienta služby Azure Active Directory (Azure AD) a mohou být sdíleny s odběry. Každý klient může mít až 2000 vlastní role. Můžete vytvořit vlastní role pomocí Azure PowerShell, rozhraní příkazového řádku Azure nebo rozhraní REST API.

## <a name="custom-role-example"></a>Příklad vlastní role

Následující obrázek znázorňuje vlastní role pro monitorování a restartování virtuálního počítače, který zobrazovat pomocí Azure PowerShell:

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

Jakmile vytvoříte vlastní roli, zobrazí se na portálu Azure ikonou oranžové prostředků.

![Ikona vlastní role](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Kroky k vytvoření vlastních rolí

1. Určit oprávnění, které potřebujete

    Když vytvoříte vlastní roli, musíte znát prostředek operace poskytovatele, které jsou k dispozici k definování oprávnění. Chcete-li zobrazit seznam operací, můžete použít [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) nebo [seznam operací zprostředkovatele az](/cli/azure/provider/operation#az-provider-operation-list) příkazy.
    Nastavit oprávnění pro vaše vlastní role, přidejte na operací `actions` nebo `notActions` vlastnosti [definice role](role-definitions.md). Pokud máte data operací, přidejte je do `dataActions` nebo `notDataActions` vlastnosti.

2. Vytvořit vlastní role

    Azure PowerShell nebo rozhraní příkazového řádku Azure slouží k vytvoření vlastních rolí. Obvykle začínat existující předdefinovaná role a upravit ho pro své potřeby. Potom pomocí [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) nebo [vytvoření definice role az](/cli/azure/role/definition#az-role-definition-create) příkazy k vytvoření vlastních rolí. Chcete-li vytvořit vlastní roli, musíte mít `Microsoft.Authorization/roleDefinitions/write` oprávnění u všech `assignableScopes`, například [vlastníka](built-in-roles.md#owner) nebo [správce přístupu uživatelů](built-in-roles.md#user-access-administrator).

3. Otestovat vlastní role

    Až budete mít vlastní roli, musíte otestovat a ověřte, že funguje podle očekávání. Pokud je nutné provést úpravy, můžete aktualizovat vlastní role.

## <a name="custom-role-properties"></a>Vlastnosti vlastní role

Vlastní role má následující vlastnosti.

| Vlastnost | Požaduje se | Typ | Popis |
| --- | --- | --- | --- |
| `Name` | Ano | Řetězec | Zobrazovaný název vlastní role. Musí být jedinečný pro vašeho klienta. Může obsahovat písmena, číslice, mezery a speciální znaky. Maximální počet znaků je 128. |
| `Id` | Ano | Řetězec | Jedinečné ID vlastní role. Pro prostředí Azure PowerShell a rozhraní příkazového řádku Azure je toto ID automaticky vygeneruje při vytvoření nové role. |
| `IsCustom` | Ano | Řetězec | Určuje, zda se jedná o vlastní roli. Nastavte na `true` pro vlastní role. |
| `Description` | Ano | Řetězec | Popis vlastní role. Může obsahovat písmena, číslice, mezery a speciální znaky. Maximální počet znaků je 1024. |
| `Actions` | Ano | Řetězec] | Pole řetězců, které určuje operace správy, které umožňuje provést roli. Další informace najdete v tématu [akce](role-definitions.md#actions). |
| `NotActions` | Ne | Řetězec] | Pole řetězců, které určuje operace správy, které budou vyloučeny z povolené maximum `actions`. Další informace najdete v tématu [notActions](role-definitions.md#notactions). |
| `DataActions` | Ne | Řetězec] | Pole řetězců, které určuje data operací, které umožňuje provádět, aby vaše data v rámci objektu roli. Další informace najdete v tématu [dataActions (Preview)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Ne | Řetězec] | Pole řetězců, které určuje operace dat, které budou vyloučeny z povolené maximum `dataActions`. Další informace najdete v tématu [notDataActions (Preview)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Ano | Řetězec] | Pole řetězců, které určuje obory, že je k dispozici pro přiřazení vlastních rolí. Nelze nastavit na kořenovém oboru (`"/"`). Další informace najdete v tématu [assignableScopes](role-definitions.md#assignablescopes). |

## <a name="assignablescopes-for-custom-roles"></a>assignableScopes pro vlastní role

Předdefinované role, že stejně jako `assignableScopes` vlastnost určuje, zda je k dispozici pro přiřazení role obory. Však nelze použít kořenovém oboru (`"/"`) ve vlastní role. Pokud se pokusíte, bude dojde k chybě autorizace. `assignableScopes` Vlastnost pro vlastní role také určuje, který můžete vytvořit, odstranit, upravit nebo zobrazit vlastní role.

| Úkol | Operace | Popis |
| --- | --- | --- |
| Vytvořit nebo odstranit vlastní roli | `Microsoft.Authorization/ roleDefinition/write` | Uživatelé, kteří jsou udělena tato operace na všech `assignableScopes` vlastní role můžete vytvořit (nebo odstranit) vlastní role pro použití v tyto obory. Například [vlastníky](built-in-roles.md#owner) a [správci přístupu uživatele](built-in-roles.md#user-access-administrator) odběry, skupiny prostředků a prostředků. |
| Upravit vlastní roli | `Microsoft.Authorization/ roleDefinition/write` | Uživatelé, kteří jsou udělena tato operace na všech `assignableScopes` vlastní role, můžete upravit vlastní role v tyto obory. Například [vlastníky](built-in-roles.md#owner) a [správci přístupu uživatele](built-in-roles.md#user-access-administrator) odběry, skupiny prostředků a prostředků. |
| Zobrazit vlastní role | `Microsoft.Authorization/ roleDefinition/read` | Uživatelé, kteří jsou udělena tato operace v oboru můžete zobrazit vlastní role, které jsou k dispozici pro přiřazení v tomto oboru. Všechny předdefinované role umožňují vlastní role být k dispozici pro přiřazení. |

## <a name="next-steps"></a>Další postup
- [Vytvořit vlastní role pomocí Azure PowerShell](custom-roles-powershell.md)
- [Vytvoření vlastních rolí pomocí rozhraní příkazového řádku Azure](custom-roles-cli.md)
- [Pochopení definice rolí](role-definitions.md)
