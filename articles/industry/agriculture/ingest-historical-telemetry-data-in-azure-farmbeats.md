---
title: Ingestování historických telemetrických dat
description: Tento článek popisuje, jak ingestovat historická telemetrická data.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e0a5e89f256b562ce5f702e9ff1388cb4d021bf5
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437694"
---
# <a name="ingest-historical-telemetry-data"></a>Ingestování historických telemetrických dat

Tento článek popisuje, jak ingestovat data historických senzorů do Azure FarmBeats.

Ingestování historických dat z prostředků Internetu věcí (IoT), jako jsou zařízení a senzory, je běžným scénářem v FarmBeats. Vytvoříte metadata pro zařízení a senzory a potom ingestovat historická data FarmBeats v kanonickém formátu.

## <a name="before-you-begin"></a>Než začnete

Než budete pokračovat v tomto článku, ujistěte se, že jste nainstalovali FarmBeats a shromážděná historická data ze zařízení IoT. Je také nutné povolit přístup partnera, jak je uvedeno v následujících krocích.

## <a name="enable-partner-access"></a>Povolení přístupu partnerů

Musíte povolit integraci partnerů do instance Azure FarmBeats. Tento krok vytvoří klienta, který má přístup k vaší instanci Azure FarmBeats jako partner vašeho zařízení a poskytuje následující hodnoty, které jsou požadovány v následujících krocích:

- Koncový bod rozhraní API: Toto je adresa\<URL Datahubu, například https:// datahub>.azurewebsites.net
- ID tenanta
- ID klienta
- Tajný klíč klienta
- Připojovací řetězec EventHub

Postupujte následovně:

> [!NOTE]
> Chcete-li provést následující kroky, musíte být správcem.

1. Přihlaste se k webu https://portal.azure.com/.

2. **Pokud jste na FarmBeats verze 1.2.7 nebo novější, přeskočte kroky a, b a c a přejděte ke kroku 3.** Můžete zkontrolovat Verzi FarmBeats výběrem ikony **Nastavení** v pravém horním rohu ui FarmBeats.

      a.  Přejděte na**Registrace aplikací služby** **Azure Active Directory** > 

      b. Vyberte **registraci aplikace,** která byla vytvořena jako součást vašeho nasazení FarmBeats. Bude mít stejný název jako váš Datahub FarmBeats.

      c. Vyberte **vystavit rozhraní API** > vyberte Přidat **klientskou aplikaci** a zadejte **04b07795-8ddb-461a-bbee-02f9e1bf7b46** a zkontrolujte **autorizovat obor**. Tím se umožní přístup k Azure CLI (Cloud Shell) k provedení níže uvedených kroků:

3. Otevřete Cloud Shell. Tato možnost je dostupná na panelu nástrojů v pravém horním rohu portálu Azure.

    ![Panel nástrojů portálu Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Ujistěte se, že je prostředí nastaveno na **PowerShell**. Ve výchozím nastavení je nastavena na Bash.

    ![Nastavení panelu nástrojů prostředí PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Přejděte do domovského adresáře.

    ```azurepowershell-interactive 
    cd  
    ```

6. Spusťte následující příkaz. Tím se stáhne skript do domovského adresáře.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

7. Spusťte následující skript. Skript požádá o ID klienta, které lze získat ze stránky**Přehled** **služby Azure Active Directory.** > 

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

8. Podle pokynů na obrazovce zachyťte hodnoty pro **koncový bod rozhraní API**, **ID klienta**, **ID klienta**, **tajný klíč klienta**a **připojovací řetězec EventHub**.


## <a name="create-device-or-sensor-metadata"></a>Vytvoření metadat zařízení nebo senzoru

 Nyní, když máte požadované přihlašovací údaje, můžete definovat zařízení a senzory. Chcete-li to provést, vytvořte metadata voláním FarmBeats API. Nezapomeňte volat api jako klientskou aplikaci, kterou jste vytvořili ve výše uvedené části.

 FarmBeats Datahub má následující rozhraní API, která umožňují vytváření a správu metadat zařízení nebo senzoru.

 > [!NOTE]
 > Jako partner máte přístup pouze ke čtení, vytváření a aktualizaci metadat; **možnost odstranění je omezena na partnera.**

- /**DeviceModel**: DeviceModel odpovídá metadatům zařízení, jako je například výrobce a typ zařízení, což je brána nebo uzel.
- /**Zařízení**: Zařízení odpovídá fyzickému zařízení přítomnému na farmě.
- /**SensorModel**: SensorModel odpovídá metadatům senzoru, jako je výrobce, typ senzoru, který je analogový nebo digitální, a měření senzoru, jako je okolní teplota a tlak.
- /**Senzor**: Senzor odpovídá fyzickému senzoru, který zaznamenává hodnoty. Senzor je obvykle připojen k zařízení s ID zařízení.  


|        DeviceModel   |  Návrhy   |
| ------- | -------             |
|     Typ (uzel, brána)        |          Typ zařízení – uzel nebo brána      |
|          Výrobce            |         Jméno výrobce    |
|  Productcode                    |  Kód produktu zařízení nebo název nebo číslo modelu. Například EnviroMonitor#6800.  |
|            Porty          |     Název a typ portu, který je digitální nebo analogový.
|     Name (Název)                 |  Název k identifikaci prostředku. Například název modelu nebo název produktu.
      Popis     | Zadejte smysluplný popis modelu.
|    Vlastnosti          |    Další vlastnosti od výrobce.   |
|    **Zařízení**             |                      |
|   DeviceModelId     |     ID přidruženého modelu zařízení.  |
|  Id hardwaru          | Jedinečné ID zařízení, například adresa MAC.
|  Interval hlášení        |   Interval hlášení v sekundách.
|  Umístění            |  Zeměpisná šířka zařízení (-90 až +90), zeměpisná délka (-180 až 180) a nadmořská výška (v metrech).   
|Id nadřazeného zařízení       |    ID nadřazeného zařízení, ke kterému je toto zařízení připojeno. Například uzel, který je připojen k bráně. Uzel má parentDeviceId jako bránu.  |
|    Name (Název)            | Název k identifikaci prostředku. Partneři zařízení musí odeslat název, který je konzistentní s názvem zařízení na straně partnera. Pokud je název partnerského zařízení definován uživatelem, měl by být stejný název definovaný uživatelem rozšířen na FarmBeats.|
|     Popis       |      Poskytněte smysluplný popis. |
|     Vlastnosti    |  Další vlastnosti od výrobce.
|     **SenzorModel**        |          |
|       Typ (analogový, digitální)          |      Typ senzoru, ať už je to analogové nebo digitální.       |
|          Výrobce            |       Výrobce senzoru.     |
|     Productcode| Kód produktu nebo název nebo číslo modelu. Například RS-CO2-N01. |
|       Název > sensormeasures       | Název měry snímače. Podporována jsou pouze malá písmena. Pro měření z různých hloubek určete hloubku. Například soil_moisture_15cm. Tento název musí být konzistentní s telemetrická data.  |
|          SensorMeasures > datový typ       |Datový typ telemetrie. V současné době double je podporována.|
|    Typ > senzorůměří    |Typ měření telemetrických dat senzoru. Systémem definované typy jsou AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Dusičnan, O2, PH, Fosfát, PointInTime, Draslík, Tlak, RainGauge, RelativníVlhkost, Slanost, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiracation, PAR. Chcete-li přidat další, naleznete rozhraní /ExtendedType API.|
|        SenzorOpatření > jednotka              | Jednotka telemetrických dat senzoru. Systémem definované jednotky jsou NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, Meter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Procento, PartsPerMillion, MicroMol, MicroMolesPerLitre, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroPerMeterSquaredPerSecond, InchesPerHour Chcete-li přidat další, viz /ExtendedType API.|
|    SensorMeasures > AggregationType    |  Hodnoty mohou být žádné, průměrné, maximální, minimální nebo Odchylka podle směrovek.  |
|          Name (Název)            | Název k identifikaci prostředku. Například název modelu nebo název produktu.  |
|    Popis        | Zadejte smysluplný popis modelu.|
|   Vlastnosti       |  Další vlastnosti od výrobce.|
|    **Senzor**      |          |
| Id hardwaru          |   Jedinečné ID senzoru nastavené výrobcem.|
|  SensorModelId     |    ID přidruženého modelu senzoru.|
| Umístění          |  Zeměpisná šířka senzoru (-90 až +90), zeměpisná délka (-180 až 180) a nadmořská výška (v metrech).|
|   Název > portu        |  Název a typ portu, ke kterému je senzor připojen v zařízení. To musí být stejný název, jak je definováno v modelu zařízení.|
|    Deviceid  |    ID zařízení, ke kterému je senzor připojen. |
| Name (Název)            |   Název k identifikaci prostředku. Například název senzoru nebo název produktu a číslo modelu nebo kód produktu.|
|    Popis      | Poskytněte smysluplný popis.|
|    Vlastnosti        |Další vlastnosti od výrobce.|

Další informace o objektech naleznete v tématu [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Požadavek rozhraní API na vytvoření metadat

Chcete-li vytvořit požadavek rozhraní API, zkombinujte metodu HTTP (POST), adresu URL služby rozhraní API a identifikátor URI s prostředkem, který má dotazovat, odesílat data, vytvářet nebo odstraňovat požadavek. Potom přidáte jednu nebo více hlaviček požadavků HTTP. Adresa URL služby rozhraní API je koncový bod rozhraní API,\<to znamená, že adresa URL Datahub (https:// yourdatahub>.azurewebsites.net).  

### <a name="authentication"></a>Authentication

FarmBeats Datahub používá ověřování nosiče, které potřebuje následující pověření, které byly generovány v předchozí části:

- ID klienta
- Tajný klíč klienta
- ID tenanta

Pomocí těchto pověření může volající požádat o přístupový token. Token musí být odeslán v následujících požadavcích rozhraní API v části záhlaví takto:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Následující ukázkový kód Pythonu poskytuje přístupový token, který lze použít pro následná volání rozhraní API farmbeats: 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
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

**Hlavičky požadavků HTTP**

Zde jsou nejčastější hlavičky požadavků, které musí být zadány při volání rozhraní API farmbeats Datahub:

- **Content-Type**: aplikace/json
- **Autorizace**: Nosič <> přístupového tokenu
- **Přijmout**: aplikace/json

### <a name="input-payload-to-create-metadata"></a>Vstupní datová část pro vytvoření metadat

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Zařízení

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SenzorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Senzor

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Následující ukázkový požadavek vytvoří zařízení. Tento požadavek má vstupní JSON jako datovou část s tělem požadavku.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

Níže je ukázkový kód v Pythonu. Přístupový token použitý v této ukázce je stejný, který je přijat během ověřování.

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> Api vrátí jedinečné ID pro každou vytvořenou instanci. Chcete-li odeslat odpovídající telemetrické zprávy, musíte zachovat ID.

### <a name="send-telemetry"></a>Odeslání telemetrie

Nyní, když jste vytvořili zařízení a senzory v FarmBeats, můžete odesílat přidružené telemetrické zprávy.

### <a name="create-a-telemetry-client"></a>Vytvoření klienta telemetrie

Telemetrická data je nutné odeslat do centra událostí Azure ke zpracování. Azure Event Hubs je služba, která umožňuje sdílení dat v reálném čase (telemetrie) z připojených zařízení a aplikací. Chcete-li odeslat telemetrická data do FarmBeats, vytvořte klienta, který odesílá zprávy do centra událostí v FarmBeats. Další informace o odesílání telemetrie najdete [v tématu Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

### <a name="send-a-telemetry-message-as-the-client"></a>Odeslání telemetrické zprávy jako klienta

Po připojení navázána jako klient centra událostí, můžete odesílat zprávy do centra událostí jako JSON.

Tady je ukázkový kód Pythonu, který odesílá telemetrii jako klientdo zadaného centra událostí:

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Převeďte formát dat historických senzorů na kanonický formát, kterému Azure FarmBeats rozumí. Formát kanonické zprávy je následující:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

Po přidání odpovídajících zařízení a senzorů získáte ID zařízení a ID senzoru ve zprávě telemetrie, jak je popsáno v předchozí části.

Tady je příklad telemetrické zprávy:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```

## <a name="troubleshooting"></a>Řešení potíží

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nelze zobrazit telemetrická data po požití historických/streamovaných dat ze senzorů

**Příznak**: Zařízení nebo senzory jsou nasazeny a vytvořili jste zařízení / senzory na FarmBeats a pozůstalé telemetrii na EventHub, ale nemůžete získat nebo zobrazit telemetrická data na FarmBeats.

**Nápravná opatření**:

1. Ujistěte se, že jste provedli příslušnou registraci partnera - můžete to zkontrolovat tak, že přejdete na vaše datahub swagger, přejděte na /Partner API, Do get a zkontrolujte, zda je partner registrován. Pokud ne, postupujte [podle pokynů zde](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) přidat partnera.

2. Ujistěte se, že jste vytvořili metadata (DeviceModel, Device, SensorModel, Sensor) pomocí přihlašovacích údajů klienta partnera.

3. Ujistěte se, že jste použili správný formát zprávy telemetrie (jak je uvedeno níže):

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

## <a name="next-steps"></a>Další kroky

Další informace o podrobnostech integrace založené na rozhraní REST API naleznete v tématu [REST API](rest-api-in-azure-farmbeats.md).
