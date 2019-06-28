---
title: Jak si vyžádat data v reálném čase ve službě Azure Maps | Dokumentace Microsoftu
description: Žádost o data v reálném čase pomocí služby Azure Maps Mobility.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: aaab5ef4d8fc3d60a12f9e9f85f2846695fd1ab4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329664"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Žádost o data v reálném čase pomocí služby Mobility Azure Maps

V tomto článku se dozvíte, jak používat Azure Maps [služby Mobility](https://aka.ms/AzureMapsMobilityService) požadovat data v reálném čase přenosu.

V tomto článku se dozvíte, jak:


 * Požádat o další příchozích v reálném čase pro všechny řádky přicházejících u daného stop
 * Žádost o informace v reálném čase pro danou kol dokovací stanice.


## <a name="prerequisites"></a>Požadavky

Aby všechna volání do veřejné přenosu Azure Maps rozhraní API, budete potřebovat účet Maps a klíč. Informace o vytváření účtu a klíč načítání, najdete v části [jak spravovat účet Azure Maps a klíče](how-to-manage-account-keys.md).

Tento článek používá [aplikaci Postman](https://www.getpostman.com/apps) vytvářet volání REST. Můžete použít všechny vývojové prostředí rozhraní API, které dáváte přednost.


## <a name="request-real-time-arrivals-for-a-stop"></a>Žádost o přijetí v reálném čase pro zarážku

Aby bylo možné požádat o data v reálném čase doručení pro konkrétní veřejné přenosu stop, budete muset vytvořit žádost o [rozhraní API v reálném čase doručení](https://aka.ms/AzureMapsMobilityRealTimeArrivals) Azure Maps [služby Mobility](https://aka.ms/AzureMapsMobilityService). Budete potřebovat **metroID** a **stopID** dokončit žádost. Další informace o tom, jak tyto parametry žádosti, naleznete v tématu naše postupy průvodce [požádat o veřejné tranzitní směrování](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Umožňuje použít jako naše metro "522" ID, které je metro ID pro oblasti "Seattle – Tacoma – Bellevue, WA" a potom použijte ID stop "2060603", které je na sběrnici zastavit na "Ne 24th St & ložit 162nd Ne, Bellevue WA". Žádost o následujících pět data v reálném čase doručení pro všechny další živé doručení na tomto stop, proveďte následující kroky:

1. Vytvořte kolekci, ve kterých se mají ukládat požadavky. V aplikaci Postman vyberte **nový**. V **vytvořit nový** okně **kolekce**. Název kolekce a vyberte **vytvořit** tlačítko.

2. Pokud chcete vytvořit žádost, vyberte **nový** znovu. V **vytvořit nový** okně **požádat o**. Zadejte **název žádosti** žádosti, vyberte kolekci, do které jste vytvořili v předchozím kroku jako umístění, ve kterém uložte požadavek a pak vyberte **Uložit**.

    ![Vytvořit žádost o v nástroji Postman](./media/how-to-request-transit-data/postman-new.png)

3. Vyberte metodu GET HTTP na kartě tvůrce a zadejte následující adresu URL k vytvoření žádosti GET.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=2060603&transitType=bus
    ```

4. Po úspěšném požadavku zobrazí se následující odpověď.  Všimněte si, že tento parametr 'parametr-scheduleType' Definuje, zda čas doručení odhadované podle dat v reálném čase nebo statické.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 4,
                "scheduleType": "realTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 30,
                "scheduleType": "scheduledTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Data v reálném čase pro kol dokovací stanice

[Získat informace o API Dock přenosu](https://aka.ms/AzureMapsMobilityTransitDock) služby Mobility Azure Maps, umožňuje požádat o statické, tak v reálném čase informace, jako je dostupnost a volných informace pro danou kolo nebo scooter dokovací stanice. Učiníme požadavek na získání dat v reálném čase pro dokovací stanice kol.

Aby bylo možné žádost o získání přenosu Dock informace o API, budete potřebovat **dockId** pro příslušnou stanici. Můžete získat ID ukotvení tím, že žádost o vyhledávání na [získání rozhraní API blízké přenosu](https://aka.ms/AzureMapsMobilityNearbyTransit) a nastavení **objectType** parametr "bikeDock". Podle následujících pokynů k získání dat v reálném čase z dokovací stanice kol.


### <a name="get-dock-id"></a>Získejte ID ukotvení

Chcete-li získat **dockID**, postupujte podle pokynů můžete vytvořit žádost o získání blízké přenosu rozhraní API:

1. V nástroji Postman, klikněte na tlačítko **novou žádost o** | **požadavek GET** a pojmenujte ho **Get ukotvit ID**.

2.  Na kartě tvůrce, vyberte **získat** metodu HTTP, zadejte následující adresu URL požadavku a klikněte na tlačítko **odeslat**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Po úspěšném požadavku zobrazí se následující odpověď. Všimněte si, že nyní je k dispozici **id** v odpovědi, které je možné později jako parametr dotazu v žádosti o získání přenosu Dock informace o API.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 30,
                    "lastUpdated": "2019-05-21T20:06:59-04:00",
                    "operatorInfo": {
                        "id": "80",
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


### <a name="get-real-time-bike-dock-status"></a>Získání stavu v reálném čase kol ukotvení

Postupujte podle pokynů můžete vytvořit žádost o získání přenosu ukotvit informace o rozhraní API k získání dat v reálném čase pro vybrané docku.

1. V nástroji Postman, klikněte na tlačítko **novou žádost o** | **požadavek GET** a pojmenujte ho **získání dat v reálném čase dock**.

2.  Na kartě tvůrce, vyberte **získat** metodu HTTP, zadejte následující adresu URL požadavku a klikněte na tlačítko **odeslat**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Po úspěšné žádosti se zobrazí odpověď následující strukturu:

    ```JSON
    {
        "availableVehicles": 1,
        "vacantLocations": 29,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-05-21T20:26:47-04:00",
        "operatorInfo": {
            "id": "80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Další postup

Zjistěte, jak požádat o přenosu dat pomocí služby Mobility:

> [!div class="nextstepaction"]
> [Jak si vyžádat přenosu dat](how-to-request-transit-data.md)

Projděte si dokumentaci rozhraní API služby Mobility Azure Maps:

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní API služby mobility](https://aka.ms/AzureMapsMobilityService)
