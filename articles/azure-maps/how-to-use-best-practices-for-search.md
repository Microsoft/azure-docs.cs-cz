---
title: Vyhledávejte efektivně pomocí Azure Maps Search Service | Mapy Microsoft Azure
description: Naučte se používat osvědčené postupy pro službu vyhledávání pomocí Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 68c7408f13027ded7beaabf46fb663217a90c52b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845753"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Osvědčené postupy pro použití Azure Maps Search Service

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) obsahuje rozhraní API s různými možnostmi. Například rozhraní API pro vyhledávání adres slouží k hledání bodu zájmu (POI) nebo dat v okolí konkrétního umístění. Tento článek popisuje zvukové postupy, které je potřeba použít při volání dat ze služby Azure Maps Search Services. V tomto kurzu se naučíte:

* Sestavení dotazů pro vrácení odpovídajících shod
* Omezení výsledků hledání
* Přečtěte si rozdíl mezi různými typy výsledků
* Přečíst strukturu odpovědi na hledání adres


## <a name="prerequisites"></a>Požadavky

Pokud chcete volat rozhraní API služby Maps, potřebujete účet Azure Maps a klíč. Pokud potřebujete, postupujte podle pokynů v tématu [Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) a [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account). Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

> [!Tip]
> Pokud chcete zadat dotaz na vyhledávací službu, můžete k sestavení volání REST použít [aplikaci pro odesílání](https://www.getpostman.com/apps) nebo můžete použít jakékoli vývojové prostředí API, které dáváte přednost.


## <a name="best-practices-for-geocoding-address-search"></a>Osvědčené postupy pro geografické kódování (vyhledávání adres)

Když vyhledáte úplnou nebo částečnou adresu pomocí Azure Maps Search Service, rozhraní API přečte klíčová slova z vyhledávacího dotazu a vrátí zeměpisnou délku a zeměpisnou šířku adresy. Tento proces se nazývá geografické kódování. Schopnost geografického kódu v zemi závisí na pokrytí dat provozu a přesnosti geografického kódování služby geografického kódování.

Další informace o Azure Maps možností geografického kódování podle země nebo oblasti najdete v části [pokrytí geografického kódování](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) .

### <a name="limit-search-results"></a>Omezení výsledků hledání

 Rozhraní API pro Azure Maps Search vám může pomáhat omezit výsledky hledání, abyste mohli zobrazit relevantní data pro uživatele.

   > [!Note]
   > Ne všechny podporované parametry rozhraní API pro vyhledávání jsou uvedené níže.

   **Výsledky hledání geografického bias**

   Aby bylo možné své výsledky geograficky vysměrovat do příslušné oblasti pro uživatele, měli byste vždy přidat maximální možné zadání podrobného umístění. Chcete-li omezit výsledky hledání, zvažte přidání následujících typů vstupu:

   1. Nastavte parametr `countrySet`, například US, FR. Výchozím chováním vyhledávání je hledání celého světa, což může vracet zbytečné výsledky. Pokud má váš dotaz parametr `countrySet`, hledání může vracet nepřesné výsledky. Například hledání města s názvem **Bellevue** vrátí výsledky z USA a Francie, protože v vývaru Francie a USA jsou města s názvem **Bellevue** .

   2. Pomocí parametrů `btmRight` a `topleft` můžete nastavit ohraničovací rámeček a omezit tak hledání na určitou oblast na mapě.

   3. Chcete-li ovlivnit oblast důležitosti pro výsledky, můžete definovat `lat`a `lon` parametry souřadnic a nastavit poloměr oblasti hledání pomocí parametru `radius`.


   **Parametry hledání přibližné vyhledávání**
   
  Azure Maps [rozhraní API pro vyhledávání s fuzzy logikou](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) je doporučená služba pro použití v případě, že nevíte, jaké uživatelské vstupy jsou pro vyhledávací dotaz. Rozhraní API kombinuje POI (Point of Interest) a geografické kódování do kanonického *víceřádkového vyhledávání*.

   1. `minFuzzyLevel` a `maxFuzzyLevel` vrátí relevantní shody i v případě, že parametry dotazu přesně neodpovídají požadovaným informacím. K získání výkonu a omezení neobvyklých výsledků se výchozí vyhledávací dotazy `minFuzzyLevel=1` a `maxFuzzyLevel=2`. Příkladem hledaného termínu "restrant" se shoduje s názvem "restaurace", pokud je `maxFuzzyLevel` nastaveno na 2. Výchozí přibližné úrovně lze podle potřeby přepsat.  

   2. Můžete také určit prioritu přesné sady typů výsledků, které mají být vráceny pomocí parametru `idxSet`. Pro tento účel můžete odeslat seznam indexů oddělených čárkami; pořadí položek nezáleží na tom. Podporovány jsou následující indexy:

       * `Addr`**rozsahy adres** - : u některých ulic jsou k dispozici adresní body, které se interpolují od začátku do konce ulice. Tyto body jsou reprezentovány jako rozsahy adres.
       * `Geo` - **geografické**oblasti: oblasti na mapě, které reprezentují správní rozdělení půdy, tj. země, stát, město.
       * Adresa  - **bodu**`PAD`: body na mapě, kde se v indexu nachází konkrétní adresa s názvem a číslem ulice, například Soquel Dr 2501. Tato hodnota idxSet je nejvyšší úroveň přesnosti, která je k dispozici pro adresy.  
       * `POI` - **bodů zájmu**: body na mapě, které stojí za pozornost a můžou být zajímavé.  [Získat adresu hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) vrátí zájmu.  
       * `Str` - **ulic**: reprezentace ulic na mapě.
       * `XStr` - **více ulic/průniky**: reprezentace spojení; místa, kde se protínají dvě ulice.


       **Příklady použití**:

       * idxSet = POI (jenom hledání bodů zájmu) 

       * idxSet = PAD; addr (Prohledat pouze adresy, PAD = adresa bodu, addr = rozsah adres)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Převrátit filtr typu geografického kódu a geografické entity

Při zpětném vyhledávání v rámci vyhledávání pomocí [adresy Search reverzního rozhraní API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)má služba schopnost vracet mnohoúhelníky pro oblasti správy. Zadáním parametru `entityType` v žádosti můžete zúžit hledání zadaných typů geografických entit. Výsledná odpověď bude obsahovat geografické ID a odpovídající typ entity. Pokud zadáte více než jednu entitu, koncový bod vrátí **nejmenší dostupnou entitu**. Vrácené ID geometrie lze použít k získání geometrie této geografické [služby prostřednictvím Get mnohoúhelníku](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Vzorový požadavek:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Základě**

```JSON
{
    "summary": {
        "queryTime": 14,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>Jazyk výsledků hledání

Parametr `language` umožňuje vybrat jazyk výsledků vrácený rozhraním API. Pokud jazyk není v požadavku nastavený, vyhledávací služba se automaticky nastaví na nejběžnější jazyk v zemi nebo oblasti. I když nejsou k dispozici data v zadaném jazyce, použije se výchozí jazyk. Seznam podporovaných jazyků od Azure Maps Services podle země nebo oblasti najdete v tématu [podporované jazyky](https://docs.microsoft.com/azure/azure-maps/supported-languages) .


### <a name="predictive-mode-autosuggest"></a>Prediktivní režim (Automatický návrh)

Chcete-li najít další shody pro částečné dotazy, `typeahead` parametr by měl být nastaven na hodnotu "true". Dotaz bude interpretován jako částečný vstup a hledání bude obsahovat prediktivní režim. V opačném případě převezme služba všechny příslušné informace.

V následujícím ukázkovém dotazu vidíte, že se pro "Microso" používá dotaz na vyhledávací službu s parametrem `typeahead` nastaveným na **hodnotu true**. Pokud si vyberete odpověď, vidíte, že vyhledávací služba interpretuje dotaz jako částečný dotaz. Odpověď obsahuje výsledky pro dotaz s automatickým návrhem.

**Ukázkový dotaz:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Základě**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>Kódování identifikátoru URI pro zpracování speciálních znaků 

Chcete-li najít adresy mezi adresami, je nutné zakódovat identifikátor URI pro zpracování speciálních znaků v adrese. Vezměte v úvahu tento příklad adresy: "1 způsob & Union Street, Seattle". Před odesláním žádosti je třeba zakódovat speciální znak ' & '. Doporučujeme kódovat znaková data v identifikátoru URI, kde jsou všechny znaky kódovány pomocí znaku% a šestnáctková hodnota se dvěma znaky odpovídající jejich znaku UTF-8.

**Příklady použití**:

Získat adresu pro hledání:

```
query=1st Avenue & E 111th St, New York
```

 musí být kódovaný jako:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Existují různé metody, které je možné použít pro různé jazyky: 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

Ruby
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Jděte
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Osvědčené postupy pro hledání POI

Hledání bodů zájmu (POI) umožňuje vyžádat si výsledky POI podle názvu, třeba vyhledat firmu podle názvu. Důrazně doporučujeme, abyste pomocí parametru `countrySet` určili země, ve kterých vaše aplikace potřebuje pokrytí, protože výchozí chování bude prohledávat celý svět, potenciálně vracet nepotřebné výsledky nebo výsledky hledání trvat delší dobu.

### <a name="brand-search"></a>Hledání značky

Chcete-li zlepšit relevanci výsledků a informace v odpovědi hledání POI (Point of Interest), obsahuje informace o značce, které lze použít k analýze odpovědi.

V žádosti můžete také odeslat seznam názvů značek oddělených čárkami. Seznam můžete použít k omezení výsledků na konkrétní značky pomocí parametru `brandSet`. Pořadí položek nezáleží na tom. Pokud je k dispozici více značek, vrátí se pouze výsledky, které patří do (alespoň) jeden ze zadaných seznamů.

Pojďme vytvořit [POIou](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) žádost o vyhledávání u plynárenských stanic v rámci Microsoft Campus (Redmond, WA). Pokud obdržíte odpověď, uvidíte informace o značce pro každou vrácenou POI.


**Ukázkový dotaz:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Základě**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Hledání na letišti

POI Search podporuje hledání letišť pomocí oficiálních kódů letišť. Například **námořní** (Brno – Tacoma mezinárodní letiště). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Okolní hledání

Pokud chcete načíst jenom POI výsledky kolem konkrétního umístění, může být nejvhodnější [rozhraní API pro hledání v blízkosti](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) . Tento koncový bod vrátí pouze výsledky POI a nebere v parametru vyhledávacího dotazu. Chcete-li omezit výsledky, doporučuje se nastavit poloměr.

## <a name="understanding-the-responses"></a>Porozumění odpovědím

Pojďme na [službu Azure Maps Search](https://docs.microsoft.com/rest/api/maps/search) udělat požadavek na hledání adresy v Seattlu. Pokud se podíváte na adresu URL žádosti níže, nastavili jsme parametr `countrySet`, **abychom** mohli adresu v USA Americe vyhledat.

**Ukázkový dotaz:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Dále se podívejme na níže uvedenou strukturu odpovědi. Typy výsledků výsledných objektů v odpovědi se liší. Pokud pečlivě obdržíte pozor, vidíte, že máme tři různé typy objektů výsledků, které jsou "adresa Point", "ulice" a "meziulice". Všimněte si, že vyhledávání adres nevrací zájmu. Parametr `Score` pro každý objekt Response označuje relativní shodu skóre s skóre ostatních objektů ve stejné odpovědi. Další informace o parametrech objektu Response najdete v tématu [získání adresy pro hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) .

**Podporované typy výsledku:**

* **Adresa bodu:** Odkazuje na mapě s určitou adresou s názvem a číslem ulice. Nejvyšší úroveň přesnosti, která je k dispozici pro adresy. 

* **Rozsah adres:**  U některých ulic existují adresní body, které se interpolují od začátku a konce ulice. Tyto body jsou reprezentovány jako rozsahy adres. 

* **Zeměpisná oblast:** Oblasti na mapě, které reprezentují členění půdy, tj., země, stát, město. 

* **POI – (body zájmu):** Ukazuje na mapě, která stojí za pozornost a může být zajímavá.

* **Ulice:** Reprezentace ulic na mapě Adresy se přeloží na souřadnici zeměpisné šířky a délky ulice, která obsahuje adresu. Číslo domu nesmí být zpracováno. 

* **Mezi ulici:** Průsečíků. Reprezentace spojení; místa, kde se protínají dvě ulice.

**Základě**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometrie

Pokud je typ odpovědi **geometrie**, může obsahovat ID geometrie, které je vráceno v objektu **DataSources** v části geometrie a ID. Například [získání služby mnohoúhelníku](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) vám umožní požádat o data geometrie ve formátu geometrických JSON. Například osnova města nebo letiště pro sadu entit. Tato data hranice můžete použít pro [monitorování geografických zón](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) nebo [hledání zájmu uvnitř geometrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Hledání v adresách](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) nebo [hledání přibližných](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) odpovědí rozhraní API může zahrnovat **ID geometrie** , které se vrátí do objektu DataSources v části geometrie a ID.


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Další kroky

* Naučte [se vytvářet žádosti o služby Azure Maps Search](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Prozkoumejte [dokumentaci k rozhraní API služby Azure Maps Search Service](https://docs.microsoft.com/rest/api/maps/search). 
