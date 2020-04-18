---
title: Principy definic rolí v RBAC pro prostředky Azure | Dokumenty společnosti Microsoft
description: Další informace o definicích rolí v řízení přístupu na základě rolí (RBAC) pro jemně odstupňovanou správu přístupu prostředků Azure.
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
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 777ea7cc29679a3819e94d39913f167ea1cb3453
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641378"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Principy definic rolí pro prostředky Azure

Pokud se snažíte pochopit, jak role funguje, nebo pokud vytváříte vlastní [roli pro prostředky Azure](custom-roles.md), je užitečné pochopit, jak jsou definovány role. Tento článek popisuje podrobnosti definice rolí a poskytuje některé příklady.

## <a name="role-definition"></a>Definice role

*Definice role* je kolekce oprávnění. Někdy jí jednoduše říká *role*. Definice role poskytuje seznam operací, které je možné provádět, například čtení, zápis a odstranění. Může také obsahovat seznam operací, které není možné provádět, nebo operací souvisejících s podkladovými daty. Definice role má následující vlastnosti:

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

| Vlastnost | Popis |
| --- | --- |
| `Name` | Zobrazovaný název role. |
| `Id` | Jedinečné ID role. |
| `IsCustom` | Označuje, zda se jedná o vlastní roli. Nastaveno `true` na pro vlastní role. |
| `Description` | Popis role. |
| `Actions` | Pole řetězců, které určuje operace správy, které role umožňuje provádět. |
| `NotActions` | Pole řetězců, které určuje operace správy, které jsou `Actions`vyloučeny z povolené . |
| `DataActions` | Pole řetězců, které určuje datové operace, které role umožňuje provádět na data v rámci tohoto objektu. |
| `NotDataActions` | Pole řetězců, které určuje datové operace, které jsou `DataActions`vyloučeny z povolené . |
| `AssignableScopes` | Pole řetězců, které určuje obory, které je role k dispozici pro přiřazení. |

### <a name="operations-format"></a>Provozní formát

Operace jsou určeny pomocí řetězců, které mají následující formát:

- `{Company}.{ProviderName}/{resourceType}/{action}`

Část `{action}` řetězce operace určuje typ operací, které lze provádět u typu prostředku. Například se zobrazí následující podřetězce `{action}`v :

| Dílčí řetězec akce    | Popis         |
| ------------------- | ------------------- |
| `*` | Zástupný znak uděluje přístup ke všem operacím, které odpovídají řetězci. |
| `read` | Umožňuje operace čtení (GET). |
| `write` | Umožňuje operace zápisu (PUT nebo PATCH). |
| `action` | Umožňuje vlastní operace, jako je restartování virtuálních počítačů (POST). |
| `delete` | Povolí operace odstranění (DELETE). |

### <a name="role-definition-example"></a>Příklad definice role

Tady je definice role [přispěvatele](built-in-roles.md#contributor) ve formátu JSON. Zástupný znak operace (`*`) ve sloupci `Actions` označuje, že objekt zabezpečení přiřazený k této roli může provádět všechny akce – neboli (jinými slovy) může spravovat vše. Zahrnuje to i akce definované v budoucnosti v souvislosti s tím, jak Azure přidává nové typy prostředků. Operace ve sloupci `NotActions` se odčítají od operací ve sloupci `Actions`. V případě role [Contributor](built-in-roles.md#contributor) (Přispěvatel) se prostřednictvím `NotActions` odebere schopnost role spravovat přístup k prostředkům a také přiřazovat přístup k prostředkům.

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

## <a name="management-and-data-operations"></a>Správa a datové operace

Řízení přístupu na základě rolí pro `Actions` `NotActions` operace správy je určeno v a vlastnosti definice role. Tady je několik příkladů operací správy v Azure:

- Správa přístupu k účtu úložiště
- Vytvoření, aktualizace nebo odstranění kontejneru objektů blob
- Odstranění skupiny prostředků a všech jejích prostředků

Přístup ke správě není zděděn do vašich dat za předpokladu, že metoda ověřování kontejneru je nastavena na "Uživatelský účet Azure AD" a ne na "Přístupový klíč". Toto oddělení zabraňuje rolím`*`se zástupnými znaky ( ) v neomezeném přístupu k vašim datům. Například pokud uživatel má roli [čtenáře](built-in-roles.md#reader) na předplatné, pak mohou zobrazit účet úložiště, ale ve výchozím nastavení nemohou zobrazit podkladová data.

Dříve nebylo pro operace s daty použito řízení přístupu založené na rolích. Autorizace pro datové operace se lišila mezi poskytovateli prostředků. Stejný model autorizace řízení přístupu založený na rolích, který se používá pro operace správy, byl rozšířen na datové operace.

Pro podporu datových operací byly do definice role přidány nové vlastnosti dat. Operace s daty se definují pomocí vlastností `DataActions` a `NotDataActions`. Přidáním těchto vlastností dat je zachováno oddělení mezi správou a daty. Zabrání se tak tomu, aby aktuální přiřazení role pomocí zástupných znaků (`*`) nepředvídaně umožnilo přístup k datům. Tady jsou některé datové operace, které je možné zadat pomocí vlastností `DataActions` a `NotDataActions`:

- Čtení seznamu objektů blob v kontejneru
- Zápis objektu blob úložiště v kontejneru
- Odstranění zprávy ve frontě

Tady je definice role [čtečky dat objektů blob úložiště,](built-in-roles.md#storage-blob-data-reader) která zahrnuje operace ve vlastnostech `Actions` i `DataActions` ve vlastnostech. Tato role umožňuje číst kontejner objektů blob a také základní data objektu blob.

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

Do vlastností `DataActions` a `NotDataActions` je možné přidat jenom datové operace. Poskytovatelé prostředků identifikují operace, které `isDataAction` jsou `true`datovými operacemi, nastavením vlastnosti na . Seznam operací, kde `isDataAction` se `true`nachází, naleznete v tématu [Operace zprostředkovatele prostředků](resource-provider-operations.md). Role, které nemají operace dat, `DataActions` nemusí `NotDataActions` mít a vlastnosti v rámci definice role.

Autorizaci pro všechna volání rozhraní API operace správy zpracovává Správce prostředků Azure. Autorizace pro volání rozhraní API pro operace dat zpracovává buď poskytovatel prostředků, nebo Správce prostředků Azure.

### <a name="data-operations-example"></a>Příklad datových operací

Chcete-li lépe pochopit, jak fungují operace správy a dat, zvažte konkrétní příklad. Alice byla přiřazena role [vlastníka](built-in-roles.md#owner) v oboru předplatného. Petrovi byla přiřazena role [přispěvatele dat objektů blob úložiště](built-in-roles.md#storage-blob-data-contributor) v oboru účtu úložiště. Následující diagram ukazuje tento příklad.

![Řízení přístupu založené na rolích bylo rozšířeno tak, aby podporovalo správu i datové operace.](./media/role-definitions/rbac-management-data.png)

Role [Vlastník](built-in-roles.md#owner) pro Alici a roli [přispěvatele dat objektů blob úložiště](built-in-roles.md#storage-blob-data-contributor) pro Boba má následující akce:

Vlastník

&nbsp;&nbsp;&nbsp;&nbsp;Akce<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Přispěvatel dat objektu blob úložiště

&nbsp;&nbsp;&nbsp;&nbsp;Akce<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;Akce data<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Vzhledem k tomu,`*`že Alice má zástupný znak ( ) akce v oboru předplatného, jejich oprávnění dědí dolů, aby mohlprovádět všechny akce správy. Alice může číst, psát a odstraňovat kontejnery. Alice však nemůže provádět operace s daty bez provedení dalších kroků. Například ve výchozím nastavení Alice nemůže číst objekty BLOB uvnitř kontejneru. Chcete-li číst objekty BLOB, Alice bude muset načíst přístupové klíče úložiště a použít je pro přístup k objektům BLOB.

Oprávnění Boba jsou omezena `Actions` pouze `DataActions` na a zadaná v roli [přispěvatele dat objektů blob úložiště.](built-in-roles.md#storage-blob-data-contributor) Na základě role může Bob provádět operace správy i dat. Například Bob můžete číst, zapisovat a odstraňovat kontejnery v zadaném účtu úložiště a můžete také číst, zapisovat a odstraňovat objekty BLOB.

Další informace o správě a zabezpečení roviny dat pro úložiště najdete v [průvodci zabezpečením azure storage](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Jaké nástroje podporují použití RBAC pro datové operace?

Chcete-li zobrazit a pracovat s datovými operacemi, musíte mít správné verze nástrojů nebo sad SDK:

| Nástroj  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 nebo novější |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 nebo novější |
| [Azure pro rozhraní .NET](/dotnet/azure/) | 2.8.0-náhled nebo novější |
| [Azure SDK for Go](/azure/go/azure-sdk-go-install) | 15.0.0 nebo novější |
| [Azure pro Javu](/java/azure/) | 1.9.0 nebo novější |
| [Azure pro Python](/azure/python/) | 0.40.0 nebo novější |
| [Azure SDK pro Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 nebo novější |

Chcete-li zobrazit a použít datové operace v rozhraní REST API, musíte nastavit parametr **api-version** na následující verzi nebo novější:

- 2018-07-01

## <a name="actions"></a>Akce

Oprávnění `Actions` určuje operace správy, které role umožňuje provádět. Jedná se o kolekci řetězců operací, které identifikují seřiditelné operace poskytovatelů prostředků Azure. Zde jsou některé příklady operací správy, `Actions`které lze použít v .

> [!div class="mx-tableFixed"]
> | Řetězec operace    | Popis         |
> | ------------------- | ------------------- |
> | `*/read` | Uděluje přístup ke čtení operací pro všechny typy prostředků všech poskytovatelů prostředků Azure.|
> | `Microsoft.Compute/*` | Uděluje přístup ke všem operacím pro všechny typy prostředků ve zprostředkovateli prostředků Microsoft.Compute.|
> | `Microsoft.Network/*/read` | Uděluje přístup ke operacím čtení pro všechny typy prostředků v zprostředkovateli prostředků Microsoft.Network.|
> | `Microsoft.Compute/virtualMachines/*` | Uděluje přístup ke všem operacím virtuálních počítačů a jeho podřízených typů prostředků.|
> | `microsoft.web/sites/restart/Action` | Uděluje přístup k restartování webové aplikace.|

## <a name="notactions"></a>NotActions

Toto `NotActions` oprávnění určuje operace správy, které `Actions`jsou vyloučeny z povolené . Oprávnění `NotActions` použijte, pokud je sada operací, které chcete povolit, snadněji definována vyloučením operací s omezeným přístupem. Přístup udělený rolí (efektivní oprávnění) se vypočítá odečtením `NotActions` `Actions` operací z operací.

> [!NOTE]
> Pokud je uživateli přiřazena role, `NotActions`která vylučuje operaci v aplikaci , a je přiřazena druhá role, která uděluje přístup ke stejné operaci, může uživatel tuto operaci provést. `NotActions`není odepřít pravidlo – je to prostě pohodlný způsob, jak vytvořit sadu povolených operací, když konkrétní operace je třeba vyloučit.
>

## <a name="dataactions"></a>Akce data

Oprávnění `DataActions` určuje datové operace, které role umožňuje provádět s daty v rámci tohoto objektu. Například pokud uživatel má přístup k datům objektu blob přístup k účtu úložiště, pak mohou číst objekty BLOB v rámci tohoto účtu úložiště. Zde jsou některé příklady datových operací, které lze použít v . `DataActions`

> [!div class="mx-tableFixed"]
> | Řetězec operace    | Popis         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Vrátí objekt blob nebo seznam objektů BLOB. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Vrátí výsledek zápisu objektu blob. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Vrátí zprávu. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Vrátí zprávu nebo výsledek zápisu nebo odstranění zprávy. |

## <a name="notdataactions"></a>NotDataActions

Toto `NotDataActions` oprávnění určuje datové operace, které `DataActions`jsou vyloučeny z povolené . Přístup udělený rolí (efektivní oprávnění) se vypočítá odečtením `NotDataActions` `DataActions` operací z operací. Každý poskytovatel prostředků poskytuje příslušnou sadu api ke splnění datových operací.

> [!NOTE]
> Pokud je uživateli přiřazena role, která `NotDataActions`vylučuje datovou operaci v aplikaci , a je mu přiřazena druhá role, která uděluje přístup ke stejné datové operaci, může uživatel tuto datovou operaci provést. `NotDataActions`není odepřít pravidlo – je to prostě pohodlný způsob, jak vytvořit sadu povolených datových operací, když je třeba vyloučit konkrétní datové operace.
>

## <a name="assignablescopes"></a>AssignableScopes

Vlastnost `AssignableScopes` určuje obory (skupiny pro správu, odběry nebo skupiny prostředků), které mají k dispozici tuto definici role. Roli můžete zpřístupnit pro přiřazení pouze ve skupinách pro správu, předplatných nebo skupinách prostředků, které ji vyžadují. Musíte použít alespoň jednu skupinu pro správu, předplatné nebo skupinu prostředků.

Předdefinované role `AssignableScopes` mají nastavený na`"/"`kořenový obor ( ). Kořenový obor označuje, že role je k dispozici pro přiřazení ve všech oborech. Příklady platných přiřaditelných oborů:

> [!div class="mx-tableFixed"]
> | Role je k dispozici pro přiřazení | Příklad |
> |----------|---------|
> | Jedno předplatné | `"/subscriptions/{subscriptionId1}"` |
> | Dvě předplatná | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Skupina síťových prostředků | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Jedna skupina pro správu | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Skupina pro správu a předplatné | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Všechny obory (platí pouze pro předdefinované role) | `"/"` |

Informace o `AssignableScopes` vlastních rolích najdete [v tématu Vlastní role pro prostředky Azure](custom-roles.md).

## <a name="next-steps"></a>Další kroky

* [Předdefinované role pro prostředky Azure](built-in-roles.md)
* [Vlastní role pro prostředky Azure](custom-roles.md)
* [Operace zprostředkovatele prostředků Azure Resource Manager](resource-provider-operations.md)
