---
title: Analýza formátu JSON a AVRO v Azure Stream Analytics
description: Tento článek popisuje, jak pracovat s komplexní datové typy jako pole JSON, CSV ve formátu data.
services: stream-analytics
ms.service: stream-analytics
author: jasonwhowell
ms.author: mamccrea
manager: kfile
ms.topic: conceptual
ms.date: 08/03/2018
ms.openlocfilehash: 456167d3f17811aa77bb0c43165e5e70bb641814
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979008"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Parsovat JSON nebo Avro, data ve službě Azure Stream Analytics

Azure Stream Analytics podporuje zpracování událostí v CSV, JSON a Avro datových formátů. Komplexní typy, jako jsou vnořené objekty (záznamy) a pole může obsahovat data JSON a Avro. 
  
## <a name="array-data-types"></a>Pole datových typů  
Datové typy pole jsou uspořádaná kolekce hodnot. Některé typické operací na hodnotách pole je podrobně popsaný níže. Tyto příklady předpokládají, že vstupní události mají vlastnost s názvem "arrayField", který je datového typu pro pole.

Tyto příklady používají funkce [GetArrayElement](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelement-azure-stream-analytics), [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics), [GetArrayLength](https://msdn.microsoft.com/azure/stream-analytics/reference/getarraylength-azure-stream-analytics)a [použít](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operátor.

## <a name="examples"></a>Příklady  
 Vyberte pole prvku na zadaném indexu (výběrem první prvek pole):  
  
```SQL 
SELECT   
    GetArrayElement(arrayField, 0) AS firstElement  
FROM input  
```  
  
 Vyberte délka pole:  
  
```SQL  
SELECT   
    GetArrayLength(arrayField) AS arrayLength  
FROM input  
```  
  
Vyberte všechny elementu pole jako jednotlivé události. [Použít](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operátor spolu s [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics) předdefinované funkce extrahuje všechny prvky pole jako jednotlivé události:  
  
```SQL  
SELECT   
    arrayElement.ArrayIndex,  
    arrayElement.ArrayValue  
FROM input as event  
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement  
```  
  
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
        "CustomSensor02" : 99  
    }  
}  
```  
  
## <a name="examples"></a>Příklady  
Pomocí zápisu s tečkou (.) pro přístup k vnořená pole. Například tento dotaz vybere Lat a dlouho koordinuje pod vlastností umístění v předchozím data JSON: 
  
```SQL  
SELECT  
    DeviceID,  
    Location.Lat,  
    Location.Long  
FROM input  
```  

Použití [GetRecordPropertyValue](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordpropertyvalue-azure-stream-analytics) fungovat, pokud Neznámý název vlastnosti. Představte si například, že ukázkový datový proud musí být spojen s referenční data obsahující prahové hodnoty pro každý ze senzorů zařízení:  

```json  
{  
    "DeviceId" : "12345",  
    "SensorName" :  "Temperature",
    "Value" : 75
}  
```  
  
```SQL  
SELECT  
    input.DeviceID,  
    thresholds.SensorName  
FROM input  
JOIN thresholds  
ON  
    input.DeviceId = thresholds.DeviceId  
WHERE  
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value  
```  
  
Chcete-li převést pole záznamu samostatné události, použijte [použít](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operátor spolu s [GetRecordProperties](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordproperties-azure-stream-analytics) funkce. Například pokud chcete převést ukázkový datový proud na datový proud událostí s údajů jednotlivých snímačů přes, tento dotaz mohl být použit:  
  
```SQL  
SELECT   
    event.DeviceID,  
    sensorReading.PropertyName,  
    sensorReading.PropertyValue  
FROM input as event  
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading  
```  

Můžete vybrat všechny vlastnosti vnořené záznam pomocí "*" zástupný znak. Vezměte v úvahu v následujícím příkladu:  

```SQL  
SELECT input.SensorReadings.*  
FROM input  
```  

Výsledkem je:  

```json  
{  
    "Temperature" : 80,  
    "Humidity" : 70,  
    "CustomSensor01" : 5,  
    "CustomSensor022" : 99  
}  
```  
  
## <a name="see-also"></a>Viz také  
 [Datové typy ve službě Azure Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/data-types-azure-stream-analytics)  
