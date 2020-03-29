---
title: Jak určit model rozpoznávání - Plocha
titleSuffix: Azure Cognitive Services
description: V tomto článku se zobrazí, jak zvolit, který model rozpoznávání se má použít s aplikací Azure Face.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76938831"
---
# <a name="specify-a-face-recognition-model"></a>Určení modelu rozpoznávání obličeje

Tato příručka ukazuje, jak určit model rozpoznávání obličeje pro detekci obličeje, identifikaci a hledání podobnosti pomocí služby Azure Face.

Služba Face používá modely strojového učení k provádění operací na lidských tvářích v obrazech. Nadále zlepšujeme přesnost našich modelů na základě zpětné vazby od zákazníků a pokroku ve výzkumu a tato vylepšení dodáváme jako aktualizace modelů. Vývojáři mají možnost určit, kterou verzi modelu rozpoznávání obličeje chtějí použít; mohou zvolit model, který nejlépe vyhovuje jejich případu použití.

Pokud jste nový uživatel, doporučujeme použít nejnovější model. Přečtěte si o tom, jak ji zadat v různých operacích face a zároveň se vyhnout konfliktům modelu. Pokud jste pokročilý uživatel a nejste si jisti, zda byste měli přepnout na nejnovější model, přejděte na část [Vyhodnotit různé modely](#evaluate-different-models) a vyhodnotit nový model a porovnat výsledky pomocí aktuální sady dat.

## <a name="prerequisites"></a>Požadavky

Měli byste být obeznámeni s koncepty detekce a identifikace obličeje ai. Pokud nejste, podívejte se nejprve na tyto návody:

* [Jak rozpoznat tváře v obraze](HowtoDetectFacesinImage.md)
* [Jak identifikovat tváře v obraze](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detekce ploch se zadaným modelem

Detekce obličejů identifikuje vizuální orientační body lidských tváří a najde jejich umístění ohraničovacího rámečku. Také extrahuje rysy obličeje a ukládá je pro použití v identifikaci. Všechny tyto informace tvoří reprezentaci jedné tváře.

Model rozpoznávání se používá při extrahování prvků plochy, takže můžete určit verzi modelu při provádění operace Rozpoznat.

Při použití [Face - Detect] API, přiřadit `recognitionModel` verzi modelu s parametrem. Dostupné hodnoty jsou:

* `recognition_01`
* `recognition_02`

Volitelně můžete zadat parametr _returnRecognitionModel_ (default **false**) k označení, zda by měl být v odpovědi vrácen _model rozpoznávání._ Takže adresa URL požadavku pro [face - detekce] rozhraní REST API bude vypadat takto:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Pokud používáte klientskou knihovnu, můžete `recognitionModel` přiřadit hodnotu předáním řetězce představujícího verzi.
Pokud jej ponecháte nepřiřazený, použije se výchozí verze modelu (_recognition_01_). Podívejte se na následující příklad kódu pro knihovnu klienta .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifikace ploch se zadaným modelem

Služba Face může extrahovat data obličeje z obrázku a přidružit je k objektu **Person** (například prostřednictvím volání rozhraní API [pro rozpoznávání tváře)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a více objektů **Person** lze uložit společně v **PersonGroup**. Potom lze porovnat novou tvář se **skupinou PersonGroup** (s [face - identify] call) a odpovídající osobu v rámci této skupiny lze identifikovat.

**PersonGroup** by měl mít jeden jedinečný model rozpoznávání pro všechny **osoby** `recognitionModel` s a můžete zadat pomocí parametru při vytváření skupiny ([PersonGroup - Create] nebo [LargePersonGroup - Create]). Pokud tento parametr nezadáte, `recognition_01` použije se původní model. Skupina bude vždy používat model rozpoznávání, se kterým byla vytvořena, a nové tváře budou přidruženy k tomuto modelu, když jsou do něj přidány; to nelze změnit po vytvoření skupiny. Chcete-li zjistit, jaký model **PersonGroup** je nakonfigurován s, použijte [PersonGroup - Get] API s _returnRecognitionModel_ parametr nastavit jako **true**.

Podívejte se na následující příklad kódu pro knihovnu klienta .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

V tomto kódu **persongroup** s `mypersongroupid` ID je vytvořen a je nastaven a používat _recognition_02_ modelu extrahovat prvky plochy.

Odpovídajícím způsobem je třeba zadat, který model se má použít při zjišťování tváří k porovnání s touto **PersonGroup** (prostřednictvím [rozhraní Face - Detect] API). Model, který používáte, by měl být vždy konzistentní s konfigurací **PersonGroup.** v opačném případě se operace nezdaří z důvodu nekompatibilní modely.

Neexistuje žádná změna v [Face - Identifikovat] rozhraní API; stačí zadat verzi modelu v detekci.

## <a name="find-similar-faces-with-specified-model"></a>Najít podobné plochy se zadaným modelem

Můžete také určit model rozpoznávání pro hledání podobnosti. Můžete přiřadit verzi modelu `recognitionModel` s při vytváření seznamu tváří s [FaceList - Vytvořit] ROZHRANÍ API nebo [LargeFaceList - Vytvořit]. Pokud tento parametr nezadáte, `recognition_01` použije se původní model. Seznam ploch bude vždy používat model rozpoznávání, se kterým byl vytvořen, a nové tváře budou přidruženy k tomuto modelu, když jsou k němu přidány; to nelze změnit po vytvoření. Chcete-li zjistit, s jakým modelem je seznam obličejů nakonfigurován, použijte [FaceList - Get] API s parametrem _returnRecognitionModel_ jako **true**.

Podívejte se na následující příklad kódu pro knihovnu klienta .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Tento kód vytvoří seznam `My face collection`ploch s názvem , pomocí _modelu recognition_02_ pro extrakci prvku. Když hledáte v tomto seznamu ploch podobné plochy jako nová zjištěná plocha, musí být tato plocha zjištěna ([Plocha - Detekce)]pomocí _recognition_02_ modelu. Stejně jako v předchozí části musí být model konzistentní.

Neexistuje žádná změna v [face - najít podobné] rozhraní API; zadáte pouze verzi modelu v detekci.

## <a name="verify-faces-with-specified-model"></a>Ověření ploch se zadaným modelem

[Face - Ověřit] rozhraní API kontroluje, zda dvě tváře patří stejné osobě. Neexistuje žádná změna v ověřit rozhraní API s ohledem na modely rozpoznávání, ale můžete porovnat pouze plochy, které byly zjištěny se stejným modelem. Takže obě tváře budou muset být detekovány pomocí `recognition_01` nebo `recognition_02`.

## <a name="evaluate-different-models"></a>Vyhodnocení různých modelů

Pokud chcete porovnat výkony _recognition_01_ a _recognition_02_ modely na vašich datech, budete muset:

1. Vytvořte dvě **persongroup**s s _recognition_01_ a _recognition_02_ v uvedeném pořadí.
1. Pomocí obrazových dat můžete rozpoznat tváře a zaregistrovat je na **Person**s pro tyto dvě **persongroup**s a spustit proces školení s [PersonGroup - Train] API.
1. Test s [tváří – identifikujte] se na **obou persongroup**a porovnejte výsledky.

Pokud obvykle zadáte prahovou hodnotu spolehlivosti (hodnotu mezi nulou a hodnotou, která určuje, jak jistý musí být model k identifikaci plochy), bude pravděpodobně nutné použít různé prahové hodnoty pro různé modely. Prahová hodnota pro jeden model není určena pro sdílení do jiného a nemusí nutně vést ke stejným výsledkům.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak určit model rozpoznávání pro použití s různými řešení API služby obličeje. Dále postupujte podle rychlého startu a začněte používat detekci obličeje.

* [Plocha .NET SDK](../Quickstarts/csharp-sdk.md)
* [Tvář Python SDK](../Quickstarts/python-sdk.md)
* [Obličej Go SDK](../Quickstarts/go-sdk.md)

[Face - Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Tvář - Najít podobné]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Plocha - Ověřit]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup – Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Získat]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup – Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup – Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Vytvořit]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Získat]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - vytvořit]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
