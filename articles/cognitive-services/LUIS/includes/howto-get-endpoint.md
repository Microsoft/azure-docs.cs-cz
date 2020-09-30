---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.openlocfilehash: 6d1ca85c59f03ae0d008342f71597f4d3ca5d97a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545557"
---
V části **Spravovat** (v pravém horním rohu) na stránce **prostředky Azure** (nabídka vlevo) zkopírujte adresu URL **ukázkového dotazu** a vložte ji do nové karty prohlížeče.

Adresa URL koncového bodu vypadá jako v následujícím formátu s vlastní subdoménou, ID aplikace a klíčem koncového bodu, který nahrazuje ID aplikace a ID klíče:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```