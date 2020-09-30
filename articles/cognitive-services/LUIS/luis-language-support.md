---
title: Jazyková podpora – LUIS
titleSuffix: Azure Cognitive Services
description: Služba LUIS obsahuje celou řadu funkcí. Ne všechny funkce jsou dostupné ve stejných jazycích. Ujistěte se, že funkce, o které máte zájem, jsou podporované v jazykové verzi, na kterou cílíte. Aplikace LUIS je specifická pro jazykovou verzi a po jejím nastavení ji nejde změnit.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: e377f910e1c1ddda864ea312b40fe3608c166376
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541386"
---
# <a name="language-and-region-support-for-luis"></a>Podpora jazyků a oblastí pro LUIS

Služba LUIS obsahuje celou řadu funkcí. Ne všechny funkce jsou dostupné ve stejných jazycích. Ujistěte se, že funkce, o které máte zájem, jsou podporované v jazykové verzi, na kterou cílíte. Aplikace LUIS je specifická pro jazykovou verzi a po jejím nastavení ji nejde změnit.

## <a name="multi-language-luis-apps"></a>Aplikace LUIS pro více jazyků

Pokud potřebujete klientskou aplikaci LUIS s více jazyky, jako je chatovací robot, máte několik možností. Pokud LUIS podporuje všechny jazyky, vyvíjíte aplikaci LUIS pro každý jazyk. Každá aplikace LUIS má jedinečné ID aplikace a protokol koncového bodu. Pokud potřebujete zadat jazykovou porozumění pro jazyk LUIS, můžete použít [službu Translator](../Translator/translator-info-overview.md) k překladu utterance do podporovaného jazyka, odeslat utterance do koncového bodu Luis a získat výsledné výsledky.

## <a name="languages-supported"></a>Podporované jazyky

LUIS rozumí projevy v následujících jazycích:

| Jazyk |Národní prostředí  |  Předem sestavená doména | Předem vytvořená entita | Doporučení pro seznam frází | **[Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Mínění a<br>Klíčov|
|--|--|:--:|:--:|:--:|:--:|
| Angličtina (Spojené státy) |`en-US` | ✔ | ✔  |✔|✔|
| Arabština (Preview – moderní standardní arabština) |`ar-AR`|-|-|-|-|
| *[Čínština](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Nizozemština |`nl-NL` |✔|-|-|✔|
| francouzština (Francie) |`fr-FR` |✔| ✔ |✔ |✔|
| Francouzština (Kanada) |`fr-CA` |-|-|-|✔|
| Němčina |`de-DE` |✔| ✔ |✔ |✔|
| Gudžarátština | `gu-IN`|-|-|-|-|
| Hindština | `hi-IN`|-|✔|-|-|
| Italština |`it-IT` |✔| ✔ |✔|✔|
| *[Japonština](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Jenom klíčová fráze|
| Korejština |`ko-KR` |✔|-|-|Jenom klíčová fráze|
| Maráthština | `mr-IN`|-|-|-|-|
| Portugalština (Brazílie) |`pt-BR` |✔| ✔ |✔ |Ne všechny dílčí kultury|
| Španělština (Španělsko) |`es-ES` |✔| ✔ |✔|✔|
| Španělština (Mexiko)|`es-MX` |-|-|✔|✔|
| Tamilština | `ta-IN`|-|-|-|-|
| Telugština | `te-IN`|-|-|-|-|
| Turečtina | `tr-TR` |✔|✔|-|Jenom mínění|




Podpora jazyků se liší u [předem sestavených entit](luis-reference-prebuilt-entities.md) a [předem sestavených domén](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>* Japonské poznámky k podpoře

 - Vzhledem k tomu, že LUIS neposkytuje syntaktickou analýzu a nebude porozumět rozdílům mezi Keigo a neformálními Japonštinami, musíte začlenit různé úrovně formalit jako příklady výukových programů pro vaše aplikace.
     - でございます není stejný jako です.
     - です není stejný jako だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Jazyky podporované rozhraním API pro rozpoznávání řeči
Jazyky režimů diktování řeči najdete v tématu [podporované jazyky](../speech-service/speech-to-text.md) Speech.

### <a name="bing-spell-check-supported-languages"></a>Kontrola pravopisu Bingu podporované jazyky
Seznam podporovaných jazyků a stavu naleznete v tématu Kontrola pravopisu Bingu [podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) .

## <a name="rare-or-foreign-words-in-an-application"></a>Vzácná nebo cizí slova v aplikaci
V `en-us` jazykové verzi se Luis učí, jak rozlišovat anglická slova, včetně slangem. V `zh-cn` jazykové verzi se Luis učí, aby lišily většinu čínských znaků. Pokud v nástroji použijete zřídka používané slovo `en-us` nebo znak `zh-cn` a vidíte, že Luis se zdá, že toto slovo nebo znak není možné odlišit, můžete toto slovo nebo znak přidat do [funkce seznamu frází](luis-how-to-add-features.md). Například slova mimo jazykovou verzi aplikace – to znamená, že cizí slova--by měla být přidána do funkce seznamu frází.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hybridní jazyky
Hybridní jazyky kombinují slova ze dvou kultur, jako je angličtina a čínština. Tyto jazyky nejsou v LUIS podporované, protože aplikace je založená na jedné jazykové verzi.

## <a name="tokenization"></a>Tokenizace
K provedení strojového učení LUIS rozdělí utterance na [tokeny](luis-glossary.md#token) založené na jazykové verzi.

|Jazyk|  každé místo nebo speciální znak | úroveň znaku|složená slova
|--|:--:|:--:|:--:|
|Arabština|✔|||
|Čínština||✔||
|Nizozemština|✔||✔|
|Angličtina (en-us)|✔ |||
|francouzština (fr-FR),|✔|||
|Francouzština (fr-CA)|✔|||
|Němčina|✔||✔|
|Gudžarátština|✔|||
|Hindština|✔|||
|Italština|✔|||
|Japonština|||✔
|Korejština||✔||
|Maráthština|✔|||
|Portugalština (Brazílie)|✔|||
|španělština (es-ES),|✔|||
|Španělština (ES-MX)|✔|||
|Tamilština|✔|||
|Telugština|✔|||
|Turečtina|✔|||


### <a name="custom-tokenizer-versions"></a>Vlastní verze provádějících tokenizaci

Následující jazykové verze mají vlastní verze provádějících tokenizaci:

|Kultura|Verze|Účel|
|--|--|--|
|Němčina<br>`de-de`|1.0.0|Tokenizes slova rozdělením pomocí provádějících tokenizaci založeného na strojovém učení, které se pokusí rozdělit složené slova do jejich jediné součásti.<br>Pokud uživatel zadá `Ich fahre einen krankenwagen` jako utterance, je zapnutý `Ich fahre einen kranken wagen` . Povolení označení `kranken` a `wagen` nezávislého označování jako různých entit.|
|Němčina<br>`de-de`|1.0.2|Tokenizes slova rozdělením na mezery.<br> Pokud uživatel zadá `Ich fahre einen krankenwagen` jako utterance, zůstane jeden token. Proto `krankenwagen` je označen jako jediná entita. |
|Nizozemština<br>`nl-nl`|1.0.0|Tokenizes slova rozdělením pomocí provádějících tokenizaci založeného na strojovém učení, které se pokusí rozdělit složené slova do jejich jediné součásti.<br>Pokud uživatel zadá `Ik ga naar de kleuterschool` jako utterance, je zapnutý `Ik ga naar de kleuter school` . Povolení označení `kleuter` a `school` nezávislého označování jako různých entit.|
|Nizozemština<br>`nl-nl`|1.0.1|Tokenizes slova rozdělením na mezery.<br> Pokud uživatel zadá `Ik ga naar de kleuterschool` jako utterance, zůstane jeden token. Proto `kleuterschool` je označen jako jediná entita. |


### <a name="migrating-between-tokenizer-versions"></a>Migrace mezi verzemi provádějících tokenizaci
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID.

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

K tokenizace dochází na úrovni aplikace. Neexistuje žádná podpora pro tokeny na úrovni verze.

[Importujte soubor jako novou aplikaci](luis-how-to-start-new-app.md)místo verze. Tato akce znamená, že nová aplikace má jiné ID aplikace, ale používá verzi provádějících tokenizaci zadanou v souboru.
