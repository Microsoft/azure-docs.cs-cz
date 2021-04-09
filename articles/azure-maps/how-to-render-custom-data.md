---
title: Vykreslovat vlastní data na rastrové mapě | Mapy Microsoft Azure
description: Přečtěte si, jak přidat špendlíky, popisky a geometrické obrazce do rastrové mapy. V této části najdete informace o použití služby statické bitové kopie v Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5c70835c11bafb3fd06645ba51099b33d1eb6149
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96906076"
---
# <a name="render-custom-data-on-a-raster-map"></a>Vykreslovat vlastní data na rastrové mapě

V tomto článku se dozvíte, jak používat [službu statického obrazu](/rest/api/maps/render/getmapimage)s funkcí kompozice obrázků k umožnění překryvů nad rastrovou mapou. Složení obrázku zahrnuje možnost získat rastrovou dlaždici s dalšími daty, jako jsou vlastní špendlíky, popisky a překryvy geometrie.

Chcete-li vykreslit vlastní špendlíky, popisky a překryvy geometrie, můžete použít aplikaci post. [Rozhraní API služby Azure Maps data Service](/rest/api/maps/data) můžete použít k ukládání a vykreslování překryvů.

> [!Tip]
> Použití Azure Maps webové sady SDK je často mnohem výhodnější k zobrazení jednoduché mapy na webové stránce, než aby používala službu statických imagí. Webová sada SDK používá dlaždice mapy a pokud uživatel neposouvá a nezvětšuje mapu, často generuje pouze zlomek transakce na zatížení mapy. Všimněte si, že Azure Maps Web SDK obsahuje možnosti pro vypnutí posouvání a zvětšování. Azure Maps webová sada SDK navíc poskytuje bohatší sadu možností vizualizace dat než webová služba statické mapy.  

## <a name="prerequisites"></a>Požadavky

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps

Pokud chcete dokončit postupy v tomto článku, musíte nejdřív vytvořit účet Azure Maps a získat klíč účtu Maps. Podle pokynů v části [Vytvoření účtu](quick-demo-map-app.md#create-an-azure-maps-account) vytvořte předplatné Azure Maps účtu a podle kroků v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) Získejte primární klíč pro svůj účet. Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Vykreslení připínáček s popisky a vlastní imagí

> [!Note]
> Postup v této části vyžaduje účet Azure Maps v cenové úrovni S0 nebo S1.

Úroveň S0 účtu Azure Maps podporuje pouze jednu instanci `pins` parametru. Umožňuje vykreslit až pět připínáček a zadat v žádosti URL s vlastní imagí.

Chcete-li vykreslit špendlíky s popisky a vlastní image, proveďte tyto kroky:

1. Vytvořte kolekci, do které se mají ukládat požadavky. V aplikaci pro odesílání vyberte **Nový**. V okně **vytvořit nové** vyberte **kolekce**. Pojmenujte kolekci a vyberte tlačítko **vytvořit** . 

2. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** pro špendlíky. Vyberte kolekci, kterou jste vytvořili v předchozím kroku, jako umístění pro uložení žádosti. Pak vyberte **Uložit**.
    
    ![Vytvoření žádosti v post](./media/how-to-render-custom-data/postman-new.png)

3. Na kartě tvůrce vyberte metodu GET HTTP a zadejte následující adresu URL pro vytvoření žádosti o získání.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Tady je výsledný obrázek:

    ![Vlastní připínáček s popiskem](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Získat data z Azure Maps úložiště dat

> [!Note]
> Postup v této části vyžaduje účet Azure Maps v cenové úrovni S1.

Můžete také získat cestu a informace o umístění PIN pomocí [rozhraní API pro nahrání dat](/rest/api/maps/data/uploadpreview). Pomocí následujících kroků nahrajte cestu a data PIN.

1. V aplikaci pro odesílání otevřete novou kartu v kolekci, kterou jste vytvořili v předchozí části. Na kartě tvůrce vyberte metodu POST HTTP a zadejte následující adresu URL, která provede požadavek POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Na kartě **param** zadejte následující páry klíč/hodnota, které se použijí pro adresu URL požadavku POST. Hodnotu nahraďte `subscription-key` klíčovým předplatným Azure Maps.
    
    ![Parametry klíč/hodnota v předzálohovacím](./media/how-to-render-custom-data/postman-key-vals.png)

3. Na kartě **text** vyberte formát nezpracovaného vstupu a v rozevíracím seznamu zvolte formát JSON jako vstupní formát. Zadejte tento JSON jako data, která se mají nahrát:
    
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

4. Vyberte **Odeslat** a zkontrolujte hlavičku odpovědi. Po úspěšné žádosti bude hlavička umístění obsahovat identifikátor URI stavu, aby zkontrolovala aktuální stav žádosti o nahrání. Identifikátor URI stavu by měl být v následujícím formátu.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Zkopírujte identifikátor URI stavu a přidejte do něj parametr klíč předplatného s hodnotou svého klíče předplatného Azure Maps účtu. Použijte stejný klíč předplatného účtu, který jste použili k nahrání dat. Formát identifikátoru URI stavu by měl vypadat takto:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Pokud chcete získat udId, otevřete novou kartu v aplikaci pro odesílání. Na kartě tvůrce vyberte získat metodu HTTP. Vytvořte požadavek GET na identifikátor URI stavu. Pokud se vaše data úspěšně nahrála, obdržíte udId v těle odpovědi. Zkopírujte udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. `udId`K vykreslování funkcí na mapě použijte hodnotu přijatou z rozhraní API pro nahrání dat. Provedete to tak, že otevřete novou kartu v kolekci, kterou jste vytvořili v předchozí části. Na kartě tvůrce vyberte metodu GET HTTP, nahraďte klíč {Subscription-Key} a {udId} hodnotami a zadejte tuto adresu URL, abyste mohli vytvořit požadavek GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Tady je obrázek odpovědi:

    ![Získat data z Azure Maps úložiště dat](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Vykreslení mnohoúhelníku barvou a neprůhledností

> [!Note]
> Postup v této části vyžaduje účet Azure Maps v cenové úrovni S1.


Vzhled mnohoúhelníku lze upravit pomocí modifikátorů stylu s [parametrem cesty](/rest/api/maps/render/getmapimage#uri-parameters).

1. V aplikaci pro odesílání otevřete novou kartu v kolekci, kterou jste vytvořili dříve. Na kartě tvůrce vyberte metodu GET HTTP a zadejte následující adresu URL, abyste nakonfigurovali požadavek GET na vykreslení mnohoúhelníku s barvou a neprůhledností:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Tady je obrázek odpovědi:

    ![Vykreslit neprůhledný mnohoúhelník](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Vykreslit kružnici a špendlíky s vlastními popisky

> [!Note]
> Postup v této části vyžaduje účet Azure Maps v cenové úrovni S1.


Můžete upravit vzhled PIN kódů přidáním modifikátorů stylu. Chcete-li například vytvořit špendlíky a jejich popisky větší nebo menší, použijte `sc` Modifikátor "měřítko stylu". Tento modifikátor přebírá hodnotu, která je větší než nula. Hodnota 1 je standardní stupnice. Hodnoty větší než 1 zajistí větší velikost PIN kódů a hodnoty menší než 1 budou menší. Další informace o modifikátorech stylu najdete v tématu [parametry cesty ke službě statických imagí](/rest/api/maps/render/getmapimage#uri-parameters).


Pomocí těchto kroků můžete vykreslit kružnici a špendlíky s vlastními popisky:

1. V aplikaci pro odesílání otevřete novou kartu v kolekci, kterou jste vytvořili dříve. Na kartě tvůrce vyberte metodu GET HTTP a zadáním této adresy URL vytvořte žádost o získání:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Tady je obrázek odpovědi:

    ![Vykreslení kruhu s vlastními špendlíky](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Chcete-li změnit barvu špendlíků z posledního kroku, změňte modifikátor stylu "co". Prohlédněte `pins=default|la15+50|al0.66|lc003C62|co002D62|` si, aktuální barva bude zadána jako #002D62 v šabloně stylů CSS. Řekněme, že ji chcete změnit na #41d42a. Zapište novou hodnotu barvy za specifikátorem "co", například: `pins=default|la15+50|al0.66|lc003C62|co41D42A|` . Vytvořit novou žádost o získání:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Tady je obrázek odpovědi po změně barev kódů PIN:

    ![Vykreslení kruhu s aktualizovanými špendlíky](./media/how-to-render-custom-data/circle-updated-pins.png)

Podobně můžete změnit, přidat a odebrat ostatní modifikátory stylu.

## <a name="next-steps"></a>Další kroky


* Prozkoumejte dokumentaci k [rozhraní API pro Image mapy Azure Maps získat](/rest/api/maps/render/getmapimage) .
* Další informace o službě Azure Maps data Service (Preview) najdete v [dokumentaci ke službě](/rest/api/maps/data).