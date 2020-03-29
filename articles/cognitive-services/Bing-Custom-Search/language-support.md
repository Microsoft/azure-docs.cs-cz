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
ms.openlocfilehash: 85326ae9166f7ea15ec2f45c01755b8f9ef03aff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "66388569"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Jazyková a oblastová podpora rozhraní API pro vlastní vyhledávání Bingu

Rozhraní API pro vlastní vyhledávání Bingu podporuje více než tři desítky zemí nebo oblastí, z nich mnohé s více než jedním jazykem.

I když je volitelné, požadavek by měl určit parametr dotazu [mkt,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) který identifikuje trh, odkud chcete, aby výsledky pocházejí. Seznam volitelných parametrů dotazu naleznete v tématu [Parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)

Zemi nebo oblast můžete zadat `cc` pomocí parametru dotazu. Pokud zadáte zemi nebo oblast, musíte také zadat jeden `Accept-Language` nebo více kódů jazyka pomocí záhlaví. Podporované jazyky se liší podle země nebo oblasti; jsou uvedeny pro každou zemi nebo oblast v tabulce **Trhy.**

Záhlaví `Accept-Language` a `setLang` parametr dotazu se vzájemně vylučují – nezadávejte obojí. Podrobnosti naleznete v [tématu Accept-Language](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage).

## <a name="countriesregions"></a>Země/oblasti

|Země|kód|
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
|Hongkong – zvláštní správní oblast|HK|
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

|Země|Jazyk|Tržní kód|
|-------|--------|-----------|
|Argentina|Španělština|es-AR|
|Austrálie|Angličtina|en-AU|
|Rakousko|Němčina|de-AT|
|Belgie|Nizozemština|nl-BE|
|Belgie|Francouzština|fr-BE|
|Brazílie|Portugalština|pt-BR|
|Kanada|Angličtina|en-CA|
|Kanada|Francouzština|fr-CA|
|Chile|Španělština|es-CL|
|Dánsko|Dánština|da-DK|
|Finsko|Finština|fi-FI|
|Francie|Francouzština|fr-FR|
|Německo|Němčina|de-DE|
|Hongkong, SAR|Tradiční čínština|zh-HK|
|Indie|Angličtina|en-IN|
|Indonésie|Angličtina|en-ID|
|Itálie|Italština|it-IT|
|Japonsko|Japonština|ja-JP|
|Jižní Korea|Korejština|ko-KR|
|Malajsie|Angličtina|en-MY|
|Mexiko|Španělština|es-MX|
|Nizozemsko|Nizozemština|nl-NL|
|Nový Zéland|Angličtina|en-NZ|
|Norsko|Norština|ne-NE|
|Čína|Chinese|zh-CN|
|Polsko|Polština|pl-PL|
|Portugalsko|Portugalština|pt-PT|
|Filipíny|Angličtina|en-PH|
|Rusko|Ruština|ru-RU|
|Saúdská Arábie|Arabština|ar-SA|
|Jižní Afrika|Angličtina|en-ZA|
|Španělsko|Španělština|es-ES|
|Švédsko|Švédština|sv-SE|
|Švýcarsko|Francouzština|fr-CH|
|Švýcarsko|Němčina|de-CH|
|Tchaj-wan|Tradiční čínština|zh-TW|
|Turecko|Turečtina|tr-TR|
|Spojené království|Angličtina|en-CZ|
|Spojené státy|Angličtina|cs-CZ|
|Spojené státy|Španělština|es-USA|
