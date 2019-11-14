---
title: Služba Speech to text-Speech
titleSuffix: Azure Cognitive Services
description: Funkce převodu řeči na text umožňuje přepis zvukových streamů v reálném čase na text, který můžou aplikace, nástroje nebo zařízení spotřebovat, zobrazovat a provádět s nimi akce jako vstup příkazu. Tato služba funguje bez problémů s převodem textu na řeč (rozpoznávání řeči) a funkcemi překladu řeči.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 49bfa4a0dbf0adc498d545a2908c20f0ffa35b4b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075721"
---
# <a name="what-is-speech-to-text"></a>Co je převod řeči na text?

Převod řeči na text z Azure Speech Services, označovaný také jako převod řeči na text, umožňuje přepis zvukových streamů v reálném čase na text, který vaše aplikace, nástroje nebo zařízení můžou spotřebovat, zobrazovat a provádět s nimi akce jako vstup příkazu. Tato služba využívá stejnou technologii rozpoznávání, kterou Microsoft používá pro Cortana a produkty Office, a bezproblémově pracuje s překlady a převodem textu na řeč. Úplný seznam dostupných jazyků pro převod řeči na text najdete v tématu [podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

Ve výchozím nastavení používá služba Speech-to-text model univerzálního jazyka. Tento model byl vyškolený pomocí dat vlastněných společností Microsoft a nasazený v cloudu. Je ideální pro scénáře konverzace a diktování. Pokud používáte převod řeči na text pro rozpoznávání a přepis v jedinečném prostředí, můžete vytvořit a vyškolit vlastní modely akustického, jazyka a výslovnosti pro řešení hluku nebo slovníku specifického pro konkrétní obor.

Zvuk můžete snadno zachytit z mikrofonu, číst z datového proudu nebo přistupovat ke zvukovým souborům z úložiště pomocí sady Speech SDK a rozhraní REST API. Sada SDK služby Speech podporuje rozpoznávání řeči u 16bitových zvukových souborů WAV nebo PCM s jedním kanálem a frekvencí 16 nebo 8 kHz. Podpora dalších zvukových formátů je k dispozici při použití [koncového bodu rozhraní REST služby Převod řeči na text](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) nebo [služby dávkového přepisu](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Základní funkce

Tady jsou funkce, které jsou k dispozici prostřednictvím sady Speech SDK a rozhraní REST API:

| Případ použití | Sada SDK | REST |
|--------- | --- | ---- |
| Přepisovat short projevy (< 15 sekund). Podporuje jenom konečný výsledek přepisu. | Ano | Ano |
| Plynulé přepisy dlouhého projevy a streamování zvuku (> 15 sekund). Podporuje dočasné a konečné výsledky přepisu. | Ano | Ne |
| Odvodit záměry z výsledků rozpoznávání pomocí [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Ano | Ne\* |
| Asynchronní přepis zvukových souborů v dávce. | Ne  | Ano\*\* |
| Vytvářejte a spravujte modely řeči. | Ne | Ano\*\* |
| Vytváření a Správa nasazení vlastních modelů. | Ne  | Ano\*\* |
| Vytvořte testy přesnosti pro měření přesnosti směrného modelu oproti vlastním modelům. | Ne  | Ano\*\* |
| Správa předplatných. | Ne  | Ano\*\* |

\*_záměrů a entit Luis lze odvodit pomocí samostatného předplatného Luis. V rámci tohoto předplatného může sada SDK volat LUIS za vás a poskytovat výsledky entit a záměrů. Pomocí REST API můžete volat LUIS sami a odvodit záměry a entity s vaším předplatným LUIS._

\*\*_tyto služby jsou k dispozici pomocí koncového bodu CRIS.AI. Viz [odkaz Swagger](https://westus.cris.ai/swagger/ui/index)._

## <a name="get-started-with-speech-to-text"></a>Začínáme s převodem řeči na text

Nabízíme rychlé zprovoznění v nejoblíbenějších programovacích jazycích, z nichž každá je navržena tak, aby používala kód za méně než 10 minut. [Tato tabulka](https://aka.ms/csspeech#5-minute-quickstarts) obsahuje úplný seznam rychlých startů pro sadu Speech SDK uspořádaných podle platforem a jazyků. Reference k rozhraní API se taky dá najít [tady](https://aka.ms/csspeech#reference).

Pokud dáváte přednost používání služby REST (Speech-to-text), přečtěte si téma [rozhraní REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Kurzy a ukázkový kód

Až budete mít možnost používat služby pro rozpoznávání řeči, vyzkoušejte náš kurz, který vás učí, jak rozpoznávat záměry pomocí sady Speech SDK a LUIS.

- [Kurz: rozpoznávání záměrů z řeči pomocí sady Speech SDK a LUISC#](how-to-recognize-intents-from-speech-csharp.md)

Vzorový kód pro sadu Speech SDK je k dispozici na GitHubu. Tyto ukázky se týkají běžných scénářů, jako je čtení zvuku ze souboru nebo datového proudu, průběžné a jednorázové rozpoznávání a práce s vlastními modely.

- [Ukázky řeči na text (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Ukázky pro dávková přepisy (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Přizpůsobení

Kromě standardního směrného plánu, který využívají služby pro rozpoznávání řeči, si můžete přizpůsobit modely podle vašich potřeb s dostupnými daty, abyste překonali překážky rozpoznávání řeči, jako je mluvený styl, slovník a šum na pozadí, a přečtěte si téma [Custom Speech](how-to-custom-speech.md)

> [!NOTE]
> Možnosti přizpůsobení se liší podle jazyka nebo národního prostředí (viz [podporované jazyky](supported-languages.md)).

## <a name="migration-guides"></a>Průvodci migrací

> [!WARNING]
> Zpracování řeči Bingu byl vyvyřazen z 15. října 2019.

Pokud vaše aplikace, nástroje nebo produkty používají Zpracování řeči Bingu rozhraní API nebo Custom Speech, vytvořili jsme příručky, které vám pomůžou s migrací na služby pro rozpoznávání řeči.

- [Migrace z Zpracování řeči Bingu na služby pro rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
- [Migrace z Custom Speech na služby pro rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Referenční dokumenty

- [Speech SDK](https://aka.ms/csspeech)
- [Sada Speech Devices SDK](speech-devices-sdk.md)
- [REST API: Převod řeči na text](rest-speech-to-text.md)
- [REST API: převod textu na řeč](rest-text-to-speech.md)
- [REST API: přepis a přizpůsobení Batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další kroky

- [Získat klíč předplatného služby Speech Services zdarma](get-started.md)
- [Získat sadu Speech SDK](speech-sdk.md)
