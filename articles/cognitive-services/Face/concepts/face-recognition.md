---
title: Koncepce rozpoznávání obličeje
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte o konceptech ověřování, hledání podobných, skupin a identifikaci operací rozpoznávání obličeje a základních datových struktur.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 00dadf8a91b7ed01ab9f91933d296744305a95af
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518802"
---
# <a name="face-recognition-concepts"></a>Koncepce rozpoznávání obličeje

V tomto článku se dozvíte o konceptech ověřování, hledání podobných, skupin a identifikaci operací rozpoznávání obličeje a základních datových struktur. V podstatě rozpoznávání popisuje práci porovnávání dvou různých plošek, aby bylo možné zjistit, zda jsou podobné nebo patří stejné osobě.

## <a name="recognition-related-data-structures"></a>Datové struktury týkající se rozpoznávání

Operace rozpoznávání používají hlavně následující datové struktury. Tyto objekty jsou uložené v cloudu a můžou na ně odkazovat řetězce ID. Řetězce ID jsou vždycky jedinečné v rámci předplatného. Pole názvů je možné duplikovat.

|Název|Popis|
|:--|:--|
|DetectedFace| Tato reprezentace jedné plochy je načítána operací [detekce obličeje](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) . Jeho ID vyprší 24 hodin po jeho vytvoření.|
|PersistedFace| Když se do skupiny přidají objekty DetectedFace, jako je například FaceList nebo person, stanou se objekty PersistedFace. Můžete je kdykoli [načíst](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) a nemusíte vypršet jeho platnost.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) nebo [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Tato datová struktura je seznam roztříděných PersistedFace objektů. FaceList má jedinečné ID, řetězec názvu a volitelně uživatelský Datový řetězec.|
|[Osoba](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Tato datová struktura je seznam objektů PersistedFace, které patří stejné osobě. Má jedinečné ID, řetězec názvu a volitelně uživatelský Datový řetězec.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) nebo [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Tato datová struktura je seznam objektů osob. Má jedinečné ID, řetězec názvu a volitelně uživatelský Datový řetězec. Předtím, než se dá použít při operacích rozpoznávání, musí být [školená](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) osoba.|

## <a name="recognition-operations"></a>Operace rozpoznávání

V této části se dozvíte, jak čtyři operace rozpoznávání používají výše popsané datové struktury. Obecný popis jednotlivých operací rozpoznávání najdete v tématu [Přehled](../Overview.md).

### <a name="verify"></a>Ověření

Operace [ověřování](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) vezme ID obličeje z DetectedFace nebo PersistedFace a buď jiné ID obličeje, nebo objekt Person a určí, zda patří stejné osobě. Pokud předáte objekt person, můžete volitelně předat do objektu person, ke kterému osoba patří, aby vylepšila výkon.

### <a name="find-similar"></a>Najít podobné

Operace [Najít podobná](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) má ID obličeje z DetectedFace nebo PersistedFace a buď FaceList, nebo pole jiných ID obličeje. S FaceList vrátí menší FaceList obličeje, které jsou podobné dané ploškě. S polem ID tváře, podobně vrátí menší pole.

### <a name="group"></a>Group (Skupina)

Operace [skupiny](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) převezme pole roztříděné ID obličeje z DetectedFace nebo PersistedFace a vrátí stejné identifikátory seskupené do několika menších polí. Každé pole "skupiny" obsahuje ID tváře, které se zobrazí jako podobné. Jedno pole "messyGroup" obsahuje ID obličeje, pro které nebyly nalezeny žádné podobnosti.

### <a name="identify"></a>Identifikace

Operace [Identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) přebírá jedno nebo několik ID obličeje z DetectedFace nebo PersistedFace a osoby ze strany a vrátí seznam objektů Person, ke kterým může každá ploška patřit. Vrácené objekty Person jsou zabaleny jako kandidátské objekty, které mají hodnotu spolehlivosti předpovědi.

## <a name="input-data"></a>Vstupní data

Pomocí následujících tipů zajistěte, aby vaše vstupní image poskytovaly nejpřesnější výsledky rozpoznávání:

* Podporované formáty vstupních obrázků jsou JPEG, PNG, GIF (první snímek), BMP.
* Velikost souboru obrázku by neměla být větší než 6 MB.
* Když vytváříte objekty osob, používejte fotky, které mají různé druhy úhlů a osvětlení.
* Některé plošky nemusí být rozpoznané z důvodu technických problémů, jako například:
  * Obrázky s extrémním osvětlením, například vážným světelnou signalizací.
  * Překážky, které blokují jeden nebo oba oči.
  * Rozdíly v typu vlasů nebo vlasy obličeje.
  * Změny v zobrazení obličeje jsou z důvodu stáří.
  * Výrazy pro extrémní obličeje.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámení se základními pojmy rozpoznávání obličeje, můžete napsat skript, který identifikuje plošky s školenými osobami.

* [Rychlý úvod do klientské knihovny](../Quickstarts/client-libraries.md)