---
title: Řeč na text - Řečová služba
titleSuffix: Azure Cognitive Services
description: Funkce převodu řeči na text umožňuje přepis zvukových proudů do textu v reálném čase. Vaše aplikace, nástroje nebo zařízení mohou spotřebovávat, zobrazovat a přijímat akce na tomto textovém vstupu. Tato služba pracuje bez problémů s funkcemi převodu textu na řeč (syntéza řeči) a překladu řeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: dapine
ms.openlocfilehash: 2854f4e8d91164c8ae1f35761d6f605cae725245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052631"
---
# <a name="what-is-speech-to-text"></a>Co je převod řeči na text?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Převod řeči na text ze služby Řeč, označovaný také jako rozpoznávání řeči, umožňuje přepis zvukových proudů v reálném čase do textu. Vaše aplikace, nástroje nebo zařízení mohou využívat, zobrazovat a přijímat akce s tímto textem jako příkazovým vstupem. Tato služba je poháněna stejnou technologií rozpoznávání, kterou společnost Microsoft používá pro produkty Cortana a Office. Hladce pracuje s nabídkami <a href="./speech-translation.md" target="_blank">překladů <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a <a href="./text-to-speech.md" target="_blank">převodu textu na řeč. <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Úplný seznam dostupných jazyků převodu řeči na text naleznete v [části Podporované jazyky](language-support.md#speech-to-text).

Služba převodu řeči na text ve výchozím nastavení používá univerzální jazykový model. Tento model byl trénovaný pomocí dat vlastněných microsoftem a nasadí se v cloudu. Je optimální pro konverzační scénáře a scénáře diktování. Při použití funkce převodřeči na text pro rozpoznávání a přepis v jedinečném prostředí můžete vytvářet a trénovat vlastní modely akustické, jazykové a výslovnosti. Přizpůsobení je užitečné pro řešení okolního hluku nebo slovníku specifického pro dané odvětví.

> [!NOTE]
> 15. října 2019 byla vyřazena z provozu proslov Bing. Pokud vaše aplikace, nástroje nebo produkty používají proslovová ová lana Pro slov bing, vytvořili jsme průvodce, kteří vám pomohou migrovat do služby Řeč.
> - [Migrace z řeči Bingu do služby Řeč](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>Začínáme s převodem řeči na text

Služba převodu řeči na text je k dispozici prostřednictvím [sady Speech SDK](speech-sdk.md). Existuje několik běžných scénářů, které jsou k dispozici jako rychlé starty v různých jazycích a platformách:

 - [Úvodní příručka: Rozpoznávání řeči se vstupem mikrofonu](quickstarts/speech-to-text-from-microphone.md)
 - [Úvodní příručka: Rozpoznání řeči ze souboru](quickstarts/speech-to-text-from-file.md)
 - [Úvodní příručka: Rozpoznávání řeči uložené v úložišti objektů blob](quickstarts/from-blob.md)

Pokud dáváte přednost použití služby REST pro převod řeči na text, [přečtěte](rest-speech-to-text.md)si viz REST API .

## <a name="tutorials-and-sample-code"></a>Kurzy a ukázkový kód

Poté, co jste měli možnost používat službu řeči, zkuste náš kurz, který vás naučí, jak rozpoznat záměry z řeči pomocí sady Speech SDK a LUIS.

- [Kurz: Rozpoznat záměry z řeči s sadou Speech SDK a LUIS pomocí C #](how-to-recognize-intents-from-speech-csharp.md)

Ukázkový kód pro sadu Speech SDK je k dispozici na GitHubu. Tyto ukázky zahrnují běžné scénáře, jako je čtení zvuku ze souboru nebo datového proudu, nepřetržité rozpoznávání a rozpoznávání jedním výstřelem a práce s vlastními modely.

- [Ukázky převodu řeči na text (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Vzorky přepisu dávky (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Přizpůsobení

Kromě standardního modelu služby rozpoznávání řeči můžete vytvořit vlastní modely. Přizpůsobení pomáhá překonat překážky rozpoznávání řeči, jako je styl mluvení, slovní zásoba a šum na pozadí, viz [Vlastní řeč](how-to-custom-speech.md). Možnosti vlastního nastavení se liší podle jazyka nebo národního prostředí, podpora ověřuje [v podporovaných jazycích.](supported-languages.md)

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Další kroky

- [Získejte zdarma klíč předplatného služby Speech](get-started.md)
- [Získání sady SDK pro rozpoznávání řeči](speech-sdk.md)
