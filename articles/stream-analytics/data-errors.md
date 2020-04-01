---
title: Chyby diagnostických protokolů Azure Stream Analytics
description: Tento článek vysvětluje různé chyby vstupních a výstupních dat, ke kterým může dojít při používání Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 5457308d577b95201fa31bfad0a6634a7a79eda3
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398136"
---
# <a name="azure-stream-analytics-data-errors"></a>Chyby dat Azure Stream Analytics

Chyby dat jsou chyby, ke kterým dochází při zpracování dat.  K těmto chybám nejčastěji dochází během operace deserializace, serializace a zápisu dat.  Dojde-li k chybám dat, Stream Analytics zapíše podrobné informace a ukázkové události do diagnostických protokolů.  V některých případech je souhrn těchto informací poskytován také prostřednictvím oznámení portálu.

Tento článek popisuje různé typy chyb, příčiny a podrobnosti diagnostického protokolu pro chyby vstupních a výstupních dat.

## <a name="diagnostic-log-schema"></a>Schéma diagnostického protokolu

Viz [Poradce při potížích s Azure Stream Analytics pomocí protokolů diagnostiky](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) zobrazíte schéma pro diagnostické protokoly. Následující JSON je ukázková hodnota pro pole **Vlastnosti** diagnostického protokolu pro chybu dat.

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

## <a name="input-data-errors"></a>Chyby vstupních dat

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Příčina: Vybraný typ komprese vstupu neodpovídá datům.
* Oznámení portálu: Ano
* Úroveň diagnostického protokolu: Upozornění
* Dopad: Zprávy s chybami deserializace včetně neplatného typu komprese jsou vynechány ze vstupu.
* Podrobnosti protokolu
   * Identifikátor vstupní zprávy. Pro centrum událostí je identifikátor PartitionId, Posun a Pořadové číslo.

**Chybová zpráva**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Příčina: Záhlaví vstupních dat je neplatné. Například csv má sloupce s duplicitními názvy.
* Oznámení portálu: Ano
* Úroveň diagnostického protokolu: Upozornění
* Dopad: Zprávy s chybami deserializace včetně neplatné hlavičky jsou vynechány ze vstupu.
* Podrobnosti protokolu
   * Identifikátor vstupní zprávy. 
   * Skutečná datová část až několik kilobajtů.

**Chybová zpráva**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Příčina: Vstupní sloupce definované pomocí create table nebo prostřednictvím funkce TIMESTAMP BY neexistují.
* Oznámení portálu: Ano
* Úroveň diagnostického protokolu: Upozornění
* Dopad: Události s chybějícími sloupci jsou vynechány ze vstupu.
* Podrobnosti protokolu
   * Identifikátor vstupní zprávy. 
   * Názvy chybějících sloupců. 
   * Skutečná datová část až několik kilobajtů.

**Chybové zprávy**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Příčina: Vstup nelze převést na typ zadaný v příkazu CREATE TABLE.
* Oznámení portálu: Ano
* Úroveň diagnostického protokolu: Upozornění
* Dopad: Události s chybou převodu typu jsou vynechány ze vstupu.
* Podrobnosti protokolu
   * Identifikátor vstupní zprávy. 
   * Název sloupce a očekávaný typ.

**Chybové zprávy**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Příčina: Vstupní data nejsou ve správném formátu. Například vstup není platný JSON.
* Oznámení portálu: Ano
* Úroveň diagnostického protokolu: Upozornění
* Dopad: Všechny události ve zprávě po výskytu neplatné chyby dat jsou vynechány ze vstupu.
* Podrobnosti protokolu
   * Identifikátor vstupní zprávy. 
   * Skutečná datová část až několik kilobajtů.

**Chybové zprávy**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Příčina: Hodnotu výrazu TIMESTAMP BY nelze převést na datetime.
* Oznámení portálu: Ano
* Úroveň diagnostického protokolu: Upozornění
* Dopad: Události s neplatným vstupním časovým razítkem jsou vynechány ze vstupu.
* Podrobnosti protokolu
   * Identifikátor vstupní zprávy. 
   * Chybová zpráva. 
   * Skutečná datová část až několik kilobajtů.

**Chybová zpráva**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>Neplatný klíč InputTimeStampKey

* Příčina: Hodnota timestamp by over timestampcolumn je NULL.
* Oznámení portálu: Ano
* Úroveň diagnostického protokolu: Upozornění
* Dopad: Události s neplatným vstupním klíčem časového razítka jsou vynechány ze vstupu.
* Podrobnosti protokolu
   * Skutečná datová část až několik kilobajtů.

**Chybová zpráva**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Příčina: Rozdíl mezi časem aplikace a časem doručení je větší než okno tolerance pozdního doručení.
* Oznámení portálu: Ne
* Úroveň diagnostického protokolu: Informace
* Dopad: Pozdní vstupní události jsou zpracovány podle nastavení "Zpracování jiných událostí" v části Pořadí událostí konfigurace úlohy. Další informace naleznete v [tématu Zásady zpracování času](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Podrobnosti protokolu
   * Čas aplikace a čas příjezdu. 
   * Skutečná datová část až několik kilobajtů.

**Chybová zpráva**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Příčina: Rozdíl mezi časem aplikace a časem doručení je větší než 5 minut.
* Oznámení portálu: Ne
* Úroveň diagnostického protokolu: Informace
* Dopad: Události včasného vstupu jsou zpracovány podle nastavení "Zpracování jiných událostí" v části Pořadí událostí konfigurace úlohy. Další informace naleznete v [tématu Zásady zpracování času](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Podrobnosti protokolu
   * Čas aplikace a čas příjezdu. 
   * Skutečná datová část až několik kilobajtů.

**Chybová zpráva**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Příčina: Událost je považována za neuspořádanou podle definovaného okna tolerance mimo pořadí.
* Oznámení portálu: Ne
* Úroveň diagnostického protokolu: Informace
* Dopad: Události mimo pořadí jsou zpracovány podle nastavení "Zpracování jiných událostí" v části Pořadí událostí konfigurace úlohy. Další informace naleznete v [tématu Zásady zpracování času](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Podrobnosti protokolu
   * Skutečná datová část až několik kilobajtů.

**Chybová zpráva**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Chyby výstupních dat

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Příčina: Sloupec požadovaný pro výstup neexistuje. Například sloupec definovaný jako Azure Table PartitionKey neexistuje.
* Oznámení portálu: Ano
* Úroveň diagnostického protokolu: Upozornění
* Dopad: Všechny chyby převodu výstupních dat včetně chybějícího požadovaného sloupce jsou zpracovány podle nastavení [Zásady výstupních dat.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Podrobnosti protokolu
   * Název sloupce a identifikátor záznamu nebo jeho část.

**Chybová zpráva**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Příčina: Hodnota sloupce neodpovídá výstupu. Například název sloupce není platný sloupec tabulky Azure.
* Oznámení portálu: Ano
* Úroveň diagnostického protokolu: Upozornění
* Dopad: Všechny chyby převodu výstupních dat včetně neplatného názvu sloupce jsou zpracovány podle nastavení [Zásady výstupních dat.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Podrobnosti protokolu
   * Název sloupce a identifikátor záznamu nebo část záznamu.

**Chybová zpráva**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Příčina: Sloupec nelze převést na platný typ ve výstupu. Například hodnota sloupce není kompatibilní s omezeními nebo typem definovaným v tabulce SQL.
* Oznámení portálu: Ano
* Úroveň diagnostického protokolu: Upozornění
* Dopad: Všechny chyby převodu výstupních dat včetně chyby převodu typu jsou zpracovány podle nastavení [Zásady výstupních dat.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Podrobnosti protokolu
   * Název sloupce.
   * Identifikátor záznamu nebo jeho část.

**Chybová zpráva**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Příčina: Hodnota zprávy je větší než podporovaná velikost výstupu. Například záznam je větší než 1 MB pro výstup centra událostí.
* Oznámení portálu: Ano
* Úroveň diagnostického protokolu: Upozornění
* Dopad: Všechny chyby převodu výstupních dat včetně limitu velikosti překročení záznamu jsou zpracovány podle nastavení [Zásady výstupních dat.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Podrobnosti protokolu
   * Identifikátor záznamu nebo jeho část.

**Chybová zpráva**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Příčina: Záznam již obsahuje sloupec se stejným názvem jako sloupec Systém. Například výstup CosmosDB se sloupcem s názvem ID, když je sloupec ID do jiného sloupce.
* Oznámení portálu: Ano
* Úroveň diagnostického protokolu: Upozornění
* Dopad: Všechny chyby převodu výstupních dat včetně duplicitního klíče jsou zpracovány podle nastavení [Zásady výstupních dat.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Podrobnosti protokolu
   * Název sloupce.
   * Identifikátor záznamu nebo jeho část.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Další kroky

* [Poradce při potížích s Azure Stream Analytics pomocí protokolů diagnostiky](stream-analytics-job-diagnostic-logs.md)

* [Principy monitorování úloh Služby Stream Analytics a monitorování dotazů](stream-analytics-monitoring.md)
