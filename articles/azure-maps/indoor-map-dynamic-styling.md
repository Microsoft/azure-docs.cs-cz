---
title: Implementovat dynamické styly pro mapy vnitřních verzí Azure Maps Creator (Preview)
description: Naučte se implementovat dynamické styly pro mapy vnitřních verzí Creator (Preview).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a23c492d4a81703c0dc6612928a56b5b31d52cae
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726307"
---
# <a name="implement-dynamic-styling-for-creator-preview-indoor-maps"></a>Implementovat dynamické styly pro mapy vnitřních verzí Creator (Preview)

> [!IMPORTANT]
> Služby Azure Maps Creator jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Služba stavu funkcí](/rest/api/maps/featurestate) Azure Maps Creator umožňuje aplikovat styly založené na dynamických vlastnostech funkcí dat mapy vnitřního prvku.  Například můžete vykreslit místnosti pro schůzky zařízení s určitou barvou, která odráží stav obsazení. V tomto článku vám ukážeme, jak dynamicky vykreslovat funkce pro vnitřní mapu pomocí [služby stavu funkcí](/rest/api/maps/featurestate) a [vnitřního webového modulu](how-to-use-indoor-module.md).

## <a name="prerequisites"></a>Požadavky

1. [Vytvoření účtu Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.
3. [Vytvoření prostředku Creator (Preview)](how-to-manage-creator.md)
4. Stáhněte si [vzorový balíček pro kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples).
5. [Vytvořením vnitřních map](tutorial-creator-indoor-maps.md) získáte `tilesetId` a `statesetId` .
6. Sestavte webovou aplikaci podle kroků v tématu [Jak používat modul vnitřní mapy](how-to-use-indoor-module.md).

V tomto kurzu se používá aplikace [po](https://www.postman.com/) aplikaci, ale můžete zvolit jiné vývojové prostředí API.

## <a name="implement-dynamic-styling"></a>Implementovat dynamické styly

Až dokončíte požadavky, měli byste mít nakonfigurované jednoduché webové aplikace s vaším klíčem předplatného, `tilesetId` a `statesetId` .

### <a name="select-features"></a>Výběr funkcí

Chcete-li implementovat dynamické styly, musí být funkce, jako je například schůzka nebo konferenční místnost, odkazována funkcí `id` . Pomocí této funkce `id` aktualizujete dynamickou vlastnost nebo *stav* této funkce. Chcete-li zobrazit funkce definované v datové sadě, můžete použít jednu z následujících metod:

* Rozhraní WFS API (služba webové funkce). K datovým sadám se dá zadat dotaz pomocí rozhraní WFS API. WFS se řídí funkcemi rozhraní API pro Open Geospatial Consortium. Rozhraní WFS API je užitečné pro dotazování funkcí v rámci datové sady. Můžete například použít WFS k vyhledání všech místností pro setkání střední velikosti daného zařízení a úrovně podlahy.

* Implementujte přizpůsobený kód, který uživateli umožňuje vybrat funkce na mapě pomocí webové aplikace. V tomto článku tuto možnost využijeme.  

Následující skript implementuje událost kliknutí myší. Kód načte funkci `id` na základě bodu kliknutí. Do své aplikace můžete vložit kód pod blok kódu správce vnitřních kódů. Spusťte aplikaci a podívejte se do konzoly, abyste získali funkci `id` bodu kliknutí.

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor");

    features.forEach(function (feature) {
        if (feature.layer.id == 'indoor_unit_office') {
            console.log(feature);
        }
    });
});
```

Kurz [vytvoření mapy vnitřních](tutorial-creator-indoor-maps.md) souborů nakonfiguroval funkci stateset, aby přijímala aktualizace stavu pro `occupancy` .

V další části nastavíme *stav* obsazení Office `UNIT26` na `true` . i když `UNIT27` bude sada Office nastavená na `false` .

### <a name="set-occupancy-status"></a>Nastavit stav obsazení

 Teď aktualizujeme stav obou poboček `UNIT26` a `UNIT27` :

1. V aplikaci post vyberte možnost **Nový**. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** a vyberte kolekci. Klikněte na **Uložit**.

2. K aktualizaci stavu použijte [rozhraní API pro stavy aktualizací funkcí](/rest/api/maps/featurestate/updatestatespreview) . Předejte ID stateset a `UNIT26` jednu z obou jednotek. Přidejte svůj klíč předplatného Azure Maps. Tady je adresa URL požadavku **post** , který aktualizuje stav:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID=UNIT26&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. V **záhlaví** žádosti **post** nastavte `Content-Type` na `application/json` . V **těle** žádosti **post** Zapište následující nezpracovaný kód JSON s aktualizacemi funkcí. Tato aktualizace se uloží jenom v případě, že se za časovým razítkem použitým v předchozích požadavcích na aktualizaci stavu funkce pro stejnou funkci ukládá časové razítko `ID` . Pokud chcete aktualizovat svou hodnotu, předejte "obsazené" `keyName` .

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

4. Zopakováním kroků 2 a 3 pomocí použijte `UNIT27` následující JSON.

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>Vizualizace dynamických stylů na mapě

Webová aplikace, kterou jste dříve otevřeli v prohlížeči, by nyní měla odrážet aktualizovaný stav funkcí mapy. `UNIT27`(142) by se mělo zobrazit zelenou a `UNIT26` (143) by se mělo zobrazit červeně.

![Volná místnost v zelených a zaneprázdněných místnostech v Red](./media/indoor-map-dynamic-styling/room-state.png)

[Viz Živá ukázka](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu:

> [!div class="nextstepaction"]
> [Creator (Preview) pro mapování vnitřních verzí](creator-indoor-maps.md)

Viz odkazy na rozhraní API uvedená v tomto článku:

> [!div class="nextstepaction"]
> [Nahrávání dat](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Převod dat](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Integrován](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Sada stavů funkcí](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [Služba WFS](creator-indoor-maps.md#web-feature-service-api)