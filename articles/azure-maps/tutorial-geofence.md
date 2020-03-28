---
title: 'Kurz: Vytvořte geofence a sledovat zařízení na mapě | Mapy Microsoft Azure'
description: Naučte se nastavit geografickou zónu a sledovat zařízení vzhledem k geografické vazbě pomocí služby Microsoft Azure Maps Spatial Service.
author: philmea
ms.author: philmea
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 126829f12d71e40511c26e781cb191988c1d031e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333870"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Kurz: Nastavení geografické zóny pomocí Azure Maps

Tento kurz vás provede základními kroky nastavení geografické zóny pomocí Azure Maps. Vezměme si tento scénář, stavební Site Manager musí sledovat potenciální nebezpečné zařízení. Manažer musí zajistit, aby zařízení zůstalo ve vybraných celkových stavebních oblastech. Tato celková konstrukční plocha je tvrdým parametrem. Předpisy vyžadují, aby zařízení zůstalo v rámci tohoto parametru, a porušení jsou nahlášena provoznímu manažerovi.  

Rozhraní API pro nahrávání dat používáme k ukládání geografické zóny a ke kontrole umístění zařízení vzhledem k geografické vazbě používáme rozhraní API geofence. Rozhraní API pro nahrávání dat i rozhraní Geofence jsou z Azure Maps. Azure Event Grid také používáme k streamování výsledků geografické zóny a nastavení oznámení na základě výsledků geografické zóny. Další informace o službě Event Grid najdete [v tématu Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

V tomto tutoriálu se zabýváme tím, jak:

> [!div class="checklist"]
> * Nahrajte oblast geografické zóny do Služby Azure Maps, Data pomocí rozhraní API pro nahrávání dat.
> *   Nastavte mřížku událostí pro zpracování událostí geofence.
> *   Nastavení obslužné rutiny událostí geografické zóny.
> *   Nastavte výstrahy v reakci na události geografické zóny pomocí logic apps.
> *   Pomocí rozhraní API služby Azure Maps geofence můžete sledovat, jestli je stavební majetek ve staveništi nebo ne.


## <a name="prerequisites"></a>Požadavky

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps 

Postupujte podle pokynů v [webu Vytvoření účtu k](quick-demo-map-app.md#create-an-account-with-azure-maps) vytvoření předplatného účtu Azure Maps s cenovou úrovní S1. Postup získání [primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) ukazuje, jak načíst primární klíč vašeho účtu. Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Nahrát geofences

Předpokládáme, že hlavní geofence je podřízený1, který má nastavenou dobu vypršení platnosti. Můžete vytvořit více vnořené geografické zóny podle vašich požadavků. Tyto sady plotů lze použít ke sledování různých stavebních ploch v rámci celkové stavební plochy. Podřízený web1 může být například tam, kde probíhají práce v týdnu 1 až 4 plánu. podplace2 může být místo, kde se práce uskutečňuje v týdnu 5 až 7. Všechny tyto ploty lze načíst jako jednu datovou sadu na začátku projektu. Tyto ploty se používají ke sledování pravidel na základě času a prostoru. 

Chcete-li nahrát geofence pro staveniště pomocí rozhraní API pro nahrávání dat, používáme aplikaci pošťák. Nainstalujte [pošťák aplikace](https://www.getpostman.com/) a vytvořit bezplatný účet. 

Po instalaci aplikace Postman postupujte podle těchto kroků k nahrání geografické zóny staveniště pomocí rozhraní Azure Maps, Data Upload API.

1. Otevřete aplikaci Pošťák a klikněte na nový | Vytvořte nový a vyberte Požádat. Zadejte název požadavku pro odeslání dat geografické ploty, vyberte kolekci nebo složku, do které ji chcete uložit, a klikněte na Uložit.

    ![Nahrání geofences pomocí Pošťáka](./media/tutorial-geofence/postman-new.png)

2. Na kartě tvůrce vyberte metodu POST HTTP a zadejte následující adresu URL pro vytvoření požadavku POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Parametr GEOJSON v cestě URL představuje formát dat nahraných dat.

3. Klikněte na **Params**a zadejte následující páry klíč/hodnota, které se použijí pro adresu URL požadavku POST. Nahraďte {název předplatného} klíčem předplatného Azure Maps, který se označuje také jako primární klíč.
   
    ![Parametry pro nahrávání dat (geofence) v Postman](./media/tutorial-geofence/postman-key-vals.png)

4. Klepněte na **body** a vyberte nezpracovaný vstupní formát a z rozevíracího seznamu zvolte JSON jako vstupní formát. Jako data, která mají být odeslána, uveďte následující údaje JSON:

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

5. Klikněte na Odeslat a zkontrolujte záhlaví odpovědi. Na základě úspěšné žádosti bude hlavička **Umístění** obsahovat identifikátor URI stavu. Identifikátor URI stavu je následujícího formátu. Hodnota uploadStatusId není mezi { }. Je běžnou praxí použít { } k zobrazení hodnot, které musí uživatel zadat, nebo hodnot, které se pro různéuživatele liší.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Zkopírujte svůj stav URI a připojit klíč předplatného. Formát identifikátoru URI stavu by měl být podobný formátu níže. Všimněte si, že v níže uvedeném formátu byste změnili {subscription-key}, neincludingi { }, s klíčem předplatného.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Chcete-li `udId`získat , otevřete novou kartu v aplikaci Postman a vyberte metodu GET HTTP na kartě tvůrce. Pokud bylo nahrání dat úspěšné, obdržíte udId v těle odpovědi. Zkopírujte udId pro pozdější použití.

   ```JSON
   {
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udId}?api-version=1.0"
   }
   ```

## <a name="set-up-an-event-handler"></a>Nastavení obslužné rutiny události

V této části vytvoříme obslužnou rutinu události, která přijímá oznámení. Tato obslužná rutina události by měla upozornit nástroj Operations Manager na události vstupu a ukončení libovolného zařízení.

Vytvoříme dvě služby [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) pro zpracování událostí enter a exit. Když se události v logic apps aktivační události, další události aktivační událost v pořadí. Cílem je odeslat upozornění, v tomto případě e-maily, na Operations Manager. Následující obrázek znázorňuje vytvoření logické aplikace pro událost geofence enter. Podobně můžete vytvořit další pro událost ukončení. Další informace naleznete ve všech [podporovaných obslužných rutinách událostí.](https://docs.microsoft.com/azure/event-grid/event-handlers)

1. Vytvořte aplikaci logiky na webu Azure Portal. Vyberte aplikaci logiky na Azure Marketplace. Potom vyberte tlačítko **Vytvořit.**

   ![Vytvoření aplikací Azure Logic Apps pro zpracování událostí geografické zóny](./media/tutorial-geofence/logic-app.png)

2. V nabídce nastavení aplikace Logika přejděte do **Návrháře aplikací logiky**

3. Vyberte aktivační událost požadavku HTTP a pak vyberte "Nový krok". V konektoru aplikace Outlook vyberte jako akci možnost Odeslat e-mail.
  
   ![Schéma aplikací logiky](./media/tutorial-geofence/logic-app-schema.png)

4. Vyplňte pole pro odeslání e-mailu. Ponechte adresu URL HTTP, automaticky se vygeneruje po kliknutí na tlačítko "uložit"

   ![Generování koncového bodu aplikace logiky](./media/tutorial-geofence/logic-app-endpoint.png)

5. Uložte aplikaci logiky, abyste vygenerovali koncový bod http url a zkopírovali adresu URL HTTP.

## <a name="create-an-azure-maps-events-subscription"></a>Vytvoření předplatného událostí Azure Maps

Azure Maps podporuje tři typy událostí. Můžete se podívat na typy podporovaných událostí Azure Maps [zde](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Potřebujeme dvě různá předplatná událostí, jedno pro událost enter a jedno pro události ukončení.

Podle následujících kroků vytvořte odběr událostí pro události geofence enter. Můžete se přihlásit k odběru událostí ukončení geofence podobným způsobem.

1. Přejděte na svůj účet VAP Maps. Na řídicím panelu vyberte Předplatná. Klikněte na název předplatného a vyberte **události** z nabídky nastavení.

   ![Přechod na události účtu Azure Maps](./media/tutorial-geofence/events-tab.png)

2. Pokud chcete vytvořit odběr událostí, vyberte odběr událostí na stránce události.

   ![Vytvoření předplatného událostí Azure Maps](./media/tutorial-geofence/create-event-subscription.png)

3. Pojmenujte odběr událostí a přihlaste se k odběru typu Enter události. Nyní vyberte webový hák jako "Typ koncového bodu". Klikněte na "Vyberte koncový bod" a zkopírujte koncový bod HTTP URL aplikace logiky do "{Endpoint}"

   ![Podrobnosti o předplatném Událostí Azure Maps](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Použití rozhraní API geografické zóny

Rozhraní GEOFENCE API můžete použít ke kontrole, zda **zařízení**, v tomto případě zařízení, je uvnitř nebo vně geofence. Umožňuje dotaz geofence GET API proti různých umístěních, kde se konkrétní zařízení přesunulo v průběhu času. Následující obrázek znázorňuje pět míst s pěti stavebními zařízeními. 

> [!Note]
> Scénář a chování je založena na stejné **id zařízení** tak, aby odráží pět různých umístění jako na obrázku níže.

"DeviceId" je jedinečné ID, které zadáte pro vaše zařízení v požadavku GET při dotazování na jeho umístění. Když provedete asynchronní požadavek na **hledání geofence - GET API**, "deviceId" pomáhá při publikování geofence události pro toto zařízení, vzhledem k zadané geofence. V tomto kurzu jsme provedli asynchronní požadavky na rozhraní API s jedinečným "deviceId". Požadavky v tutoriálu jsou prováděny v chronologickém pořadí, jako v diagramu. Vlastnost "isEventPublished" v odpovědi získá publikována vždy, když zařízení vstoupí nebo ukončí geofence. Není třeba registrovat zařízení sledovat s tímto kurzem.

Podívejme se zpět na diagram. Každá z těchto pěti míst se používá k posouzení změny stavu vstupu a výstupu geofence proti plotu. Pokud dojde ke změně stavu, služba geofence aktivuje událost, která je odeslána do aplikace logiky mřížkou událostí. V důsledku toho manažer operace obdrží odpovídající oznámení o zadání nebo ukončení prostřednictvím e-mailu.

![Geofence mapa v Mapách Azure](./media/tutorial-geofence/geofence.png)

V aplikaci Postman otevřete novou kartu ve stejné kolekci, kterou jste vytvořili výše. Na kartě tvůrce vyberte metodu GET HTTP:

Následuje pět požadavků HTTP GET Geofencing API s různými souřadnicemi umístění zařízení. Souřadnice jsou sledovány v chronologickém pořadí. Každý požadavek následuje tělo odpovědi.
 
1. Umístění 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Geografický dotaz 1](./media/tutorial-geofence/geofence-query1.png)

   Ve výše uvedené odpovědi negativní vzdálenost od hlavního geofence znamená, že zařízení je uvnitř geofence. Kladná vzdálenost od geofence podřízených lokalit znamená, že zařízení je mimo geofence podřízených lokalit. 

2. Umístění 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Geografický dotaz 2](./media/tutorial-geofence/geofence-query2.png)

   Pokud se podíváte na předchozí odezvu JSON pečlivě zařízení je mimo podřízený web, ale je uvnitř hlavního plotu. Nespustí se žádná událost a neodešle se žádný e-mail.

3. Umístění 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geografický dotaz 3](./media/tutorial-geofence/geofence-query3.png)

   Došlo ke změně stavu a nyní je zařízení v rámci hlavních i podřízených geofence. Tato změna způsobí, že událost publikovat a e-mail s oznámením bude odeslána operations manager.

4. Umístění 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Geografický dotaz 4](./media/tutorial-geofence/geofence-query4.png)

   Pečlivým sledováním odpovídající odpovědi si můžete všimnout, že zde nebude publikována žádná událost, i když zařízení opustilo geofence podřízených lokalit. Pokud se podíváte na zadaný čas uživatele v požadavku GET, uvidíte, že pro tuto dobu vypršela platnost geografické zóny podřízených lokalit. Zařízení je stále v hlavní geofence. Můžete také zobrazit ID geometrie geofence `expiredGeofenceGeometryId` pod v těle odezvy.


5. Umístění 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence dotaz 5](./media/tutorial-geofence/geofence-query5.png)

   Můžete vidět, že zařízení opustilhlavní staveniště geofence. Událost je publikována a nástroj Operations Manager je odeslán e-mail s výstrahou.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli: jak nastavit geografickou zónu nahráním ve službě Mapy a data Azure pomocí rozhraní API pro nahrávání dat. Také jste se naučili, jak používat Azure Maps Events Grid k odběru a zpracování událostí geografické zóny. 

* Viz [Zpracování typů obsahu v Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type), kde se dozvíte, jak pomocí logicových aplikací analyzovat JSON k vytvoření složitější logiky.
* Další informace o obslužných rutinách událostí v event gridu naleznete v [tématu podporované obslužné rutiny událostí v modulu Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).
