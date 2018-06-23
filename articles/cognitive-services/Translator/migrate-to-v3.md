---
title: Překladač Microsoft Text API – migrace do V3 | Microsoft Docs
description: Zjistěte, jak migrovat z V2 v3 rozhraní API Text překladač.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: translator
ms.topic: article
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: 16fec351af5b5b3875657ee244c18f305311d965
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343565"
---
# <a name="microsoft-translator-text-api-v2-to-v3-migration"></a>Překladač Microsoft Text API V2 a V3 migrace

Tým Microsoft Translator vydala verze 3 (V3) rozhraní API pro překlad textu. Tato verze obsahuje nové funkce, zastaralé metody a nový formát pro odesílání a příjmu dat ze služby Microsoft překladač. Tento dokument obsahuje informace pro změnu aplikacím používat V3. V2 přestanou na 30 duben 2018 a na 30. dubna 2019 budou zastaveny.

Konci tohoto dokumentu obsahuje užitečné odkazy pro vás další informace.

## <a name="summary-of-features"></a>Souhrn funkcí

* Žádné trasování - V3 ne-trasování se vztahuje na všechny cenové úrovně na portálu Azure. To znamená, že žádný text odeslané na rozhraní API V3 uloží společností Microsoft.
* JSON – XML je nahrazena JSON. Všechna data odešle do služby a přijímají se ze služby je ve formátu JSON.
* Více jazyků cíl v jedné žádosti - The převede metoda přijímá několik jazyků 'to' pro překlad v jedné žádosti. Například jedné žádosti může být angličtina z a do, němčina, španělština a japonština nebo některé jiné skupiny jazyků.
* Dvojjazyčné slovník – metoda dvojjazyčné slovník byl přidán do rozhraní API. Tato metoda obsahuje "vyhledávání" a "Příklady".
* Transliterate – metoda transliterate byl přidán do rozhraní API. Tato metoda převede slova a věty v jednom skriptu (např. Arabic) do jiné skriptu (např. Latina).
* Jazyky – nové metody "jazycích" přináší jazykové informace ve formátu JSON pro použití s 'Převede', 'slovník' a 'transliterate' metody.
* Nový přeložit – nové funkce přidané 'Převede, metoda pro podporu některé funkce, které byly v rozhraní API V2 jako samostatné metody. Příkladem je TranslateArray.
* Metoda řeči – funkce pro převod textu na řeč již není podporována v rozhraní API služby Microsoft překladač. Převod textu na řeč funkce je k dispozici v služby Microsoft Azure kognitivní rozhraní API pro rozpoznávání řeči Bing.

Následující seznam metod V2 a V3 identifikuje rozhraní API, které poskytuje funkce, které byly dodány s V2 a V3 metody.

| V2 Metoda API   | V3 Kompatibilita rozhraní API |
|:----------- |:-------------|
| Převede     | Převede          |
| TranslateArray      | Převede          |
| GetLanguageNames      | Jazyky          |
| GetLanguagesForTranslate     | Jazyky        |
| GetLanguagesForSpeak      | Rozpoznávání řeči kognitivní služeb rozhraní API         |
| Řeči     | Rozpoznávání řeči kognitivní služeb rozhraní API          |
| Zjišťování     | Zjišťování         |
| DetectArray     | Zjišťování         |
| AddTranslation     | Rozhraní API Microsoft překladač ROZBOČOVAČE         |
| AddTranslationArray    | Rozhraní API Microsoft překladač ROZBOČOVAČE          |
| BreakSentences      | BreakSentence         |
| GetTranslations      | Funkce již není podporována.         |
| GetTranslationsArray      | Funkce již není podporována.         |

## <a name="move-to-json-format"></a>Přesunout do formátu JSON

Microsoft překladač Text překlad V2 je přijatá a vrátil data ve formátu XML. V V3 všechna data odeslané a přijaté pomocí rozhraní API je ve formátu JSON. XML už budou přijímat nebo vrátí V3. 

Tato změna bude mít vliv na několik aspektů aplikace napsané pro rozhraní API pro překlad Text V2. Jako příklad: rozhraní API pro jazyky vrátí jazykové informace pro překlad textu, přepis a metody dvě slovníku. Můžete požádat všechny informace o jazyk pro všechny metody v jednom volání nebo o ně požádat samostatně.

Metoda jazyky nevyžaduje ověření; Kliknutím na následující odkaz zobrazí všechny informace jazyk V3 ve formátu JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, slovník, přepis](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Ověřovací klíč

Ověřovací klíč, který používáte pro V2 se bude akceptovat pro V3. Nebudete muset získat nové předplatné. Bude moct kombinovat V2 a V3 ve svých aplikacích během období migrace yearlong usnadnit vydání nové verze, zatímco stále migrujete z V2 XML V3 JSON.

## <a name="pricing-model"></a>Cenový model

Překladač Microsoft verze 3 je cenově stejným způsobem, který byl za cenu V2; za znaků včetně mezer. Nové funkce v V3 provedeme některé změny v jaké znaky počítají pro fakturaci.

| Metoda v3   | Znaky počítají pro fakturaci |
|:----------- |:-------------|
| Jazyky     | Žádné znaky odeslána, none počítají, bez poplatků.          |
| Převede     | Počet je založena na tom, kolik znaků se odešlou pro překlad a kolik jazyky znaky jsou převedeny do. odeslání 50 znaků a 5 jazyky požadovaný bude 50 × 5.           |
| Transliterate     | Počet znaků odeslané pro přepis, se počítají.         |
| Slovník vyhledávání & příklad     | Počet znaků odeslána slovník vyhledávání a příklady, se počítají.         |
| BreakSentence     | Bez poplatků.       |
| Zjišťování     | Bez poplatků.      |

## <a name="v3-end-points"></a>V3 koncové body

Globální

* API.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>Rozhraní API v3 text překlady metody

[Jazyky](reference/v3-0-languages.md)

[Převede](reference/v3-0-translate.md)

[Transliterate](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Zjištění](reference/v3-0-detect.md)

[Slovník nebo vyhledávání](reference/v3-0-dictionary-lookup.md)

[Slovník/příklad](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>Přizpůsobení

Ve výchozím nastavení používá V3 překladač Microsoft neural strojového překladu. Jako takový ho nelze použít s překladač centra společnosti Microsoft, který podporuje pouze starší verze statistické strojového překladu. Vlastní nastavení pro překlad neuronové je k dispozici pomocí vlastní překladač. [Další informace o přizpůsobení neuronové strojový překlad](customization.md)

Neuronové překlad textem V3 rozhraní API nepodporuje použití standardní kategorií (smt, rozpoznávání řeči, text, generalnn).


## <a name="links"></a>Odkazy

* [Zásady ochrany osobních údajů společnosti Microsoft](https://privacy.microsoft.com/privacystatement)
* [Právní informace Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Podmínky pro online služby](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zobrazení V3.0 dokumentace](reference/v3-0-reference.md)
