---
title: 'Kurz: Směrování elektromobilů pomocí poznámkových bloků Azure (Python) | Mapy Microsoft Azure'
description: Směrujte elektromobily pomocí rozhraní API směrování Microsoft Azure Maps a poznámkových bloků Azure.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3118ca39ec0efd42c9f7b622c91f857034ef4b03
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333840"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Kurz: Směrování elektromobilů pomocí poznámkových bloků Azure (Python)

Azure Maps je portfolio rozhraní API geoprostorové služby, která jsou nativně integrovaná do Azure. Tato rozhraní API umožňují vývojářům, podnikům a neistům nejste schopni vyvíjet aplikace pro zjišťování polohy, IoT, mobilitu, logistiku a řešení pro sledování majetku. 

Rozhraní API Azure Maps REST lze volat z jazyků, jako je Python a R, aby bylo možné povolit analýzu geoprostorových dat a scénáře strojového učení. Azure Maps nabízí robustní sadu [rozhraní API směrování,](https://docs.microsoft.com/rest/api/maps/route) které umožňují uživatelům vypočítat trasy mezi několika datovými body. Výpočty jsou založeny na různých podmínkách, jako je typ vozidla nebo dosažitelná oblast. 

V tomto tutoriálu budete chodit pomoci řidiči, jehož baterie elektrického vozidla je nízká. Řidič musí najít nejbližší možnou nabíjecí stanici z místa, kde vozidlo je.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Vytvořte a spusťte poznámkový blok Jupyter v [poznámkových blocích Azure](https://docs.microsoft.com/azure/notebooks) v cloudu.
> * Volání Azure Maps REST ROZHRANÍ API v Pythonu.
> * Vyhledejte dosažitelný dojezd založený na modelu spotřeby elektromobilu.
> * Hledejte nabíjecí stanice pro elektrická vozidla v dosahu, neboli isochrone.
> * Vykreslete hranici dosažitelného dosahu a nabíjecí stanice na mapě.
> * Najděte a vizualizujte trasu k nejbližší nabíjecí stanici pro elektromobily na základě doby jízdy.


## <a name="prerequisites"></a>Požadavky 

Chcete-li dokončit tento kurz, musíte nejprve vytvořit účet Azure Maps a získat primární klíč (klíč předplatného). 

Pokud chcete vytvořit předplatné účtu Azure Maps, postupujte podle pokynů v [aplikaci Create a account](quick-demo-map-app.md#create-an-account-with-azure-maps). Potřebujete předplatné účtu Azure Maps s cenovou úrovní S1. 

Chcete-li získat primární klíč předplatného pro svůj účet, postupujte podle pokynů v tématu [získat primární klíč](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](./how-to-manage-authentication.md).

## <a name="create-an-azure-notebook"></a>Vytvoření poznámkového bloku Azure

Chcete-li sledovat spolu s tímto kurzem, musíte vytvořit projekt poznámkového bloku Azure a stáhnout a spustit soubor poznámkového bloku Jupyter. Soubor poznámkového bloku obsahuje kód Pythonu, který implementuje scénář v tomto kurzu. Pokud chcete vytvořit projekt poznámkového bloku Azure a nahrát do něj dokument poznámkového bloku Jupyter, postupujte takto:

1. Přejděte na [Poznámkové bloky Azure](https://notebooks.azure.com) a přihlaste se. Další informace naleznete [v tématu Úvodní příručka: Přihlášení a nastavení ID uživatele](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
1. V horní části stránky veřejného profilu vyberte **Moje projekty**.

    ![Tlačítko Moje projekty](./media/tutorial-ev-routing/myproject.png)

1. Na stránce **Moje projekty** vyberte **Nový projekt**.
 
   ![Tlačítko Nový projekt](./media/tutorial-ev-routing/create-project.png)

1. V podokně **Vytvořit nový projekt** zadejte název projektu a ID projektu.
 
    ![Podokno Vytvořit nový projekt](./media/tutorial-ev-routing/create-project-window.png)

1. Vyberte **Vytvořit**.

1. Po vytvoření projektu si stáhněte tento [soubor dokumentu poznámkového bloku Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) z [úložiště poznámkových bloků Azure Maps Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook).

1. V seznamu projekty na stránce **Moje projekty** vyberte projekt a pak vyberte **Nahrát,** chcete-li nahrát soubor dokumentu poznámkového bloku Jupyter. 

    ![nahrát poznámkový blok](./media/tutorial-ev-routing/upload-notebook.png)

1. Nahrajte soubor z počítače a pak vyberte **Hotovo**.

1. Po úspěšném dokončení nahrávání se soubor zobrazí na stránce projektu. Poklepáním na soubor jej otevřete jako poznámkový blok Jupyter.

Pokuste se pochopit funkce, které jsou implementovány v souboru poznámkového bloku. Spusťte kód v souboru poznámkového bloku po jedné buňce. Kód v každé buňce můžete spustit tak, že v horní části aplikace poznámkového bloku vyberete tlačítko **Spustit.**

  ![Tlačítko Spustit](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Instalace balíčků na úrovni projektu

Chcete-li spustit kód v poznámkovém bloku, nainstalujte balíčky na úrovni projektu následujícím způsobem:

1. Stáhněte soubor [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) z [úložiště poznámkových bloků Azure Maps Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)a nahrajte ho do projektu.
1. Na řídicím panelu projektu vyberte **Nastavení projektu**. 
1. V podokně **Nastavení projektu** vyberte kartu **Prostředí** a pak vyberte **Přidat**.
1. V části **Kroky instalace prostředí**postupujte takto:   
    a. V prvním rozevíracím seznamu vyberte **položku Requirements.txt**.  
    b. V druhém rozevíracím seznamu vyberte soubor *requirements.txt.*  
    c. Ve třetím rozevíracím seznamu vyberte jako verzi **Python verze 3.6.**
1. Vyberte **Uložit**.

    ![Instalace balíčků](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Načíst požadované moduly a rámce

Chcete-li načíst všechny požadované moduly a architektury, spusťte následující skript.

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Vyžádejte si hranici dosažitelného dosahu

Zásilková společnost má ve svém vozovém parku několik elektrických vozidel. Během dne je třeba elektromobily dobíjet, aniž by se museli vracet do skladu. Pokaždé, když zbývající poplatek klesne na méně než hodinu, vyhledáte sadu nabíjecích stanic, které jsou v dosahu. V podstatě hledáte nabíjecí stanici, když je baterie vybitá. A získáte informace o hranicích pro tento rozsah nabíjecích stanic. 

Vzhledem k tomu, že společnost upřednostňuje použití tras, které vyžadují rovnováhu mezi hospodárností a rychlostí, je požadovaná trasa Type *ekologická*. Následující skript volá [rozhraní API get route range](https://docs.microsoft.com/rest/api/maps/route/getrouterange) směrovací služby Azure Maps. Používá parametry pro model spotřeby vozidla. Skript pak analyzuje odpověď k vytvoření polygonového objektu ve formátu geojson, který představuje maximální dosažitelný rozsah vozu.

Chcete-li určit hranice dosažitelného rozsahu elektromobilu, spusťte skript v následující buňce:

```python
subscriptionKey = "Your Azure Maps key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Vyhledávání nabíjecích stanic pro elektrická vozidla v dosahu

Poté, co určíte dosažitelný dojezd (isochrone) pro elektrické vozidlo, můžete vyhledávat nabíjecí stanice v tomto rozsahu. 

Následující skript volá azure maps [post search inside geometry API](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry). Hledá nabíjecí stanice pro elektrická vozidla, v rámci hranic maximálního dosažitelného dojezdu vozu. Potom skript analyzuje odpověď na pole dosažitelných umístění.

Chcete-li vyhledat nabíjecí stanice pro elektrická vozidla v dosahu, spusťte následující skript:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Nahrání dostupného rozsahu a dobíjecích stanic do datové služby Azure Maps

Na mapě budete chtít vizualizovat nabíjecí stanice a hranice pro maximální dosažitelný dosah elektromobilu. Chcete-li tak učinit, nahrajte data hraničních dat a nabíjecích stanic jako geojson objekty do datové služby Azure Maps. Použijte [rozhraní API pro nahrávání dat](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Chcete-li nahrát data hranic a nabíjecích bodů do datové služby Azure Maps, spusťte následující dva buňky:

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data Service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data Service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Vykreslujte nabíjecí stanice a dosažitelný dosah na mapě

Po nahrání dat do datové služby volejte [službu](https://docs.microsoft.com/rest/api/maps/render/getmapimage)Azure Maps Get Map Image . Tato služba se používá k vykreslení nabíjecích bodů a maximální dosažitelné hranice na statickémapě spuštěním následujícího skriptu:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![Mapa znázorňující rozsah polohy](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Najděte optimální nabíjecí stanici

Nejprve chcete určit všechny potenciální nabíjecí stanice v dosahu. Pak chcete vědět, který z nich může být dosaženo v minimálním množství času. 

Následující skript volá rozhraní AZURE Maps [Matrix Routing API](https://docs.microsoft.com/rest/api/maps/route/postroutematrix). Vrátí určené místo vozidla, dobu jízdy a vzdálenost ke každé nabíjecí stanici. Skript v další buňce analyzuje odpověď na umístění nejbližší dosažitelné nabíjecí stanice s ohledem na čas.

Chcete-li najít nejbližší dosažitelnou nabíjecí stanici, které lze dosáhnout v co nejmenším čase, spusťte skript v následující buňce:

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Výpočet trasy k nejbližší nabíjecí stanici

Nyní, když jste našli nejbližší nabíjecí stanici, můžete zavolat [na rozhraní Get Route Directions API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) a požádat o podrobnou trasu z aktuální polohy elektromobilu do nabíjecí stanice.

Chcete-li získat trasu k nabíjecí stanici a analyzovat odezvu k vytvoření geojsonobjekt, který představuje trasu, spusťte skript v následující buňce:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Vizualizace trasy

Chcete-li pomoci vizualizovat trasu, nejprve nahrajte data trasy jako geojson objekt do služby Azure Maps Data Service. Chcete-li tak učinit, použijte rozhraní AZURE Maps [Data Upload API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Potom zavolejte vykreslovací službu [Get Map Image API](https://docs.microsoft.com/rest/api/maps/render/getmapimage), vykreslujte trasu na mapě a vizualizujte ji.

Chcete-li získat obrázek pro vykreslenou trasu na mapě, spusťte následující skript:

```python
# Upload the route data to Azure Maps Data Service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Mapa zobrazující trasu](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili volat rozhraní API AZURE Maps REST přímo a vizualizovat data Azure Maps pomocí Pythonu.

Chcete-li prozkoumat rozhraní API Mapy Azure, které se používají v tomto kurzu, najdete v tématu:

* [Získat rozsah tras](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Post Search Uvnitř geometrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Nahrávání dat](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Vykreslení – obrázek získat mapu](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Matice postupu příspěvku](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)
* [Získat trasu trasy](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Úplný seznam rozhraní API AZURE Maps REST najdete v [tématu Azure Maps REST API](https://docs.microsoft.com/azure/azure-maps/consumption-model).

Další informace o poznámkových blocích Azure najdete v [tématu Poznámkové bloky Azure](https://docs.microsoft.com/azure/notebooks).
