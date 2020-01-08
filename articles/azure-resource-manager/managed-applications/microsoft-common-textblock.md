---
title: TextBlock – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. TextBlock pro Azure Portal. Slouží k přidání textu do rozhraní.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 418056cb149f4441bac49db839a0dba40c2bb42d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652213"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft. Common. TextBlock – element uživatelského rozhraní

Ovládací prvek, který lze použít k přidání textu do rozhraní portálu.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>Schéma

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
