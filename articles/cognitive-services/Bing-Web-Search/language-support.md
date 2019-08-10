---
title: Jazyková podpora – rozhraní API Bingu pro vyhledávání na webu
titleSuffix: Azure Cognitive Services
description: Seznam přirozených jazyků, zemí a oblastí, které jsou podporovány rozhraní API Bingu pro vyhledávání zpráv.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882678"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Podpora jazyků a oblastí pro rozhraní API Bingu pro vyhledávání na webu

Rozhraní API Bingu pro vyhledávání na webu podporuje více než tři desítkové země nebo oblasti, mnoho s více než jedním jazykem. Zadání země nebo oblasti s dotazem vám pomůže Upřesnit výsledky hledání na základě zájmů těchto zemí nebo oblastí. Výsledky můžou zahrnovat odkazy na Bing a tyto odkazy můžou lokalizovat uživatelské prostředí Bingu podle konkrétní země nebo oblasti nebo jazyka.

Můžete zadat zemi nebo oblast pomocí `cc` parametru dotazu. Pokud je zadána země nebo oblast, je nutné zadat jeden nebo více kódů jazyka s [ `Accept-Language` hlavičkou](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). V [tabulce trhy](#markets) můžete zobrazit seznam jazyků podporovaných na jednotlivých trzích.

Alternativně můžete zadat trh s `mkt` parametrem dotazu a kód z tabulky **trhy** . Určování trhu současně určuje zemi nebo oblast a preferovaný jazyk. Jazyk můžete explicitně nastavit pomocí `setLang` parametru dotazu.

## <a name="countriesregions"></a>Země/oblasti

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
|Hongkong – zvláštní správní oblast|HK|
|Indie|IN|
|Indonésie|id|
|Itálie|it|
|Japonsko|JP|
|Korejská republika|KR|
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

|Země/oblast|Jazyk|Kód trhu|
|-------|--------|-----------|
|Argentina|Španělština|ES-AR|
|Austrálie|Angličtina|cs AU|
|Rakousko|Němčina|de-AT|
|Belgie|Holandština|nl-BE|
|Belgie|Francouzština|fr – bude|
|Brazílie|Portugalština|pt-BR|
|Kanada|Angličtina|cs CA|
|Kanada|Francouzština|fr-CA|
|Chile|Španělština|es-CL|
|Dánsko|dánština|da-DK|
|Finsko|Finština|fi-FI|
|Francie|Francouzština|fr-FR|
|Německo|Němčina|de-DE|
|Hongkong – zvláštní správní oblast|Tradiční čínština|zh-HK|
|Indie|Angličtina|en-IN|
|Indonésie|Angličtina|EN-ID|
|Itálie|italština|IT-IT|
|Japonsko|Japonština|ja-JP|
|Korejská republika|Korejština|ko-KR|
|Malajsie|Angličtina|EN – MY|
|Mexiko|Španělština|es-MX|
|Nizozemsko|Holandština|NL-NL|
|Nový Zéland|Angličtina|cs NZ|
|Norsko|norština|Ne – ne|
|Čína|Čínština|zh-CN|
|Polsko|polština|pl-PL|
|Portugalsko|Portugalština|pt-PT|
|Filipíny|Angličtina|EN-PH|
|Rusko|ruština|ru-RU|
|Saúdská Arábie|arabština|ar-SA|
|Jižní Afrika|Angličtina|EN-ZA|
|Španělsko|Španělština|es-ES|
|Švédsko|švédština|sv-SE|
|Švýcarsko|Francouzština|FR-CH|
|Švýcarsko|Němčina|de-CH|
|Tchaj-wan|Tradiční čínština|zh-TW|
|Turecko|turečtina|tr-TR|
|Spojené království|Angličtina|en-GB|
|Spojené státy|Angličtina|en-US|
|Spojené státy|Španělština|es-US|

## <a name="next-steps"></a>Další postup

* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
