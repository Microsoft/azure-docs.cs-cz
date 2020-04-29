---
title: Získat data o počasí od partnerů počasí
description: Tento článek popisuje, jak z partnerů získat data o počasí.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266159"
---
# <a name="get-weather-data-from-weather-partners"></a>Získat data o počasí od partnerů počasí

Azure FarmBeats pomáhá přenášet údaje o počasí ze zprostředkovatelů dat počasí pomocí rozhraní konektoru na bázi Docker. Pomocí tohoto rozhraní poskytovatelé dat počasí implementují Docker, který se dá integrovat s FarmBeats. V současné době jsou podporovány následující poskytovatelé dat počasí:

[NOAA dat z otevřených datových sad Azure](https://azure.microsoft.com/services/open-datasets/)

Data o počasí je možné využít ke generování přehledů s užitečnými informacemi a sestavení modelů AI/ML na FarmBeats.

## <a name="before-you-start"></a>Než začnete

Pokud chcete získat data o počasí, ujistěte se, že máte nainstalovanou FarmBeats. **Integrace počasí je podporovaná ve verzi 1.2.11 nebo vyšší**. Pokud chcete nainstalovat Azure FarmBeats, přečtěte si téma [install FarmBeats](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Povolit integraci počasí s FarmBeats

Pokud chcete začít získávat data o počasí v datovém centru FarmBeats, postupujte podle následujících kroků:

1. Přejít do aplikace FarmBeats data hub Swagger (https://yourdatahub.azurewebsites.net/swagger)

2. Přejděte na/partner API a vytvořte žádost POST s následující vstupní datovou částí:

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

   Pokud například chcete získat data o počasí z NOAA pomocí Azure Open DataSets, použijte následující datovou část. Můžete upravit název a popis podle vaší předvolby.

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
   > Další informace o partnerském objektu najdete v [příloze](get-weather-data-from-weather-partner.md#appendix) .

   Předchozí krok zřídí prostředky pro povolení Docker pro spuštění v prostředí FarmBeats zákazníka.  

   Zřizování výše uvedených prostředků trvá přibližně 10-15 minut.

3. Ověřte stav objektu/partner, který jste vytvořili v kroku 2. Provedete to tak, že vytvoříte požadavek GET na rozhraní/partner API a zkontrolujete **stav** objektu partnera. Jakmile FarmBeats zřídí partnera úspěšně, stav se nastaví na **aktivní**.

4. Přejděte na/JobType API a vytvořte požadavek GET na stejnou hodnotu. Podívejte se na úlohy počasí vytvořené v rámci procesu přidávání partnerů v kroku 2. Pole název **kanálu** v úkolech počasí bude v následujícím formátu: "partner-name_partner-type_job-Name".

5. Instance FarmBeats má teď aktivní datový partner s daty o počasí a můžete spouštět úlohy pro vyžádání dat o počasí pro konkrétní umístění (zeměpisná šířka/délka) a rozsah dat. JobType (y) bude obsahovat podrobnosti o tom, jaké parametry jsou potřeba ke spouštění úloh počasí.

   Například pro NOAA data z otevřených datových sad Azure se vytvoří následující JobType (y):

   - get_weather_data (získání ISD/historických dat o počasí)
   - get_weather_forecast_data (získání dat počasí GFS/předpověď)

6. Poznamenejte si **ID** a parametry JobType (y).

7. Přejděte na/Jobs API a v/Jobs vytvořte žádost POST s následující vstupní datovou částí:

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

   Pokud například chcete spustit **get_weather_data**, použijte následující datovou část:

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

8. Předchozí krok spustí úlohy počasí podle definice v Docker partnera a ingestuje data o počasí do FarmBeats. Stav úlohy můžete zkontrolovat tak, že na/Jobs vytvoříte požadavek GET a v odpovědi vyhledáte **CurrentState** . Po dokončení bude currentState nastaven na hodnotu **úspěch**.

## <a name="query-ingested-weather-data"></a>Dotázat se na data ingestovaná počasí

Po dokončení úloh počasí můžete zadávat dotazy na ingestovaná data o počasí k sestavování modelů nebo užitečných přehledů. Existují dva způsoby, jak získat přístup k datům počasí z FarmBeats a dotazovat se na ně:

- Rozhraní API a
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Dotaz pomocí REST API

Pokud chcete zadat dotaz na data o počasí pomocí REST API FarmBeats, postupujte podle následujících kroků:

1. V Swagger FarmBeats datacentra (https://yourdatahub.azurewebsites.net/swagger), přejděte na rozhraní/WeatherDataLocation API a vytvořte žádost o získání. Odpověď bude obsahovat objekty/WeatherDataLocation vytvořené pro umístění (zeměpisná šířka/délka), která byla zadána jako součást běhu úlohy. Poznamenejte si **ID** a **weatherDataModelId** objektu (y).

2. Pro **weatherDataModelId** , jak je uvedeno v kroku 1, vytvořte/WEATHERDATAMODEL API Get/{ID}. "Datový model počasí" obsahuje veškerá metadata a podrobnosti o přijatých datech o počasí. Například **míra počasí** v objektu **datového modelu počasí** obsahuje podrobnosti o tom, jaké informace o počasí jsou podporovány a v jakých typech a jednotkách. Například:

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

   Poznamenejte si odpověď z volání metody GET/{ID} pro datový model počasí.

3. Přejděte na rozhraní API **telemetrie** a vytvořte žádost post s následující vstupní datovou částí:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. Odpověď obsahující data v počasí, která jsou k dispozici pro zadaný časový rozsah, budou vypadat takto:

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

V předchozím příkladu má odpověď data pro dvě časová razítka spolu s názvem míry ("teplota") a hodnotami hlášených povětrnostních dat ve dvou časových razítkech. Pro interpretaci typu a jednotky hlášených hodnot budete potřebovat odkaz na přidružený datový model počasí (jak je popsáno v kroku 2 výše).

### <a name="query-using-azure-time-series-insights-tsi"></a>Dotaz pomocí Azure Time Series Insights (TSI)

FarmBeats používá [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) k ingestování, ukládání, dotazování a vizualizaci dat v IoT Scale – data, která jsou s vysokou mírou kontextu a optimalizovaná pro časové řady.

Data o počasí se přijímají na EventHub a pak se do prostředí TSI v rámci skupiny prostředků FarmBeats. Data pak mohou být přímo dotazována z TSI. Další informace najdete v [dokumentaci k TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).

Podle postupu Vizualizujte data v TSI:

1. Přejděte do **Azure portal** > **skupiny prostředků Azure Portal FarmBeats DataHub** > vyberte **Time Series Insights** prostředí (TSI-xxxx) > **zásady přístupu k datům**. Přidejte uživatele s přístupem Čtenář nebo Přispěvatel.

2. Přejít na stránku **Přehled** prostředí **Time Series Insights** (TSI-xxxx) a vyberte **adresu URL aplikace Time Series Insights Explorer**. Teď můžete vizualizovat data ingestovaná počasí.

Kromě ukládání, dotazování a vizualizace dat počasí, TSI taky umožňuje integraci do řídicího panelu Power BI. Další informace najdete v tématu [vizualizace dat z Time Series Insights v Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi).

## <a name="appendix"></a>Příloha

|        Partner   |  Podrobnosti   |
| ------- | -------             |
|     DockerDetails – imageName         |          Název bitové kopie Docker. Například docker.io/azurefarmbeats/farmbeats-noaa (Image v hub.docker.com) nebo myazureacr.azurecr.io/mydockerimage (obrázek v Azure Container Registry) a tak dále. Pokud není zadaný žádný registr, výchozí hodnota je hub.docker.com.      |
|          DockerDetails - imageTag             |         Název značky obrázku Docker Výchozí hodnota je "nejnovější"     |
|  DockerDetails – přihlašovací údaje      |  Přihlašovací údaje pro přístup k soukromému Docker. Tuto adresu poskytne partner pro zákazníka.   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch SKU virtuálního počítače. Všechny dostupné virtuální počítače se systémem Linux najdete [tady](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) . Platné hodnoty jsou: "Small", "ExtraLarge", "Large", "A8", "Medium", "Medium", "A5", "A7", "STANDARD_D1", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4 ', ' STANDARD_D11 ', ' STANDARD_D12 ', ' STANDARD_D13 ', ' STANDARD_D14 ', ' A10 ', ' A11 ', ' STANDARD_D1_V2 ', ' STANDARD_D2_V2 ', ' STANDARD_D3_V2 ', ' STANDARD_D4_V2 ', ' STANDARD_D11_V2 ', ' STANDARD_D12_V2 ', ' STANDARD_D13_V2 ', ' STANDARD_D14_V2 ', ' STANDARD_G1 '; ' STANDARD_G2 ' , ' STANDARD_G5 ', ' STANDARD_D5_V2 ', ' BASIC_A1 ', ' BASIC_A2 ', ' BASIC_A3 ', ' BASIC_A4 ', ' STANDARD_A1 ', ' STANDARD_A2 ', ' STANDARD_A3 ', ' STANDARD_A4 ', ' STANDARD_A5 ', ' STANDARD_A6 ', ' STANDARD_A7 ', ' STANDARD_A8 ', ' STANDARD_A9 ', ' STANDARD_A10 ', ' STANDARD_A11 ', ' STANDARD_D15_V2 ', ' STANDARD_F1 ', ' STANDARD_F2 ', ' STANDARD_F4 ', ' STANDARD_F8 ', ' STANDARD_F16 ', ' STANDARD_NV6 ', ' STANDARD_NV12 ', ' STANDARD_NV24 ', ' STANDARD_NC6 ', ' STANDARD_NC12 ', ' STANDARD_NC24 ', ' STANDARD_NC24r ', ' ' , ' STANDARD_H8 ', ' STANDARD_H8m ', ' STANDARD_H16 ', ' STANDARD_H16m ', ' STANDARD_H16mr ', ' STANDARD_H16r ', ' STANDARD_A1_V2 ', ' STANDARD_A2_V2 ', ' STANDARD_A4_V2 ', ' STANDARD_A8_V2 ', ' STANDARD_A2m_V2 ', ' STANDARD_A4m_V2 ', ' STANDARD_A8m_V2 ', ' STANDARD_M64ms ', ' STANDARD_M128s ', ' STANDARD_D2_V3 ', ' ', ' ', ' '. **Výchozí hodnota je "standard_d2_v2".**  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Ne. vyhrazené uzly počítače pro fond služby Batch. Výchozí hodnota je 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    ID SKU agenta uzlu Azure Batch. V současné době je podporován pouze agent uzlu Batch. Node. Ubuntu 18,04.    |
| DockerDetails - partnerCredentials | přihlašovací údaje pro volání rozhraní API partnerského serveru v Docker Partner musí zákazníkům poskytnout tyto informace na základě ověřovacího mechanizmu, který je podporován například. Uživatelské jméno/heslo nebo klíče rozhraní API. |
| partnerType | "Počasí" (jiné typy partnerů v FarmBeats jsou "senzor" a "satelitní")  |
|  jméno   |   Požadovaný název partnera v FarmBeats systému   |
|  description |  Popis   |

## <a name="next-steps"></a>Další kroky

Nyní máte dotazovaná data senzorů z vaší instance Azure FarmBeats. Nyní se dozvíte, jak [vygenerovat mapy](generate-maps-in-azure-farmbeats.md#generate-maps) pro vaše farmy.
