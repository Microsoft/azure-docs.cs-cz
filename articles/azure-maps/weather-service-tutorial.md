---
title: 'Kurz: Připojení dat senzorů s daty předpovědi počasí pomocí Poznámkových bloků (Pythonu) Azure | Mapy Microsoft Azure'
description: Tento kurz ukazuje, jak spojit data senzorů s daty předpovědi počasí z Meteorologické služby Microsoft Azure Maps pomocí Azure Notebooks(Python).
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e5292f5166e739264e9cf969480b70f415fcc75a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333495"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Kurz: Připojení dat senzorů s daty předpovědi počasí pomocí poznámkových bloků Azure (Python)

Větrná energie je jedním z alternativních zdrojů energie pro fosilní paliva v boji proti změně klimatu. Vzhledem k tomu, že vítr není ze své podstaty konzistentní, musí provozovatelé větrných elektráren vytvářet modely strojového učení (ML), aby předpověděli kapacitu větrné energie. Tato předpověď je nezbytná pro uspokojení poptávky po elektřině a zajištění stability sítě. V tomto kurzu se provedeme, jak azure maps data předpověď počasí je v kombinaci s ukázková data pro čtení počasí. Údaje o předpovědi počasí se požadují na telefonním čísle Azure Maps Weather service.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Pracujte s datovými soubory v [poznámkových blocích Azure](https://docs.microsoft.com/azure/notebooks) v cloudu.
> * Načtěte ukázková data ze souboru.
> * Volání Azure Maps REST ROZHRANÍ API v Pythonu.
> * Vykreslete údaje o poloze na mapě.
> * Obohaťte ukázková data o data počasí Azure Maps [Daily Forecast.](https://aka.ms/AzureMapsWeatherDailyForecast)
> * Vykreslete data prognózy v grafech.


## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento kurz, musíte nejprve:

1. Vytvořte předplatné účtu Azure Maps v cenové úrovni S0 podle pokynů v části [Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps).
2. Získejte primární klíč předplatného pro svůj účet, postupujte podle pokynů v [části získat primární klíč](quick-demo-map-app.md#get-the-primary-key-for-your-account).


Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](./how-to-manage-authentication.md).

Pokud se chcete seznámit s poznámkovými bloky Azure a zjistit, jak začít, postupujte podle pokynů [Vytvořit poznámkový blok Azure](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook).

> [!Note]
> Soubor notebooku Jupyter pro tento projekt lze stáhnout z [úložiště notebooků Weather Maps Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Načíst požadované moduly a rámce

Chcete-li načíst všechny požadované moduly a architektury, spusťte následující skript:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Import dat počasí

V zájmu tohoto kurzu použijeme údaje o počasí ze senzorů instalovaných ve čtyřech různých větrných turbínách. Ukázková data se skládají z 30 dnů odečtů počasí. Tyto údaje jsou shromažďovány z datových center počasí v blízkosti každé lokality turbíny. Demo data obsahují údaje o teplotě, rychlosti větru a směru. Ukázková data si můžete stáhnout [zde](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). Skript níže importuje ukázková data do poznámkového bloku Azure.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Vyžádat si údaje o denní prognóze

V našem scénáři bychom chtěli požádat o denní předpověď pro každou polohu senzoru. Následující skript volá [rozhraní API denní prognózy](https://aka.ms/AzureMapsWeatherDailyForecast) meteorologické služby Azure Maps. Toto rozhraní API vrátí předpověď počasí pro každou větrnou turbínu pro dalších 15 dní od aktuálního data.


```python
subscription_key = "Your Azure Maps key"

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

Níže uvedený skript vykresluje umístění turbíny na mapě voláním [služby](https://docs.microsoft.com/rest/api/maps/render/getmapimage)Azure Maps Get Map Image .

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

![Umístění turbín](./media/weather-service-tutorial/location-map.png)


Data prognózy seskupili s ukázkovými daty na základě ID stanice. ID stanice je pro datové centrum počasí. Toto seskupení rozšíří ukázková data o data prognózy.

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

V následující tabulce jsou zobrazena kombinovaná historická data a data prognózy pro jedno z umístění turbíny.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Seskupená data](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Vykreslení dat prognózy

Vykreslíme předpovídané hodnoty podle dnů, pro které jsou prognózovány. Tento pozemek nám umožňuje vidět rychlost a směr změny větru pro příštích 15 dní.

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

Níže uvedené grafy vizualizují data prognózy. Změna rychlosti větru naleznete v levém grafu. Změna směru větru najdete v pravém grafu. Tato data jsou předpověď pro dalších 15 dnů ode dne, kdy jsou požadována data.

<center>

![Vykreslení](./media/weather-service-tutorial/speed-date-plot.png) ![rychlosti větru Vykreslení směru větru](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak volat Azure Maps REST API získat data předpověď počasí. Také jste se naučili, jak vizualizovat data v grafech.

Další informace o tom, jak volat rozhraní API Azure Maps REST v poznámkových blocích Azure, najdete v tématu [Směrování EV pomocí poznámkových bloků Azure](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing).

Chcete-li prozkoumat rozhraní API Mapy Azure, které se používají v tomto kurzu, najdete v tématu:

* [Denní předpověď](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Vykreslení – obrázek získat mapu](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Úplný seznam rozhraní API AZURE Maps REST najdete v [tématu Azure Maps REST API](https://docs.microsoft.com/azure/azure-maps/consumption-model).

Další informace o poznámkových blocích Azure najdete v [tématu Poznámkové bloky Azure](https://docs.microsoft.com/azure/notebooks).
