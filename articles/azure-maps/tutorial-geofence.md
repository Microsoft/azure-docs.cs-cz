---
title: Vytvoření monitorové geografické zóny, pomocí Azure Maps | Dokumentace Microsoftu
description: Instalační program monitorové geografické zóny s využitím map Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 55dc0fa31398bcc04d9793c8cffc9258dc29e4c7
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244449"
---
# <a name="set-up-a-geofence-by-using-azure-maps"></a>Nastavení monitorové geografické zóny s využitím Azure Maps

Tento kurz vás provede kroky základní informace o nastavení monitorové geografické zóny s využitím map Azure. Scénář, který můžeme vyřešit v tomto kurzu se nápovědy konstrukce lokality vedoucí monitorování potenciální nebezpečné zařízení překonání oblasti určené konstrukce. Vytváření webu zahrnuje nákladné zařízení a nařízení. Obvykle vyžaduje, aby zařízení zůstane uvnitř lokality konstrukce a nenechává bez oprávnění.

Mapuje nahrát API služby Azure Data budeme používat k ukládání monitorové geografické zóny a použití Azure maps monitorové geografické zóny rozhraní API pro kontrolu zařízení umístění vzhledem k monitorové geografické zóny. Azure Event Grid budeme používat streamování výsledků monitorové geografické zóny a nastavení oznámení na základě výsledků monitorové geografické zóny.
Další informace o službě Event Grid najdete v tématu [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).
V tomto kurzu se dozvíte jak:

> [!div class="checklist"]
* Nahrajte oblasti monitorové geografické zóny ve službě Azure Maps, datové služby pomocí rozhraní API odeslat Data.
*   Nastavení Event Grid pro zpracování událostí monitorové geografické zóny.
*   Obslužné rutiny událostí instalace monitorové geografické zóny.
*   Nastavte si upozornění v reakci na události monitorové geografické zóny pomocí Logic Apps.
*   Pomocí služby Azure Maps monitorové geografické zóny rozhraní API pro sledování, zda je konstrukce prostředků v rámci lokality konstrukce nebo ne.


## <a name="prerequisites"></a>Požadavky

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps 

K dokončení kroků v tomto kurzu, musíte nejprve zobrazíte [spravovat účet a klíče](how-to-manage-account-keys.md) k vytváření a správě účtu předplatného S1 pro cenovou úroveň.

## <a name="upload-geofences"></a>Nahrát monitorovaná geografická zóna

Pokud chcete nahrát monitorové geografické zóny pro vytváření webu pomocí rozhraní API nahrát Data, budeme používat aplikaci postman. Pro účely tohoto kurzu předpokládáme, že není celkový oblasti lokality konstrukce, které je obtížné parametr, který by neměl porušují konstrukce zařízení. Porušení tohoto ohrazení jsou vážné obranu a hlášení do nástroje Operations Manager. Optimalizované sadu další ohrazení je možné, které sledují vytváření různých oblastí v rámci oblasti celkové konstrukce podle plánu. Můžete předpokládáme, že má hlavní monitorové geografické zóny Podweb1, který má nastavit vypršení platnosti čas a vyprší po uplynutí této doby. Můžete vytvořit více vnořených monitorovaná geografická zóna podle vašich požadavků. Podweb1 může být například, pokud pracovní probíhat během 1 až 4 týdnu plán a podřízeného webu 2 je, kde se práce probíhá týdnu 5 až 7. Takové ohrazení může načíst jako jedné datové sady na začátku projektu a používají ke sledování pravidel na základě času a místa. Další informace o formátu dat monitorové geografické zóny najdete v tématu [GeoJSON monitorové geografické zóny dat](https://docs.microsoft.com/azure/azure-maps/geofence-geojson). Další informace o nahrávání dat do služby Azure Maps, naleznete v tématu [dokumentace k rozhraní API nahrát Data](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) .

Otevřete aplikaci Postman a postupujte podle následujících kroků k nahrání konstrukce lokality monitorové geografické zóny pomocí Azure Maps API nahrát Data.

1. Otevřete aplikaci Postman a klikněte na tlačítko Nový | Vytvořit novou a vyberte žádost o. Zadejte název žádosti o nahrání monitorové geografické zóny dat, vyberte kolekce nebo složce a uložit ho. tím, klikněte na tlačítko Uložit.

    ![Nahrát monitorovaná geografická zóna pomocí nástroje Postman](./media/tutorial-geofence/postman-new.png)

2. Vyberte metodu POST HTTP na kartě tvůrce a zadejte následující adresu URL do požadavku POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Formát dat v průběhu nahrávání dat představuje parametr GEOJSON v cestě adresy URL.

3. Klikněte na tlačítko **Params**a zadejte následující dvojice klíč/hodnota má být použit pro adresu URL požadavku POST. Nahraďte hodnotu klíč předplatného s klíči předplatného Azure Maps.
   
    ![Parametry klíč-hodnota Postman](./media/tutorial-geofence/postman-key-vals.png)

4. Klikněte na tlačítko **tělo** pak vyberte vstupní formát raw a zvolte JSON jako formát vstupu z rozevíracího seznamu. Zadejte následující kód JSON jako data k odeslání:

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
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
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

5. Klikněte na tlačítko Odeslat a projděte si hlavičky odpovědi. Hlavička umístění obsahuje identifikátor URI pro přístup k nebo stáhnout data pro budoucí použití. Také obsahuje jedinečné `udId` pro odesílaná data.

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```

## <a name="set-up-an-event-handler"></a>Nastavit obslužnou rutinu události

Upozornění týkající se enter a ukončete událostí nástroje Operations Manager, jsme měli vytvořit obslužnou rutinu události, která bude přijímat oznámení.

Budeme vytvářet dvě [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) služby, které zvládnou, zadejte a události ukončení. Vytvoříme také aktivačních procedur událostí v rámci Logic Apps, které se aktivují podle těchto událostí. Pro odeslání výstrah, spočívá v tomto případě e-mailem do nástroje Operations Manager, vždy, když zařízení přejde do nebo ukončí vytváření webu. Následující obrázek znázorňuje vytvoření aplikace logiky pro událost vstupu monitorové geografické zóny. Podobně můžete vytvořit jiný pro událost ukončení.
Zobrazí se všechna [podporované obslužné rutiny událostí](https://docs.microsoft.com/azure/event-grid/event-handlers) pro další informace.

1. Vytvoření aplikace logiky na webu Azure portal

  ![vytváření aplikací logiky](./media/tutorial-geofence/logic-app.png)

2. Výběr triggeru požadavku HTTP a pak vyberte "send a e-mailu" jako akci v aplikaci outlook connector
  
  ![Schéma Logic Apps](./media/tutorial-geofence/logic-app-schema.png)

3. Uložení aplikace logiky pro generování koncového bodu adresy URL protokolu HTTP a zkopírujte adresu URL protokolu HTTP.

  ![Koncový bod aplikace logiky](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Vytvořit odběr události Azure Maps

Azure Maps podporuje tři typy událostí. Podívejte se na Azure Maps podporované typy událostí může mít [tady](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Vytvoříme dvou různých předplatných, jeden pro enter a druhou pro ukončení události.

Podle následujících pokynů k vytvoření odběru událostí pro události zadejte monitorové geografické zóny. Podobným způsobem můžete odebírat události ukončení monitorové geografické zóny.

1. Přejděte do svého účtu Azure Maps prostřednictvím [tohoto portálu odkazu](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/) a vyberte kartu události.

   ![Události Azure Maps](./media/tutorial-geofence/events-tab.png)

2. Pokud chcete vytvořit odběr událostí, vyberte odběr události ze stránky události.

   ![Předplatné Azure mapy událostí](./media/tutorial-geofence/create-event-subscription.png)

3. Název odběru události a přihlásit se k typu události Enter. Teď vyberte Webhook jako "Typ koncového bodu" a zkopírujte váš koncový bod adresy URL protokolu HTTP aplikace logiky do "Koncového bodu"

   ![Odběr události](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Použití rozhraní API monitorové geografické zóny

Rozhraní API monitorové geografické zóny můžete použít ke kontrole, jestli **zařízení** (zařízení je součástí stavu) je uvnitř nebo vně monitorové geografické zóny. Pokud chcete lépe porozumět monitorové geografické zóny získání rozhraní API. Zadáme dotaz na různých místech, kde se přesunul určité zařízení v čase. Následující obrázek znázorňuje pěti lokalitách konkrétní konstrukci zařízení s jedinečným **id zařízení** , jak v chronologickém pořadí. Každá z těchto pět umístění se používá k vyhodnocení monitorové geografické zóny vstupní a výstupní změna stavu proti plot. Pokud dojde ke změně stavu, službu monitorové geografické zóny aktivuje událost, která se odesílá do aplikace logiky pomocí služby Event Grid. V důsledku operace správce se zobrazí odpovídající enter nebo ukončete oznámení prostřednictvím e-mailu.

> [!Note]
> Výše uvedené scénáře a chování je založena na stejném **id zařízení** tak, aby odráží pět různých umístění jako na následujícím obrázku.

![Mapa monitorové geografické zóny](./media/tutorial-geofence/geofence.png)

V aplikaci Postman otevřete novou kartu ve stejné kolekci, kterou jste vytvořili výše. Vyberte metodu GET HTTP na kartě Tvůrce:

Následují pět požadavky HTTP GET API monitorování geografických zón s různých odpovídající souřadnice umístění zařízení, jak v chronologickém pořadí. Každý požadavek následuje text odpovědi.
 
1. 1. umístění:
    
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```
  ![Dotaz monitorové geografické zóny 1](./media/tutorial-geofence/geofence-query1.png)

  Když se podíváte na odpovědi výše, záporná vzdálenost od hlavní monitorové geografické zóny znamená, že zařízení se nachází uvnitř monitorové geografické zóny a kladné z podřízeného webu monitorové geografické zóny znamená, že je mimo podřízeného webu monitorové geografické zóny. 

2. Umístění 2: 
   
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```
    
  ![Monitorové geografické zóny dotazu 2](./media/tutorial-geofence/geofence-query2.png)

  Když se podíváte na předchozí odpověď JSON, pečlivě zařízení je mimo podřízeného webu, ale je uvnitř hlavní ohrazení. Aktivovat události a žádný e-mail se odešle.

3. 3. umístění: 
  
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```

  ![Dotaz monitorové geografické zóny 3](./media/tutorial-geofence/geofence-query3.png)

  Došlo ke změně stavu a nyní je zařízení v rámci oba hlavní a monitorovaná geografická zóna podřízených lokalit. To publikuje událost a oznámení e-mailu se odešlou do nástroje Operations Manager.

4. Umístění 4: 

  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```
  
  ![Monitorové geografické zóny dotazu 4](./media/tutorial-geofence/geofence-query4.png)

   Pozorováním pečlivě odpovídající odpověď, jste si, že žádná událost publikuje sem i v případě, že zařízení se ukončil podřízeného webu monitorové geografické zóny. Když se podíváte v určený čas uživatele v požadavek GET, uvidíte, že monitorové geografické zóny podřízeného webu vypršela platnost vzhledem k této doby a zařízení je stále v hlavní monitorové geografické zóny. Můžete také zjistit ID geometrie monitorové geografické zóny podřízené lokality pod `expiredGeofenceGeometryId` v textu odpovědi.


5. Umístění 5:
      
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```

  ![Monitorové geografické zóny dotazu 5](./media/tutorial-geofence/geofence-query5.png)

  Uvidíte, že zařízení není dostupný hlavní konstrukce lokality monitorové geografické zóny. Publikuje událost, a jedná se o závažnou porušení kritické výstrahy e-mail se odešle do nástroje Operations Manager.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak nastavit monitorové geografické zóny díky nahrávání ve službě Azure Maps, datové služby pomocí rozhraní API odeslat Data. Také jste zjistili, jak pomocí služby Azure Maps události Grid přihlásit k odběru a zpracování událostí monitorové geografické zóny. 

* Zobrazit [zpracování typů obsahu v Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type), se naučíte používat aplikace logiky k parsování formátu JSON vytvářet složitější logiku.
* Další informace o obslužných rutin událostí ve službě Event Grid, naleznete v tématu [podporované obslužných rutin událostí ve službě Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).
