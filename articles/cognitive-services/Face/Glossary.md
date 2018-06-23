---
title: Glosář pro rozhraní API služby vzhled | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Glosáři vysvětluje pojmy, můžete narazit při práci se službou API řez.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ec3068c013be9f2fa4ff34b1c24596e46e7c5d05
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342546"
---
# <a name="glossary"></a>Glosář

## <a name="a"></a>A

#### <a name="Attributes"></a>Atributy

Atributy jsou volitelné v [detekce](#Detection-Face-Detection) výsledků, jako například [stáří](#Age-Attribute), [pohlaví](#Gender-Attribute), [head pozice](#Head-Pose-Attribute), [obličeje kříž](#Facial-Hair-Attribute), [usmívající se](#Smile-Attribute).
Získat z [detekce](#Detection-Face-Detection) rozhraní API tak, že zadáte parametry dotazu: returnFaceAttributes. Atributy poskytnout další informace o vybrané [řezy](#Face); kromě [čelí ID](#Face-ID) a [obdélníku](#Face-Rectangle).

Další podrobnosti naleznete v průvodci [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Age-Attribute"></a>Stáří (atribut)

Stáří je jedním z [atributy](#Attributes) stáří určitý řez, který popisuje. Je v volitelné stáří atribut [detekce](#Detection-Face-Detection) výsledky a je možné řídit pomocí [detekce](#Detection-Face-Detection) žádosti zadáním parametru returnFaceAttributes.

Další podrobnosti naleznete v průvodci [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="b"></a>B

## <a name="c"></a>C

#### <a name="Candidate"></a>Candidate

Kandidáty jsou v podstatě [identifikace](#Identification) výsledky (např. zjištěné osob a úrovní spolehlivosti v detekce). Kandidátem je reprezentována [PersonID](#Person-ID) a [spolehlivosti](#Confidence), která udává, že je osoba, která identifikuje s vysokou úrovní spolehlivosti.

Další podrobnosti naleznete v průvodci [čelí – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="Confidence"></a>Spolehlivosti

Spolehlivosti je měření odhalil podobnosti mezi [řezy](#Face) nebo [osoba](#Person) číselné hodnoty – který se používá v [identifikace](#Identification), a [ ověření](#Verification) udávajících podobnosti výsledků vyhledávaná, identifikovaných a ověřovat.

Další podrobnosti naleznete v následujících příručkách: [vzhled - Najít podobné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [čelí – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [čelí – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="Detection-Face-Detection"></a>Zjišťování/vzhled detekce

Detekce řez je akce vyhledávání tyto řezy v bitové kopie. Uživatele můžete nahrát bitovou kopii nebo zadejte adresu URL obrázku v požadavku. Zjištěné řezy jsou vráceny s [čelí ID](#Face-ID) označující jedinečnou identitu v rozhraní API řez. Obdélníky označuje umístění vzhled obrázku v pixelech, jakož i nepovinný [atributy](#Attributes) pro každý řez jako [stáří](#Age-Attribute), [pohlaví](#Gender-Attribute), [head pozice ](#Head-Pose-Attribute), [obličeje kříž](#Facial-Hair-Attribute) a [usmívající se](#Smile-Attribute).

Další podrobnosti naleznete v průvodci [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

## <a name="f"></a>F

#### <a name="Face"></a>Řez

Řez je jednotná termín pro výsledky odvozené od vzhled rozhraní API související s zjištěné řezy. Nakonec, řez je reprezentována jednotnou identitu ([vzhled ID](#Face-ID)), zadané oblasti do bitových kopií ([vzhled rámečku](#Face-Rectangle)) a navíc vzhled související s [atributy](#Face-Attributes-Facial-Attributes), jako je například [stáří](#Age-Attribute), [pohlaví](#Gender-Attribute), [zajímavá](#Face-Landmarks-Facial-Landmarks) a [head pozice](#Head-Pose-Attribute). Kromě toho mohou být vráceny řezy z [detekce](#Detection-Face-Detection).

Další podrobnosti naleznete v průvodci [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Face-API"></a>Vzhled rozhraní API

Vzhled rozhraní API je rozhraní API založené na cloudu, které poskytuje nejmodernější algoritmy pro detekci vzhled a rozpoznat. Hlavní funkce rozhraní API řez je možné rozdělit do dvou kategorií: vzhled [detekce](#Detection-Face-Detection) s [atributy](#Face-Attributes-Facial-Attributes)a vzhled [rozpoznávání](#Recognition).

Další podrobnosti naleznete v následujících příručkách: [přehled API vzhled](./Overview.md), [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [vzhled - Najít podobné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [čelí – skupina](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Čelí – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [čelí – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="Face-Attributes-Facial-Attributes"></a>Vzhled atributy nebo Facial atributy

Najdete v tématu [atributy](#Attributes).

#### <a name="Face-ID"></a>Vzhled ID

ID řez je odvozený od [detekce](#Detection-Face-Detection) výsledky, ve kterých řetězec reprezentuje [vzhled](#Face) v [vzhled API](#Face-API).

Další podrobnosti naleznete v průvodci [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Face-Landmarks-Facial-Landmarks"></a>Vzhled zajímavá/Facial zajímavá

Zajímavá jsou volitelné v [detekce](#Detection-Face-Detection) výsledků, které jsou sémantického obličeje bodů, například očí, nos a úst (ukazuje následující obrázek). Zajímavá se dá řídit pomocí [detekce](#Detection-Face-Detection) požadavek Boolean returnFaceLandmarks číslo. Pokud returnFaceLandmarks nastavená na hodnotu true, bude vrácená řezy mít významné atributy.

Další podrobnosti naleznete v průvodci [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="Face-Rectangle"></a>Vzhled rámečku

Vzhled rámečku je odvozený od [detekce](#Detection-Face-Detection) výsledky, které je výšku obdélníku (vlevo, top, šířka a výška) v obrázku v pixelech. Levém horním rohu [vzhled](#Face) (levé, top), kromě šířky a výšky určuje vzhled velikostí v x a y OS v uvedeném pořadí.

Další podrobnosti naleznete v průvodci [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Facial-Hair-Attribute"></a>Obličeje kříž (atribut)

Obličeje kříž je jedním z [atributy](#Attributes) používají k popisu obličeje kříž délka dostupné řezy. Atribut obličeje kříž je v volitelné [detekce](#Detection-Face-Detection) výsledky a je možné řídit pomocí [detekce](#Detection-Face-Detection) požadavek returnFaceAttributes. Obsahuje-li returnFaceAttributes 'facialHair', bude mít vrácený řezy obličeje kříž atributy.

Další podrobnosti naleznete v průvodci [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="FaceList"></a>FaceList

FaceList je kolekce [PersistedFace](#PersistedFace) a je jednotkou [Najít podobné](#Find-Similar). Součástí FaceList [FaceList ID](#FaceList-ID), stejně jako ostatní atributy, jako [název](#Name) a [uživatelská Data](#UserData-User-Data).

Další podrobnosti naleznete v následujících příručkách: [FaceList - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="FaceList-ID"></a>FaceList ID

FaceList ID je řetězec uživatel zadal slouží jako identifikátor [FaceList](#FaceList). FaceList ID musí být jedinečný v rámci předplatného.

Další podrobnosti naleznete v následujících příručkách: [FaceList - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="Find-Similar"></a>Vyhledejte podobné

Toto rozhraní API slouží k vyhledávání nebo dotazu podobné řezy podle kolekce řezy. Dotaz řezy a vzhled kolekce jsou reprezentovány jako [čelí ID](#Face-ID) nebo [FceList ID](#FaceList-ID)/[LargeFaceList ID](#LargeFaceList-ID) v požadavku. Vrátí výsledky jsou vyhledávaná podobné řezy reprezentována [čelí ID](#Face-ID) nebo [PersistedFace ID](#PersistedFace-ID).

Další podrobnosti naleznete v následujících příručkách: [vzhled - Najít podobné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="Gender-Attribute"></a>Pohlaví (atribut)

Pohlaví je jedním z [atributy](#Attributes) používají k popisu pohlaví dostupné ploch. Je v volitelný atribut pohlaví [detekce](#Detection-Face-Detection) výsledky a je možné řídit pomocí [detekce](#Detection-Face-Detection) požadavek returnFaceAttributes. Pokud returnfaceAttributes obsahuje, pohlaví', bude mít vrácený řezy pohlaví atributy.

Další podrobnosti naleznete v průvodci [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Grouping"></a>Seskupení

Seskupení řez je seskupení kolekce řezy podle obličeje podobnosti. Vzhled kolekce jsou označeny ID kolekce řez v požadavku. V důsledku seskupení, podobně jako řezy jsou seskupeny dohromady jako [skupiny](#Groups), a jako komplikované skupiny jsou sloučeny řezy, které nejsou podobně jako ostatní řez. Je nejvíce, jedna [komplikované skupiny](#Messy-Group) ve výsledku seskupení.

Další podrobnosti naleznete v průvodci [čelí – skupina](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="Groups"></a>Skupiny

Skupiny jsou odvozeny od [seskupení](#Grouping) výsledky. Každá skupina obsahuje kolekci podobné řezy, které jsou označeny řezy, které [čelí ID](#Face-ID).

Další podrobnosti naleznete v průvodci [čelí – skupina](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="Head-Pose-Attribute"></a>HEAD představovat (atribut)

Pozice HEAD je jedním z [atributy](#Attributes) , představuje čelí orientaci v 3D prostoru podle kumulativní, výšky a úhlu natočení úhly, jak ukazuje následující obrázek. Hodnota rozsahy vrátit a yaw jsou [-180, 180] a [-90, 90] ve stupních. V aktuální verzi, výška hodnota vrácená z detekce je vždy 0. Atribut head pozice je volitelný v [detekce](#Detection-Face-Detection) výsledky a je možné řídit pomocí [detekce](#Detection-Face-Detection) požadavku returnFaceAttributes parametrem. Pokud parametr returnFaceAttributes obsahuje 'headPose', bude mít vrácený řezy head představovat atributy.

Další podrobnosti naleznete v průvodci [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="Identification"></a>Identifikace

Identifikace je určit jeden nebo více řezy z LargePersonGroup/PersonGroup.
A [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup) je kolekce [osob](#Person).
Řezy a LargePersonGroup/PersonGroup jsou reprezentované pomocí [čelí ID](#Face-ID) a [LargePersonGroup ID](#LargePersonGroup-ID)/[PersonGroup ID](#PersonGroup-ID) v uvedeném pořadí v požadavek.
Jsou identifikované výsledky [kandidáty](#Candidate), je znázorněn [osob](#Person) s jistotou.
Více řezy ve vstupu se považují za samostatně a každý řez bude mít svůj vlastní identifikovaných výsledek.

> [!NOTE]
> LargePersonGroup/PersonGroup by Trénink úspěšně před identifikace. Pokud není Trénink LargePersonGroup/PersonGroup, nebo školení [stav](#Status-Train) není zobrazena jako "byla úspěšná. (tj ' spuštěna',"se nezdařilo"nebo"časový limit.), je 400 odpověď na žádost.
> 

Další podrobnosti naleznete v následujících příručkách: [čelí – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [LargePersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [ LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="Is-Identical"></a>IsIdentical

IsIdentical je logická hodnota pole [ověření](#Verification) výsledky, která určuje, zda dva řezy patřit do stejné osobě.

Další podrobnosti naleznete v průvodci [čelí – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="j"></a>J

## <a name="k"></a>tis.

## <a name="l"></a>L

#### <a name="landmarks"></a>Zajímavá

Najdete v tématu [čelí zajímavá](#Face-Landmarks-Facial-Landmarks).

#### <a name="LargeFaceList"></a>LargeFaceList

LargeFaceList je kolekce [PersistedFace](#PersistedFace) a je jednotkou [Najít podobné](#Find-Similar). Součástí LargeFaceList [LargeFaceList ID](#LargeFaceList-ID), stejně jako ostatní atributy, jako [název](#Name) a [uživatelská Data](#UserData-User-Data).

Další podrobnosti naleznete v následujících příručkách: [LargeFaceList - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList - seznamu vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="LargeFaceList-ID"></a>LargeFaceList ID

LargeFaceList ID je řetězec uživatel zadal slouží jako identifikátor [LargeFaceList](#LargeFaceList). LargeFaceList ID musí být jedinečný v rámci předplatného.

Další podrobnosti naleznete v následujících příručkách: [LargeFaceList - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="LargePersonGroup"></a>LargePersonGroup

LargePersonGroup je kolekce [osob](#Person) a je jednotkou [identifikace](#Identification). Součástí LargePersonGroup [LargePersonGroup ID](#LargePersonGroup-ID), stejně jako ostatní atributy, jako [název](#Name) a [uživatelská Data](#UserData-User-Data).

Další podrobnosti naleznete v následujících příručkách: [LargePersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [LargePersonGroup osoba – seznam](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="LargePersonGroup-ID"></a>LargePersonGroup ID

LargePersonGroup ID je řetězec uživatel zadal slouží jako identifikátor [LargePersonGroup](#LargePersonGroup). LargePersonGroup ID musí být jedinečný v rámci předplatného.

Další podrobnosti naleznete v následujících příručkách: [LargePersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="Messy-Group"></a>Komplikované skupiny

Komplikované skupiny je odvozený od [seskupení](#Grouping) výsledky; které obsahuje řezy není podobně jako ostatní řez. Každý řez ve skupině komplikované je indikován [čelí ID](#Face-ID).

Další podrobnosti naleznete v průvodci [čelí – skupina](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>Ne

#### <a name="name-person"></a>Název (uživatel)

Název je uživatelsky přívětivý popisný řetězec pro [osoba](#Person). Na rozdíl od [ID osoby](#Person-ID), může být duplicitní název osoby v rámci skupiny.

Další podrobnosti naleznete v následujících příručkách: [LargePersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup osoba - získat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Získat PersonGroup osoba -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="Name"></a>Název (LargePersonGroup/PersonGroup)

Název je taky uživatelsky přívětivý popisný řetězec pro [LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup). Na rozdíl od [LargePersonGroup ID](#LargePersonGroup-ID)/[PersonGroup ID](#PersonGroup-ID), mohou být duplicitní název LargePersonGroups/PersonGroups v rámci předplatného.

Další podrobnosti naleznete v následujících příručkách: [LargePersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [PersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [ PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="o"></a>O

## <a name="p"></a>P

#### <a name="PersistedFace"></a>PersistedFace

PersistedFace je datová struktura, v rozhraní API řez. Součástí PersistedFace [PersistedFace ID](#PersistedFace-ID), stejně jako ostatní atributy, jako [název](#Name), a [uživatelská Data](#UserData-User-Data).

Další podrobnosti naleznete v následujících příručkách: [LargeFaceList - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup osoba - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ Osoba, PersonGroup - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="Person-ID"></a>ID osoby

ID osoby se vygeneruje při [PersistedFace](#PersistedFace) byla úspěšně vytvořena. Řetězec se vytvoří k reprezentaci této řez v [vzhled API](#Face-API).

Další podrobnosti naleznete v následujících příručkách: [LargeFaceList - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup osoba - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ Osoba, PersonGroup - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="Person"></a>Osoba

Osobou, která je datová struktura spravované v rozhraní API řez. Uživatel se dodává s [ID osoby](#Person-ID), stejně jako ostatní atributy, jako [název](#Name), kolekce [PersistedFace](#PersistedFace), a [uživatelská Data](#UserData-User-Data).

Další podrobnosti naleznete v následujících příručkách: [LargePersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup osoba - získat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Získat PersonGroup osoba -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="Person-ID"></a>ID osoby

ID osoby se vygeneruje při [osoba](#Person) byla úspěšně vytvořena. Řetězec se vytvoří představují tato osoba v [vzhled API](#Face-API).

Další podrobnosti naleznete v následujících příručkách: [LargePersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup osoba - získat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Získat PersonGroup osoba -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="PersonGroup"></a>PersonGroup

PersonGroup je kolekce [osob](#Person) a je jednotkou [identifikace](#Identification). Součástí PersonGroup [PersonGroup ID](#PersonGroup-ID), stejně jako ostatní atributy, jako [název](#Name) a [uživatelská Data](#UserData-User-Data).

Další podrobnosti naleznete v následujících příručkách: [PersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [PersonGroup osoba – seznam](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="PersonGroup-ID"></a>PersonGroup ID

PersonGroup ID je řetězec uživatel zadal slouží jako identifikátor [PersonGroup](#PersonGroup). ID skupiny musí být jedinečný v rámci předplatného.

Další podrobnosti naleznete v následujících příručkách: [PersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Představovat (atribut)

Najdete v tématu [Head představovat](#Head-Pose-Attribute).

## <a name="q"></a>OTÁZKY

## <a name="r"></a>R

#### <a name="Recognition"></a>Rozpoznávání

Rozpoznávání je oblíbených aplikací pro řez technologií, jako například [Najít podobné](#Find-Similar), [seskupení](#Grouping), [identifikace](#Identification),[ověření dva otočená stejné, nebo ne ](#Verification).

Další podrobnosti naleznete v následujících příručkách: [vzhled - Najít podobné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [čelí – skupina](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [čelí – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [čelí – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Obdélník (vzhled)

Najdete v tématu [vzhled rámečku](#Face-Rectangle).

## <a name="s"></a>S

#### <a name="Smile-Attribute"></a>Oblouček dolů (atribut)

Smajlíka je jedním z [atributy](#Attributes) používají k popisu smajlíka výraz k dispozici tyto řezy. Je v volitelný atribut smajlíka [detekce](#Detection-Face-Detection) výsledky a je možné řídit pomocí [detekce](#Detection-Face-Detection) požadavek returnFaceAttributes. Obsahuje-li returnFaceAttributes 'Oblouček dolů', bude mít vrácený řezy smajlíka atributy.

Další podrobnosti naleznete v průvodci [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="similar-face-searching"></a>Vyhledávání podobných tváří

Najdete v tématu [Najít podobné](#Find-Similar).

#### <a name="Status-Train"></a>Stav (Train)

Stav je řetězec slouží k popisují postup pro [školení LargeFaceList/LargePersonGroups/PersonGroups](#Train), včetně 'notstarted', 'spuštěna', 'bylo úspěšné","se nezdařilo".

Další podrobnosti naleznete v průvodci [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Klíč předplatného

Klíč předplatného je řetězec, který je třeba zadat jako parametr řetězce dotazu, chcete-li vyvolání jakéhokoli rozhraní API řez. Klíč předplatného naleznete na stránce Moje předplatné po přihlášení na portál kognitivní služby společnosti Microsoft. Budou existovat dva klíče, které jsou spojené s každou předplatným: jeden primární klíč a jednu sekundární klíč. Obě slouží k volání rozhraní API shodně. Je potřeba zabezpečit předplatné klíče a můžete obnovit předplatné klíče ze stránky Můj odběr také kdykoli.

## <a name="t"></a>T

#### <a name="Train"></a>Cvičení (LargeFaceList/LargePersonGroup/PersonGroup)

Toto rozhraní API slouží k předem zpracovat [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup) zajistit [najít Podobně jako](#Find-Similar)/[identifikace](#Identification) výkonu. Pokud není provozovat školení, nebo [školení stav](#Status-Train) není zobrazena jako byly úspěšné, bude identifikační pro tento PersonGroup dojít k selhání.

Další podrobnosti naleznete v následujících příručkách: [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [čelí – identifikace ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="UserData-User-Data"></a>UserData nebo uživatelská Data

Data uživatele jsou doplňující informace, které jsou přidružené k [osoba](#Person) a [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup). Uživatelská data nastavovat uživatelé snadněji používat, pochopit a mějte na paměti, aby data.

Další podrobnosti naleznete v následujících příručkách: [LargePersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - aktualizace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [LargePersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup osoba - aktualizovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [PersonGroup - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - aktualizace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [PersonGroup osoba - vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Aktualizovat PersonGroup osoba -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="Verification"></a>Ověření

Toto rozhraní API slouží k ověření, zda dva řezy jsou stejné, nebo ne. Obě tyto řezy jsou reprezentována jako čelí ID v požadavku. Ověřit výsledky obsahují logické pole ([isIdentical](#Is-Identical)) označující stejné v případě hodnoty true a u číselného pole ([spolehlivosti](#Confidence)) označující úroveň spolehlivosti.

Další podrobnosti naleznete v průvodci [čelí – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>Ano

## <a name="z"></a>Z
