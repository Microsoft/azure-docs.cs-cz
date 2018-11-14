---
title: Co je rozhraní API pro rozpoznávání tváře?
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak pomocí služby Rozpoznávání tváře rozpoznávat a analyzovat tváře na obrázcích.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/29/2018
ms.author: sbowles
ms.openlocfilehash: 3fe9dd713bef509b1972b51cb07d4f942c544544
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914188"
---
# <a name="what-is-the-azure-face-api"></a>Co je rozhraní API Azure pro rozpoznávání tváře?

Rozhraní API Azure pro rozpoznávání tváře je služba Cognitive Services, která poskytuje algoritmy pro detekci, rozpoznávání a analýzu lidských tváří na obrázcích. Schopnost zpracovávat informace o lidských tvářích je důležitá v řadě různých scénářů softwaru, jako jsou zabezpečení, přirozené uživatelské rozhraní, analýza a správa obsahu obrázků, mobilní aplikace a robotika.

Rozhraní API pro rozpoznávání tváře poskytuje několik různých funkcí, které jsou popsané v následujících částech. Pokračujte ve čtení a získejte o nich další informace, abyste mohli určit, jestli vyhovují vašim potřebám.

## <a name="face-detection"></a>Rozpoznávání tváře

Rozhraní API pro rozpoznávání tváře dokáže rozpoznat lidské tváře na obrázku a vrátit souřadnice obdélníků jejich výskytu. Volitelně může detekce obličeje extrahovat řadu atributů souvisejících s obličejem, jako je póza, pohlaví, věk, pozice hlavy, vousy nebo brýle.

![Obrázek ženy a muže s vykreslenými obdélníky kolem jejich tváří a zobrazeným věkem a pohlavím](./Images/Face.detection.jpg)

Funkce detekce obličeje je k dispozici také prostřednictvím [rozhraní API pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), ale pokud chcete s daty o tvářích provádět další operace, měli byste použít rozhraní API pro rozpoznávání tváře (tuto službu). Další informace o detekci obličeje najdete v tématu věnovaném [rozhraní API pro rozpoznávání](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-verification"></a>Ověření tváře

Rozhraní API pro ověřování provádí ověření dvou detekovaných tváří nebo jedné detekované tváře a jednoho objektu osob. Prakticky vyhodnotí, jestli dvě tváře patří stejné osobě. Toto je potenciálně užitečně ve scénářích zabezpečení. Další informace najdete v tématu věnovaném [rozhraní API pro ověřování](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="find-similar-faces"></a>Vyhledání podobných tváří

Rozhraní API pro vyhledání podobných přebírá cílový obličej a sadu kandidátských tváří a vyhledá menší sadu tváří, které se nejvíce podobají cílovému obličeji. Podporují se dva režimy – **matchPerson** a **matchFace**. Režim **matchPerson** vrátí podobné tváře po vyfiltrování stejné osoby (pomocí [rozhraní API pro ověřování](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)). Režim **matchFace** filtr pro stejnou osobu ignoruje a vrátí seznam podobných kandidátských tváří, které můžou, ale nemusí patřit stejné osobě.

V následujícím příkladu je toto cílový obličej:

![Usmívající se žena](./Images/FaceFindSimilar.QueryFace.jpg)

A toto jsou kandidátské tváře:

![Pět obrázků usmívajících se lidí. Obrázky (a) a (b) patří stejné osobě.](./Images/FaceFindSimilar.Candidates.jpg)

Režim **matchPerson** by při vyhledávání čtyř podobných tváří vrátil obrázky (a) a (b), na kterých je stejná osoba s cílovým obličejem. Režim **matchFace** vrátí obrázky (a), (b), (c) a (d) &mdash; přesně čtyři kandidáty, přestože na některých je jiná než cílová osoba a podobnost některých z nich je nízká. Další informace najdete v tématu věnovaném [rozhraní API pro vyhledávání podobných](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="face-grouping"></a>Seskupování tváří

Rozhraní API pro seskupování rozdělí sadu neznámých tváří do několika skupin podle podobnosti. Každá skupina je vlastní disjunktní podmnožina původní sady tváří. Všechny tváře ve skupině pravděpodobně patří stejné osobě, ale pro jednu osobu může existovat několik různých skupin (odlišených podle jiného faktoru, například výrazu). Další informace najdete v tématu věnovaném [rozhraní API pro seskupování](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="person-identification"></a>Identifikace osob

Rozhraní API pro identifikaci je možné použít k identifikaci rozpoznané tváře v databázi lidí. To může být užitečné pro automatické označování obrázků v softwaru pro správu fotografií. Databázi vytvoříte předem a pak ji můžete v průběhu času upravovat.

Následující obrázek znázorňuje příklad databáze myfriends. Každá skupina může obsahovat až 1 000 000 různých objektů osob a každý objekt osob může registrovat až 248 tváří.

![Mřížka se 3 sloupci pro různé osoby, z nichž každý obsahuje 3 řádky obrázků tváří](./Images/person.group.clare.jpg)

Po vytvoření a natrénování databáze můžete proti této skupině provést identifikaci nově rozpoznané tváře. Pokud se tvář identifikuje jako jedna z osob ve skupině, pak se tento objekt osob vrátí.

Další informace o identifikaci osob najdete v tématu věnovaném [rozhraní API pro identifikaci](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="sample-apps"></a>Ukázkové aplikace

Následující ukázkové aplikace ukazují několik způsobů použití rozhraní API pro rozpoznávání tváře.

- [Rozhraní API Microsoftu pro rozpoznávání tváře: Klientská knihovna Windows a ukázka](https://github.com/Microsoft/Cognitive-Face-Windows) – aplikace WPF, která ukazuje několik scénářů detekce, analýzy a identifikace obličeje.
- [Aplikace pro UWP FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes) – aplikace pro Univerzální platformu Windows (UPW), která využívá identifikaci tváří společně s řečí, Cortanou, rukopisem a fotoaparátem ve scénáři sdílení rodinných poznámek.

## <a name="next-steps"></a>Další kroky

Postupujte podle rychlého startu k implementaci jednoduchého scénáře detekce obličeje v kódu.
- [Rychlý start: Rozpoznávání tváří na obrázcích pomocí sady .NET SDK a jazyka C#](quickstarts/csharp.md) (k dispozici jsou i další jazyky)
