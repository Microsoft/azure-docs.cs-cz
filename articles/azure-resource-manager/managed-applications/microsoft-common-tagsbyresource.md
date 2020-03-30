---
title: Element ui služby TagsByResource
description: Popisuje element microsoft.common.tagsbyresource ui pro portál Azure. Slouží k použití značek na prostředek během nasazení.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652200"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Element uzlina microsoft.Common.TagsByResource

Ovládací prvek pro připojování [značek](../management/tag-resources.md) s prostředky v nasazení.

## <a name="ui-sample"></a>Ukázka ui

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>Schéma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Poznámky

- Musí být zadána `resources` alespoň jedna položka v poli.
- Každý prvek `resources` v musí být plně kvalifikovaný typ prostředku. Tyto prvky se zobrazí v rozevírací **části Prostředek** a jsou taggable uživatelem.
- Výstup ovládacího prvku je formátován pro snadné přiřazení hodnot značek v šabloně Azure Resource Manager. Chcete-li získat výstup ovládacího prvku v šabloně, zahrňte do šablony parametr, jak je znázorněno v následujícím příkladu:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  U každého prostředku, který lze označit, přiřaďte vlastnost tagy hodnotě parametru pro daný typ prostředku:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Při přístupu k parametru tagsByResource použijte funkci [if.](../templates/template-functions-logical.md#if) Umožňuje přiřadit prázdný objekt, pokud nejsou přiřazeny žádné značky danému typu prostředku.

## <a name="next-steps"></a>Další kroky

- Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
- Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
