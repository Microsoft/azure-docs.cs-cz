---
title: Integrace partnerských řešení pro snímky
description: Popisuje integraci partnerských serverů pro satelitní navýšení.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 788ffd9e7036996f6ac1bc7fcbc33137aca40ee2
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132020"
---
# <a name="imagery-partner-integration"></a>Integrace partnerských řešení pro snímky

Tento článek popisuje, jak pomocí komponenty překladače Azure FarmBeats posílat data z snímků do FarmBeats. Data ze zemědělských snímků můžou být z různých zdrojů, včetně více spektrálních kamer, satelitů a DRONY zachraňují životy. Partneři zemědělských snímků se mohou integrovat s FarmBeats a poskytovat zákazníkům s vlastními mapami generovanými pro své farmy.

Data, jakmile jsou k dispozici, je možné vizuálně rozpracovat prostřednictvím akcelerátoru FarmBeats a potenciálně použít pro modelování dat a (Machine Learning/umělá inteligentní funkce) na základě zemědělských podniků nebo integrátorů systému zákazníka.

FarmBeats poskytuje možnosti:

- Definování vlastních typů obrázků, zdrojů, formátu souborů pomocí rozšířených rozhraní API typu
- Ingestování dat snímků z různých zdrojů přes & rozhraní API SceneFile

Níže uvedené informace se zaměřují na získání jakékoli formy snímků do FarmBeats systému.

Když vyberete část pomocí dronů, zobrazí se automaticky otevírané okno s obrázkem s vysokým rozlišením orthomosaicu pomocí dronů. Můžete získat přístup k partnerskému softwaru, který pomáhá plánovat pomocí dronů lety a získávat nezpracovaná data. Budete nadále používat software partnera pro plánování cest a orthomosaicí obrázků.

Partneři pomocí dronů musí zákazníkům povolit, aby propojí svůj účet zákazníka s instancí FarmBeats na Azure.

V partnerském softwaru pomocí dronů je nutné použít následující přihlašovací údaje pro propojení FarmBeats:

- Koncový bod rozhraní API
- ID tenanta
- ID klienta
- Tajný kód klienta

## <a name="api-development"></a>Vývoj rozhraní API

Rozhraní API obsahují technickou dokumentaci k Swagger. Přečtěte si [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) , kde najdete informace o rozhraních API a odpovídajících požadavcích nebo odpovědích.

## <a name="authentication"></a>Ověřování

FarmBeats využívá [službu Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)Microsoft Azure. Azure App Service poskytuje integrovanou podporu ověřování a autorizace. 

Další informace o najdete v tématu [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

FarmBeats data hub používá ověřování pomocí nosiče, které potřebuje následující přihlašovací údaje:

- ID klienta
- Tajný kód klienta
- ID tenanta

Pomocí výše uvedených přihlašovacích údajů může volající požádat o přístupový token, který se musí poslat v následujících požadavcích rozhraní API, a to následujícím způsobem:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Níže je příklad kódu Pythonu, který načte přístupový token. Pak můžete použít token pro následné volání rozhraní API do FarmBeat:   
importovat Azure 

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

## <a name="http-request-headers"></a>Hlavičky požadavku HTTP

Tady jsou nejčastější hlavičky požadavků, které je potřeba zadat při volání rozhraní API FarmBeats data hub:

**Hlavička** | **Popis a příklad**
--- | ---
Typ obsahu  | Formát požadavku (Content-Type: Application/<format>) pro formát rozhraní API datového centra FarmBeats je JSON. Content-Type: Application/JSON
Autorizace | Určuje přístupový token potřebný k vytvoření volání rozhraní API. Autorizace: nosný < přístup-token >
Vyjádřit  | Formát odpovědi. Pro rozhraní API centra dat FarmBeats je formátem přijetí JSON: Application/JSON.


## <a name="api-requests"></a>Požadavky rozhraní API

Chcete-li vytvořit žádost o REST API, zkombinujete metodu HTTP (GET/POST/PUT), adresu URL služby API, identifikátor URI prostředku (pro dotazování, odesílání dat, aktualizaci nebo odstranění) a jednu nebo více hlaviček požadavku HTTP.

Volitelně můžete zahrnout parametry dotazu pro volání funkce GET k filtrování, omezení velikosti a řazení dat v odpovědích.

Níže uvedená ukázková žádost slouží k získání seznamu zařízení:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

Většina volání GET, POST a PUT vyžaduje tělo požadavku JSON.

Níže uvedená ukázková žádost slouží k vytvoření zařízení (obsahuje vstupní kód JSON s textem žádosti).


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formát dat

JSON (JavaScript Object Notation) je běžný jazyk nezávislý na jazyce, který poskytuje jednoduchou textovou reprezentaci libovolných datových struktur. Další informace najdete v tématu věnovaném nástroji [JSON org](https://JSON.org).

## <a name="ingesting-imagery-into-farmbeats"></a>Ingestování snímků do FarmBeats

Když má partner přihlašovací údaje pro připojení k datovému rozbočovači FarmBeats, partner v součásti translatoru provede toto:

1.  Vytvořte nový rozšířený typ pro následující pole v souladu s typem ukládaného textu, který se nahraje:

    - Zdroj scény: například < drone_partner_name >
    - Typ scény: například <drone>
    - Typ souboru scény: například <chlorophyll index>
    - Typ obsahu souboru scény: například < image/TIFF >

2.  Zavolejte rozhraní API pro farmy, abyste získali seznam farem v systému Azure FarmBeats.
3.  Poskytněte zákazníkovi možnost zvolit jednu farmu ze seznamu Farm.

    Partnerský systém musí zobrazit farmu v rámci partnerského softwaru, aby bylo možné provádět plánování a pomocí dronů let a shromažďování imagí.

4.  Zavolejte rozhraní API pro scény a poskytněte požadované podrobnosti pro vytvoření nové scény s jedinečným SceneID.
5.  Přijímají se adresa URL SAS objektu BLOB pro nahrání požadovaných imagí do FarmBeats datového centra v kontextu zvolené farmy do FarmBeats systému.

Tady je podrobný postup při volání rozhraní API:

### <a name="step-1-extendedtype"></a>Krok 1: ExtendedType

Pokud je typ a zdroj souborů k dispozici na FarmBeats, vraťte se v rozhraní ExtendedType API. To můžete provést voláním GET na rozhraní/ExtendedType API.

Hodnoty definované systémem jsou následující:

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

Jedná se o jednorázovou instalaci a rozsah této nové scenetype je omezený na předplatné, ve kterém je nasazený projekt FarmBeats.

Příklad: Chcete-li přidat SceneSource: "SlantRange", je třeba zadat ID/ExtendedType s klíčovým řetězcem: "SceneSource" vstupní datové části:

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

Zelené pole je nově přidané do systémových hodnot scény definovaných systémem.

### <a name="step-2-get-farmdetails"></a>Krok 2: získání FarmDetails

Scény (soubory TIFF nebo. csv) budou v kontextu farmy. Podrobnosti o farmě potřebujete získat pomocí rozhraní get na/Farm API. Rozhraní API vám vrátí seznam farem dostupných v FarmBeats a můžete vybrat farmu, pro kterou chcete data ingestovat.

Získat odpověď/Farm:

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

### <a name="step-3-create-ascene-id-post-call"></a>Krok 3: vytvoření ID a scény (po volání)

Vytvořte novou scénu (soubor TIFF nebo CSV) s danými informacemi a zadejte datum, sekvenci a ID farmy, se kterými bude scéna přidružena. Metadata přidružená k scéně lze definovat v části vlastnosti, včetně doby trvání a typu míry.

Tím se vytvoří nový SceneID, který bude přidružen k farmě. Po vytvoření SceneID může uživatel použít stejnou možnost k vytvoření nového souboru (TIFF nebo. csv) & Uložit obsah souboru.

Příklad vstupní datové části pro volání post v/scéně rozhraní API

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

**Vytvořit/SceneFile**

ID scény vrácené v kroku 3 je vstupem pro SceneFile. SceneFile vrátí token URL SAS, který je platný po dobu 24 hodin.

Pokud uživatel vyžaduje programový způsob, jak nahrát datový proud imagí, je možné použít sadu SDK služby Blob Storage k definování metody pomocí ID Scenefile, umístění & URL.

Příklad vstupní datové části pro volání post v rozhraní/Scenefile API:

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

Volání post rozhraní/SceneFile API vrátí adresu URL pro nahrání SAS, kterou můžete použít k nahrání souboru CSV nebo TIFF pomocí klienta nebo knihovny Azure Blob Storage.


## <a name="next-steps"></a>Další kroky

Další informace o integraci založené na rozhraní REST API najdete v tématu [REST API](references-for-farmbeats.md#rest-api).
