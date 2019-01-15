---
title: Podpora jazyků – rozhraní API pro vyhledávání zpráv Bingu
titleSuffix: Azure Cognitive Services
description: Seznam přirozeného jazyka, země a oblasti, které podporují rozhraní API pro vyhledávání zpráv Bingu.
services: cognitive-services
author: MikeDodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: v-gedod
ms.openlocfilehash: 1d8a46287d2b84735ffb6d5159346cb9b8243472
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263986"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Podpora jazyka a oblasti pro rozhraní API pro vyhledávání zpráv Bingu

Rozhraní API pro vyhledávání zpráv Bingu podporuje mnoho zemích nebo oblastech, mnoho s více než jeden jazyk. Zadání země/oblasti s dotazem slouží především pro upřesnění výsledků hledání podle zájmů v zemi/oblast. Kromě toho výsledky mohou obsahovat odkazy na Bingu a těchto odkazů může lokalizace uživatelského rozhraní Bing podle zadanou zemí nebo oblastí nebo jazyk.

Můžete zadat, země/oblast pomocí `cc` parametr dotazu. Pokud chcete zadat určitá země nebo oblast, musíte zadat také jeden nebo více kódů jazyka pomocí `Accept-Language` hlavičky protokolu HTTP. Podporované jazyky se liší podle oblasti/countr; jsou uvedeny pro každou zemi/oblast v tabulce trhy.

Alternativně můžete zadat na trhu pomocí `mkt` parametrů a kódu z dotazů **trhy** tabulky. Zadání na trhu současně Určuje zemi/oblast a jazyk. `setLang` Parametr dotazu může být nastavená na kód jazyka v tomto případě; obvykle je to stejný jazyk určený `mkt` Pokud uživatel preferuje zobrazíte Bingu v jiném jazyce.

## <a name="supported-markets-for-news-search-endpoint"></a>Podporované trhy pro koncový bod vyhledávání zpráv

Pro `/news/search` koncový bod, následující tabulka uvádí hodnoty kódu na trhu, které můžete použít k určení `mkt` parametr dotazu. Bing vrátí obsah pouze těchto trzích. V seznamu se můžou změnit.  

Pro seznam země/oblast kódů, které můžete zadat v `cc` parametr dotazu naleznete v tématu [kódy zemí](#countrycodes).  

|Země/oblast|Jazyk|Kód na trhu|  
|---------------------|--------------|-----------------|
|Dánsko|dánština|da-DK|
|Rakousko|Němčina|de-AT|
|Švýcarsko|Němčina|de-CH|
|Německo|Němčina|de-DE|
|Austrálie|Angličtina|cs AU|
|Kanada|Angličtina|cs CA|
|Spojené království|Angličtina|en-GB|
|Indonésie|Angličtina|cs ID|
|Irsko|Angličtina|cs IE|
|Indie|Angličtina|en-IN|
|Malajsie|Angličtina|cs MY|
|Nový Zéland|Angličtina|cs NZ|
|Filipínská republika|Angličtina|cs PH|
|Singapur|Angličtina|en-SG|
|Spojené státy|Angličtina|en-US|
|Angličtina|Obecné|en-WW|
|Angličtina|Obecné|en-XA|
|Jižní Afrika|Angličtina|en-ZA|
|Argentina|Španělština|es-AR|
|Chile|Španělština|es-CL|
|Španělsko|Španělština|es-ES|
|Mexiko|Španělština|es-MX|
|Spojené státy|Španělština|es-US|
|Španělština|Obecné|es-XL|
|Finsko|Finština|fi-FI|  
|Francie|Francouzština|fr-BE|
|Kanada|Francouzština|fr-CA|
|Belgie|Holandština|nl-BE|
|Švýcarsko|Francouzština|FR-CH|
|Francie|Francouzština|fr-FR|  
|Itálie|italština|IT-IT|
|Hongkong – zvláštní správní oblast|Tradiční čínština|zh-HK|  
|Tchaj-wan|Tradiční čínština|zh-TW|
|Japonsko|Japonština|ja-JP|  
|Jižní Korea|Korejština|ko-KR|  
|Nizozemsko|Holandština|NL-NL|  
|Čínská lidová republika|Čínština|zh-CN|  
|Brazílie|Portugalština|pt-BR|
|Rusko|ruština|ru-RU|  
|Švédsko|švédština|sv-SE|  
|Turecko|turečtina|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Podporované trhy pro koncový bod příspěvků
Pro `/news` koncový bod, následující tabulka uvádí hodnoty kódu na trhu, které můžete použít k určení `mkt` parametr dotazu. Bing vrátí obsah pouze těchto trzích. V seznamu se můžou změnit.  

Pro seznam země/oblast kódů, které můžete zadat v `cc` parametr dotazu naleznete v tématu [kódy zemí](#countrycodes).  

|Země/oblast|Jazyk|Kód na trhu|  
|---------------------|--------------|-----------------|
|Dánsko|dánština|da-DK|
|Německo|Němčina|de-DE|
|Austrálie|Angličtina|cs AU|
|Spojené království|Angličtina|en-GB|
|Spojené státy|Angličtina|en-US|
|Angličtina|Obecné|en-WW|
|Chile|Španělština|es-CL|
|Mexiko|Španělština|es-MX|
|Spojené státy|Španělština|es-US|
|Finsko|Finština|fi-FI|  
|Kanada|Francouzština|fr-CA|
|Francie|Francouzština|fr-FR|  
|Itálie|italština|IT-IT|
|Brazílie|Portugalština|pt-BR|
|Čínská lidová republika|Čínština|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Podporované trhy pro populární koncový bod příspěvků
Pro `/news/trendingtopics` koncový bod, následující tabulka uvádí hodnoty kódu na trhu, které můžete použít k určení `mkt` parametr dotazu. Bing vrátí obsah pouze těchto trzích. V seznamu se můžou změnit.  

Pro seznam země/oblast kódů, které můžete zadat v `cc` parametr dotazu naleznete v tématu [kódy zemí](#countrycodes).  

|Země/oblast|Jazyk|Kód na trhu|  
|---------------------|--------------|-----------------|
|Německo|Němčina|de-DE|
|Austrálie|Angličtina|cs AU|
|Spojené království|Angličtina|en-GB|
|Spojené státy|Angličtina|en-US|
|Kanada|Angličtina|cs CA|
|Indie|Angličtina|en-IN|
|Francie|Francouzština|fr-FR|
|Kanada|Francouzština|fr-CA|
|Brazílie|Portugalština|pt-BR|
|Čínská lidová republika|Čínština|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Kódy zemí  

Následují kódů země/oblasti, které můžete zadat v `cc` parametr dotazu. V seznamu se můžou změnit.  

|Země/oblast|Kód země|  
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

## <a name="next-steps"></a>Další postup
Další informace o koncových bodech Bingu pro vyhledávání zpráv najdete v tématu [referenční dokumentace rozhraní API pro vyhledávání zpráv v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).
