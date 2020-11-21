---
title: Přehled rozpoznávání záměrů – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Rozpoznávání záměrů vám umožňuje rozpoznávat uživatelské cíle, které jste předem definovali. Tento článek představuje přehled výhod a možností služby pro rozpoznávání záměrů.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: rozpoznávání záměru
ms.openlocfilehash: 215b25e440b8cad76e0656e47d32b184edf4ac66
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018697"
---
# <a name="what-is-intent-recognition"></a>Co je rozpoznávání záměru?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

V tomto přehledu se dozvíte o výhodách a možnostech rozpoznávání záměrů. Sada Speech SDK služeb Cognitive Services se integruje se službou Language Understanding Service (LUIS) a poskytuje rozpoznávání záměrů. Záměr je něco, co chce uživatel udělat: rezervovat si let, ověřit počasí nebo si zavolat.
Pomocí rozpoznávání záměrů můžou vaše aplikace, nástroje a zařízení určit, co uživatel chce iniciovat, nebo na základě možností, které definujete v LUIS.

## <a name="luis-key-required"></a>LUIS klíč je povinný.

* Služba LUIS se integruje se službou Speech za účelem rozpoznávání záměrů z řeči. Nepotřebujete předplatné služby Speech, stačí vám služba LUIS.
* Rozpoznávání záměru řeči je integrováno do sady SDK. Ke službě Speech můžete použít LUIS klíč.
* Rozpoznávání záměrů prostřednictvím sady Speech SDK se [nabízí v podmnožině oblastí, které podporuje Luis](./regions.md#intent-recognition).

## <a name="get-started"></a>Začínáme

Projděte si [rychlý Start](quickstarts/intent-recognition.md) a začněte s rozpoznáváním záměrů.

## <a name="sample-code"></a>Ukázka kódu

Vzorový kód pro rozpoznávání záměru:

* [Rychlý start: Použití předem připravené aplikace domácí automatizace](../luis/luis-get-started-create-app.md)
* [Rozpoznávání záměrů z řeči pomocí sady Speech SDK pro jazyk C #](./how-to-recognize-intents-from-speech-csharp.md)
* [Rozpoznávání záměrů a jiné služby pro rozpoznávání řeči využívající Unity v jazyce C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Rozpoznávání záměrů pomocí sady Speech SDK for Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Rozpoznávání záměrů a jiné služby pro rozpoznávání řeči pomocí sady Speech SDK pro C++ ve Windows](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Rozpoznávání záměrů a jiné služby pro rozpoznávání řeči pomocí sady Speech SDK for Java v systému Windows nebo Linux](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Rozpoznávání záměrů a jiné služby pro rozpoznávání řeči pomocí sady Speech SDK pro JavaScript ve webovém prohlížeči](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](./speech-sdk.md)

## <a name="next-steps"></a>Další kroky

* Dokončete [rychlé](quickstarts/intent-recognition.md) zprovoznění rozpoznávání záměrů.
* [Získejte zdarma klíč předplatného služby Speech](overview.md#try-the-speech-service-for-free)
* [Získat sadu Speech SDK](speech-sdk.md)