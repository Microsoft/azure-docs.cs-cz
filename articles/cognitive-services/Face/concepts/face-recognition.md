---
title: Koncepty rozpoznávání tváří
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje koncepty ověření, najít podobné, skupiny a identifikovat operace rozpoznávání obličejů a základní datové struktury.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743072"
---
# <a name="face-recognition-concepts"></a>Koncepty rozpoznávání tváří

Tento článek vysvětluje koncepty ověření, najít podobné, skupiny a identifikovat operace rozpoznávání obličejů a základní datové struktury. Obecně platí, že rozpoznávání popisuje práci porovnání dvou různých tváří, aby se zjistilo, zda jsou podobné nebo patří stejné osobě.

## <a name="recognition-related-data-structures"></a>Datové struktury související s rozpoznáváním

Operace rozpoznávání používají především následující datové struktury. Tyto objekty jsou uloženy v cloudu a mohou odkazovat podle jejich ID řetězce. Řetězce ID jsou vždy jedinečné v rámci předplatného. Pole názvů lze duplikovat.

|Name (Název)|Popis|
|:--|:--|
|Zjištěná plocha| Tato reprezentace jedné plochy je načtena operací [detekce obličeje.](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) Jeho ID vyprší 24 hodin po jeho vytvoření.|
|Trvalá tvář| Když detectedFace objekty jsou přidány do skupiny, jako je například FaceList nebo Person, stanou persistedFace objekty. Lze je kdykoli [získat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) a nevyprší jejich platnost.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) nebo [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Tato datová struktura je nejrůznější seznam persistedface objekty. FaceList má jedinečné ID, řetězec názvu a volitelně uživatelský datový řetězec.|
|[Person (Osoba)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Tato datová struktura je seznam PersistedFace objekty, které patří do stejné osoby. Má jedinečné ID, řetězec názvu a volitelně uživatelský datový řetězec.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) nebo [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Tato datová struktura je nejrůznější seznam objektů Person. Má jedinečné ID, řetězec názvu a volitelně uživatelský datový řetězec. PersonGroup musí být [vyškoleni](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) před tím, než může být použit v operacích rozpoznávání.|

## <a name="recognition-operations"></a>Operace rozpoznávání

Tato část podrobně popisuje, jak čtyři operace rozpoznávání používají dříve popsané datové struktury. Obecný popis jednotlivých operací rozpoznávání naleznete v tématu [Přehled](../Overview.md).

### <a name="verify"></a>Ověřit

[Operace Ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) přebírá ID plochy z DetectedFace nebo PersistedFace a buď jiné id plochy nebo objekt Person a určuje, zda patří stejné osobě. Pokud předáte v Person objektu, můžete volitelně předat v PersonGroup, do které patří tato osoba ke zlepšení výkonu.

### <a name="find-similar"></a>Najít podobné

Operace [Najít podobné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) trvá ID plochy z DetectedFace nebo PersistedFace a buď FaceList nebo pole jiných id plochy. S FaceList vrátí menší FaceList ploch, které jsou podobné dané plochy. S pole min. ID obličeje podobně vrátí menší pole.

### <a name="group"></a>Skupina

Operace [Skupiny](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) přebírá pole nejrůznějších ID plochy z DetectedFace nebo PersistedFace a vrátí stejná ID seskupená do několika menších polí. Každé pole "skupiny" obsahuje ID obličeje, které se zobrazují podobně. Jediné pole "messyGroup" obsahuje ID plochy, pro které nebyly nalezeny žádné podobnosti.

### <a name="identify"></a>Identifikovat

[Operace Identifikovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) trvá jeden nebo několik id plochy z DetectedFace nebo PersistedFace a PersonGroup a vrátí seznam person objekty, které každá plocha může patřit. Vrácená osoba objekty jsou zabaleny jako Candidate objekty, které mají hodnotu spolehlivosti předpověď.

## <a name="input-data"></a>Vstupní data

Pomocí následujících tipů zajistíte, že vstupní obrázky poskytují co nejpřesnější výsledky rozpoznávání:

* Podporované formáty vstupního obrazu jsou JPEG, PNG, GIF (první snímek), BMP.
* Velikost souboru obrázku by neměla být větší než 4 MB.
* Při vytváření objektů Osoba použijte fotografie, které obsahují různé druhy úhlů a osvětlení.
* Některé tváře nemusí být rozpoznány z důvodu technických problémů, například:
  * Obrazy s extrémním osvětlením, například silné podsvícení.
  * Překážky, které blokují jedno nebo obě oči.
  * Rozdíly v typu vlasů nebo vousy.
  * Změny ve vzhledu obličeje kvůli věku.
  * Extrémní výrazy obličeje.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni s koncepty rozpoznávání tváří, přečtěte si, jak napsat skript, který identifikuje tváře proti trénované PersonGroup.

* [Identifikace tváří v obrázcích](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)