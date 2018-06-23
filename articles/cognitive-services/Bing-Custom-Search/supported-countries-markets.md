---
title: Podporované zemích a jazyky pro rozhraní API služby Bing vlastní Search v Azure | Microsoft Docs
description: Zjistěte, jaké zemích a jazyky jsou podporovány pomocí rozhraní API služby Bing vlastní Search.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/19/2017
ms.author: v-gedod
ms.openlocfilehash: 7ff309f9b789662c4ebd791dffaa2bc2e440763e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342867"
---
# <a name="bing-custom-search-countries-and-languages"></a>Hledání vlastní Bing zemích a jazyky

Rozhraní API služby Bing vlastní Search podporuje více než tří tucet zemích, mnoho s více než jeden jazyk. 

Přestože je volitelné, by měl určovat požadavek [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#mkt) parametr dotazu, který identifikuje na trhu, kam chcete výsledky pocházet z. Seznam parametrů dotazu volitelné, najdete v tématu [parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)

Můžete zadat země pomocí `cc` parametr dotazu. Pokud zadáte jiné zemi, musíte zadat také jeden nebo více kódů pomocí `Accept-Language` záhlaví. Podporované jazyky se liší podle země; jsou uvedené pro každou zemi v **trhů** tabulky.

`Accept-Language` Záhlaví a `setLang` parametr dotazu se vzájemně vylučují – nezadávejte i. Podrobnosti najdete v tématu [Accept-Language](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#acceptlanguage).

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
