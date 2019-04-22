---
title: Určení modelu rozpoznávání – rozhraní API pro rozpoznávání tváře
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže jak zvolit který model rozpoznávání pomocí aplikace Azure API pro rozpoznávání tváře.
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: 33348e637143b923719425b9674f99a475d848d9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58806655"
---
# <a name="specify-a-face-recognition-model"></a>Určení modelu rozpoznávání obličeje

Tato příručka ukazuje, jak zadat model rozpoznávání tváří pro rozpoznávání tváře, identifikaci a hledání podobnosti pomocí rozhraní API pro rozpoznávání tváře Azure.

Rozhraní API pro rozpoznávání tváře využívá modely strojového učení k provádění operací na lidských tváří na obrázcích. Abychom mohli dále zdokonalovat přesnost naší modely na základě zpětné vazby od zákazníků a pokročilé možnosti výzkumu a budeme poskytovat těmto vylepšením jako aktualizace modelu. Vývojáři mají možnost určit, kterou verzi model rozpoznávání tváře chce se použije. Uživatel může vybrat model, který nejlépe vyhovuje požadavkům jejich případu použití.

Pokud jste novým uživatelem, doporučujeme že použít na nejnovější model. Pokračujte ve čtení a zjistěte, jak ho zadat v různých operacích pro rozpoznávání tváře při vyhnutí se konfliktům modelu. Pokud jste pokročilý uživatel a nejste jisti, zda byste měli přejít na nejnovější model, pokračujte [vyhodnocení různých modelů](#evaluate-different-models) oddílu vyhodnotit nový model a porovnejte výsledky pomocí vašeho aktuální datové sady.

## <a name="prerequisites"></a>Požadavky

Měli byste se seznámit s koncepty rozpoznávání tváře AI a identifikace. Pokud si nejste, nejprve najdete v těchto návody:

* [Jak rozpoznávat tváře v obrázku](HowtoDetectFacesinImage.md)
* [Postup identifikace tváří v obrázku](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Rozpoznávání tváří pomocí určeného modelu

Rozpoznávání tváří identifikuje visual památek lidské tváře a najde jejich ohraničovací rámeček umístění. Také extrahuje funkce pro rozpoznávání tváře a uloží je pro použití v identifikace. Všechny tyto informace forms reprezentace jednoho pro rozpoznávání tváře.

Model rozpoznávání se používá při extrahování funkcí rozpoznávání tváře, takže můžete určit verzi modelu, při provádění operace rozpoznat.

Při použití [Rozpoznávání tváře – zjišťování] rozhraní API, přiřaďte verze modelu s `recognitionModel` parametru. Dostupné hodnoty jsou:

* `recognition_01`
* `recognition_02`

Volitelně můžete zadat _returnRecognitionModel_ parametr (výchozí **false**) k označení, zda _recognitionModel_ by měla vrátit v odpovědi. Tak žádost o adresu URL [Rozpoznávání tváře – zjišťování] rozhraní REST API bude vypadat například takto:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]
&subscription-key=<Subscription key>`

Pokud používáte klientskou knihovnu, můžete přiřadit hodnotu `recognitionModel` tím, že předáte řetězec představující verze.
Pokud je necháte nepřiřazené, výchozí verze modelu (_recognition_01_) se použije. Podívejte se na následující příklad kódu klientské knihovny .NET.

```csharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifikace tváří pomocí určeného modelu

Můžete extrahovat data pro rozpoznávání tváře z bitové kopie a přidružte jej k rozhraní API pro rozpoznávání tváře **osoba** objektu (prostřednictvím [přidat rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) volat rozhraní API, například) a více **osoba** objekty mohou být uložená společně v **jeden objekt PersonGroup**. Potom můžete porovná nové tváře **jeden objekt PersonGroup** (s [Face – Identify] volání), a lze je identifikovat odpovídající osoba v rámci dané skupiny.

A **jeden objekt PersonGroup** by měl mít jeden model rozpoznávání jedinečné pro všechny **osoba**s kde můžete určit pomocí `recognitionModel` parametr, když vytvoříte skupinu ([PersonGroup – Create] nebo [LargePersonGroup – Create]). Pokud nezadáte tento parametr, původní `recognition_01` model se používá. Skupina bude vždy používat model rozpoznávání, který byl vytvořen a nové tváře se stane po jejich přidání do ní; související s tímto modelem Toto chování nelze změnit po vytvoření skupiny. Zobrazíte jaké modelu **jeden objekt PersonGroup** je nakonfigurovaný, použijte [jeden objekt PersonGroup - Get] API s využitím _returnRecognitionModel_ parametr nastaven jako **true**.

Podívejte se na následující příklad kódu klientské knihovny .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

V tomto kódu **jeden objekt PersonGroup** s ID `mypersongroupid` se vytvoří a je nastavený pro použití _recognition_02_ modelu k extrakci funkce rozpoznávání tváře.

Odpovídajícím způsobem, budete muset zadat které model se má použít při zjišťování tváří pro porovnání to **jeden objekt PersonGroup** (prostřednictvím [Rozpoznávání tváře – zjišťování] rozhraní API). Model použití vždy by měl být konzistentní s **jeden objekt PersonGroup**vaší konfigurace; v opačném případě se operace nezdaří z důvodu nekompatibilní modely.

Není žádná změna v [Face – Identify] rozhraní API, vám stačí zadat verzi modelu při zjišťování.

## <a name="find-similar-faces-with-specified-model"></a>Hledání podobných tváří pomocí určeného modelu

Můžete také zadat model rozpoznávání pro hledání podobnosti. Můžete přiřadit verze modelu s `recognitionModel` při vytváření seznamu pro rozpoznávání tváře [FaceList – vytvořit] rozhraní API nebo [LargeFaceList – vytvořit]. Pokud nezadáte tento parametr, původní `recognition_01` model se používá. Seznam pro rozpoznávání tváře bude vždy používat model rozpoznávání, který byl vytvořen a nové tváře se stane po jejich přidání do ní; související s tímto modelem to není možné po vytvoření změnit. Jaké modelu má nakonfigurovanou pro rozpoznávání tváře seznamu zobrazíte pomocí [FaceList - Get] rozhraní API se _returnRecognitionModel_ parametr nastaven jako **true**.

Podívejte se na následující příklad kódu klientské knihovny .NET.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Tento kód vytvoří seznam pro rozpoznávání tváře, označovaný také `My face collection`, použije _recognition_02_ model pro extrakci funkce. Při hledání pro rozpoznávání tváře seznam podobných tváří na nové tváře zjištěné tuto plošku musí byly zjištěny ([Rozpoznávání tváře – zjišťování]) pomocí _recognition_02_ modelu. Stejně jako v předchozí části musí být konzistentní model.

Není žádná změna v [Rozpoznávání tváře – hledání podobně jako] rozhraní API, můžete zadat jenom při zjišťování verze modelu.

## <a name="verify-faces-with-specified-model"></a>Ověřování tváří zadaného modelu

[Rozpoznávání tváře – ověření] API zkontroluje, jestli dvě tváře patří stejné osobě. Není žádná změna v rozhraní API pro ověření s ohledem na modely pro rozpoznávání, ale lze porovnat pouze tváří, které byly zjištěny pomocí stejného modelu. Ano, tyto dvě tváře obě muset byl zjištěn pomocí `recognition_01` nebo `recognition_02`.

## <a name="evaluate-different-models"></a>Vyhodnocení různých modelů

Pokud chcete porovnat výkonů _recognition_01_ a _recognition_02_ modely na datech, budete muset:

1. Pak vytvoříte další dva **jeden objekt PersonGroup**s _recognition_01_ a _recognition_02_ v uvedeném pořadí.
1. Použít data bitové kopie rozpoznávání tváří a zaregistrujte je do **osoba**s pro tyto dvě **jeden objekt PersonGroup**s a aktivační události na školení zpracovat [PersonGroup – Train] rozhraní API.
1. Testování s platformou [Face – Identify] u obou **jeden objekt PersonGroup**s a porovnejte výsledky.

Pokud zadáte obvykle spolehlivosti prahová hodnota (hodnotu mezi 0 a 1, která určuje, jak jistotu modelu musí být k identifikaci tváře), budete muset používat různé prahové hodnoty různých modelů. Prahovou hodnotu pro jeden model není určené ke sdílení na jiný a nevytvoří nutně stejné výsledky.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak zadat model rozpoznávání pro použití s jinou službu rozhraní API pro rozpoznávání tváře. V dalším kroku postup rychlého spuštění abyste mohli začít používat rozpoznávání tváře.

* [Rozpoznávání tváří v obrázku](../quickstarts/csharp-detect-sdk.md)

[Rozpoznávání tváře – zjišťování]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Rozpoznávání tváře – hledání podobně jako]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Rozpoznávání tváře – ověření]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup – Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Jeden objekt PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup – Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup – Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList – vytvořit]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList – vytvořit]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
