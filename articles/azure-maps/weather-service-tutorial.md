---
title: 'Kurz: spojení dat senzorů s daty předpovědi počasí pomocí Azure Notebooks (Python) s mapami Microsoft Azure'
description: V tomto kurzu se naučíte spojit data senzorů s daty předpovědi počasí z Microsoft Azure Maps služby počasí pomocí Azure Notebooks (Python).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 276dd5b7eba33081c5131eba722df91d8685adff
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678159"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Kurz: spojení dat senzorů s daty předpovědi počasí pomocí Azure Notebooks (Python)

> [!IMPORTANT]
> Služba Azure Maps počasí jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Větrná energie je jedním z alternativních zdrojů energie pro fosilní paliva v boji proti změnám klimatu. Vzhledem k tomu, že vítr není konzistentní vzhledem k povaze, je potřeba, aby operátoři napájení větru sestavili modely strojového učení (ML) pro předpověď kapacity výkonu větru. Tato předpověď je nutná k tomu, aby splňovala požadavky na elektřinu a zajistila stabilitu mřížky. V tomto kurzu se naučíme, jak Azure Maps data předpovědi počasí kombinovat s ukázkovými daty pro čtení počasí. Data předpovědi počasí se vyžadují voláním Azure Maps služby počasí (Preview).

V tomto kurzu:

> [!div class="checklist"]
> * Pracujte s datovými soubory v [Azure Notebooks](https://notebooks.azure.com) v cloudu.
> * Načíst ukázková data ze souboru
> * V Pythonu volejte Azure Maps rozhraní REST API.
> * Vykreslení dat umístění na mapě.
> * Obohacení ukázkových dat o data počasí v Azure Maps [denní předpovědi](/rest/api/maps/weather/getdailyforecastpreview) .
> * Vykreslí data prognózy v grafech.


## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu je nutné nejprve provést tyto kroky:

1. Vytvořte předplatné účtu Azure Maps v cenové úrovni S0 podle pokynů v části [Vytvoření účtu](quick-demo-map-app.md#create-an-azure-maps-account).
2. Získejte primární klíč předplatného pro váš účet, postupujte podle pokynů v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account).


Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

Chcete-li se seznámit s poznámkovým blokům Azure a vědět, jak začít, postupujte podle pokynů v tématu [vytvoření poznámkového bloku Azure](./tutorial-ev-routing.md#create-an-azure-notebooks-project).

> [!Note]
> Soubor poznámkového bloku Jupyter pro tento projekt se dá stáhnout z [úložiště map počasí Jupyter notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Načíst požadované moduly a architektury

Pokud chcete načíst všechny požadované moduly a architektury, spusťte následující skript:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Importovat data o počasí

Pro účely tohoto kurzu použijeme čtení dat počasí ze senzorů nainstalovaných ve čtyřech různých větrných turbínách. Ukázková data se skládají z 30 dnů od čtení počasí. Tato čtení se shromažďují z datových center počasí poblíž každého místa pro turbíny. Ukázková data obsahují čtená data pro teplotu, rychlost větru a směr. Ukázková data si můžete stáhnout [tady](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). Skript níže importuje ukázková data do poznámkového bloku Azure.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Požadovat data denní předpovědi

V našem scénáři chceme pro každé umístění senzoru požádat o denní předpověď. Následující skript volá [rozhraní API pro denní předpověď](/rest/api/maps/weather/getdailyforecastpreview) Azure Mapsch povětrnostních služeb (Preview). Toto rozhraní API vrátí předpověď počasí pro každou větrnou turbínu po dobu příštích 15 dní od aktuálního data.


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps Weather services (Preview) to get daily forecast data for 15 days from current date
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

Skript níže vykreslí umístění turbín na mapě voláním Azure Maps [získat image mapy](/rest/api/maps/render/getmapimage).

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


Data prognózy budeme seskupovat pomocí ukázkových dat na základě ID stanice. ID stanice je pro datové centrum počasí. Toto seskupení rozšiřuje ukázková data o data předpovědi.

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

![Seskupená data](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Vykreslovat data předpovědi

Hodnoty prognózy vyhodnotí za dny, pro které jsou předpovědi prognózy. Toto vykreslení nám umožňuje zobrazit rychlost a směrové změny větru po dobu příštích 15 dnů.

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

Níže uvedené grafy vizualizují data předpovědi. Změnu rychlosti větru najdete v levém grafu. Pro změnu ve směru větru si přečtěte v pravém grafu. Tato data jsou předpovědi následujících 15 dnů od dne, kdy jsou data požadována.

<center>

![Rychlost větru – sestrojení ](./media/weather-service-tutorial/speed-date-plot.png) ![ směru větru](./media/weather-service-tutorial/direction-date-plot.png)</center>

V tomto kurzu jste se naučili, jak volat Azure Maps rozhraní REST API, abyste získali data předpovědi počasí. Zjistili jste také, jak vizualizovat data v grafech.

Další informace o tom, jak volat Azure Maps rozhraní REST API v Azure Notebooks, najdete v tématu [Směrování EV pomocí Azure Notebooks](./tutorial-ev-routing.md).

Pokud chcete prozkoumat rozhraní API Azure Maps používaná v tomto kurzu, přečtěte si téma:

* [Denní prognóza](/rest/api/maps/weather/getdailyforecastpreview)
* [Vykreslení – získat obrázek mapy](/rest/api/maps/render/getmapimage)

Úplný seznam Azure Maps rozhraní REST API najdete v tématu [Azure Maps REST API](./consumption-model.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejsou k dispozici žádné prostředky, které vyžadují vyčištění.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Notebooks najdete v tématu.

> [!div class="nextstepaction"]
> [Azure Notebooks](https://notebooks.azure.com)