---
title: Co je služba Rozpoznávání tváře?
titleSuffix: Azure Cognitive Services
description: Služba Azure Cognitive Services Face poskytuje algoritmy, které se používají ke zjišťování, rozpoznávání a analýze lidských tváří v obrazech.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 6714a0c4b967d80ad683ef023b5811423bdcb022
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403456"
---
# <a name="what-is-the-azure-face-service"></a>Co je služba Azure Face?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Služba Azure Cognitive Services Face poskytuje algoritmy, které detekují, rozpoznají a analyzují lidské tváře v obrazech. Schopnost zpracovávat informace o lidské tváři je důležitá v mnoha různých softwarových scénářích. Příklady scénářů zahrnují zabezpečení, přirozené uživatelské rozhraní, analýzu a správu obsahu obrázků, mobilní aplikace a robotiku.

Služba Plocha poskytuje několik různých funkcí, z nichž každá je popsána v následujících částech.

## <a name="face-detection"></a>Rozpoznávání tváře

Služba Face detekuje lidské tváře v obraze a vrátí obdélníkové souřadnice jejich umístění. Volitelně může detekce obličeje extrahovat řadu atributů souvisejících s obličejem. Příklady jsou póza hlavy, pohlaví, věk, emoce, vousy a brýle.

> [!NOTE]
> Funkce detekce obličeje je také k dispozici prostřednictvím [rozhraní Api pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Pokud chcete provést další operace s daty obličeje, použijte službu Face, což je služba popisovaná v tomto článku.

![Obrázek ženy a muže s obdélníky nakreslenými kolem jejich tváří a věku a pohlaví zobrazeny](./Images/Face.detection.jpg)

Další informace o detekci obličeje najdete v článku koncepty [rozpoznávání obličejů.](concepts/face-detection.md) Viz také referenční dokumentace [rozhraní API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

## <a name="face-verification"></a>Ověření tváře

Ověřit rozhraní API provádí ověřování proti dvěma zjištěným tvářím nebo z jedné zjištěné tváře na jeden objekt osoby. Prakticky vyhodnotí, jestli dvě tváře patří stejné osobě. Tato funkce je potenciálně užitečná ve scénářích zabezpečení. Další informace naleznete v průvodci [koncepty rozpoznávání tváří](concepts/face-recognition.md) nebo v referenční dokumentaci [ověření rozhraní API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)

## <a name="find-similar-faces"></a>Vyhledání podobných tváří

Najít podobné rozhraní API porovnává cílovou plochu se sadou kandidátských ploch, aby našel menší sadu ploch, které vypadají podobně jako cílová plocha. Dva pracovní režimy, matchPerson a matchFace, jsou podporovány. Režim matchPerson vrátí podobné plochy poté, co filtruje pro stejnou osobu pomocí [ověřit rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Režim matchFace ignoruje filtr stejné osoby. Vrátí seznam podobných kandidátských tváří, které mohou nebo nemusí patřit stejné osobě.

Následující příklad ukazuje cílovou plochu:

![Usmívající se žena](./Images/FaceFindSimilar.QueryFace.jpg)

A tyto obrázky jsou kandidát tváře:

![Pět obrázků usmívajících se lidí. Obrázky a a b ukazují stejnou osobu.](./Images/FaceFindSimilar.Candidates.jpg)

Chcete-li najít čtyři podobné plochy, matchPerson režim vrátí a a b, které ukazují stejnou osobu jako cílová plocha. Režim matchFace vrátí a, b,&mdash;c a d přesně čtyři kandidáty, i když některé nejsou stejné osoby jako cíl nebo mají nízkou podobnost. Další informace naleznete v průvodci [koncepty rozpoznávání tváří](concepts/face-recognition.md) nebo v referenční dokumentaci [najít podobné rozhraní API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)

## <a name="face-grouping"></a>Seskupování tváří

Rozhraní API pro seskupování rozdělí sadu neznámých tváří do několika skupin podle podobnosti. Každá skupina je vlastní disjunktní podmnožina původní sady tváří. Všechny tváře ve skupině pravděpodobně patří stejné osobě. Pro jednu osobu může existovat několik různých skupin. Skupiny jsou rozlišeny jiným faktorem, například výrazem. Další informace naleznete v průvodci [koncepty rozpoznávání tváří](concepts/face-recognition.md) nebo v referenční dokumentaci rozhraní [API skupiny.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)

## <a name="person-identification"></a>Identifikace osob

Rozhraní Identify API se používá k identifikaci zjištěné tváře proti databázi osob. Tato funkce může být užitečná pro automatické označování obrázků v softwaru pro správu fotografií. Databázi vytvoříte předem a můžete ji v průběhu času upravovat.

Následující obrázek znázorňuje příklad `"myfriends"`databáze s názvem . Každá skupina může obsahovat až 1 milion různých objektů osoby. Každý objekt osob může registrovat až 248 tváří.

![Mřížka se třemi sloupci pro různé lidi, každý se třemi řadami obrázků obličeje](./Images/person.group.clare.jpg)

Po vytvoření a trénování databáze můžete provést identifikaci proti skupině s novou zjištěnou plochu. Pokud se tvář identifikuje jako jedna z osob ve skupině, pak se tento objekt osob vrátí.

Další informace o identifikaci osoby najdete v průvodci [koncepty rozpoznávání tváří](concepts/face-recognition.md) nebo v referenční dokumentaci [identify API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="containers"></a>Containers

[Pomocí kontejneru Tváří](face-how-to-install-containers.md) můžete rozpoznat, rozpoznat a identifikovat tváře instalací standardizovaného kontejneru Dockeru blíže k vašim datům.

## <a name="sample-apps"></a>Ukázkové aplikace

Následující ukázkové aplikace ukazují několik způsobů použití služby Face:

- [Rozhraní API pro obličej: Klientská knihovna systému Windows a ukázka](https://github.com/Microsoft/Cognitive-Face-Windows) je aplikace WPF, která demonstruje několik scénářů detekce, analýzy a identifikace obličeje.
- [Aplikace FamilyNotes UpWP](https://github.com/Microsoft/Windows-appsample-familynotes) je aplikace pro univerzální platformu Windows (UPW), která používá identifikaci obličeje spolu s řečí, Cortanou, inkoustem a fotoaparátem ve scénáři sdílení rodinných poznámek.

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení

Stejně jako u všech prostředků služeb Cognitive Services musí vývojáři, kteří používají službu Face, znát zásady společnosti Microsoft týkající se zákaznických dat. Další informace naleznete na [stránce Služby Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v Centru zabezpečení společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

Postupujte podle rychlého startu k implementaci scénáře rozpoznávání tváří v kódu:

- [Úvodní příručka: Detekce tváří v obraze pomocí sady .NET SDK s c#](quickstarts/csharp.md). K dispozici jsou i další jazyky.
