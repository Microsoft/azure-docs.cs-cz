---
title: Element uživatelského rozhraní Azure TextBlock | Microsoft Docs
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. TextBlock pro Azure Portal. Slouží k přidání textu do rozhraní.
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
ms.openlocfilehash: da2453889f04352dbabe182772312d70deec4464
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331612"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft. Common. TextBlock – element uživatelského rozhraní
Ovládací prvek, který lze použít k přidání textu do rozhraní portálu.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
![Microsoft. Common. TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

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
