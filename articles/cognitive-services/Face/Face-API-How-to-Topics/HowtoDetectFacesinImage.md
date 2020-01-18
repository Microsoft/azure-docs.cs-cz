---
title: Rozpoznávání plošek na obrázku – tvář
titleSuffix: Azure Cognitive Services
description: Tato příručka ukazuje, jak použít detekci obličeje k extrakci atributů, jako je pohlaví, věk nebo pozice z daného obrázku.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169880"
---
# <a name="get-face-detection-data"></a>Získat data o detekci obličeje

Tato příručka ukazuje, jak použít detekci obličeje k extrakci atributů, jako je pohlaví, věk nebo pozice z daného obrázku. Fragmenty kódu v této příručce se napíší C# pomocí klientské knihovny Azure Cognitive Services Face. Stejné funkce jsou k dispozici prostřednictvím [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

V této příručce se dozvíte, jak:

- Získá umístění a rozměry ploch v obrázku.
- Získejte na obrázku umístění různých orientačních bodů, jako jsou žákům, nos a pusa.
- Odhadujte pohlaví, věk, emoce a další atributy zjištěné plochy.

## <a name="setup"></a>Nastavení

V tomto průvodci se předpokládá, že už jste nastavili objekt [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) s názvem `faceClient`s klíčovým bodem předplatného a adresou URL koncového bodu. Z tohoto místa můžete použít funkci detekce obličeje voláním [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), který se používá v tomto průvodci nebo v [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Pokyny, jak tuto funkci nastavit, najdete v některém z rychlých startů.

Tato příručka se zaměřuje na konkrétní volání detekce, například jaké argumenty můžete předat a co můžete s vrácenými daty dělat. Doporučujeme zadat dotaz jenom na funkce, které potřebujete. Dokončení každé operace trvá déle.

## <a name="get-basic-face-data"></a>Získat základní data obličeje

Chcete-li najít plošky a získat jejich umístění v obrázku, zavolejte metodu s parametrem _returnFaceId_ nastaveným na **hodnotu true**. Toto nastavení je výchozí.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Můžete zadat dotaz na vrácené objekty [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) pro jejich jedinečná ID a obdélník, který dává souřadnici v pixelech na ploše.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Informace o tom, jak analyzovat umístění a rozměry obličeje, najdete v tématu [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Tento obdélník obvykle obsahuje oči, eyebrows, nos a pusu. Horní okraje hlav, EARS a Chin nejsou nutně zahrnuté. Chcete-li použít obdélník obličeje k oříznutí celé hlavy nebo získání střední pozice na výšku, například pro obrázek typu fotografie s ID, můžete obdélník v každém směru rozšířit.

## <a name="get-face-landmarks"></a>Získat orientační vzhledy

[Přední orientační](../concepts/face-detection.md#face-landmarks) body jsou sada snadno hledaných bodů na plošku, jako jsou žákům nebo špička v nos. Pokud chcete získat data orientačních bodů, nastavte parametr _returnFaceLandmarks_ na **hodnotu true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Následující kód ukazuje, jak můžete načíst umístění nos a žáků:

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

Pomocí dat orientačních bodů můžete také přesně vypočítat směr plochy. Například můžete definovat rotaci obličeje jako vektor od středu úst až po střed oči. Následující kód vypočítá tento vektor:

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

Když znáte směr plochy, můžete obdélníkový rám otočit tak, aby byl lépe zarovnaný. Chcete-li oříznout plošky v obrázku, můžete obrázek programově otočit tak, aby se plošky vždy zobrazovaly vzhůru.

## <a name="get-face-attributes"></a>Získat atributy obličeje

Kromě obdélníků a orientačních bodů může rozhraní API pro detekci obličeje analyzovat několik koncepčních atributů plošky. Úplný seznam najdete v části koncepční [atributy Face](../concepts/face-detection.md#attributes) .

Chcete-li analyzovat atributy tváře, nastavte parametr _returnFaceAttributes_ na seznam hodnot [výčtu FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) .

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

Pak Získejte odkazy na vracená data a udělejte více operací podle vašich potřeb.

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

Další informace o jednotlivých atributech najdete v koncepční příručce pro [rozpoznávání tváře a atributy](../concepts/face-detection.md) .

## <a name="next-steps"></a>Další kroky

V této příručce jste zjistili, jak používat různé funkce detekce tváře. Potom tyto funkce Integrujte do své aplikace pomocí podrobného kurzu.

- [Kurz: Vytvoření aplikace WPF pro zobrazení dat obličeje v obrázku](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Kurz: Vytvoření aplikace pro Android pro detekci a orámování plošek v obrázku](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Související témata

- [Referenční dokumentace (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referenční dokumentace (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)