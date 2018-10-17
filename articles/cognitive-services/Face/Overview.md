---
title: Co je služba rozhraní API pro rozpoznávání tváře?
titleSuffix: Azure Cognitive Services
description: Tento glosář vysvětluje termíny, na které můžete při práci se službou rozhraní API pro rozpoznávání tváře narazit.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 15de899be5ab85e9fe84ba1b6284bc9419fcf8a1
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123466"
---
# <a name="what-is-the-face-api-service"></a>Co je služba rozhraní API pro rozpoznávání tváře?

Vítá vás služba rozhraní API pro rozpoznávání tváře – cloudová služba poskytující špičkové algoritmy pro rozpoznávání tváře. Rozhraní API pro rozpoznávání tváře má dvě hlavní funkce: detekce obličeje s atributy a rozpoznávání tváře.

## <a name="face-detection"></a>Detekce obličeje

Rozhraní API pro rozpoznávání tváře dokáže na obrázku velmi přesně detekovat až 64 lidských tváří. Obrázek je možné zadat souborem v bytech nebo platnou adresou URL.

![Přehled – Detekce obličeje](./Images/Face.detection.jpg)

Spolu s každým detekovaným obličejem se vrátí obličejový obdélník označující umístění obličeje na obrázku (vlevo, nahoře, šířka a výška). Volitelně může detekce obličeje extrahovat řadu atributů souvisejících s obličejem, jako je póza, pohlaví, věk, pozice hlavy, vousy nebo brýle. Další informace najdete v průvodci rozhraním API [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Rozpoznávání tváře

Rozpoznávání tváře se běžně používá v mnoha odvětvích včetně zabezpečení, přirozeného uživatelského rozhraní, analýzy a správy obsahu obrázku, mobilních aplikací a robotiky. K dispozici jsou čtyři funkce rozpoznávání tváře: ověření tváře, vyhledání podobných tváří, seskupování tváří a identifikace osob.

### <a name="face-verification"></a>Ověření tváře

Ověřování pomocí rozhraní API pro rozpoznávání tváře se provádí pomocí dvou detekovaných tváří nebo pomocí jedné detekované tváře a jednoho objektu osob. Další informace najdete v průvodci rozhraním API [Face – Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-face"></a>Vyhledání podobných tváří

Pokud službě dáte cílový detekovaný obličej a sadu kandidátských tváří, se kterými může hledat, vybere malou sadu tváří, které se nejvíc podobají cílovému obličeji. Podporují se dva režimy – `matchFace` a `matchPerson`. Režim `matchPerson` vrátí podobné tváře po použití prahové hodnoty pro stejnou osobu odvozené z [ověření obličeje](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Režim `matchFace` prahovou hodnotu pro stejnou osobu ignoruje a vrátí nejvíc podobné kandidátské tváře. V následujícím příkladu jsou uvedené kandidátské tváře.
![Přehled – vyhledání podobné tváře](./Images/FaceFindSimilar.Candidates.jpg) A vyhledávaná tvář je tato ![Přehled – vyhledání podobné tváře](./Images/FaceFindSimilar.QueryFace.jpg)

Režim `matchPerson` by při vyhledávání čtyř podobných tváří vrátil obrázky (a) a (b), které patří stejné osobě s vyhledávanou tváří. Režim `matchFace` vrátí obrázky (a), (b), (c) a (d) – přesně čtyři kandidáty, přestože podobnost je nízká. Další informace najdete v průvodci rozhraním API [Face – Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Seskupování tváří

Pokud rozhraní API pro seskupování tváří poskytnete sadu neznámých tváří, rozhraní je automaticky rozdělí do několika skupin podle podobnosti. Každá skupina je oddělená vhodnou podmnožinou původní neznámé sady tváří a obsahuje podobné tváře. A všechny tváře ve stejné skupině můžou patřit do stejného objektu osob. Další informace najdete v průvodci rozhraním API [Face – Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="face-identification"></a>Identifikace tváře

Rozhraní API pro rozpoznávání tváře můžete použít k identifikaci lidí na základě detekovaného obličeje a databáze lidí (definované jako kolekce LargePersonGroup/PersonGroup). Vytvořte si tuto databázi předem, později ji můžete upravovat.

Na následujícím obrázku je příklad kolekce LargePersonGroup/PersonGroup s názvem „myfriends“. Každá kolekce může obsahovat až 1 000 000/10 000 objektů osob a každý objekt osob může registrovat až 248 tváří.

![Přehled – LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

Po vytvoření a trénování kolekce LargePersonGroup/PersonGroup můžete proti této kolekci provést identifikaci nově detekovaného obličeje. Pokud se tvář identifikuje jako jeden z objektů osob v kolekci, pak se tento objekt osob vrátí.

Další informace o identifikaci osob najdete v následujících průvodcích rozhraní API:

[Face – Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)
[PersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
[PersonGroup Person – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
[PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
[LargePersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)
[LargePersonGroup Person – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)
[LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)

### <a name="face-storage"></a>Úložiště tváří

Úložiště tváří umožňuje předplatnému úrovně Standard při používání objektů Person kolekce LargePersonGroup/PersonGroup ([PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) nebo LargeFaceLists/FaceLists ([FaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) ukládat pomocí rozhraní API pro rozpoznávání tváře další trvalé tváře k identifikaci nebo hledání podobného obličeje. Za 1 000 uložených obrázků tváří se účtuje 0,5 USD. Tato sazba se každý den poměrně přepočítává. Předplatná úrovně Free jsou zdarma, ale jsou omezená celkem na 1 000 osob.

Ceny za Úložiště tváří se každý den poměrně přepočítávají. Pokud váš účet například v první polovině měsíce využil 10 000 uložených trvalých tváří a v druhé polovině měsíce žádné, bude se vám účtovat 10 000 tváří jenom po dobu, po kterou byly uložené. Případně pokud po celý měsíc každý den na několik hodin trvale uložíte 1 000 tváří a potom je vždycky v noci odstraníte, bude se vám stále za každý den účtovat 1 000 trvale uložených tváří.

## <a name="getting-started-tutorials"></a>Kurzy začínáme

Následující kurzy ukazují základní funkce rozhraní API pro rozpoznávání tváře a procesy předplatných:

- [Kurz začínáme s rozhraním API pro rozpoznávání tváře v jazyce CSharp](Tutorials/FaceAPIinCSharpTutorial.md)
- [Kurz začínáme s rozhraním API pro rozpoznávání tváře v Javě pro Android](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Kurz začínáme s rozhraním API pro rozpoznávání tváře v Pythonu](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="sample-apps"></a>Ukázkové aplikace

Podívejte se na tyto ukázkové aplikace, které využívají rozhraní API pro rozpoznávání tváře.

- [Rozhraní API Microsoftu pro rozpoznávání tváře: Klientská knihovna Windows a ukázka](https://github.com/Microsoft/Cognitive-Face-Windows)
  - Ukázková aplikace WPF, která ukazuje několik scénářů detekce, analýzy a identifikace obličeje.
- [Aplikace pro UWP FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes)
  - Ukázková aplikace pro Univerzální platformu Windows (UWP), která ukazuje použití řeči, Cortany, rukopisu a fotoaparátu na příkladu sdílení rodinných poznámek.
- [Ukázka analýzy snímků videa](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Ukázková aplikace pro Univerzální platformu Windows (UWP) ukazující analýzu živě streamovaného videa v téměř reálném čase, která používá rozhraní API pro rozpoznávání tváře, rozpoznávání emocí a počítačové zpracování obrazu.

## <a name="related-topics"></a>Související témata

- [Poznámky k verzi 1.0 rozhraní API pro rozpoznávání tváře](ReleaseNotes.md)
- [Postup rozpoznávání tváří v obrázku](Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
- [Postup identifikace tváří v obrázku](Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)
