---
title: Určení modelu zjištění – API pro rozpoznávání tváře
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže jak zvolit který model rozpoznávání tváře detekce pomocí aplikace Azure API pro rozpoznávání tváře.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: dde5623bf5bd579a13fa7271dfba64f9df61bad1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66576694"
---
# <a name="specify-a-face-detection-model"></a>Určení modelu detekce obličeje

Tato příručka obsahuje pokyny pro určení modelu rozpoznávání tváří pro rozhraní API pro rozpoznávání tváře Azure.

Rozhraní API pro rozpoznávání tváře využívá modely strojového učení k provádění operací na lidských tváří na obrázcích. Abychom mohli dále zdokonalovat přesnost naší modely na základě zpětné vazby od zákazníků a pokročilé možnosti výzkumu a budeme poskytovat těmto vylepšením jako aktualizace modelu. Vývojáři mají možnost určit, kterou verzi modelu zjišťování pro rozpoznávání tváře chce se použije. Uživatel může vybrat model, který nejlépe vyhovuje požadavkům jejich případu použití.

Pokračujte ve čtení a zjistěte, jak pro určení modelu rozpoznávání tváří v určitých operací pro rozpoznávání tváře. Rozhraní API pro rozpoznávání tváře využívá rozpoznávání tváře pokaždé, když se převede obrázek tváře na nějaké jiné podobě data.

Pokud si nejste jisti, zda byste měli použít nejnovější model, pokračujte [vyhodnocení různých modelů](#evaluate-different-models) oddílu vyhodnotit nový model a porovnejte výsledky pomocí vašeho aktuální datové sady.

## <a name="prerequisites"></a>Požadavky

Měli byste se seznámit s konceptem rozpoznávání tváře AI. Pokud si nejste, naleznete v tématu Průvodce koncepční zjišťování pro rozpoznávání tváře nebo průvodce:

* [Koncepty zjišťování pro rozpoznávání tváře](../concepts/face-detection.md)
* [Jak rozpoznávat tváře v obrázku](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Rozpoznávání tváří pomocí určeného modelu

Rozpoznávání tváře najde ohraničovací rámeček umístění lidské tváře a identifikuje jejich visual památek. Extrahuje funkce pro rozpoznávání tváře a uloží pro pozdější použití v [rozpoznávání](../concepts/face-recognition.md) operace.

Při použití [Rozpoznávání tváře – zjišťování] rozhraní API, můžete přiřadit verze modelu s `detectionModel` parametru. Dostupné hodnoty jsou:

* `detection_01`
* `detection_02`

Žádost o adresu URL [Rozpoznávání tváře – zjišťování] rozhraní REST API bude vypadat například takto:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]
&subscription-key=<Subscription key>`

Pokud používáte klientskou knihovnu, můžete přiřadit hodnotu `detectionModel` předáním odpovídající řetězec. Pokud je necháte nepřiřazené, rozhraní API bude používat výchozí verze modelu (`detection_01`). Podívejte se na následující příklad kódu klientské knihovny .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Přidání rozpoznávání tváře na osobu pomocí určeného modelu

Můžete extrahovat data pro rozpoznávání tváře z bitové kopie a přidružte jej k rozhraní API pro rozpoznávání tváře **osoba** objektu [jeden objekt PersonGroup uživatele – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) rozhraní API. V tomto volání rozhraní API, můžete zadat zjišťování modelu stejným způsobem jako v [Rozpoznávání tváře – zjišťování].

Podívejte se na následující příklad kódu klientské knihovny .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceServiceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Tento kód vytvoří **jeden objekt PersonGroup** s ID `mypersongroupid` a přidá **osoba** k němu. Potom přidá plochy k tomuto **osoba** pomocí `detection_02` modelu. Pokud nezadáte *detectionModel* parametr, rozhraní API bude používat výchozí model `detection_01`.

> [!NOTE]
> Nemusíte používat stejný model zjišťování pro všechny tváří na **osoba** objektu a není nutné používat stejný model detekce při zjištění nových ploch má být porovnán s **osoba** objekt (v [Face – Identify] rozhraní API, například).

## <a name="add-face-to-facelist-with-specified-model"></a>Přidání rozpoznávání tváře do FaceList pomocí určeného modelu

Zjišťování modelu můžete také určit, když přidáte do existujícího plochy **FaceList** objektu. Podívejte se na následující příklad kódu klientské knihovny .NET.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Tento kód vytvoří **FaceList** volá `My face collection` a přidá do něj s plochy `detection_02` modelu. Pokud nezadáte *detectionModel* parametr, rozhraní API bude používat výchozí model `detection_01`.

> [!NOTE]
> Nemusíte používat stejný model zjišťování pro všechny tváří na **FaceList** objektu a není nutné používat stejný model detekce při zjištění nových ploch má být porovnán s **FaceList** objektu.

## <a name="evaluate-different-models"></a>Vyhodnocení různých modelů

Modely detekce různých tváře jsou optimalizovány pro různé úlohy. V následující tabulce Přehled rozdílů.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Výchozí volba pro všechny operace zjišťování pro rozpoznávání tváře. | Vydáno v květnu 2019 a volitelně portálu všechny operace zjišťování pro rozpoznávání tváře k dispozici.
|Není optimalizovaná pro malé, zobrazení na straně nebo fuzzy tváří.  | Pro zpřesnění na malé, zobrazení na straně a rozmazaně tváří. |
|Pokud jste určili v rozpoznat volání pro rozpoznávání tváře vrací atributy (hlavní póza, věk, pro rozpoznávání emocí a tak dále). |  Nevrátí obličejových atributů.     |
|Pokud jste určili v rozpoznat volání pro rozpoznávání tváře vrací památek.   | Nevrátí orientačních bodů pro rozpoznávání tváře.  |

Nejlepší způsob, jak porovnat výkonů `detection_01` a `detection_02` modely jsou jejich použití v ukázkové datové sadě. Doporučujeme volání [Rozpoznávání tváře – zjišťování] rozhraní API na celou řadu imagí, hlavně obrázky tváří nebo tváří, které se nezobrazují, pomocí každý model zjišťování. Věnujte pozornost počet tváří, které vrací každý model.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak pro určení modelu detekce pomocí různých rozhraní API pro rozpoznávání tváře. V dalším kroku postup rychlého spuštění abyste mohli začít používat rozpoznávání tváře.

* [Rozpoznávání tváří v obrázku (sadu .NET SDK)](../quickstarts/csharp-detect-sdk.md)

[Rozpoznávání tváře – zjišťování]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
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