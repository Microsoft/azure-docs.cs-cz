---
title: Data Factory funkce a systémové proměnné
description: Poskytuje seznam funkcí Azure Data Factory a systémových proměnných.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 498f40a1783903b5dca0a2fe3204cc6aa25a2fec
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786426"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory – funkce a systémové proměnné
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [systémové proměnné v Data Factory](../control-flow-system-variables.md).

Tento článek poskytuje informace o funkcích a proměnných, které podporuje Azure Data Factory.

## <a name="data-factory-system-variables"></a>Data Factory systémových proměnných

| Název proměnné | Popis | Rozsah objektu | Rozsah JSON a případy použití |
| --- | --- | --- | --- |
| WindowStart |Začátek časového intervalu pro aktuální okno spuštění aktivit |aktivita |<ol><li>Zadejte dotazy pro výběr dat. Viz články o konektorech, na které se odkazuje v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) .</li> |
| WindowEnd |Konec časového intervalu pro aktuální okno spuštění aktivit |aktivita |stejné jako WindowStart. |
| Vlastnosti slicestart |Začátek časového intervalu pro vyprodukování datového řezu |aktivita<br/>integrován |<ol><li>Při práci s objekty [blob Azure](data-factory-azure-blob-connector.md) a [datovými sadami systému souborů](data-factory-onprem-file-system-connector.md)zadejte dynamické cesty ke složkám a názvy souborů.</li><li>Zadejte vstupní závislosti s funkcemi objektu pro vytváření dat v kolekci vstupy aktivity.</li></ol> |
| SliceEnd |Konec časového intervalu pro aktuální datový řez |aktivita<br/>integrován |stejné jako vlastnosti slicestart. |

> [!NOTE]
> V současné době Data Factory vyžaduje, aby plán zadaný v aktivit přesně odpovídal plánu zadanému v dostupnosti výstupní datové sady. Proto jsou WindowStart, WindowEnd a vlastnosti slicestart a SliceEnd vždy mapovány na stejné časové období a jeden výstupní řez.
> 

### <a name="example-for-using-a-system-variable"></a>Příklad použití systémové proměnné
V následujícím příkladu jsou extrahovány roky, měsíc, den a čas **vlastnosti slicestart** do samostatných proměnných, které jsou používány vlastnostmi **FolderPath** a **filename** .

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Funkce Data Factory
Funkce v datové továrně můžete použít spolu se systémovými proměnnými pro následující účely:

1. Zadání dotazů na výběr dat (viz články konektoru, na které odkazují [aktivity přesunu dat](data-factory-data-movement-activities.md) .
   
   Syntaxe vyvolání funkce Data Factory je: **$$\<function>** pro dotazy na výběr dat a další vlastnosti v aktivitě a datových sadách.  
2. Určení vstupních závislostí s funkcemi objektu pro vytváření dat v kolekci vstupů aktivit.
   
    $ $ není potřeba pro zadání výrazů vstupních závislostí.     

V následující ukázce je vlastnost **sqlReaderQuery** v souboru JSON přiřazena k hodnotě vrácené `Text.Format` funkcí. Tato ukázka také používá systémovou proměnnou s názvem **WindowStart**, která představuje čas spuštění okna spuštění aktivity.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Viz téma [vlastní formátovací řetězce pro datum a čas](/dotnet/standard/base-types/custom-date-and-time-format-strings) , které popisují různé možnosti formátování, které můžete použít (například: Ay vs. rrrr). 

### <a name="functions"></a>Functions
V následujících tabulkách jsou uvedeny všechny funkce v Azure Data Factory:

| Kategorie | Funkce | Parametry | Popis |
| --- | --- | --- | --- |
| Čas |AddHours (X, Y) |X: datum a čas <br/><br/>Y: int |Přidá Y hodiny do daného času X. <br/><br/>Příklad: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Čas |AddMinutes (X, Y) |X: datum a čas <br/><br/>Y: int |Přidá Y minuty do X.<br/><br/>Příklad: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Čas |StartOfHour (X) |X: datum a čas |Získá počáteční čas hodiny reprezentované hodinovou komponentou X. <br/><br/>Příklad: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date (Datum) |AddDays (X, Y) |X: datum a čas<br/><br/>Y: int |Přidá Y dní do X. <br/><br/>Příklad: 9/15/2013 12:00:00 PM + 2 dny = 9/17/2013 12:00:00 odp.<br/><br/>Můžete také odečíst dny zadáním Y jako záporného čísla.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date (Datum) |AddMonths (X, Y) |X: datum a čas<br/><br/>Y: int |Přidá Y měsíců do X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Zadáním hodnoty Y jako záporné číslo můžete odečíst i měsíc.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date (Datum) |AddQuarters (X, Y) |X: datum a čas <br/><br/>Y: int |Přidá Y * 3 měsíce do X.<br/><br/>Příklad: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date (Datum) |AddWeeks (X, Y) |X: datum a čas<br/><br/>Y: int |Přidá Y * 7 dní do X.<br/><br/>Příklad: 9/15/2013 12:00:00 PM + 1 týden = 9/22/2013 12:00:00 odp.<br/><br/>Zadáním hodnoty Y jako záporné číslo můžete odečíst i týdny.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date (Datum) |AddYears (X, Y) |X: datum a čas<br/><br/>Y: int |Přidá Y roky do X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Můžete také odečíst roky zadáním Y jako záporného čísla.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date (Datum) |Den (X) |X: datum a čas |Načte komponentu dne X.<br/><br/>Příklad: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date (Datum) |DayOfWeek (X) |X: datum a čas |Načte komponentu dne v týdnu X.<br/><br/>Příklad: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date (Datum) |DayOfYear (X) |X: datum a čas |Vrátí den v roce reprezentovaný komponentou year v roce X.<br/><br/>Příklady:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date (Datum) |DaysInMonth (X) |X: datum a čas |Vrátí dny v měsíci reprezentované komponentou month parametru X.<br/><br/>Příklad: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date (Datum) |EndOfDay (X) |X: datum a čas |Získá datum a čas, který představuje konec dne (den komponenty) X.<br/><br/>Příklad: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date (Datum) |EndOfMonth (X) |X: datum a čas |Vrátí konec měsíce reprezentovaného součástí měsíce parametru X. <br/><br/>Příklad: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (datum a čas, který představuje konec dne v měsíci) |
| Date (Datum) |StartOfDay (X) |X: datum a čas |Získá začátek dne reprezentovaného komponentou Day parametru X.<br/><br/>Příklad: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |Od (X) |X: řetězec |Analyzuje řetězec X do data a času. |
| DateTime |Osové značky (X) |X: datum a čas |Získá vlastnost Ticks parametru X. Jedna značka se rovná 100 nanosekund. Hodnota této vlastnosti představuje počet taktů, jejichž platnost uplynula 12:00:00 od 1. ledna 0001. |
| Text |Formát (X) |X: řetězcová proměnná |Zformátuje text (použijte `\\'` kombinaci pro řídicí `'` znak).|

> [!IMPORTANT]
> Při použití funkce v jiné funkci není nutné **$$** pro vnitřní funkci použít předponu. Například: $ $Text. Format (' PartitionKey EQ \\ ' my_pkey_filter_value \\ ' a RowKey GE \\ ' {0: RRRR-MM-DD hh: mm: ss} \\ ' ', Time. AddHours (vlastnosti slicestart,-6)). V tomto příkladu si všimněte, že **$$** se pro funkci **time. AddHours** nepoužívá předpona. 

#### <a name="example"></a>Příklad
V následujícím příkladu jsou vstupní a výstupní parametry aktivity podregistru určeny pomocí `Text.Format` systémové proměnné Function a vlastnosti slicestart. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Příklad 2

V následujícím příkladu je parametr DateTime pro aktivitu uložené procedury určen pomocí textu. Funkce Format a proměnná vlastnosti slicestart 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Příklad 3
Chcete-li číst data z předchozího dne reprezentovaného vlastnosti slicestart, použijte funkci AddDays, jak je znázorněno v následujícím příkladu: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Viz téma [vlastní formátovací řetězce pro datum a čas](/dotnet/standard/base-types/custom-date-and-time-format-strings) , které popisují různé možnosti formátování, které můžete použít (například: RR vs. rrrr).