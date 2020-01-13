---
title: Jak požadovat data o přenosech v Azure Maps | Microsoft Docs
description: Vyžádejte si data veřejného přenosu pomocí služby Azure Maps mobility.
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 47952421c50979a014507c81ef5bc3217df7d860
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432938"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Vyžádání údajů o veřejném přenosu pomocí služby Azure Maps mobility 

V tomto článku se dozvíte, jak používat [službu Azure Maps mobility](https://aka.ms/AzureMapsMobilityService) k vyžádání údajů o veřejném přenosu, včetně informací o zastavení, trasách a odhadech doby cesty.

V tomto článku se dozvíte, jak:

* Získání ID oblasti metro pomocí [rozhraní API pro získání oblasti](https://aka.ms/AzureMapsMobilityMetro) Metro
* Žádost o dosažení okolního přenosu přestane používat službu [Get okolního přenosu](https://aka.ms/AzureMapsMobilityNearbyTransit) .
* K naplánování trasy pomocí veřejného přenosu je možné [využít rozhraní API pro přenos tras](https://aka.ms/AzureMapsMobilityTransitRoute) .
* Vyžádejte si geometrii trasy přenosu a podrobný plán pro trasu pomocí [rozhraní API pro získání přenosové](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)trasy.


## <a name="prerequisites"></a>Požadavky

Aby bylo možné volat Azure Maps rozhraní API pro veřejné průjezdy, potřebujete účet a klíč mapy. Pokud chcete získat informace o vytvoření účtu a získání klíče, postupujte podle pokynů v části [Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) pro vytvoření předplatného účtu Azure Maps a postupujte podle kroků v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) pro získání primárního klíče pro váš účet. Další podrobnosti o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).


V tomto článku se k sestavení volání REST používá [aplikace pro publikování](https://www.getpostman.com/apps) . Můžete použít libovolné vývojové prostředí API, které dáváte přednost.


## <a name="get-a-metro-area-id"></a>Získat ID oblasti metro

Aby bylo možné požadovat informace o přenosech pro konkrétní metropolitní oblast, budete potřebovat `metroId` pro oblast, pro kterou chcete požádat o přenosová data. [Získat rozhraní API oblasti metro](https://aka.ms/AzureMapsMobilityMetro) vám umožní vyžádat oblasti metro, ve kterých je dostupná služba Azure Maps mobility. Odpověď obsahuje podrobnosti, jako je `metroId`, `metroName` a reprezentace geometrie oblasti metro ve formátu geometrických JSON.

Pojďme si vytvořit žádost o získání oblasti metro pro ID oblasti Tacoma Metro v Seattlu. Chcete-li požádat o ID oblasti metro, proveďte následující kroky:

1. Vytvořte kolekci, do které se mají ukládat požadavky. V aplikaci pro odesílání vyberte **Nový**. V okně **vytvořit nové** vyberte **kolekce**. Pojmenujte kolekci a vyberte tlačítko **vytvořit** .

2. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** , vyberte kolekci, kterou jste vytvořili v předchozím kroku, jako umístění, kam chcete žádost uložit, a pak vyberte **Uložit**.
    
    ![Vytvoření žádosti v post](./media/how-to-request-transit-data/postman-new.png)

3. Na kartě tvůrce vyberte metodu GET HTTP a zadejte následující adresu URL pro vytvoření žádosti o získání.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Po úspěšné žádosti se zobrazí následující odpověď:

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

5. Zkopírujte `metroId`pro pozdější použití.

## <a name="request-nearby-transit-stops"></a>Žádost o ukončení přechodu na okolí

Služba Azure Maps [získat nejbližší tranzitní](https://aka.ms/AzureMapsMobilityNearbyTransit) službu umožňuje vyhledávat přenosové objekty, například zastavení veřejného přenosu a sdílená kola kolem daného umístění, která vrací podrobnosti o objektu přenosu. V dalším kroku zašleme žádost službě, aby provedla hledání nejbližšího veřejného přenosu v rámci 300 měřičů v daném umístění. V žádosti musíme zahrnout `metroId` načtený dříve.

Chcete-li vytvořit požadavek na [dosažení nejbližšího přenosu](https://aka.ms/AzureMapsMobilityNearbyTransit), postupujte podle následujících kroků:

1. V příspěvku klikněte na **nový požadavek** | **získat žádost** a pojmenujte si ho v **nejbližším zastavení**.

2. Na kartě tvůrce vyberte metodu **Get** http, zadejte následující adresu URL pro koncový bod rozhraní API a klikněte na **Odeslat**.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Po úspěšné žádosti by struktura odpovědi měla vypadat jako na následujícím obrázku:

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

Pokud pozoruje strukturu odpovědi pečlivě, vidíte, že obsahuje parametry každého tranzitního objektu, například `id`, `type`, `stopName`, `mainTransitType`, `mainAgencyName` a pozice (souřadnice) objektu.

Pro účely porozumění použijeme `id` jednoho ze sběrnice, která se zastaví jako zdroj pro naši trasu v další části.  


## <a name="request-a-transit-route"></a>Požadavek na přenosový postup

[Rozhraní API pro přenos tras](https://aka.ms/AzureMapsMobilityTransitRoute) v Azure Maps umožňuje plánování cest, které vrací nejlepší možné možnosti směrování mezi zdrojem a cílem. Služba poskytuje celou řadu cestovních režimů, včetně procházení, cyklistice a veřejného průjezdu. Dále prohledáme trasu z nejbližšího zarážky sběrnice na místo v Seattlu.

### <a name="get-location-coordinates-for-destination"></a>Získat souřadnice umístění pro cíl

Pro účely získání souřadnic umístění pro jehlu místa umožňuje použít Azure Maps [fuzzy Search Service](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Chcete-li vytvořit požadavek na službu pro vyhledávání přibližných shod, postupujte podle následujících kroků:

1. V příspěvku klikněte na **nový požadavek** | **získat požadavek** a pojmenujte ho **souřadnice umístění**.

2.  Na kartě tvůrce vyberte metodu **Get** http, zadejte následující adresu URL požadavku a klikněte na **Odeslat**.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Pokud se podíváte na odpověď pečlivě, obsahuje několik míst ve výsledcích pro jehlu místa a obsahuje také informace o souřadnicích pro každé z nich v **umístění**. Zkopírovat `lat` a `lon` z pozice prvního výsledku.
    
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
    

### <a name="request-route"></a>Požadavek na směrování

Chcete-li vytvořit požadavek na směrování, proveďte následující kroky:

1. V příspěvku klikněte na **nový požadavek** | **získat žádost** a pojmenujte ho **získat informace o trasách**.

2. Na kartě tvůrce vyberte metodu **Get** http, zadejte následující adresu URL pro koncový bod rozhraní API a klikněte na **Odeslat**.

    Zadáním parametrů `modeType` a `transitType` vyžádáme trasy veřejných přenosů pro sběrnici. Adresa URL požadavku obsahuje umístění získaná v předchozích částech. Jak `originType` máme teď **stopid** a jako `destionationType` máme **pozici**.

    Podívejte se na [seznam parametrů identifikátoru URI](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) , které můžete ve své žádosti použít pro [rozhraní API získat přenos tras](https://aka.ms/AzureMapsMobilityTransitRoute). 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Po úspěšné žádosti by struktura odpovědi měla vypadat jako na následujícím obrázku:

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

4. Pokud pečlivě prodržíte pozor, v odpovědi se nachází více tras **Sběrnice** . Každá trasa má jedinečné **ID itineráře** a souhrn, který popisuje jednotlivé fáze trasy. V dalším kroku budeme podávat podrobnosti o nejrychlejší trase pomocí `itineraryId` v odpovědi.

## <a name="request-fastest-route-itinerary"></a>Požadavek na nejrychlejší trasu trasy

Služba Azure Maps [získat přenosovou trasu](https://aka.ms/AzureMapsMobilityTransitItinerary) umožňuje vyžádat si data pro konkrétní trasu pomocí **ID itinerář** trasy, které vrací služba [API pro získání přenosných tras](https://aka.ms/AzureMapsMobilityTransitRoute) . Pokud chcete vytvořit žádost, proveďte následující kroky:

1. V příspěvku klikněte na **nový požadavek** | **získat žádost** a pojmenujte ho **získat informace o přenosu**.

2. Na kartě tvůrce vyberte metodu **Get** http, zadejte následující adresu URL pro koncový bod rozhraní API a klikněte na **Odeslat**.

    Nastavíme parametr `detailType` na **geometrii** tak, aby odpověď obsahovala informace o stopě pro veřejný průjezd a přepnula navigaci pro procházení a nohy kol v trase.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. Po úspěšné žádosti by struktura odpovědi měla vypadat jako na následujícím obrázku:

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

Informace o tom, jak vyžádat data v reálném čase pomocí služby mobility:

> [!div class="nextstepaction"]
> [Jak vyžádat data v reálném čase](how-to-request-real-time-data.md)

Prozkoumejte dokumentaci k rozhraní API služby Azure Maps mobility

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní API služby mobility](https://aka.ms/AzureMapsMobilityService)

