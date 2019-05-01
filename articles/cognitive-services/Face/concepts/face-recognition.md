---
title: Koncepty rozpoznávání tváře
titleSuffix: Azure Cognitive Services
description: Další koncepty o rozpoznávání tváře.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 8c0bf001c2bdbedaa041dbd10b5c7edb08eec4c6
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "64415996"
---
# <a name="face-recognition-concepts"></a>Koncepty rozpoznávání tváře

Tento článek vysvětluje koncepty různé operace rozpoznávání tváře (ověřování, hledání podobně jako seskupování, identifikace) a podkladové datové struktury. Široce rozpoznávání popisuje práci při porovnávání dvou různých tváře k určení, zda jsou podobné nebo patří stejné osobě.

## <a name="recognition-related-data-structures"></a>Rozpoznávání související datové struktury

Operace rozpoznávání používají především následující datové struktury. Tyto objekty jsou uložené v cloudu a lze odkazovat pomocí jeho ID řetězce. ID řetězce se proto vždy jedinečný v rámci předplatného, že pole se jménem, se dají duplikovat.

|Název|Popis|
|:--|:--|
|**DetectedFace**| Toto je načítána pro reprezentaci jediné tváří [rozpoznávání tváře](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) operace. Jeho ID vyprší 24 hodin po jeho vytvoření.|
|**PersistedFace**| Při **DetectedFace** objekty jsou přidány do skupiny (například **FaceList** nebo **osoba**), zobrazí se **PersistedFace** objekty, které můžete být [načíst](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) na libovolné době a nevyprší platnost.|
|**[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)**/**[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)**| Toto je vyčištěný seznam **PersistedFace** objekty. A **FaceList** má jedinečný Identifikátor, řetězec název a volitelně řetězec data uživatele.|
|**[Osoba](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)**| Toto je seznam **PersistedFace** objektů, které patří stejné osobě. Má jedinečný Identifikátor, řetězec název a volitelně řetězec data uživatele.|
|**[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)**/**[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)**| Toto je vyčištěný seznam **osoba** objekty. Má jedinečný Identifikátor, řetězec název a volitelně řetězec data uživatele. A **jeden objekt PersonGroup** musí být [trénovaného](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) předtím, než je možné v operacích rozpoznávání.|

## <a name="recognition-operations"></a>Rozpoznávání operace

Tato část podrobně popisuje, jak použít čtyři operace rozpoznávání výše uvedené datové struktury. Zobrazit [přehled](../Overview.md) široké popis jednotlivých operací rozpoznávání.

### <a name="verification"></a>Ověření

[Ověřte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) operace přebírá face ID (**DetectedFace** nebo **PersistedFace**) a buď jiný face ID nebo **osoba** objekt a Určuje, zda patří stejné osobě. Pokud předáte v **osoba**, můžete volitelně předat **jeden objekt PersonGroup** do které **osoba** patří za účelem zlepšení výkonu.

### <a name="find-similar"></a>Vyhledání podobných

[Vyhledání podobných](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operace přebírá face ID (**DetectedFace** nebo **PersistedFace**) a buď **FaceList** nebo celou řadu dalších tváří ID. S **FaceList**, vrátí menší **FaceList** tváří, které jsou podobné dané tváře. S polem face ID vrátí podobně menší pole.

### <a name="grouping"></a>Seskupování

[Skupiny](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) operace přijímá pole různé face ID (**DetectedFace** nebo **PersistedFace**) a vrátí stejné ID seskupené do několika menších pole. Jednotlivá pole "groups" obsahuje face ID, které se zobrazují podobná a jedné "messyGroup" pole obsahuje face ID pro který nebyly nalezeny žádné podobnosti.

### <a name="identification"></a>Identifikace

[Identifikovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) operace má jednu nebo několik face ID (**DetectedFace** nebo **PersistedFace**) a **jeden objekt PersonGroup** a vrátí seznam z **osoba** objekty, které každou tvář může patřit do. Vrátí **osoba** objekty jsou zabaleny jako **Release Candidate** objekty, které mají hodnotu důvěru předpovědi.

## <a name="input-data"></a>Vstupní data

Chcete-li zajistit, aby vstupní Image nejpřesnější výsledky rozpoznávání použijte následující tipy:

* Formáty podporované vstupního obrázku jsou ve formátu JPEG, PNG, BMP, GIF (první snímek).
* Velikost souboru obrázku by měla být větší než 4 MB.
* Při vytváření **osoba** objektů, měli byste použít fotky, které obsahují celou řadu úhly a osvětlení.
* Některé tváří nemusí být rozpoznané kvůli technické problémy:
  * Image s extrémně osvětlení (například závažnost spodního osvětlení).
  * Překážky blokování oči jedno nebo obě.
  * Rozdíly ve stylu nebo facial vlasů vlasů.
  * Změny v vzhled pro rozpoznávání tváře kvůli stáří.
  * Extrémní výrazů tváře.

## <a name="next-steps"></a>Další postup

Teď, když jste se seznámili s koncepty rozpoznávání tváří, zjistěte, jak napsat jednoduchý skript, který identifikuje tváří proti trénovaného **jeden objekt PersonGroup**.

* [Postup identifikace tváří na obrázcích](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)