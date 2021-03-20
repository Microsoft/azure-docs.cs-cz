---
title: InfoBox – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. InfoBox pro Azure Portal. Slouží k přidání textu nebo upozornění při nasazování spravované aplikace.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 7580ac0650706d6aee49bbf0e8235e8c5dab33f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87033342"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft. Common. InfoBox – element uživatelského rozhraní

Ovládací prvek, který přidá informace do textového pole. Pole obsahuje důležité texty nebo upozornění, které uživatelům pomůžou pochopit hodnoty, které poskytují. Pro další informace se může také připojit k identifikátoru URI.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft. Common. InfoBox](./media/managed-application-elements/microsoft-common-infobox.png)


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

* Pro `icon` použijte **možnost žádná**, **informace**, **Upozornění** nebo **Chyba**.
* `uri`Vlastnost je nepovinná.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
