---
title: 'Kurz: vytvoření geografického a sledovacího zařízení na mapě Microsoft Azure'
description: Kurz jak nastavit geografickou ochranu Podívejte se, jak sledovat zařízení relativní vzhledem k geografickým záznamům pomocí Azure Maps prostorové služby.
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/20/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: ee32749e2c6f0118507fcfc6d4994a04ea3a6d69
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896796"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Kurz: Nastavení monitorované geografické zóny pomocí Azure Maps

V tomto kurzu se seznámíte se základy vytváření a používání Azure Mapsch geografických služeb. Provedete to v kontextu následujícího scénáře:

*Správce staveniště musí sledovat zařízení při jeho vstupu a opustí hranice oblasti stavby. Pokaždé, když se nějaké zařízení ukončí nebo vstoupí do těchto hraničních okruhů, pošle se na Operations Manager e-mailové oznámení.*

Azure Maps poskytuje řadu služeb pro podporu sledování zařízení vstupujících do oblasti stavby a jejich opuštění. V tomto kurzu jste:

> [!div class="checklist"]
> * Nahrajte [geografickou data o geografickou](geofence-geojson.md) lokalitu, která definují oblasti staveniště, které chcete monitorovat. Použijete [rozhraní API pro nahrání dat](/rest/api/maps/data/uploadpreview) k nahrávání geografických zón jako souřadnic mnohoúhelníku na váš Azure Maps účet.
> * Nastavte dvě [Aplikace logiky](../event-grid/handler-webhooks.md#logic-apps) , které po aktivaci odesílají e-mailová oznámení do výrobního serveru Operations Manageru, když zařízení vstoupí do oblasti geografického uspořádání a ukončí ho.
> * Pomocí [Azure Event Grid](../event-grid/overview.md) se můžete přihlásit k odběru událostí pro Azure Maps geografické ploty. Nastavili jste dva odběry událostí Webhooku, které volají koncové body HTTP definované ve vašich dvou aplikacích logiky. Aplikace logiky pak pošle vhodná e-mailová oznámení o přemístění nebo zapisování geografického zařízení.
> * Využijte [Hledat geografické ploty získat rozhraní API](/rest/api/maps/spatial/getgeofence) pro příjem oznámení v případě, že se nějaké zařízení ukončí a pak vstoupí do oblastí geografické oblasti.

## <a name="prerequisites"></a>Předpoklady

1. [Vytvořte účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.

V tomto kurzu se používá aplikace [po](https://www.postman.com/) aplikaci, ale můžete zvolit jiné vývojové prostředí API.

## <a name="upload-geofencing-geojson-data"></a>Nahrání geografických dat o geografických zón

V tomto kurzu nahrajete data geografického geografického geografického monitorování, které obsahuje `FeatureCollection` . `FeatureCollection`Obsahuje dvě geografické ploty, které definují mnohoúhelníkové oblasti v rámci staveniště. První Geografické ohraničení nemá žádné vypršení času ani omezení. Na druhý se může dotazovat jenom v pracovní době (9:00 dop. 5:00 odp v časovém pásmu tichomořského) a po 1. lednu 2022 už nebude platné. Další informace o formátu geografického JSON najdete v tématu [geografická data geografických dat](geofence-geojson.md).

>[!TIP]
>Data monitorování geografických zón můžete kdykoli aktualizovat. Další informace najdete v tématu [rozhraní API pro nahrání dat](/rest/api/maps/data/uploadpreview).

1. Otevřete aplikaci pro vyúčtování. V horní části vyberte **Nový** . V okně **vytvořit nové** vyberte **kolekce** . Pojmenujte kolekci a vyberte **vytvořit** .

2. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek** . Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozím kroku, a pak vyberte **Uložit** .

3. Na kartě tvůrce vyberte metodu **post** http a zadejte následující adresu URL pro nahrání dat o geografickou ochranu do Azure Maps. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    `geojson`Parametr v cestě URL představuje formát dat odesílaných dat.

4. Vyberte kartu **tělo** . Jako vstupní formát vyberte **nezpracované** a pak **JSON** . Zkopírujte a vložte následující data o zápisu **do textu do oblasti textu:**

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Vyberte **Odeslat** a počkejte na zpracování žádosti. Po dokončení žádosti přejít na kartu **hlavičky** odpovědi. Zkopírujte hodnotu klíče **umístění** , což je `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Chcete-li zjistit stav volání rozhraní API, vytvořte požadavek **Get** http na `status URL` . K adrese URL pro ověření budete muset připojit primární klíč předplatného. Požadavek **Get** by měl vypadat jako následující adresa URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Po úspěšném dokončení požadavku **Get** http vrátí `resourceLocation` . `resourceLocation`Obsahuje jedinečný `udid` pro nahraný obsah. Uložte `udid` si to pro dotazování rozhraní API pro zjištění geografické plotu v poslední části tohoto kurzu. Volitelně můžete pomocí `resourceLocation` adresy URL načíst metadata z tohoto prostředku v dalším kroku.

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

9. Po úspěšném dokončení požadavku **Get** http bude tělo odpovědi obsahovat `udid` zadané v `resourceLocation` kroku 7. Bude také obsahovat umístění pro přístup k obsahu a jeho stažení a další metadata o obsahu. Příkladem celkové odpovědi je:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-workflows-in-azure-logic-apps"></a>Vytváření pracovních postupů v Azure Logic Apps

V dalším kroku vytvoříte dva koncové body [Aplikace logiky](../event-grid/handler-webhooks.md#logic-apps) , které aktivují e-mailové oznámení. Tady je postup, jak vytvořit první z těchto akcí:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** .

3. Do pole **Hledat na Marketplace** zadejte **Aplikace logiky** .

4. Z výsledků vyberte možnost vytvořit **aplikaci logiky**  >  **Create** .

5. Na stránce **Aplikace logiky** zadejte následující hodnoty:
    * **Předplatné** , které chcete použít pro tuto aplikaci logiky.
    * Název **skupiny prostředků** pro tuto aplikaci logiky Můžete zvolit **Vytvoření nové** nebo **použití existující** skupiny prostředků.
    * **Název aplikace** logiky vaší aplikace logiky V takovém případě použijte `Equipment-Enter` jako název.

    Pro účely tohoto kurzu ponechte všechny ostatní hodnoty na jejich výchozím nastavení.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Snímek obrazovky s vytvořením aplikace logiky":::

6. Vyberte **zkontrolovat + vytvořit** . Zkontrolujte nastavení a vyberte **vytvořit** pro odeslání nasazení. Po úspěšném dokončení nasazení vyberte **Přejít k prostředku** . Jste převzali na **návrháře aplikace logiky** .

7. Vyberte typ triggeru. Přejděte dolů do části **Začínáme s běžným triggerem** . Vyberte, **kdy se přijme požadavek HTTP** .

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Snímek obrazovky s vytvořením aplikace logiky":::

8. V pravém horním rohu návrháře aplikace logiky vyberte **Uložit** . **Adresa URL příspěvku http** se vygeneruje automaticky. Uložte adresu URL. Budete ho potřebovat v další části, abyste vytvořili koncový bod události.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Snímek obrazovky s vytvořením aplikace logiky":::

9. Vyberte **+ Nový krok** . Nyní vyberete akci. `outlook.com email`Do vyhledávacího pole zadejte. V seznamu **Akce** přejděte dolů a vyberte **Odeslat e-mail (v2)** .
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Snímek obrazovky s vytvořením aplikace logiky":::

10. Přihlaste se k účtu Outlooku. Nezapomeňte vybrat **Ano** , pokud chcete, aby aplikace logiky měla přístup k účtu. Vyplňte pole k odeslání e-mailu.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Snímek obrazovky s vytvořením aplikace logiky":::

    >[!TIP]
    > `geometryId` `deviceId` V e-mailových oznámeních můžete načíst data o neodpověďech na data typu injson, jako je například nebo. Logic Apps můžete nakonfigurovat pro čtení dat odesílaných Event Grid. Informace o tom, jak nakonfigurovat Logic Apps pro využívání a předávání dat událostí do e-mailových oznámení, najdete v tématu [kurz: odesílání e-mailových oznámení o událostech Azure IoT Hub pomocí Event Grid a Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

11. V levém horním rohu návrháře aplikace logiky vyberte **Uložit** .

Pokud chcete vytvořit druhou aplikaci logiky, která upozorní správce, když zařízení opustí staveniště, opakujte kroky 3-11. Pojmenujte aplikaci logiky `Equipment-Exit` .

## <a name="create-azure-maps-events-subscriptions"></a>Vytvoření předplatných událostí Azure Maps

Azure Maps podporuje [tři typy událostí](../event-grid/event-schema-azure-maps.md). V tomto případě je potřeba vytvořit dvě různé odběry událostí: jednu pro události v geografickém vstupu a jednu pro události ukončení geografické korelace.

Následující kroky ukazují, jak vytvořit odběr událostí pro události zadání geografických zón. Můžete se přihlásit k odběru událostí ukončení geografického výstupu opakováním kroků podobným způsobem.

1. Přejít na účet Azure Maps. Na řídicím panelu vyberte **předplatná** . Vyberte název vašeho předplatného a v nabídce nastavení vyberte **události** .

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Snímek obrazovky s vytvořením aplikace logiky":::

2. Chcete-li vytvořit odběr události, vyberte **+ odběr události** ze stránky události.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Snímek obrazovky s vytvořením aplikace logiky":::

3. Na stránce **vytvořit odběr události** zadejte následující hodnoty:
    * **Název** odběru události
    * **Schéma události** by mělo být *Event Grid schématu* .
    * **Název systémového tématu** pro toto předplatné události, v tomto případě je to `Contoso-Construction` .
    * Pro **Filtr na typy událostí** vyberte `Geofence Entered` jako typ události.
    * Jako **Typ koncového bodu** vyberte `Web Hook` .
    * Pro **koncový bod** zkopírujte adresu URL post protokolu HTTP pro aplikaci logiky Zadejte koncový bod, který jste vytvořili v předchozí části. Pokud jste zapomněli ho uložit, můžete se vrátit zpátky do návrháře aplikace logiky a zkopírovat ho z kroku triggeru HTTP.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Snímek obrazovky s vytvořením aplikace logiky":::

4. Vyberte **Vytvořit** .

Opakujte kroky 1-4 pro koncový bod ukončení aplikace logiky, který jste vytvořili v předchozí části. V kroku 3 se ujistěte, že jste zvolili `Geofence Exited` Typ události.

## <a name="use-spatial-geofence-get-api"></a>Použití rozhraní API pro prostorovou geografickou práci

K posílání e-mailových oznámení do Operations Manageru, když některý ze zařízení vstoupí do geografických zón nebo je ukončí, použijte [rozhraní API pro prostorové geografické](/rest/api/maps/spatial/getgeofence) práci.

Každý kus vybavení má `deviceId` . V tomto kurzu sledujete jedno vybavení s jedinečným ID `device_1` .

Následující diagram znázorňuje pět umístění zařízení v průběhu času počínaje *počátečním* umístěním, které je někde mimo okolní geografické oblasti. Pro účely tohoto kurzu není umístění *začátku* definováno, protože v tomto umístění nebudete dotazováni na zařízení.

Při dotazování [rozhraní API pro prostorové geografické](/rest/api/maps/spatial/getgeofence) rozmístění pomocí umístění zařízení, které označuje počáteční položku nebo ukončení geografické oblasti, Event Grid volá příslušný koncový bod aplikace logiky k odeslání e-mailového oznámení do nástroje Operations Manager.

Každý z následujících oddílů provede požadavky rozhraní API pomocí pěti různých souřadnic umístění tohoto zařízení.

![Diagram mapy geografického rozvržení v Azure Maps](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Umístění zařízení 1 (47.638237,-122,132483)

1. V horní části okna po aplikaci vyberte **Nový** . V okně **vytvořit nové** vyberte **požadavek** . Zadejte **název žádosti** . Nastavte *umístění 1* . Vyberte kolekci, kterou jste vytvořili v [části nahrát data geografického monitorování geografických zón](#upload-geofencing-geojson-data), a pak vyberte **Uložit** .

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. Ujistěte `{Azure-Maps-Primary-Subscription-key}` se, že jste nahradili primární klíč předplatného a `{udid}` `udid` uložili jste ho v [části nahrání geografických dat pro ukládání geografických zón](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Vyberte **Poslat** . V okně odpověď se zobrazí následující text v poli s odpovědí.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

V předchozí geografické odpovědi se záporná vzdálenost od geografického umístění hlavní lokality znamená, že je zařízení uvnitř geografické sítě. Kladná vzdálenost od geografického umístění dílčí lokality znamená, že je zařízení mimo geografickou lokalitu. Vzhledem k tomu, že je toto zařízení poprvé umístěno v geografickém umístění hlavní lokality, `isEventPublished` je parametr nastaven na hodnotu `true` . Operations Manager dostane e-mailové oznámení, které zařízení dostalo do geografické zóny.

### <a name="location-2-4763800-122132531"></a>Umístění 2 (47.63800,-122,132531)

1. V horní části okna po aplikaci vyberte **Nový** . V okně **vytvořit nové** vyberte **požadavek** . Zadejte **název žádosti** . Nastavte *umístění na 2* . Vyberte kolekci, kterou jste vytvořili v [části nahrát data geografického monitorování geografických zón](#upload-geofencing-geojson-data), a pak vyberte **Uložit** .

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. Ujistěte `{Azure-Maps-Primary-Subscription-key}` se, že jste nahradili primární klíč předplatného a `{udid}` `udid` uložili jste ho v [části nahrání geografických dat pro ukládání geografických zón](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Vyberte **Poslat** . V okně odpověď se zobrazí následující text v poli s odpovědí:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

V předchozí odpovědi typu geografická odpověď se zařízení nacházelo v geografickém geografickém umístění a ještě nevstoupilo do geografické lokality. V důsledku toho `isEventPublished` je parametr nastaven na hodnotu `false` a Operations Manager neobdrží žádná e-mailová oznámení.

### <a name="location-3-4763810783315048-12213336020708084"></a>Umístění 3 (47.63810783315048,-122.13336020708084)

1. V horní části okna po aplikaci vyberte **Nový** . V okně **vytvořit nové** vyberte **požadavek** . Zadejte **název žádosti** . Nastavte umístění na IT *3* . Vyberte kolekci, kterou jste vytvořili v [části nahrát data geografického monitorování geografických zón](#upload-geofencing-geojson-data), a pak vyberte **Uložit** .

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. Ujistěte `{Azure-Maps-Primary-Subscription-key}` se, že jste nahradili primární klíč předplatného a `{udid}` `udid` uložili jste ho v [části nahrání geografických dat pro ukládání geografických zón](#upload-geofencing-geojson-data).

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Vyberte **Poslat** . V okně odpověď se zobrazí následující text v poli s odpovědí:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

V předchozí odpovědi typu geografická odpověď byla zařízení v geografickém geografickém umístění, ale byla zapsána geografická lokalita. V důsledku toho `isEventPublished` je parametr nastaven na hodnotu `true` . Operations Manager dostane e-mailové oznámení oznamující, že zařízení dostalo geografickou ochranu.

>[!NOTE]
>Pokud se zařízení přesunulo do podřízené lokality po pracovní době, nebude se žádná událost publikovat a Operations Manageru by nedostal žádná oznámení.  

### <a name="location-4-47637988-1221338344"></a>Umístění 4 (47.637988,-122,1338344)

1. V horní části okna po aplikaci vyberte **Nový** . V okně **vytvořit nové** vyberte **požadavek** . Zadejte **název žádosti** . Nastavte *umístění 4* . Vyberte kolekci, kterou jste vytvořili v [části nahrát data geografického monitorování geografických zón](#upload-geofencing-geojson-data), a pak vyberte **Uložit** .

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. Ujistěte `{Azure-Maps-Primary-Subscription-key}` se, že jste nahradili primární klíč předplatného a `{udid}` `udid` uložili jste ho v [části nahrání geografických dat pro ukládání geografických zón](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Vyberte **Poslat** . V okně odpověď se zobrazí následující text v poli s odpovědí:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

V předchozí odpovědi na geografickou odpověď bylo zařízení v geografickém geografickém umístění, ale ukončilo geografickou lokalitu. Všimněte si však, že `userTime` hodnota je po `expiredTime` Definování definice v geograficky dat. V důsledku toho `isEventPublished` je parametr nastaven na hodnotu `false` a Operations Manager neobdrží e-mailové oznámení.

### <a name="location-5-4763799--122134505"></a>Umístění 5 (47,63799,-122,134505)

1. V horní části okna po aplikaci vyberte **Nový** . V okně **vytvořit nové** vyberte **požadavek** . Zadejte **název žádosti** . Nastavte *umístění na 5* . Vyberte kolekci, kterou jste vytvořili v [části nahrát data geografického monitorování geografických zón](#upload-geofencing-geojson-data), a pak vyberte **Uložit** .

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. Ujistěte `{Azure-Maps-Primary-Subscription-key}` se, že jste nahradili primární klíč předplatného a `{udid}` `udid` uložili jste ho v [části nahrání geografických dat pro ukládání geografických zón](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63799&lon=-122.134505&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Vyberte **Poslat** . V okně odpověď se zobrazí následující text v poli s odpovědí:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

V předchozí odpovědi typu geografická odpověď byla zařízení ukončena geografickou lokalitou. V důsledku toho `isEventPublished` je parametr nastaven na hodnotu `true` a Operations Manager dostane e-mailové oznámení oznamující, že se zařízení ukončilo geografickou ochranou.


Můžete také [Odeslat e-mailová oznámení pomocí Event Grid a Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) a pomocí Azure Mapsu kontrolovat [podporované obslužné rutiny událostí v Event Grid](../event-grid/event-handlers.md) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zpracování typů obsahu v Azure Logic Apps](../logic-apps/logic-apps-content-type.md)