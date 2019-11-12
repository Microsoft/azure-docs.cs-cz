---
title: Element uživatelského rozhraní Azure TagsByResource
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. TagsByResource pro Azure Portal. Použijte k aplikování značek na prostředek během nasazování.
author: tfitzmac
ms.service: managed-applications
ms.topic: reference
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 5711759666cd68dc93e5bfb67e51ec1cc0a48bd1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73933139"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft. Common. TagsByResource – element uživatelského rozhraní

Ovládací prvek pro přidružení [značek](../azure-resource-manager/resource-group-using-tags.md) k prostředkům v nasazení.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft. Common. DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

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

- Musí být zadána alespoň jedna položka v `resources`m poli.
- Každý prvek v `resources` musí být plně kvalifikovaný typ prostředku. Tyto prvky se zobrazí v rozevíracím seznamu **prostředku** a uživatel je taggable.
- Výstup ovládacího prvku je naformátován pro snadné přiřazení hodnot značek v šabloně Azure Resource Manager. Chcete-li přijmout výstup ovládacího prvku v šabloně, zahrňte parametr do šablony, jak je znázorněno v následujícím příkladu:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Pro každý prostředek, který může být tagovaný, přiřaďte vlastnost Tags k hodnotě parametru pro tento typ prostředku:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Při přístupu k parametru tagsByResource použijte funkci [if](../azure-resource-manager/resource-group-template-functions-logical.md#if) . Umožňuje přiřadit prázdný objekt, když se k danému typu prostředku nepřiřazují žádné značky.

## <a name="next-steps"></a>Další kroky

- Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
- Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
