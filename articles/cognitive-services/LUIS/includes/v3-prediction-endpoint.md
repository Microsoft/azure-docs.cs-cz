---
title: Jak získat koncový bod předpovědi V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bc6e43faca47e360daa8214e6b9f6e9df4a2f130
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495277"
---
1. Na portálu LUIS v části spravovat (v pravé horní nabídce) na stránce klíče a koncové body (nabídka vlevo) vyberte adresu URL koncového bodu v dolní části stránky.

    Tato akce otevře kartu prohlížeče s adresou URL koncového bodu na adresním řádku.

    Adresa URL má ID aplikace, klíč a název slotu. Adresa URL koncového bodu verze V3 vypadá takto:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=UTTERANCE-TEXT.`

