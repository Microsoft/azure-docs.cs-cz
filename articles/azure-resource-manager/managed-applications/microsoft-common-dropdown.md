---
title: DropDown – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. DropDown pro Azure Portal. Slouží k výběru z dostupných možností při nasazování spravované aplikace.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: b9b27a432776635290c7e8e796e84d8c1e0e8675
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92168337"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft. Common. DropDown – element uživatelského rozhraní

Ovládací prvek výběru s rozevíracím seznamem. Můžete vybrat pouze jednu položku nebo více položek. Volitelně můžete také zahrnout popis s položkami.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Element DropDown má různé možnosti, které určují jeho vzhled na portálu.

Je-li pro výběr povolena pouze jedna položka, zobrazí se ovládací prvek jako:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Microsoft. Common. DropDown – jeden výběr":::

Pokud jsou popisy zahrnuty, zobrazí se ovládací prvek jako:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Microsoft. Common. DropDown – jeden výběr s popisy":::

Pokud je povolen vícenásobný výběr, ovládací prvek přidá možnost **Vybrat vše** a zaškrtávací políčka pro výběr více než jedné položky:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Microsoft. Common. DropDown vícenásobný výběr":::

Popisy lze zahrnout s povoleným vícenásobným výběrem.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Snímek obrazovky, který ukazuje, jak můžou být popisy zahrnuté s povoleným vícenásobným výběrem":::

Pokud je povoleno filtrování, obsahuje ovládací prvek textové pole pro přidání hodnoty filtrování.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Microsoft. Common. DropDown vícenásobný výběr s popisy":::

## <a name="schema"></a>Schéma

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "placeholder": "",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
                "value": "two"
            }
        ],
        "required": true
    },
    "visible": true
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
"two"
```

## <a name="remarks"></a>Poznámky

- Slouží `multiselect` k určení, zda mohou uživatelé vybrat více než jednu položku.
- Ve výchozím nastavení `selectAll` je v `true` případě, že je povolen vícenásobný výběr.
- `filter`Vlastnost umožňuje uživatelům vyhledávat v dlouhém seznamu možností.
- Popisek pro `constraints.allowedValues` je zobrazený text pro položku a její hodnota je výstupní hodnota prvku, když je vybraná.
- Je-li tento parametr zadán, výchozí hodnota musí být popisek přítomný v `constraints.allowedValues` . Pokud není zadán, je vybrána první položka v `constraints.allowedValues` . Výchozí hodnota je **null**.
- `constraints.allowedValues` musí mít alespoň jednu položku.
- Chcete-li emulovat hodnotu, která není požadována, přidejte položku s popiskem a hodnotou `""` (prázdný řetězec) do `constraints.allowedValues` .
- `defaultDescription`Vlastnost se používá pro položky, které nemají popis.
- `placeholder`Vlastnost je text v nápovědě, který zmizí, když uživatel začne upravovat. Pokud `placeholder` jsou a `defaultValue` definovány obě, má `defaultValue` přednost a je zobrazena.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
