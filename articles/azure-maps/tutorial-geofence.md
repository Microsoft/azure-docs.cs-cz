---
title: 'Kurz: vytvoření geografického a sledovacího zařízení na mapě | Mapy Microsoft Azure'
description: V tomto kurzu se dozvíte, jak nastavit geografickou a sledovací zařízení relativně k geografickému rozvržení pomocí služby Microsoft Azure Maps (prostorová služba).
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 0e408adfe1daed402ef690224368e846bd0a97c8
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910941"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Kurz: nastavení geografického plotu pomocí Azure Maps

V tomto kurzu se seznámíte se základními kroky pro nastavení geografického plotu pomocí Azure Maps. Scénář, který vyřešíme v tomto kurzu, je pomáhat při vytváření správců webů, které sledují potenciální nebezpečné vybavení přesahující vymezené oblasti stavby. Staveniště zahrnuje nákladná zařízení a předpisy. Obvykle vyžaduje, aby zařízení zůstalo uvnitř stavebního pracoviště a nezůstalo bez oprávnění.

K uložení geografického umístění používáme rozhraní API pro nahrání dat Azure Maps a k monitorování polohy zařízení relativních k geografickosti použít rozhraní API pro geografické ploty Azure Maps. Použijeme Azure Event Grid ke streamování výsledků geografické plotu a nastavení oznámení na základě výsledků geografického ohraničení.
Další informace o Event Grid najdete v tématu [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).
V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nahrajte oblast geografické oblasti v Azure Maps datové službě pomocí rozhraní API pro nahrání dat.
> *   Nastavte Event Grid pro zpracování událostí geografické ploty.
> *   Nastavení obslužné rutiny událostí geografické ploty
> *   Nastavte výstrahy v reakci na události geografické plotu pomocí Logic Apps.
> *   Pomocí Azure Maps rozhraní API pro monitorování geografických zón můžete sledovat, jestli se stavební prostředek nachází v rámci staveniště.


## <a name="prerequisites"></a>Požadavky

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps 

K dokončení kroků v tomto kurzu postupujte podle pokynů v části [Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) pro vytvoření předplatného Azure Maps s cenovou úrovní S1 a postupujte podle kroků v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) pro získání primárního klíče pro váš účet. Další podrobnosti o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Nahrát geografické ploty

Pro nahrání geografické lokality pro stavbu pomocí rozhraní API pro nahrání dat použijeme aplikaci po odeslání. Pro účely tohoto kurzu předpokládáme, že je k dispozici celková oblast staveniště, což je pevný parametr, který by výrobní vybavení nemělo porušovat. Porušení této ochranné plotu jsou závažným OFFENSE a jsou hlášeny Operations Manager. Optimalizovaná sada dalších plotů se dá použít pro sledování různých oblastí konstrukce v rámci celkové oblasti stavby podle plánu. Můžeme předpokládat, že hlavní geografické plot má subsite1, který má nastavený čas vypršení platnosti a po uplynutí této doby vyprší. Podle vašich požadavků můžete vytvořit více vnořených geografických zón. Například subsite1 může být, kde práce probíhá během týdne 1 až 4 plánu a dílčí lokalita 2 je, kde práce probíhá během týdne 5 až 7. Všechna taková plota lze načíst jako jedinou datovou sadu na začátku projektu a používat ke sledování pravidel na základě času a místa. Další informace o formátu dat geografické ploty najdete v článku [geografická data](https://docs.microsoft.com/azure/azure-maps/geofence-geojson)geografického formátu JSON. Další informace o nahrávání dat do služby Azure Maps najdete v tématu [dokumentace k rozhraní API pro nahrání dat](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) .

Otevřete aplikaci pro vyúčtování a podle následujících pokynů nahrajte geografickou lokalitu konstrukce pomocí Azure Maps, rozhraní API pro nahrání dat.

1. Otevřete aplikaci pro vystavování a klikněte na nový | Vytvořte nový a vyberte požadavek. Zadejte název žádosti pro nahrávání dat o geografickou ochranou, vyberte kolekci nebo složku, do které se má uložit, a klikněte na Uložit.

    ![Nahrávat geografické ploty pomocí metody post](./media/tutorial-geofence/postman-new.png)

2. Na kartě tvůrce vyberte metodu POST HTTP a zadejte následující adresu URL, která odešle požadavek POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Parametr "Al JSON" v cestě URL představuje formát dat odesílaných dat.

3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí pro adresu URL požadavku POST. Nahraďte hodnotu klíčového předplatného klíčem Azure Maps.
   
    ![Parametry pro nahrávání dat (geografické plot) v předzálohovacím](./media/tutorial-geofence/postman-key-vals.png)

4. Klikněte na **tělo** a pak vyberte formát nezpracovaného vstupu a jako vstupní formát v rozevíracím seznamu zvolte JSON. Zadejte následující JSON pro nahrání dat:

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

5. Klikněte na Odeslat a zkontrolujte hlavičku odpovědi. Po úspěšné žádosti bude hlavička **umístění** obsahovat identifikátor URI stavu, aby zkontrolovala aktuální stav žádosti o nahrání. Identifikátor URI stavu bude v následujícím formátu. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Zkopírujte identifikátor URI stavu a přidejte do něj parametr `subscription-key` s jeho hodnotou jako klíč předplatného pro Azure Maps účet. Formát identifikátoru URI stavu by měl vypadat takto:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Chcete-li získat `udId` otevřete novou kartu v aplikaci post a na kartě tvůrce vyberte získat metodu HTTP a vytvořte požadavek GET na identifikátor URI stavu. Pokud se vaše data úspěšně nahrála, obdržíte udId v těle odpovědi. Zkopírujte udId pro pozdější použití.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>Nastavení obslužné rutiny události

Chcete-li informovat Operations Manager týkající se událostí Enter a Exit, je nutné vytvořit obslužnou rutinu události, která bude přijímat oznámení.

Vytvoříme dvě služby [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) pro zpracování událostí, zadávání a ukončení. V rámci Logic Apps také vytvoříme triggery událostí, které se spouštějí těmito událostmi. Nápad je odeslat výstrahy, v tomto případě e-maily na Operations Manager vždy, když zařízení vstoupí do výrobní lokality nebo je ukončí. Následující obrázek znázorňuje vytvoření aplikace logiky pro událost vstupu geografické zóny. Podobně můžete vytvořit další pro událost Exit.
Další informace najdete v tématu všechny [podporované obslužné rutiny událostí](https://docs.microsoft.com/azure/event-grid/event-handlers) .

1. Vytvoření aplikace logiky v Azure Portal

   ![Vytvoření Azure Logic Apps pro zpracování událostí geografické plotu](./media/tutorial-geofence/logic-app.png)

2. Vyberte Trigger požadavku HTTP a pak jako akci v konektoru Outlooku zvolte Odeslat e-mail.
  
   ![Logic Apps schéma](./media/tutorial-geofence/logic-app-schema.png)

3. Uložte aplikaci logiky, aby se vygeneroval koncový bod adresy URL HTTP a zkopírujte adresu URL protokolu HTTP.

   ![Vygenerovat Logic Apps koncový bod](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Vytvoření předplatného Azure Mapsch událostí

Azure Maps podporuje tři typy událostí. [Tady](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)se můžete podívat na Azure Maps podporované typy událostí. Vytvoříme dvě různá předplatná, jednu pro Enter a druhou pro události Exit.

Pomocí následujících kroků vytvořte odběr událostí pro události zadání geografických zón. Podobným způsobem se můžete přihlásit k odběru událostí ukončení geografického výstupu.

1. Pomocí [tohoto odkazu na portál](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/) přejděte na účet Azure Maps a vyberte kartu události.

   ![Přejít na události Azure Maps účtu](./media/tutorial-geofence/events-tab.png)

2. Chcete-li vytvořit odběr události, vyberte možnost odběr události ze stránky události.

   ![Vytvoření předplatného Azure Mapsch událostí](./media/tutorial-geofence/create-event-subscription.png)

3. Pojmenujte odběr události a přihlaste se k odběru typu události Enter. Teď vyberte Webhook jako "typ koncového bodu" a zkopírujte koncový bod adresy URL HTTP aplikace logiky do koncového bodu.

   ![Podrobnosti předplatného Azure Mapsch událostí](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Použití rozhraní API pro geografické ploty

Rozhraní API pro geografickou práci můžete použít ke kontrole, jestli je **zařízení** (zařízení součástí stavu) uvnitř nebo vně geografické oblasti. Pro lepší pochopení rozhraní GET API pro monitorování geografických zón. Dotazuje se na různá místa, kde se konkrétní zařízení v průběhu času přesunulo. Následující obrázek znázorňuje pět umístění konkrétního konstrukčního zařízení s jedinečným **ID zařízení** , které je v chronologickém pořadí zaznamenáno. Každé z těchto pěti umístění se používá k vyhodnocení změny stavu v rámci geografické a ukončovací oblasti. Pokud dojde ke změně stavu, služba monitorování spustí událost, která se pošle do aplikace logiky Event Grid. V důsledku toho obdrží správce operace odpovídající oznámení o zadání nebo ukončení prostřednictvím e-mailu.

> [!Note]
> Výše uvedený scénář a chování jsou založené na stejném **ID zařízení** , aby odráželo pět různých umístění, jak je znázorněno na následujícím obrázku.

![Mapa geografického rozvržení v Azure Maps](./media/tutorial-geofence/geofence.png)

V aplikaci pro odesílání otevřete novou kartu ve stejné kolekci, kterou jste vytvořili výše. Na kartě tvůrce vyberte získat metodu HTTP:

Následuje pět požadavků na rozhraní API pro monitorování geografických zón, s různými odpovídajícími souřadnicemi umístění tohoto zařízení v chronologickém pořadí. Za každou žádost následuje text odpovědi.
 
1. Umístění 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Dotaz na geografickou plot 1](./media/tutorial-geofence/geofence-query1.png)

   Pokud se podíváte na odpověď uvedenou výše, záporná vzdálenost z hlavní geografické sítě znamená, že se zařízení nachází uvnitř geografické oblasti a pozitivní z geografického geografického umístění znamená, že je mimo geografickou lokalitu. 

2. Umístění 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Dotaz s geografickou ochranou 2](./media/tutorial-geofence/geofence-query2.png)

   Pokud se podíváte na předchozí odpověď JSON, zařízení je mimo lokalitu, ale je uvnitř hlavní ochranné oblasti. Neaktivuje událost bez odeslání e-mailu.

3. Umístění 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Dotaz na geografickou plot 3](./media/tutorial-geofence/geofence-query3.png)

   Došlo ke změně stavu, ale toto zařízení je v geografických geografických i podřízených lokalitách. Tím se publikuje událost a pošle se na Operations Manager e-mail s oznámením.

4. Umístění 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Dotaz na geografickou plot 4](./media/tutorial-geofence/geofence-query4.png)

   Díky pečlivému pozorování odpovídající odpovědi si můžete všimnout, že se tady nezveřejňuje žádná událost, i když zařízení ukončilo geografickou lokalitu. Pokud se v žádosti o získání zobrazí zadaný čas uživatele, vidíte, že časový limit geografického umístění vypršel relativně k této době a že zařízení je stále v hlavní geografické úrovni. Můžete také zobrazit ID geometrie geografické lokality v části `expiredGeofenceGeometryId` v těle odpovědi.


5. Umístění 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Dotaz s geografickou ochranou 5](./media/tutorial-geofence/geofence-query5.png)

   Vidíte, že zařízení opustilo geografickou lokalitu hlavního staveniště. Zveřejňuje událost, jedná se o vážné porušení a pošle se na Operations Manager důležitý e-mail s výstrahami.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak nastavit geografickou ochranou tím, že ji nahrajete do Azure Maps datové služby pomocí rozhraní API pro nahrání dat. Zjistili jste také, jak použít mřížku událostí Azure Maps k přihlášení k odběru a zpracování událostí geografické sítě. 

* Další informace o tom, jak pomocí Logic Apps analyzovat JSON pro sestavování složitější logiky, najdete v tématu [zpracování typů obsahu v Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type).
* Další informace o obslužných rutinách událostí v Event Grid naleznete v tématu [podporované obslužné rutiny událostí v Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).
