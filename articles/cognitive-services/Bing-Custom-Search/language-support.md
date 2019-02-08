---
title: Podpora jazyků – API pro vlastní vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Seznam podporovaných jazyků a oblastí pro rozhraní API pro vlastní vyhledávání Bingu.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: v-gedod
ms.openlocfilehash: 942e61b0ee69402d516982c66a852d5e26304809
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859433"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Podpora jazyka a oblasti pro rozhraní API pro vlastní vyhledávání Bingu

Rozhraní API pro vlastní vyhledávání Bingu podporuje více než deseti tři zemích nebo oblastech, mnoho s více než jeden jazyk.

I když je volitelné, žádost by měl uvádět [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#mkt) parametr dotazu, který identifikuje na trhu, kde chcete výsledky pocházet z. Seznam volitelné parametry dotazů najdete v tématu [parametry dotazu.](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)

Můžete zadat, země/oblast pomocí `cc` parametr dotazu. Pokud chcete zadat určitá země nebo oblast, musíte zadat také jeden nebo více kódů jazyka pomocí `Accept-Language` záhlaví. Podporované jazyky se liší podle země nebo oblasti; jsou uvedeny pro každou zemi/oblast v **trhy** tabulky.

`Accept-Language` Záhlaví a `setLang` parametr dotazu se navzájem vylučují – nezadávejte obě. Podrobnosti najdete v tématu [Accept-Language](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#acceptlanguage).

## <a name="countries"></a>Země

|Země/oblast|Kód|
|-------|----|
|Argentina|AR|
|Austrálie|AU|
|Rakousko|AT|
|Belgie|BE|
|Brazílie|BR|
|Kanada|CA|
|Chile|CL|
|Dánsko|DK|
|Finsko|FI|
|Francie|FR|
|Německo|DE|
|Hongkong|HK|
|Indie|IN|
|Indonésie|ID|
|Itálie|IT|
|Japonsko|JP|
|Jižní Korea|KR|
|Malajsie|MY|
|Mexiko|MX|
|Nizozemsko|NL|
|Nový Zéland|NZ|
|Norsko|NO|
|Čína|CN|
|Polsko|PL|
|Portugalsko|PT|
|Filipíny|PH|
|Rusko|RU|
|Saúdská Arábie|SA|
|Jižní Afrika|ZA|
|Španělsko|ES|
|Švédsko|SE|
|Švýcarsko|CH|
|Tchaj-wan|TW|
|Turecko|TR|
|Spojené království|GB|
|Spojené státy|USA|


## <a name="markets"></a>Trhy

|Země/oblast|Jazyk|Kód na trhu|
|-------|--------|-----------|
|Argentina|Španělština|es-AR|
|Austrálie|Angličtina|cs AU|
|Rakousko|Němčina|de-AT|
|Belgie|Holandština|nl-BE|
|Belgie|Francouzština|fr-BE|
|Brazílie|Portugalština|pt-BR|
|Kanada|Angličtina|cs CA|
|Kanada|Francouzština|fr-CA|
|Chile|Španělština|es-CL|
|Dánsko|dánština|da-DK|
|Finsko|Finština|fi-FI|
|Francie|Francouzština|fr-FR|
|Německo|Němčina|de-DE|
|Hongkong|Tradiční čínština|zh-HK|
|Indie|Angličtina|en-IN|
|Indonésie|Angličtina|cs ID|
|Itálie|italština|IT-IT|
|Japonsko|Japonština|ja-JP|
|Jižní Korea|Korejština|ko-KR|
|Malajsie|Angličtina|cs MY|
|Mexiko|Španělština|es-MX|
|Nizozemsko|Holandština|NL-NL|
|Nový Zéland|Angličtina|cs NZ|
|Norsko|norština|no-NO|
|Čína|Čínština|zh-CN|
|Polsko|polština|pl-PL|
|Portugalsko|Portugalština|pt-PT|
|Filipíny|Angličtina|cs PH|
|Rusko|ruština|ru-RU|
|Saúdská Arábie|arabština|ar-SA|
|Jižní Afrika|Angličtina|en-ZA|
|Španělsko|Španělština|es-ES|
|Švédsko|švédština|sv-SE|
|Švýcarsko|Francouzština|FR-CH|
|Švýcarsko|Němčina|de-CH|
|Tchaj-wan|Tradiční čínština|zh-TW|
|Turecko|turečtina|tr-TR|
|Spojené království|Angličtina|en-GB|
|Spojené státy|Angličtina|en-US|
|Spojené státy|Španělština|es-US|
