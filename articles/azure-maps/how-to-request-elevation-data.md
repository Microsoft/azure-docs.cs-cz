---
title: Vyžádat data zvýšení oprávnění pomocí služby zvýšení úrovně Azure Maps (Preview)
description: Naučte se požadovat data zvýšení úrovně pomocí služby zvýšení úrovně Azure Maps (Preview).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: af3653d9e4509b1aa31a377dfc22cb6b6b2ff34e
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906059"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service-preview"></a>Vyžádat data zvýšení oprávnění pomocí služby zvýšení úrovně Azure Maps (Preview)

> [!IMPORTANT]
> Služba zvýšení oprávnění Azure Maps je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Služba zvýšení oprávnění](https://docs.microsoft.com/rest/api/maps/elevation) Azure Maps poskytuje rozhraní API pro data zvýšení úrovně dotazu kdekoli na povrchu země. Můžete požadovat ukázková data zvýšení úrovně podél cest, v rámci definovaného ohraničujícího pole nebo v určitých souřadnicích. K načtení dat zvýšení úrovně ve formátu dlaždic můžete také použít [rozhraní vykreslování dlaždice pro vykreslení v2 – získat dlaždici mapy](https://docs.microsoft.com/rest/api/maps/renderv2) . Dlaždice se doručují ve formátu Informat Raster. V tomto článku se dozvíte, jak používat službu zvýšení úrovně Azure Maps a rozhraní API pro dlaždici získat mapu pro vyžádání dat zvýšení úrovně. Data zvýšení oprávnění lze požadovat ve formátech informated JSON i intiff.

## <a name="prerequisites"></a>Předpoklady

1. [Vytvoření účtu Azure Maps v cenové úrovni S1](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.

Další informace o ověřování v Azure Maps [Správa ověřování v Azure Maps](how-to-manage-authentication.md).

Tento článek používá aplikaci [post](https://www.postman.com/) , ale můžete zvolit jiné vývojové prostředí API.

## <a name="request-elevation-data-in-raster-tiled-format"></a>Požádat o data zvýšení úrovně v rastrovém formátu vedle sebe

Chcete-li vyžádat data zvýšení úrovně ve formátu rastrových dlaždic, použijte [rozhraní příkazového okna vykreslení v2 – získat dlaždici mapy](https://docs.microsoft.com/rest/api/maps/renderv2). Pokud lze najít dlaždici, rozhraní API vrátí dlaždici jako intiff. V opačném případě rozhraní API vrátí hodnotu 0. Všechny ikony rastrového rámečku DEM využívají režim země GeoId (na úrovni moře). V tomto příkladu budeme požadovat data zvýšení úrovně pro Mt. Everest.

>[!TIP]
>Chcete-li načíst dlaždici v konkrétní oblasti na mapě světa, je nutné najít správnou dlaždici na příslušné úrovni přiblížení. Všimněte si také, že WorldDEM pokrývá celé globální landmass, ale nepokrývá oceány.  Další informace najdete v tématu [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md).

1. Otevřete aplikaci pro vyúčtování. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **kolekce**.  Pojmenujte kolekci a vyberte tlačítko **vytvořit** .

2. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozím kroku, a pak vyberte **Uložit**.

3. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL pro vyžádání rastrové dlaždice. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. Klikněte na tlačítko **Odeslat** . Měli byste obdržet rastrovou dlaždici, která obsahuje data zvýšení úrovně ve formátu intiff. Každý pixel v nezpracovaných datech dlaždice rastrového data je typu `float` . Hodnota každého pixelu představuje výšku zvýšení úrovně v měřičích.

## <a name="request-elevation-data-in-geojson-format"></a>Vyžádat data zvýšení úrovně ve formátu injson

Použijte rozhraní API služby zvýšení oprávnění (Preview) k vyžádání dat zvýšení úrovně ve formátu. V této části se zobrazí všechna ze tří rozhraní API:

* [Získat data pro body](https://docs.microsoft.com/rest/api/maps/elevation/getdataforlatlongcoordinates)
* [Odeslání dat pro body](https://docs.microsoft.com/rest/api/maps/elevation/postdataforlatlongcoordinates)
* [Získat data pro lomenou čáru](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)
* [Vystavení dat pro lomenou čáru](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline)
* [Získat data pro ohraničovací rámeček](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> Pokud nemůžete vrátit žádná data, vrátí všechna rozhraní API `0` .

### <a name="request-elevation-data-for-points"></a>Požadovat data zvýšení úrovně pro body

V tomto příkladu použijeme [rozhraní API získat data pro body](https://docs.microsoft.com/rest/api/maps/elevation/getdataforlatlongcoordinates) k vyžádání dat zvýšení úrovně na Mt. Everest a Chamlang Mountains. Pak použijeme [data post pro rozhraní API pro body](https://docs.microsoft.com/rest/api/maps/elevation/postdataforlatlongcoordinates) k vyžádání dat zvýšení úrovně pomocí stejných dvou bodů. Očekává se, že Latitudes a délky v adrese URL budou v desítkovém stupni WGS84 (World Geodetic System).

 >[!IMPORTANT]
 >Z důvodu omezení délky znaků adresy URL 2048 není možné předat více než 100 souřadnic jako řetězec oddělený kanálem v žádosti adresy URL GET. Pokud máte v úmyslu předat více než 100 souřadnic jako řetězec oddělený pomocí kanálu, použijte POST data pro body.

1. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozím kroku, a pak vyberte **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. Klikněte na tlačítko **Odeslat** .  Zobrazí se následující odpověď JSON:

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

4. Nyní budeme volat [post data pro rozhraní API bodů](https://docs.microsoft.com/rest/api/maps/elevation/postdataforlatlongcoordinates) a získat tak data zvýšení úrovně pro stejné dva body. Na kartě tvůrce vyberte metodu **post** http a zadejte následující adresu URL. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. V **záhlaví** žádosti **post** nastavte `Content-Type` na `application/json` . V části **text** zadejte informace o souřadnicovém bodu níže. Až skončíte, klikněte na **Odeslat**.

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

### <a name="request-elevation-data-samples-along-a-polyline"></a>Vyžádat si ukázky dat zvýšení úrovně na lomené dráze

V tomto příkladu použijeme příkaz [získat data pro lomenou čáru](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline) k vyžádání pěti rovnoměrně rozmístěných vzorků dat zvýšení oprávnění podél rovné čáry mezi souřadnicemi na Mt. Everest a Chamlang Mountains. Oba souřadnice musí být definovány ve formátu Long/lat. Pokud nezadáte hodnotu `samples` parametru, použije se jako výchozí počet vzorků hodnota 10. Maximální počet vzorků je 2 000.

Pak použijeme data získat data pro lomenou čáru k vyžádání tří rovnoměrně rozmístěných vzorků dat zvýšení úrovně podél cesty. Přesné umístění ukázek definujeme tak, že projdete tři páry souřadnic pro dlouhé/lat.

Nakonec použijeme [data post pro rozhraní API lomené čáry](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) k vyžádání dat zvýšení úrovně na stejných třech rovnoměrně rozložených vzorcích.

Očekává se, že Latitudes a délky v adrese URL budou v desítkovém stupni WGS84 (World Geodetic System).

 >[!IMPORTANT]
 >Z důvodu omezení délky znaků adresy URL 2048 není možné předat více než 100 souřadnic jako řetězec oddělený kanálem v žádosti adresy URL GET. Pokud máte v úmyslu předat více než 100 souřadnic jako řetězec oddělený pomocí kanálu, použijte POST data pro body.

1. Vyberte možnost pro **novou** položku. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** a vyberte kolekci. Klikněte na **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. Klikněte na tlačítko **Odeslat** .  Zobrazí se následující odpověď JSON:

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

4. Nyní si vyžádáme tři vzorky dat zvýšení úrovně v cestě mezi souřadnicemi na přípojných Everest, Chamlang a Jannu Mountains. V oddílu **param** zkopírujte následující pole souřadnic pro hodnotu `lines` klíče dotazu.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. Změňte `samples` hodnotu klíče dotazu na `3` .  Následující obrázek ukazuje nové hodnoty.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="Načte tři ukázky dat zvýšení oprávnění.":::

6. Klikněte na modré tlačítko **Odeslat** . Zobrazí se následující odpověď JSON:

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

7. Nyní budeme volat [post data pro rozhraní API lomené čáry](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) a získat tak data zvýšení úrovně pro tytéž tři body. Na kartě tvůrce vyberte metodu **post** http a zadejte následující adresu URL. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. V **záhlaví** žádosti **post** nastavte `Content-Type` na `application/json` . V části **text** zadejte informace o souřadnicovém bodu níže. Až skončíte, klikněte na **Odeslat**.

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

### <a name="request-elevation-data-by-bounding-box"></a>Vyžádat data zvýšení oprávnění pomocí ohraničovacího boxu

Teď k vyžádání dat zvýšení úrovně v blízkosti Mt použijeme pole [získat data pro ohraničování](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox) . Rainier, WA. Data zvýšení úrovně se vrátí do umístění v rámci ohraničovacího rámečku v rovnoměrně rozmístěných umístěních. Ohraničující oblast definovaná (2) sadami pro tabulky LAT/dlouhých souřadnic (Jižní Zeměpisná šířka, západní Zeměpisná délka | severní šířky, východní délky) je rozdělena do řádků a sloupců. Okraje účtu ohraničujícího pole pro dva (2) řádky a dva (2) sloupce. Pro vrcholy mřížky vytvořené v průsečíkech řádků a sloupců jsou vraceny zvýšení oprávnění. V jednom požadavku se dá vrátit až 2000 zvýšení úrovně oprávnění.

V tomto příkladu zadáme řádky = 3 a sloupce = 6. v odpovědi se vrátí 18 hodnot zvýšení úrovně oprávnění. V následujícím diagramu jsou hodnoty zvýšení úrovně seřazené počínaje rohovém rohem a pak budou v oblasti západní až východní a jižní až Severní.  Body zvýšení oprávnění jsou očíslovány v pořadí, ve kterém jsou vraceny.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="Souřadnice ohraničujícího pole v rozích NE a SE.":::

1. Vyberte možnost pro **novou** položku. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** a vyberte kolekci. Klikněte na **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. Klikněte na modré tlačítko **Odeslat** . 18 ukázek dat zvýšení úrovně, jeden pro každý vrchol mřížky, se vrátí v odpovědi.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-preview-apis-in-azure-maps-control"></a>Ukázky: použití rozhraní API služby zvýšení oprávnění (Preview) v ovládacím prvku Azure Maps

### <a name="get-elevation-data-by-coordinate-position"></a>Získat data zvýšení úrovně podle pozice souřadnic

Následující ukázková webová stránka ukazuje, jak pomocí mapového ovládacího prvku zobrazit data zvýšení úrovně v bodě souřadnic. Když uživatel značku přetáhne, mapa zobrazí data zvýšení úrovně v překryvném okně.

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="Získat zvýšení úrovně na pozici" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na to, že pero <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>získá zvýšení úrovně na pozici</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>Získat data zvýšení úrovně pomocí ohraničovacího boxu

Následující ukázková webová stránka ukazuje, jak pomocí mapového ovládacího prvku zobrazit data zvýšení oprávnění obsažená v ohraničujícím poli. Uživatel definuje ohraničovací rámeček kliknutím na `square` ikonu v levém horním rohu a nakreslí čtverc kdekoli na mapě. Mapový ovládací prvek následně vykreslí data zvýšení úrovně v souladu s barvami, které jsou uvedeny v pravém horním rohu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Zvýšení oprávnění podle ohraničovacího rámečku" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobrazit <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>zvýšení úrovně pera pomocí ohraničujícího rámečku</a> podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>Získat data zvýšení úrovně podle cesty lomené čáry

Následující ukázková webová stránka ukazuje, jak pomocí mapového ovládacího prvku zobrazit data zvýšení úrovně podél cesty. Uživatel definuje cestu kliknutím na `PolyLine` ikonu v levém horním rohu a vykreslení lomené čáry na mapě. Mapový ovládací prvek následně vykreslí data zvýšení úrovně ve barvách, které jsou uvedeny v klíči umístěném v pravém horním rohu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Přechod cesty zvýšení úrovně" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>cestu zvýšení úrovně</a> pera podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další kroky

Další informace o rozhraních API pro zvýšení úrovně oprávnění Azure Maps (Preview) najdete v tématu:

> [!div class="nextstepaction"]
> [Zvýšení oprávnění (Preview) – získat data pro dlouhé souřadnice v tabulce LAT](https://docs.microsoft.com/rest/api/maps/elevation/getdataforlatlongcoordinates)

> [!div class="nextstepaction"]
> [Zvýšení oprávnění (Preview) – získá data pro ohraničovací rámeček.](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Zvýšení oprávnění (Preview) – získat data pro lomenou čáru](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Vykreslování v2 – získat dlaždici mapy](https://docs.microsoft.com/rest/api/maps/renderv2)

Úplný seznam Azure Maps rozhraní REST API najdete v tématu:

> [!div class="nextstepaction"]
> [Rozhraní REST API pro Azure Maps](https://docs.microsoft.com/rest/api/maps/)
