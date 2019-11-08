---
title: Integrace partnerů senzorů
description: Popisuje integraci partnera se senzory.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e7de815b7254fb071b3094f9ae636b712b38684b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797683"
---
# <a name="sensor-partner-integration"></a>Integrace partnerů senzorů
Tento článek poskytuje informace o komponentě **překladače** Azure FarmBeats.

Pomocí této součásti můžou partneři vyvíjet senzory, které se integrují se službou FarmBeats, využitím našeho rozhraní API a posíláním dat a telemetrie zákaznických zařízení do datového centra FarmBeats. Data se vizuálně používají v akcelerátoru FarmBeats. Data se dají použít pro datovou syntézu a pro vytváření modelů počítačů a umělých analytických modelů.

## <a name="link-farmbeats-account"></a>Propojit účet FarmBeats

Jakmile si zákazníci zakoupili a nasadili zařízení/senzory, budou mít přístup k datům a telemetrie zařízení na portálu SaaS (software jako služba). Partneři zařízení musí umožnit zákazníkům propojit svůj účet s instancí FarmBeats v Azure. K vyplnění uživateli/SI musí být k dispozici následující přihlašovací údaje:

   - Zobrazovaný název (volitelné pole pro uživatele k definování názvu pro tuto integraci)
   - Koncový bod rozhraní API
   - ID tenanta
   - ID klienta
   - Tajný klíč klienta
   - Připojovací řetězec EventHub
   - Počáteční datum

   > [!NOTE]
   > Počáteční datum povoluje historický datový kanál, tj. data od data zadaného uživatelem.

## <a name="unlink-farmbeats"></a>Zrušit propojení FarmBeats

Zákazníci mají možnost odpojit stávající integraci FarmBeats. Zrušení propojení FarmBeats by nemělo odstraňovat žádná metadata zařízení/senzorů vytvořená v centru dat zákazníka. Zrušení propojení provede následující akce:

   - Zastaví tok telemetrie.
   - Odstraní a smaže přihlašovací údaje integrace na partnerovi zařízení.

## <a name="edit-farmbeats-integration"></a>Upravit integraci FarmBeats

Zákazník může upravit integraci FarmBeats. Primární scénář pro úpravy je, když se změní tajný kód klienta nebo připojovací řetězec z důvodu vypršení platnosti, v tomto případě zákazník může upravovat jenom následující pole.

   - Zobrazované jméno (Pokud je k dispozici)
   - Tajný kód klienta (měl by se zobrazit ve formátu "2x8 * * * * * * * * * * *" nebo Zobrazit/skrýt funkci místo nešifrovaný text)
   - Připojovací řetězec (měl by se zobrazit ve formátu "2x8 * * * * * * * * * * *" nebo Zobrazit/skrýt funkci místo nešifrovaný text)

   > [!NOTE]
   > Edit by neměl přerušit vytváření objektů metadat.

## <a name="view-last-telemetry-sent"></a>Zobrazit poslední odeslané telemetrie

Můžete zobrazit časové razítko poslední **odeslané telemetrie**. To je čas, kdy se úspěšně poslala nejnovější telemetrie do FarmBeats.

## <a name="translator-development"></a>Vývoj překladatelů

**Integrace založená na rozhraní REST API**

Možnosti integrace dat ze senzorů FarmBeats jsou zpřístupněny prostřednictvím REST API. K dispozici jsou možnosti definice metadat, zřizování zařízení/senzorů, zařízení a správa senzorů.

**Ingestování telemetrie**

Data telemetrie se mapují na kanonickou zprávu, která je publikovaná v centru událostí Azure ke zpracování. Azure EventHub je služba, která umožňuje přijímání dat v reálném čase z připojených zařízení a aplikací.

**Vývoj rozhraní API**

Rozhraní API obsahují technickou dokumentaci k Swagger. Další informace o rozhraních API a jejich odpovídajících požadavcích a odpovědích najdete v [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) .

**Ověřování**

FarmBeats využívá ověřování služby Active Directory Microsoft Azure. Azure App Service poskytuje integrovanou podporu ověřování a autorizace.

Další informace najdete v tématu [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

FarmBeats data hub používá ověřování pomocí nosiče, které potřebuje následující přihlašovací údaje:
   - ID klienta
   - Tajný kód klienta
   - ID tenanta

Pomocí výše uvedených přihlašovacích údajů může volající požádat o přístupový token, který se musí poslat v následujících žádostech o rozhraní API v části záhlaví, jak je znázorněno níže:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Níže je uveden vzorový kód Pythonu, který poskytuje přístupový token, který se dá použít k následnému volání rozhraní API do FarmBeats: 

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

Tady jsou nejčastější hlavičky požadavků, které je potřeba zadat při volání rozhraní API FarmBeats data hub:


**Hlavička** | **Popis a příklad**
--- | ---
Typ obsahu | Formát požadavku (Content-Type: Application/<format>) pro formát rozhraní API datového centra FarmBeats je JSON. Content-Type: Application/JSON
Autorizace | Určuje přístupový token vyžadovaný pro autorizaci volání rozhraní API: Bearer < Access-token >
vyjádřit | Formát odpovědi. Pro rozhraní API centra dat FarmBeats je formátem přijetí JSON: Application/JSON.

**Požadavky rozhraní API**

Chcete-li udělat požadavek rozhraní API REST (representational state transfer), zkombinujete metodu HTTP (GET, POST nebo PUT), adresu URL ke službě API, identifikátor URI (Uniform Resource Identifier) prostředku pro dotazování, odesílání dat do, aktualizaci nebo odstranění a jednu nebo více požadavků HTTP. záhlaví. Adresa URL služby API je koncovým bodem rozhraní API, který poskytuje zákazník. Tady je ukázka: https://\<yourdatahub-website-název >. azurewebsites. NET

Volitelně můžete zahrnout parametry dotazu pro volání funkce GET k filtrování, omezení velikosti a řazení dat v odpovědích.

Níže uvedená ukázková žádost slouží k získání seznamu zařízení:

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```
Většina volání GET, POST a PUT vyžaduje tělo požadavku JSON.

Níže uvedená ukázková žádost je vytvořit zařízení (Tato ukázka obsahuje vstupní kód JSON s textem žádosti).

```
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formát dat

JSON (JavaScript Object Notation) je běžný jazyk nezávislý na jazyce, který poskytuje jednoduchou textovou reprezentaci libovolných datových struktur. Další informace najdete v tématu [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Specifikace metadat

FarmBeats data hub má následující rozhraní API, která partnerům zařízení umožňují vytvářet a spravovat metadata zařízení a senzorů.  

- /**DeviceModel** -Device model odpovídá metadatům zařízení, jako je výrobce, typ zařízení buď bránu, nebo uzel.  
- zařízení /**zařízení** odpovídá fyzickému zařízení přítomnému ve farmě.
- model /**SensorModel** -snímač odpovídá meta údajům snímačů, jako je výrobce, typ snímače buď analogového, nebo digitálního, měření snímačů, jako je okolní teplota, tlak atd.
- senzor /**senzorů** odpovídá fyzickému senzoru, který zaznamenává hodnoty. Senzor se obvykle připojuje k zařízení s ID zařízení.

  Model zařízení| DeviceModel odpovídá metadatům zařízení, jako je výrobce, typ zařízení buď bránu, nebo uzel.
  --- | ---
  Typ (uzel, brána)  | 1 hvězdička |
  Výrobce  | 2 hvězdičky |
  ProductCode  | Kód produktu zařízení nebo název/číslo modelu. Například EnviroMonitor # 6800 |
  Porty  | Název a typ portu (digitální/analogový)  |
  Název  | Název, který identifikuje prostředek. Například název modelu/název produktu |
  Popis  | Zadejte smysluplný popis modelu. |
  Vlastnosti  | Další vlastnosti od výrobce |
  **Zařízení** | **Zařízení odpovídá fyzickému zařízení přítomnému ve farmě. Každé zařízení má jedinečné ID zařízení.** |
DeviceModelId  |ID přidruženého modelu zařízení |
hardwareId   |Jedinečné ID zařízení, například adresa MAC atd.  |
reportingInterval |Interval generování sestav v sekundách |
Umístění    |Zeměpisná šířka zařízení (-90 až + 90)/longitude (-180 až 180)/Elevation (v metrech) |
ParentDeviceId | ID nadřazeného zařízení, ke kterému je připojeno toto zařízení Například. Uzel připojený k bráně; uzel bude mít parentDeviceID jako bránu. |
  Název  | Název, který identifikuje prostředek.  Partneři zařízení budou muset Odeslat název, který je konzistentní s názvem zařízení na straně partnera zařízení. Pokud je název zařízení definovaný uživatelem na straně partnera zařízení, stejný uživatelsky definovaný název by měl být šířen na FarmBeats  |
  Popis  | Zadejte smysluplný popis.  |
  Vlastnosti  |Další vlastnosti od výrobce  |
  **Model senzorů** | SensorModel odpovídá metadatům senzorů, jako je výrobce, typ snímače buď analogového, nebo digitálního, měření senzorů, jako je například pokojová teplota, tlak atd. |
  Typ (analogové, digitální)  |Zmínky o analogovém nebo digitálním senzoru|
  výrobců  | jméno výrobce |
  ProductCode  | Kód produktu nebo název/číslo modelu Například RS-CO2-N01  |
  Název > SensorMeasures  | Název míry senzoru Podporuje se jenom malá písmena. Pro měření z různých hloubek Určete hloubku. Například soil_moisture_15cm tento název musí být v souladu s daty telemetrie. |
  SensorMeasures > datový typ  | Datový typ telemetrie. Aktuálně je podporován typ Double.  |
  Typ > sensorMeasures  | Typ měření dat telemetrie senzorů. Níže jsou uvedené typy systému: AmbientTemperature, CO2, Hloubka, ElectricalConductivity, LeafWetness, Length, LiquidLevel, dusičnan, O2, PH, fosforečnan, PointInTime, draselný, tlak, RainGauge, RelativeHumidity, slanost, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, evapotranspiration, PAR. Pokud chcete přidat víc, podívejte se na/ExtendedType API.
  Jednotka > SensorMeasures | Jednotka dat telemetrie snímačů. Níže jsou uvedené jednotky definované systémem: jednotka jednotek, Celsia, Fahrenheita, Kelviny, Rankine, Pascal, rtuť, Psí, milimetry, centimetry, měřiče, palce, nohy, míle, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, stupeň, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, procenta, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litr, MilliLiter, sekundy, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour, další informace najdete na/ Rozhraní API pro ExtendedType
  SensorMeasures > aggregationType  | Buď žádná, průměrná, maximální, minimální, StandardDeviation
  Hloubka > SensorMeasures  | Hloubka senzoru v centimetrech (například měření vlhkosti 10 cm pod vozovkou)
  Popis > sensorMeasures  | Zadejte smysluplný popis míry.
  jméno  | Název, který identifikuje prostředek. Například název modelu/název produktu
  description  | Zadejte smysluplný popis modelu.
  properties  | Další vlastnosti od výrobce
  **Elektrické**  |
  hardwareId  | Jedinečné ID pro senzory nastavené výrobcem
  sensorModelId  | ID přidruženého modelu senzoru
  location  | Zeměpisná šířka (-90 až + 90)/longitude (-180 až 180)/Elevation (v metrech)
  název > portu  |Název a typ portu, ke kterému je senzor připojen na zařízení. Tento název musí být stejný jako definovaný v modelu zařízení.
  deviceId  | ID zařízení, ke kterému je senzor připojený
  jméno  | Název, který identifikuje prostředek. Například název snímače/název produktu a číslo modelu/kód produktu.
  description  | Zadejte smysluplný popis.
  properties  | Další vlastnosti od výrobce

 Informace o jednotlivých objektech a jejich vlastnostech naleznete v [Swagger](httpa://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Rozhraní API vrací jedinečná ID pro každou vytvořenou instanci. Toto ID musí být zachováno překladatelem pro správu zařízení a synchronizaci metadat.


**Synchronizace metadat**

Překladatel by měl odesílat aktualizace metadat. Například scénáře aktualizace jsou – změna názvu zařízení/senzoru, změna umístění zařízení/senzoru.

Překladatel by měl mít možnost přidávat nová zařízení nebo senzory, které byly nainstalovány uživatelem po propojení FarmBeats. Podobně platí, že pokud uživatel nebo senzor aktualizoval uživatel, měl by být stejný, jako by byl aktualizován v FarmBeats pro odpovídající zařízení/senzor. Typické scénáře aktualizace zařízení/senzoru můžou být: Změna umístění zařízení, přídavek senzorů v uzlu atd.


> [!NOTE]
> Odstranění není pro metadata zařízení/senzoru podporováno.
>
> Chcete-li aktualizovat metadata, je nutné volat/Get/{ID} na zařízení nebo senzoru, aktualizovat změněné vlastnosti a pak provést/Put/{ID}, aby nedošlo ke ztrátě všech vlastností nastavených uživatelem.

### <a name="adding-new-typesunit"></a>Přidávání nových typů/jednotek

FarmBeats podporuje přidávání nových typů měření senzorů a jednotek. Další informace o rozhraní/ExtendedType API najdete v [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="telemetry-specifications"></a>Specifikace telemetrie

Data telemetrie se mapují na kanonickou zprávu, která je publikovaná v centru událostí Azure ke zpracování. Azure EventHub je služba, která umožňuje přijímání dat v reálném čase z připojených zařízení a aplikací.

## <a name="send-telemetry-data-to-farmbeats"></a>Posílání dat telemetrie do FarmBeats

Chcete-li odesílat data telemetrie do FarmBeats, budete muset vytvořit klienta, který odesílá zprávy do centra událostí v FarmBeats. Další informace o datech telemetrie najdete v tématu [posílání telemetrie do centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

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

Formát kanonické zprávy je následující:

```
{
“deviceid”: “<id of the Device created>”,
 "timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>”
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": value
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": value
        }
      ]
    }
}

```

Všechny názvy klíčů v kódu JSON telemetrie by měly být malá, například DeviceID, sensordata atd.

Například zpráva telemetrie:


```
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

## <a name="troubleshooterror-management"></a>Řešení potíží a správa chyb

**Řešení potíží s možností/podpora**

V případě, že zákazník nemůže přijímat data zařízení nebo telemetrie v zadané instanci FarmBeats, musí partner zařízení poskytovat podporu a mechanismus pro řešení potíží se stejným.

**Uchovávání dat telemetrie**

Data telemetrie by se měla uchovávat i pro předem definované časové období, aby totéž mohlo být užitečné při ladění nebo opětovném odeslání telemetrie v případě chyby nebo ztráty dat.

**Správa chyb/oznámení o chybách**

V případě chyby, která má vliv na metadata zařízení/senzoru/data o integraci nebo telemetrie dat v partnerském systému zařízení, by mělo být stejné oznámení zákazníkovi. Mechanismus pro řešení těchto chyb by měl být také navržen a implementován.

**Kontrolní seznam připojení**

Výrobci zařízení/partneři můžou mít následující správnosti test/kontrolní seznam, aby zajistili, že přihlašovací údaje poskytnuté zákazníkem jsou přesné.

   - Ověřte, jestli se k zadaným přihlašovacím údajům neobdržel přístupový token.
   - Ověřte, jestli se volání rozhraní API zdaří s obdrženým přístupovým tokenem.
   - Ověřte, jestli je navázáno připojení klienta EventHub.

## <a name="next-steps"></a>Další kroky

Další informace o REST API najdete v tématu [REST API](references-for-farmbeats.md#rest-api).
