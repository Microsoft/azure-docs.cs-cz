---
title: Glosář – služba API pro rozpoznávání tváře
titleSuffix: Azure Cognitive Services
description: Tento glosář vysvětluje termíny, na které můžete při práci se službou rozhraní API pro rozpoznávání tváře narazit.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: bd4285a2d0f882b8c766563f2304031f1f6e2898
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787934"
---
# <a name="glossary"></a>Glosář

## <a name="a"></a>A

#### <a name="attributes"></a>Atributy

Rozpoznávání tváře volitelné funkce, které lze zjistit, jako jsou atributy [stáří](#age-attribute), [pohlaví](#gender-attribute), [hlavní póza](#head-pose-attribute), [vousy](#facial-hair-attribute)a [usmívejte](#smile-attribute). Je možné je získat z rozhraní API pro detekci tak, že zadáte _returnFaceAttributes_ parametr dotazu.

Úplný seznam atributů pro rozpoznávání tváře najdete v referenční dokumentaci: [Rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="age-attribute"></a>Stáří (atribut)

Stáří je jedním z [atributy](#attributes) , který popisuje stáří konkrétní tváře. Stáří atribut je volitelný ve výsledcích zjišťování a lze ovládat pomocí požadavku zjišťování tak, že zadáte parametr returnFaceAttributes.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="c"></a>C

#### <a name="candidate"></a>Release Candidate

Kandidáty jsou v podstatě [identifikace](#identification) výsledky (např. zjištěné osob a úrovní spolehlivosti v detekce). Je reprezentován kandidát [PersonID](#person-id) a [spolehlivosti](#confidence), označující, že osoba s vysokou úrovní spolehlivosti rozpoznán.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="confidence"></a>Spolehlivost

Pravděpodobnost je měření odhalení podobnosti mezi [tváří](#face) nebo [osoba](#person) v číselné hodnoty – který se používá v [identifikace](#identification), a [ ověření](#verification) udávajících podobnosti hledaný, identifikován a ověřené výsledky.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – vyhledání podobných](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [pro rozpoznávání tváře – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Detekce a rozpoznávání

Rozpoznávání tváří, je proces vyhledání tváří na obrázcích. Uživatelům můžete nahrát obrázek nebo zadejte adresu URL obrázku v požadavku. Zjištěné tváří jsou vráceny pomocí [face ID](#face-id) označující jedinečné identity v rozhraní API pro rozpoznávání tváře. Obdélníků označení umístění pro rozpoznávání tváře v obrázku v pixelech, stejně jako volitelný [atributy](#attributes) pro každou tvář, jako [stáří](#age-attribute), [pohlaví](#gender-attribute), [hlavní pozice ](#head-pose-attribute), [vousy](#facial-hair-attribute) a [usmívající se](#smile-attribute).

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

#### <a name="emotion-attribute"></a>Rozpoznávání emocí (atribut)

Rozpoznávání emocí je jedním z [atributy pro rozpoznávání tváře](#attributes). Při dotazu vrátí seznam hodnot emoce a svoji sebedůvěru zjišťování pro daný typ písma. Skóre spolehlivosti jsou normalizovány: skóre mezi všechny emoce přidá k jedné. Emoce, vrátí se štěstí, smutek, neutrální, hněv, opovržení, znechucení, překvapením a zaregistrují.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="f"></a>F

#### <a name="face"></a>Tvář

Rozpoznávání tváře je jednotný termín pro výsledky odvozen z rozhraní API pro rozpoznávání tváře související s zjištěné tváří. Nakonec pro rozpoznávání tváře je reprezentována jednotné identity ([Face ID](#face-id)), určité oblasti do bitové kopie ([pro rozpoznávání tváře obdélník](#face-rectangle)) a rozpoznávání tváře velmi související s atributy, jako [stáří](#age-attribute), [pohlaví](#gender-attribute), orientačních bodů a [hlavní póza](#head-pose-attribute). Kromě toho mohou být vráceny tváří z detekce.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-api"></a>Rozhraní API pro rozpoznávání tváře

Rozhraní API pro rozpoznávání tváře je rozhraní API založené na cloudu, které poskytuje špičkové algoritmy pro rozpoznávání tváře a uznání. Hlavní funkce rozhraní API pro rozpoznávání tváře je možné rozdělit do dvou kategorií: rozpoznávání s atributy a rozpoznávání tváře [rozpoznávání](#recognition).

Další informace naleznete v referenční dokumentaci: [Přehled rozhraní API pro rozpoznávání tváře](./Overview.md), [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [pro rozpoznávání tváře – vyhledání podobných](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [pro rozpoznávání tváře – seskupit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [ Rozpoznávání tváře – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="face-attributesfacial-attributes"></a>Atributy/Facial atributy tváří

Podrobnosti najdete na [atributy](#attributes).

#### <a name="face-id"></a>ID tváře

Face ID je odvozena z výsledků zjišťování, ve kterých představuje [pro rozpoznávání tváře](#face) v [API pro rozpoznávání tváře](#face-api).

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-landmarksfacial-landmarks"></a>Zajímavá památek/Facial pro rozpoznávání tváře

Památek jsou volitelné ve výsledcích zjišťování; sémantické tváře bodů, například oči, nos a přidržte (znázorněný na následujícím obrázku), které jsou. Zajímavá mohou být požadavku zjišťování řízena logická returnFaceLandmarks čísla. Pokud returnFaceLandmarks je nastavená na hodnotu true, bude mít vrácené tváří památek atributy.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![Jak se rozpoznává tvář](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Rámeček pro rozpoznávání tváře

Rozpoznávání tváře obdélníku je odvozen z výsledky vyhledávání se svislé obdélníku, (vlevo, horní, šířka, výška) v obrázku v pixelech. Levého horního rohu [pro rozpoznávání tváře](#face) (levý, horní), kromě šířku a výšku, určuje velikost pro rozpoznávání tváře ve x a y OS v uvedeném pořadí.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facial-hair-attribute"></a>Vousy (atribut)

Vousy je jedním z [atributy](#attributes) používají k popisu vousy délka k dispozici tváří. Vousy atribut je volitelný ve výsledcích zjišťování a mohou být s žádostí o detekci řízena returnFaceAttributes. Obsahuje-li returnFaceAttributes "facialHair", bude mít vrácené tváří vousy atributy.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facelist"></a>FaceList

FaceList je kolekce [PersistedFace](#persistedface) a je jednotka [Najít podobné](#find-similar). FaceList dodává se [FaceList ID](#facelist-id), stejně jako ostatní atributy jako jsou název a uživatelská Data.

Další informace naleznete v referenční dokumentaci: [FaceList - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="facelist-id"></a>FaceList ID

FaceList ID je použít jako identifikátor z poskytnutého uživatelem řetězec [FaceList](#facelist). FaceList ID musí být jedinečné v rámci předplatného.

Další informace naleznete v referenční dokumentaci: [FaceList - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="find-similar"></a>Vyhledání podobných

Toto rozhraní API se používá k hledání nebo dotaz podobných tváří na základě kolekce tváří. Dotaz tváří a rozpoznávání tváře kolekce jsou reprezentovány ve formě [face ID](#face-id) nebo [FceList ID](#facelist-id)/[LargeFaceList ID](#largefacelist-id) v požadavku. Vrátí prohledávaném podobných tváří reprezentována jsou výsledky [face ID](#face-id) nebo PersistedFace identifikátory.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – vyhledání podobných](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="gender-attribute"></a>Pohlaví (atribut)

Pohlaví je jedním z [atributy](#attributes) používají k popisu pohlaví dostupné tváří. Pohlaví atribut je volitelný ve výsledcích zjišťování a mohou být s žádostí o detekci řízena returnFaceAttributes. Obsahuje-li returnfaceAttributes "pohlaví", vrácený tváří bude mít atributy pohlaví.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="grouping"></a>Seskupování

Tváří nebo seskupení je seskupení v kolekci tváří podle obličeje podobnosti. Rozpoznávání tváře kolekce jsou označeny kolekce face ID v požadavku. V důsledku seskupování podobných tváří jsou seskupeny jako [skupiny](#groups), a jako skupinu neuspořádaných jsou sloučeny tváří, které nejsou podobně jako ostatní rozpoznávání tváře. Existuje nanejvýš, [neuspořádaných skupiny](#messy-group) ve výsledku seskupení.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – seskupit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="groups"></a>Skupiny

Skupiny jsou odvozeny z [seskupení](#grouping) výsledky. Každá skupina obsahuje kolekci podobných tváří, kde jsou označeny tváří [face ID](#face-id).

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – seskupit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>Hlavní představovat (atribut)

Pozice HEAD je jedním z [atributy](#attributes) , představuje pro rozpoznávání tváře orientace v 3D prostoru podle vrácení, výšku a úhlu natočení úhly, jak je znázorněno na následujícím obrázku. Rozsahy hodnot z vrátit a yaw jsou [-180, 180] a [-90, 90] ve stupních. V aktuální verzi rozteč hodnota vrácená z detekce je vždy 0. Hlavní póza atribut je volitelný ve výsledcích zjišťování a lze ovládat pomocí žádosti o zjištění returnFaceAttributes parametrem. Pokud returnFaceAttributes parametru obsahuje "headPose", vrácený tváří bude mít hlavní představovat atributy.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Identifikace

Identifikace je identifikace tváří jeden objekt LargePersonGroup/PersonGroup jeden nebo více.
A [jeden objekt PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup) je kolekce [osoby](#person).
Jsou reprezentovány tváří a LargePersonGroup/jeden objekt PersonGroup [face ID](#face-id) a [LargePersonGroup ID](#largepersongroup-id)/[jeden objekt PersonGroup ID](#persongroup-id) v uvedeném pořadí požadavek.
Jsou identifikované výsledky [kandidáty](#candidate), je znázorněn [osoby](#person) bez obav.
Více tváří na vstupu se považují za samostatně a každou tvář bude mít identifikovaný výsledek.

> [!NOTE]
> LargePersonGroup/jeden objekt PersonGroup by Trénink úspěšně před identifikace. Pokud není vyškolený LargePersonGroup/jeden objekt PersonGroup, nebo na školení [stav](#status-train) nezobrazuje 'bylo dokončeno"(to znamená" systém","se nezdařilo"nebo"časový limit"), odpovědi na požadavek je 400.
> 

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [osoba LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [jeden objekt PersonGroup Osoba – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [jeden objekt PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="isidentical"></a>IsIdentical

IsIdentical je logická pole [ověření](#verification) výsledky, která určuje, jestli dvě tváře patří stejné osobě.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="l"></a>L

#### <a name="landmarks"></a>Zajímavá

Podrobnosti najdete na orientačních bodů pro rozpoznávání tváře.

#### <a name="largefacelist"></a>LargeFaceList

LargeFaceList je kolekce [PersistedFace](#persistedface) a je jednotka [Najít podobné](#find-similar). LargeFaceList dodává se [LargeFaceList ID](#largefacelist-id), stejně jako ostatní atributy jako jsou název a uživatelská Data.

Další informace naleznete v referenční dokumentaci: [LargeFaceList – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList – Face seznamu](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="largefacelist-id"></a>LargeFaceList ID

LargeFaceList ID je použít jako identifikátor z poskytnutého uživatelem řetězec [LargeFaceList](#largefacelist). LargeFaceList ID musí být jedinečné v rámci předplatného.

Další informace naleznete v referenční dokumentaci: [LargeFaceList – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup je kolekce [osoby](#person) a je jednotka [identifikace](#identification). LargePersonGroup dodává se [LargePersonGroup ID](#largepersongroup-id), stejně jako ostatní atributy jako jsou název a uživatelská Data.

Další informace naleznete v referenční dokumentaci: [LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [osoba LargePersonGroup – seznam](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="largepersongroup-id"></a>LargePersonGroup ID

LargePersonGroup ID je použít jako identifikátor z poskytnutého uživatelem řetězec [LargePersonGroup](#largepersongroup). LargePersonGroup ID musí být jedinečné v rámci předplatného.

Další informace naleznete v referenční dokumentaci: [LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="messy-group"></a>Neuspořádaných skupiny

Neuspořádaných skupiny je odvozený od [seskupení](#grouping) výsledků, které obsahuje tváří není podobně jako ostatní rozpoznávání tváře. Každý obličej neuspořádaných skupiny se vyznačuje [face ID](#face-id).

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – seskupit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>Ne

#### <a name="name-person"></a>Název (osoba)

Název je popisný popisný řetězec pro [osoba](#person). Na rozdíl od [ID osoby](#person-id), jména lidí, se dají duplikovat. v rámci skupiny.

Další informace naleznete v referenční dokumentaci: [Vytvořit uživatele LargePersonGroup –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [osoba LargePersonGroup – získání](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [vytvořit jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [získat jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="name-largepersongrouppersongroup"></a>Název (LargePersonGroup/jeden objekt PersonGroup)

Název je také uživatelsky přívětivé popisný řetězec pro [LargePersonGroup](#largepersongroup)/[jeden objekt PersonGroup](#persongroup). Na rozdíl od [LargePersonGroup ID](#largepersongroup-id)/[jeden objekt PersonGroup ID](#persongroup-id), může být duplicitní název LargePersonGroups/objektů Persongroup v rámci předplatného.

Další informace naleznete v referenční dokumentaci: [LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [jeden objekt PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace je datová struktura v rozhraní API pro rozpoznávání tváře. PersistedFace dodává se [PersistedFace ID](#persisted-face-id), stejně jako další atributy, například název a dat uživatele.

Další informace naleznete v referenční dokumentaci: [LargeFaceList – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [osoba LargePersonGroup – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [jeden objekt PersonGroup uživatele – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="persisted-face-id"></a>Trvalé Face ID

Trvalé Face ID je generována při [PersistedFace](#persistedface) byla úspěšně vytvořena. Řetězec se vytvoří pro reprezentaci této tvář [API pro rozpoznávání tváře](#face-api).

Další informace naleznete v referenční dokumentaci: [LargeFaceList – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [osoba LargePersonGroup – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [jeden objekt PersonGroup uživatele – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person"></a>Person (Osoba)

Uživatel je datová struktura, spravované v rozhraní API pro rozpoznávání tváře. Součástí osoba [ID osoby](#person-id), stejně jako ostatní atributy, jako je například název, kolekci [PersistedFace](#persistedface)a dat uživatele.

Další informace naleznete v referenční dokumentaci: [Vytvořit uživatele LargePersonGroup –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [osoba LargePersonGroup – získání](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [vytvořit jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [získat jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="person-id"></a>ID osoby

ID osoby se vygeneruje při [osoba](#person) byla úspěšně vytvořena. Vytvoření řetězec představující tuto osobu v [API pro rozpoznávání tváře](#face-api).

Další informace naleznete v referenční dokumentaci: [Vytvořit uživatele LargePersonGroup –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [osoba LargePersonGroup – získání](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [vytvořit jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [získat jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="persongroup"></a>PersonGroup

Jeden objekt PersonGroup je kolekce [osoby](#person) a je jednotka [identifikace](#identification). Jeden objekt PersonGroup dodává se [jeden objekt PersonGroup ID](#persongroup-id), stejně jako ostatní atributy jako jsou název a uživatelská Data.

Další informace naleznete v referenční dokumentaci: [Jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [jeden objekt PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [jeden objekt PersonGroup uživatele – seznam](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="persongroup-id"></a>Jeden objekt PersonGroup ID

Jeden objekt PersonGroup ID je použít jako identifikátor z poskytnutého uživatelem řetězec [jeden objekt PersonGroup](#persongroup). ID skupiny musí být jedinečný v rámci předplatného.

Další informace naleznete v referenční dokumentaci: [Jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [jeden objekt PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Představovat (atribut)

Podrobnosti najdete na [představovat Head](#head-pose-attribute).

## <a name="r"></a>R

#### <a name="recognition"></a>Rozpoznání

Rozpoznávání, jako je oblast Oblíbené aplikace pro rozpoznávání tváře technologie [Najít podobné](#find-similar), [seskupení](#grouping), [identifikovat](#identification),[ověření dvě čelí stejné, nebo ne ](#verification).

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – vyhledání podobných](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [pro rozpoznávání tváře – seskupit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [pro rozpoznávání tváře – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Obdélník (pro rozpoznávání tváře)

Podrobnosti najdete na [pro rozpoznávání tváře obdélník](#face-rectangle).

## <a name="s"></a>S

#### <a name="similar-face-searching"></a>Vyhledávání podobných tváří

Podrobnosti najdete na [Najít podobné](#find-similar).

#### <a name="smile-attribute"></a>Usmívejte (atribut)

Úsměv je jedním z [atributy](#attributes) používají k popisu úsměv výrazu tváře k dispozici. Úsměv atribut je volitelný ve výsledcích zjišťování a mohou být s žádostí o detekci řízena returnFaceAttributes. Obsahuje-li returnFaceAttributes "usmívejte", bude mít vrácené tváří smajlíka atributy.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="snapshot"></a>Snímek

Snímek je dočasný vzdálené úložiště pro některé typy dat pro rozpoznávání tváře. Funguje jako typ schránky pro kopírování dat z jednoho předplatného do druhého. První uživatel "přebírá" snímek dat ve zdrojovém předplatném, a potom "použijí" je nový datový objekt v cílovém předplatném. 

Další podrobnosti najdete v tématu [Průvodce migrací pro rozpoznávání tváře](./face-api-how-to-topics/how-to-migrate-face-data.md) také [snímku - trvat](/rest/api/cognitiveservices/face/snapshot/take) a [snímku - použít](/rest/api/cognitiveservices/face/snapshot/apply) referenční dokumentace (REST).

#### <a name="status-train"></a>Stav (regrese)

Stav je řetězec, slouží k popisu postup pro školení LargeFaceList/LargePersonGroups/objektů Persongroup, včetně 'notstarted","spuštěna","bylo úspěšné","se nezdařilo".

Další informace naleznete v referenční dokumentaci: [LargeFaceList – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [jeden objekt PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Klíč předplatného

Klíč předplatného je řetězec, který je třeba zadat jako parametr řetězce dotazu k vyvolání libovolného rozhraní API pro rozpoznávání tváře. Klíč předplatného najdete na stránce Moje předplatné po přihlášení na portál služeb Microsoft Cognitive Services. Bude existovat dva klíče přidružené k každé předplatné: jeden primární klíč a jedna sekundární klíč. Jak je možné vyvolat shodně rozhraní API. Potřebujete k lepšímu zabezpečení klíče předplatného a klíče předplatného můžete obnovit kdykoli ze stránky Moje předplatné i.

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Trénování (LargeFaceList/LargePersonGroup/jeden objekt PersonGroup)

Toto rozhraní API slouží k předběžně zpracovat [LargeFaceList](#largefacelist)/[LargePersonGroup](#largepersongroup)/[jeden objekt PersonGroup](#persongroup) zajistit [najít Podobně jako](#find-similar)/[identifikace](#identification) výkonu. Pokud není provozované na školení, nebo [školení stav](#status-train) nezobrazuje bylo dokončeno, identifikace pro tento jeden objekt PersonGroup bude mít za následek selhání.

Další informace naleznete v referenční dokumentaci: [LargeFaceList – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [jeden objekt PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>UserData/User Data

Data uživatele jsou další informace o přidružené [osoba](#person) a [jeden objekt PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup). Uživatelská data je nastavena podle uživatele tak, aby data použít, a mějte na paměti.

Další informace naleznete v referenční dokumentaci: [LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – aktualizace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [osoba LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [aktualizovat uživatele LargePersonGroup –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [Jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [jeden objekt PersonGroup – aktualizace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [vytvořit jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [aktualizovat jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="verification"></a>Ověření

Toto rozhraní API se používá k ověření, jestli dvě tváře jsou stejné, nebo ne. Obě tváří jsou zastoupeny EDT face ID v požadavku. Ověřit výsledky obsahují pole Boolean (isIdentical) stejný označující, pokud je hodnota true a pole čísla ([spolehlivosti](#confidence)) označující úroveň spolehlivosti.

Další informace naleznete v referenční dokumentaci: [Rozpoznávání tváře – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).
