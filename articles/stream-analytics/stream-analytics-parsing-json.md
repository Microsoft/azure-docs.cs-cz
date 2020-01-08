---
title: Analýza JSON a AVRO v Azure Stream Analytics
description: Tento článek popisuje, jak pracovat s komplexními datovými typy, jako jsou pole, JSON a formátovaná data ve formátu CSV.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1741510c7398ce74da81f006cb4109d9a33f8f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431594"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analyzovat data JSON a Avro v Azure Stream Analytics

Azure Stream Analytics podporovat zpracování událostí v datových formátech CSV, JSON a Avro. Data JSON a Avro mohou být strukturovaná a obsahují některé komplexní typy, jako jsou například vnořené objekty (záznamy) a pole. 




## <a name="record-data-types"></a>Záznam typů dat
Datové typy záznamu se používají k reprezentování polí JSON a Avro, když se v datových proudech vstupních dat používají odpovídající formáty. Tyto příklady ukazují vzorový senzor, který čte vstupní události ve formátu JSON. Tady je příklad jedné události:

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


### <a name="access-nested-fields-in-known-schema"></a>Přístup k vnořeným polím ve známém schématu
Pro snadný přístup k vnořeným polím přímo z dotazu použijte tečku (.). Tento dotaz například vybírá souřadnice Zeměpisná šířka a délka pod vlastností Location (umístění) v předchozích datech JSON. Zápis tečky lze použít k procházení více úrovní, jak je znázorněno níže.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Vybrat všechny vlastnosti
Můžete vybrat všechny vlastnosti vnořeného záznamu pomocí zástupného znaku *. Vezměte v úvahu v následujícím příkladu:

```SQL
SELECT input.Location.*
FROM input
```

Výsledek je následující:

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Přístup k vnořeným polím, pokud je název vlastnosti proměnná
Použijte funkci [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) , pokud je název vlastnosti proměnná. 

Představte si například, že vzorový datový proud musí být spojen s referenčními daty obsahujícími prahové hodnoty pro každý senzor zařízení. Fragment těchto referenčních dat je uveden níže.

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

### <a name="convert-record-fields-into-separate-events"></a>Převod polí záznamu na samostatné události
Chcete-li převést pole záznamu na samostatné události, použijte operátor [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) společně s funkcí [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) . Pokud například předchozí příklad obsahovala několik záznamů pro SensorReading, je možné použít následující dotaz k extrakci v různých událostech:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Datové typy polí

Datové typy pole jsou seřazené kolekce hodnot. Některé typické operace s hodnotami polí jsou popsány níže. Tyto příklady předpokládají, že vstupní události mají vlastnost s názvem "arrayField", která je datovým typem pole.

V těchto příkladech se používají funkce [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)a operátor [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) .

### <a name="working-with-a-specific-array-element"></a>Práce s konkrétním prvkem pole
Vybrat prvek pole v zadaném indexu (Výběr prvního prvku pole):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Vybrat délku pole

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Převést elementy pole na samostatné události
Vyberte všechny prvky pole jako jednotlivé události. Operátor [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) společně s vestavěnou funkcí [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) extrahuje všechny prvky pole jako jednotlivé události:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>Viz také
[Datové typy v Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
