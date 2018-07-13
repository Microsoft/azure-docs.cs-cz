---
title: Podporované země/oblasti a jazyky pro API Bingu pro vyhledávání obrázků v Azure | Dokumentace Microsoftu
description: Zjistěte, které země/oblasti a jazyky podporují rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 97e2bed509146172c10aa9ac2658b99ed7610fcc
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004434"
---
# <a name="bing-image-search-countriesregions-and-languages"></a>Vyhledávání obrázků Bingu země/oblasti a jazyky

Rozhraní API Bingu pro vyhledávání obrázků podporuje více než deseti tři zemích nebo oblastech, mnoho s více než jeden jazyk. Zadání země/oblasti s dotazem slouží především pro upřesnění výsledků hledání podle zájmů v zemi/oblast. Kromě toho výsledky mohou obsahovat odkazy na Bingu a těchto odkazů může lokalizace uživatelského rozhraní Bing podle země nebo oblasti zadaného nebo jazyk.

Chcete-li zadat zemi/oblast a jazyk, nastavte `mkt` parametr dotazu (trhu) pro kódování ze **trhy** následující tabulka. Určuje, na trhu země/oblast a jazyk. Pokud uživatel preferuje zobrazíte zobrazení textu v jiném jazyce, nastavte `setLang` parametr kódu příslušný jazyk dotazu.

Alternativně můžete zadat pomocí země/oblast `cc` parametr dotazu. Pokud chcete zadat určitá země nebo oblast, musíte zadat také jeden nebo více kódů jazyka pomocí `Accept-Language` hlavičky protokolu HTTP. Podporované jazyky se liší podle země nebo oblasti; jsou uvedeny pro každou zemi/oblast v tabulce trhy.

> [!NOTE]
> Rozhraní API pro obrázky vzroste zájem o aktuálně podporuje pouze následujících zemích:
> - en US (angličtina, USA) 
> - cs CA (angličtina, Kanada) 
> - cs Austrálie (angličtina, Austrálie) 
> - zh-CN (čínština, Čína)

## <a name="countries"></a>Země

|Země/oblast|Kód|
|-------|----|
|Argentina|AR|
|Austrálie|AU|
|Rakousko|AT|
|Belgie|BÝT|
|Brazílie|BRAZÍLIE|
|Kanada|CA|
|Chile|CL|
|Dánsko|DK|
|Finsko|FI|
|Francie|FR|
|Německo|DE|
|Hongkong|HK|
|Indie|INDIE|
|Indonésie|ID|
|Itálie|IT|
|Japonsko|JP|
|Jižní Korea|KOREA|
|Malajsie|MOJE|
|Mexiko|MX|
|Nizozemsko|NL|
|Nový Zéland|NZ|
|Norsko|NE|
|Čína|CN|
|Polsko|PL|
|Portugalsko|PT|
|Filipíny|PH|
|Rusko|RU|
|Saúdská Arábie|SA|
|Jihoafrická republika|ZA|
|Španělsko|ES|
|Švédsko|SE|
|Švýcarsko|CH|
|Tchaj-wan|TRADIČNÍ ČÍNŠTINA|
|Turecko|TR|
|Spojené království|GB|
|Spojené státy|USA|


## <a name="markets"></a>Trhy

|Země/oblast|Jazyk|Kód na trhu|
|-------|--------|-----------|
|Argentina|Španělština|ES AR|
|Austrálie|Angličtina|cs AU|
|Rakousko|Němčina|de-AT|
|Belgie|Holandština|nl-BE|
|Belgie|Francouzština|FR-být|
|Brazílie|Portugalština|pt-BR|
|Kanada|Angličtina|cs CA|
|Kanada|Francouzština|fr-CA|
|Chile|Španělština|ES-CL|
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
|Čína|Čínština|zh-CN|
|Polsko|polština|pl-PL|
|Portugalsko|Portugalština|pt-PT|
|Filipíny|Angličtina|cs PH|
|Rusko|ruština|ru-RU|
|Saúdská Arábie|arabština|ar-SA|
|Jihoafrická republika|Angličtina|cs ZA|
|Španělsko|Španělština|es-ES|
|Švédsko|švédština|sv-SE|
|Švýcarsko|Francouzština|FR-CH|
|Švýcarsko|Němčina|de-CH|
|Tchaj-wan|Tradiční čínština|zh-TW|
|Turecko|turečtina|tr-TR|
|Spojené království|Angličtina|en-GB|
|Spojené státy|Angličtina|cs-CZ|
|Spojené státy|Španělština|ES US|

## <a name="next-steps"></a>Další postup
Další informace o koncových bodech Bingu pro vyhledávání zpráv najdete v tématu [zpráv API pro vyhledávání obrázků v7 odkaz](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).
