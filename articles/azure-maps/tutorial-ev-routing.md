---
title: Směrování elektrického vozidla pomocí Azure Notebooks (Python) | Microsoft Docs
description: Směrování EV pomocí Azure Maps rozhraní API směrování a Azure Notebooks.
author: walsehgal
ms.author: v-musehg
ms.date: 10/01/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: c4b46bc952782fc7c9b56d6f0c049fe17b63d0f2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836381"
---
# <a name="electric-vehicle-routing-using-azure-notebooks-python"></a>Směrování elektrického vozidla pomocí Azure Notebooks (Python)

Azure Maps je portfolio rozhraní API geoprostorové služby nativně integrovaných do Azure, které umožňuje vývojářům, podnikům a nezávislým výrobcům softwaru vytvářet aplikace s podporou polohy a řešení IoT, mobility, logistiky a sledování prostředků. Rozhraní REST API Azure Maps můžete volat z jazyků, jako je Python a R, a umožnit tak scénáře geoprostorového testování dat a strojového učení. Azure Maps nabízí robustní sadu [rozhraní API pro směrování](https://docs.microsoft.com/rest/api/maps/route) , která umožňuje uživatelům vypočítat trasy mezi několika datovými body na základě různých podmínek, jako je typ vozidla nebo dosažitelná oblast. V tomto kurzu Vás provedeme scénářem, který vám pomůže s řidičem elektrického vozidla, u kterého je nedostatek energie na baterii, aby bylo možné najít nejbližší možnou zpoplatněnou stanici s ohledem na dobu trvání jednotky.

V tomto kurzu provedete tyto kroky:

> [!div class="checklist"]
> * Vytvoření a spuštění Jupyter Notebook v [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) v cloudu
> * Volání rozhraní REST API Azure Maps v Pythonu
> * Vyhledejte dosažitelný rozsah založený na modelu spotřeby elektrického vozidla.
> * Vyhledejte elektrická stanoviště pro zpoplatnění vozidel v dosahu dostupného rozsahu (nebo isochrone).
> * Vykreslete dosažitelný rozsah rozsahu a vyúčtování stanic na mapě.
> * Najděte a vizualizujte trasu k nejbližší elektrické stanici zpoplatnění vozidel na základě času.


## <a name="prerequisites"></a>Požadavky 

Abyste mohli dokončit kroky v tomto kurzu, musíte nejdřív vytvořit účet Azure Maps a získat primární klíč (klíč předplatného). Podle pokynů v části [Správa účtu](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) Vytvořte předplatné účtu Azure Maps s cenovou úrovní S1 a podle kroků v části [získání primárního klíče](./tutorial-search-location.md#getkey) Získejte primární klíč předplatného pro váš účet.

## <a name="create-an-azure-notebook"></a>Vytvoření poznámkového bloku Azure

Aby bylo možné postupovat spolu s tímto kurzem, budete muset vytvořit projekt Azure notebook a stáhnout a spustit soubor poznámkového bloku Jupyter. Soubor poznámkového bloku obsahuje kód Pythonu, který implementuje scénář v tomto kurzu. Postupujte podle následujících kroků a vytvořte projekt Azure notebook a nahrajte do něj dokument poznámkového bloku Jupyter.

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. Další informace najdete v tématu [rychlý Start](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
2. Na stránce veřejný profil vyberte v horní části stránky možnost **Moje projekty** .

    ![Můj projekt](./media/tutorial-ev-routing/myproject.png)

3. Na stránce **Moje projekty** vyberte **Nový projekt**.
 
   ![Nový projekt](./media/tutorial-ev-routing/create-project.png)

4. V místní nabídce **vytvořit nový projekt** , která se zobrazí, zadejte následující informace a klikněte na **vytvořit**:
    * Název projektu
    * ID projektu
 
    ![vytvořit projekt](./media/tutorial-ev-routing/create-project-window.png)

5. Po vytvoření projektu si stáhněte [soubor dokumentu poznámkového bloku Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) z [úložiště Jupyter notebook Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook). 

6. Vyberte svůj projekt ze seznamu projekty na stránce **Moje projekty** a kliknutím na **Odeslat** uložte soubor dokumentu poznámkového bloku Jupyter. Nahrajte soubor z počítače a klikněte na **Hotovo**.

    ![nahrát Poznámkový blok](./media/tutorial-ev-routing/upload-notebook.png)

7. Po úspěšném nahrání se Váš soubor zobrazí na stránce projektu. Klikněte na soubor poznámkového bloku a otevřete ho jako Jupyter Notebook.

Abyste lépe pochopili funkčnost implementovaná v souboru poznámkového bloku, doporučujeme, abyste spustili kód v poznámkovém bloku vždy po jedné buňce. Můžete spustit kód v každé buňce kliknutím na tlačítko **Spustit** v horní části aplikace poznámkového bloku.

  ![spouštěl](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Instalovat balíčky na úrovni projektu

Aby bylo možné spustit kód v poznámkovém bloku, budete muset nainstalovat balíčky na úrovni projektu. Chcete-li nainstalovat požadované balíčky, postupujte podle následujících kroků:

1. Stáhněte si soubor ["požadavky. txt"](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) z [úložiště Azure Maps Jupyter notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) a nahrajte ho do projektu.
2. Na řídicím panelu projekt vyberte **nastavení projektu**. 
3. V místní nabídce, která se zobrazí, vyberte **kartu prostředí**a pak vyberte **Přidat**.
4. V části **Postup nastavení prostředí** 
    * V prvním rozevíracím seznamu vyberte možnost **požadavky. txt**.
    * V druhém ovládacím prvku rozevírací seznam vyberte soubor "požadavky. txt".
    * V třetím ovládacím prvku rozevírací seznam vyberte Python verze 3,6 jako verzi Pythonu.
7. Vyberte **Uložit**.

    ![instalovat balíčky](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-required-modules-and-frameworks"></a>Načíst požadované moduly a architektury

Spusťte následující skript, který načte všechny požadované moduly a rozhraní.

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-for-reachable-range-boundary"></a>Požadavek na hranici dosažitelného rozsahu

V našem scénáři má společnost pro doručování balíčků ve svém loďstvu některá elektrická vozidla. V průběhu dne musí být elektrická vozidla znovu naúčtována bez nutnosti vracet se do skladu. Pokaždé, když aktuální zbývající poplatek za elektrické vozidlo dosáhne méně než hodinu (elektrické vozidlo má nízké náklady), musíme vyhledat sadu zpoplatněných stanic, které jsou v dosažitelném rozsahu, a získat informace o hranicích pro tento rozsah. Vzhledem k tomu, že společnost upřednostňuje používat trasy vyvážené podle hospodárnosti a rychlosti, je požadovaná routeType "ekosystém". Následující skript volá [rozhraní API rozsahu Get Route](https://docs.microsoft.com/rest/api/maps/route/getrouterange) služby Azure Maps Routing Service s parametry pro model spotřeby vozidel a analyzuje odpověď pro vytvoření objektu mnohoúhelníku ve formátu injson, který představuje maximální dosažitelný rozsah auta. .

Spuštěním skriptu v následující buňce získáte meze pro dosažitelný rozsah elektrického vozidla.

```python
subscriptionKey = "Your Azure Maps primary subscription key"
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


# Get bounds for the electric vehicle's reachable range.
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

## <a name="search-electric-vehicle-charging-stations-within-reachable-range"></a>Prohledat stanice zpoplatněné elektrické vozidlo v dosažitelném rozsahu

Jakmile máme dostupný rozsah (isochrone) pro elektrické vozidlo, můžeme v tomto rozsahu vyhledat stanice zpoplatnění. Následující skript volá Azure Maps [po hledání uvnitř rozhraní API geometrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) , aby prohledal stanice pro zpoplatnění elektrického vozidla v mezích maximálního dosažitelného rozsahu automobilu a pak analyzuje odpověď na pole dosažitelných umístění.

Spusťte následující skript, který hledá stanice zpoplatnění elektrického vozidla v dosažitelném rozsahu.

```python
# Search for EV stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Nahrajte dostupný rozsah a vyúčtování bodů na službu Azure Maps data Service.

Aby bylo možné vizualizovat zpoplatněné stanice a hranice pro maximální dosažitelný rozsah elektrického vozidla na mapě, musíme nahrát data hranic a vysílat data stanic jako své objekty ve službě Azure Maps data Service pomocí [rozhraní API pro nahrání dat. ](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Spuštěním následujících dvou buněk nahrajte data hranice a bodu zpoplatnění do služby Azure Maps data Service.

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

# Upload range data to Azure Maps data service.
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

# Upload EV charging stations data to Azure Maps data service.
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

## <a name="render-charging-stations-and-reachable-range-on-map"></a>Vykreslování stanic pro zpoplatnění a dosažitelného rozsahu na mapě

Po nahrání dat do datové služby teď spustíte následující skript, který bude volat [službu Azure Maps získat image mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage) pro vykreslení bodů zpoplatnění a maximální dosažitelné hranice na statické imagi mapy.

```python
# Get bounds for bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
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

# Render range and EV charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![Rozsah umístění](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station-to-stop"></a>Najít optimální stanici zpoplatnění, která se má zastavit

Až budeme mít všechny potenciální stanice zpoplatnění v dosahu dostupného rozsahu, chceme zjistit, k jakému z nich se dá dostat v minimálním množství času. Následující skript volá [rozhraní API pro směrování Azure Maps matrice](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) , které vrací pro dané vozidlo místo cesty a vzdálenost do každé z těchto umístění stanoviště zpoplatnění. Skript v další buňce analyzuje odpověď a získá místo toho, aby se dosáhlo nejbližší stanice zpoplatnění, která je v souladu s časem.

Spusťte následující buňku, abyste našli nejbližší dostupnou stanici zpoplatnění, na kterou se dá dosáhnout v minimálním množství času.

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

# Get the travel time and distance to every given charging station location.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-route-to-the-closest-charging-station"></a>Vypočítat trasu k nejbližší stanici zpoplatnění

Teď, když jsme našli nejbližší stanici zpoplatnění, budeme volat [rozhraní API pro získání tras](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) , abychom pomohli podrobnou trasu z aktuálního umístění elektrického vozidla do stanice zpoplatnění.

Spusťte skript v následující buňce, který získá trasu, a analyzujte odpověď pro vytvoření objektu. JSON, který představuje trasu.

```python
# Get route from current location to the closest charging station. 
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

Aby bylo možné vizualizovat trasu, napřed do datové služby Azure Maps pomocí [rozhraní API Azure Maps pro nahrání](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)dat nahrajte údaje o trasách jako objekt pro zobrazení dat typu injson. A potom zavolejte službu vykreslování, [Získejte rozhraní API mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage) pro vykreslení trasy na mapě a vizualizujte ji.

Spuštěním následujícího skriptu získáte obrázek pro vykreslenou trasu na mapě.

```python
# Upload route data to Azure data service.
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


# Get bounds for bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

#Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![cestě](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak volat Azure Maps rozhraní REST API přímo a vizualizovat Azure Maps data pomocí Pythonu.

Pokud chcete prozkoumat rozhraní API Azure Maps použitá v tomto kurzu, přečtěte si:

* [Získat rozsah tras](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Po vyhledání v geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Nahrávání dat](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Vykreslení – získat obrázek mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Vyjednaná matice směrování](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)
* [Získat směr směrování](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Úplný seznam Azure Maps rozhraní REST API najdete v tématu:

* [Rozhraní REST API pro Azure Maps](https://docs.microsoft.com/azure/azure-maps/#reference)

Další informace o Azure Notebooks najdete v těchto tématech:

* [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)