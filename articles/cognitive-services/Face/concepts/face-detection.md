---
title: Detekce obličeje a atributy koncepty
titleSuffix: Azure Cognitive Services
description: Detekce obličeje je akce lokalizace lidských tváří v obraze a volitelně vrácení různých druhů dat souvisejících s obličejem.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 15e39eb9f5b8dd3556ea9ff8240bc2c9d252cd31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743050"
---
# <a name="face-detection-and-attributes"></a>Detekce obličeje a atributy

Tento článek vysvětluje koncepty rozpoznávání obličeje a dat atributů obličeje. Detekce obličeje je akce lokalizace lidských tváří v obraze a volitelně vrácení různých druhů dat souvisejících s obličejem.

Pomocí [operace Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) můžete detekovat tváře v obraze. Minimálně každý zjištěné plochy odpovídá faceRectangle pole v odpovědi. Tato sada souřadnic obrazových bodů pro levou, horní, šířku a výšku označuje umístěnou plochu. Pomocí těchto souřadnic můžete získat umístění plochy a její velikost. V odpovědi rozhraní API jsou plochy uvedeny v pořadí velikostí od největší po nejmenší.

## <a name="face-id"></a>ID obličeje

ID plochy je jedinečný řetězec identifikátoru pro každou zjištěnou plochu v obraze. Můžete požádat o Face ID v [obličeji – zjistit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) volání rozhraní API.

## <a name="face-landmarks"></a>Orientační body tváře

Orientační body obličeje jsou souborem snadno k nalezení bodů na obličeji, jako jsou žáci nebo špička nosu. Standardně existuje 27 předdefinovaných orientačních bodů. Následující obrázek znázorňuje všech 27 bodů:

![Čelní diagram se všemi 27 orientačními body označenými](../Images/landmarks.1.jpg)

Souřadnice bodů jsou vráceny v jednotkách obrazových bodů.

## <a name="attributes"></a>Atributy

Atributy jsou sada funkcí, které mohou být volitelně detekovány [face - detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. Lze zjistit následující atributy:

* **Věk**. Odhadovaný věk v letech určité tváře.
* **Rozostření**. Rozostření obličeje v obraze. Tento atribut vrátí hodnotu mezi nulou a jedničkou a neformálním hodnocením nízká, střední nebo vysoká.
* **Emoce**. Seznam emocí s jejich detekcí důvěry pro danou tvář. Skóre důvěry jsou normalizovány, a skóre napříč všemi emocemi přidat až jeden. Emoce vrátil jsou štěstí, smutek, neutrální, hněv, opovržení, znechucení, překvapení a strach.
* **Expozice**. Expozice obličeje v obraze. Tento atribut vrátí hodnotu mezi nulou a jednou a neformální hodnocení podExpozice, goodExposure nebo overExposure.
* **Vousy**. Odhadovaná přítomnost vousů a délka daného obličeje.
* **Pohlaví**. Odhadované pohlaví dané tváře. Možné hodnoty jsou mužské, ženské a bezpohlavní.
* **Brýle**. Zda daný obličej má brýle. Možné hodnoty jsou NoGlasses, Brýle na čtení, sluneční brýle a plavecké brýle.
* **Vlasy**. Typ vlasů obličeje. Tento atribut ukazuje, zda jsou vlasy viditelné, zda je zjištěna plešatost a jaké barvy vlasů jsou detekovány.
* **Hlava představovat**. Orientace plochy ve 3D prostoru. Tento atribut je popsán úhly stoupání, náklonu a stákání ve stupních. Rozsahy hodnot jsou -90 stupňů až 90 stupňů, -180 stupňů až 180 stupňů a -90 stupňů až 90 stupňů. Mapování úhlů naleznete v následujícím diagramu:

    ![Hlava s roztečí, role a vyjetých os označených](../Images/headpose.1.jpg)
* **Make-up**. Zda má obličej make-up. Tento atribut vrátí logickou hodnotu pro eyeMakeup a lipMakeup.
* **Hluk**. Vizuální šum detekovaný na obrázku obličeje. Tento atribut vrátí hodnotu mezi nulou a jedničkou a neformálním hodnocením nízká, střední nebo vysoká.
* **Okluze**. Zda jsou objekty, které blokují části plochy. Tento atribut vrátí logickou hodnotu pro eyeOccluded, čeloOccluded a mouthOccluded.
* **Úsměv**. Úsměv výraz dané tváře. Tato hodnota je mezi nulou bez úsměvu a jednou pro jasný úsměv.

> [!IMPORTANT]
> Atributy obličeje jsou předpovězeny pomocí statistických algoritmů. Nemusí být vždy přesné. Při rozhodování na základě dat atributů buďte opatrní.

## <a name="input-data"></a>Vstupní data

Pomocí následujících tipů se ujistěte, že vstupní obrázky poskytují nejpřesnější výsledky zjišťování:

* Podporované formáty vstupního obrazu jsou JPEG, PNG, GIF pro první snímek a BMP.
* Velikost souboru obrázku by neměla být větší než 4 MB.
* Detekovatelný rozsah velikostí obličeje je 36 x 36 až 4096 x 4096 pixelů. Plochy mimo tento rozsah nebudou rozpoznány.
* Některé tváře nemusí být detekovány z důvodu technických problémů. Detekci mohou ovlivnit extrémní úhly obličeje (póza hlavy) nebo okluze obličeje (předměty, jako jsou sluneční brýle nebo ruce, které blokují část obličeje). Čelní a téměř čelní plochy poskytují nejlepší výsledky.

Pokud detekujete tváře z videokanálu, můžete zvýšit výkon úpravou určitých nastavení videokamery:

* **Vyhlazení**: Mnoho videokamer aplikuje efekt vyhlazení. Pokud je to možné, měli byste to vypnout, protože vytváří rozostření mezi snímky a snižuje čistotu.
* **Rychlost závěrky:** Vyšší rychlost závěrky snižuje množství pohybu mezi snímky a zpřehledňuje každý snímek. Doporučujeme rychlost závěrky 1/60 sekundy nebo vyšší.
* **Úhel závěrky**: Některé fotoaparáty určují úhel závěrky namísto rychlosti závěrky. Pokud je to možné, měli byste použít nižší úhel závěrky. Výsledkem bude jasnější snímky videa.

    >[!NOTE]
    > Fotoaparát s nižším úhlem závěrky bude v každém snímku méně světla, takže obraz bude tmavší. Budete muset určit správnou úroveň použití.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni s koncepty detekce obličeje, naučte se psát skript, který detekuje tváře v daném obrázku.

* [Rozpoznávání tváří na obrázku](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)