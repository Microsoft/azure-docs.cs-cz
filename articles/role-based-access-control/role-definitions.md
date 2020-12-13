---
title: Vysvětlení definic rolí Azure – Azure RBAC
description: Přečtěte si o definicích rolí Azure v řízení přístupu na základě role Azure (Azure RBAC) pro detailní řízení přístupu k prostředkům Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: bc3640fecbe1138e46fd0d36975691740bc669dd
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369255"
---
# <a name="understand-azure-role-definitions"></a>Vysvětlení definic rolí Azure

Pokud se snažíte pochopit, jak funguje role Azure, nebo pokud vytváříte vlastní [vlastní roli Azure](custom-roles.md), je užitečné pochopit, jak jsou role definované. Tento článek popisuje podrobné informace o definicích rolí a obsahuje několik příkladů.

## <a name="role-definition"></a>Definice role

*Definice role* je kolekce oprávnění. Někdy se pouze nazývá *role*. Definice role poskytuje seznam operací, které je možné provádět, například čtení, zápis a odstranění. Může taky vypsat operace, které jsou vyloučené z povolených operací nebo operací souvisejících s podkladovým datům.

V následujícím příkladu vidíte příklad vlastností v definici role při zobrazení pomocí Azure PowerShell:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

V následujícím příkladu vidíte příklad vlastností v definici role při zobrazení pomocí Azure Portal, rozhraní příkazového řádku Azure nebo REST API:

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

Následující tabulka popisuje, co znamenají vlastnosti role.

| Vlastnost | Popis |
| --- | --- |
| `Name`</br>`roleName` | Zobrazovaný název role. |
| `Id`</br>`name` | Jedinečné ID role |
| `IsCustom`</br>`roleType` | Označuje, zda se jedná o vlastní roli. Nastavte na `true` nebo `CustomRole` pro vlastní role. Nastavte na `false` nebo `BuiltInRole` pro předdefinované role. |
| `Description`</br>`description` | Popis role |
| `Actions`</br>`actions` | Pole řetězců, které určuje operace správy, které může role provést. |
| `NotActions`</br>`notActions` | Pole řetězců, které určují operace správy, které jsou vyloučeny z povolených `Actions` . |
| `DataActions`</br>`dataActions` | Pole řetězců, které určuje datové operace, které může role provádět na vašich datech v rámci daného objektu. |
| `NotDataActions`</br>`notDataActions` | Pole řetězců, které určují operace s daty, které jsou vyloučeny z povolených `DataActions` . |
| `AssignableScopes`</br>`assignableScopes` | Pole řetězců, které určuje rozsahy, které je role k dispozici pro přiřazení. |

### <a name="operations-format"></a>Formát operací

Operace jsou zadány s řetězci, které mají následující formát:

- `{Company}.{ProviderName}/{resourceType}/{action}`

`{action}`Část řetězce operace určuje typ operací, které můžete provádět u typu prostředku. Například se zobrazí následující podřetězce v `{action}` :

| Podřetězec akce    | Popis         |
| ------------------- | ------------------- |
| `*` | Zástupný znak uděluje přístup ke všem operacím, které odpovídají řetězci. |
| `read` | Povoluje operace čtení (GET). |
| `write` | Povoluje operace zápisu (PUT nebo PATCH). |
| `action` | Povolí vlastní operace, jako je restartování virtuálních počítačů (POST). |
| `delete` | Povolí operace delete (odstranit). |

### <a name="role-definition-example"></a>Příklad definice role

Tady je definice role [Přispěvatel](built-in-roles.md#contributor) , jak se zobrazuje Azure PowerShell a Azure CLI. Zástupný znak operace (`*`) ve sloupci `Actions` označuje, že objekt zabezpečení přiřazený k této roli může provádět všechny akce – neboli (jinými slovy) může spravovat vše. Zahrnuje to i akce definované v budoucnosti v souvislosti s tím, jak Azure přidává nové typy prostředků. Operace ve sloupci `NotActions` se odčítají od operací ve sloupci `Actions`. V případě role [přispěvatele](built-in-roles.md#contributor) `NotActions` Odebere tuto roli schopnost spravovat přístup k prostředkům a také spravovat Azure Blueprint přiřazení.

Role přispěvatele, jak se zobrazuje v Azure PowerShell:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Role přispěvatele, jak se zobrazuje v Azure CLI:

```json
{
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
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management-and-data-operations"></a>Operace správy a dat

Řízení přístupu na základě role pro operace správy je určené ve `Actions` vlastnostech a `NotActions` definice role. Tady jsou některé příklady operací správy v Azure:

- Správa přístupu k účtu úložiště
- Vytvoření, aktualizace nebo odstranění kontejneru objektů BLOB
- Odstraní skupinu prostředků a všechny její prostředky.

Přístup pro správu se nedědí do vašich dat, pokud je metoda ověřování kontejneru nastavená na "uživatelský účet Azure AD", nikoli na přístupový klíč. Toto oddělení brání rolím se zástupnými znaky ( `*` ), aby měli neomezený přístup k vašim datům. Pokud má uživatel například roli [Čtenář](built-in-roles.md#reader) v předplatném, může zobrazit účet úložiště, ale ve výchozím nastavení nemůže zobrazit podkladová data.

Dřív se pro operace s daty nepoužilo řízení přístupu na základě role. Autorizace u datových operací mezi poskytovateli prostředků je různá. Stejný autorizační model řízení přístupu založený na rolích, který se používá pro operace správy, se rozšířil na operace s daty.

V rámci podpory datových operací byly do definice role přidány nové vlastnosti dat. Operace s daty se definují pomocí vlastností `DataActions` a `NotDataActions`. Přidáním těchto vlastností dat se zachová oddělení správy a dat. Zabrání se tak tomu, aby aktuální přiřazení role pomocí zástupných znaků (`*`) nepředvídaně umožnilo přístup k datům. Tady jsou některé datové operace, které je možné zadat pomocí vlastností `DataActions` a `NotDataActions`:

- Čtení seznamu objektů blob v kontejneru
- Zápis objektu blob úložiště v kontejneru
- Odstranění zprávy ve frontě

Tady je definice role [čtečky dat objektů BLOB úložiště](built-in-roles.md#storage-blob-data-reader) , která zahrnuje operace ve `Actions` vlastnostech a `DataActions` . Tato role umožňuje číst kontejner objektů BLOB a také podkladová data objektů BLOB.

Role čtečky dat objektů BLOB úložiště, jak se zobrazuje v Azure PowerShell:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Role čtečky dat objektů BLOB úložiště, jak se zobrazuje v Azure CLI:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

Do vlastností `DataActions` a `NotDataActions` je možné přidat jenom datové operace. Poskytovatelé prostředků identifikují operace operací s daty nastavením `isDataAction` vlastnosti na `true` . Seznam operací, na kterých `isDataAction` se nachází, najdete `true` v tématu [operace poskytovatele prostředků](resource-provider-operations.md). Role, které nemají datové operace, nemusí mít `DataActions` a `NotDataActions` vlastnosti v rámci definice role.

Ověřování pro všechna volání rozhraní API operací správy se zpracovává pomocí Azure Resource Manager. Autorizace volání rozhraní API operací s daty se zpracovává buď poskytovatelem prostředků, nebo Azure Resource Manager.

### <a name="data-operations-example"></a>Příklad operací s daty

Abychom lépe pochopili, jak fungují operace správy a dat, uvažujme konkrétní příklad. Alici byla přiřazena role [vlastníka](built-in-roles.md#owner) v oboru předplatného. Uživateli byl přiřazená role [Přispěvatel dat objektů BLOB úložiště](built-in-roles.md#storage-blob-data-contributor) v oboru účtu úložiště. Tento příklad znázorňuje následující obrázek.

![Řízení přístupu na základě role bylo rozšířeno na podporu operací správy a dat.](./media/role-definitions/rbac-management-data.png)

Role [vlastníka](built-in-roles.md#owner) pro roli [Přispěvatel dat objektu BLOB služby Storage](built-in-roles.md#storage-blob-data-contributor) pro Boba má následující akce:

Vlastník

&nbsp;&nbsp;&nbsp;&nbsp;Činností<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Přispěvatel dat v objektech blob služby Storage

&nbsp;&nbsp;&nbsp;&nbsp;Činností<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;Akce dataactions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Vzhledem k tomu, že Alice má akci zástupných znaků `*` v oboru předplatného, dědí jejich oprávnění, aby mohla provádět všechny akce správy. Alice může číst, zapisovat a odstraňovat kontejnery. Alice ale nemůže provádět operace s daty, aniž by bylo nutné provést další kroky. Ve výchozím nastavení například Alice nemůže číst objekty blob uvnitř kontejneru. Pro čtení objektů BLOB by Alice musela načíst přístupové klíče úložiště a používat je pro přístup k objektům blob.

Oprávnění Bob jsou omezená jenom na `Actions` a `DataActions` zadaná v roli [Přispěvatel dat objektu BLOB úložiště](built-in-roles.md#storage-blob-data-contributor) . Na základě role může Bob provádět operace správy i dat. Bob může například číst, zapisovat a odstraňovat kontejnery v zadaném účtu úložiště a může také číst, zapisovat a odstraňovat objekty blob.

Další informace o zabezpečení správy a roviny dat pro úložiště najdete v [Průvodci zabezpečením Azure Storage](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-azure-roles-for-data-operations"></a>Jaké nástroje podporují používání rolí Azure pro operace s daty?

Chcete-li zobrazit a pracovat s datovými operacemi, je nutné mít správné verze nástrojů nebo sad SDK:

| Nástroj  | Verze  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 nebo novější |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 nebo novější |
| [Azure pro .NET](/dotnet/azure/) | 2.8.0-Preview nebo novější |
| [Azure SDK for Go](/azure/go/azure-sdk-go-install) | 15.0.0 nebo novější |
| [Azure pro jazyk Java](/java/azure/) | 1.9.0 nebo novější |
| [Azure pro Python](/azure/python/) | 0.40.0 nebo novější |
| [Azure SDK pro Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 nebo novější |

Chcete-li zobrazit a používat operace s daty v REST API, je nutné nastavit parametr **verze rozhraní API** na následující verzi nebo novější:

- 2018-07-01

## <a name="actions"></a>Akce

`Actions`Oprávnění určuje operace správy, které může role provést. Je to kolekce řetězců operací, které identifikují zabezpečitelné operace poskytovatelů prostředků Azure. Zde je několik příkladů operací správy, které lze použít v nástroji `Actions` .

> [!div class="mx-tableFixed"]
> | Řetězec operace    | Popis         |
> | ------------------- | ------------------- |
> | `*/read` | Uděluje přístup k operacím čtení pro všechny typy prostředků všech poskytovatelů prostředků Azure.|
> | `Microsoft.Compute/*` | Uděluje přístup ke všem operacím pro všechny typy prostředků v poskytovateli prostředků Microsoft. Compute.|
> | `Microsoft.Network/*/read` | Uděluje přístup k operacím čtení pro všechny typy prostředků v poskytovateli prostředků Microsoft. Network.|
> | `Microsoft.Compute/virtualMachines/*` | Uděluje přístup ke všem operacím virtuálních počítačů a jejích podřízených typů prostředků.|
> | `microsoft.web/sites/restart/Action` | Udělí přístup k restartování webové aplikace.|

## <a name="notactions"></a>NotActions

`NotActions`Oprávnění určuje operace správy, které jsou vyloučeny z povolených `Actions` . Oprávnění použijte v `NotActions` případě, že sada operací, které chcete zakázat, je snazší definovat s vyloučením operací s omezeným přístupem. Přístup udělený rolí (efektivní oprávnění) je vypočítán odečtením `NotActions` operací od `Actions` operací.

> [!NOTE]
> Pokud je uživateli přiřazena role, která vylučuje operaci v `NotActions` a je jí přiřazena druhá role, která uděluje přístup ke stejné operaci, uživatel může tuto operaci provést. `NotActions` není pravidlo Odepřít – jedná se o pohodlný způsob, jak vytvořit sadu povolených operací, pokud je potřeba vyloučit konkrétní operace.
>

## <a name="dataactions"></a>Akce dataactions

`DataActions`Oprávnění určuje operace s daty, které role umožňuje provádět na vašich datech v rámci daného objektu. Pokud má uživatel například oprávnění číst data objektů BLOB k účtu úložiště, může číst objekty BLOB v rámci tohoto účtu úložiště. Zde je několik příkladů operací s daty, které lze použít v nástroji `DataActions` .

> [!div class="mx-tableFixed"]
> | Řetězec operace    | Popis         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Vrátí objekt BLOB nebo seznam objektů BLOB. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Vrátí výsledek zápisu objektu BLOB. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Vrátí zprávu. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Vrátí zprávu nebo výsledek zápisu nebo odstranění zprávy. |

## <a name="notdataactions"></a>NotDataActions

`NotDataActions`Oprávnění určuje operace s daty, které jsou vyloučeny z povolených `DataActions` . Přístup udělený rolí (efektivní oprávnění) je vypočítán odečtením `NotDataActions` operací od `DataActions` operací. Každý poskytovatel prostředků poskytuje odpovídající sadu rozhraní API pro splnění operací s daty.

> [!NOTE]
> Pokud má uživatel přiřazenou roli, která vylučuje datovou operaci v a `NotDataActions` má přiřazenou druhou roli, která udělí přístup ke stejné datové operaci, uživatel může tuto datovou operaci provést. `NotDataActions` není pravidlo Odepřít – jedná se o pohodlný způsob, jak vytvořit sadu povolených datových operací, když je potřeba vyloučit konkrétní datové operace.
>

## <a name="assignablescopes"></a>AssignableScopes

`AssignableScopes`Vlastnost určuje obory (skupiny pro správu, odběry nebo skupiny prostředků), které mají tuto definici role k dispozici. Role může být k dispozici pro přiřazení pouze v rámci skupin pro správu, předplatných nebo skupin prostředků, které to vyžadují. Je nutné použít alespoň jednu skupinu pro správu, předplatné nebo skupinu prostředků.

Předdefinované role jsou `AssignableScopes` nastaveny na kořenový obor ( `"/"` ). Kořenový obor určuje, že role je k dispozici pro přiřazení ve všech oborech. Mezi platné obory přiřazení patří:

> [!div class="mx-tableFixed"]
> | Role je k dispozici pro přiřazení. | Příklad |
> |----------|---------|
> | Jedno předplatné | `"/subscriptions/{subscriptionId1}"` |
> | Dvě předplatná | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Skupina síťových prostředků | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Jedna skupina pro správu | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Skupina pro správu a předplatné | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Všechny obory (platí jenom pro předdefinované role) | `"/"` |

Informace o `AssignableScopes` vlastních rolích najdete v tématu [vlastní role Azure](custom-roles.md).

## <a name="next-steps"></a>Další kroky

* [Předdefinované role v Azure](built-in-roles.md)
* [Vlastní role Azure](custom-roles.md)
* [Operace poskytovatele prostředků Azure](resource-provider-operations.md)
