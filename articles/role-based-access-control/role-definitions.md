---
title: Pochopení definic rolí v Azure RBAC | Dokumentace Microsoftu
description: Další informace o definice rolí v řízení přístupu na základě role (RBAC) pro správu prostředků v Azure velice přesně kontrolovat přístup.
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
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: ea022ce9b02fd54be7959f5e9521ca5dc34e4e7b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426417"
---
# <a name="understand-role-definitions"></a>Vysvětlení definic rolí

Pokud se snažíte porozumět fungování role, nebo pokud vytváříte vlastní [vlastní roli](custom-roles.md), je užitečné k pochopení, jak jsou definované role. Tento článek popisuje podrobnosti o definicích rolí a obsahuje některé příklady.

## <a name="role-definition-structure"></a>Struktura definic rolí

*Definice role* je kolekce oprávnění. Někdy jí jednoduše říká *role*. Definice role poskytuje seznam operací, které je možné provádět, například čtení, zápis a odstranění. Můžete ho také seznam operací, které nelze provést, nebo operace související se podkladová data. Definice role obsahuje následující strukturu:

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

Operace jsou určeny pomocí řetězce, které mají tento formát:

- `{Company}.{ProviderName}/{resourceType}/{action}`

`{action}` Část řetězce operace určuje typ operace můžete provádět na typ prostředku. Například, zobrazí se následující podřetězce v `{action}`:

| Podřetězec akce    | Popis         |
| ------------------- | ------------------- |
| `*` | Zástupný znak uděluje přístup ke všem operacím, které odpovídají řetězci. |
| `read` | Povolí čtení (GET). |
| `write` | Umožňuje zapisovat operací (PUT, POST a PATCH). |
| `delete` | Umožňuje odstranit operace (DELETE). |

Tady je [Přispěvatel](built-in-roles.md#contributor) definice role ve formátu JSON. Zástupný znak (`*`) provozu `Actions` označuje, že objekt zabezpečení přiřazené k této roli mohou provádět všechny akce, nebo jinými slovy, všechno, co dokáže spravovat. To zahrnuje akce definované v budoucnu se přidá nové typy prostředků Azure. Operace v rámci `NotActions` jsou odečtena od `Actions`. V případě třídy [Přispěvatel](built-in-roles.md#contributor) role, `NotActions` odstraní tato role umožňuje spravovat přístup k prostředkům a také přiřazení přístupu k prostředkům.

```json
{
    "Name":  "Contributor",
    "Id":  "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "IsCustom":  false,
    "Description":  "Lets you manage everything except access to resources.",
    "Actions":  [
                    "*"
                ],
    "NotActions":  [
                       "Microsoft.Authorization/*/Delete",
                       "Microsoft.Authorization/*/Write",
                       "Microsoft.Authorization/elevateAccess/Action"
                   ],
    "DataActions":  [

                    ],
    "NotDataActions":  [

                       ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

## <a name="management-and-data-operations-preview"></a>Operace správy a dat (Preview)

Řízení přístupu na základě rolí pro operace správy je zadán v `Actions` a `NotActions` vlastnosti definice role. Tady je několik příkladů operací správy v Azure:

- Správa přístupu k účtu úložiště
- Vytvořit, aktualizovat nebo odstranit kontejner objektů blob
- Odstranit skupinu prostředků a všechny její prostředky

Přístup pro správu není zděděno k vašim datům. Toto oddělení brání role se zástupnými znaky (`*`) z mají stejně neomezený přístup k vašim datům. Například, pokud má uživatel [čtečky](built-in-roles.md#reader) role v rámci předplatného, pak mohou zobrazit účet úložiště, ale ve výchozím nastavení se nedá podívat na podkladová data.

Řízení přístupu na základě rolí nebyla dříve, použít pro operace s daty. Autorizace pro operace s daty různorodé přes poskytovatele prostředků. Model pro autorizace stejné řízení přístupu na základě rolí používá pro operace správy rozšířilo a operace s daty (aktuálně ve verzi preview).

Pro podporu operace s daty, byly přidány nové vlastnosti dat struktuře definice role. Operace s daty jsou určené v `DataActions` a `NotDataActions` vlastnosti. Přidáním těchto vlastností dat se udržuje oddělení mezi správou a data. To zabrání aktuální přiřazení rolí se zástupnými znaky (`*`) z náhle mají přístup k datům. Tady jsou některé operace s daty, které lze zadat v `DataActions` a `NotDataActions`:

- Přečtěte si seznam objektů BLOB v kontejneru
- Zapsat úložiště objektů blob v kontejneru
- Odstranit zprávu ve frontě

Tady je [Čtenář dat objektu Blob služby Storage (Preview)](built-in-roles.md#storage-blob-data-reader-preview) definice role, který zahrnuje operace v obou `Actions` a `DataActions` vlastnosti. Tato role umožňuje číst kontejner objektů blob a také podkladová data objektů blob.

```json
{
    "Name":  "Storage Blob Data Reader (Preview)",
    "Id":  "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "IsCustom":  false,
    "Description":  "Allows for read access to Azure Storage blob containers and data",
    "Actions":  [
                    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
                ],
    "NotActions":  [

                   ],
    "DataActions":  [
                        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
                    ],
    "NotDataActions":  [

                       ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Lze přidat pouze operace s daty `DataActions` a `NotDataActions` vlastnosti. Poskytovatelé prostředků identifikaci operací, které jsou operace s daty tak, že nastavíte `isDataAction` vlastnost `true`. Pokud chcete zobrazit seznam operací kde `isDataAction` je `true`, naleznete v tématu [operace poskytovatele prostředků](resource-provider-operations.md). Role, které nemají operace s daty nemusí mít `DataActions` a `NotDataActions` vlastnosti v rámci definice role.

Autorizace pro všechna volání rozhraní API operace správy se určují pomocí Azure Resource Manageru. Autorizace pro volání rozhraní API operace dat zařizuje služba poskytovatele prostředků nebo Azure Resource Manageru.

### <a name="data-operations-example"></a>Příklad dat operace

Abyste lépe pochopili, jak fungují operace správy a data, Pojďme se podívat na konkrétní příklad. Alice se přiřadila [vlastníka](built-in-roles.md#owner) role v oboru předplatného. Bob se přiřadila [Přispěvatel dat objektu Blob služby Storage (Preview)](built-in-roles.md#storage-blob-data-contributor-preview) role v rozsahu účtu úložiště. Následující diagram ukazuje tento příklad.

![Řízení přístupu na základě rolí se prodloužila, aby podporoval management i operace s daty](./media/role-definitions/rbac-management-data.png)

[Vlastníka](built-in-roles.md#owner) role pro Alici a [Přispěvatel dat objektu Blob služby Storage (Preview)](built-in-roles.md#storage-blob-data-contributor-preview) role pro Roberta mít následující akce:

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

Vzhledem k tomu, že Alice obsahuje zástupný znak (`*`) akci v oboru předplatného, své oprávnění dědí dolů povolit jí provádět veškeré akce správy. Alice však nemohou provádět operace s daty. Například ve výchozím nastavení, Alice nelze číst objekty BLOB v kontejneru, ale může číst, zapisovat a odstranění kontejnerů.

Oprávnění Boba jsou omezeny na jenom `Actions` a `DataActions` zadané v poli [Přispěvatel dat objektu Blob služby Storage (Preview)](built-in-roles.md#storage-blob-data-contributor-preview) role. Na základě role, Bob provádět správu a operace s daty. Například Bob čtení, zápis a odstranění kontejnerů ze zadaný účet úložiště a si můžete také čtení, zápisu a odstranění objektů BLOB.

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Jaké nástroje podporují pomocí RBAC pro operace s daty?

Zobrazení a práce s operace s daty, musí mít správné verze prvků nástrojů nebo sad SDK:

| Nástroj  | Verze  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps) | 5.6.0 nebo novější |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 nebo novější |
| [Azure for .NET](/dotnet/azure/) | 2.8.0-Preview nebo novější |
| [Azure SDK for Go](/go/azure/azure-sdk-go-install) | 15.0.0 nebo novější |
| [Azure pro Javu](/java/azure/) | 1.9.0 nebo novější |
| [Azure for Python](/python/azure) | 0.40.0 nebo novější |
| [Azure SDK pro Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 nebo novější |

Zobrazení a použití operací s daty v rozhraní REST API, je nutné nastavit **verze api-version** parametr na následující verze nebo novější:

- 2018-01-01-preview

Na webu Azure portal také umožňuje uživatelům prohlížet a spravovat obsah front a objektů Blob v kontejnerech přes Azure AD ve verzi preview prostředí. Zobrazit a spravovat obsah kontejneru kliknutím fronty nebo objektu Blob **zkoumání dat pomocí Azure AD ve verzi preview** na účet úložiště – Přehled.

![Prozkoumejte službu front a objektů Blob kontejnerů pomocí Azure AD ve verzi preview](./media/role-definitions/rbac-dataactions-browsing.png)

## <a name="actions"></a>Akce

`Actions` Oprávnění určuje, které role umožňuje provádět operace správy. Jde o kolekci operace řetězců, které identifikují zabezpečitelných operace poskytovatele prostředků Azure. Tady je několik příkladů operace správy, které lze použít v `Actions`.

| Řetězec operace    | Popis         |
| ------------------- | ------------------- |
| `*/read` | Uděluje přístup pro čtení operací pro všechny typy prostředků všechny poskytovatele prostředků Azure.|
| `Microsoft.Compute/*` | Uděluje přístup ke všem operacím pro všechny typy prostředků v poskytovateli prostředků Microsoft.Compute.|
| `Microsoft.Network/*/read` | Uděluje přístup pro čtení operací pro všechny typy prostředků v poskytovateli prostředků Microsoft.Network.|
| `Microsoft.Compute/virtualMachines/*` | Uděluje přístup ke všem operacím virtuálních počítačů a jeho podřízené typy prostředků.|
| `microsoft.web/sites/restart/Action` | Uděluje přístup k restartování webové aplikace.|

## <a name="notactions"></a>NotActions

`NotActions` Oprávnění určuje operace správy, které jsou vyloučené z povolených `Actions`. Použití `NotActions` oprávnění, pokud sadu operací, které chcete povolit je mnohem snazší definované s výjimkou operace s omezeným přístupem. Udělení přístupu podle role (efektivní oprávnění) je vypočítána odečtením `NotActions` operací `Actions` operace.

> [!NOTE]
> Pokud uživatel je přiřazena role, který vylučuje operace v `NotActions`a je mu přiřazená druhá role, která uděluje přístup ke stejné operace má uživatel k provedení této operace. `NotActions` není odmítnout pravidlo – je jednoduše pohodlný způsob, jak vytvořit sadu povolených operací při určité operace je třeba vyloučit.
>

## <a name="dataactions-preview"></a>DataActions (Preview)

`DataActions` Oprávnění určuje datové operace, které povoluje roli mají být provedeny ke svým datům v rámci daného objektu. Například pokud uživatel má přístup k datům objektu blob do účtu úložiště najdete potom může číst objekty BLOB v rámci tohoto účtu úložiště. Tady je několik příkladů operace s daty, které lze použít v `DataActions`.

| Řetězec operace    | Popis         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Vrátí objekt blob nebo seznam objektů BLOB. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Vrátí výsledek zápisu objektu blob. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Vrátí zprávu. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Vrátí zprávu nebo psaní nebo odstranění zprávy. |

## <a name="notdataactions-preview"></a>NotDataActions (Preview)

`NotDataActions` Oprávnění určuje datové operace, které jsou vyloučené z povolených `DataActions`. Udělení přístupu podle role (efektivní oprávnění) je vypočítána odečtením `NotDataActions` operací `DataActions` operace. Každý poskytovatel prostředků nabízí jeho odpovídající sadu rozhraní API pro splnění operace s daty.

> [!NOTE]
> Pokud uživatel je přiřazena role, který vylučuje operace s daty v `NotDataActions`a je mu přiřazená druhá role, která uděluje přístup ke stejné operace s daty, uživatel může k provedení této operace s daty. `NotDataActions` není odmítnout pravidlo – je jednoduše pohodlný způsob, jak vytvořit sadu operací povolených dat, když se specifickými datovými operacemi je třeba vyloučit.
>

## <a name="assignablescopes"></a>assignableScopes

`AssignableScopes` Vlastnost určuje obory (předplatná, skupiny prostředků nebo prostředky), že je k dispozici pro přiřazení role. Můžete vytvořit roli k dispozici pro přiřazení v jenom předplatná nebo skupiny prostředků, které vyžadují a ne uživatele nepořádku prostředí pro zbytek předplatných nebo skupinách prostředků. Je nutné použít aspoň jeden předplatné, skupinu prostředků nebo ID prostředku.

Předdefinované role mají `AssignableScopes` nastavena na kořenového oboru (`"/"`). Kořenového oboru označuje, že role je k dispozici pro přiřazení ve všech oborech. Příklady platných přiřaditelných oborů:

| Scénář | Příklad: |
|----------|---------|
| Role je k dispozici pro přiřazení v rámci jednoho předplatného | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Role je k dispozici pro přiřazení ve dvou předplatných | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Role je k dispozici pro přiřazení pouze ve skupině síťových prostředků | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Role je k dispozici pro přiřazení ve všech oborech | `"/"` |

Informace o `AssignableScopes` vlastních rolí, najdete v části [vlastní role](custom-roles.md).

## <a name="next-steps"></a>Další postup

* [Předdefinované role](built-in-roles.md)
* [Vlastní role](custom-roles.md)
* [Operace poskytovatele prostředků Azure Resource Manageru](resource-provider-operations.md)
