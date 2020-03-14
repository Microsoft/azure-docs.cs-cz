---
title: Pochopení definic rolí v RBAC pro prostředky Azure | Microsoft Docs
description: Seznamte se s definicemi rolí v řízení přístupu na základě role (RBAC) pro detailní řízení přístupu k prostředkům Azure.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 3ff4b2cb6a59a35dc6da4748a7c7fbb4758a4fcf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283222"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Pochopení definic rolí pro prostředky Azure

Pokud se snažíte pochopit, jak role funguje, nebo pokud vytváříte vlastní [roli pro prostředky Azure](custom-roles.md), je vhodné pochopit, jak jsou role definované. Tento článek popisuje podrobné informace o definicích rolí a obsahuje několik příkladů.

## <a name="role-definition-structure"></a>Struktura definice role

*Definice role* je kolekce oprávnění. Někdy jí jednoduše říká *role*. Definice role poskytuje seznam operací, které je možné provádět, například čtení, zápis a odstranění. Může také obsahovat seznam operací, které nelze provést, nebo operace související s podkladovým datům. Definice role má následující strukturu:

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

Operace jsou zadány s řetězci, které mají následující formát:

- `{Company}.{ProviderName}/{resourceType}/{action}`

`{action}` část řetězce operace určuje typ operací, které můžete provádět u typu prostředku. Například se zobrazí následující podřetězce v `{action}`:

| Podřetězec akce    | Popis         |
| ------------------- | ------------------- |
| `*` | Zástupný znak uděluje přístup ke všem operacím, které odpovídají řetězci. |
| `read` | Povoluje operace čtení (GET). |
| `write` | Povoluje operace zápisu (PUT nebo PATCH). |
| `action` | Povolí vlastní operace, jako je restartování virtuálních počítačů (POST). |
| `delete` | Povolí operace delete (odstranit). |

Tady je definice role [Přispěvatel](built-in-roles.md#contributor) ve formátu JSON. Operace se zástupnými znaky (`*`) pod `Actions` označuje, že objekt přiřazený k této roli může provádět všechny akce, nebo jinými slovy, může spravovat vše. Zahrnuje to i akce definované v budoucnu, protože Azure přidává nové typy prostředků. Operace v rámci `NotActions` jsou odečteny od `Actions`. V případě role [přispěvatele](built-in-roles.md#contributor) `NotActions` odebere tuto roli schopnost spravovat přístup k prostředkům a také přiřadit přístup k prostředkům.

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
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations"></a>Operace správy a dat

Řízení přístupu na základě role pro operace správy je určené ve vlastnostech `Actions` a `NotActions` definice role. Tady jsou některé příklady operací správy v Azure:

- Správa přístupu k účtu úložiště
- Vytvoření, aktualizace nebo odstranění kontejneru objektů BLOB
- Odstraní skupinu prostředků a všechny její prostředky.

Přístup pro správu se nedědí do vašich dat, pokud je metoda ověřování kontejneru nastavená na "uživatelský účet Azure AD", nikoli na přístupový klíč. Toto oddělení brání rolím se zástupnými znaky (`*`), aby měli neomezený přístup k vašim datům. Pokud má uživatel například roli [Čtenář](built-in-roles.md#reader) v předplatném, může zobrazit účet úložiště, ale ve výchozím nastavení nemůže zobrazit podkladová data.

Dřív se pro operace s daty nepoužilo řízení přístupu na základě role. Autorizace u datových operací mezi poskytovateli prostředků je různá. Stejný autorizační model řízení přístupu založený na rolích, který se používá pro operace správy, se rozšířil na operace s daty.

V rámci podpory datových operací byly do struktury definice role přidány nové vlastnosti dat. Datové operace jsou zadány ve vlastnostech `DataActions` a `NotDataActions`. Přidáním těchto vlastností dat se zachová oddělení správy a dat. Tím zabráníte tomu, aby přiřazení aktuálních rolí se zástupnými znaky (`*`) byla náhle přístup k datům. Zde jsou některé operace s daty, které lze zadat v `DataActions` a `NotDataActions`:

- Přečtěte si seznam objektů BLOB v kontejneru.
- Zápis objektu BLOB úložiště do kontejneru
- Odstranění zprávy ve frontě

Tady je definice role [čtečky dat objektů BLOB úložiště](built-in-roles.md#storage-blob-data-reader) , která zahrnuje operace ve vlastnostech `Actions` i `DataActions`. Tato role umožňuje číst kontejner objektů BLOB a také podkladová data objektů BLOB.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
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

Do `DataActions` a `NotDataActions` vlastností lze přidat pouze datové operace. Poskytovatelé prostředků identifikují operace operací s daty nastavením vlastnosti `isDataAction` na hodnotu `true`. Pokud chcete zobrazit seznam operací, které `isDataAction` `true`, přečtěte si téma [operace poskytovatele prostředků](resource-provider-operations.md). Role, které nemají operace s daty, nemusí mít `DataActions` a `NotDataActions` vlastnosti v rámci definice role.

Ověřování pro všechna volání rozhraní API operací správy se zpracovává pomocí Azure Resource Manager. Autorizace volání rozhraní API operací s daty se zpracovává buď poskytovatelem prostředků, nebo Azure Resource Manager.

### <a name="data-operations-example"></a>Příklad operací s daty

Abychom lépe pochopili, jak fungují operace správy a dat, uvažujme konkrétní příklad. Alici byla přiřazena role [vlastníka](built-in-roles.md#owner) v oboru předplatného. Uživateli byl přiřazená role [Přispěvatel dat objektů BLOB úložiště](built-in-roles.md#storage-blob-data-contributor) v oboru účtu úložiště. Tento příklad znázorňuje následující obrázek.

![Řízení přístupu na základě role bylo rozšířeno na podporu operací správy a dat.](./media/role-definitions/rbac-management-data.png)

Role [vlastníka](built-in-roles.md#owner) pro roli [Přispěvatel dat objektu BLOB služby Storage](built-in-roles.md#storage-blob-data-contributor) pro Boba má následující akce:

Vlastník

&nbsp;&nbsp;&nbsp;&nbsp;akce<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Přispěvatel dat objektu BLOB služby Storage

&nbsp;&nbsp;&nbsp;&nbsp;akce<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;ch akcí<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Vzhledem k tomu, že Alice obsahuje akci zástupných znaků (`*`) v oboru předplatného, jejich oprávnění zdědí nižší verze, aby mohly provádět všechny akce správy. Alice může číst, zapisovat a odstraňovat kontejnery. Alice ale nemůže provádět operace s daty, aniž by bylo nutné provést další kroky. Ve výchozím nastavení například Alice nemůže číst objekty blob uvnitř kontejneru. Pro čtení objektů BLOB by Alice musela načíst přístupové klíče úložiště a používat je pro přístup k objektům blob.

Oprávnění Bob jsou omezená jenom na `Actions` a `DataActions` zadaná v roli [Přispěvatel dat objektů BLOB úložiště](built-in-roles.md#storage-blob-data-contributor) . Na základě role může Bob provádět operace správy i dat. Bob může například číst, zapisovat a odstraňovat kontejnery v zadaném účtu úložiště a může také číst, zapisovat a odstraňovat objekty blob.

Další informace o zabezpečení správy a roviny dat pro úložiště najdete v [Průvodci zabezpečením Azure Storage](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Jaké nástroje podporují používání RBAC pro operace s daty?

Chcete-li zobrazit a pracovat s datovými operacemi, je nutné mít správné verze nástrojů nebo sad SDK:

| Nástroj  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 nebo novější |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 nebo novější |
| [Azure pro .NET](/dotnet/azure/) | 2.8.0-Preview nebo novější |
| [Azure SDK pro Go](/azure/go/azure-sdk-go-install) | 15.0.0 nebo novější |
| [Azure pro jazyk Java](/java/azure/) | 1.9.0 nebo novější |
| [Azure pro Python](/azure/python/) | 0.40.0 nebo novější |
| [Azure SDK pro Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 nebo novější |

Chcete-li zobrazit a používat operace s daty v REST API, je nutné nastavit parametr **verze rozhraní API** na následující verzi nebo novější:

- 2018-07-01

## <a name="actions"></a>Akce

Oprávnění `Actions` Určuje operace správy, které může role provést. Je to kolekce řetězců operací, které identifikují zabezpečitelné operace poskytovatelů prostředků Azure. Tady je několik příkladů operací správy, které je možné použít v `Actions`.

| Řetězec operace    | Popis         |
| ------------------- | ------------------- |
| `*/read` | Uděluje přístup k operacím čtení pro všechny typy prostředků všech poskytovatelů prostředků Azure.|
| `Microsoft.Compute/*` | Uděluje přístup ke všem operacím pro všechny typy prostředků v poskytovateli prostředků Microsoft. Compute.|
| `Microsoft.Network/*/read` | Uděluje přístup k operacím čtení pro všechny typy prostředků v poskytovateli prostředků Microsoft. Network.|
| `Microsoft.Compute/virtualMachines/*` | Uděluje přístup ke všem operacím virtuálních počítačů a jejích podřízených typů prostředků.|
| `microsoft.web/sites/restart/Action` | Udělí přístup k restartování webové aplikace.|

## <a name="notactions"></a>NotActions

Oprávnění `NotActions` Určuje operace správy, které jsou vyloučeny z povolených `Actions`. Oprávnění `NotActions` použijte v případě, že sada operací, které chcete zakázat, je snazší definovat s vyloučením operací s omezeným přístupem. Přístup udělený rolí (efektivní oprávnění) se vypočítává odečtením `NotActions` operací od `Actions` operací.

> [!NOTE]
> Pokud je uživateli přiřazena role, která vylučuje operaci v `NotActions`a má přiřazenou druhou roli, která udělí přístup ke stejné operaci, uživatel může tuto operaci provést. `NotActions` není pravidlo Odepřít – jedná se o pohodlný způsob, jak vytvořit sadu povolených operací, pokud je potřeba vyloučit konkrétní operace.
>

## <a name="dataactions"></a>Akce dataactions

Oprávnění `DataActions` Určuje operace s daty, které role umožňuje provádět na vašich datech v rámci daného objektu. Pokud má uživatel například oprávnění číst data objektů BLOB k účtu úložiště, může číst objekty BLOB v rámci tohoto účtu úložiště. Zde je několik příkladů operací s daty, které lze použít v `DataActions`.

| Řetězec operace    | Popis         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Vrátí objekt BLOB nebo seznam objektů BLOB. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Vrátí výsledek zápisu objektu BLOB. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Vrátí zprávu. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Vrátí zprávu nebo výsledek zápisu nebo odstranění zprávy. |

## <a name="notdataactions"></a>NotDataActions

Oprávnění `NotDataActions` Určuje operace s daty, které jsou vyloučeny z povolených `DataActions`. Přístup udělený rolí (efektivní oprávnění) se vypočítává odečtením `NotDataActions` operací od `DataActions` operací. Každý poskytovatel prostředků poskytuje odpovídající sadu rozhraní API pro splnění operací s daty.

> [!NOTE]
> Pokud je uživateli přiřazena role, která vylučuje datovou operaci v `NotDataActions`a je jí přiřazena druhá role, která uděluje přístup ke stejné datové operaci, uživatel může tuto datovou operaci provést. `NotDataActions` není pravidlo Odepřít – jedná se o pohodlný způsob, jak vytvořit sadu povolených datových operací, když je potřeba vyloučit konkrétní datové operace.
>

## <a name="assignablescopes"></a>AssignableScopes

Vlastnost `AssignableScopes` určuje obory (skupiny pro správu, předplatná, skupiny prostředků nebo prostředky), které mají tuto definici role k dispozici. Role může být k dispozici pro přiřazení pouze v rámci skupin pro správu, předplatných nebo skupin prostředků, které to vyžadují. Je nutné použít alespoň jednu skupinu pro správu, předplatné, skupinu prostředků nebo ID prostředku.

Předdefinované role mají `AssignableScopes` nastavené na kořenový obor (`"/"`). Kořenový obor určuje, že role je k dispozici pro přiřazení ve všech oborech. Mezi platné obory přiřazení patří:

| Role je k dispozici pro přiřazení. | Příklad |
|----------|---------|
| Jedno předplatné | `"/subscriptions/{subscriptionId1}"` |
| Dvě předplatná | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
| Skupina síťových prostředků | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
| Jedna skupina pro správu | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
| Skupina pro správu a předplatné | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
| Všechny obory (platí jenom pro předdefinované role) | `"/"` |

Informace o `AssignableScopes` pro vlastní role najdete v tématu [vlastní role pro prostředky Azure](custom-roles.md).

## <a name="next-steps"></a>Další kroky

* [Předdefinované role pro prostředky Azure](built-in-roles.md)
* [Vlastní role pro prostředky Azure](custom-roles.md)
* [Azure Resource Manager operace poskytovatele prostředků](resource-provider-operations.md)
