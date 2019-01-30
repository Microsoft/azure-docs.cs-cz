---
title: Časté otázky – rozhraní API pro rozpoznávání Emocí
titlesuffix: Azure Cognitive Services
description: Získejte odpovědi na nejčastější dotazy týkající se rozhraní API pro rozpoznávání Emocí.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: ded91c6de498b130cc26109a70e89955dd70d862
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208980"
---
# <a name="emotion-api-frequently-asked-questions"></a>API pro rozpoznávání emocí – nejčastější dotazy

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání emocí se přestane používat 15. února 2019. Funkce rozpoznávání emocí je teď obecně dostupná v rámci [rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, zkuste se zeptat v komunitě rozhraní API pro rozpoznávání Emocí na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) nebo se obraťte na Nápověda a podpora na [UserVoice](https://cognitive.uservoice.com/).  

-----

**Dotaz**: *Jaké typy imagí získat nejlepší výsledky z rozhraní API pro rozpoznávání Emocí?*

**Odpověď**: Použijte bez překážek, úplné čelní tváře Image pro dosažení co nejlepších výsledků. Zmenší spolehlivost s částečnou čelní tváří a rozhraní API pro rozpoznávání Emocí nemusí rozpoznávání emocí v obrázcích, kde je typ písma otočený o více než 45 stupňů.

-----

**Dotaz**: *Kolik emocí rozpozná rozhraní API pro rozpoznávání Emocí?*

**Odpověď**: API pro rozpoznávání emocí rozpoznává osmi různých všeobecně přijatá emoce:
* Štěstí
* Smutek
* Překvapení
* Hněv
* Strach
* Opovržení
* Odpor
* Neutrální

-----

**Dotaz**: *Existuje nějaký způsob, jak předat proud živého videa do rozhraní API a získat výsledek současně?*

**Odpověď**: Použití image na základě rozhraní API pro rozpoznávání emocí a volat na každém snímku nebo přeskočit snímků pro výkon.  Video analýza snímků snímků ukázky jsou k dispozici.

-----

**Dotaz**: *Trávím obrázek binární data v, ale díky ní: "Neplatný pro rozpoznávání tváře image.**

**Odpověď**: Tato zpráva znamená, že algoritmus narazili na problém při analýze bitovou kopii.  
* Formáty podporované vstupního obrázku jsou ve formátu JPEG, PNG, BMP, GIF (první snímek)
* Velikost souboru obrázku by neměly být větší než 4 MB
* Rozsah velikosti zjistitelné pro rozpoznávání tváře je 36 x 36 do 4096 × 4096 pixelů. Nerozpozná tváří mimo tento rozsah.
* Z důvodu technické komplikace, třeba velký pro rozpoznávání tváře úhlů (hlavní pozice), velké uzavření nemusí být detekována některé tváří. Přední a téměř čelní strany mají nejlepší výsledky

-----
