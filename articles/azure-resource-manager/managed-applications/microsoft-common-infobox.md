---
title: Prvek rozhraní InfoBox
description: Popisuje prvek microsoft.common.infobox ui pro portál Azure. Při nasazování spravované aplikace se používá k přidání textu nebo upozornění.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 6d1e4a84904ef7022d9ce85803bf10285bf0b8ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652473"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Element ui rozhraní Microsoft.Common.InfoBox

Ovládací prvek, který přidá informační pole. Pole obsahuje důležitý text nebo upozornění, která uživatelům pomáhají porozumět hodnotám, které poskytují. Může také odkazovat na identifikátor URI pro další informace.

## <a name="ui-sample"></a>Ukázka ui

![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


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

* Pro `icon`, použijte **žádné**, **Informace**, **Upozornění**nebo **Chyba**.
* Ubytovací `uri` zařízení je volitelné.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
