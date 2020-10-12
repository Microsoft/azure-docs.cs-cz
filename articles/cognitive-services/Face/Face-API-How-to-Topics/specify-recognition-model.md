---
title: Určení modelu rozpoznávání – Face
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak zvolit model rozpoznávání, který se má používat s aplikací Azure Face.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: d250fc005c5760a3eecc2793d02b6f2a9161e663
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91283033"
---
# <a name="specify-a-face-recognition-model"></a>Určení modelu rozpoznávání obličeje

V této příručce se dozvíte, jak zadat model rozpoznávání obličeje pro rozpoznávání tváře, identifikaci a hledání podobnosti pomocí služby Azure Face.

Služba obličeje používá modely strojového učení k provádění operací na lidských plochách na obrázcích. I nadále vylepšujeme přesnost našich modelů na základě zpětné vazby od zákazníků a pokrok ve výzkumu a poskytujeme tato vylepšení jako aktualizace modelu. Vývojáři mají možnost určit, kterou verzi modelu rozpoznávání obličeje má použít; můžou zvolit model, který nejlépe odpovídá jejich případu použití.

Služba Azure Face má k dispozici tři modely rozpoznávání. _Recognition_01_ modelů (publikovaných 2017) a _recognition_02_ (publikovaných 2019) se průběžně podporují, aby se zajistila zpětná kompatibilita pro zákazníky, kteří používají FaceLists nebo **Person**s těmito modely. **FaceList** nebo **osoba** bude vždy používat model rozpoznávání, pomocí kterého byl vytvořen, a při jejich přidání se k tomuto modelu přiřadí nové plošky. Tuto změnu nelze po vytvoření změnit a zákazníci budou muset použít odpovídající model rozpoznávání s odpovídajícím **FaceList** nebo **osobou**.

Můžete přejít na pozdější modely rozpoznávání, a to na vlastní pohodlí; budete ale muset vytvořit nové FaceLists a objektů persongroup s modelem rozpoznávání podle vašeho výběru.

Model _recognition_03_ (publikovaný 2020) je nejpřesnější model, který je aktuálně k dispozici. Pokud jste nový zákazník, doporučujeme použít tento model. _Recognition_03_ zajistí lepší přesnost pro porovnání podobnosti a porovnávání porovnávání s osobami. Všimněte si, že každý model pracuje nezávisle na ostatních a prahová hodnota spolehlivosti pro jeden model není určena k porovnání napříč jinými modely rozpoznávání.

Přečtěte si, kde se dozvíte, jak zadat vybraný model v různých operacích obličeje a vyhnout se konfliktům modelu. Pokud jste pokročilý uživatel a chcete zjistit, jestli byste měli přejít na nejnovější model, přejděte k části [vyhodnotit různé modely](#evaluate-different-models) , abyste vyhodnotili nový model a porovnali výsledky pomocí aktuální datové sady.


## <a name="prerequisites"></a>Požadavky

Měli byste být obeznámeni s koncepty detekce a identifikace obličeje. Pokud ne, přečtěte si tyto příručky jako první:

* [Koncepce rozpoznávání obličeje](../concepts/face-detection.md)
* [Koncepce rozpoznávání obličeje](../concepts/face-recognition.md)
* [Jak detekovat plošky v obrázku](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detekovat plošky se zadaným modelem

Detekce tváře identifikuje vizuální vzhledy lidských plošek a vyhledá jejich umístění s jeho ohraničujícím rámečkem. Také extrahuje funkce plochy a ukládá je pro použití v identifikaci. Všechny tyto informace tvoří reprezentace jedné plošky.

Model rozpoznávání se používá při extrakci funkcí obličeje, takže můžete určit verzi modelu při provádění operace zjišťování.

Při použití rozhraní API [pro detekci obličeje] přiřaďte verzi modelu s `recognitionModel` parametrem. Dostupné hodnoty jsou:
* recognition_01
* recognition_02
* recognition_03


Volitelně můžete zadat parametr _returnRecognitionModel_ (výchozí **hodnota false**), který určuje, zda má být v odpovědi vráceno _recognitionModel_ . Adresa URL požadavku pro REST API pro [rozpoznávání tváře] by tedy vypadala takto:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Používáte-li knihovnu klienta, lze hodnotu přiřadit `recognitionModel` pomocí předání řetězce představujícího verzi. Pokud necháte nepřiřazený, použije se výchozí verze modelu `recognition_01` . Podívejte se na následující příklad kódu pro klientskou knihovnu rozhraní .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifikujte plošky se zadaným modelem

Služba obličeje může z obrázku extrahovat data z obrázku a přidružit ho k objektu **Person** (například prostřednictvím volání rozhraní API pro [Přidání obličeje](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ) a ukládat objekty více **osob** společně ve službě **Person**. Nový obličej se pak dá porovnávat se skupinou **osob** (s voláním [Identifikace obličeje] ) a je možné identifikovat odpovídající osobu v této skupině.

Skupina **osob** by měla mít jeden jedinečný model rozpoznávání pro všechny **osoby**a a při `recognitionModel` vytváření skupiny ([Person-Create] nebo [LargePersonGroup-Create]) můžete tuto možnost zadat pomocí parametru. Pokud tento parametr nezadáte, použije se původní `recognition_01` model. Skupina bude vždy používat model rozpoznávání, pomocí kterého byl vytvořen, a při jejich přidání do tohoto modelu budou přidruženy nové plošky. tuto hodnotu nelze změnit po vytvoření skupiny. Pokud chcete zjistit, ke kterému modelu je nakonfigurované **osoba** , použijte rozhraní [Person-Get] API s parametrem _returnRecognitionModel_ nastaveným na **hodnotu true**.

Podívejte se na následující příklad kódu pro klientskou knihovnu rozhraní .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

V tomto kódu se vytvoří skupinu **Person** s ID `mypersongroupid` a je nastavená na použití _recognition_02ho_ modelu k extrakci funkcí obličeje.

Odpovídajícím způsobem je potřeba určit, který model se má použít při rozpoznávání ploch pro porovnání s touto **osobou** (prostřednictvím rozhraní API [pro detekci obličeje] ). Model, který použijete, by měl vždycky být v souladu s konfigurací objektu **Person**. v opačném případě se operace nezdařila z důvodu nekompatibilních modelů.

V rozhraní API [pro rozpoznávání tváře] se žádná změna nezměnila. Stačí zadat verzi modelu v části detekce.

## <a name="find-similar-faces-with-specified-model"></a>Hledání podobných plošek se zadaným modelem

Můžete také zadat model rozpoznávání pro hledání podle podobnosti. Verzi modelu můžete přiřadit `recognitionModel` při vytváření seznamu obličeje pomocí [FaceList-Create] API nebo [LargeFaceList-Create]. Pokud tento parametr nezadáte, `recognition_01` použije se ve výchozím nastavení model. V seznamu obličeje bude vždy použit model rozpoznávání, pomocí kterého byl vytvořen, a při jejich přidání do seznamu se k tomuto modelu přiřadí nové plošky. tuto změnu nelze po vytvoření změnit. Pokud chcete zjistit, pro který model je seznam obličeje nakonfigurovaný pomocí, použijte rozhraní API [FaceList-Get] s parametrem _returnRecognitionModel_ nastaveným na **hodnotu true**.

Podívejte se na následující příklad kódu pro klientskou knihovnu rozhraní .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_03");
```

Tento kód vytvoří seznam obličeje s názvem `My face collection` pomocí _recognition_03ho_ modelu pro extrakci funkcí. Když vyhledáte tento seznam obličeje pro podobné plošky nově zjištěné plochy, je nutné, aby tato hlava byla detekována ([rozpoznávání obličeje]) pomocí modelu _recognition_03_ . Jak je uvedeno v předchozí části, model musí být konzistentní.

U [podobných] rozhraní API se nezměnila žádná změna. zadáváte pouze verzi modelu v detekci.

## <a name="verify-faces-with-specified-model"></a>Ověřit plošky se zadaným modelem

Rozhraní API pro [ověření tváře] kontroluje, jestli dvě plošky patří stejné osobě. V rozhraní API pro ověřování se nezměnila žádná změna v souvislosti s modely rozpoznávání, ale můžete porovnat pouze ty plošky, které byly zjištěny se stejným modelem.

## <a name="evaluate-different-models"></a>Vyhodnotit různé modely

Pokud chcete porovnat výkon různých modelů rozpoznávání na vlastních datech, budete potřebovat:
1. Vytvořte tři objektů persongroup pomocí _recognition_01_, _recognition_02_a _recognition_03_ .
1. Použijte svá data obrázků k detekci plošek a zaregistrujte je **osobě**s v těchto třech **osobách**. 
1. Objektů persongroup své služby pomocí rozhraní API person-vlak.
1. Test pomocí obličeje – identifikujte všechny tři **osoby**a porovnejte výsledky.


Pokud obvykle zadáte prahovou hodnotu spolehlivosti (hodnota mezi 0 a 1, která určuje, jak jistotu musí model identifikovat obličej), možná budete muset pro různé modely použít jiné prahové hodnoty. Prahová hodnota pro jeden model není určena ke sdílení jiné a nebude nutně vracet stejné výsledky.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak určit model rozpoznávání pro použití s různými rozhraními API služby obličeje. Potom postupujte podle pokynů k rychlému zprovoznění a začněte používat rozpoznávání tváře.

* [Sada Face .NET SDK](../Quickstarts/csharp-sdk.md)
* [Sada SDK pro tváře v Pythonu](../Quickstarts/python-sdk.md)
* [Tvář – sada SDK](../Quickstarts/go-sdk.md)

[Face - Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Plocha – najít podobné]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Tvář – ověření]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup – Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Person – Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup – Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList – vytvořit]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList – získat]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList – vytvořit]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
