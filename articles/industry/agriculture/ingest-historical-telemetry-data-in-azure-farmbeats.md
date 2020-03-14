---
title: Ingestování historických telemetrických dat
description: Tento článek popisuje, jak ingestovat historická data telemetrie.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d47fdb9461786d80d65ee2448cc983a7a8348ff2
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298763"
---
# <a name="ingest-historical-telemetry-data"></a>Ingestování historických telemetrických dat

Tento článek popisuje, jak ingestovat historická data ze senzorů do Azure FarmBeats.

Běžným scénářem v FarmBeats je příjem historických dat z prostředků Internet věcí (IoT), jako jsou zařízení a senzory. Můžete vytvářet metadata pro zařízení a senzory a následně ingestovat historická data do FarmBeats v kanonickém formátu.

## <a name="before-you-begin"></a>Před zahájením

Než budete pokračovat v tomto článku, ujistěte se, že jste nainstalovali FarmBeats a shromáždili historická data ze svých zařízení IoT. Musíte taky povolit partnerský přístup, jak je uvedeno v následujících krocích.

## <a name="enable-partner-access"></a>Povolit přístup k partnerovi

Musíte povolit integraci partnera s instancí Azure FarmBeats. Tento krok vytvoří klienta, který má přístup k vaší instanci Azure FarmBeats jako partnerovi zařízení a poskytne následující hodnoty, které jsou potřeba v následujících krocích:

- Koncový bod rozhraní API: Jedná se o adresu URL DataHub, například https://\<DataHub >. azurewebsites. NET
- ID tenanta
- ID klienta
- Tajný klíč klienta
- Připojovací řetězec EventHub

Postupujte následovně:

>[!NOTE]
> Chcete-li provést následující postup, musíte být správcem.

1. Stáhněte [soubor zip](https://aka.ms/farmbeatspartnerscriptv2)a extrahujte ho na místní disk. V souboru zip bude k dispozici jeden soubor.
2. Přihlaste se k https://portal.azure.com/ a přejít na **Azure Active Directory** > **Registrace aplikací**.

3. Vyberte **registraci aplikace** vytvořenou jako součást nasazení FarmBeats. Bude mít stejný název jako FarmBeats DataHub.

4. Vyberte **zveřejnit rozhraní API** > vyberte **Přidat klientskou aplikaci** a zadejte **04b07795-8ddb-461a-bbee-02f9e1bf7b46** a ověřte **obor autorizace**. Tím zajistíte přístup k rozhraní příkazového řádku Azure CLI (Cloud Shell), abyste mohli provést následující kroky:

5. Otevřete Cloud Shell. Tato možnost je k dispozici na panelu nástrojů v pravém horním rohu Azure Portal.

    ![Panel nástrojů Azure Portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Ujistěte se, že je prostředí nastavené na **PowerShell**. Ve výchozím nastavení je nastavená na bash.

    ![Nastavení panelu nástrojů PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Nahrajte soubor z kroku 1 v instanci Cloud Shell.

    ![Tlačítko nahrát na panelu nástrojů](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Přejít do adresáře, kam se soubor nahrál. Ve výchozím nastavení se soubory odešlou do domovského adresáře pod uživatelským jménem.

9. Spusťte následující skript. Skript zobrazí výzvu k zadání ID tenanta, které se dá získat z **Azure Active Directory** > **stránce s přehledem**.

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1   

    ```

10. Postupujte podle pokynů na obrazovce a zaznamenejte hodnoty pro **koncový bod rozhraní API**, **ID tenanta**, **ID klienta**, **tajný klíč klienta**a **připojovací řetězec EventHub**.
## <a name="create-device-or-sensor-metadata"></a>Vytvořit zařízení nebo metadata senzorů

 Teď, když máte požadované přihlašovací údaje, můžete definovat zařízení a senzory. Chcete-li to provést, vytvořte metadata voláním rozhraní FarmBeats API. Nezapomeňte volat rozhraní API jako klientskou aplikaci, kterou jste vytvořili v předchozí části.

 FarmBeats DataHub má následující rozhraní API, které umožňuje vytvářet a spravovat metadata zařízení nebo senzorů.

 > [!NOTE]
 > Jako partner, ke kterému máte přístup, stačí jen číst, vytvářet a aktualizovat metadata; **možnost odstranit je omezena na partnera.**

- /**DeviceModel**: DeviceModel odpovídá metadatům zařízení, jako je například výrobce a typ zařízení, což je brána nebo uzel.
- /**zařízení**: zařízení odpovídá fyzickému zařízení, které je ve farmě přítomné.
- /**SensorModel**: SensorModel odpovídá metadatům snímače, jako je výrobce, typ snímače, který je buď analogový, nebo digitální, a měření snímače, například okolní teplota a tlak.
- **senzor**/: senzor odpovídá fyzickému senzoru, který zaznamenává hodnoty. Senzor se obvykle připojuje k zařízení s ID zařízení.  


|        DeviceModel   |  Návrhy   |
| ------- | -------             |
|     Typ (uzel, brána)        |          Typ zařízení – uzel nebo brána      |
|          Výrobce            |         Název výrobce    |
|  ProductCode                    |  Kód produktu nebo číslo modelu zařízení Například EnviroMonitor # 6800.  |
|            Porty          |     Název portu a typ, který je digitální nebo analogový.
|     Název                 |  Název, který identifikuje prostředek. Například název modelu nebo název produktu.
      Popis     | Poskytněte smysluplný popis modelu.
|    Vlastnosti          |    Další vlastnosti od výrobce.   |
|    **Zařízení**             |                      |
|   DeviceModelId     |     ID přidruženého modelu zařízení  |
|  HardwareId          | Jedinečné ID zařízení, například adresa MAC.
|  ReportingInterval        |   Interval generování sestav v sekundách.
|  Umístění            |  Zeměpisná šířka zařízení (-90 až + 90), zeměpisná délka (-180 až 180) a zvýšení úrovně (v metrech).   
|ParentDeviceId       |    ID nadřazeného zařízení, ke kterému je připojeno toto zařízení Například uzel, který je připojen k bráně. Uzel má jako bránu parentDeviceId.  |
|    Název            | Název, který identifikuje prostředek. Partneři zařízení musí poslat název, který je konzistentní s názvem zařízení na straně partnera. Pokud je název partnerského zařízení definovaný uživatelem, je potřeba rozšířit stejný uživatelsky definovaný název na FarmBeats.|
|     Popis       |      Zadejte smysluplný popis. |
|     Vlastnosti    |  Další vlastnosti od výrobce.
|     **SensorModel**        |          |
|       Typ (analogové, digitální)          |      Typ snímače bez ohledu na to, zda je analogový nebo digitální.       |
|          Výrobce            |       Výrobce snímače.     |
|     ProductCode| Kód produktu nebo název modelu nebo číslo. Například RS-CO2-N01. |
|       Název > SensorMeasures       | Název míry senzoru Podporují se jenom malá písmena. Pro měření z různých hloubek Určete hloubku. Například soil_moisture_15cm. Tento název musí být konzistentní s daty telemetrie.  |
|          SensorMeasures > datový typ       |Datový typ telemetrie. V současné době je podporována dvojitá přesnost.|
|    Typ > SensorMeasures    |Typ měření dat telemetrie senzorů. Systémem definované typy jsou AmbientTemperature, CO2, Hloubka, ElectricalConductivity, LeafWetness, Length, LiquidLevel, dusičnan, O2, PH, fosforečnan, PointInTime, draselný, tlak, RainGauge, RelativeHumidity, slanost, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, evapotranspiration, PAR. Pokud chcete přidat další informace, podívejte se na rozhraní/ExtendedType API.|
|        Jednotka > SensorMeasures              | Jednotka dat telemetrie snímačů. Jednotky definované systémem jsou jednotky jednotek, Celsia, Fahrenheita, Kelviny, Rankine, Pascal, rtuť, Psí, milimetry, centimetry, měřiče, palce, nohy, kilometry, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, stupeň, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, procenta, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litr, MilliLiter, sekundy, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour, další informace najdete v tématu rozhraní API pro/ExtendedType.|
|    SensorMeasures > AggregationType    |  Hodnoty mohou být None, Average, maximum, minima nebo StandardDeviation.  |
|          Název            | Název, který identifikuje prostředek. Například název modelu nebo název produktu.  |
|    Popis        | Poskytněte smysluplný popis modelu.  |
|   Vlastnosti       |  Další vlastnosti od výrobce.  |
|    **Elektrické**      |          |
| HardwareId          |   Jedinečné ID pro senzory nastavené výrobcem. |
|  SensorModelId     |    ID přidruženého modelu senzoru   |
| Umístění          |  Zeměpisná šířka (-90 až + 90), zeměpisná délka (-180 až 180) a zvýšení úrovně (v metrech).|
|   Název > portu        |  Název a typ portu, ke kterému je senzor připojen na zařízení. Tento název musí být stejný jako definovaný v modelu zařízení. |
|    ID zařízení  |    ID zařízení, ke kterému je senzor připojen.     |
| Název            |   Název, který identifikuje prostředek. Například název senzoru nebo název produktu a číslo modelu nebo kód produktu.|
|    Popis      | Zadejte smysluplný popis. |
|    Vlastnosti        |Další vlastnosti od výrobce. |

Další informace o objektech naleznete v [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Požadavek rozhraní API na vytvoření metadat

Chcete-li vytvořit požadavek rozhraní API, můžete zkombinovat metodu HTTP (POST), adresu URL služby API a identifikátor URI k prostředku, který se má dotazovat, odeslat data do, vytvořit nebo odstranit žádost. Pak přidáte jednu nebo více hlaviček požadavku HTTP. Adresa URL služby API je koncový bod rozhraní API, to znamená DataHub adresa URL (https://\<yourdatahub >. azurewebsites. NET).  

### <a name="authentication"></a>Authentication

FarmBeats DataHub používá ověřování pomocí nosiče, které potřebuje následující přihlašovací údaje vygenerované v předchozí části:

- ID klienta
- Tajný klíč klienta
- ID tenanta

Pomocí těchto přihlašovacích údajů může volající požádat o přístupový token. Token musí být odeslán v následujících požadavcích rozhraní API, v části záhlaví, následovně:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Následující vzorový kód Pythonu poskytuje přístupový token, který se dá použít k následnému volání rozhraní API do FarmBeats: 

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


**Hlavičky požadavku HTTP**

Tady jsou nejběžnější hlavičky požadavků, které je potřeba zadat při volání rozhraní API FarmBeats DataHub:

- **Content-Type**: Application/JSON
- **Autorizace**: nosný < přístup-token >
- **Přijmout**: Application/JSON

### <a name="input-payload-to-create-metadata"></a>Vstupní datová část pro vytváření metadat

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

SensorModel

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
Elektrické

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
Následující vzorový požadavek vytvoří zařízení. Tento požadavek má jako datovou část vstupní JSON s textem žádosti.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

Níže je uveden vzorový kód v Pythonu. Přístupový token použitý v této ukázce je stejný jako přijatý během ověřování.

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
> Rozhraní API vrací jedinečná ID pro každou vytvořenou instanci. Aby bylo možné odesílat odpovídající zprávy telemetrie, je nutné zachovat ID.

### <a name="send-telemetry"></a>Odeslání telemetrie

Teď, když jste vytvořili zařízení a senzory v FarmBeats, můžete odeslat přidružené zprávy telemetrie.

### <a name="create-a-telemetry-client"></a>Vytvoření klienta telemetrie

Telemetrii musíte odeslat do služby Azure Event Hubs ke zpracování. Azure Event Hubs je služba, která umožňuje přijímání dat v reálném čase z připojených zařízení a aplikací. Chcete-li odesílat data telemetrie do FarmBeats, vytvořte klienta, který odesílá zprávy do centra událostí v FarmBeats. Další informace o posílání telemetrie najdete v tématu [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

### <a name="send-a-telemetry-message-as-the-client"></a>Odeslání zprávy telemetrie jako klienta

Po navázání připojení jako klienta Event Hubs můžete odesílat zprávy do centra událostí jako JSON.

Tady je ukázkový kód Pythonu, který odesílá telemetrii jako klienta do zadaného centra událostí:

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

Převeďte historický formát dat ze senzorů na Kanonický formát, který Azure FarmBeats rozumí. Formát kanonické zprávy je následující:

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
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<values>"
        }
      ]
    }
 ]
}
```

Po přidání odpovídajících zařízení a senzorů Získejte ID zařízení a ID snímače ve zprávě telemetrie, jak je popsáno v předchozí části.

Tady je příklad zprávy telemetrie:


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

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Po ingestování historických/streamových dat ze senzorů nejde zobrazit data telemetrie.

**Příznak**: zařízení nebo senzory se nasazují a vytvořili jste zařízení/senzory na FarmBeats a ingestovaná telemetrie na EventHub, ale data telemetrie nemůžete získat nebo zobrazit na FarmBeats.

**Nápravná opatření**:

1. Ujistěte se, že jste provedli příslušnou registraci partnera – můžete to zkontrolovat tak, že přejdete do DataHub Swagger, přejdete do rozhraní/partner API, provedete získání a zkontrolujete, jestli je partner zaregistrovaný. Pokud ne, přidejte partnera pomocí následujících [kroků](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) .

2. Ujistěte se, že jste vytvořili metadata (DeviceModel, Device, SensorModel, snímač) pomocí přihlašovacích údajů partnerského klienta.

3. Ujistěte se, že jste používali správný formát zprávy telemetrie (jak je uvedeno níže):

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
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```


## <a name="next-steps"></a>Další kroky

Další informace o REST API podrobností integrace na základě REST API najdete v tématu [REST API](rest-api-in-azure-farmbeats.md).
