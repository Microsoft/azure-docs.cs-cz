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
ms.date: 05/18/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: cb6bb5ed80f08941b872e6fabeb8bb150a21dede
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640383"
---
# <a name="understand-role-definitions"></a>Vysvětlení definic rolí

Pokud chcete pochopit, jak funguje roli, nebo při vytváření vlastní [vlastní role](custom-roles.md), je vhodné se seznámit s definici role. Tento článek popisuje podrobnosti o definice rolí a obsahuje některé příklady.

## <a name="role-definition-structure"></a>Struktura definice role

A *definice role* je kolekce oprávnění. Někdy právě se nazývá *role*. Definice role obsahuje seznam operací, které lze provést, jako je čtení, zápisu a odstranění. Můžete také seznam, operace, které nelze provést, nebo operace související s základní data. Definice role má následující strukturu:

```
assignableScopes []
description
id
name
permissions []
  actions []
  dataActions []
  notActions []
  notDataActions []
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
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-and-data-operations-preview"></a>Operace správy a data (Preview)

Řízení přístupu na základě rolí pro operace správy je uveden v `actions` a `notActions` části definici role. Tady jsou některé příklady operací správy v Azure:

- Správa přístupu k účtu úložiště
- Vytvářet, aktualizovat nebo odstranit kontejner objektů blob
- Odstranit skupinu prostředků a všechny její prostředky

Přístup pro správu není zděděna ke svým datům. Toto rozdělení brání role se zástupnými znaky (`*`) z má neomezený přístup k datům. Například pokud má uživatel [čtečky](built-in-roles.md#reader) role na předplatném, pak mohou zobrazit účet úložiště, ale ve výchozím nastavení se základní data nelze zobrazit.

Řízení přístupu na základě role nebyla dříve, slouží k operacím data. Autorizace pro datové operace nejrůznější napříč zprostředkovatelé prostředků. Model pro autorizace stejné řízení přístupu na základě role používat pro operace správy rozšířilo a operace dat (momentálně ve verzi preview).

Pro podporu operací, data, byly přidány nové části dat na strukturu definici role. Operace dat jsou určené v `dataActions` a `notDataActions` oddíly. Přidáním těchto částí data se udržuje oddělení mezi správou a data. Zabrání se tak aktuální přiřazení rolí se zástupnými znaky (`*`) z najednou má přístup k datům. Tady jsou některé operace dat, které lze zadat v `dataActions` a `notDataActions`:

- Přečtěte si seznam objektů BLOB v kontejneru
- Zápis do úložiště objektu blob v kontejneru
- Odstranění zprávy ve frontě

Tady je [čtecí modul dat objektů Blob Storage (Preview)](built-in-roles.md#storage-blob-data-reader-preview) definice role, která zahrnuje operace v obou `actions` a `dataActions` oddíly. Tato role umožňuje číst kontejneru objektů blob a také základní data objektů blob.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Allows for read access to Azure Storage blob containers and data.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/read"
        ],
        "additionalProperties": {},
        "dataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
        ],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Storage Blob Data Reader (Preview)",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Pouze operace, data mohou být přidány do `dataActions` a `notDataActions` oddíly. Zprostředkovatelé prostředků identifikovat operací, které jsou data operací, a to nastavením `isDataAction` vlastnost `true`. Chcete-li zobrazit seznam operací, kde `isDataAction` je `true`, najdete v části [operace poskytovatele prostředků](resource-provider-operations.md). Role, které nemají operace dat nemusí mít `dataActions` a `notDataActions` částí v definici role.

Autorizace pro všechna volání rozhraní API management operace zpracovává pomocí Správce prostředků Azure. Autorizace pro volání rozhraní API operaci dat zpracovává poskytovatele prostředků nebo Azure Resource Manager.

### <a name="data-operations-example"></a>Příklad operations dat

Abyste lépe pochopili, jak fungují operace správy a data, zvažte konkrétní příklad. Alice byla přiřazena [vlastníka](built-in-roles.md#owner) role v obor předplatného. Byl přiřazen Bob [Přispěvatel Data objektů Blob Storage (Preview)](built-in-roles.md#storage-blob-data-contributor-preview) role v rozsahu účtu úložiště. Následující diagram ukazuje tento příklad.

![Řízení přístupu na základě rolí rozšířilo pro podporu správy a operace dat](./media/role-definitions/rbac-management-data.png)

[Vlastníka](built-in-roles.md#owner) role pro Alici a [Přispěvatel Data objektů Blob Storage (Preview)](built-in-roles.md#storage-blob-data-contributor-preview) role pro Roberta mít následující akce:

Vlastník

&nbsp;&nbsp;&nbsp;&nbsp;Akce<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Přispěvatel dat objektu blob služby Storage (Preview)

&nbsp;&nbsp;&nbsp;&nbsp;Akce<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Vzhledem k tomu, že Alice je zástupný znak (`*`) akce v oboru předplatné, jeho oprávnění dědit dolů o provádět všechny akce správy. Alice však nelze provádět operace data. Například ve výchozím nastavení, Alice nemůže přečíst uvnitř kontejneru objektů BLOB, ale uživatel může číst, zapisovat a odstranění kontejnerů.

Oprávnění Boba jsou omezeny na právě `actions` a `dataActions` zadaný v [Přispěvatel Data objektů Blob Storage (Preview)](built-in-roles.md#storage-blob-data-contributor-preview) role. Na základě role, Bob provádět správu a operace s daty. Například Bob čtení, zápis a odstranění kontejnerů v zadaný účet úložiště a mohl můžete také čtení, zápisu a odstranění objektů BLOB.

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Jaké nástroje podporuje pomocí RBAC pro operace dat?

Zobrazit a pracovat s datovými operacemi, musí mít správná verze nástroje nebo sady SDK:

| Nástroj  | Verze  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-azurerm-ps) | 5.6.0 nebo novější |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 nebo novější |
| [Azure pro .NET](/dotnet/azure/) | 2.8.0-Preview nebo novější |
| [Azure SDK pro přejděte](/go/azure/azure-sdk-go-install) | 15.0.0 nebo novější |
| [Azure pro jazyk Java](/java/azure/) | 1.9.0 nebo novější |
| [Azure pro jazyk Python](/python/azure) | 0.40.0 nebo novější |
| [Azure SDK pro Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 nebo novější |

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

## <a name="dataactions-preview"></a>dataActions (Preview)

`dataActions` Oprávnění určuje operace dat, ke kterým roli udělí přístup k datům v rámci tohoto objektu. Například pokud uživatel má přístup pro čtení objektů blob dat k účtu úložiště, může číst objekty BLOB v rámci tohoto účtu úložiště. Zde jsou některé příklady data operací, které mohou být používány `dataActions`.

| Operace řetězec    | Popis         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Vrátí objekt blob nebo seznam objektů BLOB. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Vrátí výsledek zápisu do objektu blob. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Vrátí zprávu. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Vrátí zprávu nebo výsledek zápis nebo odstranění zprávy. |

## <a name="notdataactions-preview"></a>notDataActions (Preview)

`notDataActions` Oprávnění určuje operace dat, které budou vyloučeny z povolené maximum `dataActions`. Udělení přístupu podle rolí (skutečná oprávnění) se počítá odečtením `notDataActions` operací `dataActions` operace. Každý poskytovatel prostředků nabízí její odpovídající sadu rozhraní API pro splnění data operací.

> [!NOTE]
> Pokud je uživatel přiřazenou roli, která nezahrnuje operace dat v `notDataActions`a je mu přiřazená druhá role, který uděluje přístup ke stejné operace dat má uživatel k provedení této operace data. `notDataActions` není odepření pravidlo – je jenom pohodlný způsob, jak vytvořit sadu povolených datovými operacemi, když se specifickými datovými operacemi muset vyloučeny.
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
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
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

## <a name="see-also"></a>Další informace najdete v tématech

* [Předdefinované role](built-in-roles.md)
* [Vlastní role](custom-roles.md)
* [Operace zprostředkovatele prostředků v Azure Resource Manager](resource-provider-operations.md)
