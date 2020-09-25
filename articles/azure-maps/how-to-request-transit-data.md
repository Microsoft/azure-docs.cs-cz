---
title: Požadavek na přenosová data pomocí služby mobility Microsoft Azure Maps
description: Naučte se používat službu Azure Maps mobility k vyžádání dat veřejného přenosu, jako jsou ID oblasti metro, zastavení přenosu, trasy a cesty trasy.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/22/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3ded5f6186d8ccdae0f42ecb9942c70e09f2dd9f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310879"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Vyžádání údajů o veřejném přenosu pomocí služby Azure Maps mobility

V tomto článku se dozvíte, jak používat [službu Azure Maps mobility](https://aka.ms/AzureMapsMobilityService) k vyžádání dat veřejného přenosu. Přenosová data zahrnují zastavení přenosu, informace o trasách a odhady doby cesty.

V tomto článku se dozvíte, jak:

* Získání ID oblasti metro pomocí [rozhraní API pro získání oblasti](https://aka.ms/AzureMapsMobilityMetro) Metro
* Žádost o dosažení okolního přenosu přestane používat službu [Get okolního přenosu](https://aka.ms/AzureMapsMobilityNearbyTransit) .
* K naplánování trasy pomocí veřejného přenosu je možné [využít rozhraní API pro přenos tras](https://aka.ms/AzureMapsMobilityTransitRoute) .
* Vyžádejte si geometrii trasy přenosu a podrobný plán pro trasu pomocí [rozhraní API pro získání přenosové](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)trasy.

## <a name="prerequisites"></a>Požadavky

1. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného. Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

V tomto kurzu se používá aplikace [po](https://www.postman.com/) aplikaci, ale můžete zvolit jiné vývojové prostředí API.

## <a name="get-a-metro-area-id"></a>Získat ID oblasti metro

Aby bylo možné požádat o podrobné informace o přenosových úřadech a podporovaných typech přenosu pro konkrétní metropolitní oblast, budete potřebovat příslušnou `metroId` oblast. [Rozhraní API oblasti získat Metro](https://aka.ms/AzureMapsMobilityMetro) umožňuje vyžádat oblasti metro, ve kterých je dostupná služba Azure Maps mobility. Odpověď obsahuje podrobnosti, jako je `metroId` , `metroName` , a reprezentace geometrie oblasti metro ve formátu geometrických JSON.

Pojďme si vytvořit žádost o získání oblasti metro pro ID oblasti Tacoma Metro v Seattlu. Chcete-li požádat o ID oblasti metro, proveďte následující kroky:

1. Otevřete aplikaci pro odesílání a pojďme vytvořit kolekci pro uložení požadavků. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **kolekce**.  Pojmenujte kolekci a vyberte tlačítko **vytvořit** .

2. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozím kroku, jako umístění, kam chcete žádost Uložit. Pak vyberte **Uložit**.
  
    ![Vytvoření žádosti v post](./media/how-to-request-transit-data/postman-new.png)

3. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL pro vytvoření žádosti o získání. Nahraďte `{subscription-key}` Azure Mapsým primárním klíčem.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Po úspěšné žádosti obdržíte tuto odpověď:

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

## <a name="request-nearby-transit-stops"></a>Žádost o ukončení přechodu na okolí

Služba Azure Maps [získat nejbližší přenosové](https://aka.ms/AzureMapsMobilityNearbyTransit) služby umožňuje vyhledávat přenosové objekty. Rozhraní API vrátí podrobnosti o objektu přenosu, jako je například zastavení veřejného přenosu a sdílená kola kolem daného umístění. V dalším kroku pošleme požadavek službě, aby se v rámci tohoto umístění vyhledalo v blízkosti poloměru 300 měřičů veřejných přenosů.

Chcete-li vytvořit požadavek na [dosažení nejbližšího přenosu](https://aka.ms/AzureMapsMobilityNearbyTransit), postupujte podle následujících kroků:

1. V příspěvku klikněte na **nový požadavek**  |  **získat žádost** a pojmenujte ho v **nejbližším zastavení**.

2. Na kartě tvůrce vyberte metodu **Get** http, zadejte následující adresu URL pro koncový bod rozhraní API a klikněte na **Odeslat**.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126&radius=300&objectType=stop
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

Pokud pozoruje strukturu odpovědi pečlivě, uvidíte, že obsahuje parametry pro každý tranzitní objekt. Každý tranzitní objekt má parametry, jako jsou `id` ,, `type` `stopName` , `mainTransitType` , `mainAgencyName` a pozici, v souřadnicích objektu.

Pro účely učení použijeme `id` jako zdroj jako počátek pro naši trasu v další části službu zastavení.  

## <a name="request-a-transit-route"></a>Požadavek na přenosový postup

[Rozhraní API pro přenos tras](https://aka.ms/AzureMapsMobilityTransitRoute) v Azure Maps umožňuje plánování cest. Vrátí nejlepší možné možnosti směrování od počátku do cíle. Služba poskytuje různé druhy cest, včetně procházení, cyklistice a veřejného průjezdu. V dalším kroku provedeme trasu z nejbližšího zastavení sběrnice na místo, kde se nachází věž v Seattlu.

### <a name="get-location-coordinates-for-destination"></a>Získat souřadnice umístění pro cíl

K získání souřadnic umístění věže pro práci v prostoru použijeme službu Azure Maps pro [hledání přibližných výsledků](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Chcete-li vytvořit požadavek na službu pro vyhledávání přibližných shod, postupujte podle následujících kroků:

1. V příspěvku klikněte na **Nový žádost o**  |  **získání žádosti** a pojmenujte ji **získat souřadnice umístění**.

2. Na kartě tvůrce vyberte metodu **Get** http, zadejte následující adresu URL požadavku a klikněte na **Odeslat**.

    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```

3. Pokud se podíváte na odpověď pečlivě, obsahuje několik míst ve výsledcích hledání ručičky pro místo. Každý výsledek obsahuje souřadnice umístění v rámci **pozice**. Zkopírujte `lat` a `lon` pod **pozici** prvního výsledku.

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

1. V příspěvku klikněte na **nový požadavek**  |  **získat žádost** a pojmenujte ho **získat informace o trasách**.

2. Na kartě tvůrce vyberte metodu **Get** http, zadejte následující adresu URL pro koncový bod rozhraní API a klikněte na **Odeslat**.

    Zadáním parametrů a vyžádáme veřejné trasy pro přenos pro sběrnici `modeType` `transitType` . Adresa URL požadavku obsahuje umístění získaná v předchozích částech. Pro `originType` teď máme **stopid**. A pro je `destionationType` to **pozice**.

    Podívejte se na [seznam parametrů identifikátoru URI](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) , které můžete ve své žádosti použít pro [rozhraní API získat přenos tras](https://aka.ms/AzureMapsMobilityTransitRoute).
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
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
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
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
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            }
        ]
    }
    ```

4. Pokud pečlivě prodržíte pozor, v odpovědi se nachází více tras **Sběrnice** . Každá trasa má jedinečné **ID itineráře**, souhrn, který popisuje každou nožku trasy, a `itineraryFare` poskytuje jak vydanou, tak i celkovou cenu za lístky Bus. Nožka trasy je částí trasy mezi dvěma stopami Waypoints. Dále si vyžádáme podrobnosti o nejrychlejší trase s využitím `itineraryId` v odpovědi.

## <a name="request-fastest-route-itinerary"></a>Požadavek na nejrychlejší trasu trasy

Služba Azure Maps [získat přenosovou trasu](https://aka.ms/AzureMapsMobilityTransitItinerary) umožňuje vyžádat si data pro konkrétní trasu pomocí **ID itinerář** trasy, které vrací služba [API pro získání přenosných tras](https://aka.ms/AzureMapsMobilityTransitRoute) . Pokud chcete vytvořit žádost, proveďte následující kroky:

1. V příspěvku klikněte na **nový požadavek**  |  **získat žádost** a pojmenujte ho **získat informace o přenosu**.

2. Na kartě tvůrce vyberte metodu **Get** http. Pro koncový bod rozhraní API zadejte následující adresu URL požadavku a klikněte na **Odeslat**.

    Nastavíme `detailType` parametr na **geometrii** , aby odpověď obsahovala informace o stopu pro veřejný průjezd a přepnula navigaci pro procházení a nohy kol v trase.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```

3. Po úspěšné žádosti by struktura odpovědi měla vypadat jako na následujícím obrázku.  Pokud obdržíte odpověď ve formátu JSON, všimnete si, že každá nožka sběrnice obsahuje `legfare` prvek. `legfare`Element obsahuje náklady v centech každé trasy sběrnice, která je zakoupena samostatně. Na konci odpovědi uvidíte `itineraryFare` prvek, který obsahuje náklady, v centech celé trasy. V tomto příkladu jsou k dispozici čtyři trasy sběrnice `$2.75` . Pokud ale koupíte jeden lístek pro celou trasu, náklady jsou `$5.50` .

    ```JSON
   {
        "departureTime": "2020-07-22T19:54:47Z",
        "arrivalTime": "2020-07-22T21:12:21Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T19:54:47Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
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
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    },
                    {
                        "stopId": "522---2062263",
                        "stopKey": "85630",
                        "stopName": "Bellevue Tc",
                        "stopCode": "85630",
                        "position": {
                            "latitude": 47.615591,
                            "longitude": -122.196491
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T20:15:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                       ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:20:00Z",
                "legEndTime": "2020-07-22T20:28:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666071",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "241",
                    "caption1": "Eastgate P&R - Bellevue Transit Center",
                    "caption2": "241 Eastgate P&R - Bellevue Transit Center",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756619",
                    "lineGroupId": "522---666071",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "241",
                    "lineDestination": "Eastgate P&R Factoria"
                },
                "stops": [
                ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                   ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:31:00Z",
                "legEndTime": "2020-07-22T20:54:13Z",
                "lineGroup": {
                    "lineGroupId": "522---312636",
                    "agencyId": "522---854535",
                    "agencyName": "Sound Transit",
                    "lineNumber": "550",
                    "caption1": "Bellevue - Seattle",
                    "caption2": "550 Bellevue - Seattle",
                    "color": "00008B",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---962201",
                    "lineGroupId": "522---312636",
                    "direction": "backward",
                    "agencyId": "522---854535",
                    "lineNumber": "550",
                    "lineDestination": "Seattle"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                 ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:57:00Z",
                "legEndTime": "2020-07-22T21:06:00Z",
                "lineGroup": {
                    "lineGroupId": "522---480518",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "13",
                    "caption1": "Seattle Pacific - Downtown Seattle",
                    "caption2": "13 Seattle Pacific - Downtown Seattle",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---1744932",
                    "lineGroupId": "522---480518",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "13",
                    "lineDestination": "Seattle Pacific University Seattle Center W"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                      ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
        ],
        "itineraryFare": {
            "price": {
                "amount": 550,
                "currencyCode": "USD"
            },
            "tickets": [
                {
                    "amount": 275,
                    "currencyCode": "USD"
                },
                {
                    "amount": 275,
                    "currencyCode": "USD"
                }
            ]
        }
    }

    ```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak vyžádat data v reálném čase pomocí služby mobility:

> [!div class="nextstepaction"]
> [Jak vyžádat data v reálném čase](how-to-request-real-time-data.md)

Prozkoumejte dokumentaci k rozhraní API služby Azure Maps mobility

> [!div class="nextstepaction"]
> [Dokumentace ke službě mobility](https://aka.ms/AzureMapsMobilityService)
