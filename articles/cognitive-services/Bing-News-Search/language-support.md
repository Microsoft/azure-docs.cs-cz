---
title: Jazyková podpora – rozhraní API pro vyhledávání zpráv Bing
titleSuffix: Azure Cognitive Services
description: Seznam přirozených jazyků, zemí a oblastí, které jsou podporovány rozhraním API pro vyhledávání zpráv Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220190"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Jazyková a regionální podpora rozhraní API pro vyhledávání zpráv Bingu

Rozhraní API pro vyhledávání zpráv Bing urychlující mnoho zemí nebo oblastí, mnoho z nich s více než jedním jazykem. Určení země nebo oblasti s dotazem slouží především k upřesnění výsledků hledání na základě zájmů v dané zemi nebo oblasti. Kromě toho výsledky mohou obsahovat odkazy na Bing a tyto odkazy mohou lokalizovat uživatelské prostředí Bing podle zadané země nebo oblasti nebo jazyka.

Zemi nebo oblast můžete zadat `cc` pomocí parametru dotazu. Pokud zadáte zemi nebo oblast, musíte také zadat jeden `Accept-Language` nebo více kódů jazyka pomocí hlavičky HTTP. Podporované jazyky se liší podle počtu nebo oblasti; jsou uvedeny pro každou zemi nebo oblast v tabulce Trhy.

Případně můžete zadat trh pomocí `mkt` parametru dotazu a kódu z tabulky **Markets.** Zadání trhu současně určuje zemi nebo oblast a upřednostňovaný jazyk. Parametr `setLang` dotazu může být v tomto případě nastaven na kód jazyka; obvykle se jedná o `mkt` stejný jazyk, který byl určen, pokud uživatel neupřednostňuje zobrazení bingu v jiném jazyce.

## <a name="supported-markets-for-news-search-endpoint"></a>Podporované trhy pro koncový bod vyhledávání zpráv

Pro `/news/search` koncový bod v následující tabulce jsou uvedeny hodnoty tržního kódu, které můžete použít k určení parametru dotazu. `mkt` Bing vrací obsah pouze pro tyto trhy. Seznam se může změnit.  

Seznam kódů zemí nebo oblastí, které `cc` můžete zadat v parametru dotazu, naleznete v [tématu Kódy zemí](#countrycodes).  

|Země|Jazyk|Kód trhu|  
|---------------------|--------------|-----------------|
|Dánsko|Dánština|da-DK|
|Rakousko|Němčina|de-AT|
|Švýcarsko|Němčina|de-CH|
|Německo|Němčina|de-DE|
|Austrálie|Angličtina|en-AU|
|Kanada|Angličtina|en-CA|
|Spojené království|Angličtina|en-CZ|
|Indonésie|Angličtina|en-ID|
|Irsko|Angličtina|en-IE|
|Indie|Angličtina|en-IN|
|Malajsie|Angličtina|en-MY|
|Nový Zéland|Angličtina|en-NZ|
|Filipínská republika|Angličtina|en-PH|
|Singapur|Angličtina|en-SG|
|Spojené státy|Angličtina|cs-CZ|
|Angličtina|obecné|en-WW|
|Angličtina|obecné|en-XA|
|Jižní Afrika|Angličtina|en-ZA|
|Argentina|Španělština|es-AR|
|Chile|Španělština|es-CL|
|Španělsko|Španělština|es-ES|
|Mexiko|Španělština|es-MX|
|Spojené státy|Španělština|es-USA|
|Španělština|obecné|es-XL|
|Finsko|Finština|fi-FI|  
|Francie|Francouzština|fr-BE|
|Kanada|Francouzština|fr-CA|
|Belgie|Nizozemština|nl-BE|
|Švýcarsko|Francouzština|fr-CH|
|Francie|Francouzština|fr-FR|  
|Itálie|Italština|it-IT|
|Hongkong – zvláštní správní oblast|Tradiční čínština|zh-HK|  
|Tchaj-wan|Tradiční čínština|zh-TW|
|Japonsko|Japonština|ja-JP|  
|Jižní Korea|Korejština|ko-KR|  
|Nizozemsko|Nizozemština|nl-NL|  
|Čínská lidová republika|Chinese|zh-CN|  
|Brazílie|Portugalština|pt-BR|
|Rusko|Ruština|ru-RU|  
|Švédsko|Švédština|sv-SE|  
|Turecko|Turečtina|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Podporované trhy pro koncový bod zpráv
Pro `/news` koncový bod v následující tabulce jsou uvedeny hodnoty tržního kódu, které můžete použít k určení parametru dotazu. `mkt` Bing vrací obsah pouze pro tyto trhy. Seznam se může změnit.  

Seznam kódů zemí nebo oblastí, které `cc` můžete zadat v parametru dotazu, naleznete v [tématu Kódy zemí](#countrycodes).  

|Země|Jazyk|Kód trhu|  
|---------------------|--------------|-----------------|
|Dánsko|Dánština|da-DK|
|Německo|Němčina|de-DE|
|Austrálie|Angličtina|en-AU|
|Spojené království|Angličtina|en-CZ|
|Spojené státy|Angličtina|cs-CZ|
|Angličtina|obecné|en-WW|
|Chile|Španělština|es-CL|
|Mexiko|Španělština|es-MX|
|Spojené státy|Španělština|es-USA|
|Finsko|Finština|fi-FI|  
|Kanada|Francouzština|fr-CA|
|Francie|Francouzština|fr-FR|  
|Itálie|Italština|it-IT|
|Brazílie|Portugalština|pt-BR|
|Čínská lidová republika|Chinese|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Podporované trhy pro trendy novinky
Pro `/news/trendingtopics` koncový bod v následující tabulce jsou uvedeny hodnoty tržního kódu, které můžete použít k určení parametru dotazu. `mkt` Bing vrací obsah pouze pro tyto trhy. Seznam se může změnit.  

Seznam kódů zemí nebo oblastí, které `cc` můžete zadat v parametru dotazu, naleznete v [tématu Kódy zemí](#countrycodes).  

|Země|Jazyk|Kód trhu|  
|---------------------|--------------|-----------------|
|Německo|Němčina|de-DE|
|Austrálie|Angličtina|en-AU|
|Spojené království|Angličtina|en-CZ|
|Spojené státy|Angličtina|cs-CZ|
|Kanada|Angličtina|en-CA|
|Indie|Angličtina|en-IN|
|Francie|Francouzština|fr-FR|
|Kanada|Francouzština|fr-CA|
|Brazílie|Portugalština|pt-BR|
|Čínská lidová republika|Chinese|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Kódy zemí  

Níže jsou uvedeny kódy zemí nebo `cc` oblastí, které můžete zadat v parametru dotazu. Seznam se může změnit.  

|Země|Kód země|  
|---------------------|------------------|  
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
|Čínská lidová republika|CN|  
|Polsko|PL|  
|Portugalsko|PT|  
|Filipínská republika|PH|  
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

## <a name="next-steps"></a>Další kroky
Další informace o koncových bodech vyhledávání zpráv Bingu naleznete v [tématu Odkaz na rozhraní API pro vyhledávání zpráv v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).
