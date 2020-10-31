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
ms.openlocfilehash: f32c7a97036319dcae610fd0baa2dad48763d8ae
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094927"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Podpora jazyků a oblastí pro rozhraní API pro vizuální vyhledávání Bingu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Rozhraní API pro vizuální vyhledávání Bingu podporuje více než tři desítkové země nebo oblasti, mnoho s více než jedním jazykem. Každý požadavek by měl zahrnovat zemi nebo oblast uživatele a zvolený jazyk. Znalost trhu uživatele pomáhá službě Bing vracet odpovídající výsledky. Pokud nezadáte zemi, oblast a jazyk, Bing se snaží určit zemi, oblast a jazyk uživatele. Vzhledem k tomu, že výsledky mohou obsahovat odkazy na Bing, může správce země nebo oblasti a jazyku poskytovat preferované lokalizované uživatelské prostředí Bingu, pokud uživatel klikne na odkazy Bingu.

Pokud chcete zadat zemi nebo oblast a jazyk, nastavte `mkt` parametr dotazu (na trhu) na kód z tabulky **trhy** níže. Tento trh určuje zemi nebo oblast i jazyk. Pokud uživatel upřednostňuje zobrazení textu v jiném jazyce, nastavte `setLang` parametr dotazu na příslušný kód jazyka.

Případně můžete zadat zemi nebo oblast pomocí `cc` parametru dotazu. Pokud zadáte zemi nebo oblast, musíte zadat také jeden nebo více kódů jazyka pomocí `Accept-Language` hlavičky HTTP. Podporované jazyky se liší podle země nebo oblasti; jsou uvedeny pro každou zemi v tabulce trhy.



> [!NOTE]
> Platí následující omezení trhu:
>
> - Poznámky k rozpoznávání obrázků jsou k dispozici pouze v angličtině.
> - Informace o receptu, nákupu a stránkách, včetně přehledů, jsou k dispozici pouze na trhu en-US.


## <a name="countriesregions"></a>Země nebo oblasti

|Země|Kód|
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
|Hongkong – zvláštní administrativní oblast|HK|
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
|USA|USA|


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
|Hongkong – zvláštní administrativní oblast|Tradiční čínština|zh – HK|
|Indie|Angličtina|en-IN|
|Indonésie|Angličtina|EN-ID|
|Itálie|Italština|it-IT|
|Japonsko|Japonština|ja-JP|
|Jižní Korea|Korejština|ko-KR|
|Malajsie|Angličtina|EN – MY|
|Mexiko|Španělština|ES – MX|
|Nizozemsko|Nizozemština|nl-NL|
|Nový Zéland|Angličtina|EN-NZ|
|Čína|Čínština|zh-CN|
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
|USA|Angličtina|en-US|
|USA|Španělština|ES – US|
