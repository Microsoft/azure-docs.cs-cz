---
title: Funkce datové továrny a systémové proměnné
description: Obsahuje seznam funkcí azure data factory a systémových proměnných.
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9acc369e24d1bac92dea3fb6ae391a410e5f6c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73667655"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory – funkce a systémové proměnné
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Systémové proměnné v datové továrně](../control-flow-system-variables.md).

Tento článek obsahuje informace o funkcích a proměnných podporovaných službou Azure Data Factory.

## <a name="data-factory-system-variables"></a>Systémové proměnné datové továrny

| Název proměnné | Popis | Obor objektu | Rozsah json a případy použití |
| --- | --- | --- | --- |
| Spuštění okna |Začátek časového intervalu pro aktuální okno spuštění aktivity |aktivita |<ol><li>Zadejte dotazy na výběr dat. Viz články konektoru odkazované v článku [Aktivity přesunu dat.](data-factory-data-movement-activities.md)</li> |
| Konec okna |Konec časového intervalu pro aktuální okno spuštění aktivity |aktivita |stejné jako WindowStart. |
| ŘezStart |Začátek časového intervalu pro vytvářený řez dat |aktivita<br/>Dataset |<ol><li>Při práci s datovými sadami [Objektů blob](data-factory-azure-blob-connector.md) a [souborového systému](data-factory-onprem-file-system-connector.md)Azure zadejte cesty dynamických složek a názvy souborů .</li><li>Zadejte vstupní závislosti s funkcemi factory dat v kolekci vstupů aktivity.</li></ol> |
| Konec řezu |Konec časového intervalu pro aktuální řez dat. |aktivita<br/>Dataset |stejné jako SliceStart. |

> [!NOTE]
> V současné době data factory vyžaduje, aby plán zadaný v aktivitě přesně odpovídá plánu zadanému v dostupnosti výstupní datové sady. Proto WindowStart, WindowEnd a SliceStart a SliceEnd vždy mapovat na stejné časové období a jeden výstupní řez.
> 

### <a name="example-for-using-a-system-variable"></a>Příklad použití systémové proměnné
V následujícím příkladu jsou rok, měsíc, den a čas **SliceStart** extrahovány do samostatných proměnných, které jsou používány vlastnostmi **folderPath** a **fileName.**

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
Můžete použít funkce v datové továrně spolu se systémovými proměnnými pro následující účely:

1. Určení dotazů na výběr dat (viz články spojnic, na které odkazuje článek [Aktivity přesunu dat.](data-factory-data-movement-activities.md)
   
   Syntaxe pro vyvolání funkce ** $$ \<** datové továrny je: funkce>pro dotazy výběru dat a další vlastnosti v aktivitě a datových sadách.  
2. Určení vstupních závislostí s funkcemi factory dat v kolekci vstupů aktivit.
   
    $$ není potřeba pro určení vstupních výrazů závislostí.     

V následující ukázce je vlastnost **sqlReaderQuery** v souboru JSON přiřazena hodnotě vrácené `Text.Format` funkcí. Tato ukázka také používá systémovou proměnnou s názvem **WindowStart**, která představuje čas zahájení okna spuštění aktivity.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Viz [Téma Vlastní řetězce kalendářních dat a časových řetězců,](https://msdn.microsoft.com/library/8kb3ddd4.aspx) které popisuje různé možnosti formátování, které můžete použít (například: ay vs. yyyy). 

### <a name="functions"></a>Funkce
V následujících tabulkách jsou uvedeny všechny funkce v Azure Data Factory:

| Kategorie | Funkce | Parametry | Popis |
| --- | --- | --- | --- |
| Time |AddHours (X,Y) |X: DateTime <br/><br/>Y: int |Přidá hodiny Y k danému času X. <br/><br/>Příklad: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Time |AddMinutes (X,Y) |X: DateTime <br/><br/>Y: int |Přidá minuty Y do X.<br/><br/>Příklad: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Time |ZačátekHodiny(X) |X: Datum |Získá počáteční čas pro hodinu reprezentován hodinovou komponentou X. <br/><br/>Příklad: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Datum |PřidejteDny (X,Y) |X: DateTime<br/><br/>Y: int |Přidá dny Y do X. <br/><br/>Příklad: 15.9.2013 12:00:00 PM + 2 dny = 9/17/2013 12:00:00 PM.<br/><br/>Dny můžete odečíst také zadáním čísla Y jako záporného čísla.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Datum |AddMonths (X,Y) |X: DateTime<br/><br/>Y: int |Přidá y měsíce x.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Měsíce můžete odečíst také zadáním čísla Y jako záporného čísla.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Datum |AddQuarters (X,Y) |X: DateTime <br/><br/>Y: int |Přidá Y * 3 měsíce do X.<br/><br/>Příklad: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Datum |Přidejtetýdny (X,Y) |X: DateTime<br/><br/>Y: int |Přidá Y * 7 dní do X<br/><br/>Příklad: 9/15/2013 12:00:00 PM + 1 týden = 9/22/2013 12:00:00 PM<br/><br/>Týdny můžete odečíst také zadáním čísla Y jako záporného čísla.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Datum |AddYears (X,Y) |X: DateTime<br/><br/>Y: int |Přidá Y let X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Roky můžete odečíst také zadáním y jako záporného čísla.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Datum |Den (X) |X: DateTime |Získá denní součást X.<br/><br/>Příklad: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Datum |DayOfWeek(X) |X: DateTime |Získá den v týdnu součást X.<br/><br/>Příklad: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Datum |DayOfYear(X) |X: DateTime |Získá den v roce reprezentované roční součást X.<br/><br/>Příklady:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Datum |DaysInMonth(X) |X: DateTime |Získá dny v měsíci reprezentované měsíční součástí parametru X.<br/><br/>Příklad: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Datum |EndofDay(X) |X: DateTime |Získá datum čas, který představuje konec dne (day component) X.<br/><br/>Příklad: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Datum |Konec měsíce(X) |X: DateTime |Získá konec měsíce reprezentované měsíc součást parametru X. <br/><br/>Příklad: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (datum čas, který představuje konec měsíce září) |
| Datum |Začátekdne(X) |X: DateTime |Získá začátek dne reprezentované day komponenty parametru X.<br/><br/>Příklad: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |Od(X) |X: Řetězec |Analyzovat řetězec X na datum času. |
| DateTime |Značky(X) |X: DateTime |Získá značky vlastnost parametru X. Jedno zaškrtnutí se rovná 100 nanosekundám. Hodnota této vlastnosti představuje počet značek, které uplynuly od půlnoci 12:00:00, 1. |
| Text |Formát(X) |X: String proměnná |Zformátuje text `\\'` (pomocí `'` kombinace k úniku znaků).|

> [!IMPORTANT]
> Při použití funkce v rámci jiné funkce **$$** není nutné používat předponu pro vnitřní funkci. Příklad: \\$$Text.Format('PartitionKey eq\\'my_pkey_filter_value ' \\a RowKey ge '{0: yyyy-MM-dd\\HH:mm:ss} '', Time.AddHours(SliceStart, -6)). V tomto příkladu **$$** všimněte si, že předpona se nepoužívá pro **Time.AddHours** funkce. 

#### <a name="example"></a>Příklad
V následujícím příkladu jsou vstupní a výstupní parametry pro `Text.Format` aktivitu Hive určeny pomocí funkce a systémové proměnné SliceStart. 

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

V následujícím příkladu datetime parametr pro aktivitu uložené procedury je určena pomocí Text. Funkce Formát a proměnná SliceStart. 

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
Chcete-li číst data z předchozího dne namísto dne reprezentovaného řezem Start, použijte funkci AddDays, jak je znázorněno v následujícím příkladu: 

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

Viz [Téma Vlastní řetězce kalendářních dat a časových řetězců,](https://msdn.microsoft.com/library/8kb3ddd4.aspx) které popisuje různé možnosti formátování, které můžete použít (například yy vs. yyyy). 

