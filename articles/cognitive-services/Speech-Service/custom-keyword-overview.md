---
title: Vlastní klíčová slova – služba Speech
titleSuffix: Azure Cognitive Services
description: Přehled funkcí, možností a omezení pro vlastní klíčová slova pomocí sady Speech Software Development Kit (SDK).
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: dd5748cf8afe19a49e5ea406aea9b558432eeaf3
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82802178"
---
# <a name="what-is-a-keyword"></a>Co je klíčové slovo?

Klíčové slovo je slovo nebo krátká fráze, která umožňuje, aby byl váš produkt hlasově aktivovaný. Například "Hey Cortana" je klíčové slovo pro pomocníka Cortana. Aktivace hlasu umožňuje uživatelům zahájit interakci s produktem zcela bez jakýchkoli rukou pouhým vyvoláním klíčového slova. Vzhledem k tomu, že váš produkt nepřetržitě naslouchá klíčovému slovu, se veškerý zvuk zpracovává místně na zařízení uživatele, dokud se neobjeví detekce, která zajistí, aby data zůstala co možná soukromá.

## <a name="core-features-of-custom-keyword"></a>Základní funkce vlastního klíčového slova

Díky funkcím pro přizpůsobení, výkon a integraci vlastního klíčového slova můžete přizpůsobovat aktivaci hlasu tak, aby vyhovovala potřebám vašeho produktu a potřebám uživatelů.

| Funkce | Popis |
|----------|----------|
| Přizpůsobení klíčového slova | Klíčové slovo představuje rozšíření vaší značky a zvyšuje tak jmění, které jste sestavili s vašimi zákazníky. Vlastní portál klíčového slova v studiu řeči umožňuje zadat libovolné slovo nebo krátkou frázi, která nejlépe odpovídá vaší značce. Klíčové slovo můžete dále přizpůsobit tak, že vyberete správné výslovnosti, která bude dodržena generovaným modelem klíčových slov.
| Ověření klíčového slova | Když je klíčové slovo, které se detekuje místně, velmi důvěrné, pošle se do cloudu pro další ověření, že uživatel uvedli klíčové slovo. Ověřování pomocí klíčového slova poskytuje další úroveň zabezpečení tím, že snižuje dopad nesprávného místního zjišťování a ochranu osobních údajů uživatelů.
| Hlasový asistent & integrace sady Speech SDK | Klíčová slova generovaná z klíčového slova Custom v sadě Speech Studio je možné snadno integrovat do svého zařízení nebo aplikace prostřednictvím sady Speech SDK. Jednoduše nasměrujte sadu SDK na klíč klíčového slova poskytnutý programem Speech Studio a váš produkt bude hlas aktivovaný na základě ověření pomocí klíčového slova. Hlasové prostředí vašeho produktu můžete dokončit vytvořením vlastního [hlasového asistenta](voice-assistants.md).

## <a name="sample-code"></a>Ukázka kódu

Vzorový kód pro integraci modelu klíčového slova je k dispozici na GitHubu. Tyto ukázky zahrnují klientskou aplikaci pro integraci vašeho klíčového slova do několika oblíbených programovacích jazyků.

* [Kurz: hlas – aktivace produktu pomocí sady Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Ukázky hlasového asistenta (SDK)](https://aka.ms/csspeech/samples)

## <a name="tutorial"></a>Kurz

* [Vytvoření vlastního klíčového slova pomocí rozpoznávání řeči Studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
* Způsob, jakým [hlas aktivujete, pomocí sady Speech SDK](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="reference-docs"></a>Referenční dokumenty

* [Vlastní pokyny pro pojmenovávání klíčových slov](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-kws-guidelines)
* [Speech SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>Další kroky

* [Získejte zdarma klíč předplatného služby Speech](get-started.md)
* [Vytvoření vlastního klíčového slova](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
* [Získat sadu Speech SDK](speech-sdk.md)
* [Další informace o hlasových asistentech](voice-assistants.md)
