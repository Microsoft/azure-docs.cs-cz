---
title: Integrace partnerských řešení pro počasí
description: Přečtěte si, jak může zprostředkovatel dat počasí integrovat s FarmBeats.
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93147075"
---
# <a name="weather-partner-integration-with-farmbeats"></a>Integrace s informacemi o počasí s FarmBeats

Tento článek poskytuje informace o komponentě Docker konektoru Azure FarmBeats. Jako poskytovatel dat počasí můžete použít Docker konektoru pro integraci s FarmBeats. Použijte jeho rozhraní API k posílání dat o počasí do FarmBeats. V FarmBeats se data dají použít pro fúze dat a vytváření modelů strojového učení nebo umělých analytických modelů.

 > [!NOTE]
 > V tomto článku používáme [referenční implementaci](https://github.com/azurefarmbeats/noaa_docker) , která byla sestavená pomocí otevřených datových sad Azure a dat počasí z národního oceán a NOAA (). Používáme také odpovídající [Image Docker](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa).

Musíte poskytnout [vhodnou Image nebo program Docker](#docker-specifications) a hostovat image Docker v registru kontejnerů, ke kterým mají uživatelé přístup. Poskytněte zákazníkům tyto informace:

- Adresa URL obrázku Docker
- Značka image Docker
- Klíče nebo přihlašovací údaje pro přístup k imagi Docker
- Klíče rozhraní API pro konkrétní zákazníky nebo přihlašovací údaje pro přístup k datům z vašeho systému
- Podrobnosti o SKU virtuálního počítače (tyto podrobnosti uveďte v případě, že image Docker má konkrétní požadavky na virtuální počítače). V opačném případě můžou zákazníci vybírat z podporovaných SKU virtuálních počítačů v Azure.)

Zákazníci používají tyto informace Docker k registraci spoluklimatického partnera v instanci FarmBeats. Další informace o tom, jak můžou zákazníci používat Docker k ingestování dat v FarmBeats, najdete v tématu [získání dat od partnerů počasí](./get-weather-data-from-weather-partner.md).

## <a name="connector-docker-development"></a>Vývoj pro Docker konektoru

**Integrace na základě REST API**

Rozhraní API FarmBeats obsahují technickou dokumentaci k Swagger. Další informace o rozhraních API a jejich odpovídajících požadavcích nebo odpovědích najdete v [FarmBeats Swagger](https://aka.ms/farmbeatsswagger). 

Pokud jste již nainstalovali FarmBeats, získáte přístup k FarmBeats Swagger na adrese `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Všimněte si, že *rozhraní API* je připojené k názvu webu FarmBeats. Koncový bod rozhraní API je `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>DataHub lib

FarmBeats poskytuje lib, kterou můžete použít. Lib je aktuálně k dispozici jako [součást implementace reference](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib). Později bude k dispozici jako sada SDK pro více jazyků.

### <a name="authentication"></a>Authentication

**Ověřování pomocí rozhraní FarmBeats API**

FarmBeats používá ověřování pomocí nosiče. Přístup k rozhraním API můžete zajistit poskytnutím přístupového tokenu v části záhlaví žádosti. Tady je příklad:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Přístupový token můžete požádat z aplikace Azure Functions, která je spuštěná v instanci FarmBeats zákazníka. Azure Functions adresa URL se k programu Docker poskytne jako argument. Přístupový token můžete získat tak, že na `GET` adrese URL vytvoříte žádost. Odpověď z adresy URL obsahuje přístupový token. 

K získání přístupového tokenu použijte pomocné funkce v knihovně DataHub lib. Další informace najdete na [stránce GitHubu pro Image Docker NOAA](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

Přístupový token je platný jenom pár hodin. Až vyprší, musíte si ji vyžádat znovu.

**Ověřování pomocí rozhraní API na straně partnerů**

Aby bylo možné provést ověření pomocí rozhraní API na straně partnera v době, kdy je spuštěná úloha Docker, zákazníci musí přihlašovací údaje poskytnout během registrace partnera. Tady je příklad:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
Služba API tuto dict – zaserializace a uloží ji do [trezoru klíčů](../../key-vault/general/basic-concepts.md).

[Azure Data Factory](../../data-factory/introduction.md) slouží k orchestraci úloh počasí. Roztočí prostředky pro spuštění kódu Docker. Data Factory taky poskytuje mechanismus pro zabezpečené odesílání dat do virtuálního počítače, na kterém běží úloha Docker. Přihlašovací údaje rozhraní API se pak bezpečně ukládají do trezoru klíčů. 

Přihlašovací údaje se čtou jako zabezpečené řetězce z trezoru klíčů. Poskytují se jako rozšířené vlastnosti v pracovním adresáři kontejneru Docker. Cesta k souboru je */mnt/working_dir/activity.jsna*. 

Kód Docker může číst přihlašovací údaje z *activity.jsv* průběhu doby běhu pro přístup k rozhraním API na straně partnera pro zákazníka. V souboru JSON by přihlašovací údaje vypadaly například jako tento příklad kódu:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
`partnerCredentials`Přihlašovací údaje jsou k dispozici způsobem, jakým ho Zákazník poskytl během registrace partnera.

FarmBeats lib poskytuje pomocné funkce. Pomocí těchto funkcí si můžete přečíst přihlašovací údaje z vlastností aktivity. Další informace najdete na [stránce GitHubu pro Image Docker NOAA](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

Soubor se používá jenom v případě, že je spuštěný Kód Docker. Po dokončení kódu se soubor odstraní.

Další informace o tom, jak Data Factory kanály a aktivity fungují, najdete v tématu [schéma a mapování datových typů](../../data-factory/copy-activity-schema-and-type-mapping.md).

**Hlavičky požadavku HTTP**

V následující tabulce jsou uvedeny nejběžnější hlavičky požadavků, které je třeba zadat při volání rozhraní API do FarmBeats.

Hlavička | Popis a příklad
--- | ---
Typ obsahu | Formát požadavku. Příklad: `Content-Type: application/<format>` <br/>Pro rozhraní FarmBeats DataHub API je formát JSON. Příklad: ` Content-Type: application/json`
Autorizace | Přístupový token, který je vyžadován pro volání rozhraní API. Příklad: `Authorization: Bearer <Access-Token>`
Přijmout | Formát odpovědi. Pro rozhraní FarmBeats DataHub API je formát JSON. Příklad: `Accept: application/json`

## <a name="data-format"></a>Formát dat

JSON je běžný jazyk nezávislý na jazyce, který poskytuje jednoduchou textovou reprezentaci libovolných datových struktur. Další informace najdete v tématu [JSON.org](https://json.org).

## <a name="docker-specifications"></a>Specifikace Docker

Docker program potřebuje dvě komponenty: Bootstrap a úlohu. Program může mít více než jednu úlohu.

### <a name="bootstrap"></a>Metoda bootstrap

Spouštěcí komponenta by se měla spustit, když zákazník spustí registraci Docker na FarmBeats. `arg1` `arg2` Do programu jsou předány následující argumenty (a):

- **Koncový bod rozhraní API FarmBeats**: koncový bod rozhraní FarmBeats API pro žádosti rozhraní API. Tento koncový bod zpřístupňuje volání rozhraní API FarmBeats nasazení.
- **Adresa URL Azure Functions**: váš vlastní koncový bod. Tato adresa URL poskytuje token pro přístup k rozhraním API FarmBeats. K `GET` načtení přístupového tokenu můžete zavolat na tuto adresu URL.

Bootstrap vytvoří metadata, která uživatelé potřebují ke spouštění vašich úloh, aby mohli získat data o počasí. Další informace najdete v [referenční implementaci](https://github.com/azurefarmbeats/noaa_docker). 

Pokud přizpůsobíte *bootstrap_manifest.jsv* souboru, pak program pro spuštění odkazu vytvoří požadovaná metadata za vás. Spouštěcí program vytvoří následující metadata: 

 > [!NOTE]
 > Pokud aktualizujete *bootstrap_manifest.jspro* soubor, jak popisuje [referenční implementace](https://github.com/azurefarmbeats/noaa_docker) , nemusíte vytvářet následující metadata. Spouštěcí program použije váš soubor manifestu k vytvoření potřebných metadat.

- /**WeatherDataModel**: metadata WeatherDataModel představují data o počasí. Odpovídá datovým sadám, které poskytuje zdroj. DailyForecastSimpleModel může například poskytnout průměrnou teplotu, vlhkost a informace o vysrážení jednou denně. Naopak DailyForecastAdvancedModel může poskytovat mnohem více informací v hodinové členitosti. Můžete vytvořit libovolný počet datových modelů počasí.
- /**JobType**: FarmBeats má rozšiřitelný systém správy úloh. Jako poskytovatel dat počasí budete mít různé datové sady a rozhraní API (například GetDailyForecasts). Tyto datové sady a rozhraní API můžete v FarmBeats povolit pomocí JobType. Po vytvoření typu úlohy může zákazník aktivovat úlohy tohoto typu, aby získal data o počasí pro svoje umístění nebo jejich farmu, které vás zajímají. Další informace najdete v tématu rozhraní API pro JobType a úlohy v [Swagger FarmBeats](https://aka.ms/farmbeatsswagger).

### <a name="jobs"></a>Úlohy

Komponenta Jobs je vyvolána pokaždé, když uživatel FarmBeats spustí úlohu/JobType, kterou jste vytvořili jako součást procesu Bootstrap. Příkaz Spustit jako Docker pro úlohu je definován jako součást/JobType, kterou jste vytvořili.

Úloha načte data ze zdroje a vloží je do FarmBeats. Během zaváděcího procesu by měly být parametry vyžadované k získání dat definovány jako součást/JobType..

V rámci úlohy musí program vytvořit/WeatherDataLocation založenou na/WeatherDataModel, která byla vytvořena během procesu Bootstrap. /WeatherDataLocation odpovídá umístění (souřadnice zeměpisné šířky a délky), které uživatel zadal jako parametr pro úlohu.

### <a name="object-details"></a>Podrobnosti objektu

WeatherDataModel | Popis |
--- | ---
Název  | Název datového modelu počasí |
Description  | Smysluplný popis modelu |
Vlastnosti  | Další vlastnosti definované poskytovatelem dat. |
Název > weatherMeasures  | Název míry počasí Například humidity_max. |
weatherMeasures > datový typ  | Buď typ Double, nebo Enum. Pokud je vyžadováno Enum, measureEnumDefinition. |
weatherMeasures > measureEnumDefinition  | Požadováno pouze v případě, že je datový typ Enum. Například `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }`. |
Typ > weatherMeasures  | Typ dat telemetrie počasí Například RelativeHumidity. Systémem definované typy jsou AmbientTemperature, jednotka, CO2, Hloubka, ElectricalConductivity, LeafWetness, Length, LiquidLevel, dusičnan, O2, PH, fosforečnan, PointInTime, draselný, tlak, RainGauge, RelativeHumidity, slanost, SoilMoisture, SoilTemperature, SolarRadiation, TimeDuration, UVRadiation, UVIndex, WindDirection, WindRun, WindSpeed, evapotranspiration,, a nominální hodnota. Chcete-li přidat další typy, přečtěte si část [Přidání ExtendedType](#add-extendedtype) v tomto článku.
Jednotka > weatherMeasures | Jednotka počasí data telemetrie. Jednotky definované systémem jsou jednotky ve stupních, stupních Celsia, Fahrenheita, kelvinech, Rankine, Pascal, rtuť, PSI, milimetr, centimetr, měřič, palce, nohy, míle, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, stupeň, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, PartsPerMillion, MicroMole, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, s VolumetricIonContent, MilliLiter, UnixTimestamp a MicroMolePerMeterSquaredPerSecond. Pokud chcete přidat další jednotky, přečtěte si část [Přidání ExtendedType](#add-extendedtype) v tomto článku.
weatherMeasures > AggregationType  | Typ agregace. Možné hodnoty jsou None, Average, Max, minim, StandardDeviation, Sum a Total.
Hloubka > weatherMeasures  | Hloubka senzoru v centimetrech Například měření vlhkosti 10 cm pod vozovkou.
Popis > weatherMeasures  | Smysluplný popis měření. 

JobType | Popis |
--- | ---
Název  | Název úlohy Například Get_Daily_Forecast. Zákazník spustí tuto úlohu, aby získala data o počasí.|
pipelineDetails > parametry > název  | Název parametru |
pipelineDetails > parametry > typu | Typ parametru. Možné hodnoty zahrnují řetězec, int, float, bool a Array. |
parametry > pipelineDetails > je potřeba. | Logická hodnota parametru. Hodnota je true, pokud je parametr požadován. V opačném případě je hodnota false. Výchozí hodnota je true. |
pipelineDetails > parametry > defaultValue | Výchozí hodnota parametru |
pipelineDetails > parametry > popisu | Popis parametru |
Vlastnosti  | Další vlastnosti od výrobce.
Vlastnosti > programRunCommand | Příkaz Docker run. Tento příkaz se spustí, když zákazník spustí úlohu počasí. |

WeatherDataLocation | Description |
--- | ---
weatherDataModelId  | ID odpovídající WeatherDataModel, která byla vytvořena během procesu Bootstrap.|
location  | Zeměpisná šířka, zeměpisná délka a zvýšení úrovně. |
Name | Název objektu. |
Description | Popis umístění dat počasí |
farmId | Volitelné ID farmy Zákazník toto ID poskytne jako součást parametru úlohy. |
Vlastnosti  | Další vlastnosti od výrobce.

Další informace o objektech a jejich vlastnostech najdete v [FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger).

> [!NOTE]
> Rozhraní API vrací jedinečná ID pro každou vytvořenou instanci. Překladatel pro správu zařízení a synchronizaci metadat musí zachovat toto ID.

**Synchronizace metadat**

Komponenta Docker konektoru by měla být schopna odesílat aktualizace metadat. Například by měl odesílat aktualizace, když poskytovatel počasí přidá nové parametry do datové sady nebo když jsou přidány nové funkce, jako je například nová 30denní předpověď.

> [!NOTE]
> Odstranění není podporováno pro metadata v datovém modelu počasí.
>
> Chcete-li aktualizovat metadata, je nutné zavolat `/Get/{ID}` na datový model počasí. Aktualizujte změněné vlastnosti a pak udělejte a uložte `/Put/{ID}` všechny vlastnosti, které uživatel nastaví.

## <a name="weather-data-telemetry-specifications"></a>Specifikace dat počasí (telemetrie)

Data o počasí jsou namapována na kanonickou zprávu, která je vložena do centra událostí Azure ke zpracování. Azure Event Hubs je služba, která umožňuje přijímání dat v reálném čase z připojených zařízení a aplikací. 

Chcete-li odesílat data o počasí do FarmBeats, vytvořte klienta, který odesílá zprávy do centra událostí v FarmBeats. Další informace najdete v tématu [posílání telemetrie do centra událostí](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Následující vzorový kód Pythonu odešle telemetrii jako klienta do zadaného centra událostí.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Tady je formát kanonické zprávy:

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Tady je příklad zprávy telemetrie:

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

### <a name="error-logging"></a>Protokolování chyb

Úloha partnera se spustí v existujícím rozhraní. Chyby jsou tedy protokolovány stejným způsobem jako chyby pro jiné dříve existující úlohy FarmBeats (například GetFarmData a SensorPlacement). Data Factory aktivita spuštěná v rámci kanálu Data Factory protokoluje `STDERR` a `STDOUT` . Oba soubory jsou k dispozici v `datahublogs-xxx` účtu úložiště v rámci skupiny prostředků FarmBeats.

DataHub lib poskytuje pomocné funkce pro povolení protokolování v rámci celkových protokolů DataHub. Další informace najdete na [stránce GitHubu pro Image Docker NOAA](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

### <a name="troubleshooting-and-support"></a>Řešení potíží a podpora

Pokud zákazník nemůže přijímat data v počasí v instanci FarmBeats, poskytněte podporu a mechanismus pro řešení tohoto problému.

## <a name="add-extendedtype"></a>Přidat ExtendedType

FarmBeats podporuje přidávání nových typů měření senzorů a jednotek. Můžete přidat nové jednotky nebo typy aktualizací *bootstrap_manifest.js* v souboru v [referenční implementaci](https://github.com/azurefarmbeats/noaa_docker).

Pomocí následujících kroků přidejte nový typ WeatherMeasure, například PrecipitationDepth.

1. Vytvořte `GET` žádost o/ExtendedType pomocí dotazu `filter - key = WeatherMeasureType` .
2. Poznamenejte si ID vráceného objektu.
3. Přidejte nový typ do seznamu v vráceném objektu. Vytvořte `PUT` v/ExtendedType{ID} požadavek pomocí následujícího nového seznamu. Vstupní datová část by měla být stejná jako odpověď, kterou jste získali dříve. Nová jednotka by měla být připojena na konci seznamu hodnot.

Další informace o rozhraní/ExtendedType API najdete v tématu [FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Další kroky

Teď máte komponentu Docker konektoru, která se integruje s FarmBeats. V dalším kroku zjistíte, jak [získat data o počasí](get-weather-data-from-weather-partner.md) pomocí Image Docker v FarmBeats. 
