---
title: Jazyková podpora – rozhraní API pro vyhledávání obrázků bingem
titleSuffix: Azure Cognitive Services
description: Zjistěte, které země nebo oblasti a jazyky jsou podporovány rozhraním API pro vyhledávání obrázků Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: ca3821b6088e45730334d1b0971e270b1d86dfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881926"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Jazyková a oblastová podpora rozhraní API pro vyhledávání obrázků Bingu

Rozhraní API pro vyhledávání obrázků Bingu podporuje více než tři desítky zemí nebo oblastí, mnoho z nich s více než jedním jazykem. Určení země nebo oblasti s dotazem slouží především k upřesnění výsledků hledání na základě zájmů v dané zemi nebo oblasti. Kromě toho výsledky mohou obsahovat odkazy na Bing a tyto odkazy mohou lokalizovat uživatelské prostředí Bing podle zadané země nebo oblasti nebo jazyk.

Chcete-li určit zemi nebo oblast `mkt` a jazyk, nastavte parametr (tržní) dotaz na kód z níže uvedené tabulky **Trhy.** Trh specifikuje jak zemi nebo oblast, tak jazyk. Pokud uživatel upřednostňuje zobrazení textu v jiném jazyce, nastavte `setLang` parametr dotazu na příslušný kód jazyka.

Případně můžete zadat zemi nebo oblast `cc` pomocí parametru dotazu. Pokud zadáte zemi nebo oblast, musíte také zadat jeden `Accept-Language` nebo více kódů jazyka pomocí hlavičky HTTP. Podporované jazyky se liší podle země nebo oblasti; jsou uvedeny pro každou zemi nebo oblast v tabulce Trhy.

> [!NOTE]
> Rozhraní API pro populární obrázky v současné době podporuje pouze následující trhy:
> - en-US (Angličtina, Spojené státy americké)
> - cs-CA (angličtina, Kanada)
> - en-AU (anglicky, Austrálie)
> - zh-CN (čínština, Čína)

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
Další informace o koncových bodech vyhledávání zpráv Bingu naleznete v [tématu News Image Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
