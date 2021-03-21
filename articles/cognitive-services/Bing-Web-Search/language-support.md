---
title: Jazyková podpora – rozhraní API Bingu pro vyhledávání na webu
titleSuffix: Azure Cognitive Services
description: Seznam přirozených jazyků, zemí a oblastí, které jsou podporovány rozhraní API Bingu pro vyhledávání na webu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 6de01d2c12454f43a2802b4a7a979b5f74b46f0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96340267"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Podpora jazyků a oblastí pro rozhraní API Bingu pro vyhledávání na webu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Rozhraní API Bingu pro vyhledávání na webu podporuje více než tři desítkové země nebo oblasti, mnoho s více než jedním jazykem. Zadání země nebo oblasti s dotazem vám pomůže Upřesnit výsledky hledání na základě zájmů těchto zemí nebo oblastí. Výsledky můžou zahrnovat odkazy na Bing a tyto odkazy můžou lokalizovat uživatelské prostředí Bingu podle konkrétní země nebo oblasti nebo jazyka.

Můžete zadat zemi nebo oblast pomocí `cc` parametru dotazu. Pokud je zadána země nebo oblast, je nutné zadat jeden nebo více kódů jazyka s [ `Accept-Language` hlavičkou](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). V [tabulce trhy](#markets) můžete zobrazit seznam jazyků podporovaných na jednotlivých trzích.

Alternativně můžete zadat trh s `mkt` parametrem dotazu a kód z tabulky **trhy** . Určování trhu současně určuje zemi nebo oblast a preferovaný jazyk. Jazyk můžete explicitně nastavit pomocí `setLang` parametru dotazu.

## <a name="countriesregions"></a>Země nebo oblasti

|Země|Kód|
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
|Hongkong – zvláštní administrativní oblast|HK|
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
|USA|USA|

## <a name="markets"></a>Trhy

|Země|Jazyk|Kód trhu|
|-------|--------|-----------|
|Argentina|Španělština|ES-AR|
|Austrálie|Angličtina|EN-AU|
|Rakousko|Němčina|de-AT|
|Belgie|Nizozemština|NL|
|Belgie|Francouzština|fr – bude|
|Brazílie|Portugalština|pt-BR|
|Kanada|Angličtina|en-CA|
|Kanada|Francouzština|fr – CA|
|Chile|Španělština|ES-CL|
|Dánsko|Dánština|da-DK|
|Finsko|Finština|fi-FI|
|Francie|Francouzština|fr-FR|
|Německo|Němčina|de-DE|
|Hongkong – zvláštní administrativní oblast|Tradiční čínština|zh – HK|
|Indie|Angličtina|en-IN|
|Indonésie|Angličtina|EN-ID|
|Itálie|Italština|it-IT|
|Japonsko|Japonština|ja-JP|
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
|USA|Angličtina|en-US|
|USA|Španělština|ES – US|

## <a name="next-steps"></a>Další kroky

* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)