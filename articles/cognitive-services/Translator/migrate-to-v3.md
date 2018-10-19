---
title: Migrace na V3 – Translator Text API
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak migrovat z V2 na V3 Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: 74fcfa0e1474d7d2a25d0bf9d0476fb1bb870334
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429503"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Translator Text API V2 na V3 migrace

Tým Microsoft Translatoru vydala rozhraní Translator Text API verze 3 (V3). Tato verze obsahuje nové funkce, nepoužívané metody a nový formát pro odesílání a přijímání dat ze služby Microsoft Translator. Tento dokument obsahuje informace pro změnu aplikace, aby používaly V3. V2 se přestanou používat 30. dubna 2018 a bude ukončena 30. dubna 2019.

Konci tohoto dokumentu obsahuje užitečné odkazy pro vás další informace.

## <a name="summary-of-features"></a>Přehled funkcí

* Žádné trasování – v V3 bez trasování se vztahuje na všechny cenové úrovně na webu Azure Portal. Tato funkce znamená, že žádný text, odešle do rozhraní API V3 se uloží společností Microsoft.
* JSON – XML je nahrazena JSON. Všechna data ve službě odeslané a přijaté ze služby je ve formátu JSON.
* Více jazyků cíl v jednom požadavku – The přeložit metoda přijímá několik jazyků 'do' pro překlad v jedné žádosti. Jeden požadavek může být třeba angličtina z a do, němčina, španělština a japonština nebo jiná skupina jazyků.
* Dvojjazyčný slovník – metoda dvojjazyčného slovníku se přidala rozhraní API. Tato metoda zahrnuje "vyhledávání" a "Příklady".
* Transkripce – metoda transliterate byl přidán do rozhraní API. Tato metoda převede slov a vět jeden skript (například Arabské) do jiného skriptu (například Latinka).
* Jazyky – novou metodu "v jazycích" poskytuje informace jazyk, ve formátu JSON pro použití s "přeložit", "slovník" a "transkripce" metody.
* Nové přeložit – nové funkce byly přidány k metodě "přeložit" pro podporu některých funkcí, které byly v rozhraní API V2 jako samostatné metody. Příkladem je TranslateArray.
* Metoda mluvit – funkce pro převod textu na řeč je již nejsou podporovány v rozhraní Microsoft Translator API. Převod textu na řeč funkce jsou dostupné v [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

Následující seznam metod V2 a V3 identifikuje rozhraní API, která poskytne funkci, která byla součástí V2 a V3 metody.

| V2 Metoda API   | V3 Kompatibilitu s rozhraními API |
|:----------- |:-------------|
| Překlad     | [Překlad](reference/v3-0-translate.md)          |
| TranslateArray      | [Překlad](reference/v3-0-translate.md)        |
| GetLanguageNames      | [Jazyky](reference/v3-0-languages.md)         |
| GetLanguagesForTranslate     | [Jazyky](reference/v3-0-languages.md)       |
| GetLanguagesForSpeak      | [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| Řeči     | [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| Zjišťování     | [Zjištění](reference/v3-0-detect.md)         |
| DetectArray     | [Zjištění](reference/v3-0-detect.md)         |
| AddTranslation     | [Centrum Microsoft Translator API](https://hub.microsofttranslator.com/Help/Download/Microsoft%20Translator%20Hub%20API%20Guide.pdf)         |
| AddTranslationArray    | [Centrum Microsoft Translator API](https://hub.microsofttranslator.com/Help/Download/Microsoft%20Translator%20Hub%20API%20Guide.pdf)          |
| BreakSentences      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| GetTranslations      | Funkce se už nepodporuje.         |
| GetTranslationsArray      | Funkce se už nepodporuje.         |

## <a name="move-to-json-format"></a>Přesunout do formátu JSON

Microsoft Translator Text překlad V2 přijmout a vrátil data ve formátu XML. Ve verzi 3 všech dat odeslaných a přijatých pomocí rozhraní API je ve formátu JSON. XML se už přijata nebo vráceny ve verzi 3. 

Tato změna bude týkat několik aspektů aplikace napsané pro rozhraní API pro překlad textu V2. Jako příklad: rozhraní API jazyků vrátí informace o jazyka pro překlady textů, přepis a metody dvě slovníku. Můžete požádat o všechny informace o jazyk pro všechny metody v jednom volání nebo o ně požádat samostatně.

Metoda jazyky nevyžaduje ověření; Po kliknutí na následující odkaz zobrazí všechny informace o jazyku pro V3 ve formátu JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, slovník, přepis](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Ověřovací klíč

Pro V3 se nebudou přijímat ověřovací klíč, který používáte pro V2. Nemusíte si nové předplatné. Budete moci kombinovat V2 a V3 ve vašich aplikacích v průběhu migrace yearlong usnadnit vydání nové verze, zatímco stále migrujete z V2 XML na V3 JSON.

## <a name="pricing-model"></a>Cenový model

Microsoft Translator V3 se účtuje podle stejným způsobem, který se stanoví V2; na znak včetně mezer. Nové funkce ve verzi 3 provést nějaké změny v jaké znaky se počítají pro fakturaci.

| Metoda v3   | Znaky počítá fakturace |
|:----------- |:-------------|
| Jazyky     | Odeslané žádné znaky, none nepočítají, žádné poplatky.          |
| Překlad     | Počet je založen na tom, kolik znaků se odešlou pro překlad a kolik jazycích jsou znaky přeloženy do. odeslání 50 znaků a 5 jazyky požadovaný bude 50 × 5.           |
| Transliterace     | Počet odeslaných k transkripci znaků, které se počítají.         |
| Příklad & vyhledávací slovník     | Se počítají počet znaků, odešle ke slovníku lookup a examples.         |
| BreakSentence     | Bez poplatků.       |
| Zjišťování     | Bez poplatků.      |

## <a name="v3-end-points"></a>V3 koncové body

Globální

* API.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>Metody překlady text v3 API

[Jazyky](reference/v3-0-languages.md)

[Překlad](reference/v3-0-translate.md)

[Transkripce](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Zjištění](reference/v3-0-detect.md)

[Slovník/vyhledávání](reference/v3-0-dictionary-lookup.md)

[Slovník/příklad](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>Přizpůsobení

Ve výchozím nastavení používá Microsoft Translator V3 Neurální strojový překlad. V důsledku toho jej nelze použít v centru Microsoft Translator. Překladač rozbočovače podporuje pouze starší verze statistické strojového překladu. Přizpůsobení pro Neurální překladové je nyní dostupné s použitím překladač vlastní. [Další informace o přizpůsobení Neurální strojový překlad](customization.md)

Neurální překladové s V3 text API nepodporuje použití standardní kategorie (SMT, řeči, text, generalnn).


## <a name="links"></a>Odkazy

* [Zásady ochrany osobních údajů společnosti Microsoft](https://privacy.microsoft.com/privacystatement)
* [Právní informace týkající se Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Podmínky online služeb](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zobrazit dokumentaci verze 3.0](reference/v3-0-reference.md)
