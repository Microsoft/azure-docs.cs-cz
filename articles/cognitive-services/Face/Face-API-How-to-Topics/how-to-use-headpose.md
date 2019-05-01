---
title: Pomocí HeadPose nastavte obličejový obdélník
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak pomocí atributu HeadPose automaticky otočit obličejový obdélník.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: ddc5bc522c0d3ac258581f2a48a5c3b755302f01
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576499"
---
# <a name="use-the-headpose-attribute-to-adjust-the-face-rectangle"></a>Pomocí atributu HeadPose upravit obličejový obdélník

V tomto průvodci použijete atribut zjištěných pro rozpoznávání tváře HeadPose, otočit obdélník objekt pro rozpoznávání tváře. Ukázkový kód v této příručce, od [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) ukázkovou aplikaci pomocí sady .NET SDK.

Obličejový obdélník se vrátil s každou zjištěnou plošku, označuje umístění a velikost tvář na obrázku. Ve výchozím nastavení obdélníku je vždy v souladu s bitovou kopii (jeho stran jsou dokonale vertikálním a horizontálním); To může být neefektivní pro rámců zalomená tváří. V situacích, ve které chcete prostřednictvím kódu programu oříznout tváří v obrázku je výhodné umožnit otočit obdélník oříznutí.

## <a name="explore-the-sample-code"></a>Prozkoumejte vzorový kód

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

## <a name="display-the-updated-rectangle"></a>Zobrazení aktualizované obdélník

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

## <a name="next-steps"></a>Další postup

Zobrazit [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) aplikace na Githubu pro funkční příklad otočený obdélníky. Nebo si přečtěte [ukázka HeadPose rozhraní API pro rozpoznávání tváře](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) aplikaci, která sleduje HeadPose atribut v reálném čase pro zjištění různých hlavní pohybů plb typu (pokyvující, přičemž).