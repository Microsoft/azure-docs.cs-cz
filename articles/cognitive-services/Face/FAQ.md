---
title: Nejčastější dotazy pro službu vzhled rozhraní API | Microsoft Docs
description: Tady najdete odpovědi na nejoblíbenější otázky týkající se rozhraní API služby řez.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: da2f75deef8a8beea3ba23b6a39eb6d2fe104b54
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342423"
---
# <a name="face-api-frequently-asked-questions"></a>Nejčastější dotazy k vzhled rozhraní API
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Pokud nenajdete odpovědi na otázky v tohoto článku, zkuste žádostí API vzhled komunity na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) nebo kontaktujte nápovědu a podporu na [UserVoice](https://cognitive.uservoice.com/).

-----
**Otázka**: faktorů můžete snížit přesnost vzhled API pro rozpoznávání, ověření nebo Najít podobné?

**Odpověď**: obecně je stejné případy, kdy člověka mít potíže při identifikaci někdo včetně;
* Překážky blokování očí jeden nebo oba
* Hrubý osvětlení, například závažné spodního osvětlení
* Změny kříž styl nebo facial kříž
* Změny kvůli stáří
* Extrémně obličeje výrazy (například screaming)

Vzhled rozhraní API je často úspěšné v takových případech náročné, ale může snížit přesnost. Ujistěte se, rozpoznávání robustnější a vyřešit tyto problémy, cvičení vaší osoby se fotografie, které zahrnují různorodost úhly a osvětlení.

-----
**Otázka**: I mě předávání dat binární bitové kopie v ale zobrazí chybu "Neplatná vzhled obrázku".

**Odpověď**: to znamená, že algoritmus měl problém analýza bitovou kopii. Příčiny:
* Formáty podporované vstupní image obsahuje JPEG, PNG, BMP, GIF (první snímek).
* Velikost souboru obrázku musí být větší než 4 MB volného místa
* Rozsah velikost rozpoznat řez je 36 x 36 do 4096 x 4096 pixelů. Řezy mimo tento rozsah nebudou zjištěna.
* Z důvodu technické problémy, například velké vzhled úhly (head pozice), nemusí být detekována některé řezy velké NF pásmová. Čelní a téměř čelní strany mají nejlepších výsledků

-----
