---
title: TextBlock – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. TextBlock pro Azure Portal. Slouží k přidání textu do rozhraní.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: ba2c4d410891910c29ee1fda1065f8230ab171bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063859"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft. Common. TextBlock – element uživatelského rozhraní

Ovládací prvek, který lze použít k přidání textu do rozhraní portálu.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textblock.png)

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
