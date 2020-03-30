---
title: Integrace partnerských řešení pro snímky
description: Tento článek popisuje integraci partnerů snímků.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 62e5b363f8008380a61e24c0549573a30ecaeb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131854"
---
# <a name="imagery-partner-integration"></a>Integrace partnerských řešení pro snímky

Tento článek popisuje, jak používat komponentu Azure FarmBeats Translator k odesílání dat snímků do FarmBeats. Data zemědělských snímků lze generovat z různých zdrojů, jako jsou multispektrální kamery, satelity a drony. Partneři pro zemědělské snímky se mohou integrovat s FarmBeats a poskytnout zákazníkům mapy vytvořené na zakázku pro své farmy.

Data, jakmile jsou k dispozici, mohou být vizualizována prostřednictvím akcelerátoru FarmBeats a potenciálně použita pro fúzi dat a strojové učení / umělou inteligenci (ML / AI) model budovy zemědělskými podniky nebo integrátory zákaznického systému.

FarmBeats poskytuje možnost:

- Definujte vlastní typy obrazů, zdroj a formát souboru pomocí souborů /ExtendedType API.
- Ingestovat data snímků z různých zdrojů prostřednictvím /Scene a /SceneFile API.

Následující informace se zaměřuje na získání jakékoli formy snímků do systému FarmBeats.

Když vyberete sekci **Snímky drone,** otevře se vyskakovací okno, které zobrazí obraz ortomozaiky drone ve vysokém rozlišení. Můžete přistupovat k partnerskému softwaru, který pomáhá plánovat lety dronů a získat nezpracovaná data. Budete i nadále používat software partnera pro plánování cesty a ortomosaic prošívání obrazu.

Partneři dronů musí zákazníkům umožnit propojit svůj zákaznický účet s instancí FarmBeats v Azure.

Pro propojení FarmBeats musíte použít následující pověření v softwaru partnera pro drony:

- Koncový bod rozhraní API
- ID tenanta
- ID klienta
- Tajný klíč klienta

## <a name="api-development"></a>Vývoj rozhraní API

Api obsahují swagger technickou dokumentaci. Informace o api a odpovídající požadavky nebo odpovědi, naleznete v tématu [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="authentication"></a>Ověřování

FarmBeats používá [Službu](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) Microsoft Azure Active Directory (Azure AD). Služba Azure App Service poskytuje integrovanou podporu ověřování a autorizace. 

Další informace o Azure AD najdete v [tématu Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

FarmBeats Datahub používá ověřování nosiče, které vyžaduje následující pověření:

- ID klienta
- Tajný klíč klienta
- ID tenanta

Pomocí předchozích pověření může volající požádat o přístupový token, který je třeba odeslat v následujících požadavcích rozhraní API v části záhlaví, a to následovně:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Následující ukázka kódu Pythonu načte přístupový token. Potom můžete použít token pro následné volání rozhraní API farmbeats.

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```

## <a name="http-request-headers"></a>Hlavičky požadavků HTTP

Zde jsou nejčastější hlavičky požadavků, které je třeba zadat při volání rozhraní API farmbeats Datahub.

**Záhlaví** | **Popis a příklad**
--- | ---
Typ obsahu  | Formát požadavku (Content-Type:<format>application/ ). Pro FarmBeats Datahub API formát je JSON. Typ obsahu: aplikace/json
Autorizace | Určuje přístupový token potřebný k volání rozhraní API. Autorizace:> přístupového tokenu <nosiče
Accept  | Formát odpovědi. Pro FarmBeats Datahub API formát je JSON. Přijmout: aplikace/json


## <a name="api-requests"></a>Požadavky rozhraní API

Chcete-li vytvořit požadavek rozhraní REST API, zkombinujte:

- Metoda HTTP (GET, POST a PUT).
- Adresa URL služby rozhraní API.
- Identifikátor URI prostředku (dotaz, odeslání dat, aktualizace nebo odstranění).
- Jedna nebo více hlaviček požadavků HTTP.

Volitelně můžete zahrnout parametry dotazu na volání GET pro filtrování, omezení velikosti a řazení dat v odpovědích.

Následující ukázkový požadavek je získat seznam zařízení:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

Většina get, post a PUT volání vyžadují tělo požadavku JSON.

Následující ukázkový požadavek je vytvořit zařízení. Tato ukázka má vstupní JSON s tělo požadavku.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formát dat

JSON je společný jazykově nezávislý formát dat, který poskytuje jednoduchou textovou reprezentaci libovolných datových struktur. Další informace naleznete v [tématu JSON org](https://JSON.org).

## <a name="ingest-imagery-into-farmbeats"></a>Požití snímků do FarmBeats

Poté, co partner má pověření pro připojení k FarmBeats Datahub, partner provede následující kroky v translator komponenty.

1.  Vytvořte nový rozšířený typ pro následující pole v souladu s typem snímků, které mají být nahrány:

    - **Zdroj scény**: Například drone_partner_name
    - **Typ scény**: Například drone
    - **Typ souboru scény**: Například index chlorofylu
    - **Typ obsahu souboru scény**: Například obrázek/tiff

2.  Volání /Farms API získat seznam farem z v rámci systému Azure FarmBeats.
3.  Poskytněte zákazníkovi možnost vybrat si jednu farmu ze seznamu farem.

    Partnerský systém musí zobrazit farmu v rámci partnerského softwaru, aby mohl plánovat cestu a shromažďování letů a obrázků drone.

4.  Volání /Scene API a poskytnout požadované podrobnosti k vytvoření nové scény s jedinečným ID scény.
5.  Získejte adresu URL blob SAS a nahrajte požadované obrázky do FarmBeats Datahub v kontextu zvolené farmy v systému FarmBeats.

Tady je podrobný tok volání rozhraní API.

### <a name="step-1-extendedtype"></a>Krok 1: ExtendedType

Chcete-li zjistit, zda jsou typ a zdroj souboru k dispozici na FarmBeats, zkontrolujte rozhraní API /ExtendedType. Chcete-li tak učinit, volání GET na /ExtendedType ROZHRANÍ API.

Zde jsou systémem definované hodnoty:

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

Tento krok je jednorázové nastavení. Rozsah tohoto nového typu scény je omezen na předplatné, ve kterém je nainstalován Azure FarmBeats.

Chcete-li například přidat SceneSource: "SlantRange", proveďte PUT na ID rozhraní /ExtendedType API s klíčem "SceneSource" vstupní datové části.

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

Zelené pole je nový přírůstek do systémem definovaných zdrojových hodnot scény.

### <a name="step-2-get-farm-details"></a>Krok 2: Získání podrobností o farmě

Scény (.tiff nebo .csv soubory) jsou v kontextu farmy. Musíte získat podrobnosti farmy tím, že dělá GET na /Farm API. Rozhraní API vrátí seznam farem, které jsou k dispozici v FarmBeats. Můžete vybrat farmu, pro kterou chcete data ingestovat.

GET /Farm odpověď:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>Krok 3: Vytvoření ID scény (volání POST)

Vytvořte novou scénu (soubor .tiff nebo .csv) s danými informacemi, které poskytují datum, pořadí a ID farmy, ke kterým je scéna přidružena. Metadata přidružená ke scéně lze definovat pod vlastnostmi, které zahrnují dobu trvání a typ míry.

Vytvořením nové scény vytvoříte nové ID scény, které je přidruženo k farmě. Po vytvoření ID scény může uživatel použít stejný k vytvoření nového souboru (.tiff nebo .csv) a uložení obsahu souboru.

Příklad vstupní datové části pro volání POST v rozhraní /Scene API:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Odpověď rozhraní API:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**Vytvoření souboru scény**

ID scény vrácené v kroku 3 je vstupem pro soubor scény. Soubor scény vrátí token adresy URL SAS, který je platný po dobu 24 hodin.

Pokud uživatel vyžaduje programový způsob nahrávání datového proudu bitových kopií, lze k definování metody pomocí ID souboru scény, umístění a adresy URL sada objektů blob úložiště s udatnou metodou použít metodu.

Příklad vstupní datové části pro volání POST v rozhraní /SceneFile API:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Odpověď rozhraní API:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

Volání POST rozhraní /SceneFile API vrátí adresu URL pro nahrávání SAS, kterou lze použít k nahrání souboru .csv nebo .tiff pomocí klienta úložiště objektů blob Azure nebo knihovny.


## <a name="next-steps"></a>Další kroky

Další informace o podrobnostech integrace založené na rozhraní REST API naleznete v tématu [REST API](rest-api-in-azure-farmbeats.md).
