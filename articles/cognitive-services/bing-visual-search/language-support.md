---
title: Podpora jazyků – API pro vizuální vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Seznam přirozeného jazyka, země a oblasti, které podporují rozhraní API vizuální vyhledávání Bingu. Rozhraní API vizuální vyhledávání Bingu podporuje více než deseti tři zemích nebo oblastech, mnoho s více než jeden jazyk.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: edfd3ba88da16ff283096af3c5a4929dd06417e2
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435445"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Podpora jazyka a oblasti pro rozhraní API vizuální vyhledávání Bingu

API pro vizuální vyhledávání Bingu podporuje více než deseti tři zemích nebo oblastech, mnoho s více než jeden jazyk. Každý požadavek by měl obsahovat zemi/oblast a jazyk podle vlastní volby uživatele. Znalost trhu uživatele pomáhá Bing vrátí odpovídající výsledky. Pokud nezadáte země/oblast a jazyk, díky Bingu pokusí určit jazyk a zemi/oblast uživatele. Vzhledem k tomu, že výsledky mohou obsahovat odkazy na Bingu, znalost země/oblast a jazyk může poskytnout upřednostňované lokalizované Bingu činnost koncového uživatele Pokud uživatel klikne na Bingu odkazy.

Chcete-li zadat zemi/oblast a jazyk, nastavte `mkt` parametr dotazu (trhu) pro kódování ze **trhy** následující tabulka. Určuje, na trhu země/oblast a jazyk. Pokud uživatel preferuje zobrazíte zobrazení textu v jiném jazyce, nastavte `setLang` parametr kódu příslušný jazyk dotazu.

Alternativně můžete zadat pomocí země/oblast `cc` parametr dotazu. Pokud chcete zadat určitá země nebo oblast, musíte zadat také jeden nebo více kódů jazyka pomocí `Accept-Language` hlavičky protokolu HTTP. Podporované jazyky se liší podle země nebo oblasti; jsou uvedeny pro jednotlivé země v tabulce trhy.



> [!NOTE]
> Platí následující omezení na trhu:
>
> - Poznámky rozpoznávání obrázků jsou pouze k dispozici v angličtině.
> - Recepty, nákupy a přehledů stránek, včetně jsou k dispozici pouze na trhu en US.


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
