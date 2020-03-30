---
title: Žádost o údaje o tranzitu v reálném čase | Mapy Microsoft Azure
description: Vyžádejte si data v reálném čase pomocí služby Mobility Map Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3145181a863bf8188dd0b0bb52cd2efc662ce2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335480"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Vyžádejte si data v reálném čase pomocí služby Mobility Azure Maps

Tento článek ukazuje, jak pomocí [služby](https://aka.ms/AzureMapsMobilityService) Mobility Azure Maps požadovat data o přenosu v reálném čase.

V tomto článku se dozvíte, jak:


 * Žádost o další příjezdy v reálném čase pro všechny linky, které přijíždějí na danou zastávku
 * Vyžádejte si informace v reálném čase pro danou dokovací stanici pro cyklisty.


## <a name="prerequisites"></a>Požadavky

Abyste mohli volat na rozhraní API pro veřejnou dopravu v Azure Maps, musíte mít nejdřív účet Azure Maps a klíč předplatného. Další informace najdete v následujících pokynech [k vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) Azure Maps. Postupujte podle kroků v [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) a získejte primární klíč pro váš účet. Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](./how-to-manage-authentication.md).


Tento článek používá [aplikaci Postman](https://www.getpostman.com/apps) k vytváření volání REST. Můžete použít libovolné vývojové prostředí rozhraní API, které upřednostňujete.


## <a name="request-real-time-arrivals-for-a-stop"></a>Žádost o příjezdy v reálném čase pro zastávku

Chcete-li požádat o data o příchozích v reálném čase na konkrétní zastávce veřejné dopravy, budete muset požádat [rozhraní API pro příchozí](https://aka.ms/AzureMapsMobilityRealTimeArrivals) v reálném čase [služby](https://aka.ms/AzureMapsMobilityService)Azure Maps Mobility Service . K dokončení žádosti budete potřebovat **metroID** a **stopID.** Další informace o tom, jak tyto parametry vyžádat, najdete v našem průvodci, jak [požádat o trasy veřejné dopravy](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Pojďme použít "522" jako naše metro ID, což je metro ID pro "Seattle-Tacoma-Bellevue, WA" oblasti. Použijte "522---2060603" jako ID zastávky, tato autobusová zastávka je na adrese "Ne 24th St & 162nd Ave Ne, Bellevue WA". Chcete-li požádat o dalších pět údajů o příjezdech v reálném čase, pro všechny další živé příjezdy na tuto zastávku proveďte následující kroky:

1. Otevřete aplikaci Postman a pojďme vytvořit kolekci pro uložení požadavků. V horní části aplikace Pošťák vyberte **Nový**. V okně **Vytvořit novou** vyberte **kolekci**.  Pojmenujte kolekci a vyberte tlačítko **Vytvořit.**

2. Chcete-li vytvořit požadavek, vyberte znovu **Nový.** V okně **Vytvořit nový** vyberte **Request**. Zadejte **název požadavku** pro požadavek. Vyberte kolekci, kterou jste vytvořili v předchozím kroku, jako umístění, do kterého chcete požadavek uložit. Potom vyberte **Uložit**.

    ![Vytvoření požadavku v Pošťákovi](./media/how-to-request-transit-data/postman-new.png)

3. Vyberte metodu **GET** HTTP na kartě tvůrce a zadejte následující adresu URL pro vytvoření požadavku GET. Nahraďte `{subscription-key}`primárním klíčem Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Po úspěšné žádosti obdržíte následující odpověď.  Všimněte si, že parametr scheduleType definuje, zda je odhadovaný čas doručení založen na datech v reálném čase nebo statických dat.

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


## <a name="real-time-data-for-bike-docking-station"></a>Data v reálném čase pro dokovací stanici pro cyklisty

[Rozhraní Get Transit Dock Info API](https://aka.ms/AzureMapsMobilityTransitDock) umožňuje uživatelům požadovat statické informace a informace v reálném čase. Uživatelé mohou například požádat o informace o dostupnosti a volných místech pro kolo nebo dokovací stanici na koloběžkách. [Rozhraní Get Transit Dock Info API](https://aka.ms/AzureMapsMobilityTransitDock) je také součástí [služby mobility](https://aka.ms/AzureMapsMobilityService)Azure Maps .

Chcete-li podat žádost do [rozhraní Get Transit Dock Info API](https://aka.ms/AzureMapsMobilityTransitDock), budete potřebovat **dockId** pro tuto stanici. ID ukotvení můžete získat tak, že požadavek na vyhledávání [do get near transit API](https://aka.ms/AzureMapsMobilityNearbyTransit) s **parametrem objectType** přiřazeným k "bikeDock". Podle následujících kroků získáte data dokovací stanice pro kola v reálném čase.


### <a name="get-dock-id"></a>Získat ID doku

Chcete-li získat **dockID**, postupujte podle následujících kroků a požádejte o rozhraní Get Near Transit API:

1. V pošťáku klikněte na**Žádost GET** **nové žádosti** | a pojmenujte ji **Get dock ID**.

2.  Na kartě Tvůrce vyberte metodu **GET** HTTP, zadejte následující adresu URL požadavku a klepněte na tlačítko **Odeslat**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Po úspěšné žádosti obdržíte následující odpověď. Všimněte si, že nyní máme **id** v odpovědi, které lze použít později jako parametr dotazu v požadavku na získat rozhraní API informace o doku tranzitu.

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


### <a name="get-real-time-bike-dock-status"></a>Získejte stav doku pro kola v reálném čase

Podle následujících kroků vytvořte požadavek na rozhraní Get Transit Dock Info API, abyste získali data pro vybraný překladiště v reálném čase.

1. V pošťáku klikněte na**Žádost GET** **nové žádosti** | a pojmenujte ji **Získejte data docku v reálném čase**.

2.  Na kartě Tvůrce vyberte metodu **GET** HTTP, zadejte následující adresu URL požadavku a klepněte na tlačítko **Odeslat**.
 
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

Přečtěte si, jak požádat o údaje o tranzitu pomocí služby Mobility Service:

> [!div class="nextstepaction"]
> [Jak požádat o údaje o tranzitu](how-to-request-transit-data.md)

Seznamte se s dokumentací k rozhraní AZURE Maps Mobility Service API:

> [!div class="nextstepaction"]
> [Dokumentace rozhraní API služby mobility](https://aka.ms/AzureMapsMobilityService)
