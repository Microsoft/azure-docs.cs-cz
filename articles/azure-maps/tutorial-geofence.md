---
title: 'Kurz: vytvoření geografického a sledovacího zařízení na mapě | Mapy Microsoft Azure'
description: Naučte se, jak nastavit geografickou a sledovací zařízení relativní vzhledem k geografickým záznamům pomocí služby Microsoft Azure Maps (prostorová služba).
author: walsehgal
ms.author: v-musehg
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 932dfb9624177c299997c4f9f184dc5c973d0fa0
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899217"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Kurz: nastavení geografického plotu pomocí Azure Maps

V tomto kurzu se seznámíte se základními kroky pro nastavení geografického plotu pomocí Azure Maps. Vezměte v úvahu tento scénář, Site Manager konstrukce musí monitorovat potenciální nebezpečné zařízení. Správce musí zajistit, aby zařízení zůstalo ve vybraných celkových oblastech konstrukce. Tato celková oblast konstrukce je pevný parametr. Předpisy vyžadují, aby zařízení zůstala v rámci tohoto parametru a jejich porušení bylo hlášeno Operations Manager.  

Rozhraní API pro nahrání dat používáme k ukládání geografických zón a k kontrole umístění zařízení vzhledem k geografickému využití rozhraní API pro geografické ploty. Rozhraní API pro nahrání dat i rozhraní API pro geografické ploty jsou z Azure Maps. Používáme také Azure Event Grid ke streamování výsledků geografické plotu a nastavení oznámení na základě výsledků geografického ohraničení. Další informace o Event Grid najdete v tématu [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

V tomto kurzu zjistíte, jak:

> [!div class="checklist"]
> * Nahrajte oblast geografické oblasti v Azure Maps datové službě pomocí rozhraní API pro nahrání dat.
> *   Nastavte Event Grid pro zpracování událostí geografické ploty.
> *   Nastavení obslužné rutiny událostí geografické ploty
> *   Nastavte výstrahy v reakci na události geografické plotu pomocí Logic Apps.
> *   Pomocí Azure Maps rozhraní API pro monitorování geografických zón můžete sledovat, jestli se stavební prostředek nachází v rámci staveniště.


## <a name="prerequisites"></a>Požadavky

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps 

Podle pokynů v tématu [Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) vytvořte předplatné Azure Maps účtu s cenovou úrovní S1. Postup v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) ukazuje, jak načíst primární klíč svého účtu. Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Nahrát geografické ploty

Předpokládáme, že hlavní geografická plot je subsite1, což má nastavený čas vypršení platnosti. Podle vašich požadavků můžete vytvořit více vnořených geografických zón. Tyto sady plotů se dají použít ke sledování různých oblastí konstrukce v rámci celé oblasti stavby. Například subsite1 může být místo, kde práce probíhá během týdne 1 až 4 plánu. subsite2 může být, kde práce probíhá během týdne 5 až 7. Všechna taková plota lze načíst jako jedinou datovou sadu na začátku projektu. Tyto ploty se používají ke sledování pravidel na základě času a místa. 

Pro nahrání geografické lokality pro konstrukci pomocí rozhraní API pro nahrání dat používáme aplikaci post. Nainstalujte [aplikaci po](https://www.getpostman.com/) odinstalaci a vytvořte si bezplatný účet. 

Po instalaci aplikace pro publikování pomocí následujícího postupu nahrajte geografickou lokalitu konstrukce pomocí Azure Maps, rozhraní API pro nahrání dat.

1. Otevřete aplikaci pro vystavování a klikněte na nový | Vytvořte nový a vyberte požadavek. Zadejte název žádosti pro nahrávání dat o geografickou ochranou, vyberte kolekci nebo složku, do které se má uložit, a klikněte na Uložit.

    ![Nahrávat geografické ploty pomocí metody post](./media/tutorial-geofence/postman-new.png)

2. Na kartě tvůrce vyberte metodu POST HTTP a zadejte následující adresu URL, která odešle požadavek POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Parametr "Al JSON" v cestě URL představuje formát dat odesílaných dat.

3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí pro adresu URL požadavku POST. Nahraďte {Subscription-Key} vaším klíčem předplatného Azure Maps, který se taky označuje jako primární klíč.
   
    ![Parametry pro nahrávání dat (geografické plot) v předzálohovacím](./media/tutorial-geofence/postman-key-vals.png)

4. Klikněte na **text** a potom vyberte formát nezpracovaného vstupu a jako vstupní formát v rozevíracím seznamu zvolte JSON. Zadejte následující JSON pro nahrání dat:

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

5. Klikněte na Odeslat a zkontrolujte hlavičku odpovědi. Po úspěšné žádosti bude hlavička **umístění** obsahovat identifikátor URI stavu. Identifikátor URI stavu je v následujícím formátu. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Zkopírujte identifikátor URI stavu a přidejte klíč předplatného. Formát identifikátoru URI stavu by měl být podobný následujícímu. Všimněte si, že ve formátu níže byste změnili {klíč předplatného}, včetně {}, pomocí vašeho klíče předplatného.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Chcete-li získat `udId`, otevřete novou kartu v aplikaci post a na kartě tvůrce vyberte získat metodu HTTP. proveďte požadavek GET na identifikátor URI stavu z předchozího kroku. Pokud se vaše data úspěšně odeslala, obdržíte udId v těle odpovědi. Zkopírujte udId pro pozdější použití.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>Nastavení obslužné rutiny události

V této části vytvoříme obslužnou rutinu události, která obdrží oznámení. Tato obslužná rutina události by měla upozornit Operations Manager o událostech vstupu a ukončení jakéhokoli zařízení.

Pro zpracování událostí Enter a Exit provedeme dvě [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) služby. V případě událostí v triggeru Logic Apps se v posloupnosti spouští více událostí. Nápad je odeslat výstrahy do Operations Manager v tomto případě e-mailů. Následující obrázek znázorňuje vytvoření aplikace logiky pro událost vstupu geografické zóny. Podobně můžete vytvořit další pro událost Exit. Další informace najdete v tématu všechny [podporované obslužné rutiny událostí](https://docs.microsoft.com/azure/event-grid/event-handlers) .

1. Vytvoření aplikace logiky v Azure Portal

   ![Vytvoření Azure Logic Apps pro zpracování událostí geografické plotu](./media/tutorial-geofence/logic-app.png)

2. V nabídce nastavení aplikace logiky přejděte na **Návrhář aplikace logiky** .

3. Vyberte Trigger požadavku HTTP a pak vyberte nový krok. V Outlook Connectoru vyberte Odeslat e-mail jako akci.
  
   ![Logic Apps schéma](./media/tutorial-geofence/logic-app-schema.png)

4. Vyplňte pole k odeslání e-mailu. Ponechte adresu URL protokolu HTTP, po kliknutí na Uložit se automaticky vygeneruje.

   ![Vygenerovat Logic Apps koncový bod](./media/tutorial-geofence/logic-app-endpoint.png)

5. Uložte aplikaci logiky, aby se vygeneroval koncový bod adresy URL HTTP a zkopírujte adresu URL protokolu HTTP.

## <a name="create-an-azure-maps-events-subscription"></a>Vytvoření předplatného Azure Mapsch událostí

Azure Maps podporuje tři typy událostí. [Tady](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)se můžete podívat na Azure Maps podporované typy událostí. Potřebujeme dvě různé odběry událostí, jednu pro událost Enter a jednu pro události Exit.

Pomocí následujících kroků vytvořte odběr událostí pro události zadání geografických zón. Podobným způsobem se můžete přihlásit k odběru událostí ukončení geografického výstupu.

1. Přejděte na účet Azure Maps. Na řídicím panelu vyberte předplatná. Klikněte na název vašeho předplatného a vyberte **události** v nabídce nastavení.

   ![Přejít na události Azure Maps účtu](./media/tutorial-geofence/events-tab.png)

2. Chcete-li vytvořit odběr události, vyberte možnost odběr události ze stránky události.

   ![Vytvoření předplatného Azure Mapsch událostí](./media/tutorial-geofence/create-event-subscription.png)

3. Pojmenujte odběr události a přihlaste se k odběru typu události Enter. Nyní vyberte možnost webový zavěšení jako typ koncového bodu. Klikněte na vybrat koncový bod a zkopírujte koncový bod adresy URL HTTP aplikace logiky do {Endpoint}.

   ![Podrobnosti předplatného Azure Mapsch událostí](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Použití rozhraní API pro geografické ploty

Rozhraní API pro geografickou práci můžete použít ke kontrole, jestli je **zařízení**, v tomto případě zařízení uvnitř nebo vně geografické oblasti. Umožňuje dotazovat se na geografickou oblast získat rozhraní API proti různým umístěním, kde se konkrétní zařízení v průběhu času přesunulo. Následující obrázek znázorňuje pět umístění s pěti konstrukčními zařízeními. 

> [!Note]
> Scénář a chování jsou založené na stejném **ID zařízení** tak, aby odráželo pět různých umístění, jak je znázorněno na následujícím obrázku.

"DeviceId" je jedinečné ID, které v žádosti o získání zadáte pro vaše zařízení. při dotazování na jeho umístění. Když vytvoříte asynchronní požadavek na **hledání geograficky-získat rozhraní API**, "deviceId" pomáhá při publikování událostí geografického škálování pro toto zařízení vzhledem k určené geografické plotu. V tomto kurzu jsme udělali asynchronní požadavky na rozhraní API s jedinečným "deviceId". Žádosti v tomto kurzu se provádějí v chronologickém pořadí, jako v diagramu. Vlastnost "isEventPublished" v odpovědi se publikuje při každém vstupu nebo ukončení geografické zóny. K provedení tohoto kurzu nemusíte registrovat žádné zařízení.

Umožňuje pohled zpátky v diagramu. Každé z těchto pěti umístění se používá k vyhodnocení změny stavu v rámci geografické a ukončovací oblasti. Pokud dojde ke změně stavu, služba monitorování spustí událost, která se pošle do aplikace logiky Event Grid. V důsledku toho správce operace obdrží odpovídající oznámení o zadání nebo ukončení prostřednictvím e-mailu.

![Mapa geografického rozvržení v Azure Maps](./media/tutorial-geofence/geofence.png)

V aplikaci pro odesílání otevřete novou kartu ve stejné kolekci, kterou jste vytvořili výše. Na kartě tvůrce vyberte získat metodu HTTP:

Níže jsou uvedené pět požadavků na rozhraní API pro monitorování geografických zón s různými polohami zařízení. Souřadnice jsou pozorovány v chronologickém pořadí. Za každou žádost následuje text odpovědi.
 
1. Umístění 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Dotaz na geografickou plot 1](./media/tutorial-geofence/geofence-query1.png)

   V odpovědi výše se záporná vzdálenost od hlavní geografické sítě znamená, že je zařízení uvnitř geografické sítě. Kladná vzdálenost od geografického umístění dílčí lokality znamená, že je zařízení mimo geografickou lokalitu. 

2. Umístění 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Dotaz s geografickou ochranou 2](./media/tutorial-geofence/geofence-query2.png)

   Pokud se podíváte na předchozí odpověď JSON, zařízení je mimo lokalitu, ale je uvnitř hlavního plotu. Neaktivuje se žádná událost a nebude se posílat žádná e-mailová adresa.

3. Umístění 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Dotaz na geografickou plot 3](./media/tutorial-geofence/geofence-query3.png)

   Došlo ke změně stavu, ale toto zařízení je v geografických geografických i podřízených lokalitách. Tato změna způsobí, že se událost publikuje a pošle se do Operations Manager e-mail s oznámením.

4. Umístění 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Dotaz na geografickou plot 4](./media/tutorial-geofence/geofence-query4.png)

   Díky pečlivému pozorování odpovídající odpovědi si můžete všimnout, že se tady nezveřejňuje žádná událost, i když zařízení ukončilo geografickou lokalitu. Pokud se v žádosti o získání zobrazí zadaný čas uživatele, vidíte, že platnost geografické geografické lokality vypršela po tuto dobu. Zařízení je stále v hlavní geografické zóně. Můžete také zobrazit ID geometrie geografické lokality v části `expiredGeofenceGeometryId` v těle odpovědi.


5. Umístění 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Dotaz s geografickou ochranou 5](./media/tutorial-geofence/geofence-query5.png)

   Vidíte, že zařízení opustilo geografickou lokalitu hlavního staveniště. Dojde k publikování události a odeslání e-mailu s upozorněním na Operations Manager.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili: jak nastavit geografickou ochranou pomocí nahrávání do Azure Maps a datové služby pomocí rozhraní API pro nahrání dat. Zjistili jste také, jak použít mřížku událostí Azure Maps k přihlášení k odběru a zpracování událostí geografické sítě. 

* Další informace o tom, jak pomocí Logic Apps analyzovat JSON pro sestavování složitější logiky, najdete v tématu [zpracování typů obsahu v Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type).
* Další informace o obslužných rutinách událostí v Event Grid naleznete v tématu [podporované obslužné rutiny událostí v Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).
