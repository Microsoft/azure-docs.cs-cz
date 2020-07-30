---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 01d102de24185eba789e041d8534d6fdce8c260b
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87374651"
---
V části **Spravovat** (v pravém horním rohu) na stránce **prostředky Azure** (nabídka vlevo) zkopírujte adresu URL **ukázkového dotazu** a vložte ji do nové karty prohlížeče.

Adresa URL koncového bodu vypadá jako v následujícím formátu s vlastní subdoménou, ID aplikace a klíčem koncového bodu, který nahrazuje ID aplikace a ID klíče:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```