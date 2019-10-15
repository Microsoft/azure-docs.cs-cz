---
title: Element uživatelského rozhraní rozevíracího seznamu Azure | Microsoft Docs
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. DropDown pro Azure Portal. Slouží k výběru z dostupných možností při nasazování spravované aplikace.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5784b5df9b522f0489ca1f6087627b45cec5349d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331744"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft. Common. DropDown – element uživatelského rozhraní
Ovládací prvek výběru s rozevíracím seznamem.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
![Microsoft. Common. DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="remarks"></a>Poznámky

- Popisek pro `constraints.allowedValues` je zobrazený text pro položku a jeho hodnota je výstupní hodnota prvku, pokud je vybrána možnost.
- Je-li tento parametr zadán, musí být výchozí hodnota popisek, který je přítomný v `constraints.allowedValues`. Pokud není zadaný, vybere se první položka v `constraints.allowedValues`. Výchozí hodnota je **null**.
- `constraints.allowedValues` musí mít alespoň jednu položku.
- Chcete-li emulovat hodnotu, která není požadována, přidejte položku s popiskem a hodnotou `""` (prázdný řetězec) pro `constraints.allowedValues`.

## <a name="sample-output"></a>Ukázkový výstup
```json
"two"
```

## <a name="next-steps"></a>Další kroky
* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
