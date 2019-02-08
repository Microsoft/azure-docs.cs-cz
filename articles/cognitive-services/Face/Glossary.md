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
ms.openlocfilehash: ad3ea9b0cde16d5c9645e7eacee9ba7f9a08ed2d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859161"
---
# <a name="glossary"></a>Glosář

## <a name="a"></a>A

#### <a name="attributes"></a>Atributy

Atributy, jako jsou volitelné ve výsledcích zjišťování [stáří](#Age-Attribute), [pohlaví](#Gender-Attribute), [hlavní póza](#Head-Pose-Attribute), [vousy](#Facial-Hair-Attribute), [usmívající se ](#Smile-Attribute).
Může být získána z rozhraní API pro detekci tak, že zadáte parametry dotazu: returnFaceAttributes. Atributy poskytují další informace týkající se vybraných [tváří](#Face); kromě [face ID](#Face-ID) a [obdélník](#Face-Rectangle).

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="age-attribute"></a>Stáří (atribut)

Stáří je jedním z [atributy](#Attributes) , který popisuje stáří konkrétní tváře. Stáří atribut je volitelný ve výsledcích zjišťování a lze ovládat pomocí požadavku zjišťování tak, že zadáte parametr returnFaceAttributes.

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="b"></a>B

## <a name="c"></a>C

#### <a name="candidate"></a>Release Candidate

Kandidáty jsou v podstatě [identifikace](#Identification) výsledky (např. zjištěné osob a úrovní spolehlivosti v detekce). Je reprezentován kandidát [PersonID](#Person-ID) a [spolehlivosti](#Confidence), označující, že osoba s vysokou úrovní spolehlivosti rozpoznán.

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="confidence"></a>Spolehlivost

Pravděpodobnost je měření odhalení podobnosti mezi [tváří](#Face) nebo [osoba](#Person) v číselné hodnoty – který se používá v [identifikace](#Identification), a [ ověření](#Verification) udávajících podobnosti hledaný, identifikován a ověřené výsledky.

Další podrobnosti najdete v následujících příručkách: [Rozpoznávání tváře – vyhledání podobných](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [pro rozpoznávání tváře – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Detekce a rozpoznávání

Rozpoznávání tváří, je proces vyhledání tváří na obrázcích. Uživatelům můžete nahrát obrázek nebo zadejte adresu URL obrázku v požadavku. Zjištěné tváří jsou vráceny pomocí [face ID](#Face-ID) označující jedinečné identity v rozhraní API pro rozpoznávání tváře. Obdélníků označení umístění pro rozpoznávání tváře v obrázku v pixelech, stejně jako volitelný [atributy](#Attributes) pro každou tvář, jako [stáří](#Age-Attribute), [pohlaví](#Gender-Attribute), [hlavní pozice ](#Head-Pose-Attribute), [vousy](#Facial-Hair-Attribute) a [usmívající se](#Smile-Attribute).

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

## <a name="f"></a>F

#### <a name="face"></a>Tvář

Rozpoznávání tváře je jednotný termín pro výsledky odvozen z rozhraní API pro rozpoznávání tváře související s zjištěné tváří. Nakonec pro rozpoznávání tváře je reprezentována jednotné identity ([Face ID](#Face-ID)), určité oblasti do bitové kopie ([obdélník pro rozpoznávání tváře](#Face-Rectangle)) a navíc pro rozpoznávání tváře vztahující se atributy, jako například [stáří](#Age-Attribute), [pohlaví](#Gender-Attribute), orientačních bodů a [hlavní póza](#Head-Pose-Attribute). Kromě toho mohou být vráceny tváří z detekce.

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-api"></a>Rozhraní API pro rozpoznávání tváře

Rozhraní API pro rozpoznávání tváře je rozhraní API založené na cloudu, které poskytuje špičkové algoritmy pro rozpoznávání tváře a uznání. Hlavní funkce rozhraní API pro rozpoznávání tváře je možné rozdělit do dvou kategorií: rozpoznávání s atributy a rozpoznávání tváře [rozpoznávání](#Recognition).

Další podrobnosti najdete v následujících příručkách: [Přehled rozhraní API pro rozpoznávání tváře](./Overview.md), [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [pro rozpoznávání tváře – vyhledání podobných](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [pro rozpoznávání tváře – seskupit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [ Rozpoznávání tváře – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="face-attributesfacial-attributes"></a>Atributy/Facial atributy tváří

Podrobnosti najdete na [atributy](#Attributes).

#### <a name="face-id"></a>ID tváře

Face ID je odvozena z výsledků zjišťování, ve kterých představuje [pro rozpoznávání tváře](#Face) v [API pro rozpoznávání tváře](#Face-API).

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-landmarksfacial-landmarks"></a>Zajímavá památek/Facial pro rozpoznávání tváře

Památek jsou volitelné ve výsledcích zjišťování; sémantické tváře bodů, například oči, nos a přidržte (znázorněný na následujícím obrázku), které jsou. Zajímavá mohou být požadavku zjišťování řízena logická returnFaceLandmarks čísla. Pokud returnFaceLandmarks je nastavená na hodnotu true, bude mít vrácené tváří památek atributy.

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![Jak se rozpoznává tvář](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Rámeček pro rozpoznávání tváře

Rozpoznávání tváře obdélníku je odvozen z výsledky vyhledávání se svislé obdélníku, (vlevo, horní, šířka, výška) v obrázku v pixelech. Levého horního rohu [pro rozpoznávání tváře](#Face) (levý, horní), kromě šířku a výšku, určuje velikost pro rozpoznávání tváře ve x a y OS v uvedeném pořadí.

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facial-hair-attribute"></a>Vousy (atribut)

Vousy je jedním z [atributy](#Attributes) používají k popisu vousy délka k dispozici tváří. Vousy atribut je volitelný ve výsledcích zjišťování a mohou být s žádostí o detekci řízena returnFaceAttributes. Obsahuje-li returnFaceAttributes "facialHair", bude mít vrácené tváří vousy atributy.

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facelist"></a>FaceList

FaceList je kolekce [PersistedFace](#PersistedFace) a je jednotka [Najít podobné](#Find-Similar). FaceList dodává se [FaceList ID](#FaceList-ID), stejně jako ostatní atributy jako jsou název a uživatelská Data.

Další podrobnosti najdete v následujících příručkách: [FaceList - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="facelist-id"></a>FaceList ID

FaceList ID je použít jako identifikátor z poskytnutého uživatelem řetězec [FaceList](#FaceList). FaceList ID musí být jedinečné v rámci předplatného.

Další podrobnosti najdete v následujících příručkách: [FaceList - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="find-similar"></a>Vyhledání podobných

Toto rozhraní API se používá k hledání nebo dotaz podobných tváří na základě kolekce tváří. Dotaz tváří a rozpoznávání tváře kolekce jsou reprezentovány ve formě [face ID](#Face-ID) nebo [FceList ID](#FaceList-ID)/[LargeFaceList ID](#LargeFaceList-ID) v požadavku. Vrátí prohledávaném podobných tváří reprezentována jsou výsledky [face ID](#Face-ID) nebo PersistedFace identifikátory.

Další podrobnosti najdete v následujících příručkách: [Rozpoznávání tváře – vyhledání podobných](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="gender-attribute"></a>Pohlaví (atribut)

Pohlaví je jedním z [atributy](#Attributes) používají k popisu pohlaví dostupné tváří. Pohlaví atribut je volitelný ve výsledcích zjišťování a mohou být s žádostí o detekci řízena returnFaceAttributes. Obsahuje-li returnfaceAttributes "pohlaví", vrácený tváří bude mít atributy pohlaví.

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="grouping"></a>Seskupování

Tváří nebo seskupení je seskupení v kolekci tváří podle obličeje podobnosti. Rozpoznávání tváře kolekce jsou označeny kolekce face ID v požadavku. V důsledku seskupování podobných tváří jsou seskupeny jako [skupiny](#Groups), a jako skupinu neuspořádaných jsou sloučeny tváří, které nejsou podobně jako ostatní rozpoznávání tváře. Existuje nanejvýš, [neuspořádaných skupiny](#Messy-Group) ve výsledku seskupení.

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – seskupit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="groups"></a>Skupiny

Skupiny jsou odvozeny z [seskupení](#Grouping) výsledky. Každá skupina obsahuje kolekci podobných tváří, kde jsou označeny tváří [face ID](#Face-ID).

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – seskupit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>Hlavní představovat (atribut)

Pozice HEAD je jedním z [atributy](#Attributes) , představuje pro rozpoznávání tváře orientace v 3D prostoru podle vrácení, výšku a úhlu natočení úhly, jak je znázorněno na následujícím obrázku. Rozsahy hodnot z vrátit a yaw jsou [-180, 180] a [-90, 90] ve stupních. V aktuální verzi rozteč hodnota vrácená z detekce je vždy 0. Hlavní póza atribut je volitelný ve výsledcích zjišťování a lze ovládat pomocí žádosti o zjištění returnFaceAttributes parametrem. Pokud returnFaceAttributes parametru obsahuje "headPose", vrácený tváří bude mít hlavní představovat atributy.

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Identifikace

Identifikace je identifikace tváří jeden objekt LargePersonGroup/PersonGroup jeden nebo více.
A [jeden objekt PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup) je kolekce [osoby](#Person).
Jsou reprezentovány tváří a LargePersonGroup/jeden objekt PersonGroup [face ID](#Face-ID) a [LargePersonGroup ID](#LargePersonGroup-ID)/[jeden objekt PersonGroup ID](#PersonGroup-ID) v uvedeném pořadí požadavek.
Jsou identifikované výsledky [kandidáty](#Candidate), je znázorněn [osoby](#Person) bez obav.
Více tváří na vstupu se považují za samostatně a každou tvář bude mít identifikovaný výsledek.

> [!NOTE]
> LargePersonGroup/jeden objekt PersonGroup by Trénink úspěšně před identifikace. Pokud není vyškolený LargePersonGroup/jeden objekt PersonGroup, nebo na školení [stav](#Status-Train) nezobrazuje 'bylo dokončeno"(to znamená" systém","se nezdařilo"nebo"časový limit"), odpovědi na požadavek je 400.
> 

Další podrobnosti najdete v následujících příručkách: [Rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [osoba LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [jeden objekt PersonGroup Osoba – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [jeden objekt PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="isidentical"></a>IsIdentical

IsIdentical je logická pole [ověření](#Verification) výsledky, která určuje, jestli dvě tváře patří stejné osobě.

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="j"></a>J

## <a name="k"></a>tis.

## <a name="l"></a>L

#### <a name="landmarks"></a>Zajímavá

Podrobnosti najdete na orientačních bodů pro rozpoznávání tváře.

#### <a name="largefacelist"></a>LargeFaceList

LargeFaceList je kolekce [PersistedFace](#PersistedFace) a je jednotka [Najít podobné](#Find-Similar). LargeFaceList dodává se [LargeFaceList ID](#LargeFaceList-ID), stejně jako ostatní atributy jako jsou název a uživatelská Data.

Další podrobnosti najdete v následujících příručkách: [LargeFaceList – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList – Face seznamu](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="largefacelist-id"></a>LargeFaceList ID

LargeFaceList ID je použít jako identifikátor z poskytnutého uživatelem řetězec [LargeFaceList](#LargeFaceList). LargeFaceList ID musí být jedinečné v rámci předplatného.

Další podrobnosti najdete v následujících příručkách: [LargeFaceList – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup je kolekce [osoby](#Person) a je jednotka [identifikace](#Identification). LargePersonGroup dodává se [LargePersonGroup ID](#LargePersonGroup-ID), stejně jako ostatní atributy jako jsou název a uživatelská Data.

Další podrobnosti najdete v následujících příručkách: [LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [osoba LargePersonGroup – seznam](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="largepersongroup-id"></a>LargePersonGroup ID

LargePersonGroup ID je použít jako identifikátor z poskytnutého uživatelem řetězec [LargePersonGroup](#LargePersonGroup). LargePersonGroup ID musí být jedinečné v rámci předplatného.

Další podrobnosti najdete v následujících příručkách: [LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="messy-group"></a>Neuspořádaných skupiny

Neuspořádaných skupiny je odvozený od [seskupení](#Grouping) výsledků, které obsahuje tváří není podobně jako ostatní rozpoznávání tváře. Každý obličej neuspořádaných skupiny se vyznačuje [face ID](#Face-ID).

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – seskupit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>Ne

#### <a name="name-person"></a>Název (osoba)

Název je uživatelsky přívětivější popisný řetězec pro [osoba](#Person). Na rozdíl od [ID osoby](#Person-ID), jména lidí, se dají duplikovat. v rámci skupiny.

Další podrobnosti najdete v následujících příručkách: [Vytvořit uživatele LargePersonGroup –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [osoba LargePersonGroup – získání](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [vytvořit jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [získat jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="name-largepersongrouppersongroup"></a>Název (LargePersonGroup/jeden objekt PersonGroup)

Název je taky popisný řetězec uživatelsky přívětivější [LargePersonGroup](#LargePersonGroup)/[jeden objekt PersonGroup](#PersonGroup). Na rozdíl od [LargePersonGroup ID](#LargePersonGroup-ID)/[jeden objekt PersonGroup ID](#PersonGroup-ID), může být duplicitní název LargePersonGroups/objektů Persongroup v rámci předplatného.

Další podrobnosti najdete v následujících příručkách: [LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [jeden objekt PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="o"></a>O

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace je datová struktura v rozhraní API pro rozpoznávání tváře. PersistedFace se dodává s PersistedFace ID, jakož i další atributy, například název a dat uživatele.

Další podrobnosti najdete v následujících příručkách: [LargeFaceList – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [osoba LargePersonGroup – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [jeden objekt PersonGroup uživatele – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person-id"></a>ID osoby

ID osoby se vygeneruje při [PersistedFace](#PersistedFace) byla úspěšně vytvořena. Řetězec se vytvoří pro reprezentaci této tvář [API pro rozpoznávání tváře](#Face-API).

Další podrobnosti najdete v následujících příručkách: [LargeFaceList – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [osoba LargePersonGroup – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [jeden objekt PersonGroup uživatele – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person"></a>Person (Osoba)

Uživatel je datová struktura, spravované v rozhraní API pro rozpoznávání tváře. Součástí osoba [ID osoby](#Person-ID), stejně jako ostatní atributy, jako je například název, kolekci [PersistedFace](#PersistedFace)a dat uživatele.

Další podrobnosti najdete v následujících příručkách: [Vytvořit uživatele LargePersonGroup –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [osoba LargePersonGroup – získání](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [vytvořit jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [získat jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="person-id"></a>ID osoby

ID osoby se vygeneruje při [osoba](#Person) byla úspěšně vytvořena. Vytvoření řetězec představující tuto osobu v [API pro rozpoznávání tváře](#Face-API).

Další podrobnosti najdete v následujících příručkách: [Vytvořit uživatele LargePersonGroup –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [osoba LargePersonGroup – získání](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [vytvořit jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [získat jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="persongroup"></a>PersonGroup

Jeden objekt PersonGroup je kolekce [osoby](#Person) a je jednotka [identifikace](#Identification). Jeden objekt PersonGroup dodává se [jeden objekt PersonGroup ID](#PersonGroup-ID), stejně jako ostatní atributy jako jsou název a uživatelská Data.

Další podrobnosti najdete v následujících příručkách: [Jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [jeden objekt PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [jeden objekt PersonGroup uživatele – seznam](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="persongroup-id"></a>Jeden objekt PersonGroup ID

Jeden objekt PersonGroup ID je použít jako identifikátor z poskytnutého uživatelem řetězec [jeden objekt PersonGroup](#PersonGroup). ID skupiny musí být jedinečný v rámci předplatného.

Další podrobnosti najdete v následujících příručkách: [Jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [jeden objekt PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Představovat (atribut)

Podrobnosti najdete na [představovat Head](#Head-Pose-Attribute).

## <a name="q"></a>Q

## <a name="r"></a>R

#### <a name="recognition"></a>Rozpoznání

Rozpoznávání, jako je oblast Oblíbené aplikace pro rozpoznávání tváře technologie [Najít podobné](#Find-Similar), [seskupení](#Grouping), [identifikovat](#Identification),[ověření dvě čelí stejné, nebo ne ](#Verification).

Další podrobnosti najdete v následujících příručkách: [Rozpoznávání tváře – vyhledání podobných](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [pro rozpoznávání tváře – seskupit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [pro rozpoznávání tváře – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Obdélník (pro rozpoznávání tváře)

Podrobnosti najdete na [pro rozpoznávání tváře obdélník](#Face-Rectangle).

## <a name="s"></a>S

#### <a name="similar-face-searching"></a>Vyhledávání podobných tváří

Podrobnosti najdete na [Najít podobné](#Find-Similar).

#### <a name="smile-attribute"></a>Usmívejte (atribut)

Úsměv je jedním z [atributy](#Attributes) používají k popisu úsměv výrazu tváře k dispozici. Úsměv atribut je volitelný ve výsledcích zjišťování a mohou být s žádostí o detekci řízena returnFaceAttributes. Obsahuje-li returnFaceAttributes "usmívejte", bude mít vrácené tváří smajlíka atributy.

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="snapshot"></a>Snímek

Snímek je dočasný vzdálené úložiště pro některé typy dat pro rozpoznávání tváře. Funguje jako typ schránky pro kopírování dat z jednoho předplatného do druhého. První uživatel "přebírá" snímek dat ve zdrojovém předplatném, a potom "použijí" je nový datový objekt v cílovém předplatném. 

Další podrobnosti najdete v tématu [Průvodce migrací pro rozpoznávání tváře](./face-api-how-to-topics/how-to-migrate-face-data.md) také [snímku - trvat](https://docs.microsoft.com/rest/api/cognitiveservices/face/snapshot/take) a [snímku - použít](https://docs.microsoft.com/rest/api/cognitiveservices/face/snapshot/apply) referenční dokumentace (REST).

#### <a name="status-train"></a>Stav (regrese)

Stav je řetězec, slouží k popisu postup pro školení LargeFaceList/LargePersonGroups/objektů Persongroup, včetně 'notstarted","spuštěna","bylo úspěšné","se nezdařilo".

Další podrobnosti najdete v průvodci [LargeFaceList – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [jeden objekt PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Klíč předplatného

Klíč předplatného je řetězec, který je třeba zadat jako parametr řetězce dotazu k vyvolání libovolného rozhraní API pro rozpoznávání tváře. Klíč předplatného najdete na stránce Moje předplatné po přihlášení na portál služeb Microsoft Cognitive Services. Bude existovat dva klíče přidružené k každé předplatné: jeden primární klíč a jedna sekundární klíč. Jak je možné vyvolat shodně rozhraní API. Potřebujete k lepšímu zabezpečení klíče předplatného a klíče předplatného můžete obnovit kdykoli ze stránky Moje předplatné i.

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Trénování (LargeFaceList/LargePersonGroup/jeden objekt PersonGroup)

Toto rozhraní API slouží k předběžně zpracovat [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[jeden objekt PersonGroup](#PersonGroup) zajistit [najít Podobně jako](#Find-Similar)/[identifikace](#Identification) výkonu. Pokud není provozované na školení, nebo [školení stav](#Status-Train) nezobrazuje bylo dokončeno, identifikace pro tento jeden objekt PersonGroup bude mít za následek selhání.

Další podrobnosti najdete v následujících příručkách: [LargeFaceList – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [jeden objekt PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>UserData/User Data

Data uživatele jsou další informace o přidružené [osoba](#Person) a [jeden objekt PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup). Uživatelská data je nastavena podle uživatele tak, aby data použít, a mějte na paměti.

Další podrobnosti najdete v následujících příručkách: [LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – aktualizace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [osoba LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [aktualizovat uživatele LargePersonGroup –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [Jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [jeden objekt PersonGroup – aktualizace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [vytvořit jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [aktualizovat jeden objekt PersonGroup uživatele –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="verification"></a>Ověření

Toto rozhraní API se používá k ověření, jestli dvě tváře jsou stejné, nebo ne. Obě tváří jsou zastoupeny EDT face ID v požadavku. Ověřit výsledky obsahují pole Boolean (isIdentical) stejný označující, pokud je hodnota true a pole čísla ([spolehlivosti](#Confidence)) označující úroveň spolehlivosti.

Další podrobnosti najdete v průvodci [pro rozpoznávání tváře – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>Ano

## <a name="z"></a>Z
