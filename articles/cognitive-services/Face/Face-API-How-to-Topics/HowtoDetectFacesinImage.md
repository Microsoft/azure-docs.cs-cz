---
title: Rozpoznávání tváří v obrázku – rozhraní API pro rozpoznávání tváře
titleSuffix: Azure Cognitive Services
description: Další informace o použití různých data vrácená funkce rozpoznávání tváře.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: sbowles
ms.openlocfilehash: bf3af8f5d1d2f063199a8275c2f49c70140e8732
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588767"
---
# <a name="get-face-detection-data"></a>Získat data o detekci pro rozpoznávání tváře

Tato příručka popisuje, jak extrahovat atributů, jako je pohlaví, stáří nebo pozice z danou image pomocí rozpoznávání tváří. Fragmenty kódu v této příručce jsou napsané v C# pomocí klientské knihovny rozhraní API pro rozpoznávání tváře, ale stejné funkce je dostupná prostřednictvím [rozhraní REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Tento průvodce se dozvíte, jak do:

- Získání umístění a velikosti tváří v obrázku.
- Získání umístění různých zajímavá pro rozpoznávání tváře (žáků nos, přidržte a tak dále) v obraze.
- Odhad, pohlaví, věk a pro rozpoznávání emocí a dalších atributů zjištěné rozpoznávání tváře.

## <a name="setup"></a>Nastavení

Tento průvodce to předpokládá, že už máte vytvořený **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** objekt s názvem `faceClient`, pomocí Face předplatné key a koncového bodu adresy URL. Z tohoto místa můžete použít buď voláním funkce rozpoznávání tváře **[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)** (použitá v tomto průvodci) nebo **[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)**. Najdete v článku [detekovat čelí rychlý start pro C# ](../quickstarts/csharp-detect-sdk.md) pokyny o tom, jak nastavit tuto možnost.

Tato příručka je zaměřena na konkrétních podrobnostech volání rozpoznat&mdash;argumenty, které můžete předat a co můžete dělat s vrácenými daty. Doporučujeme pouze dotazování na funkce, které potřebujete, protože každá operace bude vyžadovat čas navíc k dokončení.

## <a name="get-basic-face-data"></a>Získání dat základní pro rozpoznávání tváře

Nenašli žádné tváře a získat jejich umístění v obraze, volání metody _returnFaceId_ parametr nastaven na **true** (výchozí).

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Vrácený **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** objekty je možné zadávat dotazy pro jejich jedinečné ID a obdélník poskytující pixel souřadnice typ písma.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Zobrazit **[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)** informace o tom, jak analyzovat umístění a rozměry pracovní plochy. Obvykle obsahuje tento obdélník oči, obočí, nos a úst; horní části head, uší a chin nejsou nutně zahrnuty. Pokud máte v úmyslu použít obličejový obdélník oříznutí kompletní head nebo střední snímek na výšku (ID typu fotografii), můžete chtít rozšířit obdélník podle marži v každém směru.

## <a name="get-face-landmarks"></a>Získat orientačních bodů pro rozpoznávání tváře

Rozpoznávání tváře památek jsou sadu snadno najít body na ciferníku například žáků nebo tip nos. Data orientačních bodů pro rozpoznávání tváře můžete získat tak, že nastavíte _returnFaceLandmarks_ parametr **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Standardně existuje 27 předdefinovaných orientačních bodů. Následující obrázek znázorňuje všechny 27 body:

![Diagram s všechny 27 orientačních bodů, které jsou označeny pro rozpoznávání tváře](../Images/landmarks.1.jpg)

Body vrátil jsou v jednotkách, které pixelů, stejně jako obdélník rámce pro rozpoznávání tváře. Následující kód ukazuje, jak může načíst umístění nos a žáků:

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

Rozpoznávání tváře zajímavá data lze také přesný výpočet směr typ písma. Například můžete definujeme otočení obličej vektor v centru ústí do středu očí. Následující kód vypočítá tento vektoru:

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

Znalost směr plošku, můžete pak otočit obdélníkové tváře rámec jeho zarovnání větší správně. Pokud chcete oříznout tváří v obrázku, můžete prostřednictvím kódu programu otočení obrázku tak, aby plochy se vždy zobrazí svislé.

## <a name="get-face-attributes"></a>Získat obličejových atributů

Kromě obdélníky a památek rozpoznávání tváře, rozhraní API můžete analyzovat několik atributů koncepční tváře. Mezi ně patří:

- Věk
- Pohlaví
- Intenzita úsměvu
- Vousy
- Brýle
- 3D hlavní pozice
- Emoce

> [!IMPORTANT]
> Tyto atributy jsou předpokládány pomocí statistické algoritmy a nemusí být přesné. Při rozhodování na základě atributů dat, buďte opatrní.
>

Chcete-li analyzovat obličejových atributů, nastavte _returnFaceAttributes_ parametru do seznamu **[FaceAttributeType výčtu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** hodnoty.

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Potom získat odkazy na vrácená data a dělat další operace podle vašich potřeb.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Další informace o každého z atributů, najdete v tématu [Glosář](../Glossary.md).

## <a name="next-steps"></a>Další postup

V této příručce jste zjistili, jak používat různé funkce rozpoznávání tváře. V dalším kroku najdete v článku [Glosář](../Glossary.md) pro podrobnější pohled na data pro rozpoznávání tváře jsme načtená.

## <a name="related-topics"></a>Související témata

- [Referenční dokumentace (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referenční dokumentace (sadu .NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)
