---
title: Jak efektivně prohledávat pomocí služby Azure Maps Search Service | Microsoft Docs
description: Naučte se používat osvědčené postupy pro vyhledávání pomocí služby Azure Maps Search Service.
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: de9e484e43c87375c2fdf9b34dd2efce3bb8aa8c
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429182"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Osvědčené postupy pro použití Azure Maps Search Service

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) obsahuje rozhraní API s různými možnostmi, například z vyhledávání adres pro hledání dat bodu zájmu (POI) v případě určitého umístění. V tomto článku budeme sdílet osvědčené postupy pro volání dat prostřednictvím služby Azure Maps Search Services. V tomto kurzu se naučíte:

* Sestavení dotazů pro vrácení odpovídajících shod
* Omezení výsledků hledání
* Přečtěte si rozdíl mezi různými typy výsledků
* Přečíst strukturu odpovědi na hledání adres


## <a name="prerequisites"></a>Požadavky

Pokud chcete volat rozhraní API služby Maps, potřebujete účet a klíč mapy. Informace o vytvoření účtu najdete v pokynech v tématu [Správa účtu](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) a postup pro získání primárního klíče předplatného pro váš účet pomocí kroků v části [získání primárního klíče](./tutorial-search-location.md#getkey) .

> [!Tip]
> Pokud chcete zadat dotaz na vyhledávací službu, můžete k sestavení volání REST použít [aplikaci pro odesílání](https://www.getpostman.com/apps) nebo můžete použít jakékoli vývojové prostředí API, které dáváte přednost.


## <a name="best-practices-for-geocoding"></a>Osvědčené postupy pro geografické kódování

Když vyhledáte úplnou nebo částečnou adresu pomocí Azure Maps Search Service, převezme se váš hledaný výraz a vrátí souřadnici zeměpisné délky a zeměpisné šířky adresy. Tento proces se nazývá geografické kódování. Schopnost geografického kódu v zemi závisí na pokrytí dat provozu a přesnosti geografického kódování služby geografického kódování.

Další informace o Azure Maps možností geografického kódování podle země nebo oblasti najdete v části [pokrytí geografického kódování](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) .

### <a name="limit-search-results"></a>Omezení výsledků hledání

   V této části se dozvíte, jak pomocí rozhraní API pro vyhledávání Azure Maps omezit výsledky hledání. 

   > [!Note]
   > Ne všechna rozhraní API pro vyhledávání plně podporují níže uvedené parametry.

   **Výsledky hledání geografického bias**

   Aby bylo možné své výsledky geograficky vysměrovat do příslušné oblasti pro uživatele, měli byste vždy přidat maximální možné zadání podrobného umístění. Chcete-li omezit výsledky hledání, zvažte přidání následujících typů vstupu:

   1. Nastavte parametr `countrySet`, například US, FR. Výchozím chováním vyhledávání je hledání celého světa, což může vracet zbytečné výsledky. Pokud dotaz nezahrnuje parametr `countrySet`, hledání může vracet nepřesné výsledky. Například hledání města s názvem **Bellevue** vrátí výsledky z USA a Francie, protože existují města s názvem **Bellevue** ve Francii a v USA.

   2. Pomocí parametrů `btmRight` a `topleft` můžete nastavit ohraničovací rámeček a omezit tak hledání na určitou oblast na mapě.

   3. Chcete-li ovlivnit oblast důležitosti pro výsledky, můžete definovat `lat`a `lon` parametry souřadnic a nastavit poloměr oblasti hledání pomocí parametru `radius`.


   **Parametry hledání přibližné vyhledávání**

   1. `minFuzzyLevel` a `maxFuzzyLevel`, vrátí relevantní shody i v případě, že parametry dotazu přesně neodpovídají požadovaným informacím. Většina vyhledávacích dotazů je ve výchozím nastavení `minFuzzyLevel=1` a `maxFuzzyLevel=2` tak, aby získala výkon a snížila neobvyklé výsledky. Příkladem hledaného termínu "restrant" se shoduje s názvem "restaurace", pokud je `maxFuzzyLevel` nastaveno na 2. Výchozí přibližné úrovně mohou být přepsány podle požadavků na požadavky. 

   2. Můžete také zadat přesně sadu výsledných typů, které mají být vráceny pomocí parametru `idxSet`. Pro tento účel můžete odeslat seznam indexů oddělených čárkami, přičemž pořadí položek nezáleží. Podporovány jsou následující indexy:

       * `Addr`**rozsahy adres** - : u některých ulic jsou k dispozici adresní body, které se interpolují od začátku do konce ulice; Tyto body jsou reprezentovány jako rozsahy adres.
       * `Geo` - **geografické**oblasti: oblasti na mapě, které reprezentují správní rozdělení půdy, tj. země, stát, město.
       * Adresa  - **bodu**`PAD`: body na mapě, kde se v indexu nachází konkrétní adresa s názvem a číslem ulice, například Soquel Dr 2501. Nejvyšší úroveň přesnosti je k dispozici pro adresy.  
       * `POI` - **bodů zájmu**: body na mapě, které stojí za pozornost a můžou být zajímavé.  [Získat adresu hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) vrátí zájmu.  
       * `Str` - **ulic**: reprezentace ulic na mapě.
       * `XStr` - **více ulic/průniky**: reprezentace spojení; místa, kde se protínají dvě ulice.


       **Příklady použití**:

       * idxSet = POI (jenom hledání bodů zájmu) 

       * idxSet = PAD; addr (Prohledat pouze adresy, PAD = adresa bodu, addr = rozsah adres)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Převrátit filtr typu geografického kódu a geografické entity

Při provádění zpětného vyhledávání v rámci INCODE pomocí služby [Search reverzního rozhraní API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)má služba schopnost vracet mnohoúhelníky pro oblasti správy. Zadáním parametru `entityType` v žádosti můžete zúžit hledání zadaných typů geografických entit. Výsledná odpověď bude obsahovat geografické ID a odpovídající typ entity. Pokud zadáte více než jednu entitu, koncový bod vrátí **nejmenší dostupnou entitu**. Vrácené ID geometrie lze použít k získání geometrie této geografické [služby prostřednictvím Get mnohoúhelníku](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Vzorový požadavek:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Základě**

```JSON
{
    "summary": {
        "queryTime": 8,
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
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
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

Parametr `language` umožňuje nastavit, které výsledky hledání jazyka by měly být vráceny. Pokud jazyk není v požadavku nastavený, služba Search Service se automaticky nastaví na nejběžnější jazyk v zemi nebo oblasti. I když nejsou k dispozici data v zadaném jazyce, použije se výchozí jazyk. Seznam podporovaných jazyků najdete v tématu [podporované jazyky](https://docs.microsoft.com/azure/azure-maps/supported-languages) s ohledem na Azure Maps služby podle země nebo oblasti.


### <a name="predictive-mode-auto-suggest"></a>Prediktivní režim (automatické návrhy)

Chcete-li najít další shody pro částečné dotazy, `typeahead` parametr by měl být nastaven na hodnotu "true". Dotaz bude interpretován jako částečný vstup a hledání bude obsahovat prediktivní režim. V opačném případě převezme služba všechny příslušné informace.

V ukázkovém dotazu níže vidíte, že se pro "Microso" používá dotaz na vyhledávací službu s parametrem `typeahead` nastaveným na **hodnotu true**. Pokud si vyberete odpověď, vidíte, že vyhledávací služba interpretuje dotaz jako na částečný dotaz a odpověď obsahuje výsledky pro automatický navrhovaný dotaz.

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
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>Kódování identifikátoru URI pro zpracování speciálních znaků 

Chcete-li najít meziulici adresy, tj. "1 způsob & Union Street, Praha", před odesláním žádosti musí být kódován speciální znak "&". Doporučujeme kódovat znaková data v identifikátoru URI, kde jsou všechny znaky kódovány pomocí znaku% a šestnáctková hodnota se dvěma znaky odpovídající jejich znaku UTF-8.

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
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
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
                "url": "www.chevron.com",
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
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
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
                "url": "www.texaco.com/",
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
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
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
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
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
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometrie

Pokud je typ odpovědi **geometrie**, může obsahovat ID geometrie, které je vráceno v objektu **DataSources** v části geometrie a ID. Například [získání služby mnohoúhelníku](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) vám umožní požádat o data geometrie ve formátu injson, jako je například osnova města nebo letiště pro sadu entit. Tato data hranice můžete použít pro [monitorování geografických zón](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) nebo [hledání zájmu uvnitř geometrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


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
