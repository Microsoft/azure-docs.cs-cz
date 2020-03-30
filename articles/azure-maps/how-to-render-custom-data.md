---
title: Vykreslení vlastních dat na rastrové mapě | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak vykreslit vlastní data na rastrové mapě pomocí služby statické image Mapy Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b8d47b69b4aba14c86fb09176b662aee7d5482d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335522"
---
# <a name="render-custom-data-on-a-raster-map"></a>Vykreslení vlastních dat na rastrové mapě

Tento článek vysvětluje, jak používat [službu statického obrazu](https://docs.microsoft.com/rest/api/maps/render/getmapimage), s funkcí kompozice obrazu, aby překryvy nad rastrovou mapou. Kompozice obrazu zahrnuje možnost získat rastrovou dlaždici zpět s dalšími daty, jako jsou vlastní připínáčky, popisky a překryvy geometrie.

Chcete-li vykreslit vlastní překrytí připínáčků, popisků a geometrie, můžete použít aplikaci Postman. [Rozhraní API datové služby](https://docs.microsoft.com/rest/api/maps/data) Azure Maps můžete použít k ukládání a vykreslení překryvy.

> [!Tip]
> Často je mnohem efektivnější použít webovou sadku Azure Maps Web SDK k zobrazení jednoduché mapy na webové stránce než k použití služby statického obrázku. Web SDK používá dlaždice mapy a pokud uživatel posouvá a přibližuje mapu, často vygenerují pouze zlomek transakce na zatížení mapy. Všimněte si, že web Azure Maps SDK má možnosti pro zakázání posouvání a zvětšování. Webová sada Azure Maps navíc poskytuje bohatší sadu možností vizualizace dat než webová služba statické mapy.  

## <a name="prerequisites"></a>Požadavky

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps

Chcete-li dokončit postupy v tomto článku, musíte nejprve vytvořit účet Azure Maps a získat klíč účtu mapy. Postupujte podle pokynů v [tématu Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) k vytvoření předplatného účtu Azure Maps a postupujte podle pokynů v [tématu získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) k získání primárního klíče pro váš účet. Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](./how-to-manage-authentication.md).


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Vykreslení přilnavých špendlíků s popisky a vlastním obrázkem

> [!Note]
> Postup v této části vyžaduje účet Azure Maps v cenové úrovni S0 nebo S1.

Úroveň účtu Azure Maps S0 podporuje jenom jednu instanci parametru. `pins` To vám umožní vykreslit až pět pinů, zadaných v požadavku URL, s vlastním obrázkem.

Chcete-li vykreslit připínáčky s popisky a vlastním obrázkem, proveďte tyto kroky:

1. Vytvořte kolekci, do které chcete uložit požadavky. V aplikaci Pošťák vyberte **Nový**. V okně **Vytvořit novou** vyberte **kolekci**. Pojmenujte kolekci a vyberte tlačítko **Vytvořit.** 

2. Chcete-li vytvořit požadavek, vyberte znovu **Nový.** V okně **Vytvořit nový** vyberte **Request**. Zadejte **název požadavku** pro připěňky. Vyberte kolekci, kterou jste vytvořili v předchozím kroku, jako umístění pro uložení požadavku. Potom vyberte **Uložit**.
    
    ![Vytvoření požadavku v Pošťákovi](./media/how-to-render-custom-data/postman-new.png)

3. Vyberte metodu GET HTTP na kartě tvůrce a zadejte následující adresu URL pro vytvoření požadavku GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Zde je výsledný obrázek:

    ![Vlastní připínáček s popiskem](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Získání dat z datového úložiště Azure Maps

> [!Note]
> Postup v této části vyžaduje účet Azure Maps v cenové úrovni S1.

Informace o poloze cesty a špendlíku můžete také získat pomocí [rozhraní DATA Upload API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Podle následujících kroků nahrajte data cesty a připnutí.

1. V aplikaci Pošťák otevřete novou kartu v kolekci, kterou jste vytvořili v předchozí části. Vyberte metodu POST HTTP na kartě tvůrce a zadejte následující adresu URL pro vytvoření požadavku POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Na kartě **Params** zadejte následující páry klíč/hodnota, které se používají pro adresu URL požadavku POST. Nahraďte hodnotu `subscription-key` klíčem předplatného Azure Maps.
    
    ![Key/value params v Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Na kartě **Body** vyberte nezpracovaný vstupní formát a z rozevíracího seznamu zvolte JSON jako vstupní formát. Zadejte tento JSON jako data, která mají být nahrána:
    
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

4. Vyberte **Odeslat** a zkontrolujte záhlaví odpovědi. Na základě úspěšné žádosti bude hlavička Umístění obsahovat identifikátor URI stavu, který zkontroluje aktuální stav požadavku na nahrání. Identifikátor URI stavu by měl následující formát.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Zkopírujte svůj stavURI a připojit parametr klíče předplatného k němu s hodnotou klíče předplatného účtu Azure Maps. Použijte stejný klíč předplatného účtu, který jste použili k nahrání dat. Formát identifikátoru URI stavu by měl vypadat jako formát následující:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Chcete-li získat udId, otevřete novou kartu v aplikaci Postman. Na kartě tvůrce vyberte metodu GET HTTP. Vytvořte požadavek GET na identifikátorURI stavu. Pokud bylo nahrání dat úspěšné, obdržíte v těle odpovědi udId. Zkopírujte udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Pomocí `udId` hodnoty přijaté z rozhraní API pro nahrávání dat vykreslujte funkce na mapě. Chcete-li tak učinit, otevřete novou kartu v kolekci, kterou jste vytvořili v předchozím oddílu. Vyberte metodu GET HTTP na kartě tvůrce, nahraďte hodnoty {subscription-key} a {udId} a zadejte tuto adresu URL pro vytvoření požadavku GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Zde je odpověď obrázek:

    ![Získání dat z datového úložiště Azure Maps](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Vykreslení polygonu s barvou a krytím

> [!Note]
> Postup v této části vyžaduje účet Azure Maps v cenové úrovni S1.


Vzhled polygonu můžete upravit pomocí modifikátorů stylu s [parametrem cesty](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. V aplikaci Postman otevřete novou kartu v kolekci, kterou jste vytvořili dříve. Na kartě tvůrce vyberte metodu GET HTTP a zadejte následující adresu URL pro konfiguraci požadavku GET pro vykreslení polygonu s barvou a krytím:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Zde je odpověď obrázek:

    ![Vykreslení neprůhledného polygonu](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Vykreslení kruhu a připínáčků pomocí vlastních popisků

> [!Note]
> Postup v této části vyžaduje účet Azure Maps v cenové úrovni S1.


Vzhled kolíků můžete upravit přidáním modifikátorů stylu. Chcete-li například zvětšit připínání a `sc` jejich popisky, použijte modifikátor "styl měřítka". Tento modifikátor má hodnotu, která je větší než nula. Standardní stupnice je hodnota 1. Hodnoty větší než 1 zvětší kolíky a hodnoty menší než 1 je zmenší. Další informace o modifikácích stylu naleznete v [tématu parametry cesty služby statického obrazu](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Chcete-li vykreslit kruh a připínáčky s vlastními popisky, postupujte takto:

1. V aplikaci Postman otevřete novou kartu v kolekci, kterou jste vytvořili dříve. Vyberte metodu GET HTTP na kartě tvůrce a zadejte tuto adresu URL, chcete-li vytvořit požadavek GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Zde je odpověď obrázek:

    ![Vykreslení kruhu pomocí vlastních připínáčků](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Chcete-li změnit barvu připínáčků z posledního kroku, změňte modifikátor stylu "co". Podívejte `pins=default|la15+50|al0.66|lc003C62|co002D62|`se na , aktuální barva by byla zadána jako #002D62 v CSS. Řekněme, že to chcete změnit na #41d42a. Za specifikátor "co" napište novou hodnotu `pins=default|la15+50|al0.66|lc003C62|co41D42A|`barvy, například takto: . Vytvořte nový požadavek GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Zde je odezva obrázek po změně barvy kolíků:

    ![Vykreslení kruhu s aktualizovanými připínáčky](./media/how-to-render-custom-data/circle-updated-pins.png)

Podobně můžete měnit, přidávat a odebírat další modifikátory stylu.

## <a name="next-steps"></a>Další kroky


* Prozkoumejte dokumentaci k [rozhraní Azure Maps Get Map Image API.](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* Další informace o službě Azure Maps Data Service najdete v [dokumentaci ke službě](https://docs.microsoft.com/rest/api/maps/data).

