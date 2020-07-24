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
ms.openlocfilehash: 60bcdac0d7e19c424b007980294898638814d586
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044866"
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

## <a name="get-started-with-custom-keywords"></a>Začínáme s vlastními klíčovými slovy

* Základní klíčové slovo Basic pro základní použití a vzory návrhu najdete v tématu [základy vlastních klíčových slov](custom-keyword-basics.md) .
* Jak [hlasově aktivovat svůj produkt pomocí sady Speech SDK, pomocí jazyka C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="see-samples-on-github"></a>Zobrazit ukázky na GitHubu

* [Rozpoznání klíčových slov pomocí sady Speech SDK na Univerzální platforma Windows pomocí jazyka C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [Rozpoznání klíčových slov pomocí sady Speech SDK, na Androidu pomocí Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Další kroky

* [Získejte zdarma klíč předplatného služby Speech](get-started.md)
* [Získat sadu Speech SDK](speech-sdk.md)
* [Další informace o hlasových asistentech](voice-assistants.md)
