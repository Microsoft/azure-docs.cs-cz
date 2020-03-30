---
title: Hrubá relokace
description: Přečtěte si o použití hrubé relokace k nalezení kotev ve vašem okolí.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844390"
---
# <a name="coarse-relocalization"></a>Přibližná relokalizace

Hrubá relokalizace je funkce, která poskytuje počáteční odpověď na otázku: *Kde je moje zařízení nyní / Jaký obsah bych měl pozorovat?* Odpověď není přesná, ale místo toho je ve formě: *Jste blízko k těmto kotvy; zkuste najít jeden z nich*.

Hrubá relokace funguje tak, že slokušuje různé hodnoty snímačů na zařízení jak s vytvářením, tak s dotazováním kotev. U venkovních scénářů jsou data ze senzorů obvykle poloha zařízení GPS (Global Positioning System). Pokud gps není k dispozici nebo nespolehlivé (například uvnitř), data ze senzorů se skládá z přístupových bodů WiFi a bluetooth majáků v dosahu. Všechna shromážděná data ze senzorů přispívají k udržování prostorového indexu, který používají prostorové kotvy Azure k rychlému určení kotev, které jsou v rámci přibližně 100 metrů od vašeho zařízení.

Rychlé vyhledávání kotev umožněné hrubou relokací zjednodušuje vývoj aplikací podpořených světovými kolekcemi (řekněme miliony geodistribuovaných) kotev. Složitost správy ukotvení je skrytá, což vám umožní více se zaměřit na vaši úžasnou aplikační logiku. Všechny kotvy zvedání těžkých břemen pro vás provádí na pozadí azure prostorové kotvy.

## <a name="collected-sensor-data"></a>Shromážděná data ze senzorů

Data ze senzorů, která můžete odeslat do kotvy, jsou jedny z následujících:

* Poloha GPS: zeměpisná šířka, délka, nadmořská výška.
* Síla signálu přístupových bodů WiFi v dosahu.
* Síla signálu bluetooth majáků v dosahu.

Obecně platí, že vaše aplikace bude muset získat oprávnění pro konkrétní zařízení pro přístup k datům GPS, WiFi nebo BLE. Navíc některá výše uvedená data senzoru nejsou na některých platformách k dispozici záměrně. Zohledňuje tecepo tyto situace, shromažďování dat ze senzorů je volitelné a je ve výchozím nastavení vypnuto.

## <a name="set-up-the-sensor-data-collection"></a>Nastavení sběru dat senzorů

Začněme vytvořením poskytovatele otisků prstů senzoru a invědomíme relaci:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Dále se budete muset rozhodnout, které senzory chcete použít pro hrubou relokalizaci. Toto rozhodnutí je specifické pro aplikaci, kterou vyvíjíte, ale doporučení v následující tabulce by vám měla poskytnout dobrý výchozí bod:


|             | Interiéru | Venku |
|-------------|---------|----------|
| Gps         | Vypnuto | Zapnuto |
| Wifi        | Zapnuto | Zapnuto (volitelné) |
| Ble majáky | Zapnuto (volitelné s upozorněními, viz níže) | Vypnuto |


### <a name="enabling-gps"></a>Povolení GPS

Za předpokladu, že vaše aplikace již má oprávnění k přístupu k poloze GPS zařízení, můžete nakonfigurovat Azure Prostorové kotvy používat:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

Při použití gps v aplikaci, mějte na paměti, že údaje poskytované hardwarem jsou obvykle:

* asynchronní a nízká frekvence (méně než 1 Hz).
* nespolehlivé / hlučné (v průměru 7-m směrodatná odchylka).

Obecně platí, že operační systém zařízení a azure prostorové kotvy bude dělat některé filtrování a extrapolace na nezpracovaný signál GPS ve snaze zmírnit tyto problémy. Toto extra zpracování vyžaduje další čas pro konvergenci, takže pro dosažení nejlepších výsledků byste se měli pokusit:

* vytvořit jednoho poskytovatele otisků prstů senzoru co nejdříve ve vaší aplikaci
* udržování poskytovatele otisků prstů senzoru naživu mezi více relacemi
* sdílení poskytovatele otisků prstů senzoru mezi více relacemi

Pokud plánujete použít poskytovatele otisků prstů senzoru mimo relaci kotvy, ujistěte se, že jste jej spustili před vyžádáním odhadů senzoru. Například následující kód se postará o aktualizaci polohy vašeho zařízení na mapě v reálném čase:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>Povolení WiFi

Za předpokladu, že vaše aplikace už má oprávnění k přístupu ke stavu Wi-Fi zařízení, můžete nakonfigurovat Azure Spatial Anchors, aby ji používali:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

Při používání Wi-Fi v aplikaci, mějte na paměti, že hodnoty poskytované hardwarem jsou obvykle:

* asynchronní a nízká frekvence (méně než 0,1 Hz).
* potenciálně omezena na úrovni operačního operačního se tu.
* nespolehlivé / hlučné (v průměru 3-dBm směrodatná odchylka).

Azure Spatial Anchors se pokusí vytvořit filtrované mapy síly signálu WiFi během relace ve snaze zmírnit tyto problémy. Pro dosažení nejlepších výsledků byste se měli pokusit:

* před umístěním první kotvy dobře vytvořte.
* udržujte relaci naživu tak dlouho, jak je to možné (to znamená, že vytvořit všechny kotvy a dotaz v jedné relaci).

### <a name="enabling-bluetooth-beacons"></a>Povolení bluetooth majáků

Za předpokladu, že vaše aplikace již má oprávnění k přístupu ke stavu Bluetooth zařízení, můžete nakonfigurovat Azure Spatial Anchors, aby ji používali:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

Majáky jsou typicky univerzální zařízení, kde lze konfigurovat vše - včetně UUID a MAC adres. Tato flexibilita může být problematická pro azure prostorové kotvy, protože považuje majáky jednoznačně identifikovat jejich UUID. Pokud se nepodaří zajistit tuto jedinečnost, nejpravděpodobněji způsobí prostorové červí díry. Pro dosažení nejlepších výsledků byste měli:

* přiřadit jedinečné UUID s vašimi majáky.
* nasazovat – obvykle v pravidelném vzoru, například v mřížce.
* předat seznam unikátních uuid majáků poskytovateli otisků prstů snímače:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Azure Spatial Anchors bude sledovat jenom bluetooth majáky, které jsou v seznamu známých uuid signálu. Škodlivé majáky naprogramované tak, aby umožnily uuidy uvedené na seznamu, mohou mít stále negativní dopad na kvalitu služby. Z tohoto důvodu byste měli používat majáky pouze v kurátorských prostorech, kde můžete řídit jejich nasazení.

## <a name="querying-with-sensor-data"></a>Dotazování s daty ze senzorů

Jakmile vytvoříte kotvy s přidruženými daty senzorů, můžete je začít načítat pomocí údajů ze senzorů nahlášených zařízením. Již nemusíte poskytovat službu seznam známých kotev, které očekáváte , že najdete - místo toho stačí, abyste službu informovali o poloze vašeho zařízení, jak je nahlášeno jeho palubními senzory. Azure Prostorové kotvy pak zjistí sadu kotvy v blízkosti vašeho zařízení a pokusí se vizuálně odpovídat jim.

Chcete-li, aby dotazy používaly data ze senzorů, začněte vytvořením kritérií "blízkého zařízení":

# <a name="c"></a>[C #](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

Parametr `DistanceInMeters` určuje, jak daleko budeme zkoumat kotva grafu načíst obsah. Předpokládejme například, že jste naplnili nějaký prostor kotvami s konstantní hustotou 2 každý metr. Kromě toho kamera na vašem zařízení pozoruje jednu kotvu a služba ji úspěšně lokalizovala. S největší pravděpodobností máte zájem získat všechny kotvy, které jste umístili v blízkosti, spíše než jedinou kotvu, kterou právě pozorujete. Za předpokladu, že kotvy, které jste umístili, jsou připojeny v grafu, může služba načíst všechny blízké kotvy za vás pomocí následujících hran v grafu. Množství grafu traversal udělat je `DistanceInMeters`řízen ; budete mít všechny kotvy připojené k jedné, kterou jste lokalizovali, které jsou blíže než `DistanceInMeters`.

Mějte na paměti, `MaxResultCount` že velké hodnoty pro může negativně ovlivnit výkon. Nastavte ji na rozumnou hodnotu pro vaši aplikaci.

Nakonec budete muset říct relaci, aby použila vyhledávání založené na senzoru:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Očekávané výsledky

Zařízení GPS na spotřebitelské úrovni jsou obvykle poměrně nepřesná. Studie [Zandenbergen a Barbeau (2011)][6] uvádí, že střední přesnost mobilních telefonů s asistovanou GPS (A-GPS) je kolem 7 metrů - poměrně velká hodnota, kterou je třeba ignorovat! Aby se zohlednily tyto chyby měření, služba zachází s kotvami jako s rozdělením pravděpodobnosti v prostoru GPS. Jako takový, kotva je nyní oblast prostoru, který s největší pravděpodobností (to znamená, s více než 95% spolehlivost) obsahuje jeho skutečné, neznámé GPS pozici.

Stejné uvažování se použije při dotazování pomocí GPS. Zařízení je reprezentováno jako další prostorová oblast spolehlivosti kolem jeho skutečné, neznámé polohy GPS. Objevování blízkých kotev se promítá do pouhého nalezení kotev s oblastmi důvěry *dostatečně blízkými* oblasti spolehlivosti zařízení, jak je znázorněno na obrázku níže:

![Výběr kotevních kandidátů s GPS](media/coarse-reloc-gps-separation-distance.png)

Přesnost signálu GPS, a to jak na vytvoření kotvy, tak při dotazech, má velký vliv na sadu vrácených kotev. Naproti tomu dotazy založené na WiFi / majáky zváží všechny kotvy, které mají alespoň jeden přístupový bod / maják společné s dotazem. V tomto smyslu je výsledek dotazu založeného na WiFi / majáky většinou určen fyzickým rozsahem přístupových bodů / majáků a environmentálními překážkami.

Níže uvedená tabulka odhaduje očekávaný vyhledávací prostor pro každý typ senzoru:

| Senzor      | Hledat poloměr prostoru (cca) | Podrobnosti |
|-------------|:-------:|---------|
| Gps         | 20 m - 30 m | Určuje nejistota GPS mimo jiné faktory. Hlášená čísla jsou odhadnuta pro střední přesnost GPS mobilních telefonů s A-GPS, to je 7 metrů. |
| Wifi        | 50 m - 100 m | Určeno rozsahem bezdrátových přístupových bodů. Záleží na frekvenci, síle vysílače, fyzických překážkách, rušení a tak dále. |
| Ble majáky |  70 m | Určeno dosahem majáku. Záleží na frekvenci, síle přenosu, fyzických překážkách, rušení a tak dále. |

## <a name="per-platform-support"></a>Podpora pro platformu

Následující tabulka shrnuje data ze senzorů shromážděná na každé z podporovaných platforem spolu se všemi upozorněními specifickými pro platformu:


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| Gps         | Není dostupné. | Podporováno prostřednictvím zařízení API [LocationManager][3] (GPS i NETWORK) | Podporovaná prostřednictvím souborů API [CLLocationManager][4] |
| Wifi        | Podporováno rychlostí přibližně jednoho skenování každé 3 sekundy | Podporuje se. Počínaje úrovní rozhraní API 28 jsou skeny WiFi omezeny na 4 volání každé 2 minuty. Z Androidu 10 lze omezení zakázat v nabídce Nastavení vývojáře. Další informace naleznete v dokumentaci k [systému Android][5]. | Není k ničemu - žádné veřejné ROZHRANÍ API |
| Ble majáky | Omezeno na [Eddystone][1] a [iBeacon][2] | Omezeno na [Eddystone][1] a [iBeacon][2] | Omezeno na [Eddystone][1] a [iBeacon][2] |

## <a name="next-steps"></a>Další kroky

Použijte hrubou relokalizaci v aplikaci.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
