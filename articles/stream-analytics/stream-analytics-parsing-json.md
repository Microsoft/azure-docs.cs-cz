---
title: Analýza JSON a AVRO v Azure Stream Analytics
description: Tento článek popisuje, jak pracovat na komplexní datové typy, jako jsou pole, JSON, CSV formátovaná data.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 73905483850a47a9d036bef1b9e1ee60d3484555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484583"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analýza dat JSON a Avro v Azure Stream Analytics

Azure Stream Analytics podporuje zpracování událostí ve formátech dat CSV, JSON a Avro. Data JSON i Avro mohou být strukturována a obsahovat některé složité typy, jako jsou vnořené objekty (záznamy) a pole. 

>[!NOTE]
>AVRO soubory vytvořené Event Hub Capture použít určitý formát, který vyžaduje použití *vlastní funkci deserializer.* Další informace naleznete v tématu [Čtení vstupu v libovolném formátu pomocí vlastních deserializátorů rozhraní .NET](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples).



## <a name="record-data-types"></a>Záznam datových typů
Datové typy záznamů se používají k reprezentaci polí JSON a Avro při použití odpovídajících formátů ve vstupních datových proudech. Tyto příklady ukazují ukázkový senzor, který čte vstupní události ve formátu JSON. Zde je příklad jedné události:

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

### <a name="access-nested-fields-in-known-schema"></a>Přístup k vnořená pole ve známém schématu
Pomocí dot notace (.) můžete snadno přistupovat k vnořenám polím přímo z dotazu. Tento dotaz například vybere souřadnice Zeměpisné šířky a délky pod vlastností Location v předchozích datech JSON. Dot notace lze použít k navigaci ve více úrovních, jak je znázorněno níže.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

Výsledkem je:

|Deviceid|Lat|Dlouhé|Teplota|Version|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Vybrat všechny vlastnosti
Pomocí zástupného znaku *můžete vybrat všechny vlastnosti vnořeného záznamu. Uvažujte následující příklad:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Výsledkem je:

|Deviceid|Lat|Dlouhé|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Přístup k vnořená pole, pokud název vlastnosti je proměnná

Pokud je název vlastnosti proměnná, použijte funkci [GetRecordPropertyValue.](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) To umožňuje vytváření dynamických dotazů bez názvů vlastností hardcoding.

Představte si například, že ukázkový datový proud musí **být spojen s referenčními daty** obsahujícími prahové hodnoty pro každý snímač zařízení. Fragment těchto referenčních údajů je uveden níže.

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

Cílem je připojit naše ukázkové datové sady z horní části článku k tomuto referenčnímu datu a výstup jedné události pro každé měření senzoru nad prahovou hodnotou. To znamená, že naše jediná událost výše může generovat více výstupních událostí, pokud je více senzorů nad příslušnými prahy, díky spojení. Chcete-li dosáhnout podobných výsledků bez spojení, podívejte se na následující část.

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

**Funkce GetRecordPropertyValue** vybere vlastnost v *hodnotě SensorReadings*, jejíž název odpovídá názvu vlastnosti pocházejícímu z referenčních dat. Poté je extrahována přidružená hodnota z *SensorReadings.*

Výsledkem je:

|Deviceid|Název senzoru|Zpráva s výstrahou|
|-|-|-|
|12345|Vlhkost|Výstraha : Senzor nad prahovou hodnotou|

### <a name="convert-record-fields-into-separate-events"></a>Převést pole záznamů na samostatné události

Chcete-li převést pole záznamu na samostatné události, použijte operátor [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) společně s funkcí [GetRecordProperties.](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics)

S původní ukázková data následující dotaz lze extrahovat vlastnosti do různých událostí.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Výsledkem je:

|Deviceid|Název senzoru|Zpráva s výstrahou|
|-|-|-|
|12345|Teplota|80|
|12345|Vlhkost|70|
|12345|Vlastnísenzor01|5|
|12345|Vlastnísenzor02|99|
|12345|Metadata senzoru|[objekt objekt]|

Pomocí [funkce WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)je pak možné směrovat tyto události do různých cílů:

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
Při použití Azure SQL Database jako referenční data ve vaší úloze, je možné mít sloupec, který má data ve formátu JSON. Příklad najdete níže.

|Deviceid|Data|
|-|-|
|12345|{"klíč" : "value1"}|
|54321|{"klíč" : "value2"}|

Záznam JSON můžete analyzovat ve sloupci *Data* napsáním jednoduché uživatelem definované funkce JavaScriptu.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

Potom můžete vytvořit krok v dotazu Stream Analytics, jak je znázorněno níže pro přístup k polím vašich záznamů JSON.

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

## <a name="array-data-types"></a>Datové typy pole

Datové typy pole jsou uspořádanou kolekcí hodnot. Některé typické operace na hodnoty pole jsou podrobně popsány níže. Tyto příklady používají funkce [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)a operátor [APPLY.](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics)

Zde je příklad jedné události. Oba `CustomSensor03` `SensorMetadata` a jsou typu **pole**:

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

### <a name="working-with-a-specific-array-element"></a>Práce s určitým prvkem pole

Vyberte prvek pole na zadaném indexu (výběr prvního prvku pole):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Výsledkem je:

|firstElement|
|-|
|12|

### <a name="select-array-length"></a>Vybrat délku pole

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Výsledkem je:

|poleDélka|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Převést prvky pole na samostatné události

Vyberte všechny elementy pole jako jednotlivé události. Operátor [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) spolu s vestavěnou funkcí [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) extrahuje všechny prvky pole jako jednotlivé události:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Výsledkem je:

|DeviceId|Arrayindex|Hodnota pole|
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
 
Výsledkem je:

|DeviceId|smKey|smValue|
|-|-|-|
|12345|Výrobce|ABC|
|12345|Version|1.2.45|

Pokud se extrahovaná pole musí zobrazit ve sloupcích, je možné kromě operace [JOIN](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) převést datovou sadu pomocí syntaxe [WITH.](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) Toto spojení bude vyžadovat podmínku [časové hranice,](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) která zabraňuje duplikaci:

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

Výsledkem je:

|DeviceId|Lat|Dlouhé|smVersion|smVýrobce|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Viz také
[Datové typy ve Službě Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
