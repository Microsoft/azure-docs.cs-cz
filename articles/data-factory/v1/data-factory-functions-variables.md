---
title: Data Factory funkce a systémové proměnné | Dokumentace Microsoftu
description: Obsahuje seznam funkcí služby Azure Data Factory a systémové proměnné
documentationcenter: ''
author: sharonlo101
manager: craigg
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 48a0bf2a7209812af23c3dd9eec9703ec5826fa9
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019567"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory – funkce a systémové proměnné
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [systémových proměnných ve službě Data Factory](../control-flow-system-variables.md).

Tento článek obsahuje informace o funkcích a proměnné, které podporované službou Azure Data Factory.

## <a name="data-factory-system-variables"></a>Data Factory systémové proměnné
| Název proměnné | Popis | Objekt oboru | Rozsah JSON a případy použití |
| --- | --- | --- | --- |
| WindowStart |Začátek časový interval pro spuštění okno aktuální aktivity |aktivita |<ol><li>Zadejte dotazech pro výběr data. Najdete v článcích konektor odkazuje [aktivity přesunu dat](data-factory-data-movement-activities.md) článku.</li> |
| WindowEnd |Konec časový interval pro spuštění okno aktuální aktivity |aktivita |stejné jako WindowStart. |
| Vlastnosti SliceStart |Začátek časového intervalu pro datový řez se vytváří |aktivita<br/>Datové sady |<ol><li>Zadejte cesty ke složkám dynamické a názvy souborů při práci s [objektů Blob v Azure](data-factory-azure-blob-connector.md) a [systému souborů datových sad](data-factory-onprem-file-system-connector.md).</li><li>Zadejte vstupní závislosti s funkce data factory v kolekci vstupů aktivity.</li></ol> |
| SliceEnd |Konec časového intervalu pro aktuální datový řez. |aktivita<br/>Datové sady |stejné jako vlastnosti SliceStart. |

> [!NOTE]
> Objekt pro vytváření dat aktuálně vyžaduje, že plán určeného v aktivitě přesně odpovídá plán zadaná ve skupině dostupnosti výstupní datové sady. Proto WindowStart, WindowEnd a SliceStart a SliceEnd vždy mapují na stejné časové období a jednu výstupní řez.
> 

### <a name="example-for-using-a-system-variable"></a>Příklad použití systémová proměnná
V následujícím příkladu, rok, měsíc, den a čas **SliceStart** jsou extrahovány do samostatných proměnných, které jsou používány **folderPath** a **fileName** vlastnosti.

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

## <a name="data-factory-functions"></a>Funkce data Factory
Funkce ve službě data factory spolu s systémové proměnné můžete použít k těmto účelům:

1. Zadávání dotazů na výběr data (najdete v článcích konektor odkazuje [aktivity přesunu dat](data-factory-data-movement-activities.md) článku.
   
   Syntaxe pro vyvolání funkce data factory: **$$ <function>** dotazech pro výběr data a další vlastnosti v aktivity a datové sady.  
2. Určení vstupní závislosti pomocí funkce data factory v kolekci vstupů aktivity.
   
    pro zadání výrazy vstupní závislosti není potřeba $$.     

V následující ukázce **sqlReaderQuery** vlastnost v souboru JSON je přiřazena hodnota vrácená `Text.Format` funkce. Tato ukázka také používá systémovou proměnnou s názvem **WindowStart**, která představuje čas zahájení okna spuštění aktivit.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

V tématu [vlastní data a řetězce formátu časových](https://msdn.microsoft.com/library/8kb3ddd4.aspx) téma, které popisují různé možnosti formátování můžete použít (například: zobrazit vs. rrrr). 

### <a name="functions"></a>Functions
V následujících tabulkách jsou uvedeny všechny funkce v Azure Data Factory:

| Kategorie | Funkce | Parametry | Popis |
| --- | --- | --- | --- |
| Čas |AddHours(X,Y) |X: DateTime <br/><br/>Y: int |Přidá do okamžiku X Y hodin. <br/><br/>Příklad: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Čas |AddMinutes(X,Y) |X: DateTime <br/><br/>Y: int |Přidá Y minut X.<br/><br/>Příklad: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Čas |StartOfHour(X) |X: Datum a čas |Získá počáteční čas pro hodinu představovanou komponentu hodin hodnoty X. <br/><br/>Příklad: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Datum |AddDays(X,Y) |X: DateTime<br/><br/>Y: int |Přidá Y dní X. <br/><br/>Příklad: 9/15/2013 12:00:00 PM + 2 dnů = 9/17/2013 12:00:00 PM.<br/><br/>Lze odečíst dny příliš zadáním Y jako záporné číslo.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Datum |AddMonths(X,Y) |X: DateTime<br/><br/>Y: int |Přidá Y měsíců X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Je příliš odečíst měsíců zadáním Y jako záporné číslo.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Datum |AddQuarters(X,Y) |X: DateTime <br/><br/>Y: int |Přidá Y * X 3 měsíců.<br/><br/>Příklad: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Datum |AddWeeks(X,Y) |X: DateTime<br/><br/>Y: int |Přidá Y * X 7 dnů<br/><br/>Příklad: 9/15/2013 12:00:00 PM + 1 týden = 9/22/2013 12:00:00 PM<br/><br/>Týdny lze odečíst příliš zadáním Y jako záporné číslo.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Datum |AddYears(X,Y) |X: DateTime<br/><br/>Y: int |Přidá Y let X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Je příliš odečíst let zadáním Y jako záporné číslo.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Datum |Day(X) |X: DateTime |Získá komponentu dne z hodnoty X.<br/><br/>Příklad: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Datum |DayOfWeek(X) |X: DateTime |Načte den v týdnu. x.<br/><br/>Příklad: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Datum |DayOfYear(X) |X: DateTime |Načte den v roce reprezentována složku roku X.<br/><br/>Příklady:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Datum |DaysInMonth(X) |X: DateTime |Získá dní v měsíci reprezentována komponentu měsíc z parametru X.<br/><br/>Příklad: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Datum |EndOfDay(X) |X: DateTime |Získá datum a čas, který představuje konec dne (součást den) x.<br/><br/>Příklad: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Datum |EndOfMonth(X) |X: DateTime |Získá Konec měsíce reprezentována složku měsíce parametru X. <br/><br/>Příklad: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (datum a čas, který představuje konec dne měsíce) |
| Datum |StartOfDay(X) |X: DateTime |Získá začátek dne reprezentována komponentu dne z hodnoty parametru X.<br/><br/>Příklad: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |From(X) |X: Řetězec |Analyzovat řetězec X na datum čas. |
| DateTime |Ticks(X) |X: DateTime |Získá dílků vlastnost parametru X. Jedna značka se rovná 100 nanosekund. Hodnota této vlastnosti představuje počet taktů, které uplynuly od 12:00:00, 1. ledna 0001. |
| Text |Format(X) |X: Řetězcová hodnota |Formátuje text (použijte `\\'` kombinaci řídicí `'` znak).|

> [!IMPORTANT]
> Pokud používáte funkci v jiné funkci, není nutné používat **$$** předpona pro vnitřní funkci. Například: $$Text.Format ("PartitionKey eq \\" my_pkey_filter_value\\"a RowKey ge \\' {0: yyyy-MM-dd hh: mm:}\\", Time.AddHours (SliceStart, -6)). V tomto příkladu, Všimněte si, že **$$** není použita předpona pro **Time.AddHours** funkce. 

#### <a name="example"></a>Příklad:
V následujícím příkladu se určují vstupní a výstupní parametry pro aktivitu Hive pomocí `Text.Format` funkce a systémové proměnné vlastnosti SliceStart. 

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

V následujícím příkladu je určen parametrem data a času pro aktivity uložené procedury pomocí textu. Formát funkce a proměnné vlastnosti SliceStart. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample",
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
Přečíst data z předchozího dne místo reprezentována z vlastnosti SliceStart, pomocí funkce přidat dny, jak je znázorněno v následujícím příkladu: 

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

V tématu [vlastní data a řetězce formátu časových](https://msdn.microsoft.com/library/8kb3ddd4.aspx) téma, které popisují různé možnosti formátování můžete použít (například: RR vs. rrrr). 

