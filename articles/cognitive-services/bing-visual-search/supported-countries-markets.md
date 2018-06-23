---
title: Podporované zemích a jazyky pro rozhraní API vyhledávání Visual Bing | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Zjistěte, jaké zemích a jazyky jsou podporovány pomocí rozhraní API vyhledávání Visual Bing.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 4723d028cc22caf8be3eb294b52506ec112cbab5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343284"
---
# <a name="bing-visual-search-countries-and-languages"></a>Hledání Visual Bing zemích a jazyky

Bing Visual vyhledávání API podporuje více než tří tucet zemích, mnoho s více než jeden jazyk. Každý požadavek by měla obsahovat země a zvolený jazyk uživatele. Znalost trhu uživatele pomáhá Bing vrátit správné výsledky. Pokud nezadáte a jazyk, díky Bing usilovně k určení země a jazyk uživatele. Protože výsledky mohou obsahovat odkazy na služby Bing, zároveň budete vědět, země a jazyk mohou poskytnout upřednostňované lokalizované Bing činnost koncového uživatele Pokud uživatel kliknutím na odkazy Bing.

Chcete-li zadat země a jazyk, nastavte `mkt` parametr dotazu (trhu) kód z **trhů** následující tabulka. Na trhu Určuje zemi i jazyk. Pokud uživatel upřednostní zobrazíte zobrazení textu v jiném jazyce, nastavte `setLang` parametr příslušný jazyk kódu dotazu.

Alternativně můžete zadat pomocí země `cc` parametr dotazu. Pokud zadáte jiné zemi, musíte zadat také jeden nebo více kódů pomocí `Accept-Language` hlavičky protokolu HTTP. Podporované jazyky se liší podle země; jsou uvedené pro každou zemi v tabulce trhů.



> [!NOTE]
> Platí následující omezení trhu:
> 
> - Obrázek rozpoznávání poznámky jsou pouze k dispozici v angličtině. 
> - Recepturách, nákupních a stránky včetně statistiky jsou dostupné jenom na trhu en US. 


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
