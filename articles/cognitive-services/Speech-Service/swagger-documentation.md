---
title: Dokumentace k Swagger – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Dokumentaci Swagger lze použít k automatickému generování sad SDK pro řadu programovacích jazyků. Swagger podporuje všechny operace v naší službě.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: e42d9713d1d61bb681c0ee51e46cc52eb0bcc807
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91363184"
---
# <a name="swagger-documentation"></a>Dokumentace ke Swaggeru

Služba rozpoznávání řeči nabízí specifikace Swagger pro interakci s několik rozhraní REST API, která se používají k importu dat, vytváření modelů, přesnosti testovacích modelů, vytváření vlastních koncových bodů, zařazování přepisů dávek a správě předplatných. Většinu operací, které jsou dostupné prostřednictvím portálu Custom Speech, se dají pomocí těchto rozhraní API dokončit programově.

> [!NOTE]
> Operace převodu řeči na text a převod textu na řeč jsou podporované jako rozhraní REST API, která jsou dále popsána ve specifikaci Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Generování kódu ze specifikace Swagger

[Specifikace Swagger](https://cris.ai/swagger/ui/index) obsahuje možnosti, které umožňují rychlé testování různých cest. Někdy je však žádoucí vygenerovat kód pro všechny cesty a vytvořit jedinou knihovnu volání, na kterých můžete založit budoucí řešení. Pojďme se podívat na proces generování knihovny Pythonu.

Musíte nastavit Swagger na stejnou oblast jako vaše předplatné služby Speech. Oblast můžete v Azure Portal ověřit pod prostředkem služby Speech Service. Úplný seznam podporovaných oblastí najdete v tématu [oblasti](regions.md).

1. Přejděte na https://editor.swagger.io.
2. Klikněte na **soubor**a pak na **importovat** .
3. Zadejte adresu URL Swagger, včetně oblasti pro předplatné služby Speech. `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Klikněte na **vygenerovat klienta** a vyberte Python.
5. Uložení klientské knihovny

Můžete použít knihovnu Python, kterou jste vygenerovali pomocí [ukázek služby pro rozpoznávání řeči na GitHubu](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Referenční dokumenty

* [REST (Swagger): dávkový přepis a přizpůsobení](https://westus.cris.ai/swagger/ui/index)
* [REST API: Převod řeči na text](rest-speech-to-text.md)
* [REST API: převod textu na řeč](rest-text-to-speech.md)

## <a name="next-steps"></a>Další kroky

* [Ukázky služby Speech na GitHubu](https://aka.ms/csspeech/samples).
* [Získejte zdarma klíč předplatného služby Speech](overview.md#try-the-speech-service-for-free)
