---
title: Podporované zemích a jazyky pro rozhraní API služby Bing webové Search v Azure | Microsoft Docs
description: Zjistěte, jaké zemích a jazyky jsou podporovány pomocí rozhraní API služby Bing webové Search.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 7b62c4a4feb7144662a8fe4d692f11f1efe5db1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342506"
---
# <a name="bing-web-search-countries-and-languages"></a>Hledání webové služby Bing zemích a jazyky

Rozhraní API služby Bing webové Search podporuje více než tří tucet zemích, mnoho s více než jeden jazyk. Určení zemi s dotazem slouží především k zpřesnění výsledků vyhledávání podle zájmů v dané země. Kromě toho výsledky mohou obsahovat odkazy na Bing a tyto odkazy mohou lokalizaci činnost koncového uživatele Bing podle zadaného zemi nebo jazyk.

Můžete zadat země pomocí `cc` parametr dotazu. Pokud zadáte jiné zemi, musíte zadat také jeden nebo více kódů pomocí `Accept-Language` hlavičky protokolu HTTP. Podporované jazyky se liší podle země; jsou uvedené pro každou zemi v tabulce trhů.

Alternativně můžete určit na trhu pomocí `mkt` parametr a kód z dotazů **trhů** tabulky. Určení na trhu současně Určuje zemi a upřednostňovaný jazyk. `setLang` Parametr dotazu může být nastaven na kód jazyka v tomto případě; to je obvykle stejný jazyk, který `mkt` Pokud uživatel upřednostní zobrazíte Bing v jiném jazyce.

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
|Norsko|norština|no-NO|
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
