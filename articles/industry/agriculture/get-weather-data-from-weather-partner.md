---
title: Získejte údaje o počasí od partnerů pro počasí
description: Tento článek popisuje, jak získat data o počasí od partnerů.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266159"
---
# <a name="get-weather-data-from-weather-partners"></a>Získejte údaje o počasí od partnerů pro počasí

Azure FarmBeats vám pomůže přenést data o počasí od vašeho poskytovatele dat o počasí pomocí rozhraní Konektor Framework založené na dockeru. Pomocí tohoto rozhraní poskytovatelé dat počasí implementovat docker, který lze integrovat s FarmBeats. V současné době jsou podporováni následující poskytovatelé dat o počasí:

[Data NOAA z otevřených datových sad Azure](https://azure.microsoft.com/services/open-datasets/)

Data o počasí lze použít ke generování užitečných přehledů a vytváření modelů AI/ML na FarmBeats.

## <a name="before-you-start"></a>Než začnete

Chcete-li získat údaje o počasí, ujistěte se, že jste nainstalovali FarmBeats. **Integrace počasí je podporována ve verzi 1.2.11 nebo vyšší**. Informace o instalaci Azure FarmBeats najdete [v tématu Instalace FarmBeats](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Povolit integraci počasí s FarmBeats

Chcete-li začít dostávat data o počasí do datového centra FarmBeats, postupujte podle následujících kroků:

1. Přejděte do svého datového centra FarmBeats (https://yourdatahub.azurewebsites.net/swagger)

2. Přejděte do rozhraní /Partner API a vytvořte požadavek POST s následující vstupní datovou částí:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Chcete-li například získat data o počasí z NOAA pomocí otevřených datových sad Azure, použijte datovou část níže. Název a popis můžete upravit podle svých preferencí.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > Další informace o objektu Partner naleznete v [dodatku](get-weather-data-from-weather-partner.md#appendix)

   Předchozí krok zřídí prostředky, které umožní spuštění dockeru v prostředí FarmBeats zákazníka.  

   To trvá asi 10-15 minut na zřízení výše uvedených zdrojů.

3. Zkontrolujte stav objektu /Partner, který jste vytvořili v kroku 2. Chcete-li to provést, vytvořte požadavek GET na rozhraní /Partner API a zkontrolujte **stav** objektu partnera. Jakmile FarmBeats zřídí partnera úspěšně, stav je nastaven na **Aktivní**.

4. Přejděte na /JobType API a provést požadavek GET na stejné. Zkontrolujte, zda úlohy počasí, které jsou vytvořeny jako součást procesu přidání partnera v kroku 2. Pole **pipelineName** v úlohách počasí bude mít následující formát: "partner-name_partner-type_job-name".

5. Nyní má vaše instance FarmBeats aktivního partnera pro data počasí a můžete spouštět úlohy a požadovat data o počasí pro určité místo (zeměpisná šířka/délka) a časové období. JobType (y) bude mít podrobnosti o tom, jaké parametry jsou nutné ke spuštění úlohy počasí.

   Například pro data NOAA z otevřených datových sad Azure budou vytvořeny následující JobType(s):

   - get_weather_data (Získat ISD/ historické údaje o počasí)
   - get_weather_forecast_data (Získejte GFS/předpověď počasí)

6. Poznamenejte si **ID** a parametry JobType(s).

7. Přejděte na /Jobs API a vytvořte požadavek POST na /Jobs s následující vstupní datovou částí:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Chcete-li například spustit **get_weather_data**, použijte následující datovou část:

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. Předchozí krok spustí úlohy počasí, jak je definováno v partnerském dockeru a ingestovat data počasí do FarmBeats. Můžete zkontrolovat stav úlohy tím, že get požadavek na /Jobs a vyhledejte **currentState** v odpovědi. Po dokončení currentState je **nastavena**na Succeeded .

## <a name="query-ingested-weather-data"></a>Dotaz na data o počasí s polykaná

Po dokončení úloh y počasí můžete dotazovat na data o počasí a vytvářet modely nebo užitečné přehledy. Existují dva způsoby, jak přistupovat a dotazovat se na údaje o počasí z FarmBeats:

- API a
- Časové řady (TSI).

### <a name="query-using-rest-api"></a>Dotaz pomocí rozhraní REST API

Chcete-li zadat dotaz na data o počasí pomocí rozhraní FarmBeats REST API, postupujte podle následujících kroků:

1. Ve vašem FarmBeats Data hubhttps://yourdatahub.azurewebsites.net/swagger)swagger ( , přejděte na /WeatherDataLocation API a podat požadavek GET. Odpověď bude mít /WeatherDataLocation objekt (y) vytvořené pro umístění (zeměpisná šířka/délka), která byla zadána jako součást spuštění úlohy. Poznamenejte si **ID** a **weatherDataModelId** objektu (objektů).

2. Vytvořte GET/{id} na /WeatherDataModel API pro **weatherDataModelId** jak je uvedeno v kroku 1. "Model data počasí" má všechna metadata a podrobnosti o pozůstalých údajů o počasí. **Například měření počasí** v rámci modelu data počasí **objekt** uvede podrobnosti o tom, jaké informace o počasí je podporována a v jaké typy a jednotky. Například:

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Poznamenejte si odpověď z volání GET/{id} pro model data počasí.

3. Přejděte na **rozhraní API telemetrie** a vytvořte požadavek POST s následující vstupní datovou částí:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. Odpověď obsahující data o počasí, která je k dispozici pro zadaný časový rozsah, bude vypadat takto:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

V předchozím příkladu má odpověď data pro dvě časová razítka spolu s názvem míry ("Teplota") a hodnotami hlášených údajů o počasí ve dvou časových razítkách. Budete muset odkazovat na přidružený model dat o počasí (jak je popsáno v kroku 2 výše) interpretovat typ a jednotku vykázaných hodnot.

### <a name="query-using-azure-time-series-insights-tsi"></a>Dotaz pomocí Azure Time Series Insights (TSI)

FarmBeats používá [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) k ingestování, ukládání, dotazování a vizualizaci dat ve škále IoT – data, která jsou vysoce kontextualizovaná a optimalizovaná pro časové řady.

Data o počasí jsou přijímána na EventHub a poté jsou posunuta do prostředí TSI v rámci skupiny prostředků FarmBeats. Data pak mohou být přímo dotazována z TSI. Další informace naleznete v [dokumentaci TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).

Vizuací dat o TSI postupujte takto:

1. Přejděte na **skupinu** > prostředků Azure portal**FarmBeats DataHub** > vyberte prostředí **Time Series Insights** (tsi-xxxx) > **zásady přístupu k datům**. Přidejte uživatele pomocí přístupu ke čtečce nebo přispěvateli.

2. Přejděte na stránku **Přehled** prostředí **Time Series Insights** (tsi-xxxx) a vyberte adresu URL **průzkumníka přehledů časové řady**. Nyní můžete vizualizovat data o požitém počasí.

Kromě ukládání, dotazování a vizualizace dat o počasí umožňuje TSI také integraci do řídicího panelu Power BI. Další informace najdete [v tématu Vizualizace dat z přehledů časových řad v Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi).

## <a name="appendix"></a>Příloha

|        Partner   |  Podrobnosti   |
| ------- | -------             |
|     DockerDetails - imageName         |          Název obrázku Dockeru. Například docker.io/azurefarmbeats/farmbeats-noaa (image v hub.docker.com) NEBO myazureacr.azurecr.io/mydockerimage (image v Azure Container Registry) a tak dále. Pokud není k dispozici žádný registr, je výchozí hub.docker.com      |
|          DockerDetails - imageTag             |         Název značky image dockeru. Výchozí hodnota je "nejnovější"     |
|  DockerDetails - pověření      |  Pověření pro přístup k privátní docker. To bude poskytnuto partnerem zákazníkovi   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure dávkové virtuální virtuální ho virtuálního počítače. Podívejte se [zde](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pro všechny virtuální počítače Linux k dispozici. Platné hodnoty jsou: "Malé", "ExtraLarge", "Velké", "A8", "A9", "Střední", "A5", "A6", "A7", "STANDARD_D1", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4", "STANDARD_D11", "STANDARD_D12", "STANDARD_D13", "STANDARD_D14", "A10", "A11", "STANDARD_D1_V2", "STANDARD_D2_V2", "STANDARD_D3_V2", "STANDARD_D4_V2", "STANDARD_D11_V2", "STANDARD_D12_V2", "STANDARD_D13_V2", "STANDARD_D14_V2", "STANDARD_G1", "STANDARD_G2", "STANDARD_G3", "STANDARD_G3", "", "STANDARD_G4", "", "STANDARD_G4", "STANDARD_G2", "STANDARD_G3", "", "STANDARD_G2", "", "STANDARD_G2", "", "STANDARD_G2", "", "STANDARD_G3", "", "STANDARD_G2", "STANDARD_G2", "STANDARD_G3", "", "STANDARD_G2", "STANDARD_G3", "STANDARD_G2", "STANDARD_G2", "STANDARD_G3", "STANDARD_G2", "STANDARD_G3", "", "STANDARD_G3", "STANDARD_G2", "STANDARD_G3", "", "STANDARD_G3", "STANDARD_G2", "STANDARD_G3", "STANDARD_G2", "STANDARD_G3", "", "STANDARD_G3", "", "STANDARD_G2", "STANDARD_G3", "", "STANDARD_G2", "", "STANDARD_G3", "", "STANDARD_G2", "", "STANDARD_G3", "", "STANDARD_G3", "", "STANDARD_G3", "", "STANDARD_G2", "STANDARD_G3", "STANDARD_G3", "STANDARD_G3", , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", "BASIC_A2", "BASIC_A3", "BASIC_A4", "STANDARD_A1", "STANDARD_A2", "STANDARD_A3", "STANDARD_A4", "STANDARD_A5", "STANDARD_A6", "STANDARD_A7", "STANDARD_A8", "STANDARD_A9", "STANDARD_A10", "STANDARD_NC6 STANDARD_A11", "STANDARD_NC12 STANDARD_D15_V2", STANDARD_NV24 STANDARD_NV12 STANDARD_NV6 STANDARD_F16 STANDARD_F8 STANDARD_F2 STANDARD_F1 "STANDARD_NC24", "STANDARD_F4", "STANDARD_NC24r", "STANDARD_NC24r", , "STANDARD_H8", "STANDARD_H8m", "STANDARD_H16", "STANDARD_H16m STANDARD_H16mr", "STANDARD_H16r", "STANDARD_A1_V2", "STANDARD_A2_V2", "STANDARD_A4_V2", "STANDARD_A8_V2", "STANDARD_A2m_V2", "STANDARD_A4m_V2", "STANDARD_A8m_V2", "STANDARD_M64ms", "STANDARD_M128s", "STANDARD_D2_V3". **Výchozí hodnota je "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - vyhrazenéComputerNodes   |  Ne. vyhrazených počítačových uzlů pro dávkový fond. Výchozí hodnota je 1. |
|    DockerDetails - azureBatchVMDetails - uzelAgentSKUID          |    ID zprostředkovatele zprostředkovatele zprostředkovatele zprostředkovatele zprostředkovatele uzlu Azure. V současné době je podporován pouze agent dávkového uzlu "batch.node.ubuntu 18.04".    |
| DockerDetails - partnerPověření | přihlašovací údaje pro volání rozhraní API partnera v dockeru. Partner musí tyto informace poskytnout svým zákazníkům na základě mechanismu ověřování, který je podporován např. Uživatelské jméno/heslo nebo klíče API. |
| partnerTyp | "Počasí" (Ostatní typy partnerů v FarmBeats jsou "Senzor" a "Snímky")  |
|  jméno   |   Požadovaný název partnera v systému FarmBeats   |
|  description |  Popis   |

## <a name="next-steps"></a>Další kroky

Nyní jste se dotazovali na data senzorů z instance Azure FarmBeats. Nyní se naučte vytvářet [mapy](generate-maps-in-azure-farmbeats.md#generate-maps) pro vaše farmy.
