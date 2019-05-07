---
title: Jak hledat, efektivně se službou Azure Maps Search | Dokumentace Microsoftu
description: Další informace o použití osvědčených postupů pro hledání pomocí služby Azure Search mapy
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9cb0f89b4a48d7139adb35dcef48c0115b005c57
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205610"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Osvědčené postupy k používání služby vyhledávání Azure Maps

Azure Maps [vyhledávací služba](https://docs.microsoft.com/rest/api/maps/search) zahrnuje rozhraní API s různými možnostmi, například z hledání adresy až po hledání bodu zájmu (POI) data na určitém místě. V tomto článku budeme sdílet osvědčené postupy při volání data prostřednictvím služby vyhledávání Azure Maps. V tomto kurzu se naučíte:

* Sestavování dotazů vrátit relevantní výsledky
* Limit výsledků hledání
* Další rozdíl mezi různými typy výsledku
* Přečtěte si Struktura adresy hledání odpovědí


## <a name="prerequisites"></a>Požadavky

Aby všechna volání do rozhraní API služby Maps, potřebujete účet Maps a klíč. Informace o vytváření účtu a klíč načítání, najdete v části [jak spravovat účet Azure Maps a klíče](how-to-manage-account-keys.md).

> [!Tip]
> K dotazování na službu search, můžete použít [aplikaci Postman](https://www.getpostman.com/apps) k sestavení REST volání nebo můžete použít všechny vývojové prostředí rozhraní API, které dáváte přednost.


## <a name="best-practices-for-geocoding"></a>Osvědčené postupy pro geografické kódování

Při hledání celé nebo jeho část adresu pomocí služby Azure Search mapy, přebírá hledaný termín a vrací souřadnice zeměpisné šířky a délky na adresu. Tento proces se nazývá geokódování. Schopnost geokód v zemi, je závislá na data pokrytí cestách a přesnost geografické kódování služby geokódování.

Zobrazit [zajištění geokódování](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) pro další informace o možnostech Azure Maps geokódování podle země nebo oblasti.

### <a name="limit-search-results"></a>Limit výsledků hledání

   V této části se dozvíte, jak používat Azure Maps search API omezit rozsah výsledků vyhledávání. 

   > [!Note]
   > Ne všechny hledání rozhraní API plně podporují níže uvedenými parametry

   **Výsledky hledání GEO posun**

   Aby geo posun výsledky do oblasti relevantní pro vaše uživatele, měli byste vždy přidat maximální možné podrobné vstup umístění. Pokud chcete omezit výsledky vyhledávání, zvažte přidání vstupní typy. následující:

   1. Nastavte `countrySet` parametru, například "US, FR". Výchozí chování vyhledávání je pro celý svět, vrací potenciálně zbytečné výsledky hledání. Pokud váš dotaz neobsahuje `countrySet` parametr hledání může vracet nepřesné výsledky. Například vyhledejte město s názvem **Bellevue** vrátí výsledky z USA a (Francie), protože existují města s názvem **Bellevue** ve Francii a v USA.

   2. Můžete použít `btmRight` a `topleft` pole parametrů pro nastavení ohraničující omezit hledání na konkrétní oblasti na mapě.

   3. K ovlivnění oblasti podle relevance výsledků, můžete definovat `lat`a `lon` koordinaci parametry a nastavení protokolu radius oblast hledání pomocí `radius` parametru.


   **Parametry vyhledávání přibližných shod**

   1. `minFuzzyLevel` a `maxFuzzyLevel`, pomoct vrátit relevantní výsledky i v případě, že parametry dotazu přesně odpovídat na požadované informace. Většinu vyhledávacích dotazů ve výchozím nastavení `minFuzzyLevel=1` a `maxFuzzyLevel=2` získat výkon a snížit neobvyklé výsledky. Příkladem hledaného termínu trvat "restrant", to odpovídá "restaurace" při `maxFuzzyLevel` je nastavena na 2. Podle potřeby žádost lze přepsat výchozí úrovně přibližných shod. 

   2. Můžete také určit přesnou sadu typy výsledků, který se má vrátit s použitím `idxSet` parametru. K tomuto účelu můžete odeslat čárkami oddělený seznam indexů, není důležité pořadí položek. Tady jsou podporované indexů:

       * `Addr` - **Rozsahy adres**: Pro některé ulice jsou adresy body, které jsou interpolovány ze začátku a konci ulice; Tyto body jsou reprezentovány ve formě rozsahy adres.
       * `Geo` - **Geografické oblasti**: Oblasti na mapu, která představuje správní území pozemek, to znamená, zemi, stát, Město.
       * `PAD` - **Adresa**:  Bodů na mapě, kde konkrétní adresu ulice název a číslo lze nalézt v indexu, například 2501 Soquel zotavení po havárii. To je nejvyšší úroveň přesnosti, které jsou k dispozici pro adresy.  
       * `POI` - **Body zájmu**: Body na mapě, které stojí za pozornost a může být zajímavé.  [Získat adresu hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) nevrátí současně.  
       * `Str` - **Ulice**: Reprezentace ulice na mapě.
       * `XStr` - **Různé ulice/průniky**:  Reprezentace spojovacích bodech; místa, kde dva ulice intersect.


       **Příklady použití**:

       * idxSet = POI (pouze hledání bodů zájmu) 

       * idxSet = panel Adresa (hledání adresy pouze, panel = Adresa, adresa = rozsah adres)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Obrátit geokód a geografického umístění filtr typu entity

Při provádění geokód zpětného vyhledávání s [rozhraní API pro adresu zpětného vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), že služba má schopnost vrátit mnohoúhelníky pro oblasti pro správu. Zadáním parametru `entityType` v požadavku, můžete zúžit hledání pro typy entity zadané zeměpisné oblasti. Výsledný odpověď bude obsahovat geography ID, jakož i odpovídající typ entity. Pokud zadáte více než jednu entitu, vrátí koncový bod **nejmenší entity, které jsou k dispozici**. Vrátí ID geometrie, je možné získat geometrie tohoto Geografie prostřednictvím [služby získat mnohoúhelníku](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Ukázka požadavku:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Odpověď:**

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

### <a name="search-results-language"></a>Jazyk výsledků vyhledávání

`language` Parametr můžete nastavit v jazyce hledání by měla být vrácena výsledky. Pokud jazyk není nastavena v požadavku, vyhledávací služba automaticky výchozí nejběžnější jazyk v zemi nebo oblast. Kromě toho pokud data v zadaném jazyce není k dispozici, je použít výchozí jazyk. Zobrazit [podporované jazyky](https://docs.microsoft.com/azure/azure-maps/supported-languages) pro seznam podporovaných jazyků s ohledem na služby Azure Maps podle země nebo oblasti.


### <a name="predictive-mode-auto-suggest"></a>Prediktivní režimu (automatické návrhy)

K vyhledání odpovídající položky pro částečné dotazy `typeahead` parametr by měl být nastaven na hodnotu "true". Dotaz, který bude vyhodnocen jako částečné vstup a hledání zadá prediktivní režimu. Jinak služby bude předpokládat, že byl předaný všechny relevantní informace.

V ukázce dotazu níže můžete vidět, že adresa služby search se dotazují pro "Microso" s `typeahead` parametr nastaven na **true**. Pokud zjistíte odpovědi, uvidíte, že službu search interpretován dotazu jako částečné dotazu a odpovědi obsahuje výsledků dotazu navrhované automaticky.

**Ukázkový dotaz:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Odpověď:**

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


### <a name="uri-encoding-to-handle-special-characters"></a>Identifikátor URI kódování pro zpracování speciálních znaků 

K vyhledání křížové ulic, to znamená, "1. dne Zacházíme & sjednocení ulici, Seattle", speciální znak 'a' musí být zakódovaný, aby před odesláním požadavku. Doporučujeme, abyste kódování dat znak v identifikátoru URI, tam, kde jsou všechny znaky kódovaný pomocí znaku "%" a dvou znaků šestnáctkové hodnoty odpovídající jejich UTF-8 znaků.

**Příklady použití**:

Získáte adresu hledání:

```
query=1st Avenue & E 111th St, New York
```

 musí být zakódován jako:

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Tady jsou různé způsoby použití pro různé jazyky: 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```C#
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

Ruby:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Go:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Osvědčené postupy pro službu POI search

Body zájmu (POI) vyhledávání umožňuje požádat o POI výsledky podle názvu, například hledat firmy podle názvu. Důrazně doporučujeme použít `countrySet` parametr k určení zemí, kde vaše aplikace potřebuje pokrytí, jako výchozí chování bude pro celý svět, vrací potenciálně zbytečné výsledky hledání a/nebo mít za následek delší dobu vyhledávání.

### <a name="brand-search"></a>Hledat značky

Ke zlepšení relevance výsledků a informací v odpovědi, odpověď hledání bodu zájmu (POI) obsahuje značku informace, které je možné dále analyzovat odpověď.

Vytvoříme [vyhledávání kategorie POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) žádost o čerpacím stanicím téměř sídla Microsoftu (Redmond, WA). Pokud zjistíte odpovědi, uvidíte informace značky pro každý POI vrátila.

**Ukázkový dotaz:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Odpověď:**

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


### <a name="airport-search"></a>Hledání letišti

Hledání POI podporuje vyhledávání letiště pomocí oficiální letiště kódy. Například **moře** (Praha Tacoma mezinárodní letiště). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Vyhledávání blízkých

K načtení jenom POI výsledky kolem určitého umístění [okolních rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) může být správnou volbou. Tento koncový bod vrátí pouze výsledky POI a nepřijímá parametr dotazu hledání. Omezit rozsah výsledků, doporučujeme nastavit protokolu radius.

## <a name="understanding-the-responses"></a>Principy odpovědi

Vytvoříme požadavek hledání adresy na Azure Maps [služba search](https://docs.microsoft.com/rest/api/maps/search) adres v Seattlu. Když se podíváte na následující adrese URL požadavku pečlivě, jsme nastavili `countrySet` parametr **USA** chcete vyhledat adresu ve Spojených státech amerických.

**Ukázkový dotaz:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Dále můžeme Podíváme se na struktuře odpověď níže. Typy výsledků výsledných objektech v odpovědi se liší. Pokud pečlivě zjistíte, že vidíte, že máme tři různé typy objektů výsledků, které jsou "Adresa", "Ulice" a "Mezi ulice". Všimněte si, že adresa hledání nevrací současně. `Score` Parametr pro každý objekt odpovědi označuje relativní odpovídající skóre skóre ostatní objekty v stejnou odpověď. Zobrazit [získat adresu hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) pro další informace o parametrech objektu odpovědi.

**Podporované typy výsledků:**

* **Adresa:** Bodů na mapě s konkrétní adresu ulice název a číslo. Nejvyšší úroveň přesnosti, které jsou k dispozici pro adresy. 

* **Rozsah adres:**  Pro některé ulice jsou adresy body, které jsou interpolovány ze začátku a konci ulice; Tyto body jsou reprezentovány ve formě rozsahy adres. 

* **Zeměpisné oblasti:** Oblasti na mapu, která představuje správní území pozemek, to znamená, zemi, stát, Město. 

* **POI - (body zájmu):** Body na mapě, které stojí za pozornost a může být zajímavé.

* **Ulice:** Reprezentace ulice na mapě. Adresy jsou vyhodnoceny na souřadnice zeměpisné šířky a délky, který obsahuje adresu ulice. Číslo domu nemusí být zpracován. 

* **Ulice mezi:** Průnik. Reprezentace spojovacích bodech; místa, kde dva ulice intersect.

**Odpověď:**

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

Pokud je typ odpovědi **geometrie**, může obsahovat ID geometrie, která je vrácena v **zdroje dat** objekt v rámci "geometrie" a "id". Například [služby získat mnohoúhelníku](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) umožňuje požádat o geometrii data ve formátu GeoJSON, například města nebo letiště obrys pro sadu entit. Můžete použít tato data hranice pro [monitorování geografických zón](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) nebo [hledání současně uvnitř geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Vyhledat adresu](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) nebo [vyhledávání přibližných shod](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) odpovědi rozhraní API můžete zahrnout **ID geometrie** , který je vrácen objekt zdroje dat v části "geometrie" a "id".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Další postup

* Přečtěte si [vytvoření žádosti o služby Azure Search mapy](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Prozkoumejte Azure Maps [vyhledat dokumentaci k rozhraní API služby](https://docs.microsoft.com/rest/api/maps/search). 