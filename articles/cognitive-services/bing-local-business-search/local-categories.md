---
title: Hledat kategorie místní firmy API pro vyhledávání Bingu | Dokumentace Microsoftu
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto článku se naučíte, a zadejte kategorie vyhledávání pro koncový bod rozhraní API vyhledávání Bingu místní firmy.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9ee6d0f8b609b87fe7ed4083bc5886ceaf158cb2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188707"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Vyhledat v kategoriích pro místní obchodní hledání rozhraní API Bingu

Místní firmy API pro vyhledávání Bingu umožňuje vyhledat místní obchodní entity v různých kategoriích, přednostně zavřít výsledky umístění uživatele. Tato hledání můžete zahrnout při hledání spolu s `localCircularView` a `localMapView` [parametry](specify-geographic-search.md).


## <a name="toplevel-categories"></a>TopLevel kategorie 

Následující typy definovat hlavní kategorie služby search.  Více než jednu kategorii lze zadat pomocí seznamu odděleného čárkami se přiřazená `localCategories` parametru.  
- EatDrink 
- SeeDo 
- Obchod 
- HotelsAndMotels 
- BanksAndCreditUnions 
- Parkovací 
- Nemocnice 

## <a name="sub-categories"></a>Podkategorie
Podkategorie jsou předány stejným způsobem jako `localCategories`. Podkategorie jsou konkrétnějších kategoriích. Jsou to podřízenou položku v tom smyslu, že pokud zadáte kategorie C a jeden z jeho podkategorie S ve stejném seznamu odděleného čárkami, zobrazí se stejné výsledky jako kdyby jste zadali samostatném jazyce C.

### <a name="eat-drink"></a>Jíst nápoje 
|  |  |  |  |
| - | - | - | - |
| BreweriesAndBrewPubs | CocktailLounges | AfricanRestaurants |
| AmericanRestaurants | Bagels | BarbecueRestaurants |
| Října | SportsBars | Pruhy |
| BarsGrillsAndPubs | BuffetRestaurants| BelgianRestaurants | 
| BritishRestaurants | CafeRestaurants | CaribbeanRestaurants |
| ChineseRestaurants | CoffeeAndTea | Delicatessens | 
| DeliveryService | Diners | DiscountStores | 
| Donuts | FastFood | FrenchRestaurants | 
| FrozenYogurt | GermanRestaurants | Supermarkety | 
| GreekRestaurants | Grocers | HawaiianRestaurants | 
| HungarianRestaurants | IceCreamAndFrozenDesserts | IndianRestaurants | 
| ItalianRestaurants | JapaneseRestaurants | Šťáv | 
| KoreanRestaurants | LiquorStores | MexicanRestaurants |
| MiddleEasternRestaurants | Pizza | PolishRestaurants | 
| PortugueseRestaurants | Pretzels | Restaurace | 
| RussianAndUkrainianRestaurants | Sendviče | SeafoodRestaurants | 
| SpanishRestaurants | SteakHouseRestaurants | SushiRestaurants | 
| Nutnost tvořit | ThaiRestaurants | TurkishRestaurants | 
| VegetarianAndVeganRestaurants | VietnameseRestaurants|  |
 
### <a name="see-do"></a>Zobrazit provést 
|  |  |  |
| -- | -- | -- |
| AmusementParks | Atrakce | Carnivals |
| Kasin | LandmarksAndHistoricalSites | MiniatureGolfCourses |
| MovieTheaters | Muzeí | Parků |
| SightseeingTours | TouristInformation | Zoos |
 
### <a name="shop"></a>Obchod 
|  |  |  |
| -- | -- | -- |
| AntiqueStores | Obchodech | CDAndRecordStores |
| ChildrensClothingStores | CigarAndTobaccoShops | ComicBookStores |
| DepartmentStores | DiscountStores | FleaMarketsAndBazaars |
| FurnitureStores | HomeImprovementStores | JewelryAndWatchesStores |
| KitchenwareStores | LiquorStores | MallsAndShoppingCenters |
| MensClothingStores | MusicStores | OutletStores |
| PetShops | PetSupplyStores | SchoolAndOfficeSupplyStores |
| ShoeStores | SportingGoodsStores | ToyAndGameStores |
| VitaminAndSupplementStores | WomensClothingStores |  |


## <a name="examples-of-local-categories-search"></a>Příklady kategorií místní hledání

Následující příklady GET výsledky podle `localCategories` parametr:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

Následující dotaz omezí počet výsledků 'nemocnici' první tři vrácená z místní firmy hledání rozhraní API Bingu:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

Následující příklad odpověď JSON obsahuje tři nemocnice větší Seattlu:

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>Další postup
- [Hranice geografické vyhledávání](specify-geographic-search.md)
- [Dotazy a odpovědi](local-search-query-response.md)
- [Rychlý start vC#](quickstarts/local-quickstart.md)
