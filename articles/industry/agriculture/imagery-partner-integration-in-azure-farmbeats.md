---
title: Integrace partnerských řešení pro snímky
description: Tento článek popisuje integraci partnerských serverů z více snímků.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.custom: has-adal-ref
ms.openlocfilehash: 1bc723892e29ccc24a7612cfbe50d4b274cdacdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183790"
---
# <a name="imagery-partner-integration"></a>Integrace partnerských řešení pro snímky

Tento článek popisuje, jak pomocí komponenty překladače Azure FarmBeats posílat data z snímků do FarmBeats. Data ze zemědělských snímků je možné vygenerovat z různých zdrojů, jako jsou například spektrální kamery, satelity a DRONY zachraňují životy. Partneři zemědělských snímků se mohou integrovat s FarmBeats a poskytovat zákazníkům s vlastními mapami generovanými pro své farmy.

Data, jakmile jsou k dispozici, je možné vizuálně rozpracovat prostřednictvím akcelerátoru FarmBeats a potenciálně použít pro modelování dat a Machine Learning/umělá inteligentní funkce (ML/AI) podle zemědělských podniků nebo integrátorů systému zákazníka.

FarmBeats poskytuje možnosti:

- Pomocí rozhraní API/ExtendedType definujte vlastní typy obrázků, zdrojový soubor a formát souboru.
- Ingestování datových snímků z různých zdrojů prostřednictvím rozhraní API/Scene a/SceneFile

Následující informace se zaměřují na získání jakékoli formy snímků do systému FarmBeats.

Když vyberete část **pomocí dronů** , zobrazí se automaticky otevírané okno s obrázkem s vysokým rozlišením orthomosaicu pomocí dronů. Můžete získat přístup k partnerskému softwaru, který pomáhá plánovat pomocí dronů lety a získávat nezpracovaná data. Budete nadále používat software partnera pro plánování cest a orthomosaicí obrázků.

Partneři pomocí dronů musí zákazníkům povolit propojení svého zákaznického účtu s instancí FarmBeats na Azure.

K propojení FarmBeats musíte použít následující přihlašovací údaje v partnerském softwaru pomocí dronů:

- Koncový bod rozhraní API
- ID tenanta
- ID klienta
- Tajný klíč klienta

## <a name="api-development"></a>Vývoj rozhraní API

Rozhraní API obsahují technickou dokumentaci k Swagger. Informace o rozhraních API a odpovídajících požadavcích nebo odpovědích naleznete v části [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="authentication"></a>Authentication

FarmBeats používá Microsoft Azure [Active Directory](../../app-service/overview-authentication-authorization.md) (Azure AD). Azure App Service poskytuje integrovanou podporu ověřování a autorizace. 

Další informace o Azure AD najdete v tématu [Azure Active Directory](../../app-service/overview-authentication-authorization.md).   

FarmBeats DataHub používá ověřování pomocí nosiče, které potřebuje následující přihlašovací údaje:

- ID klienta
- Tajný klíč klienta
- ID tenanta

Pomocí předchozích přihlašovacích údajů může volající požádat o přístupový token, který se musí poslat v následujících požadavcích rozhraní API, a to následujícím způsobem:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Následující ukázka kódu Pythonu načte přístupový token. Pak můžete použít token pro následné volání rozhraní API do FarmBeats.

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```

## <a name="http-request-headers"></a>Hlavičky požadavku HTTP

Tady jsou nejběžnější hlavičky požadavků, které je potřeba zadat při volání rozhraní API FarmBeats DataHub.

**Hlavička** | **Popis a příklad**
--- | ---
Typ obsahu  | Formát požadavku (Content-Type: Application/ <format> ). Pro rozhraní FarmBeats DataHub API je formát JSON. Content-Type: Application/JSON
Autorizace | Určuje přístupový token potřebný k vytvoření volání rozhraní API. Autorizace: nosič <Access-Token>
Přijmout  | Formát odpovědi. Pro rozhraní FarmBeats DataHub API je formát JSON. Přijmout: Application/JSON


## <a name="api-requests"></a>Požadavky rozhraní API

Chcete-li vytvořit požadavek REST API, zkombinujete:

- Metoda HTTP (GET, POST a PUT).
- Adresa URL služby API.
- Identifikátor URI prostředku (pro dotazování, odesílání dat, aktualizaci nebo odstranění).
- Jedna nebo více hlaviček požadavku HTTP.

Volitelně můžete zahrnout parametry dotazu pro volání funkce GET k filtrování, omezení velikosti a řazení dat v odpovědích.

Následující ukázková žádost slouží k získání seznamu zařízení:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

Většina volání GET, POST a PUT vyžaduje tělo požadavku JSON.

Následující ukázková žádost slouží k vytvoření zařízení. Tato ukázka obsahuje vstupní JSON s textem žádosti.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"accept: application/json" -H
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formát dat

JSON je běžný jazyk nezávislý na jazyce, který poskytuje jednoduchou textovou reprezentaci libovolných datových struktur. Další informace najdete v tématu věnovaném nástroji [JSON org](https://JSON.org).

## <a name="ingest-imagery-into-farmbeats"></a>Ingestování snímků do FarmBeats

Po tom, co partner získá přihlašovací údaje pro připojení k FarmBeats DataHub, partner provede následující kroky v součásti překladatele.

1.  Vytvořte nový rozšířený typ pro následující pole v souladu s typem ukládaného textu, který se má nahrát:

    - **Zdroj scény**: například drone_partner_name
    - **Typ scény**: například pomocí dronů
    - **Typ souboru scény**: například index Chlorophyll
    - **Typ obsahu souboru scény**: například Image/TIFF

2.  Voláním rozhraní/Farms API získáte seznam farem v rámci systému Azure FarmBeats.
3.  Poskytněte zákazníkovi možnost zvolit jednu farmu ze seznamu Farm.

    Partnerský systém musí zobrazit farmu v rámci partnerského softwaru, aby bylo možné provádět plánování a pomocí dronů let a shromažďování imagí.

4.  Zavolejte rozhraní/Scene API a poskytněte požadované podrobnosti pro vytvoření nové scény s jedinečným ID scény.
5.  Přihlaste se k adrese URL SAS objektu BLOB pro nahrání požadovaných imagí do FarmBeats DataHub v kontextu zvolené farmy v systému FarmBeats.

Tady je podrobný tok volání rozhraní API.

### <a name="step-1-extendedtype"></a>Krok 1: ExtendedType

Vraťte se změnami rozhraní/ExtendedType API a zjistěte, jestli je typ a zdroj souborů k dispozici na FarmBeats. Provedete to tak, že zavoláte metodu GET na rozhraní/ExtendedType API.

Zde jsou hodnoty definované systémem:

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

Tento krok je jednorázové nastavení. Rozsah tohoto nového typu scény je omezený na předplatné, ve kterém je nainstalovaná aplikace Azure FarmBeats.

Například pro přidání SceneSource: "SlantRange", můžete provést vložení na ID rozhraní/ExtendedType API se vstupní datovou částí "SceneSource".

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

Zelené pole je nově přidané do systémových hodnot scény definovaných v systému.

### <a name="step-2-get-farm-details"></a>Krok 2: Získání podrobností o farmě

Scény (soubory. TIFF nebo. csv) jsou v kontextu farmy. Podrobnosti o farmě potřebujete získat na/Farm API. Rozhraní API vrátí seznam farem, které jsou k dispozici v FarmBeats. Můžete vybrat farmu, pro kterou chcete data ingestovat.

ZÍSKAT odpověď/Farm:

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

### <a name="step-3-create-a-scene-id-post-call"></a>Krok 3: vytvoření ID scény (po volání)

Vytvořte novou scénu (soubor. TIFF nebo. csv) s danými informacemi, které poskytují datum, sekvenci a ID farmy, se kterými je scéna přidružená. Metadata přidružená k scéně lze definovat v části vlastnosti, která zahrnuje dobu trvání a typ míry.

Při vytvoření nové scény se vytvoří nové ID scény, které je přidružené k farmě. Po vytvoření ID scény může uživatel použít stejný soubor k vytvoření nového souboru (. TIFF nebo. csv) a uložit obsah souboru.

Příklad vstupní datové části pro volání POST na rozhraní/Scene API:

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

**Vytvořit soubor scény**

ID scény vrácené v kroku 3 je vstup pro soubor scény. Soubor scény vrátí token URL SAS, který je platný po dobu 24 hodin.

Pokud uživatel vyžaduje programový způsob, jak nahrát datový proud imagí, je možné použít sadu SDK služby Blob Storage k definování metody pomocí ID souboru scény, umístění a adresy URL.

Příklad vstupní datové části pro volání POST na rozhraní/SceneFile API:

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

Po volání rozhraní/SceneFile API se vrátí adresa URL pro nahrání SAS, kterou můžete použít k nahrání souboru. csv nebo. TIFF pomocí klienta služby Azure Blob Storage nebo knihovny.


## <a name="next-steps"></a>Další kroky

Další informace o REST API podrobností integrace na základě REST API najdete v tématu [](rest-api-in-azure-farmbeats.md).