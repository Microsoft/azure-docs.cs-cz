---
title: Služba Speech to text-Speech
titleSuffix: Azure Cognitive Services
description: Funkce převodu řeči na text umožňuje přepis zvukových streamů v reálném čase na text. Tyto textové vstupy můžou aplikace, nástroje a zařízení využívat, zobrazovat a provádět s nimi akce. Tato služba funguje bez problémů s převodem textu na řeč (rozpoznávání řeči) a funkcemi překladu řeči.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 4181491053dd1f8d66afcd5f1df7cb96313011da
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650611"
---
# <a name="what-is-speech-to-text"></a>Co je převod řeči na text?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Převod řeči na text ze služby Speech, označovaný také jako rozpoznávání řeči, umožňuje přepis zvukových streamů v reálném čase na text. Vaše aplikace, nástroje nebo zařízení můžou používat, zobrazovat a provádět na tomto textu akce jako vstup příkazu. Tato služba využívá stejnou technologii rozpoznávání, kterou Microsoft používá pro Cortana a produkty Office. Bez problémů funguje s nabídkami služeb pro <a href="./speech-translation.md" target="_blank">převod a převod <span class="docon docon-navigate-external x-hidden-focus"></span> </a> <a href="./text-to-speech.md" target="_blank">textu na <span class="docon docon-navigate-external x-hidden-focus"></span> řeč</a> . Úplný seznam dostupných jazyků pro převod řeči na text najdete v tématu [podporované jazyky](language-support.md#speech-to-text).

Služba převod řeči na text standardně používá univerzální jazykový model. Tento model byl vyškolený pomocí dat vlastněných společností Microsoft a nasazený v cloudu. Je ideální pro scénáře konverzace a diktování. Při použití převodu řeči na text pro rozpoznávání a přepis v jedinečném prostředí můžete vytvořit a naučit vlastní modely akustického, jazyka a výslovnosti. Přizpůsobení je užitečné pro řešení hlučného hluku nebo slovníku specifického pro konkrétní odvětví.

Pomocí dalšího referenčního textu jako vstupu služba Speech-to-text Service také umožňuje [posouzení výslovnosti](rest-speech-to-text.md#pronunciation-assessment-parameters) , aby vyhodnotila výslovnost řeči a dává mluvčí názory na přesnost a Fluency mluveného zvuku. S hodnocením výslovnosti mohou jazyky naučit postupovat, získávat okamžitou zpětnou vazbu a zlepšovat jejich výslovnost, aby mohli mluvit a prezentovat s jistotou. Pedagogé můžou využít schopnost vyhodnotit výslovnost více mluvčích v reálném čase. Tato funkce aktuálně podporuje americkou angličtinu a koreluje s posouzením řeči, které provádějí odborníci.

> [!NOTE]
> Zpracování řeči Bingu byl vyvyřazen z 15. října 2019. Pokud vaše aplikace, nástroje nebo produkty používají rozhraní API Zpracování řeči Bingu, vytvořili jsme příručky, které vám pomůžou s migrací na službu Speech.
> - [Migrace z Zpracování řeči Bingu do služby pro rozpoznávání řeči](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>Začínáme s převodem řeči na text

Služba převod řeči na text je k dispozici prostřednictvím [sady Speech SDK](speech-sdk.md). V různých jazycích a platformách je k dispozici několik běžných scénářů jako rychlé starty:

 - [Rychlý Start: rozpoznávání řeči pomocí vstupu mikrofonu](quickstarts/speech-to-text-from-microphone.md)
 - [Rychlý Start: rozpoznávání řeči ze souboru](quickstarts/speech-to-text-from-file.md)
 - [Rychlý Start: rozpoznávání řeči uložených v úložišti objektů BLOB](quickstarts/from-blob.md)

Pokud dáváte přednost používání služby REST (Speech-to-text), přečtěte si téma [rozhraní REST API](rest-speech-to-text.md).

 - [Rychlý Start: posouzení výslovnosti pomocí referenčního vstupu](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="tutorials-and-sample-code"></a>Kurzy a ukázkový kód

Až budete mít možnost používat službu rozpoznávání řeči, vyzkoušejte náš kurz, který vás seznámí s postupem rozpoznávání řeči pomocí sady Speech SDK a LUIS.

- [Kurz: rozpoznávání záměrů z řeči pomocí sady Speech SDK a LUIS pomocí jazyka C #](how-to-recognize-intents-from-speech-csharp.md)

Vzorový kód pro sadu Speech SDK je k dispozici na GitHubu. Tyto ukázky se týkají běžných scénářů, jako je čtení zvuku ze souboru nebo datového proudu, průběžné a jednorázové rozpoznávání a práce s vlastními modely.

- [Ukázky řeči na text (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Ukázky pro dávková přepisy (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Ukázky hodnocení výslovnosti (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Přizpůsobení

Kromě standardního modelu služby Speech můžete vytvořit vlastní modely. Přizpůsobení pomáhá překonat překážky rozpoznávání řeči, jako je například mluvený styl, slovník a šum na pozadí, viz [Custom Speech](how-to-custom-speech.md). Možnosti vlastního nastavení se liší podle jazyka nebo národního prostředí, viz [podporované jazyky](supported-languages.md) pro ověření podpory.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Další kroky

- [Získejte zdarma klíč předplatného služby Speech](get-started.md)
- [Získat sadu Speech SDK](speech-sdk.md)
