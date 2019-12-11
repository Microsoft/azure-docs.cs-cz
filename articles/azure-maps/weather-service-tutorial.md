---
title: 'Kurz: spojení dat senzorů s daty předpovědi počasí pomocí Azure Notebooks (Python) | Microsoft Docs'
description: 'Kurz: v tomto kurzu se dozvíte, jak spojit data senzorů s daty předpovědi počasí z Azure Maps služby počasí pomocí Azure Notebooks (Python).'
author: walsehgal
ms.author: v-musehg
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 8f641640ff6cf4174e2e1374404d47fc0760f79f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979900"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Kurz: spojení dat senzorů s daty předpovědi počasí pomocí Azure Notebooks (Python)

Větrná energie je jedním z alternativních zdrojů energie pro fosilní paliva v boji proti změnám klimatu. Vzhledem k tomu, že samotný vítr není v souladu s povahou, je nutné, aby operátoři napájení větru sestavovat modely ML (Machine Learning) a předpovídat kapacitu větru, aby splnila požadavky na elektřinu a zajistila stabilitu mřížky. V tomto kurzu se naučíme, jak Azure Maps data předpovědi počasí kombinovat s ukázkovou datovou sadou umístění senzorů se čteními počasí. Data předpovědi počasí jsou požadována voláním Azure Maps služby počasí.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Pracujte s datovými soubory v [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) v cloudu.
> * Načíst ukázková data ze souboru
> * V Pythonu volejte Azure Maps rozhraní REST API.
> * Vykreslení dat umístění na mapě.
> * Obohacení ukázkových dat o data počasí v Azure Maps [denní předpovědi](https://aka.ms/AzureMapsWeatherDailyForecast) .
> * Vykreslí data prognózy v grafech.


## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu je nutné nejprve provést tyto kroky:

1. Vytvořte předplatné účtu Azure Maps v cenové úrovni S0 podle pokynů v tématu [Správa účtu Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account).
2. Získejte primární klíč předplatného pro váš účet, postupujte podle pokynů v části [získání primárního klíče pro váš účet](./tutorial-search-location.md#getkey).

Chcete-li se seznámit s poznámkovým blokům Azure a vědět, jak začít, postupujte podle pokynů v tématu [vytvoření poznámkového bloku Azure](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook).

> [!Note]
> Soubor poznámkového bloku Jupyter pro tento projekt se dá stáhnout z [úložiště poznámkových bloků Jupyter mapy počasí](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Načíst požadované moduly a architektury

Pokud chcete načíst všechny požadované moduly a architektury, spusťte následující skript:

```python
import aiohttp
import pandas as pd
import datetime
from IPython.display import Image, display
```

## <a name="import-weather-data"></a>Importovat data o počasí

Pro účely tohoto kurzu budeme používat data o počasí ze senzorů nainstalovaných ve čtyřech různých větrných turbínách. Ukázková data se skládají z 30 dnů od čtení počasí shromážděných z datových center s počasí v blízkosti každého turbínového umístění. Ukázková data obsahují čtená data pro teplotu, rychlost větru a směr. Ukázková data si můžete stáhnout [tady](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). Skript níže importuje ukázková data do poznámkového bloku Azure.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Požadovat data denní předpovědi

V našem ukázkovém scénáři chceme pro každé umístění snímače požádat o denní předpověď. Následující skript volá [rozhraní API denní předpovědi](https://aka.ms/AzureMapsWeatherDailyForecast) služby Azure Maps počasí, aby získala každodenní předpověď počasí pro každou větrnou turbínu po dobu příštích 15 dní od aktuálního data.


```python
subscription_key = "Your Azure Maps primary subscription key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

Skript níže vykreslí umístění turbín na mapě voláním Azure Maps [získat image mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage).

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Umístění turbíny](./media/weather-service-tutorial/location-map.png)


Aby bylo možné rozšířit ukázková data daty předpovědi, Seskupujte data předpovědi s ukázkovými daty na základě ID stanice pro datové centrum počasí.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

V následující tabulce jsou uvedena kombinovaná data historických a předpovědí pro jedno z míst pro turbíny.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![seskupených dat](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Vykreslovat data předpovědi

Aby bylo možné zjistit, jak se rychlost a směr větru mění v průběhu následujících 15 dnů, vyhodnotí se hodnoty prognózy oproti dnům, pro které jsou předpovědi.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

Níže uvedené grafy vizualizují data předpovědi pro změnu rychlosti větru (levý graf) a směr (vpravo graf) v následujících 15 dnech od aktuálního dne.

<center>

![rychlost větru](./media/weather-service-tutorial/speed-date-plot.png) ![směrového grafu větru](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak volat Azure Maps rozhraní REST API, abyste získali data předpovědi počasí a vizualizujete data v grafech.

Další informace o tom, jak volat Azure Maps rozhraní REST API v Azure Notebooks, najdete v tématu [Směrování EV pomocí Azure Notebooks](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing).

Pokud chcete prozkoumat rozhraní API Azure Maps používaná v tomto kurzu, přečtěte si téma:

* [Denní prognóza](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Vykreslení – získat obrázek mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Úplný seznam Azure Maps rozhraní REST API najdete v tématu [Azure Maps REST API](https://docs.microsoft.com/azure/azure-maps/#reference).

Další informace o Azure Notebooks najdete v tématu [Azure Notebooks](https://docs.microsoft.com/azure/notebooks).
