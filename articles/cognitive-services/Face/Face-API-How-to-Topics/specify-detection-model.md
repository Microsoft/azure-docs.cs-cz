---
title: Jak určit model detekce - Plocha
titleSuffix: Azure Cognitive Services
description: V tomto článku se zobrazí, jak si vybrat, který model detekce obličejů se má použít s vaší aplikací Azure Face.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 40ca1dbf981c5a9025cf5a0bac6b007709d69a77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76934582"
---
# <a name="specify-a-face-detection-model"></a>Určení modelu detekce obličeje

Tato příručka ukazuje, jak určit model rozpoznávání tváří pro službu Azure Face.

Služba Face používá modely strojového učení k provádění operací na lidských tvářích v obrazech. Nadále zlepšujeme přesnost našich modelů na základě zpětné vazby od zákazníků a pokroku ve výzkumu a tato vylepšení dodáváme jako aktualizace modelů. Vývojáři mají možnost určit, kterou verzi modelu detekce obličeje chtějí použít; mohou zvolit model, který nejlépe vyhovuje jejich případu použití.

Přečtěte si o tom, jak určit model detekce obličeje v určitých operacích obličeje. Služba Face používá detekci obličeje vždy, když převádí obraz obličeje na jinou formu dat.

Pokud si nejste jisti, zda byste měli použít nejnovější model, přejděte na část [Vyhodnotit různé modely](#evaluate-different-models) a vyhodnotit nový model a porovnat výsledky pomocí aktuální sady dat.

## <a name="prerequisites"></a>Požadavky

Měli byste být obeznámeni s konceptem detekce obličeje AI. Pokud nejste, podívejte se na koncepční příručku pro detekci obličejů nebo návod, jak:

* [Koncepty detekce obličeje](../concepts/face-detection.md)
* [Jak rozpoznat tváře v obraze](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detekce ploch se zadaným modelem

Detekce obličejů najde umístění ohraničovacích rámečků lidských tváří a identifikuje jejich vizuální orientační body. Extrahuje funkce obličeje a ukládá je pro pozdější použití v operacích [rozpoznávání.](../concepts/face-recognition.md)

Při použití [Face - Detect] API, můžete přiřadit `detectionModel` verzi modelu s parametrem. Dostupné hodnoty jsou:

* `detection_01`
* `detection_02`

Adresa URL požadavku pro [rozhraní Face – Detekce] rozhraní REST API bude vypadat takto:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Pokud používáte klientskou knihovnu, můžete `detectionModel` přiřadit hodnotu předáním příslušného řetězce. Pokud jej ponecháte nepřiřazené, rozhraní API`detection_01`použije výchozí verzi modelu ( ). Podívejte se na následující příklad kódu pro knihovnu klienta .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Přidání obličeje osobě se zadaným modelem

Služba Face může extrahovat data obličeje z obrázku a přidružit je k objektu **PersonGroup** prostřednictvím [persongroup person - add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) api. V tomto volání rozhraní API můžete zadat model detekce stejným způsobem jako v [face - detect].

Podívejte se na následující příklad kódu pro knihovnu klienta .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Tento kód vytvoří **PersonGroup** `mypersongroupid` s ID a přidá **osobu** k němu. Pak přidá tvář této **osobě** `detection_02` pomocí modelu. Pokud nezadáte parametr *detectionModel,* rozhraní API použije výchozí `detection_01`model .

> [!NOTE]
> Nemusíte používat stejný model zjišťování pro všechny plochy v objektu **Person** a nemusíte používat stejný model zjišťování při zjišťování nových tváří k porovnání s objektem **Person** [(například] v rozhraní Face – Identifikace rozhraní API).

## <a name="add-face-to-facelist-with-specified-model"></a>Přidání plochy do facelistu se zadaným modelem

Můžete také určit model detekce, když přidáte plochu k existujícímu objektu **FaceList.** Podívejte se na následující příklad kódu pro knihovnu klienta .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Tento kód vytvoří **FaceList** volána `My face collection` a přidá `detection_02` Face k němu s modelem. Pokud nezadáte parametr *detectionModel,* rozhraní API použije výchozí `detection_01`model .

> [!NOTE]
> Nemusíte používat stejný model zjišťování pro všechny plochy v objektu **FaceList** a nemusíte používat stejný model zjišťování při zjišťování nových tváří k porovnání s objektem **FaceList.**

## <a name="evaluate-different-models"></a>Vyhodnocení různých modelů

Různé modely detekce obličeje jsou optimalizovány pro různé úkoly. Přehled rozdílů naleznete v následující tabulce.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Výchozí volba pro všechny operace rozpoznávání obličeje. | Vydáno v květnu 2019 a volitelně k dispozici ve všech operacích detekce obličeje.
|Není optimalizováno pro malé, boční nebo rozmazané plochy.  | Vylepšená přesnost na malých, bočních pohledech a rozmazaných tvářích. |
|Vrátí atributy plochy (póza hlavy, věk, emoce a tak dále), pokud jsou zadány v volání detect. |  Nevrací atributy tváře.     |
|Vrátí orientační body plochy, pokud jsou zadány v detekčním volání.   | Nevrací se do orientačních bodů.  |

Nejlepší způsob, jak porovnat `detection_01` `detection_02` výkony a modely je jejich použití na ukázkové datové sady. Doporučujeme volat [Face – Rozpoznat] rozhraní API na různých obrázcích, zejména obrázky mnoha tváří nebo tváří, které jsou obtížně vidět, pomocí každého modelu detekce. Věnujte pozornost počtu ploch, které každý model vrátí.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak určit model zjišťování pro použití s různými řešení API plochy. Dále postupujte podle rychlého startu a začněte používat detekci obličeje.

* [Plocha .NET SDK](../Quickstarts/csharp-sdk.md)
* [Tvář Python SDK](../Quickstarts/python-sdk.md)
* [Obličej Go SDK](../Quickstarts/go-sdk.md)

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
