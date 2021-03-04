---
title: Použití atributu HeadPose
titleSuffix: Azure Cognitive Services
description: Naučte se používat atribut HeadPose k automatickému otočení obdélníku obličeje nebo k detekci gesta hlavy v informačním kanálu videa.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 02/23/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: cec466fc420e435159e2882d7b39ef6d03eeea7c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733478"
---
# <a name="use-the-headpose-attribute"></a>Použití atributu HeadPose

V této příručce se dozvíte, jak můžete pomocí atributu HeadPose zjištěné plochy povolit některé klíčové scénáře.

## <a name="rotate-the-face-rectangle"></a>Otočení rámečku obličeje

Řez obličeje, vrácený každou zjištěnou plošku, označí umístění a velikost obličeje v obrázku. Ve výchozím nastavení je obdélník vždy zarovnán s obrázkem (jeho strany jsou svislé a vodorovné); To může být neefektivní pro orámování šikmého obličeje. V situacích, kdy chcete v obrázku programově oříznout plošky, je lepší být možné otočit obdélník, který chcete oříznout.

Ukázková aplikace [Cognitive Services obličeje WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) používá pro otočení jeho zjištěných obdélníků HeadPose atribut.

### <a name="explore-the-sample-code"></a>Prozkoumat vzorový kód

Můžete programově otočit obdélník obličeje pomocí atributu HeadPose. Pokud při rozpoznávání ploch určíte tento atribut (viz [Jak detekovat obličeje](HowtoDetectFacesinImage.md)), budete ho moct později dotazovat. Následující metoda z aplikace [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) používá seznam objektů **DetectedFace** a vrací seznam objektů **[obličeje](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** . **Tvář** je vlastní třída, která ukládá data obličeje, včetně aktualizovaných souřadnic obdélníku. Pro **horní**, **levý**, **šířku** a **výšku** jsou vypočítány nové hodnoty a nové pole **FaceAngle** Určuje otočení.

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

### <a name="display-the-updated-rectangle"></a>Zobrazit aktualizovaný obdélník

Odsud můžete v zobrazení použít vrácené objekty **Face** . Následující řádky z [FaceDetectionPage. XAML](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) ukazují, jak je nový obdélník vykreslen z těchto dat:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Detekovat gesta hlav

Pomocí sledování změn HeadPose v reálném čase můžete detekovat gesta hlav, jako je nodding a protřepání. Tuto funkci můžete použít jako vlastní detektor živých funkcí.

Detekce živých je úkol, který určuje, že subjekt je skutečná osoba, ne obrázek nebo video reprezentace. Rozpoznávání gesta hlavice může sloužit jako jeden způsob, jak zajistit živý přehled, zejména na rozdíl od reprezentace obrázku osoby.

> [!CAUTION]
> Chcete-li detekovat gesta hlav v reálném čase, je nutné volat rozhraní API pro rozpoznávání tváře s vysokou sazbou (více než jednou za sekundu). Pokud máte předplatné F0 (Free-úrovně), nebude to možné. Pokud máte předplatné s placenou úrovní, ujistěte se, že jste vypočítali náklady na rychlé volání rozhraní API pro detekci gesta hlav.

Pracovní příklad detekce gesta hlavice najdete na webu GitHub [Sample HeadPose Sample](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) .

## <a name="next-steps"></a>Další kroky

Pracovní příklad otočených obdélníkových obdélníků najdete v tématu [Cognitive Services aplikace WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) na GitHubu. Nebo si přečtěte část [ukázková aplikace Face HeadPose](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) , která sleduje atribut HeadPose v reálném čase k detekci pohybů hlav.