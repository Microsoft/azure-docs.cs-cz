---
title: Analýza formátu JSON a AVRO v Azure Stream Analytics
description: Tento článek popisuje, jak pracovat s komplexní datové typy jako pole JSON, CSV ve formátu data.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: daf5b97e4ac586f89e5964ee16ee73c86f59b01d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329365"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Parsovat JSON nebo Avro, data ve službě Azure Stream Analytics

Azure Stream Analytics podporují zpracování událostí v CSV, JSON a Avro datových formátů. Data JSON nebo Avro, může být strukturovaná a obsahují některé komplexní typy, jako jsou vnořené objekty (záznamy) a pole. 




## <a name="record-data-types"></a>Záznam datové typy
Typy záznamů dat se používá k reprezentování pole JSON a Avro případě odpovídající formáty používají vstupní datové proudy. Tyto příklady ukazují senzoru vzorku, který čte vstupní události ve formátu JSON. Tady je příklad jednu událost:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```


### <a name="access-nested-fields-in-known-schema"></a>Přístup vnořená pole ve známé schématu
Pomocí zápisu s tečkou (.) pro snadný přístup k vnořená pole přímo z dotazu. Například tento dotaz vybere souřadnice zeměpisné šířky a délky pod vlastností umístění v předchozím data JSON. Zápisu s tečkou slouží k navigaci více úrovní, jak je znázorněno níže.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Vyberte všechny vlastnosti
Můžete vybrat všechny vlastnosti vnořené záznam pomocí "*" zástupný znak. Vezměte v úvahu v následujícím příkladu:

```SQL
SELECT input.Location.*
FROM input
```

Výsledkem je:

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Přístup vnořená pole při název vlastnosti je proměnná
Použití [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) fungovat, pokud název vlastnosti není proměnná. 

Představte si například, že ukázkový datový proud musí být spojen s referenční data obsahující prahové hodnoty pro každý ze senzorů zařízení. Fragment kódu těchto referenčních dat je uveden níže.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>Převést pole záznamu do samostatných události
Chcete-li převést pole záznamu samostatné události, použijte [použít](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operátor spolu s [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) funkce. Například pokud předchozí příklad měli několik záznamů pro SensorReading, následující dotaz mohl být použit k extrakci o různých událostech:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Pole datových typů

Datové typy pole jsou uspořádaná kolekce hodnot. Některé typické operací na hodnotách pole je podrobně popsaný níže. Tyto příklady předpokládají, že vstupní události mají vlastnost s názvem "arrayField", který je datového typu pro pole.

Tyto příklady používají funkce [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)a [použít](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operátor.

### <a name="working-with-a-specific-array-element"></a>Práce s konkrétní pole elementu
Vyberte pole prvku na zadaném indexu (výběrem první prvek pole):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Vyberte délka pole

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Převést elementy pole do samostatných události
Vyberte všechny elementu pole jako jednotlivé události. [Použít](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operátor spolu s [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) předdefinované funkce extrahuje všechny prvky pole jako jednotlivé události:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>Viz také
[Datové typy ve službě Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
