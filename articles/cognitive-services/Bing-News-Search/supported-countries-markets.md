---
title: Podporované zemích a jazyky pro rozhraní API služby Bing zprávy Search v Azure | Microsoft Docs
description: Zjistěte, jaké zemích a jazyky jsou podporovány pomocí rozhraní API služby Bing Image Search.
services: cognitive-services
author: MikeDodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 12/20/2017
ms.author: v-gedod
ms.openlocfilehash: 80326d66e509b64efd5d386fe793bc9942b29ae3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342537"
---
# <a name="bing-news-search-countries-and-languages"></a>Hledání zprávy Bing zemích a jazyky

Rozhraní API služby Bing zprávy Search podporuje mnoho zemích, mnoho s více než jeden jazyk. Určení zemi s dotazem slouží především k zpřesnění výsledků vyhledávání podle zájmů v dané země. Kromě toho výsledky mohou obsahovat odkazy na Bing a tyto odkazy mohou lokalizaci činnost koncového uživatele Bing podle zadaného zemi nebo jazyk.

Můžete zadat země pomocí `cc` parametr dotazu. Pokud zadáte jiné zemi, musíte zadat také jeden nebo více kódů pomocí `Accept-Language` hlavičky protokolu HTTP. Podporované jazyky se liší podle země; jsou uvedené pro každou zemi v tabulce trhů.

Alternativně můžete určit na trhu pomocí `mkt` parametr a kód z dotazů **trhů** tabulky. Určení na trhu současně Určuje zemi a upřednostňovaný jazyk. `setLang` Parametr dotazu může být nastaven na kód jazyka v tomto případě; to je obvykle stejný jazyk, který `mkt` Pokud uživatel upřednostní zobrazíte Bing v jiném jazyce.

## <a name="supported-markets-for-news-search-endpoint"></a>Podporované trhů pro koncový bod hledání zprávy

Pro `/news/search` koncový bod, následující tabulka uvádí hodnoty trhu kódu, které můžete použít k určení `mkt` parametr dotazu. Bing vrátí obsah pouze na těchto trzích. V seznamu se mohou změnit.  
  
Pro seznam země kódy, můžete určit v `cc` parametr dotazu najdete v tématu [kódy zemí](#countrycodes).  
  
|Země/oblast|Jazyk|Trhu kódu|  
|---------------------|--------------|-----------------| 
|Dánsko|dánština|da-DK|
|Rakousko|Němčina|de-AT| 
|Švýcarsko|Němčina|de-CH|
|Německo|Němčina|de-DE|
|Austrálie|Angličtina|en-AU|
|Kanada|Angličtina|en-CA|
|Spojené království|Angličtina|en-GB|
|Indonésie|Angličtina|en-ID|
|Irsko|Angličtina|en-IE|
|Indie|Angličtina|en-IN|
|Malajsie|Angličtina|en Moje|
|Nový Zéland|Angličtina|en-NZ|
|Filipínská republika|Angličtina|en parametr|
|Singapur|Angličtina|en-SG|
|Spojené státy|Angličtina|cs-CZ|
|Angličtina|Obecné|en TT|
|Angličtina|Obecné|en-XA|
|Jihoafrická republika|Angličtina|en-ZA|
|Argentina|Španělština|ES-AR|
|Chile|Španělština|ES-CL|
|Španělsko|Španělština|ES-ES|
|Mexiko|Španělština|es-MX|
|Spojené státy|Španělština|ES USA| 
|Španělština|Obecné|ES-XL|
|Finsko|Finština|fi-FI|  
|Francie|Francouzština|FR-být|
|Kanada|Francouzština|fr-CA| 
|Belgie|Holandština|nl-BE|
|Švýcarsko|Francouzština|FR-CH|
|Francie|Francouzština|fr-FR|  
|Itálie|italština|IT-IT| 
|Hongkong – zvláštní správní oblast|Tradiční čínština|zh-HK|  
|Tchaj-wan|Tradiční čínština|zh-TW|
|Japonsko|Japonština|ja-JP|  
|Korea|Korejština|ko-KR|  
|Nizozemsko|Holandština|NL-NL|  
|Čínská lidová republika|Čínština|zh-CN|  
|Brazílie|Portugalština|pt-BR|
|Rusko|ruština|ru-RU|  
|Švédsko|švédština|sv-SE|  
|Turecko|turečtina|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Podporované trhů pro koncový bod zprávy
Pro `/news` koncový bod, následující tabulka uvádí hodnoty trhu kódu, které můžete použít k určení `mkt` parametr dotazu. Bing vrátí obsah pouze na těchto trzích. V seznamu se mohou změnit.  
  
Pro seznam země kódy, můžete určit v `cc` parametr dotazu najdete v tématu [kódy zemí](#countrycodes).  
  
|Země/oblast|Jazyk|Trhu kódu|  
|---------------------|--------------|-----------------| 
|Dánsko|dánština|da-DK|
|Německo|Němčina|de-DE|
|Austrálie|Angličtina|en-AU|
|Spojené království|Angličtina|en-GB|
|Spojené státy|Angličtina|cs-CZ|
|Angličtina|Obecné|en TT|
|Chile|Španělština|ES-CL|
|Mexiko|Španělština|es-MX|
|Spojené státy|Španělština|ES USA| 
|Finsko|Finština|fi-FI|  
|Kanada|Francouzština|fr-CA|
|Francie|Francouzština|fr-FR|  
|Itálie|italština|IT-IT| 
|Brazílie|Portugalština|pt-BR|
|Čínská lidová republika|Čínština|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Podporované trhů pro koncový bod trendů zprávy
Pro `/news/trendingtopics` koncový bod, následující tabulka uvádí hodnoty trhu kódu, které můžete použít k určení `mkt` parametr dotazu. Bing vrátí obsah pouze na těchto trzích. V seznamu se mohou změnit.  
  
Pro seznam země kódy, můžete určit v `cc` parametr dotazu najdete v tématu [kódy zemí](#countrycodes).  
  
|Země/oblast|Jazyk|Trhu kódu|  
|---------------------|--------------|-----------------| 
|Německo|Němčina|de-DE|
|Austrálie|Angličtina|en-AU|
|Spojené království|Angličtina|en-GB|
|Spojené státy|Angličtina|cs-CZ|
|Kanada|Angličtina|en-CA|
|Indie|Angličtina|en-IN|
|Francie|Francouzština|fr-FR|
|Kanada|Francouzština|fr-CA|
|Brazílie|Portugalština|pt-BR|
|Čínská lidová republika|Čínština|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Kódy zemí  

Toto jsou kódy zemí, které můžete určit v `cc` parametr dotazu. V seznamu se mohou změnit.  
  
|Země/oblast|Kód země|  
|---------------------|------------------|  
|Argentina|AR|  
|Austrálie|AU|  
|Rakousko|NA|  
|Belgie|BÝT|  
|Brazílie|BRAZÍLIE|  
|Kanada|CA|  
|Chile|CL|  
|Dánsko|DK|  
|Finsko|FI|  
|Francie|FR|  
|Německo|NĚMECKO|  
|Hongkong – zvláštní správní oblast|(HONG KONG)|  
|Indie|V|  
|Indonésie|ID|  
|Itálie|IT|  
|Japonsko|JP|  
|Korea|KR|  
|Malajsie|MOJE|  
|Mexiko|MX|  
|Nizozemsko|NL|  
|Nový Zéland|NZ|  
|Norsko|NE|  
|Čínská lidová republika|CN|  
|Polsko|PL|  
|Portugalsko|PT|  
|Filipínská republika|PARAMETR|  
|Rusko|RU|  
|Saúdská Arábie|PŘIDRUŽENÍ ZABEZPEČENÍ|  
|Jihoafrická republika|ZA|  
|Španělsko|ES|  
|Švédsko|SE|  
|Švýcarsko|CH|  
|Tchaj-wan|TRADIČNÍ ČÍNŠTINA|  
|Turecko|TR|  
|Spojené království|GB|  
|Spojené státy|USA|

## <a name="next-steps"></a>Další postup
Další informace o hledání zprávy Bing koncových bodů najdete v tématu [referenční dokumentace rozhraní API pro vyhledávání k zprávy v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).