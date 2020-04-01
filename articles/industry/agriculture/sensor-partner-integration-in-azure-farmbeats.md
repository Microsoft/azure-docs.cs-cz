---
title: Integrace partnerských řešení pro senzory
description: Tento článek popisuje integraci partnerů senzorů.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3431576acbb01a0cc3a5f372460b28be05bf7ce7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437468"
---
# <a name="sensor-partner-integration"></a>Integrace partnerských řešení pro senzory

Tento článek obsahuje informace o součásti Azure FarmBeats **Translator,** která umožňuje integraci partnerů senzorů.

Pomocí této součásti mohou partneři integrovat s FarmBeats pomocí rozhraní API FarmBeats Datahub a odesílat data a telemetrii zákazníků společnosti FarmBeats Datahub. Jakmile jsou data k dispozici v FarmBeats, je vizualizována pomocí FarmBeats Accelerator a může být použita pro fúzi dat a pro vytváření modelů strojového učení / umělé inteligence.

## <a name="before-you-start"></a>Než začnete

Chcete-li vyvinout komponentu Translator, budete potřebovat následující pověření, která umožní přístup k maskám FarmBeats.

- Koncový bod rozhraní API
- ID tenanta
- ID klienta
- Tajný klíč klienta
- Připojovací řetězec EventHub

V této části naleznete získání výše uvedených přihlašovacích údajů: [Povolit integraci zařízení](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Vývoj překladatelů

**Integrace založená na REST API**

Možnosti integrace dat senzorů FarmBeats jsou vystaveny prostřednictvím REST API. Mezi funkce patří definice metadat, zřizování zařízení a senzorů a správa zařízení a senzorů.

**Požití telemetrie**

Telemetrická data se namapují na kanonickou zprávu, která se publikuje v Azure Event Hubs pro zpracování. Azure Event Hubs je služba, která umožňuje sdílení dat v reálném čase (telemetrie) z připojených zařízení a aplikací.

**Vývoj rozhraní API**

Api obsahují swagger technickou dokumentaci. Další informace o api a jejich odpovídající požadavky nebo odpovědi, naleznete v tématu [Swagger](https://aka.ms/FarmBeatsSwagger).

**Authentication**

FarmBeats používá ověřování služby Microsoft Azure Active Directory.Služba Azure App Service poskytuje integrovanou podporu ověřování a autorizace.

Další informace naleznete v [tématu Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

FarmBeats Datahub používá ověřování nosiče, které vyžaduje následující pověření:
   - ID klienta
   - Tajný klíč klienta
   - ID tenanta

Pomocí těchto pověření může volající požádat o přístupový token. Token musí být odeslán v následujících požadavcích rozhraní API v části záhlaví takto:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Následující ukázkový kód Pythonu poskytuje přístupový token, který lze použít pro následné volání rozhraní API farmbeats.

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

Zde jsou nejčastější hlavičky požadavků, které je třeba zadat při volání rozhraní API farmbeats Datahub.


**Záhlaví** | **Popis a příklad**
--- | ---
Typ obsahu | Formát požadavku (Content-Type:<format>application/ ). Pro FarmBeats Datahub API formát je JSON. Typ obsahu: aplikace/json
Autorizace | Určuje přístupový token potřebný k volání rozhraní API. Autorizace:> přístupového tokenu <nosiče
Accept | Formát odpovědi. Pro FarmBeats Datahub API formát je JSON. Přijmout: aplikace/json

**Požadavky rozhraní API**

Chcete-li vytvořit požadavek rozhraní REST API, zkombinujte metodu HTTP (GET, POST nebo PUT), adresu URL služby rozhraní API, identifikátor URI (Uniform Resource Identifier) s prostředkem k dotazování, odesílání dat, aktualizaci nebo odstranění a jednu nebo více záhlaví požadavků HTTP. Adresa URL služby rozhraní API je koncový bod rozhraní API, který zadáte. Tady je ukázka:\<https:// yourdatahub-website-name>.azurewebsites.net

Volitelně můžete zahrnout parametry dotazu na volání GET pro filtrování, omezení velikosti a řazení dat v odpovědích.

Následující ukázkový požadavek je získat seznam zařízení.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
Většina get, post a PUT volání vyžadují tělo požadavku JSON.

Následující ukázkový požadavek je vytvořit zařízení. (Tato ukázka má vstupní JSON s tělem požadavku.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formát dat

JSON je společný jazykově nezávislý formát dat, který poskytuje jednoduchou textovou reprezentaci libovolných datových struktur. Další informace naleznete [v tématu json.org](http://json.org).

## <a name="metadata-specifications"></a>Specifikace metadat

FarmBeats Datahub má následující rozhraní API, která umožňují partnerům zařízení vytvářet a spravovat metadata zařízení nebo senzoru.

- /**DeviceModel**: DeviceModel odpovídá metadatům zařízení, jako je například výrobce a typ zařízení, což je brána nebo uzel.
- /**Zařízení**: Zařízení odpovídá fyzickému zařízení přítomnému na farmě.
- /**SensorModel**: SensorModel odpovídá metadatům senzoru, jako je výrobce, typ senzoru, který je analogový nebo digitální, a měření senzoru, jako je okolní teplota a tlak.
- /**Senzor**: Senzor odpovídá fyzickému senzoru, který zaznamenává hodnoty. Senzor je obvykle připojen k zařízení s ID zařízení.

  **DeviceModel** |  |
  --- | ---
  Typ (uzel, brána)  | Typ zařízení – uzel nebo brána |
  Výrobce  | Jméno výrobce |
  Productcode  | Kód produktu zařízení nebo název nebo číslo modelu. Například EnviroMonitor#6800. |
  Porty  | Název a typ portu, který je digitální nebo analogový.  |
  Name (Název)  | Název k identifikaci prostředku. Například název modelu nebo název produktu. |
  Popis  | Zadejte smysluplný popis modelu. |
  Vlastnosti  | Další vlastnosti od výrobce. |
  **Zařízení** |  |
  DeviceModelId  |ID přidruženého modelu zařízení. |
  Id hardwaru   |Jedinečné ID zařízení, například adresu MAC.  |
  Interval hlášení |Interval hlášení v sekundách. |
  Umístění    |Zeměpisná šířka zařízení (-90 až +90), zeměpisná délka (-180 až 180) a nadmořská výška (v metrech). |
  Id nadřazeného zařízení | ID nadřazeného zařízení, ke kterému je toto zařízení připojeno. Například pokud uzel je připojen k bráně, uzel má parentDeviceID jako bránu. |
  Name (Název)  | Název k identifikaci prostředku. Partneři zařízení musí odeslat název, který je konzistentní s názvem zařízení na straně partnera zařízení. Pokud je název zařízení definován uživatelem na straně partnera zařízení, měl by být stejný název definovaný uživatelem rozšířen na FarmBeats.  |
  Popis  | Poskytněte smysluplný popis.  |
  Vlastnosti  |Další vlastnosti od výrobce.  |
  **SenzorModel** |  |
  Typ (analogový, digitální)  |Zmiňte analogový nebo digitální senzor.|
  Výrobce  | Jméno výrobce. |
  Productcode  | Kód produktu nebo název nebo číslo modelu. Například RS-CO2-N01.  |
  Název > sensormeasures  | Název měry snímače. Podporována jsou pouze malá písmena. Pro měření z různých hloubek určete hloubku. Například soil_moisture_15cm. Tento název musí být konzistentní s telemetrická data. |
  SensorMeasures > datový typ  | Datový typ telemetrie. V současné době double je podporována. |
  Typ > senzorůměří  | Typ měření telemetrických dat senzoru. Níže jsou definovány typy systému: AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Dusičnan, O2, PH, Fosfát, PointInTime, Draslík, Tlak, RainGauge, RelativníVlhkost, Slanost, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapopirtransation, PAR. Chcete-li přidat další, naleznete rozhraní /ExtendedType API.
  SenzorOpatření > jednotka | Jednotka telemetrických dat senzoru. Níže jsou systémy definované jednotky: NoUnit, Celsia, Fahrenheita, Kelvin, Rankine, Pascal, Merkur, PSI, Milimetr, Centimetr, Metr, Palec, Nohy, Mile, Kilometr, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Stupeň, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMoleSPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterDPerSecond, aPerHour. Chcete-li přidat další, naleznete rozhraní /ExtendedType API.
  SensorMeasures > AggregationType  | Buď žádný, průměr, maximum, minimum nebo Odchylka podle směrovek.
  SensorMeasures > hloubka  | Hloubka senzoru v centimetrech. Například měření vlhkosti 10 cm pod zemí.
  Popis > senzorů  | Uveďte smysluplný popis měření.
  Name (Název)  | Název k identifikaci prostředku. Například název modelu nebo název produktu.
  Popis  | Zadejte smysluplný popis modelu.
  Vlastnosti  | Další vlastnosti od výrobce.
  **Senzor**  |  |
  Id hardwaru  | Jedinečné ID senzoru nastavené výrobcem.
  SensorModelId  | ID přidruženého modelu senzoru.
  Umístění  | Zeměpisná šířka senzoru (-90 až +90), zeměpisná délka (-180 až 180) a nadmořská výška (v metrech).
  Název > portu  |Název a typ portu, ke kterému je senzor připojen v zařízení. Musí se jednat o stejný název, který je definován v modelu zařízení.
  DeviceId  | ID zařízení, ke kterému je senzor připojen.
  Name (Název)  | Název k identifikaci prostředku. Například název senzoru nebo název produktu a číslo modelu nebo kód produktu.
  Popis  | Poskytněte smysluplný popis.
  Vlastnosti  | Další vlastnosti od výrobce.

 Informace o jednotlivých objektech a jejich vlastnostech naleznete v tématu [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Api vrátí jedinečné ID pro každou vytvořenou instanci. Toto ID musí být zachováno překladatelpro správu zařízení a synchronizaci metadat.


**Synchronizace metadat**

Překladatel by měl odesílat aktualizace metadat. Scénáře aktualizace jsou například změna názvu zařízení nebo senzoru a změna umístění zařízení nebo senzoru.

Překladatel by měl mít možnost přidávat nová zařízení nebo senzory, které byly nainstalovány uživatelem post propojení FarmBeats. Podobně, pokud zařízení nebo senzor byl aktualizován uživatelem, totéž by mělo být aktualizováno v FarmBeats pro odpovídající zařízení nebo senzor. Typické scénáře, které vyžadují aktualizaci zařízení nebo senzoru, jsou změna umístění zařízení nebo přidání senzorů v uzlu.


> [!NOTE]
> Odstranění není podporováno pro metadata zařízení nebo senzoru.
>
> Chcete-li aktualizovat metadata, je nutné volat /Get/{id} na zařízení nebo senzoru, aktualizovat změněné vlastnosti a pak provést /Put/{id} tak, aby nebyly ztraceny žádné vlastnosti nastavené uživatelem.

### <a name="add-new-types-and-units"></a>Přidání nových typů a jednotek

FarmBeats podporuje přidávání nových typů a jednotek senzorových měr. Další informace o rozhraní /ExtendedType API naleznete v tématu [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="telemetry-specifications"></a>Specifikace telemetrie

Telemetrická data se namapují na kanonickou zprávu, která se publikuje v Azure Event Hubs pro zpracování. Azure Event Hubs je služba, která umožňuje sdílení dat v reálném čase (telemetrie) z připojených zařízení a aplikací.

## <a name="send-telemetry-data-to-farmbeats"></a>Odesílání telemetrických dat společnosti FarmBeats

Chcete-li odeslat telemetrická data do FarmBeats, vytvořte klienta, který odesílá zprávy do centra událostí v FarmBeats. Další informace o telemetrických datech naleznete [v tématu Odesílání telemetrie do centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Tady je ukázkový kód Pythonu, který odesílá telemetrii jako klientdo zadaného centra událostí.

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
Všechny názvy klíčů v telemetrické JSON by měla být malá písmena. Příklady jsou deviceid a sensordata.

Například zde je telemetrická zpráva:


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
> Následující části se vztahují k dalším změnám (např. ui, správa chyb atd.) může partner senzoru odkazovat při vývoji komponenty Translator.


## <a name="link-a-farmbeats-account"></a>Propojení účtu FarmBeats

Poté, co zákazníci zakoupili a nasadili zařízení nebo senzory, mají přístup k datům zařízení a telemetrii na portálu partnerů zařízení jako na portálu služby (SaaS). Partneři zařízení mohou zákazníkům umožnit propojit svůj účet s instancí FarmBeats v Azure tím, že vám poskytnou způsob, jak zadat následující přihlašovací údaje:

   - Zobrazovaný název (volitelné pole pro uživatele k definování názvu pro tuto integraci)
   - Koncový bod rozhraní API
   - ID tenanta
   - ID klienta
   - Tajný klíč klienta
   - Připojovací řetězec EventHub
   - Počáteční datum

   > [!NOTE]
   > Počáteční datum umožňuje historický zdroj dat, to znamená data z data určeného uživatelem.

## <a name="unlink-farmbeats"></a>Odpojte FarmBeats

Partneři zařízení mohou zákazníkům umožnit odpojit existující integraci FarmBeats. Odpojení FarmBeats by nemělo odstranit žádné zařízení nebo senzor metadata, která byla vytvořena v FarmBeats Datahub. Odpojení provádí následující akce:

   - Zastaví tok telemetrie.
   - Odstraní a vymaže přihlašovací údaje integrace na partnera zařízení.

## <a name="edit-farmbeats-integration"></a>Upravit integraci FarmBeats

Partneři zařízení mohou zákazníkům umožnit upravit nastavení integrace FarmBeats, pokud se změní tajný klíč klienta nebo připojovací řetězec. V tomto případě lze upravovat pouze následující pole:

   - Zobrazovaný název (je-li k dispozici)
   - Tajný klíč klienta (by měl být zobrazen ve formátu "2x8*************" nebo funkci Zobrazit/Skrýt spíše než ve formátu prostého textu)
   - Připojovací řetězec (by měl být zobrazen ve formátu "2x8*************" nebo Zobrazit/Skrýt funkci spíše než jasný text)

## <a name="view-the-last-telemetry-sent"></a>Zobrazení poslední odeslané telemetrie

Partneři zařízení mohou zákazníkům povolit zobrazení časového razítka poslední telemetrie, která byla odeslána, která se nachází v části **Odeslaná telemetrie**. Toto je čas, kdy byla nejnovější telemetrie úspěšně odeslána do FarmBeats.

## <a name="troubleshooting-and-error-management"></a>Poradce při potížích a správa chyb

**Možnost řešení potíží nebo podpora**

Pokud zákazník není schopen přijímat data zařízení nebo telemetrie v instanci FarmBeats zadané, partner zařízení by měl poskytnout podporu a mechanismus pro řešení potíží.

**Uchovávání telemetrických dat**

Telemetrická data by měla být také zachována pro předdefinované časové období, aby mohla být užitečná při ladění nebo opětovném odeslání telemetrie, pokud dojde k chybě nebo ztrátě dat.

**Správa chyb nebo oznámení o chybě**

Pokud chyba ovlivní metadata zařízení nebo senzoru nebo integraci dat nebo tok telemetrických dat v partnerském systému zařízení, měl by zákazník obdržet oznámení. Měl by být rovněž navržen a implementován mechanismus pro řešení případných chyb.

**Kontrolní seznam připojení**

Výrobci nebo partneři zařízení mohou pomocí následujícího kontrolního seznamu zajistit, že přihlašovací údaje poskytnuté zákazníkem jsou přesné:

   - Zkontrolujte, zda je přijat přístupový token s pověřeními, které byly poskytnuty.
   - Zkontrolujte, zda volání rozhraní API proběhne úspěšně s přístupovým tokenem, který byl přijat.
   - Zkontrolujte, zda je navázáno připojení klienta EventHub.

## <a name="next-steps"></a>Další kroky

Další informace o rozhraní REST API naleznete v tématu [REST API](rest-api-in-azure-farmbeats.md).
