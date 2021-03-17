---
title: EditableGrid – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. EditableGrid pro Azure Portal. Umožňuje uživatelům shromažďovat tabelární vstup.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
ms.openlocfilehash: 04f86883a75110985d1cbe050fe3fd3e0582986a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88893712"
---
# <a name="microsoftcommoneditablegrid-ui-element"></a>Microsoft. Common. EditableGrid – element uživatelského rozhraní

Ovládací prvek pro shromažďování tabulkového vstupu. Všechna pole v mřížce jsou upravitelná a počet řádků se může lišit.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

:::image type="content" source="./media/managed-application-elements/microsoft-common-editablegrid.png" alt-text="Microsoft. Common. EditableGrid":::

## <a name="schema"></a>Schéma

```json
{
  "name": "people",
  "type": "Microsoft.Common.EditableGrid",
  "ariaLabel": "Enter information per person",
  "label": "People",
  "constraints": {
    "width": "Full",
    "rows": {
      "count": {
        "min": 1,
        "max": 10
      }
    },
    "columns": [
      {
        "id": "colName",
        "header": "Name",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.TextBox",
          "placeholder": "Full name",
          "constraints": {
            "required": true,
            "validations": [
              {
                "isValid": "[startsWith(last(take(steps('grid').people, $rowIndex)).colName, 'contoso')]",
                "message": "Must start with 'contoso'."
              },
              {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
              }
            ]
          }
        }
      },
      {
        "id": "colGender",
        "header": "Gender",
        "width": "1fr",
        "element": {
          "name": "dropDown1",
          "type": "Microsoft.Common.DropDown",
          "placeholder": "Select a gender...",
          "constraints": {
            "allowedValues": [
              {
                "label": "Female",
                "value": "female"
              },
              {
                "label": "Male",
                "value": "male"
              },
              {
                "label": "Other",
                "value": "other"
              }
            ],
            "required": true
          }
        }
      },
      {
        "id": "colContactPreference",
        "header": "Contact preference",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.OptionsGroup",
          "constraints": {
            "allowedValues": [
              {
                "label": "Email",
                "value": "email"
              },
              {
                "label": "Text",
                "value": "text"
              }
            ],
            "required": true
          }
        }
      }
    ]
  }
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
{
  "colName": "contoso",
  "colGender": "female",
  "colContactPreference": "email"
}
```

## <a name="remarks"></a>Poznámky

- Jedinými platnými ovládacími prvky v rámci pole Columns jsou [TextBox](microsoft-common-textbox.md), [Options](microsoft-common-optionsgroup.md)a [DropDown](microsoft-common-dropdown.md).
- Tato `$rowIndex` proměnná je platná pouze ve výrazech obsažených v podřízených objektech sloupců mřížky. Jedná se o celé číslo, které představuje index relativního řádku prvku a počet začíná na jednu a zvýší o jednu. Jak je znázorněno v `"columns":` části schématu, se `$rowIndex` používá k ověření.
- Pokud se ověření provádí pomocí `$rowIndex` proměnné, je možné získat hodnotu aktuálního řádku kombinováním `last()` `take()` příkazů a.

  Například:

  `last(take(<reference_to_grid>, $rowIndex))`

- `label`Vlastnost se nezobrazí jako součást ovládacího prvku, ale zobrazuje se na konečném souhrnu karty.
- `ariaLabel`Vlastnost je popisek přístupnosti pro mřížku. Zadejte užitečný text pro uživatele, kteří používají čtečky obrazovky.
- `constraints.width`Vlastnost se používá k nastavení celkové šířky mřížky. Možnosti jsou _úplné_, _střední_, _malé_. Výchozí hodnota je _plná_.
- `width`Vlastnost na podřízených sloupcích sloupce určuje šířku sloupce. Šířky se zadávají pomocí zlomkových jednotek, jako je _3fr_, přičemž celkové místo se přiděluje sloupcům úměrným jejich jednotkám. Pokud není zadaná žádná šířka sloupce, výchozí hodnota je _1fr_.

## <a name="next-steps"></a>Další kroky

- Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
- Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
