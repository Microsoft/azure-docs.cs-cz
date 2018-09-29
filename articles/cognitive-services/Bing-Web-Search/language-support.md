---
title: Podpora jazyků – rozhraní API webové vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Seznam přirozeného jazyka, země a oblasti, které podporují rozhraní API pro vyhledávání zpráv Bingu.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 09/25/2018
ms.author: erhopf
ms.openlocfilehash: c15e1ddd35e625a713ff569f26e9312d9dcd0bc8
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435467"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Podpora jazyka a oblasti pro rozhraní API webové vyhledávání Bingu

Rozhraní API webové vyhledávání Bingu podporuje více než deseti tři zemích nebo oblastech, mnoho s více než jeden jazyk. Zadání země nebo oblasti s dotazem pomáhá zpřesnit výsledky hledání založené na této země nebo oblasti zájmu. Výsledky mohou zahrnovat odkazy na Bingu a těchto odkazů může lokalizace uživatelského rozhraní Bing podle zadanou zemí nebo oblastí nebo jazyk.

Můžete zadat zemi nebo oblast pomocí `cc` parametr dotazu. Pokud je zadána zemi nebo oblast, je nutné zadat jeden nebo více kódů jazyka s [ `Accept-Language` záhlaví](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers). Použití [trhy tabulky](#Markets) seznam jazyků podporovaných v trhy.

Alternativně můžete zadat na trh `mkt` parametr dotazu a kód z **trhy** tabulky. Zadání na trhu současně Určuje zemi nebo oblasti a upřednostňovaný jazyk. Můžete explicitně nastavit jazyk s `setLang` parametr dotazu.

## <a name="countriesregions"></a>Země/oblasti

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
|Norsko|norština|no-NO|
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
