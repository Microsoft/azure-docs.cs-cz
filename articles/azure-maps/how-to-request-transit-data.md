---
title: Žádost o údaje o tranzitu | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak požádat o data veřejné dopravy pomocí služby Mobility Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f60b66790342874620971c8f15a1e8ace9a3c7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335461"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Vyžádejte si údaje o veřejné dopravě pomocí služby Mobility Azure Maps 

V tomto článku se ukazuje, jak pomocí [služby Mobility](https://aka.ms/AzureMapsMobilityService) Azure Maps požádat o data veřejné dopravy. Údaje o tranzitu zahrnují tranzitní zastávky, informace o trase a odhady doby jízdy.

V tomto článku se dozvíte, jak:

* Získání ID oblasti metra pomocí [rozhraní Get Metro Area API](https://aka.ms/AzureMapsMobilityMetro)
* Žádost v blízkosti tranzitní zastaví pomocí [Get Near Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) služby.
* Dotaz [Získat rozhraní API pro získání tranzitních tras](https://aka.ms/AzureMapsMobilityTransitRoute) pro plánování trasy pomocí veřejné dopravy.
* Vyžádejte si geometrii tranzitní trasy a podrobný plán trasy pomocí [rozhraní Get Transit Itinerary API](https://aka.ms/https://azure.microsoft.com/services/azure-maps/).


## <a name="prerequisites"></a>Požadavky

Abyste mohli volat na rozhraní API pro veřejnou dopravu v Azure Maps, musíte mít nejdřív účet Azure Maps a klíč předplatného. Další informace najdete v následujících pokynech [k vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) Azure Maps. Postupujte podle kroků v [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) a získejte primární klíč pro váš účet. Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](./how-to-manage-authentication.md).


Tento článek používá [aplikaci Postman](https://www.getpostman.com/apps) k vytváření volání REST. Můžete použít libovolné vývojové prostředí rozhraní API, které upřednostňujete.


## <a name="get-a-metro-area-id"></a>Získání ID oblasti metra

Chcete-li požádat o informace o tranzitu `metroId` pro určitou metropolitní oblast, budete potřebovat tuto oblast. [Rozhraní Get Metro Area API](https://aka.ms/AzureMapsMobilityMetro) umožňuje požadovat oblasti metra, ve kterých je dostupná služba Mobility Azure Maps. Odpověď obsahuje `metroId`podrobnosti, `metroName`jako je například , a reprezentace geometrie oblasti metra ve formátu GeoJSON.

Pojďme požádat, aby si metro prostoru pro Seattle-Tacoma metro oblasti ID. Chcete-li požádat o ID pro oblast metra, proveďte následující kroky:

1. Otevřete aplikaci Postman a pojďme vytvořit kolekci pro uložení požadavků. V horní části aplikace Pošťák vyberte **Nový**. V okně **Vytvořit novou** vyberte **kolekci**.  Pojmenujte kolekci a vyberte tlačítko **Vytvořit.**

2. Chcete-li vytvořit požadavek, vyberte znovu **Nový.** V okně **Vytvořit nový** vyberte **Request**. Zadejte **název požadavku** pro požadavek. Vyberte kolekci, kterou jste vytvořili v předchozím kroku jako umístění, do kterého chcete požadavek uložit. Potom vyberte **Uložit**.
    
    ![Vytvoření požadavku v Pošťákovi](./media/how-to-request-transit-data/postman-new.png)

3. Vyberte metodu **GET** HTTP na kartě tvůrce a zadejte následující adresu URL pro vytvoření požadavku GET. Nahraďte `{subscription-key}`primárním klíčem Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Po úspěšné žádosti obdržíte následující odpověď:

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

5. Zkopírujte `metroId`, budeme ji muset použít později.

## <a name="request-nearby-transit-stops"></a>Žádost o zastávky v okolí

Služba Azure Maps [Get Near Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) umožňuje vyhledávat objekty přenosu.  rozhraní API vrátí podrobnosti o tranzitním objektu, jako jsou zastávky veřejné dopravy a sdílená kola kolem daného umístění. Dále požádáme službu, aby v ykortu v okruhu 300 metrů kolem dané lokality vyhledala nedaleké zastávky veřejné dopravy. V žádosti musíme zahrnout `metroId` načtené dříve.

Chcete-li podat žádost na službu [Get Near Transit](https://aka.ms/AzureMapsMobilityNearbyTransit), postupujte podle následujících kroků:

1. V pošťáku klikněte na**Žádost GET** **nové žádosti** | a pojmenujte ji **Získat blízké zastávky**.

2. Na kartě Tvůrce vyberte metodu **GET** HTTP, zadejte následující adresu URL požadavku pro koncový bod rozhraní API a klepněte na tlačítko **Odeslat**.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Po úspěšné žádosti by měla struktura odpovědí vypadat jako následující:

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    }   
    ```

Pokud pečlivě sledujete strukturu odezvy, uvidíte, že obsahuje parametry pro každý objekt přenosu. Každý tranzitní objekt má `id`parametry, jako je například `type`, `stopName`, `mainTransitType`, `mainAgencyName`a umístění objektu v souřadnicích.

Pro účely učení použijeme autobusovou `id` zastávku jako původ pro naši trasu v další části.  


## <a name="request-a-transit-route"></a>Žádost o tranzitní trasu

Rozhraní AZURE Maps [Get Transit Routes API](https://aka.ms/AzureMapsMobilityTransitRoute) umožňuje plánování cest. Vrátí nejlepší možné možnosti trasy z počátku do cíle. Služba poskytuje různé druhy cestovních režimů, včetně chůze, cyklistiky a veřejné dopravy. Dále prohledáme trasu z nejbližší autobusové zastávky k věži Space Needle v Seattlu.

### <a name="get-location-coordinates-for-destination"></a>Získat souřadnice polohy pro cíl

Chcete-li získat souřadnice umístění věže Space Needle, umožňuje použít Azure Maps [Fuzzy Search Service](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Chcete-li podat žádost službě fuzzy vyhledávání, postupujte podle následujících kroků:

1. V pošťáku klikněte na**Žádost GET** **nové žádosti** | a pojmenujte ji **Získat souřadnice polohy**.

2.  Na kartě Tvůrce vyberte metodu **GET** HTTP, zadejte následující adresu URL požadavku a klepněte na tlačítko **Odeslat**.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Pokud se podíváte na odpověď pečlivě, obsahuje více míst ve výsledcích pro vyhledávání Space Needle. Každý výsledek obsahuje souřadnice umístění pod **pozicí**. Zkopírujte `lat` `lon` a pod **pozici** prvního výsledku.
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
                    "countrySubdivisionName": "Washington"
                },
                "position": {
                    "lat": 47.62039,
                    "lon": -122.34928
                },
                "viewport": {
                    "topLeftPoint": {
                        "lat": 47.62129,
                        "lon": -122.35061
                    },
                    "btmRightPoint": {
                        "lat": 47.61949,
                        "lon": -122.34795
                    }
                },
                "entryPoints": [
                    {
                        "type": "main",
                        "position": {
                            "lat": 47.61982,
                            "lon": -122.34886
                        }
                    }
                ]
            },
            ...,
            ...,
            ...
        ]
    }
    ``` 
    

### <a name="request-route"></a>Trasa požadavku

Chcete-li podat žádost o trasu, proveďte následující kroky:

1. V pošťáku klikněte na**Žádost GET** **nové žádosti** | a pojmenujte ji **Získat informace o trase**.

2. Na kartě Tvůrce vyberte metodu **GET** HTTP, zadejte následující adresu URL požadavku pro koncový bod rozhraní API a klepněte na tlačítko **Odeslat**.

    Budeme požadovat trasy veřejné dopravy pro autobus `modeType` zadáním `transitType` a parametry. Adresa URL požadavku obsahuje umístění načtená v předchozích částech. Pro `originType`, nyní máme **stopId**. A pro `destionationType`, máme **pozici**.

    Podívejte se na [seznam parametrů IDENTIFIKÁTORU URI, které](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) můžete použít v požadavku na rozhraní API get transit [routes](https://aka.ms/AzureMapsMobilityTransitRoute). 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Na základě úspěšné žádosti by struktura odpovědí měla vypadat jako ta níže:

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ]
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ]
            }
        ]
    }
    ```

4. Pokud budete pečlivě sledovat, existuje více **autobusových** linek v odpovědi. Každá trasa má jedinečné **ID itineráře** a souhrn, který popisuje každou část trasy. Úsek trasy je součástí trasy mezi dvěma body zastávky. Dále si vyžádáme podrobnosti o `itineraryId` nejrychlejší trase pomocí odpovědi.

## <a name="request-fastest-route-itinerary"></a>Vyžádejte si nejrychlejší itinerář trasy

Služba Azure Maps Get Transit Itinerary vám umožňuje [vyžádat](https://aka.ms/AzureMapsMobilityTransitItinerary) si data pro konkrétní trasu pomocí **ID itineráře** trasy vrácené službou [Rozhraní API get transit routes.](https://aka.ms/AzureMapsMobilityTransitRoute) Chcete-li podat žádost, proveďte následující kroky:

1. V pošťáku klikněte na**Žádost GET** **nové žádosti** | a pojmenujte ji **Získat informace o tranzitu**.

2. Na kartě Tvůrce vyberte metodu **GET** HTTP. Zadejte následující adresu URL požadavku pro koncový bod rozhraní API a klepněte na tlačítko **Odeslat**.

    Parametr nastavíme `detailType` na **geometrii** tak, aby odpověď obsahovala informace o zastavení veřejné dopravy a navigaci podle pořadí pro chůzi a cyklonohy trasy.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. Na základě úspěšné žádosti by struktura odpovědí měla vypadat jako ta níže:

    ```JSON
    {
        "departureTime": "2019-09-07T01:01:50Z",
        "arrivalTime": "2019-09-07T02:16:33Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:01:50Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:26:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2060604",
                        "stopKey": "71310",
                        "stopName": "NE 24th St & 160th Ave NE",
                        "stopCode": "71310",
                        "position": {
                            "latitude": 47.631565,
                            "longitude": -122.127808
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    ...,
                    ...,
                    {
                        "stopId": "522---2061704",
                        "stopKey": "74451",
                        "stopName": "Northup Way & NE 33rd Pl",
                        "stopCode": "74451",
                        "position": {
                            "latitude": 47.640911,
                            "longitude": -122.194443
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.12527,
                            47.63143
                        ],
                        [
                            -122.12529,
                            47.63143
                        ],
                        [
                            -122.12561,
                            47.63144
                        ],
                        [
                            -122.12701,
                            47.63148
                        ],
                        ...,
                        ...,
                        ...,
                        [
                            -122.19601,
                            47.64304
                        ],
                        [
                            -122.19584,
                            47.64315
                        ],
                        [
                            -122.19677,
                            47.6438
                        ]
                    ]
                }
            },
            ...,
            ...,
            ...,
            {
                "legType": "Walk",
                "legStartTime": "2019-09-07T02:12:42Z",
                "legEndTime": "2019-09-07T02:16:33Z",
                "steps": [
                    {
                        "direction": {
                            "relativeDirection": "depart"
                        },
                        "streetName": "Denny Way"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "4th Avenue North"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "Broad Street"
                    }
                ],
                "origin": {
                    "position": {
                        "latitude": 47.618578,
                        "longitude": -122.348058
                    }
                },
                "destination": {
                    "position": {
                        "latitude": 47.62039,
                        "longitude": -122.34928
                    }
                },
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.34806,
                            47.61857
                        ],
                        [
                            -122.3481,
                            47.61857
                        ],
                        [
                            -122.34894,
                            47.61858
                        ],
                        [
                            -122.34892,
                            47.61964
                        ],
                        [
                            -122.34877,
                            47.61975
                        ],
                        [
                            -122.3492,
                            47.62001
                        ],
                        [
                            -122.34918,
                            47.62003
                        ],
                        [
                            -122.34917,
                            47.62006
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34928,
                            47.62039
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Další kroky

Naučte se, jak požádat o data v reálném čase pomocí služby Mobility Service:

> [!div class="nextstepaction"]
> [Jak požádat o data v reálném čase](how-to-request-real-time-data.md)

Projděte si dokumentaci k rozhraní API služby Azure Maps Mobility Service

> [!div class="nextstepaction"]
> [Dokumentace rozhraní API služby mobility](https://aka.ms/AzureMapsMobilityService)

