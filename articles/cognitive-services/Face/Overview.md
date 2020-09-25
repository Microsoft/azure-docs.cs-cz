---
title: Co je služba Azure Face?
titleSuffix: Azure Cognitive Services
description: Služba Azure Face poskytuje algoritmy AI, které používáte ke zjišťování, rozpoznávání a analýze lidských ploch na obrázcích.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 9/17/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: rozpoznávání obličeje, software pro rozpoznávání obličeje, analýza obličeje, shoda obličeje, aplikace pro rozpoznávání obličeje, hledání na základě obrázku, hledání ve formátu obličeje
ms.openlocfilehash: 0a7e242add9fdaa9e169a4003e8ad8f39b1fb111
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91262480"
---
# <a name="what-is-the-azure-face-service"></a>Co je služba Azure Face?

> [!WARNING]
> 11. června 2020 Microsoft oznámil, že nebude prodávat technologii rozpoznávání tváří policejním oddělením v USA, dokud nebude zavedena přísná regulace založená na lidských právech. Zákazníci tak nebudou moci používat funkce rozpoznávání obličeje nebo funkce, které jsou součástí služeb Azure, jako je například obličej nebo Video Indexer, pokud je zákazník nebo je povolen používání těchto služeb v rámci nebo pro, policejní oddělení v USA.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Služba Azure Face poskytuje algoritmy AI, které zjišťují, rozpoznávají a analyzují lidské obličeje na obrázcích. Software pro rozpoznávání obličeje je důležitý v mnoha různých scénářích, jako je zabezpečení, přirozené uživatelské rozhraní, analýza obsahu a správa, mobilní aplikace a roboty.

Služba Face nabízí několik různých funkcí analýzy obličeje, které jsou uvedeny v následujících částech.

## <a name="face-detection"></a>Rozpoznávání tváře

Služba Face detekuje lidské obličeje v obrázku a vrátí souřadnice obdélníku jejich umístění. V případě potřeby může rozpoznávání tváře extrahovat řadu atributů souvisejících s obličejem, jako je například pozice, pohlaví, věk, emoce, obličeje a brýle.

> [!NOTE]
> Funkce detekce tváře je dostupná taky prostřednictvím [služby počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Pokud ale chcete provádět další operace s předními daty, měli byste místo toho použít tuto službu.

![Obrázek žena a člověku s obdélníky nakreslenými kolem plošek a stáří a zobrazených pohlaví](./Images/Face.detection.jpg)

Další informace o detekci obličeje najdete v článku koncepty [rozpoznávání obličeje](concepts/face-detection.md) . Viz také referenční dokumentace k [rozhraní API pro detekci](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

## <a name="face-verification"></a>Ověření tváře

Rozhraní API pro ověření provádí ověřování proti dvěma zjištěným ploškám nebo z jedné zjištěné plochy na jeden objekt Person. Prakticky vyhodnotí, jestli dvě tváře patří stejné osobě. Tato funkce je potenciálně užitečná ve scénářích zabezpečení. Další informace najdete v příručce věnovaném koncepcím [rozpoznávání obličeje](concepts/face-recognition.md) nebo v dokumentaci k [ověření rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) .

## <a name="find-similar-faces"></a>Vyhledání podobných tváří

Rozhraní API najít podobné se shoduje mezi cílovou ploškou a sadou kandidátních plošek a hledáním menší sady tváře, které vypadají podobně jako cílový obličej. To je užitečné při hledání na základě obrázku. 

Podporují se dva pracovní režimy **matchPerson** a **matchFace**. Režim **matchPerson** vrací podobné obličeje po filtrování pro stejnou osobu pomocí [rozhraní API pro ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Režim **matchFace** ignoruje filtr stejné osoby. Vrátí seznam podobných kandidátních plošek, které mohou nebo nemusí patřit stejné osobě.

Následující příklad ukazuje cílovou plochu:

![Usmívající se žena](./Images/FaceFindSimilar.QueryFace.jpg)

A tyto obrázky jsou kandidátské obličeje:

![Pět obrázků usmívajících se lidí. Obrázky a a b zobrazují stejnou osobu.](./Images/FaceFindSimilar.Candidates.jpg)

Chcete-li najít čtyři podobné plošky, vrátí režim **matchPerson** a a b, který zobrazí stejnou osobu jako cílovou plochu. Režim **matchFace** vrací přesně čtyři kandidáty a, b, c a d &mdash; , a to i v případě, že někteří nejsou stejná osoba jako cíl nebo mají nízkou podobnost. Další informace najdete v příručce věnovaném koncepcím [rozpoznávání obličeje](concepts/face-recognition.md) nebo v referenční dokumentaci k funkci [Najít podobné rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) .

## <a name="face-grouping"></a>Seskupování tváří

Rozhraní API pro seskupování rozdělí sadu neznámých tváří do několika skupin podle podobnosti. Každá skupina je vlastní disjunktní podmnožina původní sady tváří. Všechny plošky ve skupině budou nejspíš patřit stejné osobě. Jedna osoba může mít několik různých skupin. Skupiny jsou odlišeny jiným faktorem, například výraz. Další informace najdete v příručce věnovaném koncepcím [rozpoznávání obličeje](concepts/face-recognition.md) nebo v referenční dokumentaci k [rozhraní API skupiny](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) .

## <a name="person-identification"></a>Identifikace osob

Identifikovatelné rozhraní API se používá k identifikaci zjištěné plochy proti databázi lidí (vyhledávání v rozpoznávání obličeje). Tato funkce může být užitečná pro automatické označování obrázků v softwaru pro správu fotek. Databázi vytvoříte předem a můžete ji upravovat v průběhu času.

Následující obrázek ukazuje příklad databáze s názvem `"myfriends"` . Každá skupina může obsahovat až 1 000 000 různých objektů Person. Každý objekt osob může registrovat až 248 tváří.

![Mřížka se třemi sloupci pro různé lidi, z nichž každý má tři řádky imagí obličeje](./Images/person.group.clare.jpg)

Po vytvoření a školení databáze můžete pro skupinu provést identifikaci s novou zjištěnou ploškou. Pokud se tvář identifikuje jako jedna z osob ve skupině, pak se tento objekt osob vrátí.

Další informace o identifikaci osob najdete v příručce věnovaném koncepcím [rozpoznávání obličeje](concepts/face-recognition.md) nebo v dokumentaci k [identifikaci rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) .

## <a name="containers"></a>Containers

[Pomocí kontejneru obličeje](face-how-to-install-containers.md) můžete detekovat, rozpoznávat a identifikovat plošky tím, že si nainstalujete standardizovaný kontejner Docker blíž k vašim datům.

## <a name="sample-apps"></a>Ukázkové aplikace

Následující ukázkové aplikace ukazují několik způsobů použití služby Face:

- [Face API: Klientská knihovna a ukázka Windows](https://github.com/Microsoft/Cognitive-Face-Windows) je aplikace WPF, která předvádí několik scénářů detekce, analýzy a identifikace obličeje.
- [FamilyNotes aplikace pro UWP](https://github.com/Microsoft/Windows-appsample-familynotes) je aplikace Univerzální platforma Windows (UWP), která používá identifikaci obličeje společně s rozpoznáváním řeči, Cortana, Ink a kamery ve scénáři sdílení poznámky.

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech Cognitive Servicesch prostředků musí vývojáři, kteří používají službu Face, znát zásady Microsoftu o zákaznických datech. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na webu Microsoft Trust Center.

## <a name="next-steps"></a>Další kroky

Postupujte podle pokynů pro rychlý Start a nahlaste základní komponenty aplikace pro rozpoznávání tváře v jazyce podle vašeho výběru.

- [Rychlý Start knihovny klienta](quickstarts/client-libraries.md).
