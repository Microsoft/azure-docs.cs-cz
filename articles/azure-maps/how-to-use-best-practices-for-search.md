---
title: Doporučené postupy pro službu Azure Maps Search Service | Mapy Microsoft Azure
description: Přečtěte si, jak používat osvědčené postupy při používání vyhledávací služby z Map Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8d62d7d278323baa0ae49b9e12f46468efb067a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335303"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Doporučené postupy pro službu Azure Maps Search Service

Služba [Azure](https://docs.microsoft.com/rest/api/maps/search) Maps Search Service obsahuje rozhraní API, která nabízejí různé funkce. Rozhraní API pro vyhledávací adresu může například najít body zájmu (POI) nebo data kolem určitého místa. 

Tento článek vysvětluje, jak použít postupy zvuku při volání dat ze služby Azure Maps Search Service. Dozvíte se, jak provést tyto akce:

* Sestavení dotazů vrátit relevantní shody.
* Omezte výsledky hledání.
* Seznamte se s rozdíly mezi typy výsledků.
* Přečtěte si strukturu hledání a odpovědi na adresu.

## <a name="prerequisites"></a>Požadavky

Pokud chcete volat na rozhraní API služby Azure Maps, potřebujete účet Azure Maps a klíč. Další informace naleznete [v tématu Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) a Získání [primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account). 

Informace o ověřování v Mapách Azure najdete [v tématu Správa ověřování v Mapách Azure](./how-to-manage-authentication.md).

> [!TIP]
> Chcete-li dotaz vyhledávací služby, můžete použít [aplikaci Postman](https://www.getpostman.com/apps) k vytvoření volání REST. Nebo můžete použít libovolné vývojové prostředí rozhraní API, které upřednostňujete.

## <a name="best-practices-to-geocode-addresses"></a>Doporučené postupy pro geokódovací adresy

Při hledání úplné nebo částečné adresy pomocí služby Azure Maps Search Service rozhraní API čte klíčová slova z vyhledávacího dotazu. Potom vrátí souřadnice zeměpisné délky a šířky adresy. Tento proces se nazývá *geokódování*. 

Schopnost geokódu v zemi závisí na dostupnosti silničních dat a přesnosti geokódovací služby. Další informace o možnostech geokódování Azure Maps podle země nebo oblasti najdete v tématu [Pokrytí geografickým kódováním](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

### <a name="limit-search-results"></a>Omezení výsledků hledání

 Rozhraní API pro vyhledávání map Azure vám může pomoct odpovídajícím způsobem omezit výsledky hledání. Omezíte výsledky, abyste mohli uživatelům zobrazit relevantní data.

> [!NOTE]
> Vyhledávací rozhraní API podporují více parametrů než pouze ty, které tento článek popisuje.

#### <a name="geobiased-search-results"></a>Geobiased výsledky vyhledávání

Chcete-li geobias výsledky do příslušné oblasti pro uživatele, vždy přidejte co nejvíce podrobností o poloze, jak je to možné. Výsledky hledání můžete omezit zadáním některých typů vstupů:

* Nastavte `countrySet` parametr. Můžete ji nastavit `US,FR`například . Ve výchozím nastavení rozhraní API prohledává celý svět, takže může vracet nepotřebné výsledky. Pokud dotaz nemá `countrySet` žádný parametr, může hledání vrátit nepřesné výsledky. Například hledání města s názvem *Bellevue* vrací výsledky z USA a Francie, protože obě země obsahují město s názvem *Bellevue*.

* Parametry `btmRight` a `topleft` můžete použít k nastavení ohraničovacího rámečku. Tyto parametry omezují hledání na určitou oblast na mapě.

* Chcete-li ovlivnit oblast relevance výsledků, `lat` `lon` definujte a souřadnicové parametry. Pomocí `radius` parametru nastavte poloměr oblasti hledání.


#### <a name="fuzzy-search-parameters"></a>Parametry přibližné vyhledávání

Doporučujeme použít azure maps [search fuzzy api,](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pokud neznáte uživatelské vstupy pro vyhledávací dotaz. Rozhraní API kombinuje vyhledávání poi a geokódování do kanonického *jednořádkového vyhledávání*: 

* `minFuzzyLevel` Parametry `maxFuzzyLevel` a pomáhají vrátit relevantní shody i v případě, že parametry dotazu přesně neodpovídají informacím, které uživatel požaduje. Chcete-li maximalizovat výkon a snížit neobvyklé výsledky, nastavte vyhledávací dotazy na výchozí hodnoty `minFuzzyLevel=1` a `maxFuzzyLevel=2`. 

    Například pokud `maxFuzzyLevel` je parametr nastaven na 2, hledaný termín *restrant* je spárován s *restaurací*. V případě potřeby můžete přepsat výchozí úrovně přibližu. 

* Pomocí `idxSet` parametru můžete určit přesnou sadu typů výsledků. Chcete-li určit prioritu přesné sady výsledků, můžete odeslat seznam indexů oddělených čárkami. V seznamu nezáleží na pořadí položek. Azure Maps podporuje následující indexy:

* `Addr` - **Rozsahy adres**: Adresní body, které jsou interpolovány od začátku a konce ulice. Tyto body jsou reprezentovány jako rozsahy adres.
* `Geo` - **Geografie**: Správní členění půdy. Geografie může být například země, stát nebo město.
* `PAD` - **Point adresy**: Adresy, které obsahují název ulice a číslo. Adresy bodů lze nalézt v indexu. Příkladem je *Soquel Dr 2501*. Bodová adresa poskytuje nejvyšší úroveň přesnosti, která je k dispozici pro adresy.  
* `POI` - **Body zájmu**: Body na mapě, které jsou považovány za stojí za pozornost, nebo které by mohly být zajímavé. Rozhraní [API vyhledávací adresy](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) nevrací funkce (Poi).  
* `Str` - **Ulice**: Ulice na mapě.
* `XStr` - **Křížové ulice nebo křižovatky**: Křižovatky nebo místa, kde se protínají dvě ulice.


#### <a name="usage-examples"></a>Příklady použití

* `idxSet=POI`- Hledat pouze pozi. 

* `idxSet=PAD,Addr`- Hledat pouze adresy. `PAD`označuje adresu bodu `Addr` a rozsah adres.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Reverzní geokód a filtr pro typ entity zeměpisné oblasti

Když v rozhraní [Search Address Reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)prohledáte reverzní geokódy , může služba vracet vícenoly pro oblasti správy.Chcete-li zúžit hledání na konkrétní `entityType` typy entit zeměpisné oblasti, zahrňte parametr do požadavků. 

Výsledná odpověď obsahuje ID zeměpisné polohy a typ entity, který byl spárován. Pokud zadáte více než jednu entitu, pak koncový bod vrátí *nejmenší entitu k dispozici*. ID vrácené geometrie můžete použít k získání geometrie zeměpisné oblasti prostřednictvím [služby Polygon vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

#### <a name="sample-request"></a>Požadavek na ukázku

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

Pomocí `language` parametru nastavte jazyk pro vrácené výsledky hledání. Pokud požadavek jazyk nenastaví, používá ve výchozím nastavení vyhledávací služba nejběžnější jazyk v zemi nebo oblasti. Pokud v zadaném jazyce nejsou k dispozici žádná data, použije se výchozí jazyk. 

Další informace najdete v tématu [Podporované jazyky Azure Maps](https://docs.microsoft.com/azure/azure-maps/supported-languages).


### <a name="use-predictive-mode-automatic-suggestions"></a>Použití prediktivního režimu (automatické návrhy)

Chcete-li najít další shody `typeahead` pro `true`částečné dotazy, nastavte parametr na . Tento dotaz je interpretován jako částečný vstup a hledání zadá prediktivní režim. Pokud nenastavíte `typeahead` parametr `true`na , služba předpokládá, že byly předány všechny relevantní informace.

V následujícím ukázkovém dotazu je služba Vyhledávací adresa dotazována na *microso*. Zde parametr `typeahead` nastaven `true`na . Odpověď ukazuje, že vyhledávací služba interpretovala dotaz jako částečný dotaz. Odpověď obsahuje výsledky automaticky navrhovaného dotazu.

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

Chcete-li najít adresy ulic, musíte kódovat identifikátor URI pro zpracování speciálních znaků v adrese. Vezměme si tuto adresu příklad: *1st Avenue & Union Street, Seattle*. Zde před odesláním požadavku zakódujte znak ampersand (`&`). 

Doporučujeme zakódovat data znaků v identifikátoru URI. V identifikátoru URI kódovat všechny znaky pomocí`%`znaku procenta ( ) a dvouznakové šestnáctkové hodnoty, která odpovídá kódu UTF-8 znaků.

#### <a name="usage-examples"></a>Příklady použití

Začněte s touto adresou:

```
query=1st Avenue & E 111th St, New York
```

Zakódovat adresu:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

Můžete použít následující metody.

JavaScript nebo TypeScript:
```Javascript
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

Php:
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

Jít:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Doporučené postupy pro vyhledávání v oblasti pozi

Při hledání PoI můžete požadovat výsledky PoI podle názvu. Můžete například vyhledat firmu podle názvu. 

Důrazně doporučujeme použít `countrySet` parametr k určení zemí, kde vaše aplikace potřebuje pokrytí. Výchozí chování je prohledávat celý svět. Toto široké hledání může vrátit zbytečné výsledky a hledání může trvat dlouho.

### <a name="brand-search"></a>Vyhledávání značek

Pro zlepšení relevance výsledků a informací v odpovědi obsahuje odpověď na vyhledávání poi. Tyto informace můžete dále analyzovat odpověď.

V žádosti můžete odeslat seznam značek oddělených čárkami. Pomocí seznamu můžete omezit výsledky na konkrétní `brandSet` značky nastavením parametru. V seznamu nezáleží na pořadí položek. Pokud zadáte více seznamů značek, musí vrácené výsledky patřit alespoň do jednoho ze seznamů.

Chcete-li prozkoumat vyhledávání značek, vytvořte žádost o [vyhledávání v kategorii BZ.](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) V následujícím příkladu hledáme čerpací stanice v blízkosti kampusu Společnosti Microsoft v Redmondu ve státě Washington. Odpověď zobrazuje informace o značce pro každý PoI, který byl vrácen.

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


### <a name="airport-search"></a>Vyhledávání na letišti

Pomocí rozhraní API poi vyhledávání můžete hledat letiště pomocí jejich oficiálního kódu. Můžete například použít *SEA* k vyhledání mezinárodního letiště Seattle-Tacoma: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Vyhledávání v okolí

Chcete-li načíst výsledky BZ v okolí určitého umístění, můžete zkusit použít [rozhraní SEARCH Near API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby). Koncový bod vrátí pouze výsledky Bodů bodů. Nebere v parametru vyhledávacídotaz. 

Chcete-li omezit výsledky, doporučujeme nastavit poloměr.

## <a name="understanding-the-responses"></a>Pochopení odpovědí

Pojďme najít adresu v Seattlu tím, že žádost o vyhledávání adresy na Azure Maps Search Service. V následující url požadavku jsme `countrySet` nastavili parametr pro `US` vyhledávání adresy v USA.

### <a name="sample-query"></a>Ukázkový dotaz

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Podporované typy výsledků

* **Point Address**: Body na mapě, které mají konkrétní adresu s názvem a číslem ulice. Point Adresa poskytuje nejvyšší úroveň přesnosti pro adresy. 

* **Rozsah adres**: Rozsah adresních bodů, které jsou interpolovány od začátku a konce ulice.  

* **Geografie**: Oblasti na mapě, které představují administrativní rozdělení země, například země, stát nebo město. 

* **POI**: Body na mapě, které stojí za pozornost a které by mohly být zajímavé.

* **Ulice**: Ulice na mapě. Adresy jsou přeloženy na souřadnice zeměpisné šířky a délky ulice, která adresu obsahuje. Číslo domu nemusí být zpracováno. 

* **Cross Street**: Křižovatky. Křížové ulice představují křižovatky, kde se protínají dvě ulice.

### <a name="response"></a>Odpověď

Podívejme se na strukturu odpovědí. V odpovědi, která následuje, typy výsledcích objektů se liší. Podíváte-li se pozorně, uvidíte tři typy objektů výsledků:

* Bodová adresa
* Ulice
* Křížová ulice

Všimněte si, že hledání adresy nevrací pozi .  

Parametr `Score` pro každý objekt odpovědi označuje, jak odpovídající skóre souvisí s skóre jiných objektů ve stejné odpovědi. Další informace o parametrech objektu odpovědi naleznete v [tématu Získání vyhledávací adresy](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).

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

Typ odpovědi *geometrie* může zahrnovat ID geometrie, `dataSources` která `geometry` `id`je vrácena v objektu pod a . Můžete například použít [službu Search Polygon](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) k vyžádání dat geometrie ve formátu GeoJSON. Pomocí tohoto formátu můžete získat přehled města nebo letiště pro sadu entit. Tato data hranic pak můžete použít k [nastavení geografické zóny](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) nebo [funkce Poi hledání uvnitř geometrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


Odpovědi pro [rozhraní SEARCH ADDRESS](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) API nebo [rozhraní Search Fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API mohou obsahovat ID geometrie, které je vráceno v objektu `dataSources` pod `geometry` a: `id`


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Další kroky

Další informace naleznete v tématu :

> [!div class="nextstepaction"]
> [Jak vytvářet požadavky služby Azure Maps Search Service](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [Dokumentace rozhraní API služby Vyhledávání](https://docs.microsoft.com/rest/api/maps/search)