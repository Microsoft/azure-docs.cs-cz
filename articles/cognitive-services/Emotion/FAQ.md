---
title: Časté otázky – rozhraní API pro rozpoznávání Emocí
titlesuffix: Azure Cognitive Services
description: Získejte odpovědi na nejčastější dotazy týkající se rozhraní API pro rozpoznávání Emocí.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 6c1c4b8e5c2701f3c419a58bc3fdc33f7e629bbd
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238512"
---
# <a name="emotion-api-frequently-asked-questions"></a>API pro rozpoznávání emocí – nejčastější dotazy

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání Emocí se přestanou 15. února 2019. Možnosti rozpoznávání emocí z výrazu je teď obecně dostupná jako součást [API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, zkuste se zeptat v komunitě rozhraní API pro rozpoznávání Emocí na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) nebo se obraťte na Nápověda a podpora na [UserVoice](https://cognitive.uservoice.com/).  

-----

**Dotaz**: *typy obrázků Získejte to nejlepší výsledky z rozhraní API pro rozpoznávání Emocí?*

**Odpověď**: použití bez překážek, úplné čelní tváře imagí pro dosažení co nejlepších výsledků. Zmenší spolehlivost s částečnou čelní tváří a rozhraní API pro rozpoznávání Emocí nemusí rozpoznávání emocí v obrázcích, kde je typ písma otočený o více než 45 stupňů.

-----

**Dotaz**: *kolik emoce rozhraní API pro rozpoznávání Emocí rozpozná?*

**Odpověď**: rozhraní API pro rozpoznávání Emocí rozpozná osmi různých všeobecně přijatá emoce:
* Štěstí
* Smutek
* Překvapení
* Hněv
* Strach
* Opovržení
* Odpor
* Neutrální

-----

**Dotaz**: *nějak předat proud živého videa do rozhraní API a získat výsledek současně?*

**Odpověď**: použití bitové kopie na základě rozpoznávání emocí rozhraní API a volat na každém snímku nebo přeskočit snímků pro výkon.  Video analýza snímků snímků ukázky jsou k dispozici.

-----

**Dotaz**: *trávím obrázek binární data v, ale díky ní: "neplatný pro rozpoznávání tváře image.**

**Odpověď**: Tato zpráva znamená, že algoritmus narazili na problém při analýze bitovou kopii.  
* Formáty podporované vstupního obrázku jsou ve formátu JPEG, PNG, BMP, GIF (první snímek)
* Velikost souboru obrázku by neměly být větší než 4 MB
* Rozsah velikosti zjistitelné pro rozpoznávání tváře je 36 x 36 do 4096 × 4096 pixelů. Nerozpozná tváří mimo tento rozsah.
* Z důvodu technické komplikace, třeba velký pro rozpoznávání tváře úhlů (hlavní pozice), velké uzavření nemusí být detekována některé tváří. Přední a téměř čelní strany mají nejlepší výsledky

-----
