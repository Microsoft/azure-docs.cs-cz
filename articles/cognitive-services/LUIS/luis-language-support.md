---
title: Jazyková podpora - LUIS
titleSuffix: Azure Cognitive Services
description: Služba LUIS má v rámci služby řadu funkcí. Ne všechny funkce jsou ve stejné jazykové paritě. Ujistěte se, že funkce, které vás zajímají, jsou podporovány v jazykové kultuře, na kterou cílíte. Aplikace LUIS je specifické pro jazykovou verzi a nelze ji změnit, jakmile je nastavena.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 4b6d954d06f09bef5240bddc4860ddbc83513d69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220850"
---
# <a name="language-and-region-support-for-luis"></a>Jazyková a regionální podpora služby LUIS

Služba LUIS má v rámci služby řadu funkcí. Ne všechny funkce jsou ve stejné jazykové paritě. Ujistěte se, že funkce, které vás zajímají, jsou podporovány v jazykové kultuře, na kterou cílíte. Aplikace LUIS je specifické pro jazykovou verzi a nelze ji změnit, jakmile je nastavena.

## <a name="multi-language-luis-apps"></a>Aplikace LUIS ve více jazycích

Pokud potřebujete klientskou aplikaci LUIS s více jazyky, například chatbota, máte několik možností. Pokud luis podporuje všechny jazyky, můžete vyvinout aplikaci LUIS pro každý jazyk. Každá aplikace LUIS má jedinečné ID aplikace a protokol koncového bodu. Pokud potřebujete poskytnout jazykové znalosti pro jazyk LUIS nepodporuje, můžete použít [Rozhraní API Microsoft Translator](../Translator/translator-info-overview.md) překládat utterance do podporovaného jazyka, odešlete utterance do koncového bodu LUIS a obdrží výsledné skóre.

## <a name="languages-supported"></a>Podporované jazyky

Služba LUIS rozumí projevy v následujících jazycích:

| Jazyk |Národní prostředí  |  Předem vyestavěná doména | Předem sestavená entita | Doporučení pro seznam frází | **[Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentiment a<br>Klíčová slova)|
|--|--|:--:|:--:|:--:|:--:|
| Americká angličtina |`en-US` | ✔ | ✔  |✔|✔|
| Arabština (náhled - moderní standardní arabština) |`ar-AR`|-|-|-|-|
| *[Čínština](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Nizozemština |`nl-NL` |✔|  -   |-|✔|
| francouzština (Francie) |`fr-FR` |✔| ✔ |✔ |✔|
| Francouzština (Kanada) |`fr-CA` |-|   -   |-|✔|
| Němčina |`de-DE` |✔| ✔ |✔ |✔|
| Hindština | `hi-IN`|-|-|-|-|
| Italština |`it-IT` |✔| ✔ |✔|✔|
| *[Japonština](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Pouze klíčová fráze|
| Korejština |`ko-KR` |✔|   -   |-|Pouze klíčová fráze|
| Portugalština (Brazílie) |`pt-BR` |✔| ✔ |✔ |ne všechny subkultury|
| Španělština (Španělsko) |`es-ES` |✔| ✔ |✔|✔|
| Španělština (Mexiko)|`es-MX` |-|  -   |✔|✔|
| Turečtina | `tr-TR` |✔|-|-|Pouze sentiment|

Jazyková podpora se liší u [předem vytvořených entit](luis-reference-prebuilt-entities.md) a [předem vytvořených domén](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>*Japonské poznámky k podpoře

 - Vzhledem k tomu, že služba LUIS neposkytuje syntaktickou analýzu a nepochopí rozdíl mezi Keigo a neformální japonštinou, je třeba začlenit různé úrovně formalit jako příklady školení pro vaše aplikace.
     - Není to totéž jako 中中中.
     - Není to stejné jako 中.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Jazyky podporované rozhraním SPEECH API
Jazyky režimu diktování řeči najdete v tématu [Jazyky](../speech-service/speech-to-text.md) podporované řeči.

### <a name="bing-spell-check-supported-languages"></a>Podporované jazyky kontroly pravopisu Bingu
Seznam podporovaných jazyků a stavu v tématu [Podporované jazyky kontroly](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) pravopisu Bingu.

## <a name="rare-or-foreign-words-in-an-application"></a>Vzácná nebo cizí slova v aplikaci
V `en-us` jazykové verzi se služba LUIS učí rozlišovat většinu anglických slov, včetně slangu. V `zh-cn` jazykové verzi luis učí rozlišovat většinu čínských znaků. Pokud v aplikaci `en-us` používáte `zh-cn`vzácné slovo nebo znak a zjistíte, že služba LUIS zřejmě nedokáže toto slovo nebo znak rozlišit, můžete toto slovo nebo znak přidat do [funkce seznamu frází](luis-how-to-add-features.md). Například slova mimo jazykovou verzi aplikace – to znamená cizí slova – by měla být přidána do funkce seznamu frází.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hybridní jazyky
Hybridní jazyky kombinují slova ze dvou kultur, jako je angličtina a čínština. Tyto jazyky nejsou podporovány v LUIS, protože aplikace je založená na jedné jazykové verzi.

## <a name="tokenization"></a>Tokenizace
Chcete-li provést strojové učení, LUIS rozdělí utterance na [tokeny](luis-glossary.md#token) založené na jazykové verzi.

|Jazyk|  každý prostor nebo speciální znak | úroveň znaků|složená slova|[tokenizovaná entita vrácena](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Arabština|||||
|Chinese||✔||✔|
|Nizozemština|||✔|✔|
|Angličtina (en-us)|✔ ||||
|Francouzština (fr-FR)|✔||||
|Francouzština (fr-CA)|✔||||
|Němčina|||✔|✔|
| Hindština |✔|-|-|-|-|
|Italština|✔||||
|Japonština||||✔|
|Korejština||✔||✔|
|Portugalština (Brazílie)|✔||||
|Španělština (es-ES)|✔||||
|Španělština (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Vlastní verze tokenizeru

Následující jazykové verze mají vlastní verze tokenizer:

|Culture|Version|Účel|
|--|--|--|
|Němčina<br>`de-de`|1.0.0|Tokenizes slova rozdělením pomocí tokenizer založené na strojovém učení, který se pokusí rozdělit složená slova do jejich jednotlivých součástí.<br>Pokud uživatel zadá `Ich fahre einen krankenwagen` jako utterance, je `Ich fahre einen kranken wagen`otočena . Umožňuje označovat `kranken` a `wagen` nezávisle jako různé subjekty.|
|Němčina<br>`de-de`|1.0.2|Tokenizes slova rozdělením je na mezery.<br> Pokud uživatel zadá `Ich fahre einen krankenwagen` jako utterance, zůstane jeden token. Proto `krankenwagen` je označen jako jedna entita. |

### <a name="migrating-between-tokenizer-versions"></a>Migrace mezi verzemi tokenizeru
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

Tokenizace probíhá na úrovni aplikace. Neexistuje žádná podpora tokenizace na úrovni verze.

[Importujte soubor jako novou aplikaci](luis-how-to-start-new-app.md)místo verze. Tato akce znamená, že nová aplikace má jiné ID aplikace, ale používá verzi tokenizer zadanou v souboru.
