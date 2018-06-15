---
title: Podpora lokalizace pomocí LEOŠ aplikace v Azure | Microsoft Docs
description: Další informace o jazyky, které podporuje LEOŠ.
services: cognitive-services
author: cahann
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2017
ms.author: cahann
ms.openlocfilehash: 1eabc01ee07f8791680738a156471e3efe2c44ff
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "35343926"
---
# <a name="culture-specific-understanding-in-luis-apps"></a>Specifické pro jazykovou verzi pochopení LEOŠ aplikace

LEOŠ aplikace je specifické pro jazykovou verzi a po nastavení nejde změnit. 

## <a name="multi-language-luis-apps"></a>LEOŠ vícejazyčné aplikace
Pokud potřebujete více jazyků LEOŠ klientské aplikace jako je například chatbot, máte několik možností. Pokud LEOŠ podporuje všechny jazyky, vyvíjíte LEOŠ aplikace pro jednotlivé jazyky. Každá aplikace LEOŠ má ID aplikace jedinečný a koncový bod protokolu. Pokud potřebujete poskytovat pochopení pro jazyk LEOŠ nepodporuje, můžete použít jazyk [rozhraní API služby Microsoft překladač](../Translator/translator-info-overview.md) přeložit utterance do podporovaném jazyce, odešlete utterance ke koncovému bodu LEOŠ a přijímat Výsledný skóre.

## <a name="languages-supported"></a>Podporované jazyky
LEOŠ rozumí utterances v těchto jazycích:


| Jazyk |Národní prostředí  |  Předkompilované domény | Předkompilované entity | Návrhy fráze | **[Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) | 
|--|--|:--:|:--:|:--:|:--:|
| Americká angličtina |`en-US` | ✔ | ✔  |✔|✔|
| Francouzština (Kanada) |`fr-CA` |-|   -   |-|✔|
| *[Čínština](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holandština |`nl-NL` |-|  -   |-|✔|
| Francouzština (Francie) |`fr-FR` |-| ✔ |✔ |✔|
| Němčina |`de-DE` |-| ✔ |✔ |✔|
| italština |`it-IT` |-| ✔ |✔|✔|
| *[Japonština](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Pouze klíče fráze|
| Korejština |`ko-KR` |-|   -   |-|Pouze klíče fráze|
| Portugalština (Brazílie) |`pt-BR` |-| ✔ |✔ |Ne všechny dílčí jazykové verze|
| Španělština (Španělsko) |`es-ES` |-| ✔ |✔|✔|
| Španělština (Mexiko)|`es-MX` |-|  -   |✔|✔|


Podpora jazyků se liší u [předem entity](luis-reference-prebuilt-entities.md) a [předem domény](luis-reference-prebuilt-domains.md). 

### <a name="chinese-support-notes"></a>* Čínština podporují poznámky

 - V `zh-cn` jazykové verzi, LEOŠ očekává zjednodušená čínština znakové sady místo tradiční znaková sada.
 - Názvy tříd Intent, entity, funkce a regulární výrazy může být v čínština nebo římské znaků.
 - Najdete v článku [předem domén odkaz ](luis-reference-prebuilt-domains.md) informace, které jsou předem domén podporují v `zh-cn` jazykovou verzi.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>* Poznámky k japonské podpory

 - Protože LEOŠ neposkytuje syntaktické analýzy a nebude pochopit rozdíl mezi Keigo a neformální japonštině, budete muset mít různé úrovně náležitosti jako příklady školení pro vaše aplikace. 
     - でございます není stejný jako です. 
     - です není stejný jako だ. 

### <a name="text-analytics-support-notes"></a>** Analýza textu podporují poznámky
Je podporován pouze portugalština pro subkultury: `pt-PT` a `pt-BR`. Všechny ostatní jazykové verze jsou podporovány na úrovni primární jazykovou verzi. Další informace o Analýza textu [podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). 

### <a name="speech-api-supported-languages"></a>Rozpoznávání řeči rozhraní API, podporované jazyky
V tématu řeči [podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) pro rozpoznávání řeči diktování režimu jazyky.

### <a name="bing-spell-check-supported-languages"></a>Kontrola pravopisu Bing podporované jazyky
Najdete v části Kontrola pravopisu Bing [podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) seznam podporovaných jazyků a stav.

## <a name="rare-or-foreign-words-in-an-application"></a>Výjimečných nebo cizí slova v aplikaci
V `en-us` jazykové verzi, LEOŠ zjišťuje k rozlišení nejvíce anglická slova, včetně slang. V `zh-cn` jazykové verzi, LEOŠ zjišťuje k rozlišení většina čínské znaky. Pokud používáte výjimečných slova v `en-us` nebo znak v `zh-cn`, a zjistíte, že LEOŠ zdá se, že nelze k rozlišení toto slovo nebo znak, můžete přidat dané slovo nebo znak na [seznam frází funkce](luis-how-to-add-features.md). Například musí být přidaní slova mimo jazykovou verzi aplikace – tj. cizí slova – seznam frází funkce. Tento seznam frázi by měl být označen jiný zaměňovat, označíte, že sada výjimečných slova forms třídu, která by měl LEOŠ další rozpoznat, ale nejsou synonyma nebo zaměňovat mezi sebou.

### <a name="hybrid-languages"></a>Hybridní jazyky
Hybridní jazyky kombinovat slova ze dvou jazykové verze, jako je angličtina a čínština. Tyto jazyky nejsou podporované v LEOŠ, protože aplikace je založena na jednom jazykové verzi.

## <a name="tokenization"></a>Tokenizaci
K provedení strojové učení, LEOŠ dělí utterance [tokeny](luis-glossary.md#token) na základě jazykové verze. 

|Jazyk|  Každý prostor nebo speciální znak | úroveň znak|složených slov|[Tokenizovaná entity vrátil](luis-concept-data-extraction.md#tokenized-entity-returned)
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

 