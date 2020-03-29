---
title: Překlad řeči se službou Speech
titleSuffix: Azure Cognitive Services
description: Služba rozpoznávání řeči umožňuje přidat do aplikací, nástrojů a zařízení překlad řeči v reálném čase v reálném čase. Stejné rozhraní API lze použít pro překlad řeči na řeč i řeči na text.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f51288da6af3580ba7592950cde4f17d7adad529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052622"
---
# <a name="what-is-speech-translation"></a>Co je překlad řeči?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Překlad řeči ze služby Řeč umožňuje překlad zvukových proudů v reálném čase, více jazyků a převod řeči na text. S sadou Speech SDK mají vaše aplikace, nástroje a zařízení přístup ke zdrojovým transkripcím a výstupům překladu pro daný zvuk. Průběžné výsledky transkripce a překladu jsou vráceny při detekci řeči a výsledky finále mohou být převedeny na syntetizované řeči.

Microsoft překlad motor je poháněn dvěma různými přístupy: statistický strojový překlad (SMT) a neuronový strojový překlad (NMT). SMT používá pokročilou statistickou analýzu k odhadu nejlepších možných překladů s ohledem na kontext několika slov. S NMT, neuronové sítě se používají k poskytování přesnější, přirozeně znějící překlady pomocí úplného kontextu věty přeložit slova.

Dnes společnost Microsoft používá NMT pro překlad do nejoblíbenějších jazyků. Všechny [jazyky, které](language-support.md#speech-translation) jsou k dispozici pro překlad řeči na řeč, jsou poháněny technologií NMT. Překlad řeči na text může v závislosti na dvojici jazyků používat SMT nebo NMT. Pokud je cílový jazyk podporován NMT, je plný překlad nmt poháněl. Pokud cílový jazyk není podporován NMT, překlad je hybrid NMT a SMT, pomocí angličtiny jako "pivot" mezi dvěma jazyky.

## <a name="core-features"></a>Základní funkce

Zde jsou funkce dostupné prostřednictvím sad SPEECH SDK a REST API:

| Případ použití | Sada SDK | REST |
|----------|-----|------|
| Překlad řeči na text s výsledky rozpoznávání. | Ano | Ne |
| Překlad řeči na řeč. | Ano | Ne |
| Průběžné uznání a výsledky překladu. | Ano | Ne |

## <a name="get-started-with-speech-translation"></a>Začínáme s překladem řeči

Nabízíme rychlé starty navržené tak, abyste mohli spustit kód za méně než 10 minut. Tato tabulka obsahuje seznam rychlých startů překladu řeči uspořádaných podle jazyka.

| Rychlý start | Platforma | referenční dokumentace k rozhraní API |
|------------|----------|---------------|
| [C#, .NET Jádro](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Procházet](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Procházet](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Procházet](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Procházet](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Procházet](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Ukázka kódu

Ukázkový kód pro sadu Speech SDK je k dispozici na GitHubu. Tyto ukázky zahrnují běžné scénáře, jako je čtení zvuku ze souboru nebo datového proudu, nepřetržité rozpoznávání/překlad s jedním výstřelem a práce s vlastními modely.

* [Ukázky převodu řeči na text a překlad (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Průvodci migrací

Pokud vaše aplikace, nástroje nebo produkty používají [překladač řeči API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview), jsme vytvořili průvodce, které vám pomohou migrovat do služby Řeči.

* [Migrace z rozhraní API pro překladač řeči do služby Řeč](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [ROZHRANÍ REST API: Převod řeči na text](rest-speech-to-text.md)
* [ROZHRANÍ REST API: Převod textu na řeč](rest-text-to-speech.md)
* [ROZHRANÍ REST API: Dávkový přepis a přizpůsobení](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další kroky

* [Získejte zdarma klíč předplatného služby Speech](get-started.md)
* [Získání sady SDK pro rozpoznávání řeči](speech-sdk.md)
