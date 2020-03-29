---
title: Swagger dokumentace - Řečová služba
titleSuffix: Azure Cognitive Services
description: Swagger dokumentace lze použít k automatickému generování sad SDK pro řadu programovacích jazyků. Všechny operace v našich službách jsou podporovány společností Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fcc43caf895dadfaf832a47c3254f9b828bcb71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77430801"
---
# <a name="swagger-documentation"></a>Dokumentace ke Swaggeru

Služba Řeč nabízí swagger specifikace pro interakci s hrstkou REST API používá k importu dat, vytvářet modely, testování přesnosti modelu, vytvářet vlastní koncové body, fronty do fronty dávkové přepisy a spravovat odběry. Většinu operací dostupných prostřednictvím portálu vlastní řeči lze provést programově pomocí těchto rozhraní API.

> [!NOTE]
> Operace převodu řeči na text a převod textu na řeč jsou podporovány jako rozhraní REST API, které jsou zase zdokumentovány ve specifikaci Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Generování kódu ze specifikace Swagger

[Specifikace Swagger](https://cris.ai/swagger/ui/index) má možnosti, které vám umožní rychle testovat různé cesty. Někdy je však žádoucí generovat kód pro všechny cesty a vytvořit jednu knihovnu volání, na které můžete založit budoucí řešení. Podívejme se na proces generování knihovny Pythonu.

Budete muset nastavit Swagger do stejné oblasti jako vaše předplatné služby Řeči. Svou oblast můžete potvrdit na portálu Azure pod zdrojem služby Speech. Úplný seznam podporovaných oblastí naleznete v [tématu Oblasti](regions.md).

1. Přejděte na https://editor.swagger.io.
2. Klepněte na **soubor**a potom na **importovat.**
3. Zadejte adresu URL chvástání včetně oblasti předplatného služby Speech.`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Klikněte na **Generovat klienta** a vyberte Python.
5. Uložení klientské knihovny

Můžete použít knihovnu Pythonu, kterou jste vygenerovali s [ukázkami služby Řeč na GitHubu](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Referenční dokumenty

* [REST (Swagger): Přepis dávky a přizpůsobení](https://westus.cris.ai/swagger/ui/index)
* [ROZHRANÍ REST API: Převod řeči na text](rest-speech-to-text.md)
* [ROZHRANÍ REST API: Převod textu na řeč](rest-text-to-speech.md)

## <a name="next-steps"></a>Další kroky

* [Ukázky služby rozpoznávání řeči na GitHubu](https://aka.ms/csspeech/samples).
* [Získejte zdarma klíč předplatného služby Speech](get-started.md)
