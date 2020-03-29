---
title: Jazyková podpora – rozhraní API pro vizuální vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Seznam přirozených jazyků, zemí a oblastí, které jsou podporovány rozhraním API pro vizuální vyhledávání Bingu. Rozhraní API vizuálního vyhledávání Bingu podporuje více než tři desítky zemí nebo oblastí, mnoho z nich s více než jedním jazykem.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68883551"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Jazyková a oblastová podpora rozhraní API pro vizuální vyhledávání Bingu

Rozhraní API vizuálního vyhledávání Bingu podporuje více než tři desítky zemí nebo oblastí, mnoho z nich s více než jedním jazykem. Každá žádost by měla obsahovat zemi nebo oblast uživatele a jazyk, který si vyberete. Znalost trhu uživatele pomáhá Bing vrátit odpovídající výsledky. Pokud nezadáte zemi nebo oblast a jazyk, bing vynaloží maximální úsilí k určení země nebo jazyka uživatele. Vzhledem k tomu, že výsledky mohou obsahovat odkazy na Bing, znalost země nebo oblasti a jazyka může poskytnout upřednostňované lokalizované uživatelské prostředí Bingu, pokud uživatel klikne na odkazy Bing.

Chcete-li určit zemi nebo oblast `mkt` a jazyk, nastavte parametr (tržní) dotaz na kód z níže uvedené tabulky **Trhy.** Trh specifikuje jak zemi nebo oblast, tak jazyk. Pokud uživatel upřednostňuje zobrazení textu v jiném jazyce, nastavte `setLang` parametr dotazu na příslušný kód jazyka.

Případně můžete zadat zemi nebo oblast `cc` pomocí parametru dotazu. Pokud zadáte zemi nebo oblast, musíte také zadat jeden `Accept-Language` nebo více kódů jazyka pomocí hlavičky HTTP. Podporované jazyky se liší podle země nebo oblasti; jsou uvedeny pro každou zemi v tabulce Trhy.



> [!NOTE]
> Platí tato omezení trhu:
>
> - Poznámky k rozpoznávání obrázků jsou k dispozici pouze v angličtině.
> - Recept, nakupování a stránky-včetně postřehy jsou k dispozici pouze v en-US trhu.


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
