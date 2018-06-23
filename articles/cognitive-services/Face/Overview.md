---
title: Přehled rozhraní API služby čelí | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Glosáři vysvětluje pojmy, můžete narazit při práci se službou API řez.
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: bb3ee9431f24a51dfd3877f1c4d621f7ba633b96
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342728"
---
# <a name="what-is-face-api"></a>Co je řez API?

Vítá vás Microsoft vzhled rozhraní API, Cloudová služba, která poskytuje nejmodernější algoritmy vzhled. Vzhled rozhraní API má dvě hlavní funkce: čelí detekce s atributy a čelí rozpoznávání.

## <a name="face-detection"></a>Rozpoznávání tváře

Vzhled rozhraní API zjistí až 64 lidského řezy s vysokou přesnost vzhled umístění v obraze. A bitovou kopii lze zadat pomocí souboru v bajtech nebo platná adresa URL.

![Přehled – vzhled detekce](./Images/Face.detection.jpg)

Vzhled rámečku (vlevo, top, šířku a výšku) označující vzhled umístění na obrázku je vrácen společně s každou zjistil řez. Volitelně můžete vzhled detekce extrahuje řadu související vzhled atributů, jako jsou třeba pozice, pohlaví, stáří, head pozice, obličeje kříž a skla. Další informace najdete v tématu [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Rozpoznávání tváře úrovně

V mnoha případech, včetně zabezpečení, přirozené uživatelské rozhraní, analýzu obsahu bitové kopie a správy, mobilní aplikace a robotics se často používá rozpoznávání tváře na úrovně. Jsou k dispozici čtyři funkce rozpoznávání řez: čelí ověření, podobné řezy hledání, seskupení vzhled a identifikaci osoby.

### <a name="face-verification"></a>Ověření tváře

Vzhled rozhraní API ověření provádí ověřování proti dvěma zjištěné řezy nebo ověřování z jednoho zjištěné vzhled jeden objekt osoby. Podrobnější informace najdete v části [čelí – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-face"></a>Hledání podobně jako vzhled

Zadána cíl zjistil vzhled a sadu řezy kandidáta pro vyhledávání s vyhledá službu malého řezy, které vypadají podobně nejvíce jako cíl písmo. Dva režimy pracovní `matchFace` a `matchPerson` jsou podporovány. `matchPerson` režim vrátí podobné řezy po použití prahová hodnota stejná osoba odvozené od [čelí – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). `matchFace` režim ignoruje stejná osoba prahovou hodnotu a vrátí nejvyšší podobné candidate řezy. Proveďte následující příklad, jsou uvedeny candidate řezy.
![Přehled – vzhled Najít podobné](./Images/FaceFindSimilar.Candidates.jpg) a dotaz čelí, je ![přehled – vzhled Najít podobné](./Images/FaceFindSimilar.QueryFace.jpg)

Najít čtyři podobné řezy, `matchPerson` vrátí režim (a) a (b), který patří do stejné osoba s vzhled dotazu. `matchFace` režim vrátí (a), (b), (c) a (d), přesně čtyři kandidáty to i v případě nízkou podobnosti. Další informace najdete v tématu [vzhled - Najít podobné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Seskupování tváří

Zadána jednu sadu neznámé řezy vzhled seskupování API automaticky rozděluje je do několika skupin podle podobnosti. Každou skupinu je podmnožinu odděleném správnou původní neznámé písmo, nastavit a obsahuje podobné řezy. A všechny řezy ve stejné skupině lze považovat za patřit do stejného objektu osoby. Další informace najdete v tématu [čelí – skupina](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="face-identification"></a>Identifikace tváře

Vzhled rozhraní API slouží k identifikaci osoby podle zjištěného řez a databázi osoby (definován jako LargePersonGroup/PersonGroup). Předem vytvořit tuto databázi, která se dá upravit v čase.

Na následujícím obrázku je příkladem LargePersonGroup/PersonGroup s názvem "myfriends". Každá skupina může obsahovat objekty osoba až 1 000 000/10 000. Každý objekt osoba mezitím, může mít až 248 řezy zaregistrován.

![Přehled – LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

Po LargePersonGroup nebo PersonGroup byla vytvořena a cvičení, identifikace lze provést před skupině a nové zjištěné řez. Pokud řez je identifikován jako objekt osoba ve skupině, je vrácen objekt osoby.

Další informace o identifikaci osoby najdete v následujících příručkách rozhraní API:

[Čelí – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)
[PersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
[PersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
[PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) 
 [LargePersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)
[LargePersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)
[LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)

### <a name="face-storage"></a>Face Storage

Vzhled úložiště umožňuje standardní předplatné ukládá další trvalou řezy při použití LargePersonGroup/PersonGroup osoba objekty ([PersonGroup osoba - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup osoba - Přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) nebo LargeFaceLists/FaceLists ([FaceList - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) pro identifikaci nebo podobnosti odpovídající pomocí rozhraní API řez. Uložené bitové kopie jsou výši 0,5 na 1000 řezy a tato míra je účtovány poměrnou částí každý den. Předplatné úroveň Free je volné, ale omezen na 1000 celkový osob.

Ceny pro úložiště řez je průběžně denně. Například, pokud váš účet používá 10 000 trvalou řezy každý den pro první polovinu měsíce a žádná druhé polovině, by se fakturuje jenom 10 000 řezy dnů uložené. Případně pokud každý den v měsíci zachovat 1000 řezy po několik hodin a odstranit jejich každou noc, by stále se fakturuje 1000 trvalou řezy každý den.

## <a name="getting-started-tutorials"></a>Kurzy Začínáme

Následující kurzy ukazují základní funkce rozhraní API vzhled a odběry procesy:

- [Začínáme s vzhled rozhraní API v kurzu CSharp](Tutorials/FaceAPIinCSharpTutorial.md)
- [Začínáme s vzhled rozhraní API v jazyce Java pro Android kurzu](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Začínáme s vzhled rozhraní API v kurzu Python](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="sample-apps"></a>Ukázkové aplikace

Podívejte se na tyto ukázkové aplikace, které využívají rozhraní API řez.

- [Aplikace pro UPW FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes)
  - Univerzální platformu Windows (UWP) ukázkové aplikace, který ukazuje použití rozpoznávání řeči, Cortana, rukopisu a fotoaparát prostřednictvím rodina Poznámka: sdílení scénář.
- [Ukázkové video rámce analýzy](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Univerzální platformu Windows (UWP) ukázkové aplikace, který ukazuje Analýza provozu datové proudy videa skoro v reálném čase pomocí vzhled, počítač vize a rozhraní API pro rozpoznávání emocí úrovně.

## <a name="related-topics"></a>Související témata

- [Poznámky k verzi vzhled rozhraní API verze 1.0](ReleaseNotes.md)
- [Jak zjistit, kterým čelí v bitové kopii](Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
- [Jak identifikovat tyto řezy v bitové kopii](Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)
