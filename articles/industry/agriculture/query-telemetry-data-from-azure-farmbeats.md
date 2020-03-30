---
title: Dotaz na data s polykací telemetrie
description: Tento článek popisuje, jak dotaz ingestovaná data telemetrie.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349808"
---
# <a name="query-ingested-telemetry-data"></a>Dotaz na data s polykací telemetrie

Tento článek popisuje, jak dotaz ovat data ze senzorů Azure FarmBeats.

Ingestování dat z prostředků Internetu věcí (IoT), jako jsou zařízení a senzory, je běžným scénářem v FarmBeats. Vytvoříte metadata pro zařízení a senzory a potom ingestovat historická data FarmBeats v kanonickém formátu. Jakmile jsou data senzorů k dispozici na FarmBeats Datahub, můžeme dotaz ovat stejné generovat užitečné přehledy nebo vytvářet modely.

## <a name="before-you-begin"></a>Než začnete

Než budete pokračovat v tomto článku, ujistěte se, že jste nainstalovali FarmBeats a pozůstalé data telemetrie senzoru z vašich zařízení IoT do FarmBeats.

Chcete-li ingestovat telemetrická data senzoru, navštivte [historická telemetrická data](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Než budete pokračovat, musíte také zajistit, že jste obeznámeni s FarmBeats REST API, jak budete dotaz ovat ingestované telemetrie pomocí api. Další informace o api farmbeats naleznete v [tématu FarmBeats REST API](rest-api-in-azure-farmbeats.md). **Ujistěte se, že jste schopni provádět požadavky api na váš koncový bod FarmBeats Datahub**.

## <a name="query-ingested-sensor-telemetry-data"></a>Dotaz na daty telemetrie senzoru s polykací

Existují dva způsoby, jak přistupovat k telemetrickým datům farmbeatů a zadávat se za jedou dotazovat:

- API a
- Časové řady (TSI).

### <a name="query-using-rest-api"></a>Dotaz pomocí rozhraní REST API

Podle pokynů se můžete dotazovat na telemetrická data ingestovaného senzoru pomocí api REST FarmBeats:

1. Identifikujte senzor, který vás zajímá. Můžete to provést tak, že požadavek GET na /Sensor API.

> [!NOTE]
> **Id** a **senzorModelId** objektu zainteresovaného senzoru.

2. Proveďte GET/{id} na /SensorModel API pro **sensorModelId,** jak je uvedeno v kroku 1. "Model senzoru" má všechna metadata a podrobnosti o požité telemetrii ze senzoru. **Například senzor měření** v rámci modelu **senzoru** objektu má podrobnosti o tom, jaké míry je odesílání senzoru a v jaké typy a jednotky. Například:

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
Poznamenejte si odpověď z volání GET/{id} pro model senzoru.

3. Do volání POST na /Telemetry API s následující vstupní datové části

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
4. Odpověď z /Telemetry API bude vypadat nějak takto:

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
Ve výše uvedeném příkladu odpovědi dotazovaného senzoru poskytuje data pro dvě časová razítka spolu s názvem míry ("moist_soil_last") a hodnotami hlášené telemetrie ve dvou časových razítkách. Budete muset odkazovat na přidružený model senzoru (jak je popsáno v kroku 2) interpretovat typ a jednotku vykázaných hodnot.

### <a name="query-using-azure-time-series-insights-tsi"></a>Dotaz pomocí Azure Time Series Insights (TSI)

FarmBeats využívá [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) k ingestování, ukládání, dotazování a vizualizaci dat ve škále IoT – data, která jsou vysoce kontextualizovaná a optimalizovaná pro časové řady.

Telemetrická data jsou přijímána na EventHub a pak zpracována a zatlačena do prostředí TSI v rámci skupiny prostředků FarmBeats. Data pak mohou být přímo dotazována z TSI. Další informace naleznete v [dokumentaci TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)

Vizuací dat o TSI postupujte takto:

1. Přejděte na **skupinu** > prostředků Azure Portal**FarmBeats DataHub** > vyberte prostředí **Time Series Insights** (tsi-xxxx) > **zásady přístupu k datům**. Přidejte uživatele pomocí přístupu ke čtečce nebo přispěvateli.
2. Přejděte na stránku **Přehled** prostředí **Time Series Insights** (tsi-xxxx) a vyberte adresu URL **průzkumníka přehledů časové řady**. Nyní budete moci vizualizovat ingestované telemetrie.

Kromě ukládání, dotazování a vizualizace telemetrie umožňuje TSI také integraci do řídicího panelu Power BI. Další informace naleznete [zde]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Další kroky

Nyní jste se dotazovali na data senzorů z instance Azure FarmBeats. Nyní se naučte vytvářet [mapy](generate-maps-in-azure-farmbeats.md#generate-maps) pro vaše farmy.
