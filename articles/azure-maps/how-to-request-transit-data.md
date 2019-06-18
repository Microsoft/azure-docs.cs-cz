---
title: Jak si vyžádat přenosu dat ve službě Azure Maps | Dokumentace Microsoftu
description: Požadavek veřejné přenosu dat pomocí služby Azure Maps Mobility.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e8250763153f7c5b71f3906a560365dadfd55694
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735568"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Žádost o veřejný přenosu dat pomocí služby Mobility Azure Maps 

V tomto článku se dozvíte, jak používat Azure Maps [služby Mobility](https://aka.ms/AzureMapsMobilityService) požádat o veřejné přenosu dat, včetně zastaví, směrovat informace a projít odhady čas.

V tomto článku se dozvíte jak:

* Získat pomocí ID metro oblasti [získat Metro oblasti API](https://aka.ms/AzureMapsMobilityMetro)
* Požádat o blízké přenosu přestane používat [získat blízké přenosu](https://aka.ms/AzureMapsMobilityNearbyTransit) služby.
* Dotaz [získat API směrování přenosu](https://aka.ms/AzureMapsMobilityTransitRoute) plánovat trasy s použitím veřejné přenosu.
* Požádat o geometrii směrování přenosu a podrobný plán pro trasu pomocí [získat trasu API přenosu](https://aka.ms/ https://azure.microsoft.com/services/azure-maps/).


## <a name="prerequisites"></a>Požadavky

Aby všechna volání do veřejné přenosu Azure Maps rozhraní API, budete potřebovat účet Maps a klíč. Informace o vytváření účtu a klíč načítání, najdete v části [jak spravovat účet Azure Maps a klíče](how-to-manage-account-keys.md).

Tento článek používá [aplikaci Postman](https://www.getpostman.com/apps) vytvářet volání REST. Můžete použít všechny vývojové prostředí rozhraní API, které dáváte přednost.


## <a name="get-a-metro-area-id"></a>Získejte ID metro oblasti

Aby bylo možné žádost o informace o přenosu pro konkrétní metropolitní oblasti, budete potřebovat `metroId` chcete požádat o přenosu dat pro oblasti. [Rozhraní API oblasti Metro](https://aka.ms/AzureMapsMobilityMetro) umožňuje požádat o metro oblasti, ve kterých je dostupná služba Mobility Azure Maps. Odpověď obsahují podrobnosti, například `metroId`, `metroName` a reprezentace geometrie metro oblasti ve formátu GeoJSON.

Pojďme vytvořit žádost o získání oblasti Metro identifikátor Seattle Tacoma metro oblasti. Pro ID žádosti pro metro oblast, proveďte následující kroky:

1. Vytvořte kolekci, ve kterých se mají ukládat požadavky. V aplikaci Postman vyberte **nový**. V **vytvořit nový** okně **kolekce**. Název kolekce a vyberte **vytvořit** tlačítko.

2. Pokud chcete vytvořit žádost, vyberte **nový** znovu. V **vytvořit nový** okně **požádat o**. Zadejte **název žádosti** žádosti, vyberte kolekci, do které jste vytvořili v předchozím kroku jako umístění, ve kterém uložte požadavek a pak vyberte **Uložit**.
    
    ![Vytvořit žádost o v nástroji Postman](./media/how-to-request-transit-data/postman-new.png)

3. Vyberte metodu GET HTTP na kartě tvůrce a zadejte následující adresu URL k vytvoření žádosti GET.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Po úspěšném požadavku zobrazí se následující odpověď:

    ```JSON
    {
        "results": [
            {
                "metroId": "522",
                "metroName": "Seattle–Tacoma–Bellevue, WA",
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
                            [
                                -121.95725,
                                47.18314
                            ],
                            ...,
                            ...,
                            ...,
                            ...,
                            [
                                -122.18711,
                                47.15571
                            ],
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
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                }
            }
        ]
    }
    ```

5. Kopírovat `metroId`, pro pozdější použití.

## <a name="request-nearby-transit-stops"></a>Požádat o okolních průchod zarážky

Azure Maps [získat blízké přenosu](https://aka.ms/AzureMapsMobilityNearbyTransit) služby umožňuje vyhledávat objekty přenosu, například veřejné přenosu se zastaví a sdílet kol kolem na dané místo vrácení tranzitních Podrobnosti objektu. Dále budeme žádost do služby vyhledávání blízkých veřejné přenosu zastaví okruhu 300 měřiče po daném umístění. V požadavku, musíme zahrnout `metroId` získali v dříve.

Chcete-li vytvořit žádost o [získat blízké přenosu](https://aka.ms/AzureMapsMobilityNearbyTransit), postupujte podle následujících kroků:

1. V nástroji Postman, klikněte na tlačítko **novou žádost o** | **požadavek GET** a pojmenujte ho **získat blízké zastaví**.

2. Na kartě tvůrce, vyberte **získat** metodu HTTP, zadejte následující adresu URL požadavku pro koncový bod rozhraní API a klikněte na tlačítko **odeslat**.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Po úspěšné žádosti by měl vypadat struktura odpověď podobná té následující:

    ```JSON
    {
        "results": [
            {
                "id": "2060603",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "Ne 24th St & 162nd Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.63241381296315,
                        "longitude": -122.12659096560266
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594172088166,
                        "longitude": -122.12395908007201
                    }
                }
            },
            {
                "id": "2061020",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "Ne 24th St & 160th Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318791818726,
                        "longitude": -122.12845199584025
                    },
                    "btmRightPoint": {
                        "latitude": 47.63049919323126,
                        "longitude": -122.12582004983427
                    }
                }
            },
            {
                "id": "2060604",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "Ne 24th St & 160th Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474784183636,
                        "longitude": -122.12912401149087
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655200865796,
                        "longitude": -122.12649203418405
                    }
                }
            }
        ]
    }    
    ```

Pokud zjistíte struktura odpovědi pečlivě, uvidíte, že obsahuje parametry každého objektu, přenosu, například `id`, `type`, `stopName`, `mainTransitType`, `mainAgencyName` a pozice (souřadnice) objektu.

Za účelem porozumění, budeme používat `id` jednoho zastaví Service bus jako zdroj pro naše trasy v další části.  


## <a name="request-a-transit-route"></a>Žádost o směrování přenosu

Azure Maps [získat API směrování přenosu](https://aka.ms/AzureMapsMobilityTransitRoute) umožňuje vrácení možnosti nejlepší možné trasy mezi původu a cíle plánování cest. Služba poskytuje širokou škálu režimech, včetně walking kole a veřejné přenosu. Dále jsme bude hledat trasu z nejbližší stop Service bus k ručička místa v Seattlu.

### <a name="get-location-coordinates-for-destination"></a>Získat souřadnice polohy pro cíl

Za účelem získání umístění souřadnice ručička místa, umožňuje Azure Maps použít [přibližné vyhledávání služby](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Chcete-li žádost do služby vyhledávání přibližných shod, použijte následující postup:

1. V nástroji Postman, klikněte na tlačítko **novou žádost o** | **požadavek GET** a pojmenujte ho **získat souřadnice polohy**.

2.  Na kartě tvůrce, vyberte **získat** metodu HTTP, zadejte následující adresu URL požadavku a klikněte na tlačítko **odeslat**.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Když se podíváte na odpovědi pečlivě, obsahuje více umístění ve výsledcích pro prostor ručička a také obsahuje informace o poloze souřadnice pro každý z nich v části **pozice**. Kopírovat `lat` a `lon` od pozice pro první výsledek.
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 61,
            "numResults": 8,
            "offset": 0,
            "totalResults": 24,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/8309323",
                "score": 4.674,
                "info": "search:ta:840539000511573-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "monument"
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
                                    "name": "monument"
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
                    "country": "United States Of America",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
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
    

### <a name="request-route"></a>Směrování žádosti

Vytvořte žádost na směrování, proveďte následující postup:

1. V nástroji Postman, klikněte na tlačítko **novou žádost o** | **požadavek GET** a pojmenujte ho **informace získat trasu**.

2. Na kartě tvůrce, vyberte **získat** metodu HTTP, zadejte následující adresu URL požadavku pro koncový bod rozhraní API a klikněte na tlačítko **odeslat**.

    Jsme požádá o veřejné přenosu trasy pro Service bus tak, že zadáte `modeType` a `transitType` parametry. Požadavek na adresu URL obsahuje umístění získali v předchozí části. Jako `originType` teď máme **stopId** a jako `destionationType` máme **pozice**.

    Najdete v článku [seznam parametry identifikátoru URI](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) na vaši žádost o slouží [získat API směrování přenosu](https://aka.ms/AzureMapsMobilityTransitRoute). 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Po úspěšné žádosti by měl vypadat struktura odpověď podobná té následující:

    ```JSON
    {
        "results": [
            {
                "itineraryId": "302c38dd-6585-4fa1-bf78-44ebbc183e0c---2019040384C30774B4B94F178E7748644A476596:0---522",
                "departureTime": "2019-04-03T14:21:34-07:00",
                "arrivalTime": "2019-04-03T15:15:53-07:00",
                "travelTimeInSeconds": 3259,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:21:34-07:00",
                        "legEndTime": "2019-04-03T14:28:19-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 497
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:28:19-07:00",
                        "legEndTime": "2019-04-03T14:29:20-07:00",
                        "caption": "245"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:29:20-07:00",
                        "legEndTime": "2019-04-03T14:32:00-07:00",
                        "caption": "245",
                        "lengthInMeters": 1350
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:32:01-07:00",
                        "legEndTime": "2019-04-03T14:33:07-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 63
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:33:07-07:00",
                        "legEndTime": "2019-04-03T14:38:00-07:00",
                        "caption": "545"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:38:00-07:00",
                        "legEndTime": "2019-04-03T14:59:47-07:00",
                        "caption": "545",
                        "lengthInMeters": 16441
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:59:48-07:00",
                        "legEndTime": "2019-04-03T15:03:53-07:00",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T15:03:53-07:00",
                        "legEndTime": "2019-04-03T15:07:26-07:00",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T15:07:26-07:00",
                        "legEndTime": "2019-04-03T15:12:12-07:00",
                        "caption": "8",
                        "lengthInMeters": 1057
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:12:13-07:00",
                        "legEndTime": "2019-04-03T15:15:53-07:00",
                        "caption": "47.6205,-122.3493",
                        "lengthInMeters": 268
                    }
                ]
            },
            ...,
            {
                "itineraryId": "302c38dd-6585-4fa1-bf78-44ebbc183e0c---2019040384C30774B4B94F178E7748644A476596:2---522",
                "departureTime": "2019-04-03T14:21:34-07:00",
                "arrivalTime": "2019-04-03T15:19:18-07:00",
                "travelTimeInSeconds": 3464,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:21:34-07:00",
                        "legEndTime": "2019-04-03T14:28:19-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 497
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:28:19-07:00",
                        "legEndTime": "2019-04-03T14:29:20-07:00",
                        "caption": "245"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:29:20-07:00",
                        "legEndTime": "2019-04-03T14:32:00-07:00",
                        "caption": "245",
                        "lengthInMeters": 1350
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:32:01-07:00",
                        "legEndTime": "2019-04-03T14:33:07-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 63
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:33:07-07:00",
                        "legEndTime": "2019-04-03T14:38:00-07:00",
                        "caption": "545"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:38:00-07:00",
                        "legEndTime": "2019-04-03T15:01:00-07:00",
                        "caption": "545",
                        "lengthInMeters": 17400
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:01:01-07:00",
                        "legEndTime": "2019-04-03T15:04:59-07:00",
                        "caption": "3rd Avenue",
                        "lengthInMeters": 269
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T15:04:59-07:00",
                        "legEndTime": "2019-04-03T15:09:14-07:00",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T15:09:14-07:00",
                        "legEndTime": "2019-04-03T15:12:52-07:00",
                        "caption": "33,24",
                        "lengthInMeters": 947
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:12:53-07:00",
                        "legEndTime": "2019-04-03T15:19:18-07:00",
                        "caption": "47.6205,-122.3493",
                        "lengthInMeters": 474
                    }
                ]
            }
        ]
    }
    ```

4. Pokud zjistíte pečlivě, jsou více **Service bus** trasy v odpovědi. Každá trasa obsahuje jedinečné **trasy ID** a shrnutí, který popisuje každou větev trasy. Dále jsme se podrobnosti o nejrychlejší trasy s využitím požadavku `itineraryId` v odpovědi.

## <a name="request-fastest-route-itinerary"></a>Požádat o nejrychlejší trasu směrování

Azure Maps [získat trasu přenosu](https://aka.ms/AzureMapsMobilityTransitItinerary) service umožňuje požádat o data pro konkrétní trasu pomocí postupu **trasy ID** vrácených [získat API směrování přenosu](https://aka.ms/AzureMapsMobilityTransitRoute) Služba. Pokud chcete vytvořit žádost, proveďte následující postup:

1. V nástroji Postman, klikněte na tlačítko **novou žádost o** | **požadavek GET** a pojmenujte ho **informace o získání přenosu**.

2. Na kartě tvůrce, vyberte **získat** metodu HTTP, zadejte následující adresu URL požadavku pro koncový bod rozhraní API a klikněte na tlačítko **odeslat**.

    Nastavíme `detailType` parametr **geometrie** tak, že odpověď obsahuje informace o zastavení pro veřejné přenosu a navigace na řadě vy zapnout pro vás a kola ramena trasy.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. Po úspěšné žádosti by měl vypadat struktura odpověď podobná té následující:

    ```JSON
    {
    "departureTime": "2019-05-01T11:16:56-07:00",
    "arrivalTime": "2019-05-01T12:23:45-07:00",
    "legs": [
                {
                    "legType": "Walk",
                    "legStartTime": "2019-05-01T11:16:56-07:00",
                    "legEndTime": "2019-05-01T11:24:06-07:00",
                    "walkingSteps": [
                        {
                            "direction": {
                                "relativeDirection": "left"
                            },
                            "streetName": "Northeast 24th Street"
                        },
                        {
                            "direction": {
                                "relativeDirection": "right"
                            },
                            "streetName": "156th Avenue Northeast"
                        }
                    ],
                    "walkingOrigin": {
                        "latitude": 47.63096,
                        "longitude": -122.126
                    },
                    "walkingDestination": {
                        "latitude": 47.631843,
                        "longitude": -122.132294
                    },
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.126,
                                47.63096
                            ],
                            [
                                -122.12645,
                                47.63099
                            ],
                            ...,
                            ...,
                            [
                                -122.1323,
                                47.63184
                            ]
                        ]
                    }
                },
                {
                    "legType": "Wait",
                    "legStartTime": "2019-05-01T11:24:06-07:00",
                    "legEndTime": "2019-05-01T11:25:07-07:00",
                    "lineGroup": {
                        "lineGroupId": 666074,
                        "agencyId": 5872,
                        "agencyName": "Metro Transit",
                        "lineNumber": "245",
                        "caption1": "Kirkland Transit Center - Crossroads - Factoria",
                        "caption2": "245 Kirkland Transit Center - Crossroads - Factoria",
                        "color": "347E5D",
                        "transitType": "Bus"
                    },
                    "line": {
                        "lineId": 2756624,
                        "lineGroupId": 666074,
                        "direction": "forward",
                        "agencyId": 5872,
                        "lineNumber": "245",
                        "destination": "Kirkland Crossroads"
                    },
                    "stops": [
                        {
                            "stopId": 2061109,
                            "stopKey": "68788",
                            "stopName": "156th Ave NE & NE 24th St",
                            "position": {
                                "latitude": 47.631844,
                                "longitude": -122.132248
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        },
                        {
                            "stopId": 2061059,
                            "stopKey": "68498",
                            "stopName": "156th Ave NE & Overlake Transit Center - Bay 8",
                            "position": {
                                "latitude": 47.643986,
                                "longitude": -122.132187
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        }
                    ],
                    "waitOnVehicle": "false"
                },
                {
                    "legType": "Bus",
                    "legStartTime": "2019-05-01T11:25:07-07:00",
                    "legEndTime": "2019-05-01T11:30:00-07:00",
                    "lineGroup": {
                        "lineGroupId": 666074,
                        "agencyId": 5872,
                        "agencyName": "Metro Transit",
                        "lineNumber": "245",
                        "caption1": "Kirkland Transit Center - Crossroads - Factoria",
                        "caption2": "245 Kirkland Transit Center - Crossroads - Factoria",
                        "color": "347E5D",
                        "transitType": "Bus"
                    },
                    "line": {
                        "lineId": 2756624,
                        "lineGroupId": 666074,
                        "direction": "forward",
                        "agencyId": 5872,
                        "lineNumber": "245",
                        "destination": "Kirkland Crossroads"
                    },
                    "stops": [
                        {
                            "stopId": 2061109,
                            "stopKey": "68788",
                            "stopName": "156th Ave NE & NE 24th St",
                            "position": {
                                "latitude": 47.631844,
                                "longitude": -122.132248
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        },
                        ...,
                        ...,
                        {
                            "stopId": 2061059,
                            "stopKey": "68498",
                            "stopName": "156th Ave NE & Overlake Transit Center - Bay 8",
                            "position": {
                                "latitude": 47.643986,
                                "longitude": -122.132187
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        }
                    ],
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.13235,
                                47.63184
                            ],
                            ...,
                            ...,
                            [
                                -122.1323,
                                47.64398
                            ]
                        ]
                    }
                },
                ...,
                ...,
                ...,
                {
                    "legType": "Tram",
                    "legStartTime": "2019-05-01T12:20:00-07:00",
                    "legEndTime": "2019-05-01T12:22:00-07:00",
                    "lineGroup": {
                        "lineGroupId": 4083239,
                        "agencyId": 1360766,
                        "agencyName": "Seattle Monorail",
                        "lineNumber": "Monorail",
                        "caption1": "Seattle Center - Westlake Center",
                        "caption2": "MONORAIL Seattle Center - Westlake Center",
                        "color": "00AEEF",
                        "transitType": "Tram"
                    },
                    "line": {
                        "lineId": 3769726,
                        "lineGroupId": 4083239,
                        "direction": "backward",
                        "agencyId": 1360766,
                        "lineNumber": "Monorail",
                        "destination": "Seattle Center"
                    },
                    "stops": [
                        {
                            "stopId": 32962125,
                            "stopName": "Westlake Station",
                            "position": {
                                "latitude": 47.611417,
                                "longitude": -122.337089
                            },
                            "mainTransitType": "Tram",
                            "mainAgencyId": 1360766
                        },
                        {
                            "stopId": 32962134,
                            "stopName": "Seattle Center",
                            "position": {
                                "latitude": 47.62123,
                                "longitude": -122.349746
                            },
                            "mainTransitType": "Tram",
                            "mainAgencyId": 1360766
                        }
                    ],
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.3369,
                                47.61201
                            ],
                            ...,
                            ...,
                            [
                                -122.34973,
                                47.6212
                            ]
                        ]
                    }
                },
                {
                    "legType": "PathWayWalk",
                    "legStartTime": "2019-05-01T12:22:00-07:00",
                    "legEndTime": "2019-05-01T12:23:45-07:00"
                }
          ]
    }
    ```

## <a name="next-steps"></a>Další postup

Zjistěte, jak požádat o data v reálném čase pomocí služby Mobility:

> [!div class="nextstepaction"]
> [Jak si vyžádat data v reálném čase](how-to-request-real-time-data.md)

Projděte si dokumentaci rozhraní API služby Mobility Azure Maps

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní API služby mobility](https://aka.ms/AzureMapsMobilityService)

