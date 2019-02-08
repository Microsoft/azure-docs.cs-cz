---
title: Rozpoznávání řeči pomocí rozhraní REST API
titleSuffix: Azure Cognitive Services
description: Další informace o použití Speech to Text API ve službě Cognitive Services řeči.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: erhopf
ms.openlocfilehash: 1d8ae196884d800f441943609ac0189260a0ffcf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865723"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Rozpoznávání řeči pomocí rozhraní REST API

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

Rozhraní REST API slouží k rozpoznání krátkých projevy pomocí požadavku HTTP POST.

Rozhraní REST API je nejjednodušší způsob, jak rozpoznávání řeči, pokud nepoužíváte jazyk, který je podporován [SDK](speech-sdk.md). Vytvořit požadavek HTTP POST do koncového bodu služby a předat celé utterance v textu požadavku. Obdržíte odpověď, která má rozpoznaný text.

> [!NOTE]
> Projevy jsou omezená na 15 sekund nebo méně při použití rozhraní REST API.
> Podívejte se [sadou SDK pro řeč](how-to-recognize-speech-csharp.md) pro rozpoznávání projevy delší dobu.

Další informace o **převod řeči na Text** rozhraní REST API najdete v tématu [rozhraní REST API](rest-apis.md#speech-to-text-api) článku. Pokud chcete zobrazit rozhraní API v akci, stáhněte si [ukázky rozhraní REST API](https://github.com/Azure-Samples/SpeechToText-REST) z Githubu.

## <a name="next-steps"></a>Další postup

- Zobrazit [přehled rozhraní REST API](rest-apis.md).
