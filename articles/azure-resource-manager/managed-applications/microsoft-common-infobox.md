---
title: InfoBox – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. InfoBox pro Azure Portal. Slouží k přidání textu nebo upozornění při nasazování spravované aplikace.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 6d1e4a84904ef7022d9ce85803bf10285bf0b8ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652473"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft. Common. InfoBox – element uživatelského rozhraní

Ovládací prvek, který přidá informace do textového pole. Pole obsahuje důležité texty nebo upozornění, které uživatelům pomůžou pochopit hodnoty, které poskytují. Pro další informace se může také připojit k identifikátoru URI.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft. Common. InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


## <a name="schema"></a>Schéma

```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Poznámky

* Pro `icon`použijte **možnost žádná**, **informace**, **Upozornění**nebo **Chyba**.
* `uri` Vlastnost je nepovinná.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
