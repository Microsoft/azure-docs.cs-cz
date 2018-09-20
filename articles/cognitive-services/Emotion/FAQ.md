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
ms.openlocfilehash: 56b3f2899f1e77c1a2b840285e2c69bdb8987ff4
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363084"
---
# <a name="emotion-api-frequently-asked-questions"></a>API pro rozpoznávání emocí – nejčastější dotazy
 
> [!IMPORTANT]
> Verze Preview rozhraní Video API se ukončí 30. října 2017. Vyzkoušejte novou [verze Preview rozhraní Video Indexer API](https://azure.microsoft.com/services/cognitive-services/video-indexer/) snadno extrahovat přehledy z videí a k vylepšení možností zjišťování obsahu, jako jsou výsledky hledání podle detekce mluveného slova, tváří, charakteristik a emocí. [Další informace](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, zkuste se zeptat v komunitě rozhraní API pro rozpoznávání Emocí na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) nebo se obraťte na Nápověda a podpora na [UserVoice](https://cognitive.uservoice.com/).  

-----

**Dotaz**: *typy obrázků Získejte to nejlepší výsledky z rozhraní API pro rozpoznávání Emocí?*

**Odpověď**: použití bez překážek, úplné čelní tváře imagí pro dosažení co nejlepších výsledků. Zmenší spolehlivost s částečnou čelní tváří a rozhraní API pro rozpoznávání Emocí nemusí rozpoznávání emocí v obrázcích, kde je typ písma otočený 45 stupňů nebo více.

-----

**Dotaz**: *kolik emoce rozhraní API pro rozpoznávání Emocí rozpozná?*

**Odpověď**: rozhraní API pro rozpoznávání Emocí rozpozná osm různé emoce univerzálně přijato: 
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

**Odpověď**: z toho vyplývá, že algoritmus narazili na problém při analýze bitovou kopii.  
* Formáty podporované vstupního obrázku obsahuje JPEG, PNG, BMP, GIF (první snímek). 
* Velikost souboru obrázku by neměly být větší než 4MB
* Rozsah velikosti zjistitelné pro rozpoznávání tváře je 36 x 36 do 4096 × 4096 pixelů. Nerozpozná tváří mimo tento rozsah.
* Z důvodu technické komplikace, třeba velký pro rozpoznávání tváře úhlů (pozice head), nemusí být detekována některé tváří velké uzavření. Přední a téměř čelní strany mají nejlepší výsledky

-----
