---
title: Koncepce rozpoznávání tváře a atributů
titleSuffix: Azure Cognitive Services
description: Rozpoznávání tváře je akce hledání lidských plošek v obrázku a volitelně vracení různých druhů dat týkajících se obličeje.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 92b19941f34b9bf5656c9beb188a68d2cf01f674
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92504125"
---
# <a name="face-detection-and-attributes"></a>Detekce a atributy obličeje

Tento článek vysvětluje koncepty rozpoznávání tváře a dat atributu Face. Rozpoznávání tváře je akce hledání lidských plošek v obrázku a volitelně vracení různých druhů dat týkajících se obličeje.

K detekci plošek v obrázku použijte operaci [rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) . Přinejmenším každá zjištěná ploška odpovídá poli faceRectangle v odpovědi. Tato sada souřadnic v pixelech pro levý, horní, šířku a výšku označuje umístěný obličej. Pomocí těchto souřadnic můžete získat polohu obličeje a jeho velikosti. V odpovědi rozhraní API jsou plošky uvedené v pořadí podle velikosti od největších po nejmenší.

## <a name="face-id"></a>ID obličeje

ID obličeje je jedinečný řetězec identifikátoru pro každý zjištěný obličej v obrázku. V volání rozhraní API [pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) můžete požádat o ID obličeje.

## <a name="face-landmarks"></a>Orientační body tváře

Přední orientační body jsou sada snadno hledaných bodů na plošku, jako jsou žákům nebo špička v nos. Standardně existuje 27 předdefinovaných orientačních bodů. Následující obrázek ukazuje všechny 27 bodů:

![Obličejový diagram se všemi 27 orientačními částmi s označením](../Images/landmarks.1.jpg)

Souřadnice bodů jsou vraceny v jednotkách v pixelech.

## <a name="attributes"></a>Atributy

Atributy jsou sadou funkcí, které lze volitelně detekovat pomocí rozhraní API [pro detekci obličeje](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) . Lze zjistit následující atributy:

* **Stáří**. Odhad stáří konkrétního obličeje v letech.
* **Rozostření**. Blurriness obličeje v obrázku. Tento atribut vrací hodnotu od 0 do 1 a neformální hodnocení typu nízká, střední nebo vysoká.
* **Emoce**. Seznam emoce se spolehlivostí detekce pro danou plošku. Hodnocení spolehlivosti je normalizované a skóre všech emoce přidávají až jednu. Vrácenou emoce jsou štěstí, smutek, neutrální, hněv, retempo, znechucení, neočekávaně a strach.
* **Ozáření**. Expozice obličeje v obrázku. Tento atribut vrátí hodnotu mezi 0 a 1 a neformálním hodnocením neoficiálního ozáření, goodExposure nebo nadlimitní hodnoty.
* **Vlasy obličeje**. Odhad přítomnosti obličeje a délky pro daný obličej.
* **Pohlaví** Odhadované pohlaví dané plošky. Možné hodnoty jsou samčí, ženské a žen bez pohlaví.
* **Brýle**. Určuje, zda má daná ploška brýlí. Možné hodnoty jsou nesklad, ReadingGlasses, Sunglasses a brýle.
* **Vlasy**. Typ vlasy obličeje. Tento atribut ukazuje, zda je vlasy viditelné, zda je detekována baldness a jaké barvy vlasů byly zjištěny.
* **Pozice pozice**. Orientace obličeje v prostorovém prostoru. Tento atribut je popsán ve stupních pro rozteč, hod. a Yaw úhly. Rozsahy hodnot jsou-90 stupňů až 90 stupňů,-90 stupňů až 90 stupňů a-90 stupňů až po 90 stupňů v uvedeném pořadí. Podívejte se na následující diagram pro mapování úhlů:

    ![Hlava s Yaw osami rozteč, hod.](../Images/headpose.1.jpg)
* **Strukturu**. Zda se strukturu obličej. Tento atribut vrací logickou hodnotu pro eyeMakeup a lipMakeup.
* **Šum**. Vizuální šum zjištěný na obrázku obličeje. Tento atribut vrací hodnotu od 0 do 1 a neformální hodnocení typu nízká, střední nebo vysoká.
* **Překrytí**. Zda objekty blokující části obličeje. Tento atribut vrací logickou hodnotu pro eyeOccluded, foreheadOccluded a mouthOccluded.
* **Smajlík**. Výraz úsměvu dané plošky Tato hodnota je od nuly žádného úsměvu a jedno pro jasný úsměv.

> [!IMPORTANT]
> Atributy tváře jsou předpovězeny pomocí statistických algoritmů. Nemusí být vždy přesné. Při rozhodování na základě dat atributu buďte opatrní.

## <a name="input-data"></a>Vstupní data

Pomocí následujících tipů se ujistěte, že vstupní image poskytují nejpřesnější výsledky zjišťování:

* Podporované formáty vstupních obrázků jsou JPEG, PNG, GIF pro první snímek a BMP.
* Velikost souboru obrázku by neměla být větší než 6 MB.
* Velikost zjistitelného obličejového rozsahu je 36 × 36 až 4096 × 4096 pixelů. Nezjistí se plošky mimo tento rozsah.
* Některé plošky nemusí být zjištěny z důvodu technických výzev. Extrémní úhly obličeje (hlava pozice) nebo překrytí obličeje (objekty jako Sunglasses nebo ruce, které blokují část plochy) mohou ovlivnit detekci. Nejlepší výsledky poskytují přední a blízko čelních ploch.

Pokud detekujete obličeje z informačního kanálu videa, můžete zlepšit výkon úpravou určitých nastavení na kameře:

* **Vyhlazení**: pro mnoho videí videokamery platí efekt vyhlazení. Tuto možnost byste měli vypnout, pokud to může být způsobeno tím, že vytváří rozostření mezi snímky a snižuje přehlednost.
* **Rychlost expozice**: rychlejší rychlost expozice omezuje pohyb mezi snímky a zpřístupňuje jednotlivé snímky. Doporučujeme, abyste rychlosti závěrů 1/60 sekund nebo rychleji.
* **Úhel rolety**: některé kamery místo rychlosti expozice určují úhel rolety. Pokud je to možné, měli byste použít dolní úhel závěru. Výsledkem bude Vymazání snímků videa.

    >[!NOTE]
    > Fotoaparát s dolním úhlem rolety dostane v každém snímku méně světla, takže obrázek bude tmavší. Musíte určit správnou úroveň, kterou chcete použít.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámení s koncepty rozpoznávání obličeje, se naučíte napsat skript, který detekuje plošky v dané imagi.

* [Rozpoznávání tváří na obrázku](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)