---
title: Azure prvek uživatelského rozhraní informační okno | Dokumentace Microsoftu
description: Popisuje element Microsoft.Common.TextBlock uživatelského rozhraní pro Azure portal.
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
ms.openlocfilehash: abd1329f2ebac90bf846dfd5fc5b307ddb5e52bd
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35903192"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Prvek uživatelského rozhraní Microsoft.Common.InfoBox
Ovládací prvek, který přidá informační okno. Do pole obsahuje důležité text nebo upozornění, které pomáhají uživatelům pochopit, jaké hodnoty, který píše. Můžete také propojit na identifikátor URI pro další informace.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
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

## <a name="remarks"></a>Poznámky

* Pro `icon`, použijte **žádný**, **informace**, **upozornění**, nebo **chyba**.
* `uri` Vlastnost je volitelná.

## <a name="sample-output"></a>Ukázkový výstup

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).
