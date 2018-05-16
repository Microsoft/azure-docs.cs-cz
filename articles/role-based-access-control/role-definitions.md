---
title: Pochopit definice role v Azure RBAC | Microsoft Docs
description: Další informace o definicích rolí v řízení přístupu na základě role (RBAC) a jak definovat vlastní role pro správu jemně odstupňovaných přístupu prostředků v Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: 7a9e257d445ff7dadfe27d1c75cde6f58a393397
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="understand-role-definitions"></a>Pochopení definice rolí

Pokud chcete pochopit, jak funguje roli, nebo při vytváření vlastní [vlastní role](custom-roles.md), je vhodné se seznámit s definici role. Tento článek popisuje podrobnosti o definice rolí a obsahuje některé příklady.

## <a name="role-definition-structure"></a>Struktura definice role

A *definice role* je kolekce oprávnění. Někdy právě se nazývá *role*. Definice role obsahuje seznam operací, které lze provést, jako je čtení, zápisu a odstranění. Můžete ho také seznam operací, které nelze provést. Definice role má následující strukturu:

```
assignableScopes []
description
id
name
permissions []
  actions []
  notActions []
roleName
roleType
type
```

Operace se zadaným řetězce, které mají tento formát:

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

`{action}` Část řetězce operace určuje typ operace můžete provádět na typ prostředku. Například, zobrazí se následující dílčích řetězců v `{action}`:

| Substring akce    | Popis         |
| ------------------- | ------------------- |
| `*` | Zástupný znak uděluje přístup ke všem operacím, které odpovídají řetězec. |
| `read` | Umožňuje číst operací (GET). |
| `write` | Umožní zápisu operace (PUT, POST a oprava). |
| `delete` | Umožňuje odstranit operace (DELETE). |

Tady je [Přispěvatel](built-in-roles.md#contributor) definice role ve formátu JSON. Zástupný znak (`*`) operace v rámci `actions` označuje, že objekt přiřazení k této roli mohou provádět všechny akce, nebo jinými slovy, umožňuje spravovat všechno. To zahrnuje akce definované v budoucnu, přidává nové typy prostředků Azure. Operace v rámci `notActions` je odečten od `actions`. U [Přispěvatel](built-in-roles.md#contributor) role, `notActions` Odebere tuto roli schopnost spravovat přístup k prostředkům a také přiřazení přístupu k prostředkům.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-operations"></a>Operace správy

Řízení přístupu na základě rolí pro operace správy je uveden v `actions` a `notActions` části definici role. Tady jsou některé příklady operací správy v Azure:

- Správa přístupu k účtu úložiště
- Vytvářet, aktualizovat nebo odstranit kontejner objektů blob
- Odstranit skupinu prostředků a všechny její prostředky

Přístup pro správu není zděděna ke svým datům. Toto rozdělení brání role se zástupnými znaky (`*`) z má neomezený přístup k datům. Například pokud má uživatel [čtečky](built-in-roles.md#reader) role na předplatném, pak mohou zobrazit účet úložiště, ale nemůže zobrazit základní data.

## <a name="actions"></a>Akce

`actions` Oprávnění určuje operace správy, ke kterým roli udělí přístup. Jedná se o kolekci operaci řetězců, které identifikují zabezpečitelné operations poskytovatelů prostředků Azure. Zde jsou některé příklady operace správy, které mohou být používány `actions`.

| Operace řetězec    | Popis         |
| ------------------- | ------------------- |
| `*/read` | uděluje přístup ke čtení pro všechny typy prostředků všech poskytovatelů prostředků Azure.|
| `Microsoft.Compute/*` | uděluje přístup ke všem operacím pro všechny typy prostředků ve zprostředkovateli prostředků Microsoft.Compute.|
| `Microsoft.Network/*/read` | Uděluje přístup ke čtení pro všechny typy prostředků v poskytovatel prostředků Microsoft.Network.|
| `Microsoft.Compute/virtualMachines/*` | uděluje přístup ke všem operacím virtuálních počítačů a jeho podřízené typy prostředků.|
| `microsoft.web/sites/restart/Action` | Uděluje přístup k restartování webové aplikace.|

## <a name="notactions"></a>NotActions

`notActions` Oprávnění určuje operace správy, které budou vyloučeny z povolené maximum `actions`. Použití `notActions` oprávnění, pokud sadu operací, které chcete povolit je snadno definovanou s výjimkou operací s omezeným přístupem. Udělení přístupu podle rolí (skutečná oprávnění) se počítá odečtením `notActions` operací `actions` operace.

> [!NOTE]
> Pokud je uživatel přiřazenou roli, která nezahrnuje operace v `notActions`a je mu přiřazená druhá role, který uděluje přístup ke stejné operace, má uživatel k provedení této operace. `notActions` není odepření pravidlo – je jenom pohodlný způsob, jak vytvořit sadu povolených operací při konkrétních operací muset vyloučeny.
>

## <a name="assignablescopes"></a>AssignableScopes

`assignableScopes` Část určuje obory (skupiny pro správu (momentálně ve verzi preview), odběry, skupiny prostředků nebo prostředky), že je k dispozici pro přiřazení role. Můžete zpřístupnit role pro přiřazení v pouze předplatných nebo skupiny prostředků, které vyžadují a ne uživatele zbytečné soubory prostředí pro zbytek předplatná nebo skupiny prostředků. Je nutné použít správu alespoň jednu skupinu, předplatné, skupinu prostředků nebo ID prostředku.

Předdefinované role mají `assignableScopes` nastavena na kořenovém oboru (`"/"`). Kořenovém oboru označuje, že role je k dispozici pro přiřazení v všechny obory. Kořenovém oboru nelze použít ve vlastní role. Pokud se pokusíte, bude dojde k chybě autorizace.

Příklady platný přiřaditelnými obory:

| Scénář | Příklad: |
|----------|---------|
| Role je k dispozici pro přiřazení v rámci jednoho předplatného | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Role je k dispozici pro přiřazení v obě předplatná | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Role je k dispozici pro přiřazení pouze ve skupině prostředků sítě | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Role je k dispozici pro přiřazení v všechny obory | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes a vlastní role

`assignableScopes` Části pro vlastní role také určuje, který můžete vytvořit, odstranit, upravit nebo zobrazit vlastní role.

| Úkol | Operace | Popis |
| --- | --- | --- |
| Vytvořit nebo odstranit vlastní roli | `Microsoft.Authorization/ roleDefinition/write` | Uživatelé, kteří jsou udělena tato operace na všech `assignableScopes` vlastní role můžete vytvořit (nebo odstranit) vlastní role pro použití v tyto obory. Například [vlastníky](built-in-roles.md#owner) a [správci přístupu uživatele](built-in-roles.md#user-access-administrator) odběry, skupiny prostředků a prostředků. |
| Upravit vlastní roli | `Microsoft.Authorization/ roleDefinition/write` | Uživatelé, kteří jsou udělena tato operace na všech `assignableScopes` vlastní role, můžete upravit vlastní role v tyto obory. Například [vlastníky](built-in-roles.md#owner) a [správci přístupu uživatele](built-in-roles.md#user-access-administrator) odběry, skupiny prostředků a prostředků. |
| Zobrazit vlastní role | `Microsoft.Authorization/ roleDefinition/read` | Uživatelé, kteří jsou udělena tato operace v oboru můžete zobrazit vlastní role, které jsou k dispozici pro přiřazení v tomto oboru. Všechny předdefinované role umožňují vlastní role být k dispozici pro přiřazení. |

## <a name="role-definition-examples"></a>Příklady definice rolí

Následující příklad ukazuje [čtečky](built-in-roles.md#reader) definice role, zobrazovat pomocí rozhraní příkazového řádku Azure:

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
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

Následující příklad ukazuje vlastní role pro monitorování a restartování virtuálního počítače, který zobrazovat pomocí Azure PowerShell:

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
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>Další informace najdete v tématech

* [Předdefinované role](built-in-roles.md)
* [Vlastní role](custom-roles.md)
* [Operace zprostředkovatele prostředků v Azure Resource Manager](resource-provider-operations.md)
