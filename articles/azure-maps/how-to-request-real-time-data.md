---
title: Požadavek na přenosová data v reálném čase | Mapy Microsoft Azure
description: Vyžádejte data v reálném čase pomocí služby mobility Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 053e6c84f69e8b3d3fed0a90a8b632aa4eb311cb
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198152"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Vyžádat data v reálném čase pomocí služby Azure Maps mobility

V tomto článku se dozvíte, jak používat [službu Azure Maps mobility](https://aka.ms/AzureMapsMobilityService) k vyžádání přenosových dat v reálném čase.

V tomto článku se dozvíte, jak:


 * Požádat o další doručení v reálném čase pro všechny řádky přicházející v daném zastavení
 * Vyžádat informace v reálném čase pro příslušnou dokovací stanici kola


## <a name="prerequisites"></a>Předpoklady

Nejprve musíte mít účet Azure Maps a klíč předplatného, aby bylo možné volat rozhraní API pro veřejné průjezdy Azure Maps. Informace najdete v pokynech v tématu [Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) pro vytvoření účtu Azure Maps. Použijte k získání primárního klíče pro váš účet postup uvedený v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) . Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).


V tomto článku se k sestavení volání REST používá [aplikace pro publikování](https://www.getpostman.com/apps) . Můžete použít libovolné vývojové prostředí API, které dáváte přednost.


## <a name="request-real-time-arrivals-for-a-stop"></a>Vyžádat doručení v reálném čase o zastavení

Aby bylo možné vyžádat data o doručení konkrétního zastavení veřejného přenosu v reálném čase, budete muset požádat o [rozhraní API doručení](https://aka.ms/AzureMapsMobilityRealTimeArrivals) Azure Maps [služby mobility](https://aka.ms/AzureMapsMobilityService)v reálném čase. K dokončení žádosti budete potřebovat **metroID** a **stopID** . Další informace o tom, jak si vyžádat tyto parametry, najdete v naší příručce o tom, jak [vyžádat trasy veřejného přenosu](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Pojďme použít "522" jako naše ID Metro, což je ID Metro pro oblast "Praha – Tacoma – Bellevue, WA". Jako ID zastavení použijte "522---2060603", tato Sběrnicová zastávka je "ne 24 července St & 162nd Ave No Bellevue WA". Pokud chcete požádat o dalších pět dat o doručení v reálném čase, pro všechna další živá přijetí v tomto zastavení proveďte následující kroky:

1. Otevřete aplikaci pro odesílání a pojďme vytvořit kolekci pro uložení požadavků. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **kolekce**.  Pojmenujte kolekci a vyberte tlačítko **vytvořit** .

2. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozím kroku, jako umístění, kam se má žádost Uložit. Potom vyberte **Uložit**.

    ![Vytvoření žádosti v post](./media/how-to-request-transit-data/postman-new.png)

3. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL pro vytvoření žádosti o získání. Nahraďte `{subscription-key}`primárním klíčem Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Po úspěšné žádosti obdržíte následující odpověď.  Všimněte si, že parametr ' scheduleType ' definuje, zda je předpokládaná doba doručení založena na statických datech v reálném čase.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Data v reálném čase pro dokovací stanice kol

[Rozhraní API pro získání informací o přenosných dockech](https://aka.ms/AzureMapsMobilityTransitDock) umožňuje uživatelům požadovat statické informace a informace v reálném čase. Například uživatelé mohou požadovat dostupnost a informace o volném místě pro obdobu nebo Scooter dokovací stanice. [Rozhraní API pro přístup k informacím o získání](https://aka.ms/AzureMapsMobilityTransitDock) dat je také součástí [služby Azure Maps mobility](https://aka.ms/AzureMapsMobilityService).

Aby bylo možné vytvořit požadavek na [rozhraní API pro získání informací o přeložení](https://aka.ms/AzureMapsMobilityTransitDock), budete potřebovat **dockId** pro tuto stanici. ID Dock můžete získat tak, že v [rozhraní API pro získání přenosu](https://aka.ms/AzureMapsMobilityNearbyTransit) s parametrem **ObjectType** přiřazeným k bikeDock vytvoříte žádost o vyhledávání. Podle následujících kroků Získejte data dokovací stanice pro kola v reálném čase.


### <a name="get-dock-id"></a>Získat ID Docku

Pokud chcete získat **dockID**, postupujte podle následujících kroků a vytvořte si požadavek na rozhraní API pro získání nejbližšího přenosu:

1. V příspěvku klikněte na **nový požadavek** | **získat žádost** a název, který **získá ID Dock**.

2.  Na kartě tvůrce vyberte metodu **Get** http, zadejte následující adresu URL požadavku a klikněte na **Odeslat**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Po úspěšné žádosti obdržíte následující odpověď. Všimněte si, že teď máme **ID** v odpovědi, které se dá použít později jako parametr dotazu v požadavku do rozhraní API pro získání informací o přeložení k přenosu.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Získat stav Dock kol na kolo v reálném čase

Použijte následující postup, chcete-li vytvořit požadavek na rozhraní API získat informace o tom, jak získat data pro vybrané ukotvení v reálném čase.

1. V příspěvku klikněte na **nový požadavek** | **získat žádost** a pojmenujte si **data Dock v reálném čase**.

2.  Na kartě tvůrce vyberte metodu **Get** http, zadejte následující adresu URL požadavku a klikněte na **Odeslat**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Po úspěšné žádosti obdržíte odpověď na následující strukturu:

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Další kroky

Informace o tom, jak požadovat přenosová data pomocí služby mobility:

> [!div class="nextstepaction"]
> [Požadavky na přenosová data](how-to-request-transit-data.md)

Prozkoumejte dokumentaci k rozhraní API služby Azure Maps mobility:

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní API služby mobility](https://aka.ms/AzureMapsMobilityService)
