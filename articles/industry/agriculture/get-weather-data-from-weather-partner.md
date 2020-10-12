---
title: Získat data o počasí od partnerů počasí
description: Tento článek popisuje, jak z partnerů získat data o počasí.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 35acf4e9bd338a0e67b046a59d8884df0626e516
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87429258"
---
# <a name="get-weather-data-from-weather-partners"></a>Získat data o počasí od partnerů počasí

Azure FarmBeats vám pomůže přenést data z vašich poskytovatelů dat počasí pomocí rozhraní konektoru na bázi Docker. Pomocí tohoto rozhraní poskytovatelé dat počasí implementují Docker, který se dá integrovat s FarmBeats. V současné době je podporován následující zprostředkovatel dat počasí.

  ![Partneři FarmBeats](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

Data o počasí je možné využít ke generování přehledů s užitečnými poznatky a vytváření modelů AI a ML v FarmBeats.

## <a name="before-you-start"></a>Než začnete

Pokud chcete získat data o počasí, ujistěte se, že máte [nainstalovanou FarmBeats](https://aka.ms/farmbeatsinstalldocumentation). Integrace počasí je podporovaná ve verzích 1.2.11 a vyšších. 

## <a name="enable-weather-integration-with-farmbeats"></a>Povolit integraci počasí s FarmBeats

Jak začít získávat data o počasí na FarmBeats DataHub:

1. Přejít na FarmBeats DataHub Swagger `https://farmbeatswebsite-api.azurewebsites.net/swagger` .

2. Přejít na rozhraní API/partner a pak vytvořit žádost POST. Použijte následující vstupní datovou část:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
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

   Pokud například chcete získat data o počasí z DTN, použijte následující datovou část. Můžete upravit název a popis podle svých preferencí.

   > [!NOTE]
   > Následující krok vyžaduje klíč rozhraní API. Pokud chcete získat klíč pro předplatné DTN, kontaktujte DTN.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > Další informace o partnerském objektu najdete v [příloze](get-weather-data-from-weather-partner.md#appendix) v tomto článku.

   Předchozí krok zřídí prostředky pro povolení Docker pro spuštění v prostředí FarmBeats zákazníka.  

   Zřizování prostředků trvá přibližně 10 až 15 minut.

3. Ověřte stav objektu/partner, který jste vytvořili v předchozím kroku. Pokud chcete zkontrolovat stav, udělejte požadavek GET na rozhraní/partner API a zkontrolujte stav objektu partnera. Jakmile FarmBeats zřídí partnera úspěšně, stav je nastaven na **aktivní**.

4. V rozhraní/JobType API udělejte požadavek GET. Projděte si úlohy počasí, které jste vytvořili dříve, v procesu přidání partnera. V úkolech počasí má pole **Pipeline** následující formát: **partner-name_partner-type_job-Name**.

      Vaše FarmBeats instance teď má aktivní datový partner pro počasí. Můžete spouštět úlohy pro vyžádání dat o počasí pro konkrétní umístění (zeměpisná šířka a délka) a rozsah dat. Typy úloh budou mít podrobné informace o tom, jaké parametry jsou potřeba ke spouštění úloh počasí.

      Například pro DTN se vytvoří následující typy úloh:
   
      - **get_dtn_daily_observations**: Získejte každodenní poznámky k umístění a časovému období.
      - **get_dtn_daily_forecasts**: Získejte denní předpovědi pro určité umístění a časové období.
      - **get_dtn_hourly_observations**: Získejte hodinové pozorování pro určité umístění a časové období.
      - **get_dtn_hourly_forecasts**: Získejte hodinové prognózy pro určité umístění a časové období.

6. Poznamenejte si ID a parametry typů úloh.

7. Přejít na rozhraní/Jobs API a vytvořit žádost POST v/Jobs. Použijte následující vstupní datovou část:

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

   Pokud například chcete spustit **get_dtn_daily_observations**, použijte následující datovou část:

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. Předchozí krok spustí úlohy počasí podle definice v Docker partnera a ingestuje data o počasí do FarmBeats. Stav úlohy můžete zjistit tak, že na/Jobs. vytvoříte požadavek GET. V odpovědi vyhledejte **CurrentState**. Po dokončení bude **CurrentState** nastaveno na hodnotu **úspěšné**.

## <a name="query-ingested-weather-data"></a>Dotázat se na data ingestovaná počasí

Po dokončení úloh počasí můžete pomocí rozhraní REST API pro FarmBeats DataHub zadávat dotazy na ingestovaná data o počasí k vytváření modelů nebo užitečných přehledů.

Dotazování dat o počasí pomocí REST API FarmBeats:

1. V nástroji FarmBeats DataHub [Swagger](https://yourdatahub.azurewebsites.net/swagger), navštivte rozhraní/WeatherDataLocation API a vytvořte žádost o získání. Odpověď zahrnuje objekty/WeatherDataLocation vytvořené pro umístění (zeměpisná šířka a délka), které úloha zadala. Poznamenejte si **ID** a **weatherDataModelId** objektů.

2. Vytvořte si požadavek GET/{ID} na rozhraní/WeatherDataModel API pro **weatherDataModelId** , jak jste to provedli dříve. Datový model počasí zobrazuje všechna metadata a podrobnosti o přijatých datech o počasí. Například v objektu datového modelu počasí, míra počasí podrobně popisuje, jaké informace o počasí jsou podporovány a v jakých typech a jednotkách. Například:

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

3. Přejít na rozhraní API telemetrie a vytvořit žádost POST. Použijte následující vstupní datovou část:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    Odpověď zobrazuje data o počasí, která jsou k dispozici pro zadaný časový rozsah:

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

V předchozím příkladu odpověď zobrazuje data pro dvě časová razítka. Zobrazuje také název míry (teplotu) a hodnoty hlášených dat počasí v těchto dvou časových razítkách. Pokud chcete interpretovat typ a jednotku hlášených hodnot, přečtěte si související datový model počasí.

## <a name="troubleshoot-job-failures"></a>Řešení potíží s chybami úloh

Pokud chcete řešit problémy s chybami úloh, [Podívejte se do protokolů úloh](troubleshoot-azure-farmbeats.md#weather-data-job-failures).


## <a name="appendix"></a>Přílohy

|        Partner   |  Podrobnosti   |
| ------- | -------             |
|     DockerDetails – imageName         |          Název bitové kopie Docker. Například docker.io/mydockerimage (Image in hub.docker.com) nebo myazureacr.azurecr.io/mydockerimage (obrázek v Azure Container Registry) a tak dále. Pokud není k dispozici žádný registr, výchozí hodnota je hub.docker.com.      |
|          DockerDetails - imageTag             |         Název značky obrázku Docker Výchozí hodnota je "nejnovější".     |
|  DockerDetails – přihlašovací údaje      |  Přihlašovací údaje pro přístup k soukromému Docker. Partner poskytne přihlašovací údaje.   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch SKU virtuálního počítače. Další informace najdete v tématu [všechny dostupné virtuální počítače se systémem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <BR> <BR> Platné hodnoty jsou ' Small ', ' ExtraLarge ', ' large ', ' A8 ', ' Medium ', ' a5 ', ' A6 ', ' A7 ', ' STANDARD_D1 ', ' STANDARD_D2 ', ' STANDARD_D3 ', ' STANDARD_D4 ', ' STANDARD_D11 ', ' STANDARD_D12 ', ' STANDARD_D13 ', ' STANDARD_D14 ', ' A10 ', ' A11 ', ' STANDARD_D1_V2 ', ' STANDARD_D2_V2 ', ' STANDARD_D3_V2 ', ' STANDARD_D4_V2 ', ' STANDARD_D11_V2 ', ' STANDARD_D12_V2 ', ' STANDARD_D13_V2 ', ' STANDARD_D14_V2 ', ' STANDARD_G1 '; ' STANDARD_G2 ' , ' STANDARD_G5 ', ' STANDARD_D5_V2 ', ' BASIC_A1 ', ' BASIC_A2 ', ' BASIC_A3 ', ' BASIC_A4 ', ' STANDARD_A1 ', ' STANDARD_A2 ', ' STANDARD_A3 ', ' STANDARD_A4 ', ' STANDARD_A5 ', ' STANDARD_A6 ', ' STANDARD_A7 ', ' STANDARD_A8 ', ' STANDARD_A9 ', ' STANDARD_A10 ', ' STANDARD_A11 ', ' STANDARD_D15_V2 ', ' STANDARD_F1 ', ' STANDARD_F2 ', ' STANDARD_F4 ', ' STANDARD_F8 ', ' STANDARD_F16 ', ' STANDARD_NV6 ', ' STANDARD_NV12 ', ' STANDARD_NV24 ', ' STANDARD_NC6 ', ' STANDARD_NC12 ', ' STANDARD_NC24 ', ' STANDARD_NC24r ', ' ' , ' STANDARD_H8 ', ' STANDARD_H8m ', ' STANDARD_H16 ', ' STANDARD_H16m ', ' STANDARD_H16mr ', ' STANDARD_H16r ', ' STANDARD_A1_V2 ', ' STANDARD_A2_V2 ', ' STANDARD_A4_V2 ', ' STANDARD_A8_V2 ', ' STANDARD_A2m_V2 ', ' STANDARD_A4m_V2 ', ' STANDARD_A8m_V2 ', ' STANDARD_M64ms ', ' STANDARD_M128s ', ' STANDARD_D2_V3 ', ' ', ' ' a ' '. *Výchozí hodnota je ' STANDARD_D2_V2 '.*  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Počet vyhrazených uzlů počítače na jeden fond Batch. Výchozí hodnota je 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    ID SKU agenta uzlu Azure Batch. V současné době je podporován pouze agent uzlu Batch. Node. Ubuntu 18,04.    |
| DockerDetails - partnerCredentials | Přihlašovací údaje pro volání partnerského rozhraní API v Docker Partner poskytuje tyto informace na základě podporovaného autorizačního mechanismu; například uživatelské jméno a heslo nebo klíče rozhraní API. |
| partnerType | "Počasí". Další typy partnerů v FarmBeats jsou "senzor" a "satelitní".  |
|  name   |   Požadovaný název partnera v systému FarmBeats.   |
|  Popis |  Popis   |

## <a name="next-steps"></a>Další kroky

Teď, když jste dotazováni data ze senzorů z instance Azure FarmBeats, zjistíte, jak [vygenerovat mapy](generate-maps-in-azure-farmbeats.md#generate-maps) pro vaše farmy.
