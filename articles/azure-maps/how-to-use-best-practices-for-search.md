---
title: Osvědčené postupy pro Azure Maps Search Service | Mapy Microsoft Azure
description: Naučte se, jak použít osvědčené postupy při použití Search Service ze Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ec2b971941f460522046d56cd097ede15a3bdcbe
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086330"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Osvědčené postupy pro Azure Maps Search Service

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) obsahuje rozhraní API, která nabízejí různé možnosti, které vývojářům umožňují hledat adresy, místa, obchodní výpisy podle názvu nebo kategorie a další geografické informace. [Rozhraní API pro vyhledávání s fuzzy logikou](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) například umožňuje uživatelům vyhledat adresu nebo bod zájmu (POI).

Tento článek vysvětluje, jak použít zvukové postupy při volání dat z Azure Maps Search Service. Dozvíte se, jak provést tyto akce:
> [!div class="checklist"]
> * Sestavení dotazů pro vrácení odpovídajících shod
> * Omezení výsledků hledání
> * Informace o rozdílech mezi typy výsledků
> * Přečíst strukturu hledání adres – odpověď

## <a name="prerequisites"></a>Předpoklady

1. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.

V tomto článku se k sestavení volání REST používá [aplikace pro odesílání](https://www.postman.com/downloads/) , ale můžete zvolit prostředí pro vývoj rozhraní API.

## <a name="best-practices-to-geocode-addresses"></a>Osvědčené postupy pro používání adres pro INCODE

Když vyhledáte úplnou nebo částečnou adresu pomocí Azure Maps Search Service, rozhraní API přečte klíčová slova z vyhledávacího dotazu. Pak vrátí souřadnice délky a zeměpisné šířky adresy. Tento proces se nazývá *geografické kódování*.

Schopnost geografického kódu v zemi nebo oblasti závisí na dostupnosti dat o provozu a přesnosti služby geografického kódování. Další informace o Azure Maps možností geografického kódování podle země nebo oblasti najdete v tématu [pokrytí geografického kódování](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

### <a name="limit-search-results"></a>Omezení výsledků hledání

 Rozhraní API pro Azure Maps Search vám může pomáhat omezit výsledky hledání odpovídajícím způsobem. Výsledky můžete omezit tak, abyste uživatelům zobrazili relevantní data.

> [!NOTE]
> Rozhraní API pro vyhledávání podporují více parametrů, než jenom ty, které tento článek popisuje.

#### <a name="geobiased-search-results"></a>Výsledky hledání s nejupřednostněným výsledkem

Chcete-li výsledky geografického posunu do příslušné oblasti pro uživatele, vždy přidejte tolik podrobností umístění, kolik je možné. Můžete chtít výsledky hledání omezit zadáním některých typů vstupu:

* Nastavte `countrySet` parametr. Můžete ho nastavit například na `US,FR` . Rozhraní API standardně prohledává celý svět, takže může vracet zbytečné výsledky. Pokud dotaz nemá žádný `countrySet` parametr, hledání může vracet nepřesné výsledky. Například hledání města s názvem *Bellevue* vrátí výsledky z USA a Francie, protože země nebo oblasti obsahují město s názvem *Bellevue*.

* Pomocí `btmRight` parametrů a můžete `topleft` nastavit ohraničovací rámeček. Tyto parametry omezují hledání na konkrétní oblast na mapě.

* Pro ovlivnění oblasti relevance pro výsledky definujte `lat` `lon` parametry a souřadnice. Pomocí `radius` parametru nastavte poloměr oblasti hledání.


#### <a name="fuzzy-search-parameters"></a>Parametry hledání přibližné vyhledávání

Doporučujeme, abyste používali Azure Maps [hledání přibližného rozhraní API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) , pokud neznáte vstupy uživatelů pro vyhledávací dotaz. Například vstup od uživatele může být adresa nebo typ bodu zájmu (POI), jako je třeba *nákup malé*. Rozhraní API kombinuje POI vyhledávání a geografické kódování do kanonického *víceřádkového vyhledávání*: 

* `minFuzzyLevel`Parametry a `maxFuzzyLevel` vrátí relevantní shody i v případě, že parametry dotazu se přesně neshodují s informacemi, které uživatel chce. Pro maximalizaci výkonu a omezení neobvyklých výsledků nastavte vyhledávací dotazy na výchozí hodnoty `minFuzzyLevel=1` a `maxFuzzyLevel=2` . 

    Například pokud `maxFuzzyLevel` je parametr nastaven na hodnotu 2, hledaný výraz *restrant* odpovídá *restauraci*. Výchozí přibližné úrovně můžete přepsat, pokud potřebujete. 

* Použijte `idxSet` parametr k určení priorit přesné sady typů výsledků. Chcete-li určit prioritu přesné sady výsledků, můžete odeslat seznam indexů oddělených čárkami. V seznamu nezáleží na pořadí položek. Azure Maps podporuje následující indexy:

* `Addr` - **Rozsahy adres**: adresní body, které se interpolují od začátku a konce ulice. Tyto body jsou reprezentovány jako rozsahy adres.
* `Geo` - Geografické **oblasti: správní**rozdělení půdy. Zeměpisná oblast může být například země/oblast, stát nebo město.
* `PAD` - **Adresy bodů**: adresy, které obsahují název a číslo ulice. Adresy bodů najdete v indexu. Příkladem je *Soquel Dr 2501*. Adresa bodu poskytuje nejvyšší úroveň přesnosti, která je k dispozici pro adresy.  
* `POI` - **Body zájmu**: body na mapě, které se považují za pozornost nebo které mohou být zajímavé. [Rozhraní API pro vyhledávání adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) nevrací zájmu.  
* `Str` - **Ulice**: ulic na mapě.
* `XStr` - **Meziulice nebo průniky**: spojení nebo místa, kde se dvě ulice protínají.


#### <a name="usage-examples"></a>Příklady použití

* `idxSet=POI` -Prohledat pouze zájmu. 

* `idxSet=PAD,Addr` -Hledá pouze adresy. `PAD` označuje adresu bodu a `Addr` označuje rozsah adres.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Inverzní a filtr pro typ geografických entit

Když provedete zpětné vyhledávání v [rozhraní API pro reverzní vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), může služba vracet mnohoúhelníky pro oblasti správy. Například můžete chtít načíst mnohoúhelník oblasti pro město města.Chcete-li zúžit hledání na konkrétní typy geografických entit, zahrňte `entityType` parametr do svých požadavků. 

Výsledná odpověď obsahuje geografické ID a typ entity, které se shodovaly. Pokud zadáte více než jednu entitu, vrátí koncový bod *nejnižší dostupnou entitu*. Pomocí vráceného ID geometrie můžete získat geometrii geograficky prostřednictvím [služby mnohoúhelníku hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

#### <a name="sample-request"></a>Ukázková žádost

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Odpověď

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

### <a name="set-the-results-language"></a>Nastavení jazyka výsledků

`language`Pro nastavení jazyka pro vrácené výsledky hledání použijte parametr. Pokud požadavek nenastaví jazyk, pak ve výchozím nastavení Search Service používá nejběžnější jazyk v zemi nebo oblasti. Pokud v zadaném jazyce nejsou k dispozici žádná data, použije se výchozí jazyk. 

Další informace najdete v tématu [Azure Maps podporovaných jazycích](https://docs.microsoft.com/azure/azure-maps/supported-languages).


### <a name="use-predictive-mode-automatic-suggestions"></a>Použít prediktivní režim (automatické návrhy)

Chcete-li najít další shody pro částečné dotazy, nastavte `typeahead` parametr na `true` . Tento dotaz je interpretován jako částečný vstup a hledání do prediktivního režimu. Pokud nenastavíte `typeahead` parametr na `true` , služba předpokládá, že byly předány všechny příslušné informace.

V následujícím ukázkovém dotazu se pro *Microso*používá dotaz na vyhledávací adresu služby. Zde je `typeahead` parametr nastaven na hodnotu `true` . Odpověď ukazuje, že vyhledávací služba interpretuje dotaz jako částečný dotaz. Odpověď obsahuje výsledky pro automaticky navrhovaný dotaz.

#### <a name="sample-query"></a>Ukázkový dotaz

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Odpověď

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


### <a name="encode-a-uri-to-handle-special-characters"></a>Kódování identifikátoru URI pro zpracování speciálních znaků 

Chcete-li najít adresy mezi adresami, je nutné zakódovat identifikátor URI pro zpracování speciálních znaků v adrese. Vezměte v úvahu příklad této adresy: *1. způsob & Union Street, Seattle*. Zde zakódovat znak ampersand ( `&` ) před odesláním žádosti. 

Doporučujeme kódovat znaková data v identifikátoru URI. V identifikátoru URI zakódujete všechny znaky pomocí znaku procenta ( `%` ) a šestnáctkové hodnoty se dvěma znaky, které odpovídají kódu UTF-8.

#### <a name="usage-examples"></a>Příklady použití

Začněte s touto adresou:

```
query=1st Avenue & E 111th St, New York
```

Adresa kódování:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

Můžete použít následující metody.

JavaScript nebo TypeScript:
```javascript
encodeURIComponent(query)
```

C# nebo Visual Basic:
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

PHP
```PHP
urlencode(query)
```

Ruby
```Ruby
CGI::escape(query) 
```

SWIFT
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Jděte
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Osvědčené postupy pro hledání POI

Ve POI hledání můžete vyžádat POI výsledky podle názvu. Můžete například vyhledat firmu podle názvu. 

Důrazně doporučujeme použít `countrySet` parametr k určení zemí nebo oblastí, kde vaše aplikace potřebuje pokrytí. Výchozím chováním je hledání celého světa. Toto široké hledání může vracet zbytečné výsledky a hledání může trvat dlouhou dobu.

### <a name="brand-search"></a>Hledání značky

Pro zlepšení relevance výsledků a informací v odpovědi POI odpověď vyhledávání obsahuje informace o značce. Tyto informace můžete použít k analýze odpovědi.

V žádosti můžete odeslat seznam názvů značek oddělených čárkami. Pomocí seznamu můžete omezit výsledky na konkrétní značky nastavením `brandSet` parametru. V seznamu nezáleží na pořadí položek. Když zadáte několik seznamů značek, výsledky, které se vrátí, musí patřit alespoň do jednoho z vašich seznamů.

Pokud chcete prozkoumat hledání na základě značky, udělejte si požadavek na [Hledání kategorií POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) . V následujícím příkladu se podíváme na čerpací stanice v blízkosti služby Microsoft Campus v Redmond, Washington. Odpověď zobrazuje informace značky pro každý vrácený POI.

#### <a name="sample-query"></a>Ukázkový dotaz

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Odpověď

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

Pomocí rozhraní Search POI API můžete hledat letiště pomocí jejich oficiálního kódu. Například můžete použít *moře* k vyhledání mezinárodního letiště v Seattlu-Tacoma: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Okolní hledání

Pokud chcete načíst výsledky POI kolem konkrétního umístění, můžete zkusit použít [okolní rozhraní API pro hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby). Koncový bod vrátí pouze výsledky POI. Nebere v parametru vyhledávacího dotazu. 

Chcete-li omezit výsledky, doporučujeme nastavit protokol RADIUS.

## <a name="understanding-the-responses"></a>Porozumění odpovědím

Pojďme najít adresu v Seattlu tím, že na Azure Maps Search Service provedete požadavek na hledání adres. V následující adrese URL požadavku nastavíme parametr tak, aby se `countrySet` `US` vyhledala adresa v USA.

### <a name="sample-query"></a>Ukázkový dotaz

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Podporované typy výsledků

* **Adresa bodu**: odkazuje na mapě, která má konkrétní adresu s názvem a číslem ulice. Adresa bodu poskytuje nejvyšší úroveň přesnosti pro adresy. 

* **Rozsah adres**: rozsah adresních bodů, které se interpolují od začátku a konce ulice.  

* **Geografie**: oblasti na mapě, které reprezentují správní rozdělení půdy, například země/oblast, stát nebo město. 

* **POI**: body na mapě, které stojí za pozornost a které mohou být zajímavé.

* **Ulice**: ulic na mapě. Adresy se přeloží na zeměpisnou šířku a délku ulice, která obsahuje adresu. Číslo domu nemusí být zpracováno. 

* **Mezi ulici**: průniky. Mezi ulice představuje spojení, kde se dvě ulice protínají.

### <a name="response"></a>Odpověď

Pojďme se podívat na strukturu odpovědi. V následující reakci se typy objektů výsledků liší. Pokud pečlivě prohledáváte, zobrazí se tři typy objektů výsledků:

* Adresa bodu
* Ulice
* Mezi ulici

Všimněte si, že vyhledávání adres nevrátí zájmu.  

`Score`Parametr pro každý objekt Response indikuje, jak se shodující skóre vztahuje na skóre ostatních objektů ve stejné odpovědi. Další informace o parametrech objektu Response najdete v tématu [získání adresy hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).

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

Typ odpovědi *geometrie* může zahrnovat ID geometrie, které je vráceno v `dataSources` objektu v `geometry` a `id` . Můžete například použít [službu mnohoúhelníku hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) k vyžádání dat geometrie ve formátu "GEOMETRICKé JSON". Pomocí tohoto formátu můžete získat osnovu města nebo letiště pro sadu entit. Pak můžete pomocí těchto dat hranice [nastavit geografickou](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) nebo [vyhledávací zájmuou v geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


Odpovědi pro rozhraní API pro [vyhledávání adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) nebo rozhraní API pro [přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) můžou zahrnovat ID geometrie, které se vrátí do `dataSources` objektu v rámci `geometry` a `id` :


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc, přečtěte si:

> [!div class="nextstepaction"]
> [Sestavování žádostí o Azure Maps Search Service](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní API pro Search Service](https://docs.microsoft.com/rest/api/maps/search)
