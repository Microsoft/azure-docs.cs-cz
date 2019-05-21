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
ms.openlocfilehash: fa38c492530cb8938e49bc15e13fdd39ed5b6f1c
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890879"
---
# <a name="face-recognition-concepts"></a>Koncepty rozpoznávání tváře

Tento článek vysvětluje koncepty ověřit, Najít podobné, skupiny a identifikovat operace rozpoznávání tváří a podkladové datové struktury. Široce rozpoznávání popisuje práci při porovnávání dvou různých tváře, abyste určují, jestli jsou podobné nebo patří stejné osobě.

## <a name="recognition-related-data-structures"></a>Rozpoznávání související datové struktury

Operace rozpoznávání používají především následující datové struktury. Tyto objekty jsou uložené v cloudu a lze odkazovat pomocí jeho ID řetězce. ID řetězce jsou vždy v rámci předplatného jedinečný. Název pole mohou být duplicitní.

|Název|Popis|
|:--|:--|
|DetectedFace| Načte tento zápis jediné tváří [rozpoznávání tváře](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) operace. Jeho ID vyprší 24 hodin po jeho vytvoření.|
|PersistedFace| DetectedFace objekty jsou přidány do skupiny, jako je například FaceList nebo osoby, budou PersistedFace objekty. Může být [načíst](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) na libovolné době a nevyprší.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) nebo [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Tuto datovou strukturu je vyčištěný seznam PersistedFace objektů. FaceList má jedinečný Identifikátor, řetězec název a volitelně řetězec data uživatele.|
|[Osoba](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Tato struktura dat je seznam PersistedFace objektů, které patří stejné osobě. Má jedinečný Identifikátor, řetězec název a volitelně řetězec data uživatele.|
|[Jeden objekt PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) nebo [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Tuto datovou strukturu je vyčištěný seznam objektů osob. Má jedinečný Identifikátor, řetězec název a volitelně řetězec data uživatele. Musí být jeden objekt PersonGroup [trénovaného](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) předtím, než je možné v operacích rozpoznávání.|

## <a name="recognition-operations"></a>Rozpoznávání operace

Tato část podrobně popisuje použití datových struktur výše popsaný čtyři operace rozpoznávání. Široký popis každé operace rozpoznávání najdete v tématu [přehled](../Overview.md).

### <a name="verify"></a>Ověření

[Ověřte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) operace přebírá face ID z DetectedFace nebo PersistedFace a jiné face ID nebo osoba a určuje, zda patří stejné osobě. Pokud předáte do objektu osoba, můžete volitelně předat jeden objekt PersonGroup, ke kterému patří tato osoba ke zlepšení výkonu.

### <a name="find-similar"></a>Vyhledání podobných

[Najít podobné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operace trvá z DetectedFace nebo PersistedFace a FaceList nebo celou řadu dalších face ID a face ID. S FaceList vrátí menší FaceList tváří, které jsou podobné dané tváře. S polem face ID vrátí podobně menší pole.

### <a name="group"></a>Skupina

[Skupiny](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) operace přijímá pole různé face ID z DetectedFace nebo PersistedFace a vrátí stejné ID seskupené do několika menších pole. Jednotlivá pole "groups" obsahuje ID, které se zobrazují podobná pro rozpoznávání tváře. Pole jednoho "messyGroup" obsahuje face ID pro který nebyly nalezeny žádné podobnosti.

### <a name="identify"></a>Identifikace

[Identifikovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) operace má jednu nebo několik face ID z DetectedFace nebo PersistedFace a jeden objekt PersonGroup a vrátí seznam objektů osob, které každou tvář může patřit do. Vrátí osoba objekty jsou zabaleny jako kandidát objekty, které mají hodnotu důvěru předpovědi.

## <a name="input-data"></a>Vstupní data

Chcete-li zajistit, aby vstupní Image nejpřesnější výsledky rozpoznávání použijte následující tipy:

* Formáty podporované vstupního obrázku jsou ve formátu JPEG, PNG, BMP, GIF (první snímek).
* Velikost souboru obrázku by měla být větší než 4 MB.
* Při vytváření objektů osob použijte fotky, které obsahují různé druhy úhly a světla.
* Některé tváří nemusí rozpoznat kvůli technické komplikace, například:
  * Image s extrémně osvětlení, například závažnost spodního osvětlení.
  * Překážky, které blokují oči jedno nebo obě.
  * Rozdíly v typu nebo facial vlasů vlasů.
  * Změny v tváře vzhled kvůli stáří.
  * Extrémní výrazů tváře.

## <a name="next-steps"></a>Další postup

Teď, když jste obeznámeni s koncepty rozpoznávání tváří, zjistěte, jak napsat skript, který identifikuje tváří proti trénovaného jeden objekt PersonGroup.

* [Identifikace tváří na obrázcích](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)