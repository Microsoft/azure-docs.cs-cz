---
title: Azure Stream Analytics diagnostický protokol data chyby
description: Tento článek vysvětluje různé vstupní a výstupní data chyby, které může dojít, když pomocí Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: b00eb12092838746f4bfe16f00eac55df9224b09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65607225"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure Stream Analytics data chyby

Když jsou někde nějaké nesrovnalosti v úloze Azure Stream Analytics zpracovává data, odešle Stream Analytics data chybová událost diagnostické protokoly. Stream Analytics zapíše do jeho diagnostické protokoly, když dojde k chybám dat podrobné informace a příklady událostí. Souhrn těchto informací je k dispozici také prostřednictvím portálu oznámení pro některé chyby.

Tento článek popisuje různé chybové typy, příčiny a podrobnosti diagnostický protokol pro vstupní a výstupní data chyby.

## <a name="diagnostic-log-schema"></a>Diagnostický protokol schématu

Zobrazit [Poradce při potížích s Azure Stream Analytics s využitím diagnostických protokolů](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) abychom viděli toto schéma pro diagnostické protokoly. Následující kód JSON je příkladem hodnoty pro **vlastnosti** pole protokolu diagnostiky dat chyby.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Vstupní data chyby

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Příčina: Vstupní komprese typu vybraného neodpovídá data.
* Oznámení portálu k dispozici: Ano
* Úroveň diagnostických protokolů: Upozornění
* Podrobnosti protokolu
   * Zadejte identifikátor zprávy. Pro Centrum událostí je identifikátor ID oddílu, posun a pořadovým číslem.

**Chybová zpráva**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Příčina: Hlavička vstupních dat je neplatná. Například sdílené svazky clusteru má sloupce s duplicitními názvy.
* Oznámení portálu k dispozici: Ano
* Úroveň diagnostických protokolů: Upozornění
* Podrobnosti protokolu
   * Zadejte identifikátor zprávy. 
   * Skutečné datové části až několika kilobajtů.

**Chybová zpráva**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Příčina: Vstupní sloupce definované s CREATE TABLE nebo prostřednictvím TIMESTAMP BY neexistuje.
* Oznámení portálu k dispozici: Ano
* Úroveň diagnostických protokolů: Upozornění
* Podrobnosti protokolu
   * Zadejte identifikátor zprávy. 
   * Názvy sloupců, které nebyly nalezeny. 
   * Skutečné datové části až několika kilobajtů.

**Chybové zprávy**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Příčina: Vstup nelze převést typ určený v příkazu CREATE TABLE.
* Oznámení portálu k dispozici: Ano
* Úroveň diagnostických protokolů: Upozornění
* Podrobnosti protokolu
   * Zadejte identifikátor zprávy. 
   * Název sloupce a očekávaného typu.

**Chybové zprávy**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Příčina: Vstupních dat není ve správném formátu. Například vstup není platný kód JSON.
* Oznámení portálu k dispozici: Ano
* Úroveň diagnostických protokolů: Upozornění
* Podrobnosti protokolu
   * Zadejte identifikátor zprávy. 
   * Skutečné datové části až několika kilobajtů.

**Chybové zprávy**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Příčina: Hodnota TIMESTAMP BY výrazu nelze převést na typ datetime.
* Oznámení portálu k dispozici: Ano
* Úroveň diagnostických protokolů: Upozornění
* Podrobnosti protokolu
   * Zadejte identifikátor zprávy. 
   * Chybová zpráva. 
   * Skutečné datové části až několika kilobajtů.

**Chybová zpráva**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Příčina: Hodnota TIMESTAMP BY OVER timestampColumn má hodnotu NULL.
* Oznámení portálu k dispozici: Ano
* Úroveň diagnostických protokolů: Upozornění
* Podrobnosti protokolu
   * Skutečné datové části až několika kilobajtů.

**Chybová zpráva**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Příčina: Rozdíl mezi časem aplikace a čas přijetí je větší než pozdní okno tolerance pozdního přijetí.
* Oznámení portálu k dispozici: Ne
* Úroveň diagnostických protokolů: Informace
* Podrobnosti protokolu
   * Čas aplikace a čas přijetí. 
   * Skutečné datové části až několika kilobajtů.

**Chybová zpráva**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Příčina: Rozdíl mezi časem aplikace a čas přijetí je větší než 5 minut.
* Oznámení portálu k dispozici: Ne
* Úroveň diagnostických protokolů: Informace
* Podrobnosti protokolu
   * Čas aplikace a čas přijetí. 
   * Skutečné datové části až několika kilobajtů.

**Chybová zpráva**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Příčina: Událost je považována za mimo pořadí podle okno tolerance mimo pořadí definované.
* Oznámení portálu k dispozici: Ne
* Úroveň diagnostických protokolů: Informace
* Podrobnosti protokolu
   * Skutečné datové části až několika kilobajtů.

**Chybová zpráva**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Chyb výstupních dat

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Příčina: Vyžaduje se pro výstup sloupec neexistuje. Například sloupec definovaný jako položka hierarchyinfoguid Azure Table PartitionKey existovat.
* Oznámení portálu k dispozici: Ano
* Úroveň diagnostických protokolů: Upozornění
* Podrobnosti protokolu
   * Název sloupce a identifikátor záznamu nebo součástí záznamu.

**Chybová zpráva**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Příčina: Hodnota sloupce neodpovídají s výstupem. Například název sloupce není sloupec platné tabulky Azure.
* Oznámení portálu k dispozici: Ano
* Úroveň diagnostických protokolů: Upozornění
* Podrobnosti protokolu
   * Název sloupce a buď identifikátor záznamu nebo součástí záznamu.

**Chybová zpráva**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Příčina: Sloupec nelze převést na platný typ ve výstupu. Například hodnota sloupce není kompatibilní s omezením nebo typ definovaný v tabulce SQL.
* Oznámení portálu k dispozici: Ano
* Úroveň diagnostických protokolů: Upozornění
* Podrobnosti protokolu
   * Název sloupce.
   * Identifikátor záznamu nebo celý záznam.

**Chybová zpráva**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Příčina: Hodnota zprávy je větší než velikost podporované výstupu. Například záznam je větší než 1 MB pro výstup centra událostí.
* Oznámení portálu k dispozici: Ano
* Úroveň diagnostických protokolů: Upozornění
* Podrobnosti protokolu
   * Identifikátor záznamu nebo celý záznam.

**Chybová zpráva**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Příčina: Záznam již obsahuje sloupec se stejným názvem jako systémový sloupec. Například výstup CosmosDB sloupec s názvem ID při sloupec ID je do jiného sloupce.
* Oznámení portálu k dispozici: Ano
* Úroveň diagnostických protokolů: Upozornění
* Podrobnosti protokolu
   * Název sloupce.
   * Identifikátor záznamu nebo celý záznam.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Další postup

* [Řešení potíží s Azure Stream Analytics s využitím diagnostických protokolů](stream-analytics-job-diagnostic-logs.md)

* [Porozumění sledování úlohu Stream Analytics a monitorování dotazů](stream-analytics-monitoring.md)
