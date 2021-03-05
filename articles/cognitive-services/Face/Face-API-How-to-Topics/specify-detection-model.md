---
title: Určení modelu detekce – Face
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak zvolit model detekce obličeje pro použití s aplikací Azure Face.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.custom: devx-track-csharp
ms.openlocfilehash: 04699890af2cfe835ecca6ee983808d7d8d002c8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174185"
---
# <a name="specify-a-face-detection-model"></a>Určení modelu detekce obličeje

V této příručce se dozvíte, jak zadat model detekce obličeje pro službu Azure Face.

Služba obličeje používá modely strojového učení k provádění operací na lidských plochách na obrázcích. I nadále vylepšujeme přesnost našich modelů na základě zpětné vazby od zákazníků a pokrok ve výzkumu a poskytujeme tato vylepšení jako aktualizace modelu. Vývojáři mají možnost určit, kterou verzi modelu detekce obličeje má použít; můžou zvolit model, který nejlépe odpovídá jejich případu použití.

Přečtěte si, kde se dozvíte, jak určit model detekce obličeje v určitých operacích obličeje. Služba obličeje používá detekci obličeje vždy, když převede obrázek plošky na nějakou jinou formu dat.

Pokud si nejste jistí, jestli byste měli použít nejnovější model, přejděte k části [vyhodnotit různé modely](#evaluate-different-models) a vyhodnoťte nový model a porovnejte výsledky pomocí aktuální datové sady.

## <a name="prerequisites"></a>Požadavky

Měli byste být obeznámeni s konceptem rozpoznávání obličeje AI. Pokud ne, přečtěte si koncepční Průvodce rozpoznáváním obličeje nebo průvodce postupy:

* [Koncepce rozpoznávání obličeje](../concepts/face-detection.md)
* [Jak detekovat plošky v obrázku](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detekovat plošky se zadaným modelem

Detekce tváře vyhledá ohraničující umístění lidských plošek a identifikuje jejich vizuální orientační. Extrahuje funkce plochy a ukládá je pro pozdější použití při operacích [rozpoznávání](../concepts/face-recognition.md) .

Když použijete rozhraní API [pro detekci obličeje] , můžete přiřadit verzi modelu s `detectionModel` parametrem. Dostupné hodnoty jsou:

* `detection_01`
* `detection_02`
* `detection_03`

Adresa URL požadavku pro REST API pro [rozpoznávání tváře] bude vypadat takto:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Používáte-li knihovnu klienta, lze hodnotu přiřadit pro `detectionModel` předáním vhodného řetězce. Pokud ho necháte nepřiřazený, rozhraní API použije výchozí verzi modelu ( `detection_01` ). Podívejte se na následující příklad kódu pro klientskou knihovnu rozhraní .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_04", detectionModel: "detection_03");
```

## <a name="add-face-to-person-with-specified-model"></a>Přidat obličej k osobě se zadaným modelem

Služba obličeje může z obrázku extrahovat data z obrázku a přidružit ho k objektu **Person** přes uživatele osoby [– Přidat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) rozhraní API pro rozpoznávání tváře. V tomto volání rozhraní API můžete určit model detekce stejným způsobem jako při [rozpoznávání obličeje].

Podívejte se na následující příklad kódu pro klientskou knihovnu rozhraní .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_03" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_04");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_03");
```

Tento kód vytvoří objekt **Person** s ID `mypersongroupid` a přidá do něj **osobu** . Pak přidá na tuto **osobu** tvář s použitím `detection_03` modelu. Pokud nezadáte parametr *detectionModel* , rozhraní API použije výchozí model, `detection_01` .

> [!NOTE]
> Nemusíte používat stejný model detekce pro všechny plošky v objektu **Person** a nemusíte používat stejný model detekce při zjišťování nových plošek pro porovnání s objektem **Person** (například v rozhraní API pro [identifikaci obličeje] ).

## <a name="add-face-to-facelist-with-specified-model"></a>Přidání obličeje k FaceList se zadaným modelem

Pokud přidáte plošku do existujícího objektu **FaceList** , můžete také určit model detekce. Podívejte se na následující příklad kódu pro klientskou knihovnu rozhraní .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_04");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_03");
```

Tento kód vytvoří **FaceList** s názvem `My face collection` a přidá na něj obličej s `detection_03` modelem. Pokud nezadáte parametr *detectionModel* , rozhraní API použije výchozí model, `detection_01` .

> [!NOTE]
> Nemusíte používat stejný model detekce pro všechny plošky v objektu **FaceList** a nemusíte používat stejný model detekce při zjišťování nových plošek pro porovnání s objektem **FaceList** .

## <a name="evaluate-different-models"></a>Vyhodnotit různé modely

Různé modely detekce tváře jsou optimalizované pro různé úlohy. Přehled rozdílů najdete v následující tabulce.

|**detection_01**  |**detection_02**  |**detection_03** 
|---------|---------|---|
|Výchozí volba pro všechny operace detekce obličeje. | Vydaná v květnu 2019 a volitelně dostupná ve všech operacích detekce obličeje. |  Vydaná v únoru 2021 a volitelně dostupná ve všech operacích detekce obličeje.
|Není optimalizovaná pro malé, boční a rozmazanýé plošky.  | Vylepšená přesnost u malých, bočních a rozmazaných ploch. | Lepší přesnost, včetně menších plošek (64 × 64 pixelů) a otočených orientací obličeje.
|Vrátí hlavní atributy Face (pozice, věk, emoce atd.), pokud jsou zadány ve volání metody Detect. |  Nevrací atributy obličeje.     | Vrátí atributy "faceMask" a "noseAndMouthCovered", pokud jsou zadány ve volání metody Detect.
|Vrátí orientační vzhledy, pokud jsou zadány ve volání metody Detect.   | Nevrací orientační vzhledy.  | Nevrací orientační vzhledy.

Nejlepším způsobem, jak porovnat funkční způsobilost modelů detekce, je použít je pro ukázkovou datovou sadu. Doporučujeme volat rozhraní API pro [rozpoznávání tváře] na celou řadu imagí, zejména obrázky mnoha plošek nebo ploch, které se obtížně zobrazují, a to pomocí každého modelu detekce. Věnujte pozornost počtu ploch, které vrátí každý model.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak určit model detekce, který se má použít u různých rozhraní API pro rozpoznávání tváře. Potom postupujte podle pokynů k rychlému zprovoznění a začněte používat rozpoznávání tváře.

* [Sada Face .NET SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Sada SDK pro tváře v Pythonu](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Tvář – sada SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Face - Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc