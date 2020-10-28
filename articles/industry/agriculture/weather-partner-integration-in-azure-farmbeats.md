---
title: Integrace partnerských řešení pro počasí
description: Tento článek popisuje, jak může zprostředkovatel dat počasí integrovat s FarmBeats
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: dd5d05ff6ed2368308f90f61ea0a6f107e43acd7
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740773"
---
# <a name="weather-partner-integration"></a>Integrace partnerských řešení pro počasí

Tento článek poskytuje informace o komponentě Docker **konektoru** služby Azure FarmBeats, kterou můžou poskytovatelé dat pro práci s FarmBeats vyvíjet, a to díky využití svých rozhraní API a posílání dat počasí do FarmBeats. Jakmile jsou data v FarmBeats k dispozici, je možné je použít pro syntézu dat a pro sestavování strojového učení nebo umělých analytických modelů.

 > [!NOTE]
 > Pro účely této dokumentace budeme používat referenční implementaci vytvořenou pomocí NOAA z Azure Open DataSets a je k dispozici na adrese [https://github.com/azurefarmbeats/noaa_docker](https://github.com/azurefarmbeats/noaa_docker) .
 > Odpovídající image Docker je k dispozici na adrese [https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)

Nepříznivý partner musí poskytnout Image nebo program Docker (se specifikacemi uvedenými níže) a hostovat image Docker v registru kontejneru, který je přístupný pro zákazníky. Informující partner bude muset zákazníkům poskytnout tyto informace:

- Adresa URL obrázku Docker
- Značka image Docker
- Klíče/přihlašovací údaje pro přístup k imagi Docker
- Klíče rozhraní API pro konkrétní zákazníky/přihlašovací údaje pro přístup k datům ze systému s nepříznivými partnery
- Podrobnosti o SKU virtuálního počítače (partneři to můžou poskytnout pro případ, že Docker má konkrétní požadavky na virtuální počítače, jinak si zákazníci můžou vybrat z podporovaných SKU virtuálních počítačů v Azure)

Pomocí výše uvedených informací Docker si zákazník zaregistruje počasí v instanci FarmBeats. Další informace o tom, jak zákazníci můžou k ingestování dat v FarmBeats používat Docker, najdete v průvodci pro [získání dat o počasí](./get-weather-data-from-weather-partner.md) .

## <a name="connector-docker-development"></a>Vývoj pro Docker konektoru

**Integrace na základě REST API**

Rozhraní API FarmBeats obsahují technickou dokumentaci k Swagger. Informace o všech rozhraních API a jejich odpovídajících požadavcích nebo odpovědích najdete v tématu [FarmBeats Swagger](https://aka.ms/farmbeatsswagger). 

Pokud jste nainstalovali FarmBeats, získáte přístup k FarmBeats Swagger na adrese `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Všimněte si, že rozhraní "-API" se připojuje k vašemu názvu webu FarmBeats.
Koncový bod rozhraní API bude: `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>DataHub lib

FarmBeats poskytne lib, kterou může použít partner pro počasí. Lib je aktuálně k dispozici jako součást implementace reference [zde](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib). V budoucnu bude k dispozici jako sada SDK pro více jazyků.

### <a name="authentication"></a>Authentication

**Ověřování pomocí rozhraní FarmBeats API**

FarmBeats používá ověřování pomocí nosiče a rozhraní API jsou přístupná poskytnutím přístupového tokenu v sekci hlavičky žádosti, jak je uvedeno níže:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Přístupový token se dá požádat z funkce Azure, která běží na instanci FarmBeats zákazníka. Adresa URL funkce Azure se poskytne programu Docker jako argument a přístupový token se dá získat tak, že na adrese URL vytvoříte požadavek GET. Odpověď z adresy URL bude obsahovat přístupový token. DataHub lib poskytuje pomocné funkce, které partnerům umožňují získat přístupový token. Další podrobnosti [najdete tady](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

Přístupový token je platný jenom pár hodin a po vypršení platnosti je potřeba ho znovu požádat.

**Ověřování s využitím partnerských rozhraní API na straně partnera**

Abychom zákazníkům umožnili ověřování pomocí rozhraní API na straně partnera během provádění Docker, musí přihlašovací údaje poskytnout během registrace partnera následujícím způsobem:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
Služba API tuto dict – zaserializace a uloží ji do [trezoru](../../key-vault/general/basic-concepts.md)klíčů.

[Azure Data Factory](../../data-factory/introduction.md) slouží k orchestraci úloh počasí a k provedení kódu Docker se vytočí prostředky. Poskytuje taky mechanismus pro zabezpečené odesílání dat do virtuálního počítače, na kterém se spouští úloha Docker. Přihlašovací údaje rozhraní API, ty, které jsou teď bezpečně uložené v trezoru klíčů, se čtou jako zabezpečené řetězce z trezoru klíčů a v pracovním adresáři kontejneru Docker jsou dostupné jako rozšířené vlastnosti, protože activity.jszapnuté (cesta k souboru je "/mnt/working_dir/activity.json"). kód Docker může číst přihlašovací údaje z tohoto souboru za běhu za účelem přístupu k rozhraním API na straně serveru jménem zákazníka. Přihlašovací údaje budou k dispozici v souboru následujícím způsobem:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
Všimněte si, že "partnerCredentials" bude k dispozici přesným způsobem, který poskytl zákazník během registrace partnera.

FarmBeats lib poskytuje pomocné funkce, které umožní partnerům číst přihlašovací údaje z vlastností aktivity. Další podrobnosti [najdete tady](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

Doba života souboru je pouze v průběhu provádění Docker Code a bude odstraněna po skončení běhu Docker.

Další informace o tom, jak kanály a aktivity služby ADF fungují, najdete v tématu [https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping](../../data-factory/copy-activity-schema-and-type-mapping.md) .

**Hlavičky požadavku HTTP**

Tady jsou nejběžnější hlavičky požadavků, které je potřeba zadat při volání rozhraní API do FarmBeats.

**Hlaviček** | **Popis a příklad**
--- | ---
Typ obsahu | Formát požadavku (Content-Type: Application/ <format> ). Pro rozhraní FarmBeats DataHub API je formát JSON. Content-Type: Application/JSON
Autorizace | Určuje přístupový token potřebný k vytvoření volání rozhraní API. Autorizace: nosič <Access-Token>
Přijmout | Formát odpovědi. Pro rozhraní FarmBeats DataHub API je formát JSON. Přijmout: Application/JSON

## <a name="data-format"></a>Formát dat

JSON je běžný jazyk nezávislý na jazyce, který poskytuje jednoduchou textovou reprezentaci libovolných datových struktur. Další informace najdete v tématu [JSON.org](http://json.org).

## <a name="docker-specifications"></a>Specifikace Docker

Program Docker musí mít dvě komponenty: **bootstrap** a **úlohy** . Může existovat více než jedna úloha.

### <a name="bootstrap"></a>Metoda bootstrap

Tato součást by se měla spustit, když zákazník zahájí registraci Docker na FarmBeats. Argumenty (arg1, arg2), které budou předány tomuto programu:

- Koncový bod rozhraní FarmBeats API: koncový bod rozhraní FarmBeats API pro požadavky rozhraní API: Toto je koncový bod pro volání rozhraní API do nasazení FarmBeats.
- Adresa URL funkce Azure: Jedná se o osobní koncový bod, který vám poskytne váš přístupový token pro rozhraní API FarmBeats. Pouhým voláním metody GET na této adrese URL se v odpovědi načte přístupový token.

V rámci zavedení je nutné vytvořit metadata požadavku, aby uživatelé mohli spouštět úlohy a získávat data o počasí. Odkaz na referenční implementaci najdete [tady](https://github.com/azurefarmbeats/noaa_docker). Můžete aktualizovat bootstrap_manifest.jsv souboru podle vašich potřeb a program pro spouštění odkazů vytvoří požadovaná metadata za vás.

V rámci tohoto procesu jsou vytvořena následující metadata. 

 > [!NOTE]
 > **Nezapomeňte prosím** , že pokud aktualizujete bootstrap_manifest.jsv souboru, jak je uvedeno v [referenční implementaci](https://github.com/azurefarmbeats/noaa_docker), nemusíte vytvářet níže uvedená metadata, protože spouštěcí rutina vytvoří stejný na základě vašeho souboru manifestu.

- /**WeatherDataModel** : WeatherDataModel je model reprezentující data v počasí a odpovídá různým datovým sadám poskytovaným zdrojem. DailyForecastSimpleModel může například poskytnout průměrnou teplotu, vlhkost a informace o vysrážení jednou za den, zatímco DailyForecastAdvancedModel může poskytnout mnohem více informací při hodinové členitosti. Můžete vytvořit libovolný počet WeatherDataModels.
- /**JobType** : FarmBeats má rozšiřitelný systém správy úloh. Jako poskytovatel dat počasí budete mít různé datové sady nebo rozhraní API (například GetDailyForecasts) – můžete je povolit v FarmBeats jako JobType. Po vytvoření JobType může zákazník aktivovat úlohy tohoto typu, aby získal data o počasí pro svoje umístění nebo farmu zájmu (viz JobType a rozhraní API v [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)).

### <a name="jobs"></a>Úlohy

Tato součást bude vyvolána pokaždé, když uživatel FarmBeats spustí úlohu vašeho/JobType, kterou jste vytvořili v rámci procesu Bootstrap. Příkaz Spustit jako Docker pro úlohu je definován jako součást **/JobType** , kterou jste vytvořili.
- Úkolem úlohy bude načítat data ze zdroje a vkládat je do FarmBeats. Parametry vyžadované k získání dat by měly být definovány jako součást/JobType v procesu Bootstrap.
- V rámci úlohy bude program muset vytvořit **/WeatherDataLocation** založenou na/WeatherDataModel, která byla vytvořena jako součást procesu Bootstrap. **/WeatherDataLocation** odpovídá umístění (lat/Long), které poskytuje uživatel jako parametr pro úlohu.

### <a name="details-of-the-objects"></a>Podrobnosti o objektech

  WeatherDataModel | Popis |
  --- | ---
  Název  | Název datového modelu počasí |
  Popis  | Poskytněte smysluplný popis modelu. |
  Vlastnosti  | Další vlastnosti definované poskytovatelem dat. |
  Název > weatherMeasures  | Název míry počasí Například humidity_max |
  weatherMeasures > datový typ  | buď typ Double, nebo Enum. Pokud je vyžadováno Enum, measureEnumDefinition |
  weatherMeasures > measureEnumDefinition  | Je vyžadován pouze v případě, že datový typ je Enum. Například {"prodeště": 0, "sněhu": 1, "drizzle": 2, "deště": 3} |
  Typ > weatherMeasures  | typ dat telemetrie počasí Například "RelativeHumidity". Níže jsou uvedené typy systému: AmbientTemperature, jednotka, CO2, Hloubka, ElectricalConductivity, LeafWetness, Length, LiquidLevel, dusičnan, O2, PH, fosforečnan, PointInTime, draselný, tlak, RainGauge, RelativeHumidity, slanost, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, WindDirection, nominální hodnota. Další informace najdete v tématu rozhraní/ExtendedType API nebo v [části Přidání typů a jednotek](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) níže.
  Jednotka > weatherMeasures | Jednotka počasí data telemetrie. Níže jsou uvedené jednotky definované systémem: jednotka, Celsia, Fahrenheita, Kelviny, Rankine, Pascal, rtuť, PSI, milimetr, centimetr, měřič, palce, nohy, míle, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, stupeň, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, s VolumetricIonContent, MilliLiter, UnixTimestamp a MicroMolPerMeterSquaredPerSecond. Další informace najdete v tématu rozhraní/ExtendedType API nebo níže v [části Přidání typů a jednotek](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) .
  weatherMeasures > AggregationType  | Buď None, Average, Max, minim, StandardDeviation, Sum, Total
  Hloubka > weatherMeasures  | Hloubka senzoru v centimetrech Například měření vlhkosti 10 cm pod vozovkou.
  Popis > weatherMeasures  | Poskytněte smysluplný popis měření. |
  **JobType** | **Popis** |
  Název  | název úlohy, například Get_Daily_Forecast; úloha, kterou zákazník spustí, aby získala data o počasí|
  pipelineDetails > parametry > název  | název parametru |
  pipelineDetails > parametry > typu | buď řetězec, int, float, bool, Array |
  parametry > pipelineDetails > je potřeba. | datového true, pokud je parametr povinný, false, pokud ne; Výchozí hodnota je true. |
  pipelineDetails > parametry > defaultValue | Výchozí hodnota parametru |
  pipelineDetails > parametry > popisu | Popis parametru |
  Vlastnosti  | Další vlastnosti od výrobce.
  Vlastnosti > **programRunCommand** | příkaz Spustit jako Docker – tento příkaz se spustí, když zákazník spustí úlohu počasí. |
  **WeatherDataLocation** | **Popis** |
  weatherDataModelId  | ID odpovídající WeatherDataModel, která byla vytvořena během Bootstrap|
  location  | představuje zeměpisnou šířku, délku a zvýšení úrovně oprávnění. |
  Název | Název objektu |
  Popis | Popis |
  farmId | **volitelné** ID farmy poskytované zákazníkem jako součást parametru úlohy |
  Vlastnosti  | Další vlastnosti od výrobce.

 Informace o jednotlivých objektech a jejich vlastnostech naleznete v [Swagger](https://aka.ms/FarmBeatsSwagger).

 > [!NOTE]
 > Rozhraní API vrací jedinečná ID pro každou vytvořenou instanci. Toto ID musí být zachováno překladatelem pro správu zařízení a synchronizaci metadat.

**Synchronizace metadat**

Docker konektoru by měl mít možnost odesílat aktualizace metadat. Příklady scénářů aktualizace: přidání nových parametrů počasí do datové sady poskytovatele počasí, přidání funkcí (např. Přidání 30 dní předpovědi)

> [!NOTE]
> Odstranění není pro metadata EG podporováno. datový model počasí.
>
> Chcete-li aktualizovat metadata, je nutné volat/Get/{ID} na datový model počasí, aktualizovat změněné vlastnosti a pak provést/Put/{ID}, aby všechny vlastnosti nastavené uživatelem nebyly ztraceny.

## <a name="weather-data-telemetry-specifications"></a>Specifikace dat počasí (telemetrie)

Data o počasí jsou namapována na kanonickou zprávu, která je vložena do centra událostí Azure ke zpracování. Azure Event Hubs je služba, která umožňuje přijímání dat v reálném čase z připojených zařízení a aplikací. K odeslání dat o počasí do FarmBeats budete muset vytvořit klienta, který odesílá zprávy do centra událostí v FarmBeats. Další informace o odesílání telemetrie najdete v tématu [odesílání telemetrie do centra událostí](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) .

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
   "weatherstations": [
   {
   "id": "id of the WeatherDataLocation",
   "weatherdata": [
   {
     "timestamp": "timestamp of the data. For historical, this is the time for which the observations are sent. For forecast this is the time for which data is forecasted. Format is ISO 8601. Default time-zone is UTC",
     "predictiontimestamp": "timestamp on which the forecast data is predicted i.e time of prediction. Required only for forecast data. Format is ISO 8601. Default timezone is UTC ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Tady je například zpráva telemetrie:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Řešení potíží a správa chyb

**Protokolování chyb**

Vzhledem k tomu, že se úloha partnerských úloh bude spouštět v existujícím prostředí úloh – chyby se budou protokolovat stejným způsobem jako chyby pro jiné již existující úlohy v FarmBeats (například GetFarmData, SensorPlacement atd.). Aktivita ADF spuštěná v kanálu ADF se zaznamená do protokolu STDERR i STDOUT. Oba soubory jsou k dispozici v účtu úložiště "datahublogs-XXX" v rámci skupiny prostředků FarmBeats.

DataHub lib poskytuje pomocné funkce pro povolení protokolování v rámci celkových protokolů DataHub. Další podrobnosti [najdete tady](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

**Řešení potíží s možností nebo podporou**

V případě, že zákazník nemůže přijímat data v počasí v zadané instanci FarmBeats, by měl partner s počasí poskytovat podporu a mechanismus pro řešení potíží se stejným.

## <a name="add-extendedtype"></a>Přidat ExtendedType

FarmBeats podporuje přidávání nových typů měření senzorů a jednotek. Uvědomte si, že partnerský partner může přidat nové jednotky a typy aktualizací bootstrap_manifest.jsna soubor v referenční implementaci [zde](https://github.com/azurefarmbeats/noaa_docker) .

Chcete-li přidat nový typ WeatherMeasure, například "PrecipitationDepth", postupujte podle následujících kroků.

1. Vytvoření požadavku GET v/ExtendedType pomocí filtru dotazu-Key = WeatherMeasureType
2. Poznamenejte si ID vráceného objektu.
3. Přidejte nový typ do seznamu v vráceném objektu a vytvořte na/ExtendedType{ID} požadavek PUT s následujícím novým seznamem. Vstupní datová část by měla být stejná jako odpověď přijatá výše a nová jednotka na konci seznamu hodnot byla připojena.

Další informace o rozhraní/ExtendedType API najdete v tématu [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Další kroky

Teď máte k dispozici Docker konektoru, který se integruje s FarmBeats. Dál uvidíte, jak získat data o počasí pomocí Docker do FarmBeats. Viz [získat data o počasí](get-weather-data-from-weather-partner.md).
