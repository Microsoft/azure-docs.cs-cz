---
title: Nejčastější dotazy k rozpoznávání emocí úrovně rozhraní API | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy týkající se rozhraní API pro rozpoznávání emocí úrovně v kognitivní služby.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/26/2017
ms.author: anroth
ms.openlocfilehash: 8532d7c00fd8d7b01d84b5e55cb9bbc60241789c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342429"
---
# <a name="emotion-api-frequently-asked-questions"></a>Nejčastější dotazy k rozpoznávání emocí úrovně rozhraní API
 
> [!IMPORTANT]
> 30. října 2017 ukončen video Preview rozhraní API. Vyzkoušet nový [Preview rozhraní API Indexer Video](https://azure.microsoft.com/services/cognitive-services/video-indexer/) k snadno rozbalte statistiky z videa a vylepšení možnosti zjišťování obsahu, jako je například výsledky hledání, pomocí zjišťování mluvené slovo, řezy, znaků a emoce. [Další informace](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Pokud nenajdete odpovědi na otázky v tohoto článku, zkuste žádostí na rozhraní API pro rozpoznávání emocí úrovně komunity [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) nebo kontaktujte nápovědu a podporu na [UserVoice](https://cognitive.uservoice.com/).  

-----

**Otázka**: *jaké typy obrázků získat nejlepší výsledky z rozhraní API pro rozpoznávání emocí úrovně?*

**Odpověď**: používat bez překážek, úplná čelní obličeje Image pro dosažení co nejlepších výsledků. Spolehlivost snižuje s částečné čelní řezy a rozhraní API pro rozpoznávání emocí úrovně nemusí rozpoznat emoce v obrázcích, kde je písmo otočený 45 stupňů nebo více.

-----

**Otázka**: *kolik emoce rozhraní API pro rozpoznávání emocí úrovně poznáte?*

**Odpověď**: rozhraní API pro rozpoznávání emocí úrovně rozpozná osmi různých emoce všeobecně přijato: 
* Štěstí
* Smutek
* Překvapení
* Hněv
* Strach
* Opovržení
* Odpor 
* Neutrální 

-----

**Otázka**: *nějak živý datový proud videa předat do rozhraní API a získat výsledek současně?*

**Odpověď**: používat na základě obrázku rozpoznávání emocí úrovně rozhraní API a volání na každý snímek nebo přejděte rámce pro výkon.  Video Analysis po jednotlivých – ukázky jsou k dispozici.

-----

**Otázka**: *I mě předávání dat binární bitové kopie v ale nabízí mi: "Neplatný vzhled obrázku.**

**Odpověď**: to znamená, že algoritmus měl problém analýza bitovou kopii.  
* Formáty podporované vstupní image obsahuje JPEG, PNG, BMP, GIF (první snímek). 
* Velikost souboru obrázku musí být větší než 4 MB volného místa
* Rozsah velikost rozpoznat řez je 36 x 36 do 4096 x 4096 pixelů. Řezy mimo tento rozsah nebudou zjištěna.
* Z důvodu technické problémy, například velké vzhled úhly (head pozice), nemusí být detekována některé řezy velké NF pásmová. Čelní a téměř čelní strany mají nejlepších výsledků

-----
