---
title: Dotazovaná data telemetrie
description: Tento článek popisuje, jak zadávat dotazy na ingestovaná data telemetrie.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: 2786519bfc54b39c986ba57c78d9d6409a596351
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129783"
---
# <a name="query-ingested-telemetry-data"></a>Dotazovaná data telemetrie

Tento článek popisuje, jak dotazovat data z přijatých senzorů z Azure FarmBeats.

Běžné scénáře v FarmBeats jsou ingestování dat z prostředků Internet věcí (IoT), jako jsou zařízení a senzory. Můžete vytvářet metadata pro zařízení a senzory a následně ingestovat historická data do FarmBeats v kanonickém formátu. Jakmile jsou data snímače k dispozici v datovém centru FarmBeats, můžeme se dotazovat stejně, aby vygenerovala užitečné poznatky nebo modely sestavení.

## <a name="before-you-begin"></a>Než začnete

Než budete pokračovat v tomto článku, ujistěte se, že jste nainstalovali data telemetrie FarmBeats a ingestovaná senzory ze zařízení IoT do FarmBeats.
Pokud chcete přijímat data telemetrie senzorů, přejděte na data ingestování [historických dat telemetrie](ingest-historical-telemetry-data-in-azure-farmbeats.md) .

Než budete pokračovat, ujistěte se taky, že jste obeznámeni s rozhraními REST API FarmBeats, protože se dotazuje na ingestovaná telemetrie pomocí rozhraní API. Další podrobnosti o rozhraních API FarmBeats najdete v tématu [FARMBEATS REST API](rest-api-in-azure-farmbeats.md). **Ujistěte se, že je možné vytvořit požadavky rozhraní API na koncový bod FarmBeats data hub.**

## <a name="query-ingested-sensor-telemetry-data"></a>Dotazovaná data telemetrie senzorů

Existují dva způsoby, jak získat přístup k datům telemetrie z FarmBeats: API a Time Series Insights (TSI) a dotazovat se na ně. 

### <a name="query-using-rest-api"></a>Dotaz pomocí REST API

Pomocí následujících kroků můžete zadat dotaz na data telemetrie ingesta senzorů pomocí rozhraní FarmBeats REST API:

1. Identifikujte senzor, který vás zajímá. To můžete provést tak, že vytvoříte požadavek GET na rozhraní/Sensor API. Poznamenejte si **ID** a **sensorModelId** objektu pro daný senzor.

2. Pro **sensorModelId** , jak je uvedeno v kroku 1, vytvořte/SENSORMODEL API Get/{ID}. "Model senzoru" obsahuje všechna metadata a podrobnosti o ingestované telemetrie ze senzorů. Například "měření snímače" v rámci objektu "model senzoru" obsahuje podrobné informace o tom, jaká opatření se senzor posílá a v jakých typech a jednotkách. Například:

```json
{
    "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
    "dataType": "Double <Data Type - eg. Double>",
    "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
    "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
    "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
    "description": "<Description of the measure>"
}
```
Poznamenejte si odpověď z volání metody GET/{ID} pro model senzoru.

3. Provede volání POST na rozhraní API/Telemetry s následující vstupní datovou částí.

```json
{
  "sensorId": "<id of the sensor as noted in step 1>",
  "searchSpan": {
    "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
    "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
  },
  "filter": {
    "tsx": "string"
  },
  "projectedProperties": [
    {
      "additionalProp1": "string",
      "additionalProp2": "string",
      "additionalProp3": "string"
    }
  ]
}
```
4. Odpověď z rozhraní API/Telemetry bude vypadat přibližně takto:

```json
{
  "timestamps": [
    "2020-XX-XXT07:30:00Z",
    "2020-XX-XXT07:45:00Z"
  ],
  "properties": [
    {
      "values": [
        "<id of the sensor>",
        "<id of the sensor>"
      ],
      "name": "Id",
      "type": "String"
    },
    {
      "values": [
        2.1,
        2.2
      ],
      "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
      "type": "Double <Data Type of the value - eg. Double>"
    }
  ]
}
```
V příkladu výše odpověď provedená telemetrie senzorů poskytuje data pro dvě časová razítka spolu s názvem míry ("moist_soil_last") a hodnotami hlášené telemetrie ve dvou časových razítkech. Budete muset odkazovat na model přidruženého senzoru (jak je popsáno v kroku 2), chcete-li interpretovat typ a jednotku hlášených hodnot.

### <a name="query-using-azure-time-series-insights-tsi"></a>Dotaz pomocí Azure Time Series Insights (TSI)

FarmBeats využívá [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) k ingestování, ukládání, dotazování a vizualizaci dat v Internet věcí (IoT) Scale--data, která jsou s vysokým kontextem a optimalizovaná pro časové řady.

Data telemetrie se přijímají na EventHub a pak se zpracovávají a odešlou do prostředí TSI v rámci skupiny prostředků FarmBeats. Data pak mohou být přímo dotazována z TSI. Další informace najdete v [dokumentaci k TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer) .

Pomocí následujících kroků Vizualizujte data na TSI.

1. Přejděte na portál Azure Portal – > FarmBeats DataHub skupina prostředků – > klikněte na Time Series Insights prostředí (TSI – xxxx) – > zásady přístupu k datům. Přidejte uživatele s přístupem Čtenář nebo Přispěvatel.
2. Přejděte na stránku Přehled prostředí Time Series Insights (TSI-xxxx) a klikněte na adresu URL aplikace Time Series Insights Explorer. Nyní budete moci vizualizovat ingestnou telemetrii.

Kromě ukládání, dotazování a vizualizace telemetrie, TSI taky umožňuje integraci do řídicího panelu Power BI. Další podrobnosti [here]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Další kroky

Nyní máte dotazovaná data senzorů z vaší instance Azure FarmBeats. Nyní se dozvíte, jak [vygenerovat mapy](generate-maps-in-azure-farmbeats.md#generate-maps) pro vaše farmy.
