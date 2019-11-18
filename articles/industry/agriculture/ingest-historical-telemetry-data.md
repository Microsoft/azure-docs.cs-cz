---
title: Ingestovat historická data telemetrie
description: Popisuje, jak získat historická data telemetrie pro ingestování.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 5ae64371bd114a898ddca874e23b499bc4a2b8a3
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128779"
---
# <a name="ingest-historical-telemetry-data"></a>Ingestování historických telemetrických dat

Tento článek popisuje, jak ingestovat historická data ze senzorů do Azure FarmBeats.

Běžné scénáře v FarmBeats jsou ingestování historických dat od Internet věcí (IoT) pro prostředky, jako jsou zařízení a senzory. Můžete vytvářet metadata pro zařízení a senzory a následně ingestovat historická data do FarmBeats v kanonickém formátu.

## <a name="before-you-begin"></a>Než začnete

Než budete pokračovat v tomto článku, ujistěte se, že jste nainstalovali FarmBeats a shromáždili historická data z IoT.

## <a name="enable-partner-access"></a>Povolit přístup k partnerovi

Musíte povolit integraci partnera s instancí Azure FarmBeats. Tento krok vytvoří klienta, který bude mít přístup k vašemu Azure FarmBeats jako partnerovi zařízení, a poskytne vám následující hodnoty, které jsou potřeba v následujících krocích.

- Koncový bod rozhraní API – Toto je adresa URL centra dat, například https://<datahub>. azurewebsites.net
- ID tenanta
- ID klienta
- Tajný kód klienta
- Připojovací řetězec EventHub

Vygenerujte je podle následujících kroků:

>[!NOTE]
> Chcete-li provést následující postup, musíte být správcem.

1. Stáhněte si tento [skript](https://aka.ms/farmbeatspartnerscript) a extrahujte ho na místním disku. V souboru ZIP se nacházejí dva soubory.
2. Přihlaste se k [Azure Portal](https://portal.azure.com/) a otevřete Cloud Shell (Tato možnost je k dispozici na pravém horním panelu portálu).  

    ![Beats farmy projektu](./media/for-tutorials/navigation-bar-1.png)

3. Ujistěte se, že je prostředí nastavené na **PowerShell**.

    ![Beats farmy projektu](./media/for-tutorials/power-shell-new-1.png)

4. Nahrajte dva soubory, které jste stáhli (z kroku 1 výše), do Cloud Shell.  

    ![Beats farmy projektu](./media/for-tutorials/power-shell-two-1.png)

5. Přejít do adresáře, kam se nahrály soubory

   >[!NOTE]
   > Ve výchozím nastavení se soubor odešle do domovského adresáře/home/username/.
6. Spusťte skript pomocí příkazu:  

    ```azurepowershell-interactive
    ./generateCredentials.ps1
    ```

7. Dokončete postup podle pokynů na obrazovce.

## <a name="create-devicesensor-metadata"></a>Vytvořit metadata zařízení/senzoru

 Teď, když máte požadované přihlašovací údaje, můžete zařízení a senzory definovat vytvořením metadat pomocí rozhraní FarmBeats API.

 FarmBeats data hub má následující rozhraní API, která umožňují vytvářet a spravovat metadata zařízení/senzoru.   

- /**DeviceModel** -Device model odpovídá metadatům zařízení, jako je výrobce, typ zařízení buď bránu, nebo uzel.  
- zařízení /**zařízení** odpovídá fyzickému zařízení přítomnému ve farmě.  
- Model /**SensorModel** -snímače odpovídá metadatům senzorů, jako je výrobce, typu snímače buď analogového, nebo digitálního, měření senzorů, jako je například pokojová teplota nebo tlak.
- senzor /**senzorů** odpovídá fyzickému senzoru, který zaznamenává hodnoty. Senzor se obvykle připojuje k zařízení s ID zařízení.  


|        Model zařízení   |  Návrhy   |
| ------- | -------             |
|     Typ (uzel, brána)        |          1 hvězdička      |
|          Výrobce            |         2 hvězdičky     |
|  ProductCode                    |  Kód produktu zařízení nebo název/číslo modelu. Například EnviroMonitor # 6800.  |
|            Porty          |     Název a typ portu (digitální/analogový)
|     Název                 |  Název, který identifikuje prostředek. Například název modelu/název produktu.
      Popis     | Zadejte smysluplný popis modelu.
|    Vlastnosti          |    Další vlastnosti od výrobce   |
|    **zařízení**             |                      |
|   DeviceModelId     |     ID přidruženého modelu zařízení  |
|  hardwareId          | Jedinečné ID zařízení, například adresa MAC atd.
|  ReportingInterval        |   Interval generování sestav v sekundách
|  Umístění            |  Zeměpisná šířka zařízení (-90 až + 90)/longitude (-180 až 180)/Elevation (v metrech)   
|ParentDeviceId       |    ID nadřazeného zařízení, ke kterému je připojeno toto zařízení Například uzel připojený k bráně. Uzel bude mít parentDeviceId jako bránu.  |
|    Název            | Název, který identifikuje prostředek. Partneři zařízení musí odeslat název, který je konzistentní s názvem zařízení na straně partnera. Pokud je název partnerského zařízení definovaný uživatelem, je potřeba rozšířit stejný uživatelsky definovaný název na FarmBeats.|
|     Popis       |      Zadejte smysluplný popis.  |
|     Vlastnosti    |  Další vlastnosti od výrobce
|     **Model senzorů**        |          |
|       Typ (analogové, digitální)          |      typ snímače bez ohledu na analogový nebo digitální       |
|          Výrobce            |       výrobce snímače     |
|     ProductCode| Kód produktu nebo název/číslo modelu Například RS-CO2-N01. |
|       Název > SensorMeasures       | Název míry senzoru Podporuje se jenom malá písmena. Pro měření z různých hloubek Určete hloubku. Například soil_moisture_15cm. Tento název musí být v souladu s daty telemetrie.  |
|          SensorMeasures > datový typ       |Datový typ telemetrie. Aktuálně je podporován typ Double.|
|    Typ > sensorMeasures    |Typ měření dat telemetrie senzorů. Níže jsou uvedené typy systému: AmbientTemperature, CO2, Hloubka, ElectricalConductivity, LeafWetness, Length, LiquidLevel, dusičnan, O2, PH, fosforečnan, PointInTime, draselný, tlak, RainGauge, RelativeHumidity, slanost, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, evapotranspiration, PAR. Pokud chcete přidat další informace, podívejte se na/ExtendedType API.|
|        Jednotka > SensorMeasures              | Jednotka dat telemetrie snímačů. Níže jsou uvedené jednotky definované systémem: jednotka jednotek, Celsia, Fahrenheita, Kelviny, Rankine, Pascal, rtuť, Psí, milimetry, centimetry, měřiče, palce, nohy, míle, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, stupeň, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, procenta, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litr, MilliLiter, sekundy, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour, další informace najdete na/ Rozhraní ExtendedType API.|
|    SensorMeasures > aggregationType    |  Hodnoty mohou být None, Average, Max, minima nebo StandardDeviation.  |
|          Název            | Název, který identifikuje prostředek. Například název modelu/název produktu.  |
|    Popis        | Zadejte smysluplný popis modelu.  |
|   Vlastnosti       |  Další vlastnosti od výrobce  |
|    **Elektrické**      |          |
| hardwareId          |   Jedinečné ID pro senzory nastavené výrobcem |
|  sensorModelId     |    ID přidruženého modelu senzoru   |
| location          |  Zeměpisná šířka (-90 až + 90)/longitude (-180 až 180)/Elevation (v metrech)|
|   název > portu        |  Název a typ portu, ke kterému je senzor připojen na zařízení. Tento název musí být stejný jako definovaný v modelu zařízení. |
|    DeviceID  |    ID zařízení, ke kterému je senzor připojený     |
| Název            |   Název, který identifikuje prostředek. Například název snímače/název produktu a číslo modelu/kód produktu.|
|    Popis      | Zadejte smysluplný popis. |
|    Vlastnosti        |Další vlastnosti od výrobce |

Další informace o objektech naleznete v [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

**Požadavek rozhraní API na vytvoření metadat**

Chcete-li vytvořit požadavek rozhraní API, zkombinujete metodu HTTP (POST), adresu URL služby API, identifikátor URI k prostředku, který se má dotazovat, odešlete data pro vytvoření nebo odstranění žádosti a přidáte jednu nebo více hlaviček požadavku HTTP. Adresa URL služby API je koncový bod rozhraní API, tj. adresa URL centra dat (https://<yourdatahub>. azurewebsites.net).  

**Ověřování**:

FarmBeats data hub používá ověřování pomocí nosiče, které potřebuje následující přihlašovací údaje vygenerované v předchozí části.

- ID klienta
- Tajný kód klienta
- ID tenanta  

Pomocí výše uvedených přihlašovacích údajů může volající požádat o přístupový token, který se musí poslat v následujících žádostech o rozhraní API v části záhlaví, jak je znázorněno níže:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

**Hlavičky požadavku HTTP**:

Tady jsou nejčastější hlavičky požadavků, které je potřeba zadat při volání rozhraní API FarmBeats data hub:

- Content-Type: Application/JSON
- Autorizace: nosný < přístup-token >
- Přijmout: Application/JSON

**Vstupní datová část pro vytváření metadat**:

**DeviceModel**


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
Níže uvedená ukázková žádost slouží k vytvoření zařízení (obsahuje vstupní JSON jako datovou část s textem žádosti).  

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> Rozhraní API vrací jedinečná ID pro každou vytvořenou instanci. Je nutné zachovat ID pro odeslání odpovídajících zpráv telemetrie.

**Poslat telemetrii**

Teď, když jste vytvořili zařízení a senzory v FarmBeats, můžete odeslat přidružené zprávy telemetrie.  

**Vytvořit klienta telemetrie**

Pro zpracování musíte odeslat telemetrii do Azure Event hub. Azure EventHub je služba, která umožňuje přijímání dat v reálném čase z připojených zařízení a aplikací. Chcete-li odesílat data telemetrie do FarmBeats, je třeba vytvořit klienta, který odesílá zprávy do centra událostí v FarmBeats. Další informace o odesílání telemetrie najdete v tématu [centra událostí Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

**Poslat zprávu telemetrie jako klienta**

Jakmile máte připojení navázané jako klient EventHub, můžete odesílat zprávy na EventHub jako JSON.  
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
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

Po přidání odpovídajících zařízení a senzorů získáte DeviceID a sensorid ve zprávě telemetrie, jak je popsáno v předchozí části.

Ukázková zpráva telemetrie:


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


## <a name="next-steps"></a>Další kroky

Další informace o integraci založené na rozhraní REST API najdete v tématu [REST API](references-for-farmbeats.md#rest-api).
