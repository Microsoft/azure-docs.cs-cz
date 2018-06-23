---
title: Podporované jazyky a zemích rozhraní API vyhledávání bitové kopie Bingu v Azure | Microsoft Docs
description: Zjistěte, jaké zemích a jazyky jsou podporovány pomocí rozhraní API služby Bing Image Search.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: d0d33ee714ba5cd1ce4e846b96c04f755933bee1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343404"
---
# <a name="bing-image-search-countries-and-languages"></a>Hledání bitové kopie Bingu zemích a jazyky

Rozhraní API služby Bing Image Search podporuje více než tří tucet zemích, mnoho s více než jeden jazyk. Určení zemi s dotazem slouží především k zpřesnění výsledků vyhledávání podle zájmů v dané země. Kromě toho výsledky mohou obsahovat odkazy na Bing a tyto odkazy mohou lokalizaci činnost koncového uživatele Bing podle zadaného zemi nebo jazyk.

Chcete-li zadat země a jazyk, nastavte `mkt` parametr dotazu (trhu) kód z **trhů** následující tabulka. Na trhu Určuje zemi i jazyk. Pokud uživatel upřednostní zobrazíte zobrazení textu v jiném jazyce, nastavte `setLang` parametr příslušný jazyk kódu dotazu.

Alternativně můžete zadat pomocí země `cc` parametr dotazu. Pokud zadáte jiné zemi, musíte zadat také jeden nebo více kódů pomocí `Accept-Language` hlavičky protokolu HTTP. Podporované jazyky se liší podle země; jsou uvedené pro každou zemi v tabulce trhů.

> [!NOTE]
> Rozhraní API bitové kopie trendů aktuálně podporuje pouze následující trhy:
> - en US (angličtina, USA) 
> - en-CA (angličtina, Kanada) 
> - en-AU (angličtina, Austrálie) 
> - zh-CN (čínština, Čína)

## <a name="countries"></a>Jednotlivé země

|Země|Kód|
|-------|----|
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
|Hongkong|(HONG KONG)|
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
|Čína|CN|
|Polsko|PL|
|Portugalsko|PT|
|Filipíny|PARAMETR|
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


## <a name="markets"></a>Trzích

|Země|Jazyk|Trhu kódu|
|-------|--------|-----------|
|Argentina|Španělština|ES-AR|
|Austrálie|Angličtina|en-AU|
|Rakousko|Němčina|de-AT|
|Belgie|Holandština|nl-BE|
|Belgie|Francouzština|FR-být|
|Brazílie|Portugalština|pt-BR|
|Kanada|Angličtina|en-CA|
|Kanada|Francouzština|fr-CA|
|Chile|Španělština|ES-CL|
|Dánsko|dánština|da-DK|
|Finsko|Finština|fi-FI|
|Francie|Francouzština|fr-FR|
|Německo|Němčina|de-DE|
|Hongkong|Tradiční čínština|zh-HK|
|Indie|Angličtina|en-IN|
|Indonésie|Angličtina|en-ID|
|Itálie|italština|IT-IT|
|Japonsko|Japonština|ja-JP|
|Korea|Korejština|ko-KR|
|Malajsie|Angličtina|en Moje|
|Mexiko|Španělština|es-MX|
|Nizozemsko|Holandština|NL-NL|
|Nový Zéland|Angličtina|en-NZ|
|Čína|Čínština|zh-CN|
|Polsko|polština|pl-PL|
|Portugalsko|Portugalština|pt-PT|
|Filipíny|Angličtina|en parametr|
|Rusko|ruština|ru-RU|
|Saúdská Arábie|arabština|ar (SA)|
|Jihoafrická republika|Angličtina|en-ZA|
|Španělsko|Španělština|ES-ES|
|Švédsko|švédština|sv-SE|
|Švýcarsko|Francouzština|FR-CH|
|Švýcarsko|Němčina|de-CH|
|Tchaj-wan|Tradiční čínština|zh-TW|
|Turecko|turečtina|tr-TR|
|Spojené království|Angličtina|en-GB|
|Spojené státy|Angličtina|cs-CZ|
|Spojené státy|Španělština|ES USA|

## <a name="next-steps"></a>Další postup
Další informace o hledání zprávy Bing koncových bodů najdete v tématu [odkazu v7 API Search bitové kopie zprávy](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).
