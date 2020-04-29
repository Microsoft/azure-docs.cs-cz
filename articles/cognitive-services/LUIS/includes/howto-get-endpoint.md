---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 5b1b361778de145a5e32a07bb0164ff2293d9a1a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77279596"
---
V části **Spravovat** (v pravém horním rohu) na stránce **prostředky Azure** (nabídka vlevo) zkopírujte **ukázkovou** adresu URL dotazu a vložte ji do nové karty prohlížeče.

Adresa URL koncového bodu vypadá jako v následujícím formátu a má vlastní ID aplikace a klíč koncového bodu, který nahrazuje ID aplikace a ID klíče:

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```