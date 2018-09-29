---
title: Podpora jazyků – LUIS
titleSuffix: Azure Cognitive Services
description: Služba LUIS má celou řadu funkcí v rámci služby. Ne všechny funkce jsou na stejné paritu jazyka. Ujistěte se, že funkce, které vás zajímají jsou podporované v jazykové verzi jazyka, který cílíte. Aplikace LUIS je specifické pro jazykovou verzi a nedá se změnit, jakmile je nastavena.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/26/2018
ms.author: diberry
ms.openlocfilehash: ce0a2f36ca11c704062deb28bf47c45a91a32222
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435462"
---
# <a name="language-and-region-support-for-luis"></a>Podpora jazyka a oblasti pro LUIS

Služba LUIS má celou řadu funkcí v rámci služby. Ne všechny funkce jsou na stejné paritu jazyka. Ujistěte se, že funkce, které vás zajímají jsou podporované v jazykové verzi jazyka, který cílíte. Aplikace LUIS je specifické pro jazykovou verzi a nedá se změnit, jakmile je nastavena.

## <a name="multi-language-luis-apps"></a>Aplikace LUIS Vícejazyčná verze

Pokud potřebujete více jazyků LUIS klientské aplikace jako je například chatovacího robota, máte několik možností. Pokud služba LUIS podporuje všechny jazyky, při vývoji aplikace LUIS pro jednotlivé jazyky. Každá aplikace LUIS má ID a jedinečných aplikací a koncového bodu protokolu. Pokud je potřeba zadat jazyka pro jazyk LUIS nepodporuje, můžete použít [Microsoft Translator API](../Translator/translator-info-overview.md) přeložit utterance do podporovaného jazyka, utterance do koncového bodu služby LUIS odesílat a přijímat výsledné skóre.

## <a name="languages-supported"></a>Podporované jazyky

Služba LUIS rozumí projevy v následujících jazycích:

| Jazyk |Národní prostředí  |  Předem připravená doména | Předem připravených entit | Fráze návrhy | **[Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Mínění a<br>Klíčová slova)|
|--|--|:--:|:--:|:--:|:--:|
| Americkou angličtinu |`en-US` | ✔ | ✔  |✔|✔|
| Francouzština (Kanada) |`fr-CA` |-|   -   |-|✔|
| *[Čínština](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holandština |`nl-NL` |-|  -   |-|✔|
| Francouzština (Francie) |`fr-FR` |-| ✔ |✔ |✔|
| Němčina |`de-DE` |-| ✔ |✔ |✔|
| italština |`it-IT` |-| ✔ |✔|✔|
| *[Japonština](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Pouze klíčových frází|
| Korejština |`ko-KR` |-|   -   |-|Pouze klíčových frází|
| Portugalština (Brazílie) |`pt-BR` |-| ✔ |✔ |Ne všechny dílčí jazykových verzí|
| Španělština (Španělsko) |`es-ES` |-| ✔ |✔|✔|
| Španělština (Mexiko)|`es-MX` |-|  -   |✔|✔|


Podpora jazyků se liší u [předem připravených entit](luis-reference-prebuilt-entities.md) a [předem připravených domén](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>* Čínština podporují poznámky

 - V `zh-cn` jazykovou verzi, LUIS očekává zjednodušené čínštiny znakové sady namísto tradičních znakovou sadu.
 - Názvy tříd Intent, entity, funkce a regulární výrazy mohou být znaky čínské nebo římské číslice.
 - Najdete v článku [předem připravených domén odkaz ](luis-reference-prebuilt-domains.md) informace, na kterém jsou předem připravených domén podporovány v `zh-cn` jazykovou verzi.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>* Japonské podporuje poznámky

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
|italština|✔||||
|Japonština||||✔|
|Korejština||✔||✔|
|Portugalština (Brazílie)|✔||||
|Španělština (es-ES)|✔||||
|Španělština (es-MX)|✔||||
