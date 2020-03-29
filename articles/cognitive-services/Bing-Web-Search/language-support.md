---
title: Jazyková podpora – rozhraní API pro vyhledávání na webu Bingu
titleSuffix: Azure Cognitive Services
description: Seznam přirozených jazyků, zemí a oblastí, které jsou podporovány rozhraním API pro vyhledávání zpráv Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68882678"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Jazyková a oblastová podpora rozhraní API pro vyhledávání na webu Bingu

Rozhraní API pro vyhledávání na webu Bing podporuje více než tři desítky zemí nebo oblastí, z nich mnohé s více než jedním jazykem. Určení země nebo oblasti pomocí dotazu pomáhá upřesnit výsledky vyhledávání na základě zájmů dané země nebo oblastí. Výsledky mohou zahrnovat odkazy na Bing a tyto odkazy mohou lokalizovat uživatelské prostředí Bingpodle zadané země nebo oblasti nebo jazyka.

Pomocí parametru dotazu `cc` můžete určit zemi nebo oblast. Pokud je zadána země nebo oblast, je nutné zadat jeden nebo více kódů jazyků s [ `Accept-Language` hlavičkou](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). Tabulka [Trhy](#markets) slouží k seznamu jazyků podporovaných na jednotlivých trzích.

Případně můžete zadat trh s `mkt` parametrem dotazu a kód z tabulky **Markets.** Zadání trhu současně určuje zemi nebo oblast a upřednostňovaný jazyk. Jazyk můžete explicitně nastavit `setLang` pomocí parametru dotazu.

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
|Hongkong – zvláštní správní oblast|Tradiční čínština|zh-HK|
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

## <a name="next-steps"></a>Další kroky

* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
