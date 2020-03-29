---
title: Migrace do Rozhraní V3 – překladač textového rozhraní API
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje postup, který vám pomůže migrovat z V2 do V3 překladače Překladač Azure Cognitive Services Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: eb43d549d3e0cd449c865d533fc8701c4c3912fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837311"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Migrace překladače textu v2 až V3

> [!NOTE]
> 30. dubna 2018 byl Zastaral. Migrujte své aplikace do V3, abyste mohli využívat nové funkce dostupné výhradně ve V3.
> 
> Centrum Microsoft Translator Hub bude vyřazeno v květnu 17, 2019. [Zobrazení důležitých informací o migraci a kalendářních dat](https://www.microsoft.com/translator/business/hub/).  

Tým Microsoft Translator vydal verzi 3 (V3) rozhraní Translator Text API. Tato verze obsahuje nové funkce, zastaralé metody a nový formát pro odesílání a přijímání dat ze služby Microsoft Translator Service. Tento dokument obsahuje informace pro změnu aplikací pro použití V3. 

Konec tohoto dokumentu obsahuje užitečné odkazy, kde se dozvíte více.

## <a name="summary-of-features"></a>Shrnutí funkcí

* Žádné trasování – ve V3 bez trasování platí pro všechny cenové úrovně na webu Azure Portal. Tato funkce znamená, že společnost Microsoft neuloží žádný text odeslaný do rozhraní API V3.
* JSON - XML je nahrazen JSON. Všechna data odeslaná do služby a přijatá ze služby jsou ve formátu JSON.
* Více cílových jazyků v jednom požadavku – Metoda Překladač přijímá více jazyků "do" pro překlad v jedné žádosti. Jeden požadavek může být například "z" angličtiny a němčiny, španělštiny a japonštiny nebo jakékoli jiné skupiny jazyků.
* Dvojjazyčný slovník - Do rozhraní API byla přidána metoda dvojjazyčného slovníku. Tato metoda zahrnuje "vyhledávání" a "příklady".
* Transliterate - Transliterate metoda byla přidána do API. Tato metoda převede slova a věty do jednoho skriptu (např. arabština) do jiného skriptu (např. latinsky).
* Jazyky – Nová metoda "jazyky" poskytuje jazykové informace ve formátu JSON pro použití s metodami "přeložit", "slovník" a "transliterate".
* Nové přeložit - nové funkce byly přidány do 'přeložit' metoda pro podporu některých funkcí, které byly v Rozhraní API V2 jako samostatné metody. Příkladem je TranslateArray.
* Metoda Speak – Funkce převodu textu na řeč již není v rozhraní MICROSOFT Translator API podporována. Funkce převodu textu na řeč je k dispozici ve [službě Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

Následující seznam metod V2 a V3 identifikuje metody V3 a api, které budou poskytovat funkce dodané s V2.

| Metoda rozhraní API V2   | Kompatibilita rozhraní API V3 |
|:----------- |:-------------|
| `Translate`     | [Překlad](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Překlad](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Jazyky](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Jazyky](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Zjistit](reference/v3-0-detect.md)         |
| `DetectArray`     | [Zjistit](reference/v3-0-detect.md)         |
| `AddTranslation`     | Funkce již není podporována.       |
| `AddTranslationArray`    | Funkce již není podporována.          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Funkce již není podporována.         |
| `GetTranslationsArray`      | Funkce již není podporována.         |

## <a name="move-to-json-format"></a>Přechod do formátu JSON

Překladač microsoft překladač překladvač v2 přijata a vrácena data ve formátu XML. Ve V3 jsou všechna data odeslaná a přijatá pomocí rozhraní API ve formátu JSON. Jazyk XML již nebude přijat nebo vrácen ve v3.

Tato změna ovlivní několik aspektů aplikace napsané pro rozhraní API překladu textu V2. Jako příklad: Rozhraní API jazyky vrací informace o jazyce pro překlad textu, přepis a dvě metody slovníku. Můžete si vyžádat všechny jazykové informace pro všechny metody v jednom volání nebo je požádat jednotlivě.

Metoda languages nevyžaduje ověření; kliknutím na následující odkaz můžete vidět všechny jazykové informace pro V3 v JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,slovník,přepis](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Ověřovací klíč

Ověřovací klíč, který používáte pro V2, bude přijat pro V3. Nebudete muset získat nové předplatné. Během období roční migrace budete moci ve svých aplikacích kombinovat v2 a v3, což vám usnadní vydávání nových verzí, zatímco stále migrujete z V2-XML na V3-JSON.

## <a name="pricing-model"></a>Cenový model

Microsoft Translator V3 je cena stejným způsobem V2 byla cena; na znak, včetně mezer. Nové funkce ve V3 provést některé změny v tom, co znaky se počítají pro fakturaci.

| Metoda V3   | Znaky spočítané pro fakturaci |
|:----------- |:-------------|
| `Languages`     | Žádné znaky odeslány, žádný počítal, bez poplatku.          |
| `Translate`     | Počet je založen na počtu znaků, které jsou odeslány k překladu a kolik jazyků jsou znaky přeloženy do. 50 odeslaných znaků a 5 požadovaných jazyků bude 50x5.           |
| `Transliterate`     | Počet znaků odeslaných k přepisu se počítají.         |
| `Dictionary lookup & example`     | Počet znaků odeslaných pro vyhledávání slovníku a příklady se počítají.         |
| `BreakSentence`     | Bez poplatku.       |
| `Detect`     | Bez poplatku.      |

## <a name="v3-end-points"></a>Koncové body V3

Globální

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Metody překladů textu rozhraní API V3

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Kompatibilita a přizpůsobení

> [!NOTE]
> 
> Centrum Microsoft Translator Hub bude vyřazeno v květnu 17, 2019. [Zobrazení důležitých informací o migraci a kalendářních dat](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator V3 používá neurální strojový překlad ve výchozím nastavení. Jako takový jej nelze použít s Microsoft Translator Hub. Centrum Překladač podporuje pouze starší statistické strojové překlady. Přizpůsobení pro neurální překlad je nyní k dispozici pomocí vlastního překladače. [Další informace o přizpůsobení neurálního strojového překladu](custom-translator/overview.md)

Neurální překlad s textovým rozhraním API V3 nepodporuje použití standardních kategorií (SMT, řeč, tech, generalnn).

| |Koncový bod|    Soulad s procesorem GDPR|  Použití centra Překladač| Použití vlastního překladače (náhled)|
|:-----|:-----|:-----|:-----|:-----|
|Překladač Text API verze 2| api.microsofttranslator.com|    Ne  |Ano    |Ne|
|Překladač Text API verze 3| api.cognitive.microsofttranslator.com|  Ano|    Ne| Ano|

**Překladač Text API verze 3**
* Je obecně k dispozici a plně podporována.
* Je GDPR kompatibilní s procesorem a splňuje všechny požadavky na certifikaci ISO 20001 a 20018 i SOC 3. 
* Umožňuje vyvolat systémy překladu neuronové sítě, které jste přizpůsobili pomocí vlastního překladače (Preview), nové funkce přizpůsobení Translator NMT. 
* Neposkytuje přístup k vlastním překladatelským systémům vytvořeným pomocí centra Microsoft Translator Hub.

Používáte verzi 3 překladače textového rozhraní API Pokud používáte koncový bod api.cognitive.microsofttranslator.com.

**Překladač Text API verze 2**
* Nesplňuje všechny požadavky na certifikaci ISO 20001,20018 a SOC 3. 
* Neumožňuje vyvolat systémy překladu neuronové sítě, které jste přizpůsobili pomocí funkce přizpůsobení překladače.
* Poskytuje přístup k vlastním překladatelským systémům vytvořeným pomocí centra Microsoft Translator Hub.
* Používáte verzi 2 překladače textového rozhraní API Pokud používáte koncový bod api.microsofttranslator.com.

Žádná verze překladače rozhraní API vytvoří záznam o překlady. Vaše překlady nejsou nikdy sdíleny s nikým. Další informace naleznete na webové stránce [Translator No-Trace.](https://www.aka.ms/NoTrace)

## <a name="links"></a>Odkazy

* [Zásady ochrany osobních údajů společnosti Microsoft](https://privacy.microsoft.com/privacystatement)
* [Právní informace Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Podmínky online služeb](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazit dokumentaci V3.0](reference/v3-0-reference.md)
