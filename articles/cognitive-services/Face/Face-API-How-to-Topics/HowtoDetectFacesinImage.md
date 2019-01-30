---
title: 'Příklad: Rozpoznávání tváří na obrázcích – rozhraní API pro rozpoznávání tváře'
titleSuffix: Azure Cognitive Services
description: Rozpoznejte tváře v obrázcích pomocí rozhraní API pro rozpoznávání tváře.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 8c89a43910a5e98286a82de8626870d3aec55b94
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214207"
---
# <a name="example-how-to-detect-faces-in-image"></a>Příklad: Jak rozpoznávání tváří v obrázku

V této příručce vám ukážeme, jak v obrázku rozpoznat tváře pomocí extrahovaných atributů tváří, jako jsou pohlaví, věk a pozice hlavy. Ukázky jsou napsané v jazyce C# pomocí klientské knihovny rozhraní API pro rozpoznávání tváře. 

## <a name="concepts"></a>Koncepty

Pokud nejste obeznámeni s následujícími koncepty v této příručce, můžete se kdykoli podívat na definice v našem [Glosáři](../Glossary.md): 

- Rozpoznávání tváře
- Orientační body tváře
- Pozice hlavy
- Atributy tváře

## <a name="preparation"></a>Příprava

V tomto příkladu ukážeme následující funkce: 

- Rozpoznání tváří v obrázku a jejich vyznačení pomocí obdélníkových rámců
- Analýza umístění zorniček, nosu a úst a jejich vyznačení v obrázku
- Analýza pozice hlavy, pohlaví a věku tváře

Aby bylo možné tyto funkce provést, je potřeba připravit obrázek s alespoň jednou zřetelnou tváří. 

## <a name="step-1-authorize-the-api-call"></a>Krok 1: Povolit volání rozhraní API

Ke každému volání rozhraní API pro rozpoznávání tváře potřebujete klíč předplatného. Klíč je potřeba předat jako řetězcový parametr dotazu nebo ho uvést v hlavičce žádosti. Pokud chcete klíč předplatného předat pomocí řetězce dotazu, použijte jako příklad adresu URL žádosti pro [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

Jako alternativu, klíč předplatného také dá se zadat v hlavičce požadavku protokolu HTTP: **ocp-apim-subscription-key: &lt;Klíč předplatného&gt;**  při použití klientské knihovny, klíč předplatného se předává v konstruktoru třídy FaceServiceClient. Příklad:
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step-2-upload-an-image-to-the-service-and-execute-face-detection"></a>Krok 2: Nahrajte image do služby a spusťte rozpoznávání tváře

Základním způsobem provádění rozpoznávání tváře je přímé odeslání obrázku. Dělá se to odesláním žádosti POST s typem obsahu aplikace/oktet-stream, s daty vyčtenými z obrázku JPEG. Maximální velikost obrázku jsou 4 MB.

Při použití klientské knihovny se rozpoznávání tváře prostřednictvím odeslání dělá předáním objektu Stream. Viz následující příklad:

```CSharp
using (Stream s = File.OpenRead(@"D:\MyPictures\image1.jpg"))
{
    var faces = await faceServiceClient.DetectAsync(s, true, true);
 
    foreach (var face in faces)
    {
        var rect = face.FaceRectangle;
        var landmarks = face.FaceLandmarks;
    }
}
```

Všimněte si, že metoda DetectAsync třídy FaceServiceClient je asynchronní. Kvůli použití klauzule await musí být volající metoda také označena jako asynchronní.
Pokud je obrázek už na webu a má adresu URL, dá se rozpoznávání tváře provést také poskytnutím adresy URL. V tomto příkladu bude textem žádosti řetězec JSON obsahující adresu URL.
Při použití klientské knihovny se rozpoznávání tváře prostřednictvím adresy URL dá snadno provést dalším přetížením metody DetectAsync.

```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

Vlastnost FaceRectangle, která se vrátí s rozpoznanými tvářemi, je v podstatě tvořená umístěními na tváři v pixelech. Tento obdélník obvykle obsahuje oči, obočí, nos a ústa – vršek hlavy, uši a brada nejsou zahrnuté. Pokud oříznete celkový portrét hlavy nebo portrét do pasu (typ obrázku pro fotografickou identifikaci), může být potřeba rozšířit oblast obdélníkového rámce tváře, protože oblast tváře může být pro některé aplikace příliš malá. K přesnějšímu vyhledání tváře může být užitečné použít orientační body tváře (mechanismy vyhledání rysů tváře nebo směru tváře) popisované v další části.

## <a name="step-3-understanding-and-using-face-landmarks"></a>Krok 3: Principy a použití orientačních bodů pro rozpoznávání tváře

Orientační body tváře jsou řady podrobných bodů na obličeji – obvykle body částí obličeje, jako jsou zorničky, oční koutky a nos. Orientační body tváře jsou volitelné atributy, které můžou být analyzované během rozpoznávání tváře. Abyste zahrnuli orientační body tváře do výsledků rozpoznávání tváře, můžete buď předat logickou hodnotu „true“ do parametru dotazu returnFaceLandmarks při volání [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), nebo použít volitelný parametr returnFaceLandmarks pro metodu DetectAsync třídy FaceServiceClient.

Standardně existuje 27 předdefinovaných orientačních bodů. Následující obrázek ukazuje, jak je všech 27 bodů definovaných:

![Jak se rozpoznává tvář](../Images/landmarks.1.jpg)

Vrácené body jsou v jednotkách pixelů, podobně jako obdélníkový rámec tváře. Jeho vytvořením se proto usnadní vyznačení konkrétních bodů zájmu v obrázku. Následující kód ukazuje získání umístění nosu a zorniček:

```CSharp
var faces = await faceServiceClient.DetectAsync(imageUrl, returnFaceLandmarks:true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
 
    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;
 
    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;
 
    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
``` 

Kromě vyznačení rysů tváře v obrázku můžou orientační body tváře sloužit také k přesnému výpočtu směru tváře. Směr tváře můžeme například definovat jako vektor ze středu úst do středu očí. Podrobněji to vysvětluje následující kód:

```CSharp
var landmarks = face.FaceLandmarks;
 
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

Když znáte směr tváře, můžete natočit obdélníkový rámec tváře tak, aby odpovídal směru tváře. Je zřejmé, že orientační body tváře můžou poskytnout více podrobností a užitku.

## <a name="step-4-using-other-face-attributes"></a>Krok 4: Pomocí dalších atributů pro rozpoznávání tváře

Rozhraní API pro rozpoznávání tváře může kromě orientačních bodů tváře analyzovat také několik dalších atributů tváře. K těmto atributům patří:

- Věk
- Pohlaví
- Intenzita úsměvu
- Vousy
- 3D pozice hlavy

Tyto atributy se předpovídají pomocí statistických algoritmů a nemusí být vždy 100% přesné. Přesto můžou být užitečné, když chcete s jejich pomocí klasifikovat tváře. Další informace o těchto jednotlivých atributech najdete v [Glosáři](../Glossary.md).

Níže je příklad extrakce atributů tváře během rozpoznávání tváře:

```CSharp
var requiredFaceAttributes = new FaceAttributeType[] {
                FaceAttributeType.Age,
                FaceAttributeType.Gender,
                FaceAttributeType.Smile,
                FaceAttributeType.FacialHair,
                FaceAttributeType.HeadPose,
                FaceAttributeType.Glasses
            };
var faces = await faceServiceClient.DetectAsync(imageUrl,
    returnFaceLandmarks: true,
    returnFaceAttributes: requiredFaceAttributes);

foreach (var face in faces)
{
    var id = face.FaceId;
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
}
``` 

## <a name="summary"></a>Souhrn

V této příručce jste se seznámili s funkcemi rozhraní API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) a s tím, jak toto rozhraní může rozpoznat tváře u místně nahraných obrázků nebo u obrázků s adresami URL na webu, jak může rozpoznat tváře vrácením obdélníkových rámců tváří a jak může analyzovat orientační body tváře, 3D pozice hlavy a další atributy tváře.

Další informace o podrobnostech rozhraní API najdete v referenční příručce k [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related-topics"></a>Související témata

[Jak identifikovat tváře v obrázku](HowtoIdentifyFacesinImage.md)
