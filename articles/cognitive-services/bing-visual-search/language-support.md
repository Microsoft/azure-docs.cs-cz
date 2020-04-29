---
title: Jazyková podpora – rozhraní API pro vizuální vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Seznam přirozených jazyků, zemí a oblastí, které jsou podporovány rozhraní API pro vizuální vyhledávání Bingu. Rozhraní API pro vizuální vyhledávání Bingu podporuje více než tři desítkové země nebo oblasti, mnoho s více než jedním jazykem.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "68883551"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Podpora jazyků a oblastí pro rozhraní API pro vizuální vyhledávání Bingu

Rozhraní API pro vizuální vyhledávání Bingu podporuje více než tři desítkové země nebo oblasti, mnoho s více než jedním jazykem. Každý požadavek by měl zahrnovat zemi nebo oblast uživatele a zvolený jazyk. Znalost trhu uživatele pomáhá službě Bing vracet odpovídající výsledky. Pokud nezadáte zemi, oblast a jazyk, Bing se snaží určit zemi, oblast a jazyk uživatele. Vzhledem k tomu, že výsledky mohou obsahovat odkazy na Bing, může správce země nebo oblasti a jazyku poskytovat preferované lokalizované uživatelské prostředí Bingu, pokud uživatel klikne na odkazy Bingu.

Pokud chcete zadat zemi nebo oblast a jazyk, nastavte parametr `mkt` dotazu (na trhu) na kód z tabulky **trhy** níže. Tento trh určuje zemi nebo oblast i jazyk. Pokud uživatel upřednostňuje zobrazení textu v jiném jazyce, nastavte `setLang` parametr dotazu na příslušný kód jazyka.

Případně můžete zadat zemi nebo oblast pomocí parametru `cc` dotazu. Pokud zadáte zemi nebo oblast, musíte zadat také jeden nebo více kódů jazyka pomocí hlavičky `Accept-Language` http. Podporované jazyky se liší podle země nebo oblasti; jsou uvedeny pro každou zemi v tabulce trhy.



> [!NOTE]
> Platí následující omezení trhu:
>
> - Poznámky k rozpoznávání obrázků jsou k dispozici pouze v angličtině.
> - Informace o receptu, nákupu a stránkách, včetně přehledů, jsou k dispozici pouze na trhu en-US.


## <a name="countriesregions"></a>Země nebo oblasti

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

|Země|Jazyk|Kód trhu|
|-------|--------|-----------|
|Argentina|Španělština|ES-AR|
|Austrálie|Angličtina|EN-AU|
|Rakousko|Němčina|de-AT|
|Belgie|Nizozemština|NL|
|Belgie|Francouzština|fr – bude|
|Brazílie|Portugalština|pt-BR|
|Kanada|Angličtina|en-CA|
|Kanada|Francouzština|fr – CA|
|Chile|Španělština|ES-CL|
|Dánsko|Dánština|da-DK|
|Finsko|Finština|fi-FI|
|Francie|Francouzština|fr-FR|
|Německo|Němčina|de-DE|
|Hongkong – zvláštní správní oblast|Tradiční čínština|zh – HK|
|Indie|Angličtina|en-IN|
|Indonésie|Angličtina|EN-ID|
|Itálie|Italština|it-IT|
|Japonsko|Japonština|ja-JP|
|Jižní Korea|Korejština|ko-KR|
|Malajsie|Angličtina|EN – MY|
|Mexiko|Španělština|ES – MX|
|Nizozemsko|Nizozemština|nl-NL|
|Nový Zéland|Angličtina|EN-NZ|
|Čína|Chinese|zh-CN|
|Polsko|Polština|pl-PL|
|Portugalsko|Portugalština|pt-PT|
|Filipíny|Angličtina|EN-PH|
|Rusko|Ruština|ru-RU|
|Saúdská Arábie|Arabština|ar-SA|
|Jižní Afrika|Angličtina|EN-ZA|
|Španělsko|Španělština|es-ES|
|Švédsko|Švédština|sv-SE|
|Švýcarsko|Francouzština|fr – CH|
|Švýcarsko|Němčina|de-CH|
|Tchaj-wan|Tradiční čínština|zh-TW|
|Turecko|Turečtina|tr-TR|
|Spojené království|Angličtina|en-GB|
|Spojené státy|Angličtina|cs-CZ|
|Spojené státy|Španělština|ES – US|
