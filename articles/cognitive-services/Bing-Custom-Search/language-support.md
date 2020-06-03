---
title: Jazyková podpora – rozhraní API pro vlastní vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Seznam podporovaných jazyků a oblastí pro rozhraní API pro vlastní vyhledávání Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 004bd973651d5903db4254a8883be2c8a83d9b38
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310576"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Podpora jazyků a oblastí pro rozhraní API pro vlastní vyhledávání Bingu

Rozhraní API pro vlastní vyhledávání Bingu podporuje více než tři desítkové země nebo oblasti, mnoho s více než jedním jazykem.

I když je to volitelné, požadavek by měl specifikovat parametr dotazu [MKT](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) , který identifikuje na trhu, ze kterého mají výsledky přijít. Seznam volitelných parametrů dotazu najdete v tématu [parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) .

Můžete zadat zemi nebo oblast pomocí `cc` parametru dotazu. Zadáte-li zemi nebo oblast, je nutné zadat také jeden nebo více kódů jazyka pomocí `Accept-Language` záhlaví. Podporované jazyky se liší podle země nebo oblasti; jsou uvedené pro každou zemi nebo oblast v tabulce **trhy** .

`Accept-Language`Záhlaví a `setLang` parametr dotazu se vzájemně vylučují – nespecifikují obojí. Podrobnosti najdete v tématu [Accept-Language](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage).

## <a name="countriesregions"></a>Země nebo oblasti

|Země|Kód|
|-------|----|
|Argentina|AR|
|Australia|AU|
|Rakousko|AT|
|Belgie|BE|
|Brazílie|BR|
|Canada|CA|
|Chile|CL|
|Dánsko|DK|
|Finsko|FI|
|Francie|FR|
|Německo|DE|
|Hongkong – zvláštní administrativní oblast|HK|
|India|IN|
|Indonésie|ID|
|Itálie|IT|
|Japan|JP|
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
|Česká republika|USA|


## <a name="markets"></a>Trhy

|Země|Jazyk|Kód trhu|
|-------|--------|-----------|
|Argentina|Španělština|ES-AR|
|Australia|Angličtina|EN-AU|
|Rakousko|Němčina|de-AT|
|Belgie|Nizozemština|NL|
|Belgie|Francouzština|fr – bude|
|Brazílie|Portugalština|pt-BR|
|Canada|Angličtina|en-CA|
|Canada|Francouzština|fr – CA|
|Chile|Španělština|ES-CL|
|Dánsko|Dánština|da-DK|
|Finsko|Finština|fi-FI|
|Francie|Francouzština|fr-FR|
|Německo|Němčina|de-DE|
|Hongkong – zvláštní administrativní oblast|Tradiční čínština|zh – HK|
|India|Angličtina|en-IN|
|Indonésie|Angličtina|EN-ID|
|Itálie|Italština|it-IT|
|Japan|Japonština|ja-JP|
|Jižní Korea|Korejština|ko-KR|
|Malajsie|Angličtina|EN – MY|
|Mexiko|Španělština|ES – MX|
|Nizozemsko|Nizozemština|nl-NL|
|Nový Zéland|Angličtina|EN-NZ|
|Norsko|Norština|Ne – ne|
|Čína|Čínština|zh-CN|
|Polsko|Polština|pl-PL|
|Portugalsko|Portugalština|pt-PT|
|Filipíny|Angličtina|EN-PH|
|Rusko|Ruština|ru-RU|
|Saúdská Arábie|Arabština|ar-SA|
|Jižní Afrika|Angličtina|EN-ZA|
|Španělsko|Španělština|es-ES|
|Švédsko|Švédština|sv-SE|
|Švýcarsko|Francouzština|fr – CH|
|Švýcarsko|Němčina|de-CH|
|Tchaj-wan|Tradiční čínština|zh-TW|
|Turecko|Turečtina|tr-TR|
|Spojené království|Angličtina|en-GB|
|Česká republika|Angličtina|cs-CZ|
|Česká republika|Španělština|ES – US|
