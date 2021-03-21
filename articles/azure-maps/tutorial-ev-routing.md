---
title: 'Kurz: směrování elektrických vozidel pomocí Azure Notebooks (Python) s mapami Microsoft Azure'
description: Kurz týkající se směrování elektrických vozidel pomocí rozhraní API pro směrování v Microsoft Azure Maps a Azure Notebooks
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7341d1f07e8814edcad7b84f6b3b46c7bece3159
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98680328"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Kurz: směrování elektrických vozidel pomocí Azure Notebooks (Python)

Azure Maps je portfolio rozhraní API pro geoprostorové služby, která jsou nativně integrovaná do Azure. Tato rozhraní API umožňují vývojářům, podnikům a nezávislým výrobcům softwaru vyvíjet řešení zaměřená na umístění, řešení IoT, mobility, logistiky a sledování prostředků. 

Rozhraní REST API pro Azure Maps se dají volat z jazyků, jako je Python a R, aby se povolily scénáře geoprostorového testování dat a strojového učení. Azure Maps nabízí robustní sadu [rozhraní API pro směrování](/rest/api/maps/route) , která uživatelům umožňují vypočítat trasy mezi několika datovými body. Výpočty jsou založené na různých podmínkách, jako je typ vozidla nebo dosažitelná oblast. 

V tomto kurzu provedete pomoc s ovladačem, jehož baterie elektrického vozidla je nízká. Ovladač potřebuje najít nejbližší možnou stanici zpoplatnění z umístění vozidla.

V tomto kurzu:

> [!div class="checklist"]
> * Vytvořte a spusťte soubor Jupyter Notebook v [Azure Notebooks](https://notebooks.azure.com) v cloudu.
> * V Pythonu volejte Azure Maps rozhraní REST API.
> * Vyhledejte dosažitelný rozsah založený na modelu spotřeby elektrického vozidla.
> * Vyhledejte čerpací stanice elektrického vozidla v dosahu dostupného rozsahu nebo isochrone.
> * Vykreslete dosažitelný rozsah rozsahu a vyúčtování stanic na mapě.
> * Najděte a vizualizujte trasu k nejbližší elektrické stanici zpoplatnění elektrického vozidla na základě času na disku.


## <a name="prerequisites"></a>Předpoklady 

Abyste mohli tento kurz dokončit, musíte nejdřív vytvořit účet Azure Maps a získat primární klíč (klíč předplatného). 

Pokud chcete vytvořit předplatné účtu Azure Maps, postupujte podle pokynů v tématu [Vytvoření účtu](quick-demo-map-app.md#create-an-azure-maps-account). Potřebujete předplatné účtu Azure Maps s cenovou úrovní S1. 

Pokud chcete získat primární klíč předplatného pro svůj účet, postupujte podle pokynů v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

## <a name="create-an-azure-notebooks-project"></a>Vytvoření projektu Azure Notebooks

Chcete-li postupovat spolu s tímto kurzem, je třeba vytvořit projekt Azure Notebooks a stáhnout a spustit Jupyter Notebook soubor. Jupyter Notebook soubor obsahuje kód Pythonu, který implementuje scénář v tomto kurzu. Chcete-li vytvořit projekt Azure Notebooks a nahrát do něj dokument Jupyter Notebook, proveďte následující kroky:

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. Další informace najdete v tématu [rychlý Start: přihlášení a nastavení ID uživatele](https://notebooks.azure.com).
1. V horní části stránky veřejného profilu vyberte **Moje projekty**.

    ![Tlačítko Moje projekty](./media/tutorial-ev-routing/myproject.png)

1. Na stránce **Moje projekty** vyberte **Nový projekt**.
 
   ![Tlačítko Nový projekt](./media/tutorial-ev-routing/create-project.png)

1. V podokně **vytvořit nový projekt** zadejte název projektu a ID projektu.
 
    ![Podokno vytvořit nový projekt](./media/tutorial-ev-routing/create-project-window.png)

1. Vyberte **Vytvořit**.

1. Po vytvoření projektu Stáhněte tento [soubor dokumentu Jupyter notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) z [úložiště Azure Maps Jupyter notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook).

1. V seznamu projekty na stránce **Moje projekty** vyberte svůj projekt a pak vyberte **Odeslat** , aby se soubor dokumentu Jupyter notebook nahrál. 

    ![Odeslat Jupyter Notebook](./media/tutorial-ev-routing/upload-notebook.png)

1. Nahrajte soubor z počítače a potom vyberte **Hotovo**.

1. Po úspěšném dokončení nahrávání se Váš soubor zobrazí na stránce projektu. Dvakrát klikněte na soubor a otevřete ho jako Jupyter Notebook.

Zkuste pochopit funkčnost, která je implementovaná v souboru Jupyter Notebook. Spusťte kód v souboru Jupyter Notebook vždy po jedné buňce. Kód můžete v každé buňce spustit tak, že v horní části aplikace Jupyter Notebook vyberete tlačítko **Spustit** .

  ![Tlačítko Spustit](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Instalovat balíčky na úrovni projektu

Chcete-li spustit kód v Jupyter Notebook, nainstalujte balíčky na úrovni projektu pomocí následujících kroků:

1. Stáhněte soubor [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) z [úložiště Azure Maps Jupyter notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)a pak ho nahrajte do svého projektu.
1. Na řídicím panelu projekt vyberte **nastavení projektu**. 
1. V podokně **nastavení projektu** vyberte kartu **prostředí** a pak vyberte **Přidat**.
1. V části **nastavení prostředí** proveďte následující kroky:   
    a. V prvním rozevíracím seznamu vyberte možnost **Requirements.txt**.  
    b. V druhém rozevíracím seznamu vyberte soubor *requirements.txt* .  
    c. V třetím rozevíracím seznamu vyberte v části vaše verze **Python verze 3,6** .
1. Vyberte **Uložit**.

    ![Nainstalovat balíčky](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Načíst požadované moduly a architektury

Pokud chcete načíst všechny požadované moduly a architektury, spusťte následující skript.

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Vyžádat hranici dostupného rozsahu

Společnost pro doručování balíčků má ve svém loďstvu některá elektrická vozidla. V průběhu dne musí být elektrická vozidla znovu naúčtována bez nutnosti vracet se do skladu. Pokaždé, když se zbývající poplatek sníží na méně než hodinu, vyhledáte sadu zpoplatněných stanic, které jsou v dosahu dosažitelného rozsahu. V podstatě vyhledáváte stanoviště zpoplatnění při nízkém nabití baterie. A získáte informace o hranicích pro daný rozsah zpoplatněných stanic. 

Vzhledem k tomu, že se společnost upřednostňuje používat trasy, které vyžadují rovnováhu mezi ekonomicky a rychlostí, je požadovaná routeTypea *ekosystém*. Následující skript volá [rozhraní API rozsahu trasy](/rest/api/maps/route/getrouterange) služby Azure Maps Routing Service. Používá parametry pro model spotřeby vozidla. Skript pak analyzuje odpověď a vytvoří objekt mnohoúhelníku ve formátu injson, který představuje maximální dosažitelný rozsah auta.

Chcete-li určit hranice pro dostupný rozsah elektrického vozidla, spusťte skript v následující buňce:

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

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Hledání stanic pro zpoplatnění elektrického vozidla v dosahu dostupného rozsahu

Po určení dosažitelného rozsahu (isochrone) pro elektrické vozidlo můžete vyúčtovat stanice v daném rozsahu. 

Následující skript volá Azure Maps [po hledání uvnitř rozhraní API geometrie](/rest/api/maps/search/postsearchinsidegeometry). Vyhledává čerpací stanice pro elektrické vozidlo v rámci hranic maximálního dostupného rozsahu auta. Skript pak analyzuje odpověď na pole dosažitelných umístění.

Pokud chcete vyhledat stanice pro zpoplatnění elektrického vozidla v dosahu dostupného rozsahu, spusťte následující skript:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service-preview"></a>Nahrajte dostupný rozsah a vyúčtování bodů do služby Azure Maps data Service (Preview).

Na mapě budete chtít vizualizovat stanice zpoplatnění a hranici pro maximální dosažitelný rozsah elektrického vozidla. Provedete to tak, že do služby Data Service (Preview) nahrajete data hranic a vybíjíte data stanic jako své Azure Maps objekty. Použijte [rozhraní API pro nahrání dat](/rest/api/maps/data/uploadpreview). 

Chcete-li nahrát data hranice a bodu zpoplatnění do služby Azure Maps data Service, spusťte následující dvě buňky:

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

# Upload the range data to Azure Maps Data service (Preview).
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

# Upload the electric vehicle charging station data to Azure Maps Data service (Preview).
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

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Vykreslovat stanice zpoplatnění a dosažitelný rozsah na mapě

Po nahrání dat do datové služby volejte [službu Azure Maps získat image mapy](/rest/api/maps/render/getmapimage). Tato služba se používá k vykreslení bodů zpoplatnění a maximální dosažitelné hranice na statické imagi mapy spuštěním následujícího skriptu:

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

![Mapa znázorňující rozsah umístění](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Najít optimální stanici zpoplatnění

Nejdřív chcete určit všechny potenciální stanice zpoplatnění v dosažitelném rozsahu. Pak chcete zjistit, ke kterému z nich lze získat minimální čas. 

Následující skript volá [rozhraní API pro směrování matrice](/rest/api/maps/route/postroutematrix)Azure Maps. Vrátí zadané umístění vozidla, dobu trvání cesty a vzdálenost ke každé stanici zpoplatnění. Skript v další buňce analyzuje odpověď, aby vyhledal nejbližší dostupnou stanici zpoplatnění s ohledem na čas.

Pokud chcete najít nejbližší dosažitelnou stanici, ke které se dá získat přístup v nejmenším množství času, spusťte skript v následující buňce:

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

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Vypočítat trasu k nejbližší stanici zpoplatnění

Teď, když jste našli nejbližší stanici zpoplatnění, můžete zavolat [rozhraní API pro získání tras](/rest/api/maps/route/getroutedirections) k vyžádání podrobné trasy z aktuálního umístění elektrického vozidla na stanici zpoplatnění.

Chcete-li získat trasu k stanovišti zpoplatnění a analyzovat odpověď pro vytvoření objektu s koncovým JSON, který představuje trasu, spusťte skript v následující buňce:

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

Pro lepší vizualizaci trasy nejprve odešlete data směrování jako objekt pro zobrazení dat typu "injson" do služby Azure Maps data Service (Preview). K tomu použijte Azure Maps [rozhraní API pro nahrání dat](/rest/api/maps/data/uploadpreview). Pak zavolejte vykreslovací službu, [Získejte rozhraní API mapy](/rest/api/maps/render/getmapimage)pro vykreslení trasy na mapě a vizualizujte ji.

Chcete-li získat obrázek pro vykreslenou trasu na mapě, spusťte následující skript:

```python
# Upload the route data to Azure Maps Data service (Preview).
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

![Mapa ukazující trasu](./media/tutorial-ev-routing/route.png)

V tomto kurzu jste zjistili, jak volat Azure Maps rozhraní REST API přímo a vizualizovat Azure Maps data pomocí Pythonu.

Pokud chcete prozkoumat rozhraní API Azure Maps používaná v tomto kurzu, přečtěte si téma:

* [Získat rozsah tras](/rest/api/maps/route/getrouterange)
* [Po vyhledání v geometrii](/rest/api/maps/search/postsearchinsidegeometry)
* [Nahrávání dat](/rest/api/maps/data/uploadpreview)
* [Vykreslení – získat obrázek mapy](/rest/api/maps/render/getmapimage)
* [Vyjednaná matice směrování](/rest/api/maps/route/postroutematrix)
* [Získat směr směrování](/rest/api/maps/route/getroutedirections)
* [Rozhraní REST API pro Azure Maps](./consumption-model.md)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejsou k dispozici žádné prostředky, které vyžadují vyčištění.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Notebooks najdete v tématu.

> [!div class="nextstepaction"]
> [Azure Notebooks](https://notebooks.azure.com)