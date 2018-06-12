---
title: Vytvoření vlastních rolí pro Azure RBAC | Microsoft Docs
description: Další informace jak definovat vlastní role pomocí řízení přístupu pro přesnější správu identit ve vašem předplatném Azure.
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
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3baf616e448f1f6d5292161ae125502d72141940
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266590"
---
# <a name="create-custom-roles-in-azure"></a>Vytvořit vlastní role v Azure

Pokud [předdefinované role](built-in-roles.md) nevyhovují vašim potřebám konkrétní přístup, můžete vytvořit vlastní role. Stejně jako předdefinované role můžete přiřadit vlastní role pro uživatele, skupiny a objekty služby v odběru, skupinu prostředků a prostředků obory. Vlastní role se ukládají do klienta služby Azure Active Directory (Azure AD) a mohou být sdíleny s odběry. Každý klient může mít až 2000 vlastní role. Můžete vytvořit vlastní role pomocí Azure PowerShell, rozhraní příkazového řádku Azure nebo rozhraní REST API.

Tento článek popisuje příklad toho, jak začít vytvářet vlastní role pomocí prostředí PowerShell a rozhraní příkazového řádku Azure.

## <a name="create-a-custom-role-to-open-support-requests-using-powershell"></a>Vytvořit vlastní roli otevření žádosti o podporu pomocí prostředí PowerShell

Pokud chcete vytvořit vlastní roli, může začínat předdefinovaná role, upravit a poté vytvořit novou roli. V tomto příkladu integrované [čtečky](built-in-roles.md#reader) role upravit tak, aby vytvořit vlastní roli s názvem "úroveň přístupu lístky žádostí o podporu čtečky". Umožňuje uživatelům zobrazit vše, co v dané předplatné a také žádosti o podporu otevřete.

> [!NOTE]
> Pouze dva předdefinované role, které umožňují uživatele k otevření žádosti o podporu jsou [vlastníka](built-in-roles.md#owner) a [Přispěvatel](built-in-roles.md#contributor). Uživatel nebude moci otevřít žádosti o podporu musí mohl být přiřazena role v oboru předplatné všechny žádosti o podporu se vytváří podle předplatného Azure.

V prostředí PowerShell, použijte [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) příkaz pro export [čtečky](built-in-roles.md#reader) role ve formátu JSON.

```azurepowershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

Následující příklad zobrazuje výstup JSON [čtečky](built-in-roles.md#reader) role. Typické role se skládá ze tří hlavních částí `Actions`, `NotActions`, a `AssignableScopes`. `Actions` Části je uveden seznam povolených operací pro roli. Vyloučit operací z `Actions`, přidejte je do `NotActions`. Skutečná oprávnění se počítá odečtením `NotActions` operací `Actions` operace.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Dále můžete upravit výstup vytvořit vlastní roli ve formátu JSON. V takovém případě vytvářet podporu lístků, `Microsoft.Support/*` operace musí být přidán. Každé operace je k dispozici od zprostředkovatele prostředků. Chcete-li získat seznam operací pro poskytovatele prostředků, můžete použít [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) příkaz nebo najdete [operace poskytovatele prostředků Azure Resource Manager](resource-provider-operations.md).

Je povinný, že role obsahuje explicitní předplatné ID, kde se používá. ID předplatného jsou uvedeny v části `AssignableScopes`, v opačném případě můžete nebude moct importovat role do vašeho předplatného.

Nakonec je nutné nastavit `IsCustom` vlastnost `true` k určení, že se jedná o vlastní roli.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

Chcete-li vytvořit novou vlastní roli, je použít [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) příkazů a poskytnout aktualizovaný soubor definice role JSON.

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

Po spuštění [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition), novou vlastní roli, je k dispozici na webu Azure portal a můžete přiřadit uživatelům.

![snímek obrazovky vlastní roli, které jsou importovány na portálu Azure](./media/custom-roles/18.png)

![snímek obrazovky přiřazení vlastní importované role pro uživatele ve stejném adresáři](./media/custom-roles/19.png)

![snímek obrazovky oprávnění pro vlastní importované role](./media/custom-roles/20.png)

Uživatelé s tuto vlastní roli, mohou vytvářet nové žádosti o podporu.

![snímek obrazovky vytváření žádosti o podporu vlastní role](./media/custom-roles/21.png)

Uživatelé s tuto vlastní roli nelze provádět další akce, jako například vytvořit virtuální počítače nebo skupiny prostředků.

![snímek obrazovky vlastní roli nelze vytvořit virtuální počítače](./media/custom-roles/22.png)

![snímek obrazovky vlastní roli nelze vytvořit nové RGs](./media/custom-roles/23.png)

## <a name="create-a-custom-role-to-open-support-requests-using-azure-cli"></a>Vytvořit vlastní roli otevření žádosti o podporu pomocí rozhraní příkazového řádku Azure

Postup vytvoření vlastní role pomocí rozhraní příkazového řádku Azure jsou podobná pomocí prostředí PowerShell, s tím rozdílem, že výstup JSON se liší.

V tomto příkladu můžete spustit pomocí integrované [čtečky](built-in-roles.md#reader) role. Seznam akce [čtečky](built-in-roles.md#reader) role, použijte [seznamu definice role az](/cli/azure/role/definition#az_role_definition_list) příkaz.

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Vytvořte soubor JSON v následujícím formátu. `Microsoft.Support/*` Operaci byla přidána do `Actions` částech, aby tento uživatel může otevřít žádosti o podporu přitom dál být čtečka čipových karet. Je nutné přidat kde tato role se použije v ID předplatného `AssignableScopes` části.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Chcete-li vytvořit novou vlastní roli, použijte [vytvoření definice role az](/cli/azure/role/definition#az_role_definition_create) příkaz.

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

Nové vlastní role je nyní k dispozici na webu Azure portal a proces pomocí této role je stejné jako v předchozí části prostředí PowerShell.

![Azure portálu snímek obrazovky vlastní roli, které jsou vytvořené pomocí rozhraní příkazového řádku 1.0](./media/custom-roles/26.png)


## <a name="see-also"></a>Další informace najdete v tématech
- [Pochopení definice rolí](role-definitions.md)
- [Správa řízení přístupu na základě rolí pomocí AzurePowerShell](role-assignments-powershell.md)
- [Správa řízení přístupu na základě rolí pomocí rozhraní příkazového řádku Azure](role-assignments-cli.md)
- [Správa řízení přístupu na základě rolí pomocí rozhraní REST API](role-assignments-rest.md)
