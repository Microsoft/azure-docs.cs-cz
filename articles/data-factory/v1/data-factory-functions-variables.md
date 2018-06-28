---
title: Funkce pro vytváření dat a systémové proměnné | Microsoft Docs
description: Obsahuje seznam funkcí Azure Data Factory a systémové proměnné
documentationcenter: ''
author: sharonlo101
manager: craigg
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b4d9a684c2c21ed9ec00b04963432f9ebcff7493
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048450"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory – funkce a systémové proměnné
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [systémové proměnné v datové továrně](../control-flow-system-variables.md).

Tento článek obsahuje informace o funkcích a proměnné, podporované službou Azure Data Factory.

## <a name="data-factory-system-variables"></a>Data Factory systémové proměnné
| Název proměnné | Popis | Objekt oboru | Obor JSON a případy použití |
| --- | --- | --- | --- |
| WindowStart |Začátek časového intervalu pro aktuální aktivity při spuštění okna |aktivita |<ol><li>Zadejte data výběr dotazy. Najdete v části konektor články v odkazuje [aktivity přesunu dat](data-factory-data-movement-activities.md) článku.</li> |
| WindowEnd |Konec časového intervalu pro aktuální aktivity při spuštění okna |aktivita |stejné jako WindowStart. |
| SliceStart |Začátek časového intervalu pro datový řez vytvářen |aktivita<br/>Datové sady |<ol><li>Zadejte složku dynamické cesty a názvy souborů při práci s [objektů Blob v Azure](data-factory-azure-blob-connector.md) a [datové sady systém souborů](data-factory-onprem-file-system-connector.md).</li><li>Určení závislostí vstupní pomocí funkce objekt pro vytváření dat v kolekci vstupy aktivity.</li></ol> |
| SliceEnd |Konec časového intervalu pro aktuální datový řez. |aktivita<br/>Datové sady |stejné jako SliceStart. |

> [!NOTE]
> Objekt pro vytváření dat aktuálně vyžaduje, aby plán určeného v aktivitě přesně odpovídá plán zadaný v dostupnosti výstupní datovou sadu. Proto WindowStart, WindowEnd a SliceStart a SliceEnd vždy mapují na stejné časové období a jednu výstupní řez.
> 

### <a name="example-for-using-a-system-variable"></a>Příklad použití systémové proměnné
V následujícím příkladu, rok, měsíc, den a čas **SliceStart** extrahují do samostatné proměnné, které jsou používány **folderPath** a **fileName** vlastnosti.

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

## <a name="data-factory-functions"></a>Funkce pro vytváření dat
Můžete použít funkce v datové továrně společně s systémové proměnné pro následující účely:

1. Určení dotazy výběru dat (najdete v článcích konektor odkazuje [aktivity přesunu dat](data-factory-data-movement-activities.md) článku.
   
   Tady je syntax k vyvolání funkce objektu pro vytváření dat: **$$ <function>** pro dotazy výběru dat a další vlastnosti aktivity a datové sady.  
2. Určení vstupní závislosti pomocí funkce objekt pro vytváření dat v kolekci vstupy aktivity.
   
    $$ není potřeba pro zadání vstupní závislost výrazy.     

V následující ukázce **sqlReaderQuery** vlastnost v souboru JSON je přiřazena k hodnoty vrácené `Text.Format` funkce. Tato ukázka také používá systém proměnné s názvem **WindowStart**, která reprezentuje čas zahájení okna aktivity při spuštění.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

V tématu [vlastní řetězců data a času formát](https://msdn.microsoft.com/library/8kb3ddd4.aspx) téma, které popisuje různé možnosti formátování můžete použít (Příklad: zobrazit oproti rrrr). 

### <a name="functions"></a>Functions
V následujících tabulkách najdete všechny funkce v Azure Data Factory:

| Kategorie | Funkce | Parametry | Popis |
| --- | --- | --- | --- |
| Čas |AddHours(X,Y) |X: data a času <br/><br/>/ Y: int |Přidá do okamžiku X Y hodin. <br/><br/>Příklad: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Čas |AddMinutes(X,Y) |X: data a času <br/><br/>/ Y: int |Přidá do X minut Y.<br/><br/>Příklad: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Čas |StartOfHour(X) |X: data a času |Získá počáteční čas za hodinu reprezentována komponentu hodin hodnoty X. <br/><br/>Příklad: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Datum |AddDays(X,Y) |X: data a času<br/><br/>/ Y: int |Přidá Y dní X. <br/><br/>Příklad: 9/15/2013 12:00:00 PM + 2 dny = 9/17/2013 12:00:00 PM.<br/><br/>Počet dnů můžete odečtena příliš zadáním Y jako záporné číslo.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Datum |AddMonths(X,Y) |X: data a času<br/><br/>/ Y: int |Přidá Y měsíců X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Je příliš odečíst měsíců zadáním Y jako záporné číslo.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Datum |AddQuarters(X,Y) |X: data a času <br/><br/>/ Y: int |Přidá Y * 3 měsíců X.<br/><br/>Příklad: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Datum |AddWeeks(X,Y) |X: data a času<br/><br/>/ Y: int |Přidá Y * 7 dnů, X<br/><br/>Příklad: 9/15/2013 12:00:00 PM + 1 týden = 9/22/2013 12:00:00 PM<br/><br/>Týdny lze odečíst příliš zadáním Y jako záporné číslo.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Datum |AddYears(X,Y) |X: data a času<br/><br/>/ Y: int |Přidá Y let X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Je příliš odečíst let zadáním Y jako záporné číslo.<br/><br/>Příklad: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Datum |Day(X) |X: data a času |Získá komponentu den hodnoty X.<br/><br/>Příklad: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Datum |DayOfWeek(X) |X: data a času |Získá den týdne součást X.<br/><br/>Příklad: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Datum |DayOfYear(X) |X: data a času |Získá den v roce reprezentována komponentu roku X.<br/><br/>Příklady:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Datum |DaysInMonth(X) |X: data a času |Získá dní v měsíci reprezentována komponentu měsíce parametr X.<br/><br/>Příklad: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Datum |EndOfDay(X) |X: data a času |Získá datum a čas, který představuje konec dne (komponenta dne) X.<br/><br/>Příklad: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Datum |EndOfMonth(X) |X: data a času |Získá Konec měsíce reprezentována měsíc součást parametr X. <br/><br/>Příklad: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (datum a čas, který představuje konec dne měsíce) |
| Datum |StartOfDay(X) |X: data a času |Získá začátek dne reprezentována komponentu den parametru X.<br/><br/>Příklad: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |From(X) |X: řetězec |Analyzovat řetězec X datum a čas. |
| DateTime |Ticks(X) |X: data a času |Získá o rysky vlastnost parametru X. Jeden značek rovná 100 nanosekundách. Hodnota této vlastnosti představuje počet značek, které uplynuly od 12:00:00, 1. ledna 0001. |
| Text |Format(X) |X: proměnnou string |Formáty text (použijte `\\'` kombinaci, abyste se vyhnuli `'` znaků).|

> [!IMPORTANT]
> Při práci s funkcí v jiné funkci, není potřeba použít **$$** předponu pro vnitřní funkce. Například: $$Text.Format ('PartitionKey eq \\' my_pkey_filter_value\\' a RowKey ge \\' {0: rrrr MM-dd hh: mm:}\\", Time.AddHours (SliceStart, -6)). V tomto příkladu, Všimněte si, že **$$** předponu nepoužívá pro **Time.AddHours** funkce. 

#### <a name="example"></a>Příklad:
V následujícím příkladu jsou vstupní a výstupní parametry pro aktivitu Hive určit pomocí `Text.Format` funkce a proměnné SliceStart systému. 

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

V následujícím příkladu je určen parametr data a času pro aktivity uložené procedury pomocí textu. Formát funkce a proměnné SliceStart. 

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
Číst data z předchozí den místo den reprezentována vlastnosti SliceStart, pomocí funkce přidat dny, jak je znázorněno v následujícím příkladu: 

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

V tématu [vlastní řetězců data a času formát](https://msdn.microsoft.com/library/8kb3ddd4.aspx) téma, které popisuje různé možnosti formátování můžete použít (Příklad: RR oproti rrrr). 

