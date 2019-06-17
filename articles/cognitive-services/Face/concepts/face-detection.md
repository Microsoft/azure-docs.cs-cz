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
ms.openlocfilehash: e61048eeab9d7061c18f3237db22fc87ca52f526
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65891165"
---
# <a name="face-detection-and-attributes"></a>Rozpoznávání tváří a atributy

Tento článek vysvětluje koncepty rozpoznávání tváří a data atributu pro rozpoznávání tváře. Rozpoznávání tváří, je proces vyhledání lidské tváře v obrázku a volitelně vrací různé druhy dat týkajících se pro rozpoznávání tváře.

Můžete použít [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) operace rozpoznávání tváří v obrázku. Minimálně každou zjištěnou tvář odpovídá poli faceRectangle v odpovědi. Tato sada souřadnic pixelů pro levé straně, horní, šířku a výšku označit nachází rozpoznávání tváře. Tyto souřadnice můžete získat umístění plochu a jeho velikost. V odpovědi rozhraní API tváří jsou uvedeny v pořadí velikosti od největšího po nejmenší.

## <a name="face-id"></a>ID tváře

Face ID je jedinečný identifikátor řetězce pro každé zjištěné rozpoznávání tváře v obrázku. Můžete požádat o face ID ve vaší [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) volání rozhraní API.

## <a name="face-landmarks"></a>Orientační body tváře

Rozpoznávání tváře památek jsou sady snadno najít body na ciferníku, jako je například žáků nebo tip přední. Standardně existuje 27 předdefinovaných orientačních bodů. Následující obrázek znázorňuje všechny 27 body:

![Diagram pro rozpoznávání tváře s všechny 27 orientačních bodů s popiskem](../Images/landmarks.1.jpg)

Souřadnice bodů jsou vráceny v jednotkách, které pixelů.

## <a name="attributes"></a>Atributy

Atributy jsou sadou funkcí, které lze volitelně můžete zjistit pomocí [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rozhraní API. Můžete zjistit následující atributy:

* **Stáří**. Odhadované stáří v letech konkrétní tváře.
* **Rozostření**. Rozmazání tváří na obrázku. Tento atribut vrátí hodnotu mezi 0 a jedna a neformální hodnocení s nízkou, střední nebo vysoká.
* **Rozpoznávání emocí**. Seznam emoce bez obav jejich zjišťování pro daný typ písma. Skóre spolehlivosti jsou normalizovány a skóre mezi všechny emoce přidat k jedné. Emoce, vrátí se štěstí, smutek, neutrální, hněv, opovržení, znechucení, překvapením a zaregistrují.
* **Vystavení**. Vystavení tvář na obrázku. Tento atribut vrátí hodnotu mezi 0 a jedna a neformální hodnocení underExposure, goodExposure nebo nadměrné.
* **Vousy**. Přítomnost odhadované vousy a délka dané tváře.
* **Pohlaví**. Odhadované pohlaví dané tváře. Možné hodnoty jsou mužského Ženský a genderless.
* **Brýlí**. Určuje, zda je dané tváře brýlí. Možné hodnoty jsou NoGlasses ReadingGlasses, prodejci slunečních brýlí a Motorcycle brýle.
* **Vlasů**. Typ vlasů typ písma. Tento atribut zobrazuje, jestli je viditelný vlasy, určuje, zda je zjištěna baldness a zjištění jaké vlasů barvy.
* **Hlavní představovat**. Orientace tváří v 3D prostoru. Tento atribut je popsán rozteč, vrácení a úhlu natočení úhly ve stupních. Rozsahy hodnot jsou od-90 až 90 stupňů, - 180stupňový rozsah s orientací na 180stupňový rozsah s orientací a stupních od-90 do 90 stupňů. Podívejte se na následující diagram pro úhel mapování:

    ![Head s prvotního, vrátit a yaw popisek osy](../Images/headpose.1.jpg)
* **Strukturu**. Určuje, zda má typ písma strukturu. Tento atribut vrátí logickou hodnotu pro eyeMakeup a lipMakeup.
* **Šumu**. Vizuální šumu v obrázek tváře. Tento atribut vrátí hodnotu mezi 0 a jedna a neformální hodnocení s nízkou, střední nebo vysoká.
* **Uzavření**. Zda existují objekty blokování části plochy. Tento atribut vrátí logickou hodnotu pro eyeOccluded foreheadOccluded a mouthOccluded.
* **Usmívejte**. Výraz úsměv dané tváře. Tato hodnota je mezi 0 pro žádné úsměv a jeden pro smajlíka vymazat.

> [!IMPORTANT]
> Pomocí statistické algoritmy jsou předpokládány obličejových atributů. Nemusí být vždy přesné. Buďte opatrní při rozhodování na základě dat atribut.

## <a name="input-data"></a>Vstupní data

Abyste měli jistotu, že vstupní Image poskytují nejpřesnější výsledky vyhledávání, použijte následující tipy:

* Pro první snímek a BMP jsou formáty podporované vstupního obrázku JPEG, PNG, GIF.
* Velikost souboru obrázku by měla být větší než 4 MB.
* Rozsah velikosti zjistitelné pro rozpoznávání tváře je 36 x 36 do 4096 × 4096 pixelů. Tváří mimo tento rozsah nerozpozná.
* Z důvodu technické komplikace nemusí odhalit některá tváří. Zjišťování může ovlivnit Extreme úhly pro rozpoznávání tváře (hlavní póza) nebo uzavření pro rozpoznávání tváře (objekty například prodejci slunečních brýlí nebo praktické, které blokují součástí typ písma). Přední a téměř čelní strany poskytují nejlepší výsledky.

Pokud jste zjišťování tváří z videa informační kanál, je možné zvýšit výkon, určitých nastavení na vaše kamera:

* **Vyhlazení**: Mnoho videokamer použití vyhlazení efektu. Měli byste vypnout to pokud můžete, protože vytvoří rozostření mezi snímky a snižuje nejasnostem.
* **Příčky rychlost**: Vyšší rychlost příčky snižuje počet pohybu mezi rámce a díky každý snímek srozumitelnější. Doporučujeme, abyste příčky rychlosti 1/60 sekundy nebo rychlejší.
* **Příčky úhel**: Některé kamery zadejte úhel příčky místo expozice. Pokud je to možné byste měli použít nižší úhel příčky. Výsledkem bude jasnější snímky videí.

    >[!NOTE]
    > Kamery s nižší úhel příčky obdrží méně světla v každém rámci tak bude obrázek tmavší. Budete muset určit správnou úroveň používat.

## <a name="next-steps"></a>Další postup

Teď, když jste dobře známé koncepty rozpoznávání tváří, zjistěte, jak napsat skript, který detekuje tváří v danou image.

* [Rozpoznávání tváří v obrázku](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)