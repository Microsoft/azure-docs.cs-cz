---
title: Analýza JSON a AVRO v Azure Stream Analytics
description: Tento článek popisuje, jak pracovat s komplexními datovými typy, jako jsou pole, JSON a formátovaná data ve formátu CSV.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: conceptual
ms.date: 01/29/2020
ms.custom: devx-track-js
ms.openlocfilehash: f6cb131fb3ff3cab4122aac5e1c6960dee4f8421
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012236"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analyzovat data JSON a Avro v Azure Stream Analytics

Azure Stream Analytics podporovat zpracování událostí v datových formátech CSV, JSON a Avro. Data JSON a Avro mohou být strukturovaná a obsahují některé komplexní typy, jako jsou například vnořené objekty (záznamy) a pole. 

>[!NOTE]
>Soubory AVRO vytvořené pomocí centra událostí zachycení používají konkrétní formát, který vyžaduje použití *vlastní funkce deserializace* . Další informace najdete v tématu [čtení vstupu v jakémkoli formátu pomocí vlastních deserializátorů .NET](./custom-deserializer-examples.md).
>
>Deserializace Stream Analytics AVRO nepodporuje typ mapy. Stream Analytics nemůže číst objekty blob pro zachycení EventHub, protože zachycení EventHub používá mapu.


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
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

Výsledek je následující:

|DeviceID|Připojí|Dlouhou|Teplota|Verze|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Vybrat všechny vlastnosti
Můžete vybrat všechny vlastnosti vnořeného záznamu pomocí zástupného znaku *. Uvažujte následující příklad:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Výsledek je následující:

|DeviceID|Připojí|Dlouhou|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Přístup k vnořeným polím, pokud je název vlastnosti proměnná

Použijte funkci [GetRecordPropertyValue](/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) , pokud je název vlastnosti proměnná. To umožňuje vytvářet dynamické dotazy bez názvů vlastností zakódujeme.

Představte si například, že vzorový datový proud musí **být spojen s referenčními daty** obsahujícími prahové hodnoty pro každý senzor zařízení. Fragment těchto referenčních dat je uveden níže.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

Cílem je připojit se k naší ukázkové sadě dat z horní části článku k těmto referenčním datům a výstupem jedné události pro každou míru snímače nad rámec její prahové hodnoty. To znamená, že naše jediná událost může vygenerovat více výstupních událostí, pokud je více senzorů nad rámec jejich odpovídajících prahových hodnot, a to díky JOIN. Chcete-li dosáhnout podobných výsledků bez připojení, přečtěte si část níže.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue** vybere vlastnost v *SensorReadings*, která název odpovídá názvu vlastnosti přicházejícímu z referenčních dat. Pak je extrahována přidružená hodnota z *SensorReadings* .

Výsledek je následující:

|DeviceID|Senzor|Zadaná hodnota alertmessage|
|-|-|-|
|12345|Vlhkost|Výstraha: senzor nad prahovou hodnotou|

### <a name="convert-record-fields-into-separate-events"></a>Převod polí záznamu na samostatné události

Chcete-li převést pole záznamu na samostatné události, použijte operátor [Apply](/stream-analytics-query/apply-azure-stream-analytics) společně s funkcí [GetRecordProperties](/stream-analytics-query/getrecordproperties-azure-stream-analytics) .

S původními ukázkovými daty můžete k extrakci vlastností do různých událostí použít následující dotaz.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Výsledek je následující:

|DeviceID|Senzor|Zadaná hodnota alertmessage|
|-|-|-|
|12345|Teplota|80|
|12345|Vlhkost|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[objekt objektu]|

Pomocí [s](/stream-analytics-query/with-azure-stream-analytics)můžete tyto události směrovat do různých umístění:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>Analyzovat záznam JSON v referenčních datech SQL
Při použití Azure SQL Database jako referenčních dat v rámci úlohy je možné mít sloupec, který má data ve formátu JSON. Příklad najdete níže.

|DeviceID|Data|
|-|-|
|12345|{"Key": "hodnota1"}|
|54321|{"Key": "hodnota2"}|

Záznam JSON můžete analyzovat ve sloupci *dat* tak, že napíšete jednoduchou uživatelsky definovanou funkci JavaScriptu.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

Pak můžete vytvořit krok v dotazu Stream Analytics, jak je znázorněno níže, a získat tak přístup k polím záznamů JSON.

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>Datové typy polí

Datové typy pole jsou seřazené kolekce hodnot. Některé typické operace s hodnotami polí jsou popsány níže. V těchto příkladech se používají funkce [GetArrayElement](/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](/stream-analytics-query/getarraylength-azure-stream-analytics)a operátor [Apply](/stream-analytics-query/apply-azure-stream-analytics) .

Tady je příklad jedné události. `CustomSensor03`A `SensorMetadata` jsou typu **Array**:

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Práce s konkrétním prvkem pole

Vybrat prvek pole v zadaném indexu (Výběr prvního prvku pole):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Výsledek je následující:

|firstElement|
|-|
|12|

### <a name="select-array-length"></a>Vybrat délku pole

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Výsledek je následující:

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Převést elementy pole na samostatné události

Vyberte všechny prvky pole jako jednotlivé události. Operátor [Apply](/stream-analytics-query/apply-azure-stream-analytics) společně s vestavěnou funkcí [GetArrayElements](/stream-analytics-query/getarrayelements-azure-stream-analytics) extrahuje všechny prvky pole jako jednotlivé události:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Výsledek je následující:

|DeviceId|ArrayIndex|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
Výsledek je následující:

|DeviceId|smKey|smValue|
|-|-|-|
|12345|Manufacturer|ABC|
|12345|Verze|1.2.45|

Pokud se extrahovaná pole musí zobrazit ve sloupcích, je možné datovou sadu pivotovat pomocí syntaxe [with](/stream-analytics-query/with-azure-stream-analytics) kromě operace [Join](/stream-analytics-query/join-azure-stream-analytics) . Toto spojení bude vyžadovat podmínku [časové hranice](/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) , která zabrání duplikaci:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

Výsledek je následující:

|DeviceId|Připojí|Dlouhou|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Viz také
[Datové typy v Azure Stream Analytics](/stream-analytics-query/data-types-azure-stream-analytics)