---
title: Jak lze vykreslit vlastní data na rastrových map ve službě Azure Maps | Dokumentace Microsoftu
description: Vykreslování vlastních dat na rastrových map ve službě Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 46f08aaa33563f620e7a011620730249e903f7b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794561"
---
# <a name="render-custom-data-on-a-raster-map"></a>Vykreslování vlastních dat na rastrových map

Tento článek vysvětluje, jak používat [statický obrázek služby](https://docs.microsoft.com/rest/api/maps/render/getmapimage) s funkci složení obrázků umožňující překrytí nad rastrových map. Image se skládá možnost získání dlaždice rastrové zpět, s dalšími daty jako vlastní špendlíky popisky a překrytí geometry.

Pokud chcete zobrazit vlastní špendlíky, popisků a Překryvné prvky geometrie, můžete použít aplikaci Postman. Můžete použít Azure Maps [rozhraní API služby Data](https://docs.microsoft.com/rest/api/maps/data) k ukládání a vykreslit překrytí.


## <a name="prerequisites"></a>Požadavky

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps

K dokončení postupu v tomto článku, musíte nejprve [vytvořit účet Azure Maps](how-to-manage-account-keys.md) v cenovou úroveň S1.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Vykreslení špendlíky s popisky a vlastní image

> [!Note]
> Postup v této části vyžaduje účet Azure Maps v cenové úrovni S0 nebo S1.

Azure Maps účtu S0 úroveň podporuje jenom jednu instanci `pins` parametru. Umožňuje vykreslit až o pěti špendlíky určený v požadavku adresa URL, pomocí vlastní image.

Pokud chcete zobrazit špendlíky s popisky a vlastní image, proveďte tyto kroky:

1. Vytvořte kolekci, ve kterých se mají ukládat požadavky. V aplikaci Postman vyberte **nový**. V **vytvořit nový** okně **kolekce**. Název kolekce a vyberte **vytvořit** tlačítko. 

2. Pokud chcete vytvořit žádost, vyberte **nový** znovu. V **vytvořit nový** okně **požádat o**. Zadejte **název žádosti** špendlíky, vyberte kolekce, které jste vytvořili v předchozím kroku jako umístění, ve kterém uložte požadavek a pak vyberte **Uložit**.
    
    ![Vytvořit žádost o v nástroji Postman](./media/tutorial-geofence/postman-new.png)

3. Vyberte metodu GET HTTP na kartě tvůrce a zadejte následující adresu URL k vytvoření žádosti GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Tady je výsledný obraz:

    ![Vlastní ikonu připínáčku s popiskem](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Získání dat z úložiště dat Azure Maps

> [!Note]
> Postup v této části vyžaduje účet Azure Maps cenovou úroveň S1.

Můžete také získat informace o umístění cesty a PIN kód pomocí [API nahrát Data](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Podle následujících pokynů k nahrávání dat. cestu a PIN kódů.

1. V aplikaci Postman otevřete na nové kartě v kolekci, kterou jste vytvořili v předchozí části. Vyberte metodu POST HTTP na kartě tvůrce a zadejte následující adresu URL do požadavku POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Na **Params** kartu, zadejte následující páry klíč/hodnota, které se používají pro adresu URL požadavku POST. Nahraďte `subscription-key` hodnotu s klíči předplatného Azure Maps.
    
    ![Parametry klíč/hodnota v nástroji Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Na **tělo** kartu, vyberte vstupní formát raw a zvolte JSON jako formát vstupu z rozevíracího seznamu. Zadejte tento text JSON jako data k odeslání:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Vyberte **odeslat** a prohlédněte si hlavičky odpovědi. Hlavička umístění obsahuje identifikátor URI použitý k přístupu nebo stáhnout data pro budoucí použití. Také obsahuje jedinečné `udId` pro odesílaná data.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

5. Použití `udId` hodnotu přijatou z API nahrát Data k vykreslení funkce na mapě. Chcete-li to provést, otevřete novou kartu v kolekci, kterou jste vytvořili v předchozí části. Vyberte metodu GET HTTP na kartě tvůrce a zadejte tuto adresu URL pro požadavek GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. Tady je výsledný obraz:

    ![Získání dat z úložiště dat Azure Maps](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Vykreslení mnohoúhelník s barvy a krytí

> [!Note]
> Postup v této části vyžaduje účet Azure Maps cenovou úroveň S1.


Můžete upravit vzhled mnohoúhelník s použitím stylu modifikátory [parametr cesty](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. V aplikaci Postman otevřete na nové kartě v kolekci, kterou jste vytvořili dříve. Vyberte metodu GET HTTP na kartě tvůrce a zadejte následující adresu URL ke konfiguraci požadavek GET na mnohoúhelník s barvy a krytí vykreslení:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

Tady je výsledný obraz:

![Vykreslení neprůhledné mnohoúhelníku](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Vykreslení kruh a špendlíky s vlastními popisky

> [!Note]
> Postup v této části vyžaduje účet Azure Maps cenovou úroveň S1.


Můžete provést připínáčky a jejich popisky zvětšení nebo zmenšení s použitím `sc` modifikátor styl škálování. Tento modifikátor přebírá hodnotu, která je větší než nula. Hodnota 1 je standardní škálování. Hodnoty větší než 1 budou zvětšit kódy PIN a hodnoty menší než 1 budou tyto menší. Další informace o stylu modifikátory najdete v tématu [parametry cesty služby statický obrázek](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Postupujte podle těchto kroků k vykreslení kruh a špendlíky vlastní popisky:

1. V aplikaci Postman otevřete na nové kartě v kolekci, kterou jste vytvořili dříve. Vyberte metodu GET HTTP na kartě tvůrce a zadejte tuto adresu URL pro požadavek GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

Tady je výsledný obraz:

![Vykreslení kroužek s vlastní špendlíky](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Další postup


* Prozkoumejte [Azure Maps získat Image rozhraní API pro mapové](https://docs.microsoft.com/rest/api/maps/render/getmapimage) dokumentaci.
* Další informace o službě Azure Maps Data, najdete v článku [služby dokumentaci](https://docs.microsoft.com/rest/api/maps/data).

