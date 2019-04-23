---
title: Podpora jazyků – rozhraní API pro vyhledávání obrázků Bingu
titleSuffix: Azure Cognitive Services
description: Zjistěte, které země/oblasti a jazyky podporují rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: a4c315d2696b081a4f2ac646d097e76ff9562380
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009476"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Podpora jazyka a oblasti pro rozhraní API Bingu pro vyhledávání obrázků

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

|Země/oblast|Jazyk|Kód na trhu|
|-------|--------|-----------|
|Argentina|Španělština|es-AR|
|Austrálie|Angličtina|cs AU|
|Rakousko|Němčina|de-AT|
|Belgie|Holandština|nl-BE|
|Belgie|Francouzština|fr-BE|
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
|Jižní Afrika|Angličtina|en-ZA|
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
Další informace o koncových bodech Bingu pro vyhledávání zpráv najdete v tématu [zpráv API pro vyhledávání obrázků v7 odkaz](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).
