---
title: Rozpoznávání tváří a koncepty atributy
titleSuffix: Azure Cognitive Services
description: Další koncepty o rozpoznávání tváří a obličejových atributů.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 3293067190386738efbfeb433bd38453c9e5699f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2019
ms.locfileid: "64572547"
---
# <a name="face-detection-and-attributes"></a>Rozpoznávání tváří a atributy

Tento článek vysvětluje koncepty rozpoznávání tváří a data atributu pro rozpoznávání tváře. Rozpoznávání tváří, je proces vyhledání lidské tváře v obrázku a volitelně vrací různých dat týkajících se pro rozpoznávání tváře.

Můžete použít [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) operace rozpoznávání tváří v obrázku. Minimálně každou zjištěnou bude odpovídat pro rozpoznávání tváře **faceRectangle** pole v odpovědi. Toto je sada souřadnic pixel (vlevo, horní, šířka, výška) označení nachází rozpoznávání tváře. Tyto souřadnice můžete získat umístění plošku, jakož i jeho velikost. V odpovědi rozhraní API tváří jsou uvedeny v pořadí velikosti od největšího po nejmenší.

## <a name="face-id"></a>ID tváře

Face ID je jednoduše řetězec jedinečného identifikátoru pro každý zjištěný rozpoznávání tváře v obrázku. Můžete požádat o face ID ve vaší [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) volání rozhraní API.

## <a name="face-landmarks"></a>Orientační body tváře

Rozpoznávání tváře památek jsou sadu snadno najít body na ciferníku například žáků nebo tip nos. Standardně existuje 27 předdefinovaných orientačních bodů. Následující obrázek znázorňuje všechny 27 body:

![Diagram pro rozpoznávání tváře s všechny 27 orientačních bodů s popiskem](../Images/landmarks.1.jpg)

Souřadnice bodů jsou vráceny v jednotkách, které pixelů.

## <a name="attributes"></a>Atributy

Atributy jsou sadou funkcí další plochu, které lze volitelně můžete zjistit pomocí [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rozhraní API. Níže jsou uvedeny atributy, které lze zjistit:

* **Stáří** odhadované stáří v letech konkrétní tváře.
* **Rozostření** rozmazání tváří na obrázku. Vrátí hodnotu mezi nula a jeden, jakož i neformální hodnocení ("nízká", "střední", "vysoká").
* **Vystavení** vystavení tvář na obrázku. Vrátí hodnotu mezi nula a jeden, jakož i neformální hodnocení, ("underExposure", "goodExposure", "nadměrné").
* **Rozpoznávání emocí** seznam emoce bez obav jejich zjišťování pro daný typ písma. Skóre spolehlivosti jsou normalizovány: skóre mezi všechny emoce přidá k jedné. Emoce, vrátí se štěstí, smutek, neutrální, hněv, opovržení, znechucení, překvapením a zaregistrují.
* **Vousy** přítomnost odhadované vousy a délka dané tváře.
* **Pohlaví** odhadované pohlaví dané tváře. Možné hodnoty jsou "mužského", "Ženský" a "genderless".
* **Brýlí** toho, jestli má brýlí dané tváře. Možné hodnoty jsou "NoGlasses", "ReadingGlasses", "Prodejci slunečních brýlí" a "Motorcycle brýle".
* **Vlasů** vlasy styl typ písma. To ukazuje, jestli je viditelný vlasy, určuje, zda je zjištěna baldness a zjištění jaké vlasů barvy.
* **Hlavní představovat** orientace tváří v 3D prostoru. To je popsán rozteč, vrácení a úhlu natočení úhly ve stupních. Rozsahy hodnot jsou [-90, 90] [-180, 180] a [-90, 90] stupňů v uvedeném pořadí. Podívejte se na následující diagram pro úhel mapování:

    ![Head s prvotního, vrátit a yaw popisek osy](../Images/headpose.1.jpg)
* **Strukturu** toho, jestli má typ písma strukturu. Vrátí logickou hodnotu pro "eyeMakeup" a "lipMakeup".
* **Šumu** visual šumu v obrázek tváře. Vrátí hodnotu mezi nula a jeden, jakož i neformální hodnocení ("nízká", "střední", "vysoká").
* **Uzavření** , zda jsou objekty blokování části plochy. Vrátí logickou hodnotu pro "eyeOccluded", "foreheadOccluded" a "mouthOccluded".
* **Usmívejte** úsměv výrazu dané tváře. Jedná se o hodnotu mezi nula (žádné smajlíka) a jeden (Vymazat smajlíka).

> [!IMPORTANT]
> Obličejových atributů jsou předpokládány pomocí statistické algoritmy a nemusí být přesné. Při rozhodování na základě atributů dat, buďte opatrní.

## <a name="input-data"></a>Vstupní data

Ujistěte se, že vstupní Image poskytují nejpřesnější výsledky vyhledávání pomocí následující tipy:

* Formáty podporované vstupního obrázku jsou ve formátu JPEG, PNG, BMP, GIF (první snímek).
* Velikost souboru obrázku by měla být větší než 4 MB.
* Rozsah velikosti zjistitelné pro rozpoznávání tváře je 36 x 36 do 4096 × 4096 pixelů. Tváří mimo tento rozsah nerozpozná.
* Z důvodu technické komplikace nemusí být detekována některé tváří. Zjišťování může ovlivnit Extreme úhly pro rozpoznávání tváře (hlavní póza) nebo uzavření pro rozpoznávání tváře (objekty například prodejci slunečních brýlí nebo rukou blokování součástí typ písma). Přední a téměř čelní strany poskytují nejlepší výsledky.

## <a name="next-steps"></a>Další postup

Teď, když jste se seznámili s koncepty rozpoznávání tváří, zjistěte, jak napsat jednoduchý skript, který detekuje tváří v danou image.

* [Jak rozpoznávat tváře v obrázku](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)