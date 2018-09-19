---
title: Časté otázky – rozhraní API pro rozpoznávání tváře
titlesuffix: Azure Cognitive Services
description: Tady jsou odpovědi na oblíbené otázky týkající se služby rozhraní API pro rozpoznávání tváře.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 9b30fa0fbbd655c03800dadb19cc2568d404204d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129552"
---
# <a name="face-api-frequently-asked-questions"></a>Rozhraní API pro rozpoznávání tváře – nejčastější dotazy

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, zkuste se zeptat v komunitě API pro rozpoznávání tváře na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) nebo se obraťte na Nápověda a podpora na [UserVoice](https://cognitive.uservoice.com/).

-----
**Dotaz**: faktorů může snížit přesnost rozhraní Face API pro rozpoznávání, ověření nebo Najít podobné?

**Odpověď**: obecně je stejné případy, kde lidé mít problémy s identifikace někdo včetně;
* Blokování jednoho nebo obou oči překážky
* Hrubý osvětlení, například závažnost spodního osvětlení
* Změny stylu nebo facial vlasů vlasy
* Změny kvůli stáří
* Extrémní výrazu tváře (třeba screaming)

Rozhraní API pro rozpoznávání tváře je často úspěšné v takových případech náročné, ale je možné snížit přesnost. Ujistěte se, rozpoznávání robustnější a řeší tyto problémy, trénování vaší osoby s fotky, které patří rozmanitost úhly a osvětlení.

-----
**Dotaz**: trávím obrázek binární data v ale se zobrazí chyba "neplatný pro rozpoznávání tváře image".

**Odpověď**: z toho vyplývá, že algoritmus narazili na problém při analýze bitovou kopii. Mezi příčiny patří:
* Formáty podporované vstupního obrázku obsahuje JPEG, PNG, BMP, GIF (první snímek).
* Velikost souboru obrázku by neměly být větší než 4MB
* Rozsah velikosti zjistitelné pro rozpoznávání tváře je 36 x 36 do 4096 × 4096 pixelů. Nerozpozná tváří mimo tento rozsah.
* Z důvodu technické komplikace, třeba velký pro rozpoznávání tváře úhlů (pozice head), nemusí být detekována některé tváří velké uzavření. Přední a téměř čelní strany mají nejlepší výsledky

-----
