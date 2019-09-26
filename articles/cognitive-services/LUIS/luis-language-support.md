---
title: Jazyková podpora – LUIS
titleSuffix: Azure Cognitive Services
description: Služba LUIS má celou řadu funkcí v rámci služby. Ne všechny funkce jsou na stejné paritu jazyka. Ujistěte se, že funkce, které vás zajímají jsou podporované v jazykové verzi jazyka, který cílíte. Aplikace LUIS je specifické pro jazykovou verzi a nedá se změnit, jakmile je nastavena.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 94f019205959d63a05ed3d90ede59fece3c05901
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316358"
---
# <a name="language-and-region-support-for-luis"></a>Podpora jazyka a oblasti pro LUIS

Služba LUIS má celou řadu funkcí v rámci služby. Ne všechny funkce jsou na stejné paritu jazyka. Ujistěte se, že funkce, které vás zajímají jsou podporované v jazykové verzi jazyka, který cílíte. Aplikace LUIS je specifické pro jazykovou verzi a nedá se změnit, jakmile je nastavena.

## <a name="multi-language-luis-apps"></a>Aplikace LUIS Vícejazyčná verze

Pokud potřebujete více jazyků LUIS klientské aplikace jako je například chatovacího robota, máte několik možností. Pokud služba LUIS podporuje všechny jazyky, při vývoji aplikace LUIS pro jednotlivé jazyky. Každá aplikace LUIS má ID a jedinečných aplikací a koncového bodu protokolu. Pokud je potřeba zadat jazyka pro jazyk LUIS nepodporuje, můžete použít [Microsoft Translator API](../Translator/translator-info-overview.md) přeložit utterance do podporovaného jazyka, utterance do koncového bodu služby LUIS odesílat a přijímat výsledné skóre.

## <a name="languages-supported"></a>Podporované jazyky

Služba LUIS rozumí projevy v následujících jazycích:

| Jazyk |Národní prostředí  |  Předem připravená doména | Předem připravených entit | Doporučení pro seznam frází | **[Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Mínění a<br>Klíčová slova)|
|--|--|:--:|:--:|:--:|:--:|
| Americkou angličtinu |`en-US` | ✔ | ✔  |✔|✔|
| *[Čínština](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holandština |`nl-NL` |✔|  -   |-|✔|
| Francouzština (Francie) |`fr-FR` |✔| ✔ |✔ |✔|
| Francouzština (Kanada) |`fr-CA` |-|   -   |-|✔|
| Němčina |`de-DE` |✔| ✔ |✔ |✔|
| Hindština | `hi-IN`|-|-|-|-|
| italština |`it-IT` |✔| ✔ |✔|✔|
| *[Japonština](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Pouze klíčových frází|
| Korejština |`ko-KR` |✔|   -   |-|Pouze klíčových frází|
| Portugalština (Brazílie) |`pt-BR` |✔| ✔ |✔ |Ne všechny dílčí jazykových verzí|
| Španělština (Španělsko) |`es-ES` |✔| ✔ |✔|✔|
| Španělština (Mexiko)|`es-MX` |-|  -   |✔|✔|
| turečtina | `tr-TR` |✔|-|-|Jenom mínění|


Podpora jazyků se liší u [předem připravených entit](luis-reference-prebuilt-entities.md) a [předem připravených domén](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>\* Čínština podporují poznámky

 - V `zh-cn` jazykovou verzi, LUIS očekává zjednodušené čínštiny znakové sady namísto tradičních znakovou sadu.
 - Názvy tříd Intent, entity, funkce a regulární výrazy mohou být znaky čínské nebo římské číslice.
 - Informace o tom, které předem sestavené domény jsou v `zh-cn` jazykové verzi podporované, najdete v referenčních informacích k [předdefinovaným doménám](luis-reference-prebuilt-domains.md) .
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>\* Japonské podporuje poznámky

 - Protože služba LUIS neposkytuje syntaktické analýzy a nebude pochopili rozdíl mezi Keigo a neformální japonština, budete muset začlenit různé úrovně formální jako příklady školení pro vaše aplikace.
     - でございます není stejný jako です.
     - です není stejný jako だ.

### <a name="text-analytics-support-notes"></a>** Text analytics podporuje poznámky
Rozhraní text analytics zahrnuje keyPhrase předem připravených entit a mínění analýzy. Je podporováno pouze portugalština subkultury: `pt-PT` a `pt-BR`. Všechny další jazykové verze se podporují na úrovni primární jazykovou verzi. Další informace o rozhraní Text Analytics [podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages).

### <a name="speech-api-supported-languages"></a>Speech API podporované jazyky
Zobrazit řeči [podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) jazyků řeči diktování režimu.

### <a name="bing-spell-check-supported-languages"></a>Jazyky podporované kontrolu pravopisu Bingu
Zobrazit kontrolu pravopisu Bingu [podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) seznam podporovaných jazyků a stav.

## <a name="rare-or-foreign-words-in-an-application"></a>Zřídka nebo cizí slova v aplikaci
V `en-us` jazykovou verzi, LUIS učí k rozlišení nejvíce anglických slov, včetně slang. V `zh-cn` jazykovou verzi, LUIS učí k rozlišení většina čínské znaky. Pokud používáte výjimečných slova v `en-us` nebo znak `zh-cn`, a uvidíte, že služba LUIS zdá se, že nelze rozlišit toto slovo nebo znak, můžete přidat toto slovo nebo znak do [funkci seznamu frázi](luis-how-to-add-features.md). Slova mimo jazykovou verzi aplikace – tj. cizí slova – například by měl být přidána do funkce seznam frází. Tento seznam frázi by měla být označena bez zaměňovat, označuje, že sadu výjimečných slova tvoří třídu, která by měla služba LUIS učení se rozpoznávání, ale nejsou synonyma nebo mezi sebou vzájemně zaměnitelné.

### <a name="hybrid-languages"></a>Hybridní jazyky
Hybridní jazyky kombinovat slova a ty dvě jazykové verze, jako je angličtina a čínštinu. Tyto jazyky nepodporuje LUIS protože aplikace je založena na jediné jazykové verze.

## <a name="tokenization"></a>Tokenizace
Pro strojové učení, LUIS, rozdělí do utterance [tokeny](luis-glossary.md#token) založenými na jazykové verzi.

|Jazyk|  Každý mezera nebo speciální znak | úroveň znak|složených slov|[Vrátí tokenizovaná entity](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Čínština||✔||✔|
|Holandština|||✔|✔|
|Angličtina (en-us)|✔ ||||
|Francouzština (fr-FR)|✔||||
|Francouzština (fr-CA)|✔||||
|Němčina|||✔|✔|
| Hindština |✔|-|-|-|-|
|italština|✔||||
|Japonština||||✔|
|Korejština||✔||✔|
|Portugalština (Brazílie)|✔||||
|Španělština (es-ES)|✔||||
|Španělština (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Vlastní verze provádějících tokenizaci

Následující jazykové verze mají vlastní verze provádějících tokenizaci:

|Jazyková verze|Version|Účel|
|--|--|--|
|Němčina<br>`de-de`|1.0.0|Tokenizes slova rozdělením pomocí provádějících tokenizaci založeného na strojovém učení, které se pokusí rozdělit složené slova do jejich jediné součásti.<br>Pokud uživatel zadá `Ich fahre einen krankenwagen` jako utterance, je `Ich fahre einen kranken wagen`zapnutý. Povolení označení `kranken` a `wagen` nezávislého označování jako různých entit.|
|Němčina<br>`de-de`|1.0.2|Tokenizes slova rozdělením na mezery.<br> Pokud uživatel zadá `Ich fahre einen krankenwagen` jako utterance, zůstane jeden token. Proto `krankenwagen` je označen jako jediná entita. |

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
