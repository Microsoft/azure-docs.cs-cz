---
title: Převod řeči na text s Azure hlasové služby
titleSuffix: Azure Cognitive Services
description: Převod řeči na text z Azure hlasové služby, označované také jako převod řeči na text, umožňuje v reálném čase přepisu zvuku datových proudů do textu, které využívají vaše aplikace, nástroje nebo zařízení, zobrazit a provádět akce na jako vstup příkazu. Tato služba používá stejnou technologii rozpoznávání, který společnost Microsoft používá pro produkty Cortana a Office a bez problémů spolupracuje s překlad a převod textu na řeč.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 7aa4492a22c8aca1e54570adb2811e4cb13caf7b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900773"
---
# <a name="what-is-speech-to-text"></a>Co je převod řeči na text?

Převod řeči na text z Azure hlasové služby, označované také jako převod řeči na text, umožňuje v reálném čase přepisu zvuku datových proudů do textu, které využívají vaše aplikace, nástroje nebo zařízení, zobrazit a provádět akce na jako vstup příkazu. Tato služba používá stejnou technologii rozpoznávání, který společnost Microsoft používá pro produkty Cortana a Office a bez problémů spolupracuje s překlad a převod textu na řeč.

Ve výchozím nastavení používá služba speech to text univerzální jazykový model. Tento model se naučil pomocí dat ve vlastnictví společnosti Microsoft a je nasazena v cloudu. Je ideální pro konverzační a scénáře diktování. Pokud používáte speech to text pro rozpoznávání a přepis v jedinečné prostředí, můžete vytvořit a trénování vlastních akustických, jazyk a výslovnost modelů hluk adresu nebo slovníku specifických pro dané odvětví.

Můžete snadno zaznamenat zvuk z mikrofonu, čtení z datového proudu nebo přístup zvukové soubory ze služby storage se sadou SDK pro řeč a rozhraní REST API. Sadou SDK pro řeč podporuje WAV/PCM 16-bit, 16 kHz, zvuk jeden kanál pro rozpoznávání řeči. Další formáty zvuku jsou podporovány při použití [koncový bod REST speech to text](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-formats) nebo [určené k transkripci služba batch](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Základní funkce

Zde jsou funkce k dispozici prostřednictvím rozhraní REST API a sadou SDK pro řeč:

| Případ použití | Sada SDK | REST |
|----------|-----|------|
| Přepisy krátký projevy (< 15 sekund). Podporuje jenom určené k transkripci konečný výsledek. | Ano | Ano |
| Průběžné určené k transkripci promluvy dlouhé a streamování zvuku (> 15 sekund). Podporuje určené k transkripci dočasné a finální výsledky. | Ano | Ne |
| Záměry odvozovat výsledky rozpoznávání s [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Ano | Ne\* |
| Přepis zvukové soubory dávky asynchronně. | Ne | Ano\** |
| Vytvářet a spravovat modely řeči. | Ne | Ano\** |
| Vytvářejte a spravujte nasazení vlastního modelu. | Ne | Ano\** |
| Vytvořte testy přesnosti měření přesnost modelu směrného plánu a vlastních modelů. | Ne | Ano\** |
| Spravujte předplatná. | Ne | Ano\** |

\* *Služba LUIS záměry a entity, může být odvozena pomocí samostatné předplatné služby LUIS. Toto předplatné sady SDK můžete zavolat LUIS a zadejte entity a záměru výsledky. Pomocí rozhraní REST API, můžete volat LUIS sami sebe k odvození záměry a entity ve vašem předplatném služby LUIS.*

\** *Tyto služby jsou k dispozici prostřednictvím cris.ai koncového bodu. Zobrazit [Swagger odkaz](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Začínáme s řeči na text

Nabízíme rychlé starty v nejoblíbenějších programovacích jazyků, každý navržené tak, aby se spouštěním kódu za méně než 10 minut. Tato tabulka obsahuje úplný seznam šablon rychlý start sadou SDK pro řeč uspořádané podle jazyka.

| Rychlý start | Platforma | API – referenční informace |
|------------|----------|---------------|
| [C#, .NET Core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) | Windows | [Procházet](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) | Windows | [Procházet](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) | Windows | [Procházet](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) | Windows | [Procházet](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Procházet](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Procházet](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows, Linux | [Procházet](https://aka.ms/csspeech/javaref) |
| [Javascript, Browser](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | Browser, Windows, Linux, macOS | [Procházet](https://aka.ms/AA434tv) |
| [Javascript, Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows, Linux, macOS | [Procházet](https://aka.ms/AA434tv) |
| [Objective-C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Procházet](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows, Linux, macOS | [Procházet](https://aka.ms/AA434tr)  |

Pokud chcete použít převod řeči na text služby REST, přečtěte si téma [rozhraní REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Kurzy a ukázky kódu

Poté, co jste využili příležitost dobře se použijte hlasové služby, vyzkoušejte v našem kurzu, která vás naučí, jak rozpoznat záměry z řeči pomocí sadou SDK pro řeč a LUIS.

* [Kurz: Rozpoznat záměry z řeči se sadou SDK pro řeč a LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)

Ukázkový kód pro zpracování řeči SDK je k dispozici na Githubu. Tyto ukázky zahrnují běžné scénáře, jako jsou čtení zvuk ze souboru nebo datový proud, rozpoznávání průběžné a jednorázová, prostředků a práci s vlastní modely.

* [Ukázky řeči na text (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Přepis ukázek služby Batch (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Přizpůsobení

Kromě univerzální model používaný hlasové služby můžete vytvořit vlastních akustických a jazyk, výslovnost modelů, které jsou specifické vašeho prostředí. Tady je seznam možností přizpůsobení:

| Model | Popis |
|-------|-------------|
| [Akustický model](how-to-customize-acoustic-models.md) | Vytvoření vlastního akustického modelu je užitečné, pokud vaše aplikace, nástroje nebo zařízení se používají v konkrétním prostředí, stejně jako v automobilu nebo objekt pro vytváření s konkrétní záznam podmínky. Ukázky zahrnují řeč s přízvukem, určité zvuky na pozadí nebo použití určitého mikrofonu pro nahrávání. |
| [Jazykový model](how-to-customize-language-model.md) | Vytvoření vlastního jazykového modelu pro zlepšení přepis slovník specifických pro dané odvětví a gramatiku, jako jsou lékařské terminologie nebo žargonu IT. |
| [Model výslovnosti](how-to-customize-pronunciation.md) | Pomocí vlastních výslovnost modelu můžete definovat zapsané ve fonetické formuláře a zobrazení slovo nebo termín. Je vhodný pro zpracování vlastní podmínky, jako jsou názvy produktů nebo zkratky. Vše, co potřebujete, abyste mohli začít se výslovnost soubor – soubor .txt jednoduché. |

> [!NOTE]
> Možnosti vlastního nastavení se liší podle jazyka a národního prostředí (viz [podporované jazyky](supported-languages.md)).

## <a name="migration-guides"></a>Průvodce migrací

> [!WARNING]
> Pro zpracování řeči Bingu se vyřadit z provozu 15. října 2019.

Pokud vaše aplikace, nástroje nebo produkty používají rozhraní API pro zpracování řeči Bingu nebo Custom Speech, jsme vytvořili, kteří vám pomůžeme s migrací hlasové služby.

* [Migrace z pro zpracování řeči Bingu do hlasové služby](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Migrace z vlastní převod řeči na hlasové služby](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Rozpoznávání řeči zařízení SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Přepis služby batch a přizpůsobení](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další postup

* [Získejte klíč předplatného hlasových služeb zdarma](get-started.md)
* [Získání sady SDK pro řeč](speech-sdk.md)
