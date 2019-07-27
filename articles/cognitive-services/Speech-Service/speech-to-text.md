---
title: Služba Speech to text-Speech
titleSuffix: Azure Cognitive Services
description: Funkce řeči k textu ve službě Speech Service, která se označuje také jako převod řeči na text, umožňuje přepis zvukových datových proudů v reálném čase na text, který aplikace, nástroje nebo zařízení můžou spotřebovat, zobrazovat a provádět s nimi akce jako vstup příkazu. Tato služba využívá stejnou technologii rozpoznávání, kterou Microsoft používá pro Cortana a produkty Office, a bezproblémově pracuje s překlady a převodem textu na řeč.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 435de6e6516256d350ad93b121dd5a38d1512ddc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558858"
---
# <a name="what-is-speech-to-text"></a>Co je převod řeči na text?

Převod řeči na text z Azure Speech Services, označovaný také jako převod řeči na text, umožňuje přepis zvukových streamů v reálném čase na text, který vaše aplikace, nástroje nebo zařízení můžou spotřebovat, zobrazovat a provádět s nimi akce jako vstup příkazu. Tato služba využívá stejnou technologii rozpoznávání, kterou Microsoft používá pro Cortana a produkty Office, a bezproblémově pracuje s překlady a převodem textu na řeč.  Úplný seznam dostupných jazyků pro převod řeči na text najdete v tématu [podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

Ve výchozím nastavení používá služba Speech-to-text model univerzálního jazyka. Tento model byl vyškolený pomocí dat vlastněných společností Microsoft a nasazený v cloudu. Je ideální pro scénáře konverzace a diktování. Pokud používáte převod řeči na text pro rozpoznávání a přepis v jedinečném prostředí, můžete vytvořit a vyškolit vlastní modely akustického, jazyka a výslovnosti pro řešení hluku nebo slovníku specifického pro konkrétní obor.

Zvuk můžete snadno zachytit z mikrofonu, číst z datového proudu nebo přistupovat ke zvukovým souborům z úložiště pomocí sady Speech SDK a rozhraní REST API. Sada Speech SDK podporuje pro rozpoznávání řeči 16bitový zvuk WAV/PCM 16 bitů, 16 kHz/8 kHz pro rozpoznávání řeči v jednom kanálu. Další zvukové formáty jsou podporovány pomocí koncového bodu REST nebo [služby Batch přepisu](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats) [řeči pro text](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) .

## <a name="core-features"></a>Základní funkce

Tady jsou funkce, které jsou k dispozici prostřednictvím sady Speech SDK a rozhraní REST API:

| Případ použití | Sada SDK | REST |
|----------|-----|------|
| Přepisovat short projevy (< 15 sekund). Podporuje jenom konečný výsledek přepisu. | Ano | Ano |
| Plynulé přepisy dlouhého projevy a streamování zvuku (> 15 sekund). Podporuje dočasné a konečné výsledky přepisu. | Ano | Ne |
| Odvodit záměry z výsledků rozpoznávání pomocí [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Ano | Ne\* |
| Asynchronní přepis zvukových souborů v dávce. | Ne | Ano\** |
| Vytvářejte a spravujte modely řeči. | Ne | Ano\** |
| Vytváření a Správa nasazení vlastních modelů. | Ne | Ano\** |
| Vytvořte testy přesnosti pro měření přesnosti směrného modelu oproti vlastním modelům. | Ne | Ano\** |
| Správa předplatných. | Ne | Ano\** |

\* *Služba LUIS záměry a entity, může být odvozena pomocí samostatné předplatné služby LUIS. V rámci tohoto předplatného může sada SDK volat LUIS za vás a poskytovat výsledky entit a záměrů. Pomocí rozhraní REST API, můžete volat LUIS sami sebe k odvození záměry a entity ve vašem předplatném služby LUIS.*

\** *Tyto služby jsou k dispozici pomocí koncového bodu cris.ai. Viz [odkaz Swagger](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Začínáme s převodem řeči na text

Nabízíme rychlé zprovoznění v nejoblíbenějších programovacích jazycích, z nichž každá je navržena tak, aby používala kód za méně než 10 minut. [Tato tabulka](https://aka.ms/csspeech#5-minute-quickstarts) obsahuje úplný seznam rychlých startů pro sadu Speech SDK uspořádaných podle Platform a jazyka.  Reference k rozhraní API se taky dá najít [tady](https://aka.ms/csspeech#reference).

Pokud dáváte přednost používání služby REST (Speech-to-text), přečtěte si téma [rozhraní REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Kurzy a ukázkový kód

Až budete mít možnost používat služby pro rozpoznávání řeči, vyzkoušejte náš kurz, který vás učí, jak rozpoznávat záměry pomocí sady Speech SDK a LUIS.

* [Kurz: Rozpoznávání záměrů od řeči pomocí sady Speech SDK a LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)

Vzorový kód pro sadu Speech SDK je k dispozici na GitHubu. Tyto ukázky se týkají běžných scénářů, jako je čtení zvuku ze souboru nebo datového proudu, průběžné a jednorázové rozpoznávání a práce s vlastními modely.

* [Ukázky řeči na text (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Ukázky pro dávková přepisy (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Přizpůsobení

Kromě univerzálního modelu používaného pro hlasové služby můžete vytvořit vlastní akustické, jazykové a výslovnostní modely, které jsou specifické pro vaše prostředí. Tady je seznam možností přizpůsobení:

| Model | Popis |
|-------|-------------|
| [Akustický model](how-to-customize-acoustic-models.md) | Vytvoření vlastního akustického modelu je užitečné v případě, že se aplikace, nástroje nebo zařízení používají v konkrétním prostředí, jako je například automobil nebo továrna s konkrétními podmínkami záznamu. Ukázky zahrnují řeč s přízvukem, určité zvuky na pozadí nebo použití určitého mikrofonu pro nahrávání. |
| [Jazykový model](how-to-customize-language-model.md) | Vytvořte vlastní jazykový model pro zlepšení přepisu slovníku a gramatiky specifické pro konkrétní odvětví, jako je lékařské terminologie nebo žargonu. |
| [Model výslovnosti](how-to-customize-pronunciation.md) | Pomocí vlastního modelu výslovnosti můžete definovat fonetický tvar a zobrazení slova nebo termínu. Je vhodný pro zpracování vlastní podmínky, jako jsou názvy produktů nebo zkratky. Vše, co potřebujete, abyste mohli začít se výslovnost soubor – soubor .txt jednoduché. |

> [!NOTE]
> Možnosti přizpůsobení se liší podle jazyka nebo národního prostředí (viz [podporované jazyky](supported-languages.md)).

## <a name="migration-guides"></a>Příručky k migraci

> [!WARNING]
> Zpracování řeči Bingu se vyřadí 15. října 2019.

Pokud vaše aplikace, nástroje nebo produkty používají Zpracování řeči Bingu rozhraní API nebo Custom Speech, vytvořili jsme příručky, které vám pomůžou s migrací na služby pro rozpoznávání řeči.

* [Migrace z Zpracování řeči Bingu na služby pro rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Migrace z Custom Speech na služby pro rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](https://aka.ms/csspeech)
* [Sada Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Převod řeči na text](rest-speech-to-text.md)
* [REST API: Převod textu na řeč](rest-text-to-speech.md)
* [REST API: Přepis a přizpůsobení Batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další kroky

* [Získat klíč předplatného služby Speech Services zdarma](get-started.md)
* [Získat sadu Speech SDK](speech-sdk.md)
