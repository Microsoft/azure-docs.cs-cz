---
title: Oblasti služby řeči | Microsoft Docs
description: Referenční informace pro oblasti řeči služby.
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/27/2018
ms.author: mahilleb
ms.openlocfilehash: a201cc043f673e2285ea48950804d97b96f881ed
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054964"
---
# <a name="regions-and-endpoints-of-the-speech-service"></a>Koncové body služby řeči a oblasti

> [!NOTE]
> Oblast názvy v [řeči SDK](speech-sdk.md) odpovídat první součástí domény koncových bodů vypsáni níže.
> Například použít `westus` k určení oblasti západní USA v sadě SDK řeči.

## <a name="speech-to-text"></a>Převod řeči na text

[!include[](includes/endpoints-speech-to-text.md)]

## <a name="text-to-speech"></a>Převod textu na řeč

[!include[](includes/endpoints-text-to-speech.md)]

## <a name="authentication"></a>Authentication

[!include[](includes/endpoints-token-service.md)]

V tématu [sem](rest-apis.md#authentication) podrobnosti získání a aktualizaci tokeny autorizace.

## <a name="language-understanding-speech-sdk-only"></a>Principy jazyka (pouze řeči SDK)

Oblasti pro službu znalosti jazyka jsou uvedeny [zde](/azure/cognitive-services/luis/luis-reference-regions).
V sadě SDK rozpoznávání řeči, zadejte tyto oblasti první část názvu domény koncového bodu (například `westus`).
