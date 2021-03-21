---
title: Vyžádání dat počasí v reálném čase a předpovědi s využitím Azure Maps povětrnostních služeb (Preview)
description: Naučte se, jak vyžádat data v reálném čase (aktuální) a předpovědi (minutová, hodinová, denní) s daty počasí pomocí služby Microsoft Azure Maps počasí (Preview).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: fe1b337fe3e1dcf499f9a7428f66543108d0c050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680413"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services-preview"></a>Vyžádání dat počasí v reálném čase a předpovědi s využitím Azure Maps povětrnostních služeb (Preview) 

> [!IMPORTANT]
> Služba Azure Maps počasí jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Maps [povětrnostní služby](/rest/api/maps/weather) jsou sadou rozhraní API RESTful, která vývojářům umožňují integrovat do jejich řešení vysoce dynamická data historických dat, která jsou v reálném čase a předpovědi předpověď počasí. V tomto článku vám ukážeme, jak si vyžádat data počasí v reálném čase i předpovědi.

V tomto článku se dozvíte, jak:

* Vyžádání dat počasí v reálném čase (aktuální) pomocí [rozhraní API získat aktuální podmínky](/rest/api/maps/weather/getcurrentconditionspreview)
* Vyžádejte si závažné výstrahy počasí pomocí [rozhraní API získat závažné nepočasí výstrahy](/rest/api/maps/weather/getsevereweatheralertspreview).
* Vyžádejte si denní předpovědi pomocí [rozhraní API získat denní předpovědi](/rest/api/maps/weather/getdailyforecastpreview).
* Vyžádá hodinové prognózy pomocí [rozhraní Get hodinové prognózy API](/rest/api/maps/weather/gethourlyforecastpreview).
* Vyžádejte si minutové prognózy s použitím [rozhraní API pro předpovědi pro získání minut](/rest/api/maps/weather/getminuteforecastpreview).

V tomto videu najdete příklady, jak volat REST Azure Maps povětrnostním službám.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player?format=ny]

## <a name="prerequisites"></a>Předpoklady

1. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného. Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

    >[!IMPORTANT]
    >[Rozhraní API pro předpověď výsledků Get minut](/rest/api/maps/weather/getminuteforecastpreview) vyžaduje klíč cenové úrovně S1. Všechna ostatní rozhraní API vyžadují klíč cenové úrovně S0.

V tomto kurzu se používá aplikace [po](https://www.postman.com/) aplikaci, ale můžete zvolit jiné vývojové prostředí API.

## <a name="request-real-time-weather-data"></a>Vyžádat data o počasí v reálném čase

[Rozhraní API získat aktuální podmínky](/rest/api/maps/weather/getcurrentconditionspreview) vrací podrobné povětrnostní podmínky, jako je například vysrážení, teplota a vítr pro dané umístění souřadnic. Lze také načíst poznámky z posledních 6 nebo 24 hodin pro konkrétní umístění. Odpověď obsahuje podrobnosti, jako je datum a čas pozorování, stručný popis povětrnostních podmínek, ikona počasí, příznaky indikátoru pro vysrážení a teplota. Vrátí se také RealFeel™ teplotní a ultrafialový (UV) index.

V tomto příkladu použijete [rozhraní API získat aktuální podmínky](/rest/api/maps/weather/getcurrentconditionspreview) k načtení aktuálních povětrnostních podmínek v souřadnicích v Seattle, WA.

1. Otevřete aplikaci pro vyúčtování. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **kolekce**.  Pojmenujte kolekci a vyberte tlačítko **vytvořit** . Tuto kolekci použijete pro zbytek příkladů v tomto dokumentu.

2. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozím kroku, a pak vyberte **Uložit**.

3. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Klikněte na modré tlačítko **Odeslat** . Tělo odpovědi obsahuje aktuální informace o počasí.

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>Vyžadovat závažné výstrahy počasí

[Azure Maps získat závažné počasí upozorňující na nepočasí](/rest/api/maps/weather/getsevereweatheralertspreview) vrátí závažné výstrahy počasí, které jsou dostupné po celém světě, od oficiálních meteorologických úřadů státní správy a od začátku globálního až po regionální poskytovatele upozornění na počasí. Služba může vracet podrobnosti, jako je typ výstrahy, kategorie, úroveň a podrobné popisy aktivních závažných výstrah pro požadované umístění, jako je například hurikány, thunderstorms, blesk, tepelné vlny nebo doménová struktura požárů. Jako příklad mohou vedoucí logistiky vizualizovat závažné povětrnostní podmínky na mapě, společně s obchodními umístěními a plánovanými trasami a dále koordinovat s ovladači a místními pracovními procesy.

V tomto příkladu použijete [rozhraní API získat vážné počasí](/rest/api/maps/weather/getsevereweatheralertspreview) k načtení aktuálních povětrnostních podmínek v souřadnicích v CHEYENNE, WY.

>[!NOTE]
>Tento příklad načte v době psaní závažné výstrahy počasí. Je možné, že v požadovaném umístění již neexistují závažná upozornění na počasí. Pokud chcete při spuštění tohoto příkladu načíst skutečná data o závažných výstrahách, budete potřebovat načíst data v jiném umístění souřadnic.

1. Otevřete aplikaci pro odesílání, klikněte na **Nový** a vyberte **žádost**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozí části, nebo vytvořte novou, a pak vyberte **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Klikněte na modré tlačítko **Odeslat** . Pokud nejsou k dispozici žádné závažné výstrahy počasí, text odpovědi bude obsahovat prázdné `results[]` pole. Pokud existují závažné výstrahy počasí, tělo odpovědi obsahuje něco podobného jako u následující odpovědi JSON:

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>Žádost o každodenní předpověď počasí dat

[Rozhraní API získat denní předpovědi](/rest/api/maps/weather/getdailyforecastpreview) vrátí detailní denní předpověď počasí, jako je například teplota a vítr. Požadavek může určit, kolik dní se má vrátit: 1, 5, 10, 15, 25 nebo 45 dnů pro dané umístění souřadnic. Odpověď obsahuje podrobnosti, jako je teplota, větru, srážky, kvalita ovzduší a index UV.  V tomto příkladu si vyžádáme pět dní nastavením `duration=5` .

>[!IMPORTANT]
>V cenové úrovni S0 si můžete vyžádat denní předpověď na dalších 1, 5, 10 a 15 dnů. V cenové úrovni S1 si také můžete vyžádat denní předpověď na dalších 25 dní a 45 dnů.

V tomto příkladu použijete [rozhraní API získat denní prognózu](/rest/api/maps/weather/getdailyforecastpreview) k načtení 5 dní předpověď počasí pro souřadnice umístěné v Seattle, WA.

1. Otevřete aplikaci pro odesílání, klikněte na **Nový** a vyberte **žádost**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozí části, nebo vytvořte novou, a pak vyberte **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Klikněte na modré tlačítko **Odeslat** . Tělo odpovědi obsahuje pětiletou data předpovědi počasí. Pro účely zkrácení odpověď ve formátu JSON níže ukazuje předpověď prvního dne.
    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>Žádost o hodinu počasí data předpovědi

[Rozhraní API získat hodinové prognózy](/rest/api/maps/weather/gethourlyforecastpreview) vrátí Detailní předpověď počasí o hodinu od dalších 1, 12, 24 (1 den), 72 (3 dny), 120 (5 dní) a 240 hodin (10 dní) pro dané umístění souřadnic. Rozhraní API vrátí podrobnosti, jako je teplota, vlhkost, větru, srážky a index UV.

>[!IMPORTANT]
>V cenové úrovni S0 si můžete vyžádat hodinovou prognózu pro následující 1, 12, 24 hodin (1 den) a 72 hodin (3 dny). V cenové úrovni S1 si také můžete vyžádat hodinovou prognózu po dobu příštích 120 (5 dní) a 240 hodin (10 dní).

V tomto příkladu použijete [rozhraní Get hodinové prognózy API](/rest/api/maps/weather/gethourlyforecastpreview) k načtení předpovědi hodinových počasí po dalších 12 hodin v souřadnicích v Seattle, WA.

1. Otevřete aplikaci pro odesílání, klikněte na **Nový** a vyberte **žádost**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozí části, nebo vytvořte novou, a pak vyberte **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Klikněte na modré tlačítko **Odeslat** . Tělo odpovědi obsahuje data předpovědi počasí po dobu příštích 12 hodin. Pro účely zkrácení odpověď ve formátu JSON níže ukazuje předpověď první hodiny.

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```
## <a name="request-minute-by-minute-weather-forecast-data"></a>Vyžádat data předpovědi počasí po minutách

 [Rozhraní API pro předpověď výsledků Get minut](/rest/api/maps/weather/getminuteforecastpreview) vrací předpovědi po minutách pro dané umístění po dobu dalších 120 minut. Uživatelé můžou vyžádat předpovědi počasí v intervalech od 1, 5 do 15 minut. Odpověď obsahuje podrobnosti, jako je například typ srážky (včetně deště, sněhu nebo kombinace obou), času zahájení a hodnoty intenzity vysrážení (dBZ).

V tomto příkladu použijete [rozhraní API pro předpověď minut](/rest/api/maps/weather/getminuteforecastpreview) k načtení předpovědi počasí po minutách v souřadnicích v Seattle, WA. Předpověď počasí je uvedena po dobu dalších 120 minut. Náš dotaz požádá o zadání prognózy v intervalech 15 minut, ale můžete upravit parametr tak, aby byl buď 1, nebo 5 minut.

1. Otevřete aplikaci pro odesílání, klikněte na **Nový** a vyberte **žádost**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozí části, nebo vytvořte novou, a pak vyberte **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného.

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Klikněte na modré tlačítko **Odeslat** . Tělo odpovědi obsahuje data předpovědi počasí po dobu dalších 120 minut, v intervalech 15 minut.

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Koncepty Azure Maps počasí Services (Preview)](./weather-services-concepts.md)

> [!div class="nextstepaction"]
> [Azure Maps povětrnostní služby (Preview) REST API](/rest/api/maps/weather)