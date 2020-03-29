---
title: Detekce tváří v obraze – plocha
titleSuffix: Azure Cognitive Services
description: Tato příručka ukazuje, jak používat detekci obličeje k extrahování atributů, jako je pohlaví, věk nebo póza z daného obrázku.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169880"
---
# <a name="get-face-detection-data"></a>Získání dat pro detekci obličeje

Tato příručka ukazuje, jak používat detekci obličeje k extrahování atributů, jako je pohlaví, věk nebo póza z daného obrázku. Fragmenty kódu v této příručce jsou zapsány v C# pomocí knihovny klienta Azure Cognitive Services Face. Stejné funkce jsou k dispozici prostřednictvím [rozhraní REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Tato příručka vám ukáže, jak:

- Získejte umístění a rozměry ploch v obraze.
- Získejte umístění různých orientačních bodů obličeje, jako jsou zornice, nos a ústa, v obraze.
- Hádejte pohlaví, věk, emoce a další atributy zjištěné tváře.

## <a name="setup"></a>Nastavení

Tato příručka předpokládá, že jste již vytvořili objekt [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) s názvem `faceClient`, s klíčem předplatného Face a adresou URL koncového bodu. Zde můžete použít funkci detekce obličeje voláním buď [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), který se používá v této příručce, nebo [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Pokyny k nastavení této funkce naleznete v jednom z rychlých startů.

Tato příručka se zaměřuje na specifika detect volání, jako jsou argumenty, které můžete předat a co můžete dělat s vrácená data. Doporučujeme, abyste se dotazovali pouze na funkce, které potřebujete. Dokončení každé operace trvá déle.

## <a name="get-basic-face-data"></a>Získání základních dat o obličeji

Chcete-li najít tváře a získat jejich umístění v obrázku, zavolejte metodu s parametrem _returnFaceId_ nastaveným na **hodnotu true**. Toto nastavení je výchozí.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Můžete dotaz vrácené [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) objekty pro jejich jedinečné ID a obdélník, který poskytuje souřadnice obrazových bodů plochy.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Informace o tom, jak analyzovat umístění a rozměry plochy, naleznete v tématu [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Obvykle tento obdélník obsahuje oči, obočí, nos a ústa. Horní část hlavy, uší a brady nejsou nutně zahrnuty. Chcete-li použít obdélník obličeje k oříznutí celé hlavy nebo k získání portrétu uprostřed snímku, například pro obrázek typu ID fotografie, můžete obdélník rozbalit v každém směru.

## <a name="get-face-landmarks"></a>Získejte orientační body obličeje

[Orientační body obličeje](../concepts/face-detection.md#face-landmarks) jsou souborem snadno k nalezení bodů na obličeji, jako jsou žáci nebo špička nosu. Chcete-li získat data orientačního bodu obličeje, nastavte parametr _returnFaceLandmarks_ na **hodnotu true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Následující kód ukazuje, jak můžete získat umístění nosu a žáků:

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

Data orientačních bodů plochy můžete také použít k přesnému výpočtu směru plochy. Můžete například definovat otáčení plochy jako vektor od středu úst ke středu očí. Následující kód vypočítá tento vektor:

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

Když znáte směr plochy, můžete obdélníkový rámeček plochy otočit a správněji jej zarovnat. Chcete-li oříznout plochy v obraze, můžete obraz programově otočit tak, aby se plochy vždy zobrazovaly ve vzpřímené poloze.

## <a name="get-face-attributes"></a>Získat atributy obličeje

Kromě obdélníků obličeje a orientačních bodů může rozhraní API pro detekci obličeje analyzovat několik koncepčních atributů plochy. Úplný seznam naleznete v části [Koncepční atributy tváří.](../concepts/face-detection.md#attributes)

Chcete-li analyzovat atributy tváře, nastavte parametr _returnFaceAttributes_ na seznam hodnot [Popředí typu FaceAttributeType.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)

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

Potom získat odkazy na vrácená data a provést další operace podle vašich potřeb.

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

Další informace o jednotlivých atributech najdete v tématu [Face detection and attributes](../concepts/face-detection.md) conceptual guide.

## <a name="next-steps"></a>Další kroky

V této příručce jste se naučili používat různé funkce detekce obličeje. Dále integrujte tyto funkce do aplikace pomocí podrobného kurzu.

- [Kurz: Vytvoření aplikace WPF pro zobrazení dat obličeje v obraze](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Kurz: Vytvoření aplikace pro Android k rozpoznání a orámování tváří na obrázku](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Související témata

- [Referenční dokumentace (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referenční dokumentace (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)