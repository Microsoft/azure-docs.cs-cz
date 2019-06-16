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
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 46bd1bdd55725878bc7b1bd55d5e24b78d82aada
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124554"
---
# <a name="get-face-detection-data"></a>Získat data o detekci pro rozpoznávání tváře

Tato příručka ukazuje, jak extrahovat atributů, jako je pohlaví, stáří nebo pozice z danou image pomocí rozpoznávání tváří. Fragmenty kódu v této příručce jsou napsané v C# pomocí klientské knihovny Azure Cognitive Services Face API. Stejné funkce je dostupná prostřednictvím [rozhraní REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Tento průvodce vám ukáže, jak do:

- Získání umístění a velikosti tváří v obrázku.
- Získání umístění různých orientačních bodů pro rozpoznávání tváře, jako je například žáků nos a přidržte v obraze.
- Odhad, pohlaví, věk, pro rozpoznávání emocí a další atributy zjištěné rozpoznávání tváře.

## <a name="setup"></a>Instalace

Tento průvodce to předpokládá, že už vytvořená [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) objekt s názvem `faceClient`, pomocí Face předplatné key a koncového bodu adresy URL. Z tohoto místa můžete použít buď voláním funkce rozpoznávání tváře [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), který se používá v této příručce, nebo [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Návod, jak tuto funkci nastavit, najdete v článku [rozpoznat čelí rychlý start pro C# ](../quickstarts/csharp-detect-sdk.md).

Tato příručka se zaměřuje na konkrétní rozpoznat volání, například jaké argumenty můžete předat a co můžete dělat s vrácenými daty. Doporučujeme, abyste dotázat pouze funkce, které potřebujete. Každá operace bude vyžadovat čas navíc k dokončení.

## <a name="get-basic-face-data"></a>Získání dat základní pro rozpoznávání tváře

Nenašli žádné tváře a získat jejich umístění v obraze, volání metody _returnFaceId_ parametr nastaven na **true**. Toto nastavení je výchozí.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Můžete zadat dotaz vrácené [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) objekty pro jejich jedinečné ID a obdélníku, který poskytuje pixel souřadnice typ písma.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Informace o tom, jak analyzovat umístění a rozměry pracovní plochy najdete v tématu [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Obvykle obsahuje obdélníku oči, obočí, nos a úst. Horní části head, uší a chin nejsou nutně součástí. Použití obličejový obdélník oříznutí kompletní head nebo získat portrét uprostřed snímek, třeba pro typ ID image fotografií, můžete rozbalit obdélníku v každém směru.

## <a name="get-face-landmarks"></a>Získat orientačních bodů pro rozpoznávání tváře

[Orientačních bodů pro rozpoznávání tváře](../concepts/face-detection.md#face-landmarks) představují sadu snadno najít body na ciferníku, jako je například žáků nebo tip přední. Chcete-li získat data orientačních bodů pro rozpoznávání tváře, nastavte _returnFaceLandmarks_ parametr **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Následující kód ukazuje, jak může načíst umístění nos a žáků:

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

Rozpoznávání tváře zajímavá data můžete použít také pro přesný výpočet směr typ písma. Například můžete definovat otočení obličej vektor v centru ústí do středu očí. Následující kód vypočítá tento vektoru:

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

Když víte, směr plošku, můžete otočit obdélníkové tváře rámec jeho zarovnání větší správně. Pokud chcete provést oříznutí tváří v obrázku, můžete prostřednictvím kódu programu otočení obrázku tak, aby plochy se vždy zobrazí svislé.

## <a name="get-face-attributes"></a>Získat obličejových atributů

Kromě obdélníky a památek rozpoznávání tváře, rozhraní API můžete analyzovat několik atributů koncepční tváře. Úplný seznam najdete v tématu [atributy pro rozpoznávání tváře](../concepts/face-detection.md#attributes) koncepční oddílu.

Chcete-li analyzovat obličejových atributů, nastavte _returnFaceAttributes_ parametru do seznamu [FaceAttributeType výčtu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) hodnoty.

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

Další informace o každého z atributů najdete v tématu [rozpoznávání tváří a atributy](../concepts/face-detection.md) koncepční průvodce.

## <a name="next-steps"></a>Další postup

V této příručce jste zjistili, jak používat různé funkce rozpoznávání tváře. V dalším kroku integrate tyto funkce do vaší aplikace pomocí následujících podrobný kurz.

- [Kurz: Vytvoření aplikace WPF pro zobrazení dat pro rozpoznávání tváře v obrázku](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Kurz: Vytvoření aplikace pro Android ke zjišťování a snímků tváří v obrázku](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Související témata

- [Referenční dokumentace (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referenční dokumentace (sadu .NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)