---
title: Časté otázky – rozhraní API pro rozpoznávání tváře
titlesuffix: Azure Cognitive Services
description: Tady jsou odpovědi na oblíbené otázky týkající se služby rozhraní API pro rozpoznávání tváře.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 47ce80e1b0cefc01752d2445b751ebe1c2d65d08
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351324"
---
# <a name="face-api-frequently-asked-questions"></a>Rozhraní API pro rozpoznávání tváře – nejčastější dotazy

> [!TIP]
> Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, zkuste se zeptat v komunitě API pro rozpoznávání tváře na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) nebo se obraťte na Nápověda a podpora na [UserVoice](https://cognitive.uservoice.com/).

-----
**Dotaz**: Které faktory redukovala přesnost rozhraní Face API pro rozpoznávání, ověření nebo Najít podobné?

**Odpověď**: Obecně je stejné případy, kde lidé mít problémy s identifikace někdo včetně:
* Blokování jednoho nebo obou oči překážky
* Nepříznivých osvětlení (například závažnost spodního osvětlení)
* Změny stylu nebo facial vlasů vlasy
* Změny kvůli stáří
* Extrémní výrazu tváře (například screaming)

Rozhraní API pro rozpoznávání tváře je často úspěšné náročné případech je uveden výše, ale je možné snížit přesnost. Ujistěte se, rozpoznávání robustnější a řeší tyto problémy, trénování vaší osoby s fotky, které patří rozmanitost úhly a osvětlení.

-----
**Dotaz**:  Trávím obrázek binární data v ale se zobrazí chyba "neplatný pro rozpoznávání tváře image".

**Odpověď**:  Tato chyba znamená, že algoritmus narazili na problém při analýze bitovou kopii. Mezi příčiny patří:
* Formáty podporované vstupního obrázku obsahuje JPEG, PNG, BMP, GIF (první snímek).
* Velikost souboru obrázku by neměly být větší než 4 MB
* Rozsah velikosti zjistitelné pro rozpoznávání tváře je 36 x 36 do 4096 × 4096 pixelů. Nerozpozná tváří mimo tento rozsah.
* Z důvodu technické komplikace, třeba velký pro rozpoznávání tváře úhlů (hlavní póza), velké uzavření nemusí být detekována některé tváří. Přední a téměř čelní strany mají nejlepší výsledky

-----
