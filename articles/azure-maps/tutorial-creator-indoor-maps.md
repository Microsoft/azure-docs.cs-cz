---
title: Vytvoření vnitřních map pomocí autora
description: K vytvoření vnitřních map použijte Tvůrce Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9ed6690348816229d369bcff5d92c9703a4b3702
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469911"
---
# <a name="use-creator-to-create-indoor-maps"></a>Vytvoření vnitřních map pomocí autora

V tomto kurzu se dozvíte, jak vytvořit mapy vnitřních. V tomto kurzu se naučíte používat rozhraní API k těmto akcím:

> [!div class="checklist"]
> * Nahrání balíčku pro vykreslování mapy interiéru
> * Převod balíčku pro kreslení na data mapy
> * Vytvoření datové sady z dat mapy
> * Vytvoření TILESET z dat v datové sadě
> * Dotazování na rozhraní API služby Azure Maps Web Feature Service (WFS) pro další informace o funkcích map
> * Vytvoření funkce stateset pomocí vašich funkcí mapy a dat v datové sadě
> * Aktualizace stateset funkcí

## <a name="prerequisites"></a>Požadavky

Vytvoření vnitřních map:

1. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.
3. [Vytvoření prostředku autora](how-to-manage-creator.md)
4. Stáhněte si [vzorový balíček pro kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

V tomto kurzu se používá aplikace [po](https://www.postman.com/) aplikaci, ale můžete zvolit jiné vývojové prostředí API.

>[!IMPORTANT]
> Adresy URL rozhraní API v tomto dokumentu může být potřeba upravit podle umístění prostředku autora. Další podrobnosti najdete v tématu [přístup ke službě Creator Services](how-to-manage-creator.md#access-to-creator-services).

## <a name="upload-a-drawing-package"></a>Nahrání balíčku pro kreslení

Pomocí [rozhraní API pro nahrání dat](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) nahrajte balíček pro kreslení do prostředků Azure Maps.

Rozhraní API pro nahrání dat je dlouhodobá transakce, která implementuje vzor definovaný zde. Po dokončení operace použijeme `udid` pro přístup k nahranému balíčku k převedení. Použijte následující postup k získání `udid` .

1. Otevřete aplikaci pro vyúčtování. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **kolekce**.  Pojmenujte kolekci a vyberte tlačítko **vytvořit** .

2. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozím kroku, a pak vyberte **Uložit**.

3. Na kartě tvůrce vyberte metodu **post** http a zadejte následující adresu URL pro nahrání balíčku pro kreslení do služby Azure Maps. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Na kartě **hlavičky** zadejte hodnotu pro `Content-Type` klíč. Balíček pro kreslení je složka zip, takže použijte `application/octet-stream` hodnotu. Na kartě **tělo** vyberte **binární**. Klikněte na **Vybrat soubor** a zvolte balíček pro kreslení.

     ![Správa dat](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. Klikněte na modré tlačítko **Odeslat** a počkejte na zpracování žádosti. Až se žádost dokončí, přejdete na kartu **hlavičky** odpovědi. Zkopírujte hodnotu klíče **umístění** , což je `status URL` .

6. Chcete-li zjistit stav volání rozhraní API, vytvořte požadavek **Get** http na `status URL` . K adrese URL pro ověření budete muset připojit primární klíč předplatného. Požadavek **Get** by měl vypadat jako na následující adrese URL:

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. Po úspěšném dokončení požadavku **Get** http se vrátí `resourceLocation` . `resourceLocation`Obsahuje jedinečný `udid` pro nahraný obsah. Volitelně můžete pomocí `resourceLocation` adresy URL načíst metadata z tohoto prostředku v dalším kroku.

    ```json
    {
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
    }
    ```

8. Chcete-li načíst metadata obsahu, vytvořte požadavek **Get** http na `resourceLocation` adrese URL, která byla načtena v kroku 7. Ujistěte se, že jste k adrese URL přidávali primární klíč předplatného pro ověřování. Požadavek **Get** by měl vypadat jako následující adresa URL:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Až se požadavek **Get** http úspěšně dokončí, tělo odpovědi bude obsahovat `udid` zadané v `resourceLocation` kroku 7, umístění pro přístup k obsahu nebo jeho stažení v budoucnu a některá další metadata o obsahu, jako je datum vytvoření/aktualizace, velikost a tak dále. Příkladem celkové odpovědi je:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Převod balíčku pro kreslení

 Teď, když se balíček pro kreslení nahraje, použijeme `udid` pro nahráný balíček k převedení balíčku na data mapy. Rozhraní API pro převod používá dlouhou běžící transakci, která implementuje [zde](creator-long-running-operation.md)definovaný vzor. Po dokončení operace použijeme `conversionId` pro přístup k převedeným datům. Použijte následující postup k získání `conversionId` .

1. Vyberte **Nové**. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** a vyberte kolekci. Klikněte na **Uložit**.

2. Vyberte metodu **post** http na kartě tvůrce a zadejte následující adresu URL pro převedení nahraného balíčku pro vykreslování na data mapy. Použijte `udid` pro nahraný balíček.

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```

    >[!IMPORTANT]
    > Adresy URL rozhraní API v tomto dokumentu může být potřeba upravit podle umístění prostředku autora. Další podrobnosti najdete v tématu [přístup ke službě Creator Services](how-to-manage-creator.md#access-to-creator-services).

3. Klikněte na tlačítko **Odeslat** a počkejte na zpracování žádosti. Po dokončení žádosti přejít na kartu **hlavičky** odpovědi a vyhledejte klíč **umístění** . Zkopírujte hodnotu klíče **umístění** , který je `status URL` pro požadavek na převod. Tento postup použijete v dalším kroku.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/copy-location-uri-dialog.png" border="true" alt-text="Zkopírujte hodnotu klíče umístění.":::

4. Spusťte novou metodu **Get** http na kartě tvůrce. Přidejte k adresáři Azure Maps primární klíč předplatného `status URL` . Vytvořte požadavek **Get** `status URL` v umístění, které jste zkopírovali v kroku 3. `status URL`Vypadá to jako následující adresa URL:

    ```http
    https://atlas.microsoft.com/conversion/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

    Pokud se proces převodu ještě nedokončí, může se zobrazit něco podobného jako u následující odpovědi JSON:

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. Po úspěšném dokončení žádosti se v těle odpovědi zobrazí zpráva o stavu úspěchu.  Zkopírujte `conversionId` `resourceLocation` adresu URL pro převedený balíček. `conversionId`Je používáno jiným rozhraním API pro přístup k převedeným datům mapy.

    ```json
   {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>Aplikace po spuštění neimplementuje nativně požadavky HTTP na spouštění. V důsledku toho si můžete všimnout dlouhé prodlevy při vytváření žádosti o **získání** na adrese URL stavu.  Počkejte asi třicet sekund a zkuste kliknout na tlačítko **Odeslat** znovu, dokud odpověď neukáže úspěch nebo selže.

Vzorový balíček pro kreslení by měl být převeden bez chyb nebo upozornění. Pokud ale obdržíte chyby nebo upozornění z vlastního balíčku pro vykreslování, odpověď JSON vám poskytne odkaz na [Vizualizér chyb kreslení](drawing-error-visualizer.md). Vizualizér chyb při vykreslování umožňuje zkontrolovat podrobnosti o chybách a upozorněních. Chcete-li získat doporučení, jak vyřešit chyby a varování při převodu, přečtěte si téma [chyby a upozornění převodu vykreslování](drawing-conversion-error-codes.md).

```json
{
    "operationId": "<operationId>",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Vytvoření datové sady

Datová sada je kolekce funkcí mapy, jako jsou budovy, úrovně a místnosti. Chcete-li vytvořit datovou sadu, použijte [rozhraní API pro vytvoření datové sady](https://docs.microsoft.com/rest/api/maps/dataset/createpreview). Rozhraní API pro vytvoření datové sady přebírá `conversionId` pro převedený balíček a vrátí `datasetId` vytvořenou datovou sadu. Následující postup ukazuje, jak vytvořit datovou sadu.

1. V aplikaci post vyberte možnost **Nový**. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** a vyberte kolekci. Klikněte na **Uložit**.

2. Vytvořte novou datovou sadu pomocí požadavku **post** pro [datovou sadu vytvořit rozhraní API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) . Před odesláním žádosti přidejte svůj klíč předplatného a `conversionId` společně `conversionId` získaný během procesu převodu v kroku 5.  Požadavek by měl vypadat jako na následující adrese URL:

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Získejte `statusURL` v klíči **umístění** **hlaviček**odpovědi.

4. Vytvořte si požadavek **Get** na adresu, kde `statusURL` získáte `datasetId` . K ověřování přidejte svůj primární klíč předplatného Azure Maps. Požadavek by měl vypadat jako na následující adrese URL:

    ```http
    https://atlas.microsoft.com/dataset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. Po úspěšném dokončení požadavku **Get** http bude hlavička odpovědi obsahovat `datasetId` pro vytvořenou datovou sadu. Zkopírujte `datasetId` . K vytvoření TILESET budete muset použít `datasetId` .

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>Vytvoření TILESET

TILESET je sada vektorových dlaždic, které se vykreslují na mapě. Tilesets se vytvářejí z existujících datových sad. TILESET je však nezávislá na datové sadě, ze které byl zdroj. Pokud je datová sada odstraněna, bude TILESET nadále existovat. Chcete-li vytvořit TILESET, postupujte podle následujících kroků:

1. V aplikaci post vyberte možnost **Nový**. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** a vyberte kolekci. Klikněte na **Uložit**.

2. Vytvořte požadavek **post** na kartě tvůrce. Adresa URL požadavku by měla vypadat jako na následující adrese URL:

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Vytvořte si požadavek **Get** na `statusURL` TILESET. K ověřování přidejte svůj primární klíč předplatného Azure Maps. Požadavek by měl vypadat jako na následující adrese URL:

   ```http
    https://atlas.microsoft.com/tileset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Po úspěšném dokončení požadavku **Get** http bude hlavička odpovědi obsahovat `tilesetId` pro vytvořenou TILESET. Zkopírujte `tilesetId` .

    ```json
    {
        "operationId": "<operationId>",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>Dotazování datových sad pomocí rozhraní WFS API

 K datovým sadám se dá dotázat pomocí  [rozhraní WFS API](https://docs.microsoft.com/rest/api/maps/wfs). Pomocí rozhraní WFS API můžete zadávat dotazy na kolekce funkcí, konkrétní kolekci nebo konkrétní funkci s **ID**funkce. **ID** funkce jednoznačně identifikuje funkci v rámci datové sady. Používá se například k identifikaci toho, který stav funkce by měl být v daném stateset aktualizován.

1. V aplikaci post vyberte možnost **Nový**. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** a vyberte kolekci. Klikněte na **Uložit**.

2. Vytvořte požadavek **Get** , který zobrazí seznam kolekcí ve vaší datové sadě. Nahraďte `<dataset-id>` svým `datasetId` . Místo zástupného znaku použijte Azure Maps primární klíč. Požadavek by měl vypadat jako na následující adrese URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. Text odpovědi bude dodán ve formátu injson a bude obsahovat všechny kolekce v datové sadě. Pro zjednodušení tento příklad zobrazuje pouze `unit` kolekci. Příklad, který obsahuje všechny kolekce, najdete v tématu [WFS Popis rozhraní API kolekcí](https://docs.microsoft.com/rest/api/maps/wfs/collectiondescriptionpreview). Chcete-li získat další informace o jakékoli kolekci, můžete kliknout na kteroukoli z adres URL uvnitř `link` elementu.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. Vytvořte požadavek **Get** pro `unit` kolekce funkcí.  Nahraďte `{datasetId}` svým `datasetId` . Místo zástupného znaku použijte Azure Maps primární klíč. Tělo odpovědi bude obsahovat všechny funkce `unit` kolekce. Požadavek by měl vypadat jako na následující adrese URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Zkopírujte funkci `id` pro funkci jednotky, která má vlastnosti stylu, které se dají dynamicky upravovat.  Vzhledem k tomu, že je možné dynamicky aktualizovat stav obsazení jednotky a teplotu, tuto funkci použijeme `id` v další části. V následujícím příkladu je tato funkce `id` "UNIT26". jako stavy budeme odkazovat na vlastnosti stylu této funkce a my tuto funkci použijeme k vytvoření stateset.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Vytvoření funkce stateset

1. V aplikaci post vyberte možnost **Nový**. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** a vyberte kolekci. Klikněte na **Uložit**.

2. Vytvořte požadavek **post** [rozhraní API pro vytvoření stateset](https://docs.microsoft.com/rest/api/maps/featurestate/createstatesetpreview). Použijte `datasetId` datovou sadu, která obsahuje stav, který chcete upravit. Požadavek by měl vypadat jako na následující adrese URL:

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. V **záhlaví** žádosti **post** nastavte `Content-Type` na `application/json` . V **těle**zadejte níže styly, aby odrážely změny `occupied` `temperature` *stavů*a. Až skončíte, klikněte na **Odeslat**.

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. Zkopírujte `statesetId` z textu odpovědi.

5. Vytvoření žádosti **post** pro aktualizaci stavu: předejte statesetId a funkci do `ID` klíče předplatného Azure Maps. Požadavek by měl vypadat jako na následující adrese URL:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. V **záhlaví** žádosti **post** nastavte `Content-Type` na `application/json` . V **těle** žádosti **post** zkopírujte a vložte JSON v níže uvedené ukázce.

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

    >[!NOTE]
    > Aktualizace se uloží pouze v případě, že je čas razítka za časovým razítkem předchozí žádosti. Můžeme předat libovolný KeyName, který jsme předtím nakonfigurovali během vytváření.

7. Po úspěšné aktualizaci obdržíte `200 OK` stavový kód HTTP. Pokud jste pro mapu vnitřních umístění  [implementovali dynamické styly](indoor-map-dynamic-styling.md) , aktualizace se zobrazí ve vykreslené mapě v zadaném časovém razítku.

[Rozhraní API pro získání](https://docs.microsoft.com/rest/api/maps/featurestate/getstatespreview) stavů funkce umožňuje načíst stav funkce pomocí její funkce `ID` . Stateset a jeho prostředky můžete také odstranit pomocí [rozhraní API pro odstranění stavu funkce](https://docs.microsoft.com/rest/api/maps/featurestate/deletestatesetpreview).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nahrání balíčku pro vykreslování mapy interiéru
> * Převod balíčku pro kreslení na data mapy
> * Vytvoření datové sady z dat mapy
> * Vytvoření TILESET z dat v datové sadě
> * Dotazování na službu Azure Maps WFS, kde se dozvíte o funkcích map
> * Vytvoření funkce stateset pomocí vašich funkcí mapy a dat v datové sadě
> * Aktualizace stateset funkcí

Nyní jste vybaveni dovednostmi, které potřebujete k přechodu na další příručky:

> [!div class="nextstepaction"]
> [Použití modulu mapy Vnitřníchy](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Implementovat dynamické styly pro mapy vnitřních.](indoor-map-dynamic-styling.md)

Další informace o různých Azure Mapsch službách popsaných v tomto článku:

> [!div class="nextstepaction"]
> [Nahrávání dat](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Převod dat](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Datová sada](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Sada stavů funkcí](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [Služba WFS](creator-indoor-maps.md#web-feature-service-api)
