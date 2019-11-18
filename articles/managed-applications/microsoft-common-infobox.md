---
title: Element uživatelského rozhraní Azure InfoBox | Microsoft Docs
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. InfoBox pro Azure Portal. Slouží k přidání textu nebo upozornění při nasazování spravované aplikace.
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
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 3a72aaaa15b55b2f0fbc0a227c36a4b2f624d43b
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151323"
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

* V případě `icon`použijte **žádné**, **informace**, **Upozornění**nebo **chybu**.
* Vlastnost `uri` je volitelná.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
