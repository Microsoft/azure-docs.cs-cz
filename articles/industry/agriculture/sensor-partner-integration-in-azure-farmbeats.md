---
title: Integrace partnerských řešení pro senzory
description: Tento článek popisuje integraci partnera pro senzory.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 8d1b8203fa50609daf59431c2cfecba68eba52b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179744"
---
# <a name="sensor-partner-integration"></a>Integrace partnerských řešení pro senzory

Tento článek poskytuje informace o komponentě **překladače** Azure FarmBeats, která umožňuje integraci partnera snímače.

Pomocí této součásti můžou partneři integrovat s FarmBeats pomocí rozhraní API FarmBeats DataHub a posílat data a telemetrie zákaznických zařízení do FarmBeats DataHub. Jakmile jsou data v FarmBeats k dispozici, je možné ji vizuálně vymezit pomocí akcelerátoru FarmBeats a lze ji použít pro datové fúze a vytváření strojového učení nebo umělých analytických modelů.

## <a name="before-you-start"></a>Než začnete

K vývoji komponenty překladatele budete potřebovat následující přihlašovací údaje, které umožní přístup k rozhraním API FarmBeats.

- Koncový bod rozhraní API
- ID tenanta
- ID klienta
- Tajný klíč klienta
- Připojovací řetězec EventHub

V této části najdete výše uvedené přihlašovací údaje: [Povolení integrace zařízení](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Vývoj překladatelů

**Integrace na základě REST API**

Možnosti integrace dat ze senzorů FarmBeats jsou zpřístupněny prostřednictvím REST API. Mezi možnosti patří definice metadat, zřizování zařízení a senzorů a Správa zařízení a senzorů.

**Ingestování telemetrie**

Data telemetrie se mapují na kanonickou zprávu, která je publikovaná na Azure Event Hubs ke zpracování. Azure Event Hubs je služba, která umožňuje přijímání dat v reálném čase z připojených zařízení a aplikací.

**Vývoj rozhraní API**

Rozhraní API obsahují technickou dokumentaci k Swagger. Další informace o rozhraních API a jejich odpovídajících požadavcích nebo odpovědích najdete v tématu [Swagger](https://aka.ms/FarmBeatsSwagger).

**Authentication**

FarmBeats používá ověřování Microsoft Azure Active Directory.Azure App Service poskytuje integrovanou podporu ověřování a autorizace.

Další informace najdete v tématu [Azure Active Directory](../../app-service/overview-authentication-authorization.md).

FarmBeats DataHub používá ověřování pomocí nosiče, které potřebuje následující přihlašovací údaje:
   - ID klienta
   - Tajný klíč klienta
   - ID tenanta

Pomocí těchto přihlašovacích údajů může volající požádat o přístupový token. Token se musí odeslat v následujících požadavcích rozhraní API, a to v části záhlaví, jak je znázorněno níže:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Následující vzorový kód Pythonu poskytuje přístupový token, který se dá použít pro další volání rozhraní API pro FarmBeats.

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


**Hlavičky požadavku HTTP**

Tady jsou nejběžnější hlavičky požadavků, které je potřeba zadat při volání rozhraní API FarmBeats DataHub.


**Hlavička** | **Popis a příklad**
--- | ---
Typ obsahu | Formát požadavku (Content-Type: Application/ <format> ). Pro rozhraní FarmBeats DataHub API je formát JSON. Content-Type: Application/JSON
Autorizace | Určuje přístupový token potřebný k vytvoření volání rozhraní API. Autorizace: nosič <Access-Token>
Přijmout | Formát odpovědi. Pro rozhraní FarmBeats DataHub API je formát JSON. Přijmout: Application/JSON

**Požadavky rozhraní API**

Chcete-li vytvořit žádost o REST API, zkombinujete metodu HTTP (GET, POST nebo PUT), adresu URL služby API, identifikátor URI (Uniform Resource Identifier) k prostředku, který se má dotazovat, odeslat data do, aktualizovat nebo odstranit a jednu nebo více hlaviček požadavku HTTP. Adresa URL služby API je koncový bod rozhraní API, který zadáte. Tady je příklad: https:// \<yourdatahub-website-name> . azurewebsites.NET

Volitelně můžete zahrnout parametry dotazu pro volání funkce GET k filtrování, omezení velikosti a řazení dat v odpovědích.

Následující ukázková žádost slouží k získání seznamu zařízení.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
Většina volání GET, POST a PUT vyžaduje tělo požadavku JSON.

Následující ukázková žádost slouží k vytvoření zařízení. (Tato ukázka obsahuje vstupní JSON s textem žádosti.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formát dat

JSON je běžný jazyk nezávislý na jazyce, který poskytuje jednoduchou textovou reprezentaci libovolných datových struktur. Další informace najdete v tématu [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Specifikace metadat

FarmBeats DataHub má následující rozhraní API, které umožňuje partnerům zařízení vytvářet a spravovat metadata zařízení nebo senzorů.

- /**DeviceModel**: DeviceModel odpovídá metadatům zařízení, jako je například výrobce a typ zařízení, který je buď bránou, nebo uzlem.
- /**Zařízení**: zařízení odpovídá fyzickému zařízení, které je ve farmě přítomné.
- /**SensorModel**: SensorModel odpovídá metadatům snímače, jako je výrobce, typ snímače, který je buď analogový, nebo digitální, a míru snímače, například okolní teplotu a tlak.
- /**Senzor**: senzor odpovídá fyzickému senzoru, který zaznamenává hodnoty. Senzor se obvykle připojuje k zařízení s ID zařízení.

  DeviceModel | Description |
  --- | ---
  Typ (uzel, brána)  | Typ zařízení – uzel nebo brána |
  Manufacturer  | Název výrobce |
  ProductCode  | Kód produktu nebo číslo modelu zařízení Například EnviroMonitor # 6800. |
  Porty  | Název portu a typ, který je digitální nebo analogový.  |
  Name  | Název, který identifikuje prostředek. Například název modelu nebo název produktu. |
  Description  | Poskytněte smysluplný popis modelu. |
  Vlastnosti  | Další vlastnosti od výrobce. |
  **Zařízení** | **Popis** |
  DeviceModelId  |ID přidruženého modelu zařízení |
  HardwareId   |Jedinečné ID zařízení, například adresa MAC.  |
  ReportingInterval |Interval generování sestav v sekundách. |
  Umístění    |Zeměpisná šířka zařízení (-90 až + 90), zeměpisná délka (-180 až 180) a zvýšení úrovně (v metrech). |
  ParentDeviceId | ID nadřazeného zařízení, ke kterému je připojeno toto zařízení Například pokud je uzel připojen k bráně, uzel má jako bránu parentDeviceID. |
  Name  | Název, který identifikuje prostředek. Partneři zařízení musí poslat název, který je konzistentní s názvem zařízení na straně partnera zařízení. Pokud je název zařízení definovaný uživatelem na straně partnera zařízení, stejný uživatelsky definovaný název by měl být šířen do FarmBeats.  |
  Description  | Zadejte smysluplný popis.  |
  Vlastnosti  |Další vlastnosti od výrobce.  |
  **SensorModel** | **Popis** |
  Typ (analogové, digitální)  |Zmiňující analogový nebo digitální senzor.|
  Manufacturer  | Název výrobce. |
  ProductCode  | Kód produktu nebo název modelu nebo číslo. Například RS-CO2-N01.  |
  Název > SensorMeasures  | Název míry senzoru Podporují se jenom malá písmena. Pro měření z různých hloubek Určete hloubku. Například soil_moisture_15cm. Tento název musí být v souladu s daty telemetrie. |
  SensorMeasures > datový typ  | Datový typ telemetrie. V současné době je podporována dvojitá přesnost. |
  Typ > SensorMeasures  | Typ měření dat telemetrie senzorů. Níže jsou uvedené typy systému: AmbientTemperature, CO2, Hloubka, ElectricalConductivity, LeafWetness, Length, LiquidLevel, dusičnan, O2, PH, fosforečnan, PointInTime, draselný, tlak, RainGauge, RelativeHumidity, slanost, SoilMoisture, SoilTemperature, SolarRadiation, TimeDuration, UVRadiation, UVIndex, WindDirection, WindRun, WindSpeed, evapotranspiration,,, nominální hodnota. Pokud chcete přidat další informace, podívejte se na rozhraní/ExtendedType API.
  Jednotka > SensorMeasures | Jednotka dat telemetrie snímačů. Níže jsou uvedené jednotky definované systémem: jednotka, Celsia, Fahrenheita, Kelviny, Rankine, Pascal, rtuť, PSI, milimetr, centimetr, měřič, palce, nohy, míle, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, stupeň, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, s VolumetricIonContent, MilliLiter, UnixTimestamp a MicroMolPerMeterSquaredPerSecond. Pokud chcete přidat další informace, podívejte se na rozhraní/ExtendedType API.
  SensorMeasures > AggregationType  | Buď None, Average, Max, minima nebo StandardDeviation.
  Hloubka > SensorMeasures  | Hloubka senzoru v centimetrech Například měření vlhkosti 10 cm pod vozovkou.
  Popis > SensorMeasures  | Poskytněte smysluplný popis měření.
  Name  | Název, který identifikuje prostředek. Například název modelu nebo název produktu.
  Description  | Poskytněte smysluplný popis modelu.
  Vlastnosti  | Další vlastnosti od výrobce.
  **Elektrické**  | **Popis** |
  HardwareId  | Jedinečné ID pro senzory nastavené výrobcem.
  SensorModelId  | ID přidruženého modelu senzoru
  Umístění  | Zeměpisná šířka (-90 až + 90), zeměpisná délka (-180 až 180) a zvýšení úrovně (v metrech).
  Název > portu  |Název a typ portu, ke kterému je senzor připojen na zařízení. Tento název musí být stejný jako definovaný v modelu zařízení.
  DeviceId  | ID zařízení, ke kterému je senzor připojen.
  Name  | Název, který identifikuje prostředek. Například název senzoru nebo název produktu a číslo modelu nebo kód produktu.
  Description  | Zadejte smysluplný popis.
  Vlastnosti  | Další vlastnosti od výrobce.

 Informace o jednotlivých objektech a jejich vlastnostech naleznete v [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Rozhraní API vrací jedinečná ID pro každou vytvořenou instanci. Toto ID musí být zachováno překladatelem pro správu zařízení a synchronizaci metadat.


**Synchronizace metadat**

Překladatel by měl odesílat aktualizace metadat. Scénáře aktualizace jsou například změnou názvu zařízení nebo senzoru a změnou umístění zařízení nebo senzoru.

Překladatel by měl mít možnost přidávat nová zařízení nebo senzory, které byly nainstalovány uživatelem po propojení FarmBeats. Podobně platí, že pokud uživatel nebo senzor aktualizoval uživatel, měla by se stejná aktualizace aktualizovat v FarmBeats pro příslušné zařízení nebo senzor. Typické scénáře, které vyžadují aktualizaci zařízení nebo senzoru, jsou změnou umístění zařízení nebo přidáním senzorů v uzlu.


> [!NOTE]
> Odstranění se nepodporuje pro metadata zařízení nebo senzorů.
>
> Chcete-li aktualizovat metadata, je nutné volat/Get/{ID} na zařízení nebo senzoru, aktualizovat změněné vlastnosti a pak provést/Put/{ID}, aby všechny vlastnosti nastavené uživatelem nebyly ztraceny.

### <a name="add-new-types-and-units"></a>Přidat nové typy a jednotky

FarmBeats podporuje přidávání nových typů měření senzorů a jednotek. Další informace o rozhraní/ExtendedType API najdete v tématu [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="telemetry-specifications"></a>Specifikace telemetrie

Data telemetrie se mapují na kanonickou zprávu, která je publikovaná na Azure Event Hubs ke zpracování. Azure Event Hubs je služba, která umožňuje přijímání dat v reálném čase z připojených zařízení a aplikací.

## <a name="send-telemetry-data-to-farmbeats"></a>Posílání dat telemetrie do FarmBeats

Chcete-li odesílat data telemetrie do FarmBeats, vytvořte klienta, který odesílá zprávy do centra událostí v FarmBeats. Další informace o datech telemetrie najdete v tématu [posílání telemetrie do centra událostí](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Tady je ukázkový kód Pythonu, který odesílá telemetrii jako klienta do zadaného centra událostí.

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

Formát kanonické zprávy je následující:

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
Všechny názvy klíčů v kódu JSON telemetrie musí být malé písmeno. Příklady jsou DeviceID a sensordata.

Tady je například zpráva telemetrie:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
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

> [!NOTE]
> Následující oddíly souvisejí s jinými změnami (např. Uživatelské rozhraní, správa chyb atd.) že partner snímače může při vývoji komponenty překladatele vykázat.


## <a name="link-a-farmbeats-account"></a>Propojit účet FarmBeats

Jakmile si zákazníci zakoupili a nasadili zařízení nebo senzory, můžou získat přístup k datům a telemetrii zařízení na portálu SaaS (software jako služba) partnerů zařízení. Partneři zařízení můžou zákazníkům umožnit propojit svůj účet s instancí FarmBeats v Azure tím, že zajistí zadání následujících přihlašovacích údajů:

   - Zobrazovaný název (volitelné pole pro uživatele k definování názvu pro tuto integraci)
   - Koncový bod rozhraní API
   - ID tenanta
   - ID klienta
   - Tajný klíč klienta
   - Připojovací řetězec EventHub
   - Počáteční datum

   > [!NOTE]
   > Počáteční datum povoluje historický datový kanál, tedy data od data zadaného uživatelem.

## <a name="unlink-farmbeats"></a>Zrušit propojení FarmBeats

Partneři zařízení můžou zákazníkům povolit odpojení stávající integrace FarmBeats. Zrušení propojení FarmBeats by nemělo odstraňovat všechna zařízení nebo metadata senzorů vytvořená v FarmBeats DataHub. Zrušení propojení provede následující akce:

   - Zastaví tok telemetrie.
   - Odstraní a smaže přihlašovací údaje pro integraci na partnerovi zařízení.

## <a name="edit-farmbeats-integration"></a>Upravit integraci FarmBeats

Partneři zařízení můžou zákazníkům Povolit úpravu nastavení integrace FarmBeats, pokud se změní tajný kód klienta nebo připojovací řetězec. V takovém případě lze upravit pouze následující pole:

   - Zobrazované jméno (Pokud je k dispozici)
   - Tajný kód klienta (měl by se zobrazit ve formátu "2x8 * * * * * * * * * * *" nebo "Zobrazit/skrýt" místo nešifrovaný text)
   - Připojovací řetězec (měl by se zobrazit ve formátu "2x8 * * * * * * * * * * *" nebo Zobrazit/skrýt funkci místo nešifrovaný text)

## <a name="view-the-last-telemetry-sent"></a>Zobrazení poslední odeslané telemetrie

Partneři zařízení můžou zákazníkům povolit zobrazení časového razítka poslední doručené telemetrie, která se nachází na základě **odeslané telemetrie**. To je čas, kdy se úspěšně poslala nejnovější telemetrie do FarmBeats.

## <a name="troubleshooting-and-error-management"></a>Řešení potíží a správa chyb

**Řešení potíží s možností nebo podporou**

Pokud zákazník nemůže přijímat data zařízení nebo telemetrie v zadané instanci FarmBeats, partner zařízení by měl poskytovat podporu a mechanismus pro řešení potíží.

**Uchovávání dat telemetrie**

Data telemetrie by se měla uchovávat také pro předdefinované časové období, aby mohla být užitečná při ladění nebo opětovném odeslání telemetrie, pokud dojde k chybě nebo ztrátě dat.

**Správa chyb nebo oznamování chyb**

Pokud chyba ovlivňuje metadata zařízení nebo senzoru nebo tok dat pro integraci nebo telemetrie dat v partnerském systému zařízení, měl by zákazník obdržet oznámení. Mechanismus pro řešení všech chyb by měl být také navržen a implementován.

**Kontrolní seznam připojení**

Výrobci zařízení nebo partneři můžou pomocí následujícího kontrolního seznamu ověřit, jestli jsou přihlašovací údaje poskytnuté zákazníkem přesné:

   - Zkontrolujte, jestli je v zadaných přihlašovacích údajích přijatý přístupový token.
   - Zkontrolujte, jestli se volání rozhraní API zdaří pomocí přístupového tokenu, který jste dostali.
   - Zkontrolujte, zda je navázáno připojení klienta EventHub.

## <a name="next-steps"></a>Další kroky

Další informace o REST API najdete v tématu [REST API](rest-api-in-azure-farmbeats.md).