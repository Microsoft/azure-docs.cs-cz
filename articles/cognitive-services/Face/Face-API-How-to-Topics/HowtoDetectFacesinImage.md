---
title: Detekovat tyto řezy v bitové kopie s rozhraním API vzhled | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Použijte rozhraní API řez ve kognitivní Services ke zjištění řezy do bitových kopií.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 57cd0915450428399fd680638aa4fae2cdbe17c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342480"
---
# <a name="how-to-detect-faces-in-image"></a>Jak zjistit, kterým čelí v bitové kopii

Tato příručka popisuje, jak ke zjištění řezy z bitové kopie, s atributy vzhled jako pohlaví, stáří nebo pozice extrahovat. Ukázky jsou napsané v C# pomocí klientské knihovny vzhled rozhraní API. 

## <a name="concepts"></a> Koncepty

Pokud nejste obeznámeni s jakoukoli následující koncepty v této příručce, získáte informace definice v našem [Glosář](../Glossary.md) kdykoli: 

- Detekce tváře
- Vzhled zajímavá
- Představovat HEAD
- Vzhled atributy

## <a name="preparation"></a> Příprava

V této ukázce si předvedeme následující funkce: 

- Zjišťování řezy z bitové kopie a označením pomocí obdélníková rámcovacích
- Analýza umístění žáků, nos nebo úst a pak označením v bitové kopii
- Analýza head pozice, pohlaví a stáří písmo

Chcete-li provést tyto funkce, je nutné připravit bitovou kopii s nejméně jeden řez zrušte. 

## <a name="step1"></a> Krok 1: Autorizovat volání rozhraní API

Každé volání rozhraní API vzhled vyžaduje klíč předplatného. Tento klíč musí být buď předána parametr řetězce dotazu nebo zadaným v hlavičce žádosti. Předat klíč předplatného prostřednictvím řetězec dotazu, naleznete na adrese URL žádosti pro [čelí – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) jako příklad:

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

Jako alternativu, klíč předplatného můžete také uvést v hlavičce požadavku HTTP: **ocp-apim-subscription-key: &lt;klíč předplatného&gt;**  při použití klientské knihovny, je předaná klíč předplatného pomocí konstruktoru třídy FaceServiceClient. Příklad:
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step2"></a> Krok 2: Odešlete bitovou kopii do této služby a spusťte řez detekce

Nejzákladnější způsob k provedení zjišťování řez je tím, že nahrajete image přímo. K tomu je potřeba odesílání "POST" požadavku k typu obsahu application/octet-stream s data načtená z ve formátu JPEG. Maximální velikost bitové kopie je 4 MB volného místa.

Pomocí klientské knihovny, detekce vzhled prostřednictvím odesílání provádí předáním do datového proudu objektu. Viz následující příklad:
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

Všimněte si, že metodě DetectAsync FaceServiceClient je asynchronní. Volání metody by měl být označen jako asynchronní také, aby bylo možné použít v klauzuli await.
Pokud bitové kopie již je na webu a s adresou URL, detekce vzhled lze provést také zadáním adresy URL. V tomto příkladu bude textu žádosti řetězec formátu JSON, který obsahuje adresu URL.
Pomocí klientské knihovny, detekce vzhled prostřednictvím adresy URL lze provést snadno pomocí jiné přetížení metody DetectAsync.
```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

Vlastnost FaceRectangle, je vrácen s zjištěné řezy, je v podstatě umístění na písmo v pixelech. Obvykle obsahuje obdélníku očí, obočí, nos a úst – začátek head, uší a chin nejsou zahrnuty. Pokud oříznete dokončení head nebo na výšku střední snímek (fotografií ID typu bitová kopie), můžete chtít rozbalte oblast obdélníková vzhled rámečku, protože oblasti písmo, může být příliš malá pro některé aplikace. Přesněji řečeno, vyhledejte řez pomocí vzhled zajímavá (hledání vzhled funkcí nebo čelí směr mechanismy) popsané v další části bude prokázat být užitečné.

## <a name="step3"></a> Krok 3: Informace o používání zajímavá vzhled

Vzhled zajímavá jsou řadu podrobné bodů na řez; obvykle body součástí vzhled jako žáků, canthus nebo nos. Vzhled zajímavá jsou volitelné atributy, které mohou být analyzovány během zjišťování řez. Při volání metody můžete buď průchodu 'true' jako logická hodnota parametru dotazu returnFaceLandmarks [čelí – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), nebo použijte parametr volitelný returnFaceLandmarks pro třídu FaceServiceClient metoda DetectAsync v pořadí Zahrnout zajímavá vzhled výsledků zjišťování.

Ve výchozím nastavení jsou 27 předdefinované významné body. Následující obrázek ukazuje způsob, jakým jsou definovány všechny body 27:

![HowToDetectFace](../Images/landmarks.1.jpg)

Body vrátil jsou v jednotkách pixelů, stejně jako obdélníková vzhled rámečku. Takže je jednodušší označit konkrétní bodů zájmu v bitové kopii. Následující kód ukazuje, načítání umístění nos a žáci:
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

Kromě označení funkce vzhled obrázku, vzhled zajímavá lze také přesně vypočítat směr písmo. Například můžeme můžete definovat směr tučné vektor z centra úst k centru očí. Následující kód je podrobně toto:

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

Musíte znát směr, který je písmo v, můžete otočit obdélníková vzhled rámečku pro zarovnání s písmo. Je zřejmé, že pomocí vzhled zajímavá může poskytovat další informace a nástroje.

## <a name="step4"></a> Krok 4: Použití dalších atributů vzhled

Kromě zajímavá řez, řez – můžete zjistit rozhraní API také analyzovat několik atributů na řez. Tyto atributy patří:

- Věk
- Pohlaví
- Intenzita smajlíka
- Obličeje kříž
- 3D head pozice

Tyto atributy jsou předpovědět pomocí statistické algoritmů a nemusí být vždy přesné 100 %. Jsou to ale stále užitečné, pokud chcete klasifikovat řezy podle těchto atributů. Další informace o jednotlivých atributů naleznete [Glosář](../Glossary.md).

Dole je jednoduchý příklad extrahování vzhled atributy během zjišťování řez:
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
## <a name="summary"></a> Souhrn

V této příručce jste se naučili funkce [čelí – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rozhraní API, jak může zjistit řezy pro místní nahrán obrázky nebo adresy URL bitové kopie na webu; jak může zjistit řezy vrácením Obdélníkový řez rámce; a jak můžete také analyzovat čelí zajímavá, 3D head představuje a ostatní vzhled atributy.

Další informace o rozhraní API. Podrobnosti naleznete v příručce referenční dokumentace rozhraní API pro [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related"></a> Související témata

[Jak identifikovat tyto řezy v bitové kopii](HowtoIdentifyFacesinImage.md)
