---
title: Chyby dat protokolu Azure Stream Analytics prostředku
description: Tento článek popisuje různé chyby vstupních a výstupních dat, ke kterým může dojít při použití Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 48f178a74dea0403ff8926cf34fd64cdd9c6839f
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071995"
---
# <a name="azure-stream-analytics-data-errors"></a>Chyby Azure Stream Analytics dat

Chyby dat jsou chyby, ke kterým došlo při zpracování dat.  K těmto chybám dochází nejčastěji během operací zrušení serializace, serializace a zápisu dat.  Pokud dojde k chybám dat, Stream Analytics zapisuje podrobné informace a ukázkové události do protokolů prostředků. Pokud chcete získat další podrobnosti, povolte v úloze diagnostické protokoly. V některých případech je souhrn těchto informací k dispozici také prostřednictvím oznámení na portálu.

Tento článek popisuje různé typy chyb, příčiny a podrobnosti o chybách vstupních a výstupních dat v protokolu prostředků.

## <a name="resource-logs-schema"></a>Schéma protokolů prostředků

V tématu [řešení potíží s Azure Stream Analytics pomocí diagnostických protokolů](stream-analytics-job-diagnostic-logs.md#resource-logs-schema) můžete zobrazit schéma pro protokoly prostředků. Následující JSON je příkladem hodnoty pro pole **vlastnosti** protokolu prostředku pro chybu dat.

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

* Příčina: vybraný typ komprese vstupu neodpovídá datům.
* Poskytnuté oznámení na portálu: Ano
* Úroveň protokolu prostředků: upozornění
* Dopad: zprávy s jakoukoli chybou deserializace, včetně neplatného typu komprese, jsou ze vstupu vypuštěny.
* Podrobnosti protokolu
   * Identifikátor vstupní zprávy V centru událostí je identifikátorem PartitionId, offset a pořadové číslo.

**Chybová zpráva**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Příčina: záhlaví vstupních dat je neplatné. Sdílený svazek clusteru má například sloupce s duplicitními názvy.
* Poskytnuté oznámení na portálu: Ano
* Úroveň protokolu prostředků: upozornění
* Dopad: zprávy s jakýmikoli chybami deserializace, včetně neplatných hlaviček, jsou ze vstupu vypuštěny.
* Podrobnosti protokolu
   * Identifikátor vstupní zprávy 
   * Skutečná datová část až do několika kilobajtů

**Chybová zpráva**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Příčina: neexistuje vstupní sloupec definovaný pomocí CREATE TABLE nebo prostřednictvím časového RAZÍTKa.
* Poskytnuté oznámení na portálu: Ano
* Úroveň protokolu prostředků: upozornění
* Dopad: události, které mají chybějící sloupce, se z vstupu zahozeny.
* Podrobnosti protokolu
   * Identifikátor vstupní zprávy 
   * Názvy sloupců, které chybí. 
   * Skutečná datová část až do několika kilobajtů

**Chybové zprávy**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Příčina: nelze převést vstup na typ zadaný v příkazu CREATE TABLE.
* Poskytnuté oznámení na portálu: Ano
* Úroveň protokolu prostředků: upozornění
* Dopad: události s chybou konverze typu jsou ze vstupu vyřazeny.
* Podrobnosti protokolu
   * Identifikátor vstupní zprávy 
   * Název sloupce a očekávaného typu.

**Chybové zprávy**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Příčina: vstupní data nejsou ve správném formátu. Například vstup není platný formát JSON.
* Poskytnuté oznámení na portálu: Ano
* Úroveň protokolu prostředků: upozornění
* Dopad: všechny události ve zprávě po zjištění neplatného data jsou vyřazeny ze vstupu.
* Podrobnosti protokolu
   * Identifikátor vstupní zprávy 
   * Skutečná datová část až do několika kilobajtů

**Chybové zprávy**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Příčina: hodnota časového RAZÍTKa podle výrazu se nedá převést na typ DateTime.
* Poskytnuté oznámení na portálu: Ano
* Úroveň protokolu prostředků: upozornění
* Dopad: události s neplatným vstupním časovým razítkem jsou ze vstupu vypuštěné.
* Podrobnosti protokolu
   * Identifikátor vstupní zprávy 
   * Chybová zpráva 
   * Skutečná datová část až do několika kilobajtů

**Chybová zpráva**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Příčina: hodnota časového RAZÍTKa prostřednictvím timestampColumn má hodnotu NULL.
* Poskytnuté oznámení na portálu: Ano
* Úroveň protokolu prostředků: upozornění
* Dopad: události s neplatným vstupním klíčem časového razítka jsou ze vstupu vyřazeny.
* Podrobnosti protokolu
   * Skutečná datová část až do několika kilobajtů.

**Chybová zpráva**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Příčina: rozdíl mezi časem a dobou doručení aplikace je větší než zpožděná tolerance doručení.
* Poskytnuté oznámení na portálu: ne
* Úroveň protokolu prostředků: informace
* Dopad: zpožděné vstupní události se zpracovávají podle nastavení "zpracovat jiné události" v části řazení událostí v konfiguraci úlohy. Další informace najdete v tématu [zásady zpracování času](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Podrobnosti protokolu
   * Čas aplikace a čas doručení. 
   * Skutečná datová část až do několika kilobajtů

**Chybová zpráva**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Příčina: rozdíl mezi časem aplikace a dobou doručení je větší než 5 minut.
* Poskytnuté oznámení na portálu: ne
* Úroveň protokolu prostředků: informace
* Dopad: události předčasného vstupu jsou zpracovávány podle nastavení "zpracovat jiné události" v části řazení událostí v konfiguraci úlohy. Další informace najdete v tématu [zásady zpracování času](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Podrobnosti protokolu
   * Čas aplikace a čas doručení. 
   * Skutečná datová část až do několika kilobajtů

**Chybová zpráva**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Příčina: událost je považována za mimo pořadí podle definovaného okna tolerance mimo pořadí.
* Poskytnuté oznámení na portálu: ne
* Úroveň protokolu prostředků: informace
* Dopad: události mimo pořadí jsou zpracovávány podle nastavení "zpracovat jiné události" v části řazení událostí v konfiguraci úlohy. Další informace najdete v tématu [zásady zpracování času](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Podrobnosti protokolu
   * Skutečná datová část až do několika kilobajtů

**Chybová zpráva**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Chyby výstupních dat

Azure Stream Analytics může identifikovat chyby výstupních dat s požadavkem vstupu/výstupu do výstupní jímky v závislosti na konfiguraci. Například chybějící požadovaný sloupec, například  `PartitionKey` , při použití výstupu tabulky Azure, lze identifikovat bez požadavku na vstupně-výstupní operace. Porušení omezení v SQL Output ale vyžadují vstupně-výstupní požadavek.

Došlo k několika chybám dat, které mohou být zjištěny pouze po volání výstupní jímky, což může zpomalit zpracování. Chcete-li tento problém vyřešit, změňte konfiguraci úlohy nebo dotaz, který způsobuje chybu dat.

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Příčina: sloupec vyžadovaný pro výstup neexistuje. Například sloupec, který je definován jako Azure Table PartitionKey does't, existuje.
* Poskytnuté oznámení na portálu: Ano
* Úroveň protokolu prostředků: upozornění
* Dopad: všechny chyby převodu výstupních dat, včetně chybějících požadovaných sloupců, se zpracovávají podle nastavení [zásad výstupních dat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Podrobnosti protokolu
   * Název sloupce a buď identifikátor záznamu, nebo část záznamu.

**Chybová zpráva**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Příčina: hodnota sloupce nevyhovuje výstupu. Například název sloupce není platný sloupec tabulky Azure.
* Poskytnuté oznámení na portálu: Ano
* Úroveň protokolu prostředků: upozornění
* Dopad: všechny chyby převodu výstupních dat, včetně neplatného názvu sloupce, se zpracovávají podle nastavení [zásad výstupních dat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Podrobnosti protokolu
   * Název sloupce a buď identifikátor záznamu, nebo část záznamu.

**Chybová zpráva**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Příčina: sloupec nejde převést na platný typ ve výstupu. Například hodnota sloupce je nekompatibilní s omezeními nebo typem definovaným v tabulce SQL.
* Poskytnuté oznámení na portálu: Ano
* Úroveň protokolu prostředků: upozornění
* Dopad: všechny chyby převodu výstupních dat, včetně chyby konverze typu, se zpracovávají podle nastavení [zásad výstupních dat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Podrobnosti protokolu
   * Název sloupce
   * Buď identifikátor záznamu, nebo část záznamu.

**Chybová zpráva**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Příčina: hodnota zprávy je větší než podporovaná velikost výstupu. Například záznam je větší než 1 MB pro výstup centra událostí.
* Poskytnuté oznámení na portálu: Ano
* Úroveň protokolu prostředků: upozornění
* Dopad: všechny chyby převodu výstupních dat, včetně omezení velikosti překročení záznamu, se zpracovávají podle nastavení [zásad výstupních dat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Podrobnosti protokolu
   * Buď identifikátor záznamu, nebo část záznamu.

**Chybová zpráva**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Příčina: záznam již obsahuje sloupec se stejným názvem jako systémový sloupec. Například výstup CosmosDB se sloupcem s názvem ID, když sloupec ID je na jiný sloupec.
* Poskytnuté oznámení na portálu: Ano
* Úroveň protokolu prostředků: upozornění
* Dopad: všechny chyby převodu výstupních dat, včetně duplicitního klíče, se zpracovávají podle nastavení [zásad výstupních dat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Podrobnosti protokolu
   * Název sloupce
   * Buď identifikátor záznamu, nebo část záznamu.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s Azure Stream Analytics pomocí diagnostických protokolů](stream-analytics-job-diagnostic-logs.md)

* [Pochopení Stream Analytics monitorování úloh a postup monitorování dotazů](stream-analytics-monitoring.md)
