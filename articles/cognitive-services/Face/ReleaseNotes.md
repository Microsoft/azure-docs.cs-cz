---
title: Poznámky k verzi pro službu vzhled rozhraní API | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Poznámky k verzi rozhraní API služby vzhled zahrnují historii změn verze pro různé verze.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 918b3ea5dbaaa44e4eee1a679354c7becffd4686
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343597"
---
# <a name="face-api-release-notes"></a>Poznámky k verzi rozhraní API vzhled

V tomto článku se vztahují na služby Microsoft vzhled rozhraní API verze 1.0.

### <a name="release-changes-in-may-2018"></a>Verze změny v květnu 2018

* Vylepšené `gender` atribut výrazně i taky vylepšené `age`, `glasses`, `facialHair`, `hair`, `makeup` atributy. Používat je prostřednictvím [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametr. 

* Limit pro velikost souboru vyšší vstupní image z 4 MB volného místa na 6 MB [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup osoba - přidat Vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a [LargePersonGroup osoba - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Verze změny v březnu 2018

* Přidání milionů škálování kontejner: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) a [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Další podrobnosti v [jak používat funkci ve velkém měřítku](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Vyšší [čelí – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` parametr z [1, 5] na [1, 100] a výchozí do 10.

### <a name="release-changes-in-may-2017"></a>Verze změny v květnu 2017

* Přidat `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure`, a `noise` atributy v [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametr.

* 10 tisíc osob v PersonGroup podporovány a [čelí – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Podporované stránkování v [PersonGroup osoba – seznam](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) s volitelné parametry: `start` a `top`.

* Podporované souběžnost v řezy proti různých FaceLists a jiné osoby v PersonGroup přidání nebo odstranění.

### <a name="release-changes-in-march-2017"></a>Verze změny v březnu 2017
* Přidat `emotion` atribut [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametr.

* Opravené tučné nelze znovu rozpoznáno s obdélníku vrácená z [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) jako `targetFace` v [FaceList - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) a [PersonGroup osoba - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Pevná velikost rozpoznat vzhled a ujistěte se, že je výhradně mezi 36 x 36 do 4096 x 4096 pixelů.

### <a name="release-changes-in-november-2016"></a>Změny verze v listopadu 2016
* Přidat předplatné vzhled úložiště Standard ukládá další trvalou řezy při použití [PersonGroup osoba - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) nebo [FaceList - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) pro identifikaci nebo odpovídající podobnosti. Uložené bitové kopie jsou výši 0,5 na 1000 řezy a tato míra je účtovány poměrnou částí každý den. Úroveň Free odběry i nadále omezena na 1 000 celkový osob.

### <a name="release-changes-in-october-2016"></a>Změny vydání v říjnu 2016
* Změnit chybová zpráva z více než jeden setkávají v targetFace z 'Existují více než jeden řez v bitové kopii' do 'Je více než jeden řez v bitové kopii' v [FaceList - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) a [PersonGroup osoba - přidat vzhled](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Verze změny v července 2016
* Podporované řez k ověřování objektu osoby v [čelí – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Přidat volitelné `mode` parametr povolení výběr ze dvou režimů pracovní: `matchPerson` a `matchFace` v [vzhled - Najít podobné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a výchozí hodnota je `matchPerson`.

* Přidat volitelné `confidenceThreshold` parametr pro uživatele nastavit prahovou hodnotu jestli jeden řez patří k objektu osoby v [čelí – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Přidat volitelné `start` a `top` parametry v [PersonGroup - seznamu](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) umožňující uživateli zadat počáteční bod a celkový počet PersonGroups do seznamu.

### <a name="v10-changes-from-v0"></a>Verze 1.0 se změní z V0
* Aktualizovat koncový bod služby root z ```https://westus.api.cognitive.microsoft.com/face/v0/``` k ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Změny u: [čelí – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [čelí – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [vzhled - Najít podobné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a [čelí – skupina](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Aktualizovala se velikost minimální rozpoznat vzhled na 36 x 36 pixelů. Řezy menší než 36 x 36 pixelů nebudou zjištěna.

* Zastaralá data PersonGroup a osoby v V0 řez. Data nelze přistupovat pomocí služby vzhled verze 1.0.

* Zastaralé V0 koncový bod rozhraní API vzhled na 30. června 2016.
