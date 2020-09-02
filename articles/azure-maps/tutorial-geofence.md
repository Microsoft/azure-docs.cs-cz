---
title: 'Kurz: vytvoření geografického a sledovacího zařízení na mapě Microsoft Azure'
description: Přečtěte si, jak nastavit geografickou ochranu. Podívejte se, jak sledovat zařízení vzhledem k geografickým záznamům pomocí Azure Maps prostorové služby.
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/11/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3ea9923dd98a49b1533defa3e95616655b7ea78d
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299299"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Kurz: Nastavení monitorované geografické zóny pomocí Azure Maps

V tomto kurzu se seznámíte se základy vytváření a používání Azure Mapsch geografických služeb v kontextu následujícího scénáře:

*Konstrukce Site Manager musí sledovat zařízení při jeho vstupu a ponechání hraničních oblastí konstrukce. Pokaždé, když se nějaké zařízení ukončí nebo vstoupí do těchto hraničních okruhů, pošle se na Operations Manager e-mailové oznámení.*

Azure Maps poskytuje řadu služeb pro podporu sledování zařízení, které zadává a ukončuje oblast stavby ve výše uvedeném scénáři. V tomto kurzu zjistíte, jak:

> [!div class="checklist"]
> * Nahrajte [geografickou data o geografickou](geofence-geojson.md) lokalitu, která definují oblasti staveniště, které chcete monitorovat. [Rozhraní API pro nahrání dat](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) použijeme k nahrání geografických zón jako souřadnic mnohoúhelníku na váš Azure Maps účet.
> * Nastavte dvě [Aplikace logiky](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) , která po aktivaci pošle e-mailová oznámení na staveništi Operations Manager, když zařízení vstoupí a ukončí oblast geografické oblasti.
> * Pomocí [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) se můžete přihlásit k odběru Azure Maps události zadání a ukončení geografické zóny. Nastavíme dva odběry událostí webového zavěšení, které budou volat koncové body HTTP definované ve dvou Logic Apps. Logic Apps pak pošle příslušná e-mailová oznámení o přemístění nebo zapisování geografického zařízení.
> * Využijte [Hledat geografické ploty získat rozhraní API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) pro příjem oznámení v případě, že se nějaké zařízení ukončí a pak vstoupí do oblastí geografické oblasti.

## <a name="prerequisites"></a>Předpoklady

1. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.

V tomto kurzu se používá aplikace [po](https://www.postman.com/) aplikaci, ale můžete zvolit jiné vývojové prostředí API.

## <a name="upload-geofencing-geojson-data"></a>Nahrání geografických dat o geografických zón

V tomto kurzu pošleme data geografického uložení geografických zón, která obsahují `FeatureCollection` . `FeatureCollection`Obsahuje dvě geografické ploty, které definují mnohoúhelníkové oblasti v rámci staveniště. První Geografické ohraničení nemá žádné vypršení času ani omezení. Na druhý se může dotazovat jenom za pracovní dobu (9-5 hodin. PST) a nebude již platit od 1. ledna 2022. Další informace o formátu geografického JSON najdete v tématu [geografická data geografických dat](geofence-geojson.md).

>[!TIP]
>Data monitorování geografických zón můžete kdykoli aktualizovat. Další informace o tom, jak aktualizovat vaše data, najdete v tématu [rozhraní API pro nahrání dat](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) .

1. Otevřete aplikaci pro vyúčtování. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **kolekce**.  Pojmenujte kolekci a vyberte tlačítko **vytvořit** .

2. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozím kroku, a pak vyberte **Uložit**.

3. Na kartě tvůrce vyberte metodu **post** http a zadejte následující adresu URL pro nahrání dat o geografickou ochranu do služby Azure Maps. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Parametr "Al _JSON_ " v cestě URL představuje formát dat odesílaných dat.

4. Klikněte na kartu **tělo** . Jako vstupní formát vyberte **nezpracované**a pak **JSON** . Zkopírujte a vložte následující data o zápisu **do textu do oblasti textu:**

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

5. Klikněte na modré tlačítko **Odeslat** a počkejte na zpracování žádosti. Až se žádost dokončí, přejdete na kartu **hlavičky** odpovědi. Zkopírujte hodnotu klíče **umístění** , což je `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Chcete-li zjistit stav volání rozhraní API, vytvořte požadavek **Get** http na `status URL` . K adrese URL pro ověření budete muset připojit primární klíč předplatného. Požadavek **Get** by měl vypadat jako následující adresa URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Po úspěšném dokončení požadavku **Get** http se vrátí `resourceLocation` . `resourceLocation`Obsahuje jedinečný `udid` pro nahraný obsah. `udid`V poslední části tohoto kurzu budete muset tento postup Uložit pro dotazování rozhraní API pro zjištění geografické oblasti. Volitelně můžete pomocí `resourceLocation` adresy URL načíst metadata z tohoto prostředku v dalším kroku.

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
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-logic-app-workflows"></a>Vytváření pracovních postupů aplikací logiky

V této části vytvoříme dva koncové body [Aplikace logiky](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) , které budou aktivovat e-mailové oznámení. Ukážeme vám, jak vytvořit první aktivační událost, která pošle e-mailová oznámení pokaždé, když se zavolá její koncový bod.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

2. V levém horním rohu webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek**.

3. Do pole *Hledat na Marketplace* zadejte **Aplikace logiky**.

4. Z *výsledků*vyberte **Aplikace logiky**. Klikněte na tlačítko **vytvořit** .

5. Na stránce **Aplikace logiky** zadejte následující hodnoty:
    * *Předplatné* , které chcete použít pro tuto aplikaci logiky.
    * Název *skupiny prostředků* pro tuto aplikaci logiky Můžete zvolit možnost *Vytvořit novou* nebo *Použít existující* skupinu prostředků.
    * *Název aplikace* logiky vaší aplikace logiky V tomto případě použijeme `Equipment-Enter` jako název.

    Pro účely tohoto kurzu ponechte ostatní hodnoty na jejich výchozím nastavení.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Vytvoření aplikace logiky":::

6. Klikněte na tlačítko **Revize + vytvořit** . Zkontrolujte nastavení a kliknutím na **vytvořit** odešlete nasazení. Po úspěšném dokončení nasazení klikněte na **Přejít k prostředku**. Přejdete do **návrháře aplikace logiky** .

7. Teď vybereme typ triggeru. Posuňte se dolů k části *začátek s běžným triggerem**. **Po přijetí požadavku HTTP**klikněte na zapnuto.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Vytvoření triggeru HTTP aplikace logiky":::

8. V pravém horním rohu návrháře klikněte na **Uložit** . Automaticky se vygeneruje **Adresa URL post protokolu HTTP** . Uložte adresu URL, jak ji budete potřebovat v další části, a vytvořte koncový bod události.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Adresa URL požadavku HTTP aplikace logiky a JSON":::

9. Vyberte **+ Nový krok**. Nyní si zvolíme akci. `outlook.com email`Do vyhledávacího pole zadejte. V seznamu **Akce** se posuňte dolů a klikněte na **Odeslat e-mail (v2)**.
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Vytvoření návrháře aplikace logiky":::

10. Přihlaste se ke svému účtu Outlook.com. Nezapomeňte kliknout na **Ano** , pokud chcete, aby aplikace logiky měla přístup k účtu. Vyplňte pole k odeslání e-mailu.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Vytvoření e-mailového kroku pro odeslání aplikace logiky":::

    >[!TIP]
    > Můžete načíst data `geometryId` o Logical Response, například nebo `deviceId` v e-mailových oznámeních, a to tak, že nakonfigurujete aplikaci logiky tak, aby četla data odesílaná Event Grid. Informace o tom, jak nakonfigurovat aplikaci logiky pro využívání a předávání dat událostí do e-mailových oznámení, najdete v tématu [kurz: odesílání e-mailových oznámení o událostech Azure IoT Hub pomocí Event Grid a Logic Apps](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps).

11. V levém horním rohu návrháře Logic Apps klikněte na **Uložit** .

12. Zopakováním kroků 3-11 vytvořte druhou aplikaci logiky, která upozorní správce, když zařízení ukončí stavbu. Pojmenujte aplikaci logiky `Equipment-Exit` .

## <a name="create-azure-maps-events-subscriptions"></a>Vytvoření předplatných událostí Azure Maps

Azure Maps podporuje tři typy událostí. [Tady](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)se můžete podívat na Azure Maps podporované typy událostí.  Budeme muset vytvořit dvě různé odběry událostí: jednu pro události v geografickém režimu a jednu pro události ukončení geografického výstupu.

Pomocí následujících kroků vytvořte odběr událostí pro události zadání geografických zón. Můžete se přihlásit k odběru událostí ukončení geografického výstupu opakováním kroků podobným způsobem.

1. Přejděte na účet Azure Maps. Na řídicím panelu vyberte **předplatná**. Klikněte na název vašeho předplatného a vyberte **události** v nabídce nastavení.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Přejít na události Azure Maps účtu":::

2. Chcete-li vytvořit odběr události, vyberte **+ odběr události** ze stránky události.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Vytvoření předplatného Azure Mapsch událostí":::

3. Na stránce **vytvořit odběr události** zadejte následující hodnoty:
    * *Název* odběru události
    * *Schéma události* by mělo být *Event Grid schématu*.
    * *Název systémového tématu* pro toto předplatné události V tomto případě použijeme `Contoso-Construction` .
    * V poli *Filtr na typ události*vyberte možnost `Geofence Entered` jako typ události.
    * Jako *Typ koncového bodu*vyberte `Web Hook` .
    * Pro *koncový bod*zkopírujte adresu URL post protokolu HTTP pro aplikaci logiky Zadejte koncový bod, který jste vytvořili v předchozí části. Pokud jste zapomněli ho uložit, můžete se vrátit zpátky do návrháře aplikace logiky a zkopírovat ho z kroku triggeru HTTP.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Podrobnosti předplatného Azure Mapsch událostí":::

4. Klikněte na možnost **Vytvořit**.

5. Opakujte kroky 1-4 pro koncový bod ukončení aplikace logiky, který jste vytvořili v předchozí části. V kroku 3 se ujistěte, že jste zvolili `Geofence Exited` Typ události.

## <a name="use-spatial-geofence-get-api"></a>Použití rozhraní API pro prostorovou geografickou práci

Teď k posílání e-mailových oznámení do Operations Manager použijeme [rozhraní API prostorového geografického](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) rozjezdu, když nějaké zařízení vstoupí do geografických zón nebo je ukončí.

Každé zařízení má `deviceId` . V tomto kurzu budeme sledovat jedno zařízení, jehož jedinečný identifikátor je `device_1` .

Pro přehlednost znázorňuje následující diagram v průběhu času pět umístění zařízení, počínaje *počátečním* umístěním, které je někde mimo okolní geografické oblasti. Pro účely tohoto kurzu není *počáteční* umístění definováno, protože v tomto umístění nebudeme zadávat dotazy na zařízení.

Při dotazování [rozhraní API pro prostorové geografické](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) rozmístění pomocí umístění zařízení, které označuje počáteční položku nebo ukončení geografické oblasti, Event Grid zavolá příslušný koncový bod aplikace logiky a pošle e-mailové oznámení do Operations Manager.

Každá z následujících oddílů umožňuje, aby protokol HTTP získal požadavky rozhraní API pro monitorování geografických zón pomocí pěti různých souřadnic umístění tohoto zařízení.

![Mapa geografického rozvržení v Azure Maps](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Umístění zařízení 1 (47.638237,-122,132483)

1. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **požadavek**.  Zadejte **název žádosti** . Použijeme název, *umístění 1*. Vyberte kolekci, kterou jste vytvořili v [části nahrát data geografického monitorování geografických zón](#upload-geofencing-geojson-data), a pak vyberte **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL, kterou jste si nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného a `{udid}` s `udid` uloženým v [části nahrát data geografického zápisu do geografického](#upload-geofencing-geojson-data)formátu.

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Klikněte na tlačítko **Odeslat** . V okně odpověď se zobrazí následující kód pro zobrazení textu v odpovědi.

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

4. V rámci geografické výše uvedené odpovědi se záporná vzdálenost od geografického umístění hlavní lokality znamená, že je zařízení uvnitř geografické sítě. Kladná vzdálenost od geografického umístění dílčí lokality znamená, že je zařízení mimo geografickou lokalitu. Vzhledem k tomu, že se toto zařízení nacházelo v geografickém geografickém umístění, je tento `isEventPublished` Parametr nastavený na `true` a Operations Manager by vám dostalo e-mailové oznámení, které zařízení dostalo do geografické zóny.

### <a name="location-2-4763800-122132531"></a>Umístění 2 (47.63800,-122,132531)

1. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **požadavek**.  Zadejte **název žádosti** . Použijeme název, *umístění 2*. Vyberte kolekci, kterou jste vytvořili v [části nahrát data geografického monitorování geografických zón](#upload-geofencing-geojson-data), a pak vyberte **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL, kterou jste si nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného a `{udid}` s `udid` uloženým v [části nahrát data geografického zápisu do geografického](#upload-geofencing-geojson-data)formátu.

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Klikněte na tlačítko **Odeslat** . V okně odpověď se zobrazí následující kód pro zobrazení textu:

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

4. V odpovědi geografického formátu JSON výše se zařízení nachází v geografickém geografickém umístění a ještě nevstoupilo do geografické lokality. V důsledku toho `isEventPublished` je parametr nastaven na `false` a Operations Manager neobdrží žádná e-mailová oznámení.

### <a name="location-3-4763810783315048-12213336020708084"></a>Umístění 3 (47.63810783315048,-122.13336020708084)

1. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **požadavek**.  Zadejte **název žádosti** . Použijeme název, *umístění 3*. Vyberte kolekci, kterou jste vytvořili v [části nahrát data geografického monitorování geografických zón](#upload-geofencing-geojson-data), a pak vyberte **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL, kterou jste si nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného a `{udid}` s `udid` uloženým v [části nahrát data geografického zápisu do geografického](#upload-geofencing-geojson-data)formátu.

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Klikněte na tlačítko **Odeslat** . V okně odpověď se zobrazí následující kód pro zobrazení textu:

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

4. V odpovědi geografického kódu JSON výše se zařízení nachází v geografickém geografickém umístění, ale zadalo geografickou lokalitu. V důsledku toho `isEventPublished` je parametr nastaven na hodnotu `true` a Operations Manager dostane e-mailové oznámení oznamující, že zařízení dostalo geografickou ochranu.

    >[!NOTE]
    >Pokud se zařízení přesunulo do podřízené lokality za pracovní dobu, nepublikuje se žádná událost a Operations Manager neobdrží žádná oznámení.  

### <a name="location-4-47637988-1221338344"></a>Umístění 4 (47.637988,-122,1338344)

1. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **požadavek**.  Zadejte **název žádosti** . Použijeme název, *umístění 4*. Vyberte kolekci, kterou jste vytvořili v [části nahrát data geografického monitorování geografických zón](#upload-geofencing-geojson-data), a pak vyberte **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL, kterou jste si nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného a `{udid}`  s `udid` uloženým v [části nahrát data geografického zápisu do geografického](#upload-geofencing-geojson-data)formátu.

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Klikněte na tlačítko **Odeslat** . V okně odpověď se zobrazí následující kód pro zobrazení textu:

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

4. V odpovědi geografického JSON výše se zařízení nachází v geografickém geografickém umístění, ale ukončilo geografickou lokalitu. Nicméně pokud si všimnete, `userTime` je hodnota po `expiredTime` Definování definice v geograficky dat. V důsledku toho `isEventPublished` je parametr nastaven na hodnotu `false` a Operations Manager nepřijde o e-mailové oznámení.

### <a name="location-547637988-1221338344"></a>Umístění 5 (47.637988,-122,1338344)

1. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **požadavek**.  Zadejte **název žádosti** . Použijeme název, *umístění 4*. Vyberte kolekci, kterou jste vytvořili v [části nahrát data geografického monitorování geografických zón](#upload-geofencing-geojson-data), a pak vyberte **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL, kterou jste si nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného a `{udid}` s `udid` uloženým v [části nahrát data geografického zápisu do geografického](#upload-geofencing-geojson-data)formátu.

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Klikněte na tlačítko **Odeslat** . V okně odpověď se zobrazí následující kód pro zobrazení textu:

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

4. V odpovědi geografického formátu JSON výše se v zařízení ukončila geografická síť hlavního pracoviště. V důsledku toho `isEventPublished` je parametr nastaven na hodnotu `true` a Operations Manager dostane e-mailové oznámení oznamující, že zařízení ukončilo geografickou ochranu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zpracování typů obsahu v Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)

> [!div class="nextstepaction"]
> [Odesílání e-mailových oznámení pomocí Event Grid a Logic Apps](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)

> [!div class="nextstepaction"]
> [Podporované obslužné rutiny událostí v Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).
