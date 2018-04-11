---
title: "Vytvoření vlastních rolí pro Azure RBAC | Microsoft Docs"
description: "Další informace jak definovat vlastní role pomocí řízení přístupu pro přesnější správu identit ve vašem předplatném Azure."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2871ff5eea8fb99040dfab2593d1640d79f51092
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
---
# <a name="create-custom-roles-for-azure-role-based-access-control"></a>Vytvořit vlastní role pro řízení přístupu
Vytvořte vlastní roli v řízení řízení přístupu (RBAC), pokud žádná z předdefinovaných rolí podle svých potřeb konkrétní přístup. Můžete vytvořit vlastní role pomocí [prostředí Azure PowerShell](role-based-access-control-manage-access-powershell.md), [rozhraní příkazového řádku Azure](role-based-access-control-manage-access-azure-cli.md) (CLI) a [REST API](role-based-access-control-manage-access-rest.md). Stejně jako předdefinované role můžete přiřadit vlastní role pro uživatele, skupiny a aplikace na předplatné, skupinu prostředků a prostředků obory. Vlastní role jsou uloženy v klient služby Azure AD a mohlo sdílet víc předplatných.

Každý klient můžete vytvořit až 2 000 vlastní role. 

Následující příklad ukazuje vlastní role pro monitorování a restartování virtuálního počítače:

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
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
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Akce
**Akce** vlastnost vlastní role určuje činnosti Azure, ke kterým roli udělí přístup. Jedná se o kolekci operaci řetězců, které identifikují zabezpečitelné operations poskytovatelů prostředků Azure. Operace řetězce použijte formát `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. Operace řetězce, které obsahují zástupné znaky (\*) udělit přístup ke všem operacím, které odpovídají řetězci operace. Například:

* `*/read`uděluje přístup ke čtení pro všechny typy prostředků všech poskytovatelů prostředků Azure.
* `Microsoft.Compute/*`uděluje přístup ke všem operacím pro všechny typy prostředků ve zprostředkovateli prostředků Microsoft.Compute.
* `Microsoft.Network/*/read`uděluje přístup ke čtení pro všechny typy prostředků v poskytovatel prostředků Microsoft.Network Azure.
* `Microsoft.Compute/virtualMachines/*`uděluje přístup ke všem operacím virtuálních počítačů a jeho podřízené typy prostředků.
* `Microsoft.Web/sites/restart/Action`uděluje přístup k restartování weby.

Použití `Get-AzureRmProviderOperation` (v prostředí PowerShell) nebo `azure provider operations show` (v Azure CLI) k operacím seznamu zprostředkovatelů prostředků Azure. Ověřte, zda je řetězec v operaci platný a rozbalte řetězce operaci zástupný znak, může pomocí následujících příkazů.

```powershell
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![Snímek obrazovky prostředí PowerShell - Get-AzureRMProviderOperation](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```azurecli
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Azure CLI – snímek obrazovky - azure zprostředkovatele operations zobrazit "Microsoft.Compute/virtualMachines/\*/action" ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Použití **NotActions** vlastnost Pokud sadu operací, které chcete povolit je snadno definovanou s výjimkou operací s omezeným přístupem. Udělení přístupu podle vlastní role se počítá odečtením **NotActions** operací **akce** operace.

> [!NOTE]
> Pokud je uživatel přiřazenou roli, která nezahrnuje operace v **NotActions**a je mu přiřazená druhá role, který uděluje přístup ke stejné operace, má uživatel k provedení této operace. **NotActions** není odepření pravidlo – je jenom pohodlný způsob, jak vytvořit sadu povolených operací při konkrétních operací muset vyloučeny.
>
>

## <a name="assignablescopes"></a>AssignableScopes
**AssignableScopes** vlastnost vlastní role určuje obory (odběry, skupiny prostředků nebo prostředky), ve které je k dispozici pro přiřazení vlastní role. Můžete zpřístupnit vlastní role pro přiřazení v jenom na předplatná nebo skupiny prostředků, které je vyžadují a není zbytečných souborů činnost koncového uživatele pro zbytek předplatná nebo skupiny prostředků.

Příklady platný přiřaditelnými obory:

* "/ subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/ subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624" - zpřístupní role pro přiřazení do dvou předplatných.
* "/ subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e" - zpřístupní role pro přiřazení v rámci jednoho předplatného.
* "/ odběry nebo c276fc76-9cd4-44c9-99a7-4fd71546436e/Skupinyprostředků/síťových" - je k dispozici pro přiřazení role pouze ve skupině prostředků sítě.

> [!NOTE]
> Musíte použít aspoň jeden předplatné, skupinu prostředků nebo ID prostředku.
>
>

## <a name="custom-roles-access-control"></a>Vlastní role řízení přístupu
**AssignableScopes** vlastnost vlastní role také určuje, kdo může zobrazit, upravit a odstranit roli.

* Kdo mohou vytvářet vlastní role?
    Vlastníci (a správcům přístup uživatele) předplatných, skupiny prostředků a prostředků můžete vytvořit vlastní role pro použití v tyto obory.
    Vytvoření role uživatele musí být schopni provést `Microsoft.Authorization/roleDefinition/write` operaci na všech **AssignableScopes** role.
* Kdo může upravit vlastní roli?
    Vlastníci (a správcům přístup uživatele) předplatných, skupiny prostředků a prostředků můžete upravit vlastní role v tyto obory. Uživatelé musí být schopni provést `Microsoft.Authorization/roleDefinition/write` operaci na všech **AssignableScopes** vlastní role.
* Kdo může zobrazit vlastní role?
    Všechny předdefinované role v Azure RBAC povolí zobrazení rolí, které jsou k dispozici pro přiřazení. Uživatelé, kteří mohou provádět `Microsoft.Authorization/roleDefinition/read` operace v oboru můžete zobrazit role RBAC, které jsou k dispozici pro přiřazení v tomto oboru.

## <a name="see-also"></a>Další informace najdete v tématech
* [Řízení přístupu na základě role](role-based-access-control-configure.md): Začínáme s RBAC na portálu Azure.
* Seznam dostupných operací, najdete v části [poskytovatel prostředků Azure Resource Manager operations](role-based-access-control-resource-provider-operations.md).
* Zjistěte, jak spravovat přístup pomocí:
  * [PowerShell](role-based-access-control-manage-access-powershell.md)
  * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
  * [REST API](role-based-access-control-manage-access-rest.md)
* [Předdefinované role](role-based-access-built-in-roles.md): získat tak podrobné údaje o rolích, které jsou v RBAC standardní.
