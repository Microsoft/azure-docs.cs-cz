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
ms.openlocfilehash: 67ae05c2ec7b36d76e49f26d5765bbc68b952292
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331691"
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

## <a name="remarks"></a>Poznámky

* U `icon` použijte **žádné**, **informace**, **Upozornění**nebo **chybu**.
* Vlastnost `uri` je volitelná.

## <a name="sample-output"></a>Ukázkový výstup

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>Další kroky
* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
