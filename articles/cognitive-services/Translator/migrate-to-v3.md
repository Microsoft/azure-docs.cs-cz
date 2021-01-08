---
title: Migrace na V3 – Translator
titleSuffix: Azure Cognitive Services
description: Tento článek popisuje kroky, které vám pomůžou při migraci z verze V2 na v3 pro Azure Cognitive Services Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: baaf907d2fafe23805e6b49582ecc7592ee05e0b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028229"
---
# <a name="translator-v2-to-v3-migration"></a>Migrace překladatelů v2 na V3

> [!NOTE]
> Verze V2 byla zastaralá od 30. dubna 2018. Pokud chcete využívat nové funkce, které jsou k dispozici výhradně v v3, migrujte prosím své aplikace na v3. Verze v2 bude vyřazena 24. května 2021. 

Tým Microsoft Translator vydala verzi 3 (V3) překladatele. Tato verze zahrnuje nové funkce, zastaralé metody a nový formát pro odesílání a příjem dat ze služby Microsoft Translator. Tento dokument poskytuje informace pro změnu aplikace na použití v3. 

Konec tohoto dokumentu obsahuje užitečné odkazy, které vám pomůžou získat další informace.

## <a name="summary-of-features"></a>Souhrn funkcí

* Pro všechny cenové úrovně v Azure Portal se nepoužívá žádná No-Tracea na úrovni v3. Tato funkce znamená, že Microsoft nebude ukládat žádný text odeslaný do rozhraní V3 API.
* JSON – XML je nahrazeno kódem JSON. Všechna data odesílaná do služby a přijatá ze služby jsou ve formátu JSON.
* Několik cílových jazyků v jednom požadavku – metoda překladu přijímá více jazyků "do" pro překlad v jednom požadavku. Například jediná žádost může být "z" angličtiny a "do" němčiny, španělštiny a japonštiny nebo jakékoli jiné skupiny jazyků.
* Dvojjazyčný slovník – do rozhraní API se přidala metoda dvojjazyčného slovníku. Tato metoda zahrnuje ' Lookup ' a ' examples '.
* Přepis – do rozhraní API se přidala metoda přepisu. Tato metoda převede slova a věty v jednom skriptu (např. Arabština) do jiného skriptu (např. Latinka).
* Jazyky – nová metoda "jazyky" poskytuje jazykové informace ve formátu JSON pro použití s metodami "přeložit", "Dictionary" a "přepsanými".
* Novinka k překladu – k metodě ' přeložit ' byly přidány nové funkce, které podporují některé funkce, které byly v rozhraní v2 API, jako samostatné metody. Příkladem je TranslateArray.
* Metoda speaking – text na řeč funkce se už v Microsoft translatoru nepodporuje. Funkce převod textu na řeč je k dispozici ve [službě Microsoft Speech Service](../speech-service/text-to-speech.md).

Následující seznam metod v2 a V3 identifikuje metody v3 a rozhraní API, které poskytují funkce dodávané s v2.

| V2 – metoda rozhraní API   | V3 API – kompatibilita |
|:----------- |:-------------|
| `Translate`     | [Posunut](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Posunut](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Jazyky](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Jazyky](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Služba Microsoft Speech](../speech-service/language-support.md#text-to-speech)         |
| `Speak`     | [Služba Microsoft Speech](../speech-service/text-to-speech.md)          |
| `Detect`     | [Soustavy](reference/v3-0-detect.md)         |
| `DetectArray`     | [Soustavy](reference/v3-0-detect.md)         |
| `AddTranslation`     | Funkce už není podporovaná.       |
| `AddTranslationArray`    | Funkce už není podporovaná.          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Funkce už není podporovaná.         |
| `GetTranslationsArray`      | Funkce už není podporovaná.         |

## <a name="move-to-json-format"></a>Přesunout do formátu JSON

Microsoft Translator Translation v2 přijal a vrátil data ve formátu XML. Ve verzi V3 všechna data odesílaná a přijímaná pomocí rozhraní API jsou ve formátu JSON. Soubor XML již nebude přijat ani vrácen v v3.

Tato změna bude mít vliv na několik aspektů aplikace napsané pro rozhraní API pro překlad textu v2. Příklad: rozhraní API jazyků vrací jazykové informace pro překlad textu, pro převod na více jazyků a dvě Slovníkové metody. Můžete požadovat všechny informace o jazycích pro všechny metody v jednom volání nebo si je vyžádat jednotlivě.

Metoda languages nevyžaduje ověřování; Kliknutím na následující odkaz můžete zobrazit všechny informace o jazyce pro v3 ve formátu JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, slovník, předané](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Ověřovací klíč

Ověřovací klíč, který používáte pro v2, bude přijatý pro v3. Nebudete potřebovat získat nové předplatné. Během migrace yearlong budete moct ve svých aplikacích kombinovat v2 a v3, což usnadňuje vydávání nových verzí, i když stále migrujete z v2-XML do V3-JSON.

## <a name="pricing-model"></a>Cenový model

Microsoft Translator V3 se účtuje stejným způsobem jako ceny v2; za znak, včetně mezer. Nové funkce v v3 provedou určité změny v tom, jaké znaky se budou počítat pro účely fakturace.

| V3 – metoda   | Znaky zjištěné pro fakturaci |
|:----------- |:-------------|
| `Languages`     | Nebyly odeslány žádné znaky, žádné se nepočítají, bez poplatků.          |
| `Translate`     | Počet je založen na tom, kolik znaků je odesláno pro překlad, a kolik jazyků jsou přeloženy znaky. 50 odeslaných znaků a 5 požadovaných jazyků bude 50x5.           |
| `Transliterate`     | Počítá se počet znaků odeslaných pro účely překládání.         |
| `Dictionary lookup & example`     | Počítá se počet znaků odeslaných pro vyhledávání slovníku a příklady.         |
| `BreakSentence`     | Bez poplatků.       |
| `Detect`     | Bez poplatků.      |

## <a name="v3-end-points"></a>V3 – koncové body

Globální

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>V3 – metody překladu textu rozhraní API

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
> Centrum Microsoft Translator bude vyřazení z 17. května 2019. [Zobrazení důležitých informací a dat migrace](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator V3 ve výchozím nastavení používá neuronové strojový překlad. V takovém případě se nedá použít s centrem Microsoft Translator. Centrum překladatelů podporuje jenom starší statistické překlady počítačů. Přizpůsobení pro překlad neuronové je teď dostupné pomocí vlastního překladatele. [Další informace o přizpůsobení neuronové strojového překladu](custom-translator/overview.md)

Neuronové překladu s rozhraním textového API V3 nepodporuje použití standardních kategorií (SMT, Speech, tech, generalnn).

| Verze | Koncový bod | Dodržování předpisů pro procesory GDPR | Použít centrum překladatelů | Použít vlastní překladatele (Preview) |
| :------ | :------- | :------------------------ | :----------------- | :------------------------------ |
|Překladatel verze 2|    api.microsofttranslator.com|    Ne    |Ano    |Ne|
|Překladatel verze 3|    api.cognitive.microsofttranslator.com|    Ano|    Ne|    Ano|

**Překladatel verze 3**
* Je všeobecně dostupná a plně podporovaná.
* Je GDPR kompatibilní jako procesor a splňuje všechna ISO 20001 a 20018 a také požadavky na certifikaci SOC 3. 
* Umožňuje vyvolat systémy překladu neuronové sítě, které jste přizpůsobili pomocí vlastního překladatele (Preview), nové funkce NMT pro přizpůsobení překladatelů. 
* Neposkytuje přístup k vlastním systémům překladu vytvořeným pomocí centra Microsoft Translator.

Pokud používáte koncový bod api.cognitive.microsofttranslator.com, používáte verzi 3 tohoto překladatele.

**Překladatel verze 2**
* Nesplňuje požadavky na certifikaci ISO 20 001, 20018 a SOC 3. 
* Neumožňuje vyvolat systémy překladu neuronové sítě, které jste přizpůsobili pomocí funkce pro přizpůsobení překladatele.
* Poskytuje přístup k vlastním systémům překladu vytvořeným pomocí centra Microsoft Translator.
* Používáte verzi 2 překladatele, pokud používáte koncový bod api.microsofttranslator.com.

Žádná verze převaděče nevytvoří záznam vašich překladů. Vaše překlady se nikdy nesdílí s kýmkoli. Další informace o webové stránce [překladatele bez trasování](https://www.aka.ms/NoTrace)

## <a name="links"></a>Odkazy

* [Zásady ochrany osobních údajů společnosti Microsoft](https://privacy.microsoft.com/privacystatement)
* [Microsoft Azure právní informace](https://azure.microsoft.com/support/legal)
* [Podmínky pro služby online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Dokumentace k zobrazení V 3.0](reference/v3-0-reference.md)
