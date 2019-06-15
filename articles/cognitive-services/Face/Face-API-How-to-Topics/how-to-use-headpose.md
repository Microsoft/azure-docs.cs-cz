---
title: Použijte atribut HeadPose
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak pomocí atributu HeadPose obličejový obdélník automaticky otočit nebo zjištění hlavní gesta ve videu, kanálu.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 168b4fce873206e39a32a83da3dc5509b431d6a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058572"
---
# <a name="use-the-headpose-attribute"></a>Použijte atribut HeadPose

V této příručce uvidíte, jak můžete použít atribut HeadPose zjištěné tváře povolit některé klíčové scénáře.

## <a name="rotate-the-face-rectangle"></a>Otočit obličejový obdélník

Obličejový obdélník se vrátil s každou zjištěnou plošku, označuje umístění a velikost tvář na obrázku. Ve výchozím nastavení obdélníku je vždy v souladu s bitovou kopii (jeho stran jsou vertikálním a horizontálním); To může být neefektivní pro rámců zalomená tváří. V situacích, ve které chcete prostřednictvím kódu programu oříznout tváří v obrázku je lepší moct otočit obdélník oříznutí.

[Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) ukázková aplikace používá atribut HeadPose obměna jeho zjištěné obdélníky.

### <a name="explore-the-sample-code"></a>Prozkoumejte vzorový kód

Pomocí atributu HeadPose můžete prostřednictvím kódu programu otočit obličejový obdélník. Pokud zadáte tento atribut při zjištění tváří (naleznete v tématu [jak rozpoznávat tváře](HowtoDetectFacesinImage.md)), budete moci dotaz později. Následující metody z [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) aplikace přebírá seznam **DetectedFace** objekty a vrátí seznam hodnot **[pro rozpoznávání tváře](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** objekty. **Rozpoznávání tváře** tady je vlastní třída, že úložiště pro rozpoznávání tváře data, včetně souřadnice aktualizované obdélník. Nové hodnoty se počítají pro **horní**, **levé**, **šířka**, a **výška**a nové pole **FaceAngle**určuje otočení.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>Zobrazení aktualizované obdélník

Z tohoto místa můžete použít vráceného **pro rozpoznávání tváře** objekty v zobrazení. Následující řádky z [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) ukazují, jak je vykreslen nové obdélník z těchto dat:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Zjištění gest hlav

Můžete zjistit hlavní gesta jako pokyvující a head, přičemž Díky sledování HeadPose změny v reálném čase. Tato funkce slouží jako detektor vlastní aktivity.

Detekce aktivity je úloha určování, zda předmět je skutečná osoba a není reprezentaci obrázek nebo video. Detektor hlavní gesta může sloužit jako jedním ze způsobů, které pomůžou ověřit aktivity, zejména na rozdíl od reprezentaci image osoby.

> [!CAUTION]
> Ke zjištění hlavního gesta v reálném čase, budete potřebovat pro volání rozhraní API pro rozpoznávání tváře na vysokou míru (více než jednou za sekundu). Pokud máte předplatné bezplatné vrstvy (f0), to nebude možné. Pokud máte placené úrovně předplatné, ujistěte se, že jste vypočítat náklady na provedení rychlé API volání pro hlavní gesta zjišťování.

Najdete v článku [ukázka HeadPose rozhraní API pro rozpoznávání tváře](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) na Githubu pro funkční příklad, head gesta zjišťování.

## <a name="next-steps"></a>Další postup

Zobrazit [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) aplikace na Githubu pro funkční příklad otočený obdélníky. Nebo si přečtěte [ukázka HeadPose rozhraní API pro rozpoznávání tváře](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) aplikaci, která sleduje HeadPose atribut v reálném čase k detekci pohybu hlavy.