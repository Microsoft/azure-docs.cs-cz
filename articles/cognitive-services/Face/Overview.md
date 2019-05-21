---
title: Co je rozhraní API pro rozpoznávání tváře?
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak pomocí služby Rozpoznávání tváře rozpoznávat a analyzovat tváře na obrázcích.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: c45fd508c14c368c6c9057b9fdeea8df9d8a52c3
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905688"
---
# <a name="what-is-the-azure-face-api"></a>Co je rozhraní API Azure pro rozpoznávání tváře?

Azure Cognitive Services Face API poskytuje algoritmy, které se používají ke zjištění, rozpoznat a analyzovat lidských tváří na obrázcích. Možnost zpracování lidské tváře v trestním řízení je důležité pro mnoho scénářů s jinou softwaru. Ukázkové scénáře jsou zabezpečení, přirozené uživatelské rozhraní, analýzu obsahu bitové kopie a správy, mobilní aplikace a robotika.

Rozhraní API pro rozpoznávání tváře nabízí několik různých funkcí. Každá funkce popsané v následujících částech. Přečtěte si další informace o nich.

## <a name="face-detection"></a>Rozpoznávání tváře

Rozhraní API pro rozpoznávání tváře detekuje lidské tváře v obrázku a vrací obdélník souřadnice jejich umístění. Rozpoznávání tváře můžete volitelně extrahovat řadu atributy vztahující se k rozpoznávání tváře. Příklady jsou hlavní póza, pohlaví, věk, emoce, vousy a brýlí.

> [!NOTE]
> Je také k dispozici prostřednictvím funkce rozpoznávání tváře [rozhraní API pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Pokud chcete další operace s daty pro rozpoznávání tváře, použijte rozhraní API pro rozpoznávání tváře, které je služba popisovaných v tomto článku.

![Obrázek ženy a člověka, s obdélníky vykreslen kolem jejich tváří a stáří a zobrazí gender](./Images/Face.detection.jpg)

Další informace o rozpoznávání tváří, najdete v článku [rozpoznávání tváře](concepts/face-detection.md) koncepty článku. Viz také [zjišťování rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) referenční dokumentaci.

## <a name="face-verification"></a>Ověření tváře

Rozhraní API pro ověřování provádí ověření dvou detekovaných tváří nebo jedné detekované tváře a jednoho objektu osob. Prakticky vyhodnotí, jestli dvě tváře patří stejné osobě. Tato možnost je potenciálně užitečné v situacích, zabezpečení. Další informace najdete v tématu [rozpoznávání tváře](concepts/face-recognition.md) příručka koncepce nebo [ověření rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) referenční dokumentaci.

## <a name="find-similar-faces"></a>Vyhledání podobných tváří

Vyhledání podobných API porovnává cílové tváře sadu Release candidate tváří najít menší sadu protokolovaných tváří, které vypadají podobně jako cíl rozpoznávání tváře. Dva režimy pro práci, matchPerson a matchFace, jsou podporovány. Režim matchPerson vrátí podobných tváří po filtruje pro stejné osobě pomocí [ověření rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Režim matchFace ignoruje filtr tatáž osoba. Vrátí seznam podobných tváří Release candidate, jež mohou nebo nemusí patřit do stejné osobě.

Následující příklad ukazuje cílový typ písma:

![Usmívající se žena](./Images/FaceFindSimilar.QueryFace.jpg)

A toto jsou kandidátské tváře:

![Pět obrázků usmívajících se lidí. Obrázky a b stejné osobě.](./Images/FaceFindSimilar.Candidates.jpg)

Najít čtyři podobných tváří, vrátí hodnotu režimu matchPerson a b, která ukazují, stejná osoba jako cíl rozpoznávání tváře. Vrátí režim matchFace a, b, c a d, přesně čtyři kandidáty, i když některé nejsou stejná osoba jako cíl nebo mají nízké podobnosti. Další informace najdete v tématu [rozpoznávání tváře](concepts/face-recognition.md) příručka koncepce nebo [Najít podobné API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) referenční dokumentaci.

## <a name="face-grouping"></a>Seskupování tváří

Rozhraní API pro seskupování rozdělí sadu neznámých tváří do několika skupin podle podobnosti. Každá skupina je vlastní disjunktní podmnožina původní sady tváří. Všechny tváří ve skupině se pravděpodobně patří stejné osobě. Může existovat několik různých skupin pro jedné osobě. Skupiny jsou rozlišené pomocí dalším faktorem, jako je například výrazu, např. Další informace najdete v tématu [rozpoznávání tváře](concepts/face-recognition.md) příručka koncepce nebo [skupiny rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) referenční dokumentaci.

## <a name="person-identification"></a>Identifikace osob

Rozhraní API pro identifikaci slouží k identifikaci zjištěné rozpoznávání tváře lidí v databázi. Tato funkce může být užitečná pro automatické image značek ve fotografii software pro správu. Předem vytvořit databázi a můžete ho upravit v čase.

Následující obrázek ukazuje příklad databázi s názvem "myfriends." Každá skupina může obsahovat až 1 milión jinou osobu objekty. Každý objekt osob může registrovat až 248 tváří.

![Mřížka s tři sloupce pro různé osoby, každý se třemi řádky obrázků pro rozpoznávání tváře](./Images/person.group.clare.jpg)

Po vytvoření databáze a školení, můžete provést identifikace proti skupině s novou zjištěné rozpoznávání tváře. Pokud se tvář identifikuje jako jedna z osob ve skupině, pak se tento objekt osob vrátí.

Další informace o rozpoznávání osob, najdete v článku [rozpoznávání tváře](concepts/face-recognition.md) příručka koncepce nebo [identifikovat API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) referenční dokumentaci.

## <a name="use-containers"></a>Použití kontejnerů

[Použití kontejneru pro rozpoznávání tváře](face-how-to-install-containers.md) zjistit, rozpoznat a identifikujte tváře nainstalováním standardizované kontejner Dockeru blíž ke svým datům.

## <a name="sample-apps"></a>Ukázkové aplikace

Následující ukázkové aplikace zobrazit několik způsobů, jak použít rozhraní API pro rozpoznávání tváře:

- [Microsoft API pro rozpoznávání tváře: Klientská knihovna pro Windows a ukázka](https://github.com/Microsoft/Cognitive-Face-Windows) je aplikace WPF, který ukazuje několik scénářů detekce, analýza a identifikace tváře.
- [Aplikace pro UPW FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes) je, že používá pro rozpoznávání tváře identifikace spolu s řeči, Cortana, inkoustu a fotoaparát v případě řady Poznámka sdílení aplikace univerzální platformy Windows (UPW).

## <a name="data-privacy-and-security"></a>Ochrany osobních údajů a zabezpečení

Stejně jako všechny prostředky služby Cognitive Services, musí mít přehled o zásady společnosti Microsoft na zákaznická data vývojáře, kteří používají službu pro rozpoznávání tváře. Další informace najdete v tématu [stránku služeb Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Microsoft Trust Center.

## <a name="next-steps"></a>Další postup

Postup rychlého spuštění pro rozpoznávání tváře scénář implementovat v kódu:

- [Rychlé zprovoznění: Rozpoznávání tváří v obrázku pomocí sady .NET SDK s C# ](quickstarts/csharp.md). Další jazyky jsou k dispozici.
