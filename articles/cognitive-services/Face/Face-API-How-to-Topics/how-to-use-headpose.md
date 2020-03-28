---
title: Použití atributu HeadPose
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak pomocí atributu HeadPose automaticky otáčet obdélník obličeje nebo detekovat gesta hlavy ve videokanálu.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 534846044770d66ec5171ad4f61de921d2d5d194
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169786"
---
# <a name="use-the-headpose-attribute"></a>Použití atributu HeadPose

V této příručce uvidíte, jak můžete použít atribut HeadPose zjištěné tváře povolit některé klíčové scénáře.

## <a name="rotate-the-face-rectangle"></a>Otočení obdélníku plochy

Obdélník plochy, vrácený s každou zjištěnou tváří, označuje umístění a velikost plochy v obraze. Ve výchozím nastavení je obdélník vždy zarovnán s obrazem (jeho strany jsou svislé a vodorovné); to může být neefektivní pro rámování úhlové plochy. V situacích, kdy chcete programově oříznout plochy v obraze, je lepší otočit obdélník, aby se ořízl.

Ukázková aplikace [WPF čídolové služby face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) používá atribut HeadPose k otočení detekovaných obdélníků plochy.

### <a name="explore-the-sample-code"></a>Prozkoumejte ukázkový kód

Obdélník plochy můžete programově otáčet pomocí atributu HeadPose. Pokud zadáte tento atribut při zjišťování ploch (viz [Jak rozpoznat plochy](HowtoDetectFacesinImage.md)), budete jej moci později zadat. Následující metoda z aplikace [HŘsátka číte cognitive services face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) přebírá seznam objektů **DetectedFace** a vrací seznam objektů **[Face.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** **Plocha** zde je vlastní třída, která ukládá data plochy, včetně aktualizovaných souřadnic obdélníku. Nové hodnoty se počítají pro **horní**, **levý**, **šířka**a **výšku**a nové pole **FaceAngle** určuje otočení.

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

### <a name="display-the-updated-rectangle"></a>Zobrazení aktualizovaného obdélníku

Zde můžete použít vrácené objekty **plochy** na displeji. Následující řádky z [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) ukazují, jak je nový obdélník vykreslen z těchto dat:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Detekce gest hlavy

Gesta hlavy, jako je přikyvování a třepání hlavou, můžete detekovat sledováním změn HeadPose v reálném čase. Tuto funkci můžete použít jako vlastní detektor živosti.

Detekce živosti je úkolem určit, že předmět je skutečná osoba, a nikoli obraz nebo video reprezentace. Detektor gest hlavy by mohl sloužit jako jeden způsob, jak pomoci ověřit živost, zejména na rozdíl od obrazu reprezentace osoby.

> [!CAUTION]
> Chcete-li zjistit gesta hlavy v reálném čase, budete muset volat rozhraní API pro rozpoznávání tváře vysokou rychlostí (více než jednou za sekundu). Pokud máte bezplatné předplatné (f0), nebude to možné. Pokud máte předplatné placené úrovně, ujistěte se, že jste vypočítali náklady na rychlé volání rozhraní API pro detekci gest hlavy.

Podívejte se na [ukázku Face HeadPose](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) na GitHubu, kde najdete funkční příklad detekce gest hlavy.

## <a name="next-steps"></a>Další kroky

Podívejte se na [aplikaci Kognitivní služby Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) na GitHubu pro pracovní příklad otočených obdélníků obličeje. Nebo se podívejte na aplikaci [Face HeadPose Sample,](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) která sleduje atribut HeadPose v reálném čase, aby zjistila pohyby hlavy.