---
title: Jazyková podpora – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS má v rámci služby nejrůznější funkce. Ne všechny funkce jsou ve stejné jazykové paritě. Ujistěte se, že funkce, které vás zajímá, jsou podporované v jazykové verzi, kterou cílíte. Aplikace LUIS je specifická pro jazykovou verzi a po jejím nastavení ji nejde změnit.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: bd1e665114fff4d5b7b0b2dca267207bdeebab56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949548"
---
# <a name="language-and-region-support-for-luis"></a>Podpora jazyků a oblastí pro LUIS

LUIS má v rámci služby nejrůznější funkce. Ne všechny funkce jsou ve stejné jazykové paritě. Ujistěte se, že funkce, které vás zajímá, jsou podporované v jazykové verzi, kterou cílíte. Aplikace LUIS je specifická pro jazykovou verzi a po jejím nastavení ji nejde změnit.

## <a name="multi-language-luis-apps"></a>Aplikace LUIS pro více jazyků

Pokud potřebujete klientskou aplikaci LUIS s více jazyky, jako je chatovací robot, máte několik možností. Pokud LUIS podporuje všechny jazyky, vyvíjíte aplikaci LUIS pro každý jazyk. Každá aplikace LUIS má jedinečné ID aplikace a protokol koncového bodu. Pokud potřebujete zadat jazykovou porozumění pro jazyk LUIS, můžete použít [rozhraní Microsoft Translator API](../Translator/translator-info-overview.md) k překladu utterance do podporovaného jazyka, odeslání utteranceu do koncového bodu Luis a získání výsledných výsledků.

## <a name="languages-supported"></a>Podporované jazyky

LUIS rozumí projevy v následujících jazycích:

| Jazyk |Národní prostředí  |  Předem sestavená doména | Předem vytvořená entita | Doporučení pro seznam frází | [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) **<br>(Mínění a<br>Klíčov|
|--|--|:--:|:--:|:--:|:--:|
| Americká angličtina |`en-US` | ✔ | ✔  |✔|✔|
| @no__t – 0 –[čínština](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holandština |`nl-NL` |✔|  -   |-|✔|
| Francouzština (Francie) |`fr-FR` |✔| ✔ |✔ |✔|
| Francouzština (Kanada) |`fr-CA` |-|   -   |-|✔|
| Němčina |`de-DE` |✔| ✔ |✔ |✔|
| Hindština | `hi-IN`|-|-|-|-|
| Italština |`it-IT` |✔| ✔ |✔|✔|
| @no__t – 0[japonština](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Jenom klíčová fráze|
| Korejština |`ko-KR` |✔|   -   |-|Jenom klíčová fráze|
| Portugalština (Brazílie) |`pt-BR` |✔| ✔ |✔ |Ne všechny dílčí kultury|
| Španělština (Španělsko) |`es-ES` |✔| ✔ |✔|✔|
| Španělština (Mexiko)|`es-MX` |-|  -   |✔|✔|
| Turečtina | `tr-TR` |✔|-|-|Jenom mínění|


Podpora jazyků se liší u [předem sestavených entit](luis-reference-prebuilt-entities.md) a [předem sestavených domén](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>\* Poznámky k podpoře čínštiny

 - V kultuře `zh-cn` očekává LUIS znaková sada zjednodušené čínštiny namísto tradiční znakové sady.
 - Názvy záměrů, entit, funkcí a regulárních výrazů můžou být v čínském nebo římském znaku.
 - V referenčních informacích k [předdefinovaným doménám](luis-reference-prebuilt-domains.md) najdete informace o tom, které předem sestavené domény jsou podporovány v jazykové verzi `zh-cn`.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>\* Japonské poznámky k podpoře

 - Vzhledem k tomu, že LUIS neposkytuje syntaktickou analýzu a nebude porozumět rozdílům mezi Keigo a neformálními Japonštinami, musíte začlenit různé úrovně formalit jako příklady výukových programů pro vaše aplikace.
     - でございます není stejný jako です.
     - です není stejný jako だ.

### <a name="text-analytics-support-notes"></a>\* * Poznámky k podpoře pro analýzu textu
Analýza textu zahrnuje předem sestavenou entitu keyPhrase a analýzu mínění. Pro subjazykové verze jsou podporovány pouze portugalštiny: `pt-PT` a `pt-BR`. Všechny ostatní jazykové verze jsou podporovány na úrovni primární kultury. Přečtěte si další informace o Analýza textu [podporovaných jazycích](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages).

### <a name="speech-api-supported-languages"></a>Jazyky podporované rozhraním API pro rozpoznávání řeči
Jazyky režimů diktování řeči najdete v tématu [podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) Speech.

### <a name="bing-spell-check-supported-languages"></a>Kontrola pravopisu Bingu podporované jazyky
Seznam podporovaných jazyků a stavu naleznete v tématu Kontrola pravopisu Bingu [podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) .

## <a name="rare-or-foreign-words-in-an-application"></a>Vzácná nebo cizí slova v aplikaci
V kultuře `en-us` se LUIS učí, jak rozlišovat anglická slova včetně slangem. V jazykové verzi `zh-cn` se aplikace LUIS učí, aby lišila většinu čínských znaků. Pokud používáte zřídka používané slovo v `en-us` nebo znaku v `zh-cn` a vidíte, že LUIS se zdá, že toto slovo nebo znak není možné odlišit, můžete toto slovo nebo znak přidat do [funkce seznamu frází](luis-how-to-add-features.md). Například slova mimo jazykovou verzi aplikace – to znamená, že cizí slova--by měla být přidána do funkce seznamu frází. 

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hybridní jazyky
Hybridní jazyky kombinují slova ze dvou kultur, jako je angličtina a čínština. Tyto jazyky nejsou v LUIS podporované, protože aplikace je založená na jedné jazykové verzi.

## <a name="tokenization"></a>Tokenizace
K provedení strojového učení LUIS rozdělí utterance na [tokeny](luis-glossary.md#token) založené na jazykové verzi.

|Jazyk|  každé místo nebo speciální znak | úroveň znaku|složená slova|[byla vrácena entita s tokenem.](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Čínština||✔||✔|
|Holandština|||✔|✔|
|Angličtina (EN-US)|✔ ||||
|Francouzština (fr-FR)|✔||||
|Francouzština (fr-CA)|✔||||
|Němčina|||✔|✔|
| Hindština |✔|-|-|-|-|
|Italština|✔||||
|Japonština||||✔|
|Korejština||✔||✔|
|Portugalština (Brazílie)|✔||||
|Španělština (ES-ES)|✔||||
|Španělština (ES-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Vlastní verze provádějících tokenizaci

Následující jazykové verze mají vlastní verze provádějících tokenizaci:

|Jazykových|Version|Účel|
|--|--|--|
|Němčina<br>`de-de`|1.0.0|Tokenizes slova rozdělením pomocí provádějících tokenizaci založeného na strojovém učení, které se pokusí rozdělit složené slova do jejich jediné součásti.<br>Pokud uživatel zadá `Ich fahre einen krankenwagen` jako utterance, je zapnutý `Ich fahre einen kranken wagen`. Povolení označení `kranken` a `wagen` nezávisle na různých entitách.|
|Němčina<br>`de-de`|1.0.2|Tokenizes slova rozdělením na mezery.<br> Pokud uživatel zadá `Ich fahre einen krankenwagen` jako utterance, zůstane jeden token. Proto `krankenwagen` je označena jako jediná entita. |

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

[Importujte soubor jako novou aplikaci](luis-how-to-start-new-app.md#import-an-app-from-file)místo verze. Tato akce znamená, že nová aplikace má jiné ID aplikace, ale používá verzi provádějících tokenizaci zadanou v souboru. 
