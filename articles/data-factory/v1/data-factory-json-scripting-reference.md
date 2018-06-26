---
title: Azure Data Factory - referenčních informacích o skriptování JSON | Microsoft Docs
description: Poskytuje schémata JSON entit služby Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: d5d085e24efb7008e8275ec51afe00ba18ca9ba0
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753553"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory - referenčních informacích o skriptování JSON
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA).


Tento článek obsahuje schémata JSON a příklady pro definování entity Azure Data Factory (kanál, aktivity, datové sady a propojené služby).  

## <a name="pipeline"></a>Kanál 
Základní strukturu pro definici kanálu je následující: 

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
} 
```

Následující tabulka popisuje vlastnosti v rámci kanálu definici JSON:

| Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
| jméno | Název kanálu. Zadejte název, který představuje akci, aktivity nebo kanálu je nakonfigurovaný<br/><ul><li>Maximální počet znaků: 260.</li><li>Musí začínat písmenem, číslicí nebo podtržítkem (_).</li><li>Nejsou povolené tyto znaky: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Ano |
| description |Text popisující, co aktivity nebo kanálu se používá pro | Ne |
| activities | Obsahuje seznam aktivit. | Ano |
| start |Počáteční datum a čas pro kanál. Musí být v [formátu ISO](http://en.wikipedia.org/wiki/ISO_8601). Příklad: 2014-10-14T16:32:41. <br/><br/>Je možné zadat místní čas, například Odhadovaný čas. Tady je příklad: `2016-02-27T06:00:00**-05:00`, což je odhadované AM 6<br/><br/>Počáteční a koncové vlastnosti společně zadejte aktivní období kanálu. Výstup řezy jenom vytváří se v tomto aktivní období. |Ne<br/><br/>Pokud zadáte hodnotu pro vlastnost end, zadejte hodnotu pro vlastnost start.<br/><br/>Počáteční a koncový čas i lze vytvořit kanál prázdný. Musíte zadat obě hodnoty se nastavit aktivní období pro kanál ke spuštění. Pokud nezadáte počáteční a koncový čas při vytváření kanálu, můžete nastavit pomocí rutiny Set-AzureRmDataFactoryPipelineActivePeriod později. |
| konec |Koncové datum a čas pro kanál. Pokud zadaný, musí být ve formátu ISO. Příklad: 2014-10-14T17:32:41 <br/><br/>Je možné zadat místní čas, například Odhadovaný čas. Tady je příklad: `2016-02-27T06:00:00**-05:00`, což je odhadované AM 6<br/><br/>Chcete-li kanál spouštět bez omezení, zadejte jako hodnotu pro vlastnost end 9999-09-09. |Ne <br/><br/>Pokud zadáte hodnotu pro vlastnost spustit, musíte zadat hodnotu pro vlastnost end.<br/><br/>Naleznete v poznámkách k **spustit** vlastnost. |
| isPaused |Pokud je nastaven na hodnotu true kanálu nelze spustit. Výchozí hodnota = false. Tato vlastnost slouží k povolení nebo zakázání. |Ne |
| pipelineMode |Metoda pro naplánování spuštění pro kanál. Povolené hodnoty jsou: naplánované (výchozí), jednorázově.<br/><br/>"Pravidelnou" udává, že kanál spouští v zadaném časovém intervalu podle jeho aktivní období (počáteční a koncový čas). 'Jednorázově' udává, že kanál spouští jenom jednou. Po vytvoření jednorázově kanály nelze aktuálně upravit nebo aktualizovat. V tématu [Onetime kanálu](data-factory-create-pipelines.md#onetime-pipeline) podrobnosti o jednorázově nastavení. |Ne |
| expirationTime |Doba, po vytvoření, pro který kanálu je platný a by měla zůstat zřízené. Pokud nemá žádné aktivní, se nezdařilo, nebo čekající spuštění kanálu automaticky odstraněna po dosažení času vypršení platnosti. |Ne |


## <a name="activity"></a>Aktivita 
Základní strukturu pro aktivitu v rámci kanálu definice (aktivity element) je následující:

```json
{
    "name": "ActivityName",
    "description": "description", 
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    }
    "scheduler":
    {
    }
}
```

Následující tabulka popisuje vlastnosti v rámci aktivity definici JSON:

| Značka | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název aktivity. Zadejte název, který představuje akci nakonfigurovaný tak, aby se aktivity<br/><ul><li>Maximální počet znaků: 260.</li><li>Musí začínat písmenem, číslicí nebo podtržítkem (_).</li><li>Nejsou povolené tyto znaky: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Ano |
| description |Text popisující, co se používá aktivitu pro. |Ne |
| type |Určuje typ aktivity. Najdete v článku [ÚLOŽIŠŤ dat](#data-stores) a [aktivit TRANSFORMACE dat](#data-transformation-activities) oddíly pro různé typy aktivit. |Ano |
| vstupy |Vstupní tabulky použité aktivitou<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Ne pro HDInsightStreaming a SqlServerStoredProcedure aktivity <br/> <br/> Ano pro všechny ostatní |
| výstupy |Výstupní tabulky použité aktivitou.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Ano |
| linkedServiceName |Název propojené služby používané aktivitou. <br/><br/>Aktivita může vyžadovat zadání propojené služby, která odkazuje na požadované výpočetní prostředí. |Ano pro aktivity HDInsight, Azure Machine Learning aktivity a aktivity uložené procedury. <br/><br/>Ne ve všech ostatních případech |
| typeProperties |Vlastnosti v rámci typeProperties části závisí na typu aktivity. |Ne |
| policy |Zásady, které ovlivňují chování aktivity za běhu. Pokud není zadaný, použijí se výchozí zásady. |Ne |
| Scheduler |Vlastnost "scheduler" se používá k definování požadované plánování pro aktivitu. Jeho podvlastnosti jsou stejné jako ty, které jsou v [vlastnost availability v datové sadě](data-factory-create-datasets.md#dataset-availability). |Ne |

### <a name="policies"></a>Zásady
Zásady ovlivňují chování běhu aktivity, konkrétně při zpracování řezu tabulky. Následující tabulka obsahuje podrobnosti.

| Vlastnost | Povolené hodnoty | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| souběžnosti |Integer <br/><br/>Maximální hodnota: 10 |1 |Počet souběžných spuštění aktivity.<br/><br/>Určuje počet spuštění paralelní aktivity, které se může stát při jiné řezy. Například pokud aktivitu musí projít, velké sady dostupných dat, mají větší hodnotu souběžnosti urychluje zpracování dat. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Určuje pořadí datové řezy, které jsou zpracovávány.<br/><br/>Pokud máte 2 řezy (jeden situaci ve 4 a další v 17: 00) a jsou obě čekající na zpracování. Pokud jste nastavili executionPriorityOrder být NewestFirst, je nejprve zpracování řezu v 17: 00. Podobně pokud nastavíte executionPriorityORder být OldestFIrst, pak ve 4 zpracování řezu se. |
| retry |Integer<br/><br/>Maximální hodnota může být 10 |0 |Počet opakování, než se zpracování dat pro řez je označen jako selhání. Provedení aktivity pro datový řez je opakovat až zadaný počet. Opakovaném provádí co nejdříve po selhání. |
| timeout |Časový interval |00:00:00 |Časový limit aktivity. Příklad: 00:10:00 (znamená časový limit 10 minut)<br/><br/>Pokud hodnota není zadána nebo je 0, časový limit je nekonečno.<br/><br/>Pokud bude čas zpracování dat na řez překročí hodnota časového limitu, se zruší a systém se pokusí opakujte zpracování. Počet pokusů, závisí na vlastnost opakování. Když dojde k vypršení časového limitu, je stav nastaven na TimedOut. |
| zpoždění |Časový interval |00:00:00 |Zadejte zpoždění před zpracování dat řezu spustí.<br/><br/>Provádění aktivity pro datový řez se spustí po zpoždění očekávaný čas spuštění.<br/><br/>Příklad: 00:10:00 (znamená zpoždění 10 minut) |
| opakování po delší době |Integer<br/><br/>Maximální hodnota: 10 |1 |Počet dlouho opakování pokusů, než řez spuštění se nezdařilo.<br/><br/>pokusy o opakování po delší době jsou rozmístěny ve longRetryInterval. Takže pokud je třeba zadat čas mezi pokusy o opakování, použijte opakování po delší době. Pokud jsou zadané opakování a opakování po delší době, jednotlivé pokusy o opakování po delší době zahrnuje opakovaných pokusů a je maximální počet pokusů o opakování * opakování po delší době.<br/><br/>Například, pokud bychom měli následující nastavení v zásadách aktivit:<br/>Opakujte: 3<br/>opakování po delší době: 2<br/>longRetryInterval: 01:00:00<br/><br/>Předpokládá se jenom jeden řez provést (stav Čeká) a provedení aktivity pokaždé, když dojde k chybě. Nejdřív by 3 provádění po sobě jdoucích pokusů. Po každém pokusu o stav řezu bude opakovat. Po první 3 pokusy jsou přes, bude stav řezu opakování po delší době.<br/><br/>Po hodině (který je na longRetryInteval hodnota) bude další sadu 3 provádění po sobě jdoucích pokusů. Poté stav řezu by se nezdařilo a by se pokus o žádné další opakování. Proto celkové 6 pokusy byly provedeny.<br/><br/>Pokud žádné spuštění úspěšné, stav řezu by mít připravené a jsou pokus o žádné další opakování.<br/><br/>opakování po delší době je možné použít situace, kdy závislé data dorazí na Nedeterministický časy nebo je v nestabilním stavu v rámci které zpracování dat dojde celém prostředí. V takových případech to, které opakování, jedna po druhé nemusí být úspěšná a díky tomu v intervalech čas má za následek požadované výstup.<br/><br/>Word varování: nenastavujte vysoké hodnoty pro opakování po delší době nebo longRetryInterval. Vyšší hodnoty obvykle implikují dalších systémových otázek. |
| longRetryInterval |Časový interval |00:00:00 |Prodleva mezi pokusy o opakování dlouho |

### <a name="typeproperties-section"></a>části v rámci typeProperties
V rámci typeProperties části se liší pro každou aktivitu. Transformace aktivity mají vlastnosti typu. V tématu [aktivit TRANSFORMACE dat](#data-transformation-activities) v tomto článku pro ukázky JSON, které definují aktivit transformace v datovém kanálu. 

**Aktivita kopírování** má dvě témata v rámci typeProperties oddílu: **zdroj** a **podřízený**. V tématu [ÚLOŽIŠŤ dat](#data-stores) části v tomto článku pro JSON vzorků, které ukazují, jak použít data jako zdroj a jímka úložiště. 

### <a name="sample-copy-pipeline"></a>Ukázkový kanál kopírování
V následujícím ukázkovém kanálu je v části **activities** jedna aktivita typu **Kopírování**. V této ukázce [aktivity kopírování](data-factory-data-movement-activities.md) kopíruje data z Azure Blob storage do Azure SQL database. 

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
} 
```

Je třeba počítat s následujícím:

* V části aktivit je jenom jedna aktivita, jejíž vlastnost **type** je nastavená na **Copy**.
* Vstup aktivity je nastavený na **InputDataset** a výstup aktivity je nastavený na **OutputDataset**.
* V části **typeProperties** je jako typ zdroje určen **BlobSource** a jako typ jímky **SqlSink**.

V tématu [ÚLOŽIŠŤ dat](#data-stores) části v tomto článku pro JSON vzorků, které ukazují, jak použít data jako zdroj a jímka úložiště.    

Kompletní a podrobný postup vytváření tohoto kanálu, najdete v části [kurz: kopírování dat z úložiště objektů Blob do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

### <a name="sample-transformation-pipeline"></a>Ukázkový kanál transformace
V následujícím ukázkovém kanálu je v části **activities** jedna aktivita typu **HDInsightHive**. V této ukázce [aktivita HDInsight Hive](data-factory-hive-activity.md) transformuje data ze služby Azure Blob Storage tak, že v clusteru Azure HDInsight Hadoop spustí soubor skriptu Hive. 

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Je třeba počítat s následujícím: 

* V části activities je jenom jedna aktivita, jejíž vlastnost **type** má hodnotu **HDInsightHive**.
* Soubor skriptu Hive **partitionweblogs.hql** je uložený v účtu služby Azure Storage (který určuje služba scriptLinkedService s názvem **AzureStorageLinkedService**) a ve složce **script** v kontejneru **adfgetstarted**.
* **Definuje** části slouží k určení nastavení běhového prostředí, které se předávají skriptu hive jako konfigurační hodnoty Hive (např `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

V tématu [aktivit TRANSFORMACE dat](#data-transformation-activities) v tomto článku pro ukázky JSON, které definují aktivit transformace v datovém kanálu.

Kompletní a podrobný postup vytváření tohoto kanálu, najdete v části [kurz: sestavit svůj první kanál pro zpracování dat pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="linked-service"></a>Propojená služba
Základní strukturu pro definici propojené služby je následující:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

Následující tabulka popisuje vlastnosti v rámci aktivity definici JSON:

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- | 
| jméno | Název propojené služby. | Ano | 
| vlastnosti – typ | Typ propojené služby. Příklad: úložiště Azure, Azure SQL Database. |
| typeProperties | V rámci typeProperties části obsahuje prvky, které se liší u každé úložiště dat nebo výpočetní prostředí. V tématu [úložišť dat](#datastores) části pro všechna data ukládat propojené služby a [výpočetní prostředí](#compute-environments) pro všechny výpočetní propojené služby |   

## <a name="dataset"></a>Datová sada 
Datové sady v Azure Data Factory je definován následujícím způsobem:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
       "policy":
        {      
        }
    }
}
```

Následující tabulka popisuje vlastnosti v výše uvedený kód JSON:   

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| jméno | Název datové sady. V tématu [Azure Data Factory - pravidla po pojmenování](data-factory-naming-rules.md) pravidla pojmenování. |Ano |Není k dispozici |
| type | Typ datové sady. Zadejte jeden z typů podporovaných službou Azure Data Factory (například: AzureBlob, AzureSqlTable). V tématu [ÚLOŽIŠŤ dat](#data-stores) části pro všechny datové úložiště a datové sady typy podporované službou Data Factory. | 
| Struktura | Schéma datové sady. Obsahuje sloupce, jejich typy, atd. | Ne |Není k dispozici |
| typeProperties | Vlastnosti odpovídající vybranému typu. V tématu [ÚLOŽIŠŤ dat](#data-stores) části Podporované typy a jejich vlastnosti. |Ano |Není k dispozici |
| external | Logický příznak k určení, zda datové sady je explicitně produkovaný kanálu objekt pro vytváření dat nebo ne. |Ne |false (nepravda) |
| dostupnosti | Definuje okna pro zpracování nebo řezů model pro produkční datovou sadu. Podrobnosti na datovou sadu řezů modelu najdete v tématu [plánování a provádění](data-factory-scheduling-and-execution.md) článku. |Ano |Není k dispozici |
| policy |Definuje kritéria nebo podmínku, musíte splnit řezy datovou sadu. <br/><br/>Podrobnosti najdete v tématu [datovou sadu zásad](#Policy) části. |Ne |Není k dispozici |

Každý sloupec v **struktura** část obsahuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název sloupce. |Ano |
| type |Datový typ sloupce.  |Ne |
| jazyková verze |.NET na základě jazykovou verzi, která se použije, když je zadaný typ a typ formátu .NET `Datetime` nebo `Datetimeoffset`. Výchozí hodnota je `en-us`. |Ne |
| Formát |Řetězec, který se má použít, když je zadaný typ a typ formátu .NET formátu `Datetime` nebo `Datetimeoffset`. |Ne |

V následujícím příkladu, datová sada má tři sloupce `slicetimestamp`, `projectname`, a `pageviews` a jsou typu: řetězec, řetězec a desetinných v uvedeném pořadí.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Následující tabulka popisuje vlastnosti, které můžete použít v **dostupnosti** části:

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| frequency |Určuje časovou jednotku pro produkční řez datovou sadu.<br/><br/><b>Podporované frekvence</b>: minutu, hodinu, den, týden, měsíc |Ano |Není k dispozici |
| interval |Určuje multiplikátor pro četnost<br/><br/>"Frekvence x interval" Určuje, jak často se vytvářejí řez.<br/><br/>Pokud budete potřebovat datovou sadu, která se rozříznut hodinu, nastavíte <b>frekvence</b> k <b>hodinu</b>, a <b>interval</b> k <b>1</b>.<br/><br/><b>Poznámka:</b>: Pokud zadáte četnost jako minutu, doporučujeme nastavit interval na menší než 15 |Ano |Není k dispozici |
| Styl |Určuje, zda by měl být na zahájení a ukončení intervalu předložen řez.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Pokud je nastavena frekvence měsíc a styl je nastaven na EndOfInterval, řez vytváří poslední den v měsíci. Pokud je styl nastavené na StartOfInterval, řez vytváří první den v měsíci.<br/><br/>Pokud je nastavena frekvence den a styl je nastaven na EndOfInterval, řez se vytvářejí za poslední hodinu dne.<br/><br/>Pokud je nastavena frekvence hodinu a styl je nastaven na EndOfInterval, řez se vytvářejí na konci za hodinu. Například pro řez dobu 13: 00 – 14: 00, je řez vytvořeného ve 2. |Ne |EndOfInterval |
| anchorDateTime |Definuje absolutní pozici v čase plánovačem slouží k výpočtu hranice řez datovou sadu. <br/><br/><b>Poznámka:</b>: Pokud AnchorDateTime má částí data, která jsou podrobnější než je četnost pak podrobnější části jsou ignorovány. <br/><br/>Například pokud <b>interval</b> je <b>každou hodinu</b> (frekvence: hodin a interval: 1) a <b>AnchorDateTime</b> obsahuje <b>minuty a sekundy</b>potom <b>minuty a sekundy</b> částí AnchorDateTime jsou ignorovány. |Ne |01/01/0001 |
| Posun |Časový interval, ve kterém jsou zapuštěno počáteční a koncová všech řezech datovou sadu. <br/><br/><b>Poznámka:</b>: Pokud jsou zadané anchorDateTime i posun, výsledkem je kombinovaná shift. |Ne |Není k dispozici |

V následující části dostupnosti Určuje, že výstupní datovou sadu je buď vytvořené každou hodinu (nebo) vstupní datovou sadu každou hodinu je k dispozici:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

**Zásad** oddíl v definici datové sady definuje kritéria nebo podmínku, musíte splnit řezy datovou sadu.

| Název zásady | Popis | Použít | Požaduje se | Výchozí |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Ověří, jestli data v **objektů blob v Azure** splňuje požadavky na minimální velikost (v megabajtech). |Azure Blob |Ne |Není k dispozici |
| minimumRows |Ověří, jestli data v **Azure SQL database** nebo **tabulky Azure** obsahuje minimální počet řádků. |<ul><li>Azure SQL Database</li><li>Tabulka Azure</li></ul> |Ne |Není k dispozici |

**Příklad:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

Není-li datovou sadu se vytváří pomocí Azure Data Factory, by měl být označen jako **externí**. Toto nastavení se obvykle platí pro vstupy první aktivitu v kanálu, pokud aktivita nebo řetězení kanálu se používá.

| Název | Popis | Požaduje se | Výchozí hodnota |
| --- | --- | --- | --- |
| dataDelay |Doba zpoždění před kontroly na dostupnost externích dat pro danou řez. Například data každou hodinu je k dispozici, může být zkontrolujte externích dat je k dispozici a odpovídající řez je připravený zpožděn pomocí dataDelay.<br/><br/>Platí jenom pro aktuální čas.  Například pokud je 1:00 PM hned teď a tato hodnota je 10 minut, ověření se spustí: 10: 00.<br/><br/>Toto nastavení nemá vliv řezy v minulosti (řezy s řez koncový čas + dataDelay < teď) jsou zpracovávány bez jakéhokoli zpoždění.<br/><br/>Čas větší než 23:59 hodin muset zadat pomocí `day.hours:minutes:seconds` formátu. Například pokud chcete zadat 24 hodin, nepoužívejte 24:00:00; Místo toho použijte 1.00:00:00. Pokud používáte 24:00:00, bude považován za 24 dní (24.00:00:00). 1 den a 4 hodiny zadejte 1:04:00:00. |Ne |0 |
| retryInterval |Doba čekání mezi selhání a další opakujte pokus. Pokud se nezdaří zkuste to, je dalším pokusu o po retryInterval. <br/><br/>Pokud je 1:00 PM nyní, můžeme začít prvního pokusu. Pokud doba trvání dokončení první kontrola ověření je 1 minuta a operace se nezdařila, další pokus proběhne v 1:00 + 1 min (doba trvání) + 1 min (interval opakování) = 1:02 PM. <br/><br/>Řezy v minulosti není k dispozici žádné zpoždění není. Opakovaném dojde okamžitě. |Ne |00:01:00 (1 min) |
| retryTimeout |Časový limit pro jednotlivé pokusy o opakování.<br/><br/>Pokud je tato vlastnost nastavena na 10 minut, ověření musí být dokončeny v rámci 10 minut. Pokud trvá déle než 10 minut, aby k ověření, opakovaném časového limitu.<br/><br/>Pokud vyprší všechny pokusy o ověření řezu se označí jako TimedOut. |Ne |00:10:00 (10 minut) |
| maximumRetry |Počet přístupů k zkontrolujte dostupnost externí data. Maximální povolená hodnota je 10. |Ne |3 |


## <a name="data-stores"></a>DATOVÁ ÚLOŽIŠTĚ
[Propojená služba](#linked-service) části zadat popis pro elementy JSON, které jsou společné pro všechny typy propojené služby. Tato část obsahuje podrobnosti o elementy JSON, které jsou specifické pro každé datové úložiště.

[Datovou sadu](#dataset) části zadat popis pro elementy JSON, které jsou společné pro všechny typy datových sad. Tato část obsahuje podrobnosti o elementy JSON, které jsou specifické pro každé datové úložiště.

[Aktivity](#activity) části zadat popis pro elementy JSON, které jsou společné pro všechny typy aktivit. Tato část obsahuje podrobnosti o elementy JSON, které jsou specifické pro každý úložiště dat, pokud se používá jako zdroj/jímka v aktivitě kopírování.  

Kliknutím na odkaz pro úložiště, které vás zajímají zobrazíte schémata JSON propojené služby, datové sady a zdroj/jímka pro aktivitu kopírování.

| Kategorie | Úložiště dat 
|:--- |:--- |
| **Azure** |[Azure Blob Storage](#azure-blob-storage) |
| &nbsp; |[Azure Data Lake Store](#azure-datalake-store) |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure SQL Data Warehouse](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Search](#azure-search) |
| &nbsp; |[Azure Table storage](#azure-table-storage) |
| **Databáze** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **File** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Systém souborů](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Ostatní** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |[Webové tabulky](#web-table) |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Propojená služba
Existují dva typy propojené služby: propojená služba Azure Storage a propojená služba Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Propojení účtu úložiště Azure data factory pomocí **klíč účtu**, vytvoření služby Azure Storage, propojené. K definování Azure Storage propojené služby, nastavte **typ** propojené služby pro **azurestorage**. Potom můžete zadat následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k úložišti Azure pro vlastnost connectionString. |Ano |

##### <a name="example"></a>Příklad:  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Propojená služba Azure Storage SAS
Služba Azure úložiště SAS propojené umožňuje propojení účet úložiště Azure do Azure data factory pomocí sdíleného přístupového podpisu (SAS). Poskytuje objekt pro vytváření dat omezený nebo časově vázaných přístup k prostředkům všechna nebo konkrétní (kontejner nebo objektů blob) v úložišti. Propojte si účet úložiště Azure se objekt pro vytváření dat pomocí sdíleného přístupového podpisu, vytvořte SAS služby Azure Storage propojená služba. K definování úložiště SAS Azure propojené služby, nastavte **typ** propojené služby pro **AzureStorageSas**. Potom můžete zadat následující vlastnosti v **rámci typeProperties** části:   

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| sasUri |Zadejte identifikátor URI podpis sdíleného přístupu k prostředkům Azure Storage jako objekt blob, kontejneru nebo tabulky. |Ano |

##### <a name="example"></a>Příklad:

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

Další informace o těchto propojených služeb najdete v tématu [konektor Azure Blob Storage](data-factory-azure-blob-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby Azure Blob, nastavte **typ** datové sady, která **AzureBlob**. Potom zadejte následující vlastnosti objektů Blob v Azure konkrétní v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Cesta ke kontejneru a složce v úložišti objektů blob. Příklad: myblobcontainer\myblobfolder\ |Ano |
| fileName |Název objektu blob. Název souboru je volitelné a velká a malá písmena.<br/><br/>Pokud zadáte název souboru, na konkrétní objekt Blob funguje aktivitu (včetně kopie).<br/><br/>Pokud není zadán název souboru, zahrnuje kopírování všech objektů BLOB v folderPath pro vstupní datové sady.<br/><br/>Pokud není zadán název souboru pro datovou sadu výstupů, název vygenerovaný soubor bude v následujícím tento formát: Data. <Guid>.txt (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy vlastnost je volitelná. Můžete ji k určení dynamické folderPath a název souboru pro data časové řady. Například folderPath lze nastavit parametry pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


Další informace najdete v tématu [konektor Azure Blob](data-factory-azure-blob-connector.md#dataset-properties) článku.

### <a name="blobsource-in-copy-activity"></a>BlobSource v aktivitě kopírování
Pokud jsou kopírování dat z Azure Blob Storage, nastavte **typ zdroje** kopie aktivity na **BlobSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Označuje, zda je data načíst rekurzivně z dílčí složky nebo pouze do zadané složky. |True (výchozí hodnota), False. |Ne |

#### <a name="example-blobsource"></a>Příklad: **BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>BlobSink v aktivitě kopírování
Pokud jsou kopírování dat do Azure Blob Storage, nastavte **typ jímky** kopie aktivity na **BlobSink**a zadejte následující vlastnosti v **podřízený** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopie, pokud je zdroj BlobSource nebo systému souborů. |<b>PreserveHierarchy</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cestu k souboru cíl k cílové složce.<br/><br/><b>FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úroveň cílové složce. Cílové soubory mít název automaticky generovány. <br/><br/><b>MergeFiles (výchozí):</b> slučuje všechny soubory ze zdrojové složky pro jeden soubor. Pokud je zadán název souboru nebo objekt Blob, název souboru sloučené by být zadaný název; jinak by automaticky generovaný soubor název. |Ne |

#### <a name="example-blobsink"></a>Příklad: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace najdete v tématu [konektor Azure Blob](data-factory-azure-blob-connector.md#copy-activity-properties) článku. 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Propojená služba
K definování Azure Data Lake Store propojené služby, nastavte typ propojené služby pro **AzureDataLakeStore**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **AzureDataLakeStore** | Ano |
| dataLakeStoreUri | Zadejte informace o účtu Azure Data Lake Store. Je v následujícím formátu: `https://[accountname].azuredatalakestore.net/webhdfs/v1` nebo `adl://[accountname].azuredatalakestore.net/`. | Ano |
| subscriptionId | Id předplatného Azure, ke kterému patří Data Lake Store. | Vyžaduje se pro sink |
| resourceGroupName | Název skupiny prostředků Azure, ke kterému patří Data Lake Store. | Vyžaduje se pro sink |
| servicePrincipalId | Zadejte ID aplikace klienta. | Ano (pro objekt zabezpečení ověřování služby) |
| servicePrincipalKey | Zadejte klíč aplikace. | Ano (pro objekt zabezpečení ověřování služby) |
| tenant | Zadejte informace o klienta (název nebo klienta domény ID) v rámci které se nachází aplikace. Můžete ji načíst podržením ukazatele myši v pravém horním rohu portálu Azure. | Ano (pro objekt zabezpečení ověřování služby) |
| Autorizace | Klikněte na tlačítko **Authorize** v tlačítko **editoru služby Data Factory** a zadejte svoje přihlašovací údaje, které přiřadí automaticky generovaný autorizace adresu URL pro tuto vlastnost. | Ano (pro ověření přihlašovacích údajů uživatele)|
| ID relace | Id relace OAuth z autorizační relace OAuth. Každé id relace je jedinečné a může být použit pouze jednou. Toto nastavení se automaticky generuje při pomocí editoru služby Data Factory. | Ano (pro ověření přihlašovacích údajů uživatele) |

#### <a name="example-using-service-principal-authentication"></a>Příklad: použití ověřování hlavní služby
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Příklad: použití ověřování pověření uživatele
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

Další informace najdete v tématu [konektor Azure Data Lake Store](data-factory-azure-datalake-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby Azure Data Lake Store, nastavte **typ** datové sady, která **AzureDataLakeStore**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| folderPath |Cesta ke kontejneru a složce v Azure Data Lake úložiště. |Ano |
| fileName |Název souboru v úložišti Azure Data Lake. Název souboru je volitelné a velká a malá písmena. <br/><br/>Pokud zadáte název souboru, aktivitu (včetně kopie) funguje na konkrétní soubor.<br/><br/>Pokud není zadán název souboru, kopie zahrnuje všechny soubory v folderPath pro vstupní datové sady.<br/><br/>Pokud není zadán název souboru pro datovou sadu výstupů, název vygenerovaný soubor bude v následujícím tento formát: Data. <Guid>.txt (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy vlastnost je volitelná. Můžete ji k určení dynamické folderPath a název souboru pro data časové řady. Například folderPath lze nastavit parametry pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

#### <a name="example"></a>Příklad:
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Další informace najdete v tématu [konektor Azure Data Lake Store](data-factory-azure-datalake-connector.md#dataset-properties) článku. 

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Azure Data Lake Store zdroj v aktivitě kopírování
Pokud jsou kopírování dat z Azure Data Lake Store, nastavte **typ zdroje** kopie aktivity na **AzureDataLakeStoreSource**a zadejte následující vlastnosti v **zdroj**části:

**AzureDataLakeStoreSource** podporuje následující vlastnosti **rámci typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Označuje, zda je data načíst rekurzivně z dílčí složky nebo pouze do zadané složky. |True (výchozí hodnota), False. |Ne |

#### <a name="example-azuredatalakestoresource"></a>Příklad: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace najdete v tématu [konektor Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) článku.

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Podřízený Azure Data Lake Store v aktivitě kopírování
Pokud jsou kopírování dat do Azure Data Lake Store, nastavte **typ jímky** kopie aktivity na **AzureDataLakeStoreSink**a zadejte následující vlastnosti v **podřízený** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Určuje chování kopírování. |<b>PreserveHierarchy</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cestu k souboru cíl k cílové složce.<br/><br/><b>FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou vytvořené v první úroveň cílové složce. Cílové soubory jsou vytvořeny s názvem automaticky generovány.<br/><br/><b>MergeFiles</b>: sloučí všechny soubory ze zdrojové složky pro jeden soubor. Pokud je zadán název souboru nebo objekt Blob, název souboru sloučené by být zadaný název; jinak by automaticky generovaný soubor název. |Ne |

#### <a name="example-azuredatalakestoresink"></a>Příklad: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace najdete v tématu [konektor Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) článku. 

## <a name="azure-cosmos-db"></a>Azure Cosmos DB  

### <a name="linked-service"></a>Propojená služba
K definování Azure DB Cosmos propojené služby, nastavte **typ** propojené služby pro **DocumentDb**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| **Vlastnost** | **Popis** | **Požadované** |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k databázi Azure Cosmos DB. |Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Další informace najdete v tématu [konektor Azure Cosmos DB](data-factory-azure-documentdb-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby Azure Cosmos DB, nastavte **typ** datové sady, která **DocumentDbCollection**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| **Vlastnost** | **Popis** | **Požadované** |
| --- | --- | --- |
| Název_kolekce |Název kolekce Azure Cosmos DB. |Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Další informace najdete v tématu [konektor Azure Cosmos DB](data-factory-azure-documentdb-connector.md#dataset-properties) článku.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Zdroj kolekce Azure Cosmos DB v aktivitě kopírování
Pokud kopírujete data z databáze Cosmos Azure, nastavte **typ zdroje** kopie aktivity na **DocumentDbCollectionSource**a zadejte následující vlastnosti v **zdroj**části:


| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Požadované** |
| --- | --- | --- | --- |
| query |Zadejte dotaz číst data. |Řetězec nepodporuje Azure Cosmos DB dotazu. <br/><br/>Příklad: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Ne <br/><br/>Pokud není zadaný příkaz jazyka SQL, který se spustí: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciální znak indikující, že dokument je vnořený |Libovolný znak. <br/><br/>Azure Cosmos DB je úložiště typu NoSQL pro dokumenty JSON, kde jsou povoleny vnořené struktury. Azure Data Factory umožňuje uživateli označují hierarchie prostřednictvím nestingSeparator, což je "." v předchozích příkladech. S oddělovačem, aktivitě kopírování bude generovat objekt "Name" tři podřízené elementy nejprve, střední a příjmení podle "Name.First", "Name.Middle" a "Name.Last" v definici tabulky. |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Azure Cosmos DB kolekce podřízený v aktivitě kopírování
Pokud jsou kopírování dat do Azure Cosmos DB, nastavte **typ jímky** kopie aktivity na **DocumentDbCollectionSink**a zadejte následující vlastnosti v **podřízený** části :

| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Požadované** |
| --- | --- | --- | --- |
| nestingSeparator |Budete potřebovat speciální znak v názvu sloupce zdroj označíte, že vnořených dokumentů. <br/><br/>Například výše: `Name.First` ve výstupu tabulky vytvoří následující strukturu JSON v dokumentu Cosmos DB:<br/><br/>"Název": {<br/>    "První": "Jan"<br/>}, |Znak, který se používá k oddělení úrovní vnoření.<br/><br/>Výchozí hodnota je `.` (tečka). |Znak, který se používá k oddělení úrovní vnoření. <br/><br/>Výchozí hodnota je `.` (tečka). |
| writeBatchSize |Počet paralelní požadavků do služby Azure Cosmos DB vytvářet dokumenty.<br/><br/>Při kopírování dat z Azure Cosmos DB pomocí této vlastnosti lze optimalizovat výkon. Lepšího výkonu můžete očekávat, když zvýšíte writeBatchSize, protože se odesílají další paralelní požadavky pro Azure Cosmos DB. Ale budete muset vyhnout, omezení šířky pásma, který lze vyvolat chybovou zprávu: "Požadavků je velká".<br/><br/>Omezení je určeno podle počtu faktorů, včetně velikosti dokumentů, počet podmínky v dokumentech, indexování zásad cílovou kolekci, atd. Pro operace kopírování, můžete použít kolekci lepší (například S3) tak, aby měl nejvíce propustnost, které jsou k dispozici (2 500 žádostí jednotek za sekundu). |Integer |Ne (výchozí: 5) |
| writeBatchTimeout |Počkejte, než čas na dokončení předtím, než vyprší časový limit operace. |časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Další informace najdete v tématu [konektor Azure Cosmos DB](data-factory-azure-documentdb-connector.md#copy-activity-properties) článku.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Propojená služba
K definování Azure SQL Database propojené služby, nastavte **typ** propojené služby pro **azuresqldatabase**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci databáze SQL Azure pro vlastnost connectionString. |Ano |

#### <a name="example"></a>Příklad:
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Další informace najdete v tématu [konektor služby Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby Azure SQL Database, nastavte **typ** datové sady, která **AzureSqlTable**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení instance databáze SQL Azure, kterou propojená služba odkazuje. |Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Další informace najdete v tématu [konektor služby Azure SQL](data-factory-azure-sql-connector.md#dataset-properties) článku. 

### <a name="sql-source-in-copy-activity"></a>Zdroje SQL v aktivitě kopírování
Pokud kopírujete data z databáze SQL Azure, nastavte **typ zdroje** kopie aktivity na **SqlSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, který čte data ze zdrojové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Další informace najdete v tématu [konektor služby Azure SQL](data-factory-azure-sql-connector.md#copy-activity-properties) článku. 

### <a name="sql-sink-in-copy-activity"></a>Jímku SQL v aktivitě kopírování
Pokud data kopírujete do Azure SQL Database, nastavte **typ jímky** kopie aktivity na **SqlSink**a zadejte následující vlastnosti v **podřízený** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte, než čas na dokončení předtím, než vyprší časový limit operace dávkové vložení. |časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování provést tak, aby se vyčistit data určitý řez. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování vyplníte identifikátor automaticky generovány řez, který se používá k vyčištění dat určitý řez při spusťte znovu. |Název sloupce sloupce s datovým typem binary(32). |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury upserts (aktualizace nebo vložení) dat do cílové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název typu tabulky má být použit v uložené proceduře. Aktivita kopírování zpřístupní přesouvání dat v dočasné tabulce s tímto typem tabulky. Uložená procedura kód pak sloučit data kopírovány s existujícími daty. |Zadejte název tabulky. |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace najdete v tématu [konektor služby Azure SQL](data-factory-azure-sql-connector.md#copy-activity-properties) článku. 

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Propojená služba
K definování Azure SQL Data Warehouse propojené služby, nastavte **typ** propojené služby pro **AzureSqlDW**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k Azure SQL Data Warehouse instance pro vlastnost connectionString. |Ano |



#### <a name="example"></a>Příklad:

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Další informace najdete v tématu [konektor Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby Azure SQL Data Warehouse, nastavte **typ** datové sady, která **AzureSqlDWTable**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v databázi Azure SQL Data Warehouse, která propojená služba odkazuje. |Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Další informace najdete v tématu [konektor Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) článku. 

### <a name="sql-dw-source-in-copy-activity"></a>Zdroj datového skladu SQL v aktivitě kopírování
Pokud jsou kopírování dat z Azure SQL Data Warehouse, nastavte **typ zdroje** kopie aktivity na **SqlDWSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, který čte data ze zdrojové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace najdete v tématu [konektor Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) článku. 

### <a name="sql-dw-sink-in-copy-activity"></a>Podřízený datového skladu SQL v aktivitě kopírování
Pokud jsou kopírování dat do Azure SQL Data Warehouse, nastavte **typ jímky** kopie aktivity na **SqlDWSink**a zadejte následující vlastnosti v **podřízený** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování provést tak, aby se vyčistit data určitý řez. |Příkaz dotazu. |Ne |
| allowPolyBase |Označuje, zda místo hromadné vložení mechanismus použít PolyBase (v případě potřeby). <br/><br/> **Pomocí PolyBase je doporučeným způsobem, jak načíst data do SQL Data Warehouse.** |True <br/>NEPRAVDA (výchozí) |Ne |
| polyBaseSettings |Skupinu vlastností, které se dají zadat při **allowPolybase** je nastavena na **true**. |&nbsp; |Ne |
| rejectValue |Určuje číslo nebo podíl řádků, které může být odmítnutá předtím, než se dotaz nezdaří. <br/><br/>Další informace o možnostech odmítněte používání funkce PolyBase v **argumenty** části [vytvořit EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tématu. |0 (výchozí), 1, 2... |Ne |
| rejectType |Určuje, zda je hodnota literálu nebo jako procento zadána možnost rejectValue. |Hodnota (výchozí), procento |Ne |
| rejectSampleValue |Určuje počet řádků k načtení předtím, než PolyBase přepočítá procento odmítnutých řádků. |1, 2, … |Ano, pokud **rejectType** je **procento** |
| useTypeDefault |Určuje způsob zpracování chybějící hodnoty v textových souborů s oddělovači, když PolyBase načítá data z textového souboru.<br/><br/>Další informace o této vlastnosti v části argumenty [vytvořit EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Hodnota TRUE, False (výchozí) |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Počkejte, než čas na dokončení předtím, než vyprší časový limit operace dávkové vložení. |časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace najdete v tématu [konektor Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) článku. 

## <a name="azure-search"></a>Azure Search

### <a name="linked-service"></a>Propojená služba
K definování Azure Search propojené služby, nastavte **typ** propojené služby pro **AzureSearch**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| url | Adresa URL pro službu Azure Search. | Ano |
| key | Klíč správce pro službu Azure Search. | Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

Další informace najdete v tématu [konektor Azure Search](data-factory-azure-search-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby Azure Search, nastavte **typ** datové sady, která **AzureSearchIndex**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| type | Vlastnost typu musí být nastavená na **AzureSearchIndex**.| Ano |
| indexName | Název indexu Azure Search. Objekt pro vytváření dat vytvořit index. Index musí existovat ve službě Azure Search. | Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Další informace najdete v tématu [konektor Azure Search](data-factory-azure-search-connector.md#dataset-properties) článku.

### <a name="azure-search-index-sink-in-copy-activity"></a>Podřízený Index Azure Search v aktivitě kopírování
Pokud jsou kopírování dat do indexu Azure Search, nastavte **typ jímky** kopie aktivity na **AzureSearchIndexSink**a zadejte následující vlastnosti v **podřízený** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| -------- | ----------- | -------------- | -------- |
| writeBehavior | Určuje, jestli se má sloučit nebo nahradit, pokud již dokument v indexu existuje. | Sloučí (výchozí)<br/>Odeslat| Ne |
| writeBatchSize | Ukládání dat do indexu Azure Search, když velikost vyrovnávací paměti dosáhne writeBatchSize. | 1 do 1000. Výchozí hodnota je 1 000. | Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace najdete v tématu [konektor Azure Search](data-factory-azure-search-connector.md#copy-activity-properties) článku.

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Propojená služba
Existují dva typy propojené služby: propojená služba Azure Storage a propojená služba Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Propojení účtu úložiště Azure data factory pomocí **klíč účtu**, vytvoření služby Azure Storage, propojené. K definování Azure Storage propojené služby, nastavte **typ** propojené služby pro **azurestorage**. Potom můžete zadat následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost typu musí být nastavena na: **azurestorage.** |Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k úložišti Azure pro vlastnost connectionString. |Ano |

**Příklad:**  

```json
{  
    "name": "StorageLinkedService",  
    "properties": {  
        "type": "AzureStorage",  
        "typeProperties": {  
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
        }  
    }  
}  
```

#### <a name="azure-storage-sas-linked-service"></a>Propojená služba Azure Storage SAS
Služba Azure úložiště SAS propojené umožňuje propojení účet úložiště Azure do Azure data factory pomocí sdíleného přístupového podpisu (SAS). Poskytuje objekt pro vytváření dat omezený nebo časově vázaných přístup k prostředkům všechna nebo konkrétní (kontejner nebo objektů blob) v úložišti. Propojte si účet úložiště Azure se objekt pro vytváření dat pomocí sdíleného přístupového podpisu, vytvořte SAS služby Azure Storage propojená služba. K definování úložiště SAS Azure propojené služby, nastavte **typ** propojené služby pro **AzureStorageSas**. Potom můžete zadat následující vlastnosti v **rámci typeProperties** části:   

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost typu musí být nastavena na: **AzureStorageSas** |Ano |
| sasUri |Zadejte identifikátor URI podpis sdíleného přístupu k prostředkům Azure Storage jako objekt blob, kontejneru nebo tabulky. |Ano |

**Příklad:**

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

Další informace o těchto propojených služeb najdete v tématu [konektor Azure Table Storage](data-factory-azure-table-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby Azure Table, nastavte **typ** datové sady, která **AzureTable**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance Azure tabulku databáze, kterou propojená služba odkazuje. |Ano. Pokud název tabulky je zadán bez azureTableSourceQuery, všechny záznamy z tabulky se zkopírují do cílového umístění. Pokud je zadána také azureTableSourceQuery, záznamy z tabulky, která splňuje dotaz zkopírují do cílového umístění. |

#### <a name="example"></a>Příklad:

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Další informace o těchto propojených služeb najdete v tématu [konektor Azure Table Storage](data-factory-azure-table-connector.md#dataset-properties) článku. 

### <a name="azure-table-source-in-copy-activity"></a>Zdrojové tabulky Azure v aktivitě kopírování
Pokud jsou kopírování dat z úložiště tabulek Azure, nastavte **typ zdroje** kopie aktivity na **AzureTableSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| azureTableSourceQuery |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu tabulky Azure. Příklady v další části. |Ne. Pokud název tabulky je zadán bez azureTableSourceQuery, všechny záznamy z tabulky se zkopírují do cílového umístění. Pokud je zadána také azureTableSourceQuery, záznamy z tabulky, která splňuje dotaz zkopírují do cílového umístění. |
| azureTableSourceIgnoreTableNotFound |Označuje, zda swallow výjimky tabulky neexistuje. |HODNOTA TRUE<br/>FALSE |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace o těchto propojených služeb najdete v tématu [konektor Azure Table Storage](data-factory-azure-table-connector.md#copy-activity-properties) článku. 

### <a name="azure-table-sink-in-copy-activity"></a>Podřízený tabulky Azure v aktivitě kopírování
Pokud jsou kopírování dat do Azure Table Storage, nastavte **typ jímky** kopie aktivity na **AzureTableSink**a zadejte následující vlastnosti v **podřízený** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Výchozí hodnotu klíče oddílu, mohou být využívána jímky. |Hodnotu řetězce. |Ne |
| azureTablePartitionKeyName |Zadejte název sloupce, jejichž hodnoty se používají jako klíče oddílů. Pokud není zadaný, použije se AzureTableDefaultPartitionKeyValue jako klíč oddílu. |Název sloupce. |Ne |
| azureTableRowKeyName |Zadejte název sloupce, jejichž hodnoty sloupce jsou použity jako klíč řádku. Pokud není zadaný, použijte identifikátor GUID pro každý řádek. |Název sloupce. |Ne |
| azureTableInsertType |Režim vložení dat do tabulky Azure.<br/><br/>Tato vlastnost určuje, jestli mají existující řádky v tabulce output s odpovídajícím klíče oddílu a řádku jejich hodnoty nahradit nebo sloučit. <br/><br/>Další informace o tom, jak tato nastavení (sloučení a nahraďte) fungují, najdete v části [vložení nebo sloučení Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) a [vložení nebo nahrazení Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx) témata. <br/><br> Toto nastavení se vztahuje na úrovni řádků, není úrovni tabulky a ani možnost odstraní řádků do výstupní tabulky, které nejsou k dispozici ve vstupu. |Merge (výchozí)<br/>nahradit |Ne |
| writeBatchSize |Když je dosaženo writeBatchSize nebo writeBatchTimeout vkládá data do tabulky Azure. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Když je dosaženo writeBatchSize nebo writeBatchTimeout vkládá data do tabulky Azure |časový interval<br/><br/>Příklad: "00:20:00" (20 minut) |Ne (výchozí nastavení časového limitu výchozí úložiště klienta hodnotu 90 sekundu) |

#### <a name="example"></a>Příklad:

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Další informace o těchto propojených služeb najdete v tématu [konektor Azure Table Storage](data-factory-azure-table-connector.md#copy-activity-properties) článku. 

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Propojená služba
K definování Amazon Redshift propojené služby, nastavte **typ** propojené služby pro **AmazonRedshift**a zadejte následující vlastnosti v **rámci typeProperties** části :  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |IP adresa nebo název hostitele serveru Amazon Redshift. |Ano |
| port |Číslo portu TCP, který používá server Amazon Redshift naslouchat pro připojení klientů. |Ne, výchozí hodnota: 5439 |
| databáze |Název databáze Amazon Redshift. |Ano |
| uživatelské jméno |Jméno uživatele, který má přístup k databázi. |Ano |
| heslo |Heslo pro uživatelský účet. |Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Další informace najdete v tématu [Amazon Redshift konektor](#data-factory-amazon-redshift-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby Amazon Redshift, nastavte **typ** datové sady, která **RelationalTable**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v databázi Amazon Redshift, propojená služba odkazuje. |Ne (Pokud **dotazu** z **RelationalSource** je zadána) |


#### <a name="example"></a>Příklad:

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Další informace najdete v tématu [Amazon Redshift konektor](#data-factory-amazon-redshift-connector.md#dataset-properties) článku.

### <a name="relational-source-in-copy-activity"></a>Relačního zdroje v aktivitě kopírování 
Pokud jsou kopírování dat z Amazon Redshift, nastavte **typ zdroje** kopie aktivity na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ne (Pokud **tableName** z **datovou sadu** je zadána) |

#### <a name="example"></a>Příklad:

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Další informace najdete v tématu [Amazon Redshift konektor](#data-factory-amazon-redshift-connector.md#copy-activity-properties) článku.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Propojená služba
K definování IBM DB2 propojené služby, nastavte **typ** propojené služby pro **OnPremisesDB2**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru DB2. |Ano |
| databáze |Název databáze DB2. |Ano |
| Schéma |Název schématu v databázi. Název schématu rozlišuje velká a malá písmena. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k databázi DB2. Možné hodnoty jsou: anonymní, základní a systému Windows. |Ano |
| uživatelské jméno |Pokud používáte ověřování Basic nebo Windows, zadejte uživatelské jméno. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k místní databázi DB2. |Ano |

#### <a name="example"></a>Příklad:
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Další informace najdete v tématu [IBM DB2 konektor](#data-factory-onprem-db2-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
Chcete-li definovat DB2 datovou sadu, nastavte **typ** datové sady, která **RelationalTable**a zadejte následující vlastnosti v **rámci typeProperties** části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze DB2 na kterou odkazuje propojená služba. TableName rozlišuje velká a malá písmena. |Ne (Pokud **dotazu** z **RelationalSource** je zadána) 

#### <a name="example"></a>Příklad:
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Další informace najdete v tématu [IBM DB2 konektor](#data-factory-onprem-db2-connector.md#dataset-properties) článku.

### <a name="relational-source-in-copy-activity"></a>Relačního zdroje v aktivitě kopírování
Pokud jsou kopírování dat z IBM DB2, nastavte **typ zdroje** kopie aktivity na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Například: `"query": "select * from "MySchema"."MyTable""`. |Ne (Pokud **tableName** z **datovou sadu** je zadána) |

#### <a name="example"></a>Příklad:
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Další informace najdete v tématu [IBM DB2 konektor](#data-factory-onprem-db2-connector.md#copy-activity-properties) článku.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Propojená služba
K definování MySQL propojené služby, nastavte **typ** propojené služby pro **OnPremisesMySql**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru databáze MySQL. |Ano |
| databáze |Název databáze MySQL. |Ano |
| Schéma |Název schématu v databázi. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k databázi MySQL. Možné hodnoty jsou: `Basic`. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno pro připojení k databázi MySQL. |Ano |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali. |Ano |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k místní databázi MySQL. |Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Další informace najdete v tématu [MySQL konektor](data-factory-onprem-mysql-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu MySQL, nastavte **typ** datové sady, která **RelationalTable**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze MySQL na kterou odkazuje propojená služba. |Ne (Pokud **dotazu** z **RelationalSource** je zadána) |

#### <a name="example"></a>Příklad:

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Další informace najdete v tématu [MySQL konektor](data-factory-onprem-mysql-connector.md#dataset-properties) článku. 

### <a name="relational-source-in-copy-activity"></a>Relačního zdroje v aktivitě kopírování
Pokud kopírujete data z databáze MySQL, nastavte **typ zdroje** kopie aktivity na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ne (Pokud **tableName** z **datovou sadu** je zadána) |


#### <a name="example"></a>Příklad:
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Další informace najdete v tématu [MySQL konektor](data-factory-onprem-mysql-connector.md#copy-activity-properties) článku. 

## <a name="oracle"></a>Oracle 

### <a name="linked-service"></a>Propojená služba
K definování Oracle propojené služby, nastavte **typ** propojené služby pro **OnPremisesOracle**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| driverType | Určete, který ovladač použít ke zkopírování dat z/do databáze Oracle. Povolené hodnoty jsou **Microsoft** nebo **ODP** (výchozí). V tématu [podporované verze a instalace](#supported-versions-and-installation) části na podrobnosti o ovladači. | Ne |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci databáze Oracle pro vlastnost connectionString. | Ano |
| gatewayName | Název brány, aby se používá pro připojení k místnímu serveru Oracle |Ano |

#### <a name="example"></a>Příklad:
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Další informace najdete v tématu [Oracle konektor](data-factory-onprem-oracle-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby Oracle, nastavte **typ** datové sady, která **OracleTable**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v databázi Oracle, který propojená služba odkazuje na. |Ne (Pokud **oracleReaderQuery** z **OracleSource** je zadána) |

#### <a name="example"></a>Příklad:

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Další informace najdete v tématu [Oracle konektor](data-factory-onprem-oracle-connector.md#dataset-properties) článku.

### <a name="oracle-source-in-copy-activity"></a>Zdroj Oracle v aktivitě kopírování
Pokud kopírujete data z databáze Oracle, nastavte **typ zdroje** kopie aktivity na **OracleSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| oracleReaderQuery |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Příklad: `select * from MyTable` <br/><br/>Pokud není zadaný příkaz jazyka SQL, který se spustí: `select * from MyTable` |Ne (Pokud **tableName** z **datovou sadu** je zadána) |

#### <a name="example"></a>Příklad:

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace najdete v tématu [Oracle konektor](data-factory-onprem-oracle-connector.md#copy-activity-properties) článku.

### <a name="oracle-sink-in-copy-activity"></a>Podřízený Oracle v aktivitě kopírování
Pokud jsou kopírování dat do databáze Oracle am, nastavte **typ jímky** kopie aktivity na **OracleSink**a zadejte následující vlastnosti v **podřízený** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte, než čas na dokončení předtím, než vyprší časový limit operace dávkové vložení. |časový interval<br/><br/> Příklad: 00:30:00 (30 minut). |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize. |Celé číslo (počet řádků) |Ne (výchozí: 100) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování provést tak, aby se vyčistit data určitý řez. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování vyplníte identifikátor automaticky generovány řez, který se používá k vyčištění dat určitý řez při spusťte znovu. |Název sloupce sloupce s datovým typem binary(32). |Ne |

#### <a name="example"></a>Příklad:
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Další informace najdete v tématu [Oracle konektor](data-factory-onprem-oracle-connector.md#copy-activity-properties) článku.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Propojená služba
K definování PostgreSQL propojené služby, nastavte **typ** propojené služby pro **OnPremisesPostgreSql**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru PostgreSQL. |Ano |
| databáze |Název databáze PostgreSQL. |Ano |
| Schéma |Název schématu v databázi. Název schématu rozlišuje velká a malá písmena. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k databázi PostgreSQL. Možné hodnoty jsou: anonymní, základní a systému Windows. |Ano |
| uživatelské jméno |Pokud používáte ověřování Basic nebo Windows, zadejte uživatelské jméno. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k místní databázi PostgreSQL. |Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Další informace najdete v tématu [PostgreSQL konektor](data-factory-onprem-postgresql-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
Chcete-li definovat PostgreSQL datovou sadu, nastavte **typ** datové sady, která **RelationalTable**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze PostgreSQL, kterou propojená služba odkazuje. TableName rozlišuje velká a malá písmena. |Ne (Pokud **dotazu** z **RelationalSource** je zadána) |

#### <a name="example"></a>Příklad:
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Další informace najdete v tématu [PostgreSQL konektor](data-factory-onprem-postgresql-connector.md#dataset-properties) článku.

### <a name="relational-source-in-copy-activity"></a>Relačního zdroje v aktivitě kopírování
Pokud kopírujete data z databáze PostgreSQL, nastavte **typ zdroje** kopie aktivity na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Například: "dotaz": "vybrat * z \"MySchema\".\" MyTable\"". |Ne (Pokud **tableName** z **datovou sadu** je zadána) |

#### <a name="example"></a>Příklad:

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Další informace najdete v tématu [PostgreSQL konektor](data-factory-onprem-postgresql-connector.md#copy-activity-properties) článku.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Propojená služba
K definování SAP Business Warehouse (BW) propojené služby, nastavte **typ** propojené služby pro **SapBw**a zadejte následující vlastnosti v **rámci typeProperties** části :  

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém se nachází instance SAP BW. | řetězec | Ano
systemNumber | Číslo systému SAP BW systému. | Desetinné číslo letopočty řetězec. | Ano
clientId | ID klienta v systému SAP W klienta. | Tři číslice desítkové číslo řetězec. | Ano
uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP | řetězec | Ano
heslo | Heslo pro uživatele. | řetězec | Ano
gatewayName | Název brány, kterou služba Data Factory měla použít pro připojení k místní instanci SAP BW. | řetězec | Ano
encryptedCredential | Řetězec šifrovaný přihlašovací údaj. | řetězec | Ne

#### <a name="example"></a>Příklad:

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Další informace najdete v tématu [SAP Business Warehouse konektor](data-factory-sap-business-warehouse-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat SAP BW datovou sadu, nastavte **typ** datové sady, která **RelationalTable**. Nejsou žádné vlastnosti specifické pro typ podporované pro SAP BW datovou sadu typu **RelationalTable**.  

#### <a name="example"></a>Příklad:

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Další informace najdete v tématu [SAP Business Warehouse konektor](data-factory-sap-business-warehouse-connector.md#dataset-properties) článku. 

### <a name="relational-source-in-copy-activity"></a>Relačního zdroje v aktivitě kopírování
Pokud jsou kopírování dat z SAP Business Warehouse, nastavte **typ zdroje** kopie aktivity na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query | Určuje dotaz MDX číst data z instance SAP BW. | Dotaz MDX. | Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Další informace najdete v tématu [SAP Business Warehouse konektor](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) článku. 

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Propojená služba
K definování SAP HANA propojené služby, nastavte **typ** propojené služby pro **SapHana**a zadejte následující vlastnosti v **rámci typeProperties** části:  

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém se nachází instance SAP HANA. Pokud váš server používá vlastní port, zadejte `server:port`. | řetězec | Ano
authenticationType. | Typ ověřování. | Řetězec. "Základní" nebo "Systém Windows" | Ano 
uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP | řetězec | Ano
heslo | Heslo pro uživatele. | řetězec | Ano
gatewayName | Název brány, kterou služba Data Factory měla použít pro připojení k místní instanci SAP HANA. | řetězec | Ano
encryptedCredential | Řetězec šifrovaný přihlašovací údaj. | řetězec | Ne

#### <a name="example"></a>Příklad:

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Další informace najdete v tématu [SAP HANA konektor](data-factory-sap-hana-connector.md#linked-service-properties) článku.
 
### <a name="dataset"></a>Datová sada
Chcete-li definovat SAP HANA datovou sadu, nastavte **typ** datové sady, která **RelationalTable**. Nejsou k dispozici žádné vlastnosti specifické pro typ podporované pro datovou sadu SAP HANA typu **RelationalTable**. 

#### <a name="example"></a>Příklad:

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Další informace najdete v tématu [SAP HANA konektor](data-factory-sap-hana-connector.md#dataset-properties) článku. 

### <a name="relational-source-in-copy-activity"></a>Relačního zdroje v aktivitě kopírování
Pokud jsou kopírování dat z jiného úložiště dat SAP HANA, nastavte **typ zdroje** kopie aktivity na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query | Určuje příkaz jazyka SQL pro čtení dat z instance SAP HANA. | Dotaz SQL. | Ano |


#### <a name="example"></a>Příklad:


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Další informace najdete v tématu [SAP HANA konektor](data-factory-sap-hana-connector.md#copy-activity-properties) článku.


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Propojená služba
Vytvoření propojené služby typu **onpremisessqlserver** propojit místní databázi systému SQL Server do služby data factory. Následující tabulka obsahuje popis specifické pro službu SQL serveru propojená místní elementy JSON.

Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro SQL Server propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavená na: **onpremisessqlserver**. |Ano |
| připojovací řetězec |Zadejte připojovací řetězec informace potřebné pro připojení k místní databázi systému SQL Server pomocí ověřování SQL nebo ověřování systému Windows. |Ano |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k místní databázi systému SQL Server. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud používáte ověřování systému Windows. Příklad: **domainname\\uživatelské jméno**. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |

Můžete šifrovat přihlašovací údaje pomocí **New-AzureRmDataFactoryEncryptValue** rutiny a jak je znázorněno v následujícím příkladu je využít v připojovacím řetězci (**EncryptedCredential** vlastnost):  

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Příklad: JSON pro pomocí ověřování SQL.

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Příklad: JSON pro použití ověřování systému Windows

Pokud jsou zadané uživatelské jméno a heslo, brána je používá k zosobnění zadaný uživatelský účet pro připojení k místní databázi systému SQL Server. Jinak brána připojí k systému SQL Server přímo s kontextem zabezpečení brány (jeho účet spuštění).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Další informace najdete v tématu [systému SQL Server konektoru](data-factory-sqlserver-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sady SQL Server, nastavte **typ** datové sady, která **SqlServerTable**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení instance databáze serveru SQL, kterou propojená služba odkazuje. |Ano |

#### <a name="example"></a>Příklad:
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Další informace najdete v tématu [systému SQL Server konektoru](data-factory-sqlserver-connector.md#dataset-properties) článku. 

### <a name="sql-source-in-copy-activity"></a>Zdroje SQL v aktivitě kopírování
Pokud kopírujete data z databáze systému SQL Server, nastavte **typ zdroje** kopie aktivity na **SqlSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Například: `select * from MyTable`. Může odkazovat více tabulek z databáze odkazuje vstupní datové sady. Pokud není zadaný příkaz jazyka SQL, která se provedla: Vyberte možnost z MyTable. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, který čte data ze zdrojové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. |Ne |

Pokud **sqlReaderQuery** je zadán pro SqlSource, aktivitě kopírování spustí tento dotaz na zdroji databáze systému SQL Server získat data.

Alternativně můžete zadat uložené procedury zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přebírá parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura slouží k vytvoření dotazu vyberte možnost spustit v databázi SQL serveru. Pokud definice datové sady nemá strukturu, jsou vybrány všechny sloupce z tabulky.

> [!NOTE]
> Při použití **sqlReaderStoredProcedureName**, stále je třeba zadat hodnotu pro **tableName** vlastnost v datové sadě JSON. Neexistují žádné ověření, ale adresovat této tabulky.


#### <a name="example"></a>Příklad:
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

V tomto příkladu **sqlReaderQuery** je zadán pro SqlSource. Aktivita kopírování spustí tento dotaz na zdroji databáze systému SQL Server získat data. Alternativně můžete zadat uložené procedury zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přebírá parametry). SqlReaderQuery může odkazovat více tabulek v databázi odkazuje vstupní datové sady. Se neomezuje jenom do tabulky, nastavte jako typeProperty tableName datovou sadu.

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura slouží k vytvoření dotazu vyberte možnost spustit v databázi SQL serveru. Pokud definice datové sady nemá strukturu, jsou vybrány všechny sloupce z tabulky.

Další informace najdete v tématu [systému SQL Server konektoru](data-factory-sqlserver-connector.md#copy-activity-properties) článku. 

### <a name="sql-sink-in-copy-activity"></a>Jímku SQL v aktivitě kopírování
Pokud data kopírujete do databáze systému SQL Server, nastavte **typ jímky** kopie aktivity na **SqlSink**a zadejte následující vlastnosti v **podřízený** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte, než čas na dokončení předtím, než vyprší časový limit operace dávkové vložení. |časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz aktivity kopírování provést tak, aby se vyčistit data určitý řez. Další informace najdete v tématu [opakovatelnosti](#repeatability-during-copy) části. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování vyplníte identifikátor automaticky generovány řez, který se používá k vyčištění dat určitý řez při spusťte znovu. Další informace najdete v tématu [opakovatelnosti](#repeatability-during-copy) části. |Název sloupce sloupce s datovým typem binary(32). |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury upserts (aktualizace nebo vložení) dat do cílové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název typu tabulky má být použit v uložené proceduře. Aktivita kopírování zpřístupní přesouvání dat v dočasné tabulce s tímto typem tabulky. Uložená procedura kód pak sloučit data kopírovány s existujícími daty. |Zadejte název tabulky. |Ne |

#### <a name="example"></a>Příklad:
Kanál obsahuje aktivitu kopírování, která je konfigurovaná pro používání těchto vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **podřízený** je typ nastaven na **SqlSink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace najdete v tématu [systému SQL Server konektoru](data-factory-sqlserver-connector.md#copy-activity-properties) článku. 

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Propojená služba
K definování Sybase propojené služby, nastavte **typ** propojené služby pro **OnPremisesSybase**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru databáze Sybase. |Ano |
| databáze |Název databáze Sybase. |Ano |
| Schéma |Název schématu v databázi. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k databázi Sybase. Možné hodnoty jsou: anonymní, základní a systému Windows. |Ano |
| uživatelské jméno |Pokud používáte ověřování Basic nebo Windows, zadejte uživatelské jméno. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k místní databázi Sybase. |Ano |

#### <a name="example"></a>Příklad:
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Další informace najdete v tématu [Sybase konektor](data-factory-onprem-sybase-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat Sybase datovou sadu, nastavte **typ** datové sady, která **RelationalTable**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze Sybase na kterou odkazuje propojená služba. |Ne (Pokud **dotazu** z **RelationalSource** je zadána) |

#### <a name="example"></a>Příklad:

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Další informace najdete v tématu [Sybase konektor](data-factory-onprem-sybase-connector.md#dataset-properties) článku. 

### <a name="relational-source-in-copy-activity"></a>Relačního zdroje v aktivitě kopírování
Pokud kopírujete data z databáze Sybase, nastavte **typ zdroje** kopie aktivity na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části :


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ne (Pokud **tableName** z **datovou sadu** je zadána) |

#### <a name="example"></a>Příklad:

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Další informace najdete v tématu [Sybase konektor](data-factory-onprem-sybase-connector.md#copy-activity-properties) článku.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Propojená služba
K definování Teradata propojené služby, nastavte **typ** propojené služby pro **OnPremisesTeradata**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru Teradata. |Ano |
| authenticationType. |Typ ověřování používaný pro připojení k databázi Teradata. Možné hodnoty jsou: anonymní, základní a systému Windows. |Ano |
| uživatelské jméno |Pokud používáte ověřování Basic nebo Windows, zadejte uživatelské jméno. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k místní databázi Teradata. |Ano |

#### <a name="example"></a>Příklad:
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Další informace najdete v tématu [Teradata konektor](data-factory-onprem-teradata-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu objektu Teradata Blob, nastavte **typ** datové sady, která **RelationalTable**. Momentálně nejsou k dispozici žádné vlastnosti typu podporované pro datovou sadu Teradata. 

#### <a name="example"></a>Příklad:
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Další informace najdete v tématu [Teradata konektor](data-factory-onprem-teradata-connector.md#dataset-properties) článku.

### <a name="relational-source-in-copy-activity"></a>Relačního zdroje v aktivitě kopírování
Pokud kopírujete data z databáze Teradata, nastavte **typ zdroje** kopie aktivity na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Další informace najdete v tématu [Teradata konektor](data-factory-onprem-teradata-connector.md#copy-activity-properties) článku.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat Cassandra propojené služby, nastavte **typ** propojené služby pro **OnPremisesCassandra**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| hostitel |Jeden nebo více IP adres nebo názvů hostitelů Cassandra serverů.<br/><br/>Zadejte seznam IP adres nebo názvů hostitelů se připojit na všechny servery současně. |Ano |
| port |Port TCP, který používá Cassandra server naslouchat pro připojení klientů. |Ne, výchozí hodnota: 9042 |
| authenticationType. |Basic nebo Anonymous |Ano |
| uživatelské jméno |Zadejte uživatelské jméno pro uživatelský účet. |Ano, pokud authenticationType je nastaven na Basic. |
| heslo |Zadejte heslo pro uživatelský účet. |Ano, pokud authenticationType je nastaven na Basic. |
| gatewayName |Název brány, která se používá pro připojení k místní databázi Cassandra. |Ano |
| encryptedCredential |Přihlašovací údaje zašifrovaná pomocí brány. |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Další informace najdete v tématu [Cassandra konektor](data-factory-onprem-cassandra-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat Cassandra datovou sadu, nastavte **typ** datové sady, která **CassandraTable**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| keyspace |Název keyspace nebo schéma v Cassandra databáze. |Ano (Pokud **dotazu** pro **CassandraSource** není definován). |
| tableName |Název tabulky v databázi Cassandra. |Ano (Pokud **dotazu** pro **CassandraSource** není definován). |

#### <a name="example"></a>Příklad:

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Další informace najdete v tématu [Cassandra konektor](data-factory-onprem-cassandra-connector.md#dataset-properties) článku. 

### <a name="cassandra-source-in-copy-activity"></a>Zdroj Cassandra v aktivitě kopírování
Pokud jsou kopírování dat z Cassandra, nastavte **typ zdroje** kopie aktivity na **CassandraSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Dotaz SQL 92 nebo CQL dotazu. V tématu [CQL odkaz](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Při použití příkazu jazyka SQL, zadejte **keyspace name.table název** představují tabulky, které mají být zobrazeny. |Ne (pokud jsou definovány tableName a keyspace v sadě dat). |
| consistencyLevel |Úroveň konzistence Určuje, kolik repliky musí odpovědět na požadavek čtení před vrácením dat do klientské aplikace. Cassandra ověří zadaný počet replik pro data, aby pokryl požadavek na čtení. |JEDEN, DVA, TŘI, KVORA, VŠE, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. V tématu [konfigurace konzistenci dat](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) podrobnosti. |Ne. Výchozí hodnota je 1. |

#### <a name="example"></a>Příklad:
  
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace najdete v tématu [Cassandra konektor](data-factory-onprem-cassandra-connector.md#copy-activity-properties) článku.

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Propojená služba
K definování MongoDB propojené služby, nastavte **typ** propojené služby pro **OnPremisesMongoDB**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |IP adresa nebo název hostitele serveru MongoDB. |Ano |
| port |Port TCP, který používá MongoDB server naslouchat pro připojení klientů. |Volitelné, výchozí hodnota: 27017 |
| authenticationType. |Základní, nebo anonymní. |Ano |
| uživatelské jméno |Uživatelský účet pro přístup k MongoDB. |Ano (Pokud se používá základní ověřování). |
| heslo |Heslo pro uživatele. |Ano (Pokud se používá základní ověřování). |
| authSource |Název databáze MongoDB, kterou chcete použít ke kontrole přihlašovacích údajů pro ověřování. |Volitelný parametr (Pokud se používá základní ověřování). Výchozí: používá účet správce a do databáze určené pomocí vlastnost databaseName. |
| databaseName |Název databáze MongoDB, kterou chcete získat přístup. |Ano |
| gatewayName |Název brány, který přistupuje k úložišti. |Ano |
| encryptedCredential |Přihlašovací údaje zašifrovaná pomocí brány. |Nepovinné |

#### <a name="example"></a>Příklad:

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Další informace najdete v tématu [článku konektor MongoDB](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu s MongoDB, nastavte **typ** datové sady, která **MongoDbCollection**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| Název_kolekce |Název kolekce v databázi MongoDB. |Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Další informace najdete v tématu [článku konektor MongoDB](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>Zdroj MongoDB v aktivitě kopírování
Pokud jsou kopírování dat z MongoDB, nastavte **typ zdroje** kopie aktivity na **MongoDbSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL 92. Například: `select * from MyTable`. |Ne (Pokud **Název_kolekce** z **datovou sadu** je zadána) |

#### <a name="example"></a>Příklad:

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Další informace najdete v tématu [článku konektor MongoDB](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Propojená služba
K definování Amazon S3 propojené služby, nastavte **typ** propojené služby pro **AwsAccessKey**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| accessKeyID |ID tajný přístupový klíč. |řetězec |Ano |
| secretAccessKey |Tajný přístupový klíč sám sebe. |Šifrované tajné řetězec |Ano |

#### <a name="example"></a>Příklad:
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Další informace najdete v tématu [Amazon S3 konektor článku](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby Amazon S3, nastavte **typ** datové sady, která **AmazonS3**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| bucketName |Název sady S3. |Řetězec |Ano |
| key |Klíč objektu S3. |Řetězec |Ne |
| Předpona |Předpona pro klíč objektu S3. Jsou vybrané objekty, jejichž klíče začít s touto předponou. Platí pouze v případě, klíč je prázdný. |Řetězec |Ne |
| verze |Verze objektu S3, pokud je povolena Správa verzí S3. |Řetězec |Ne |
| Formát | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady. |Ne | |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne | |


> [!NOTE]
> bucketName + klíče určuje umístění objektu S3, kde blok je kořenový kontejner pro objekty S3 a klíč je úplná cesta k objektu S3.

#### <a name="example-sample-dataset-with-prefix"></a>Příklad: Ukázkovou datovou sadu s předponou

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Příklad: Ukázka datové sady (verze)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Příklad: Dynamické cesty pro S3
V ukázce používáme pevné hodnoty pro klíče a bucketName vlastnosti v datové sadě Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Můžete mít vypočítat klíč a bucketName dynamicky za běhu pomocí systémové proměnné, jako je například SliceStart služby Data Factory.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Můžete provést stejný pro vlastnost předponu datové sadě služby Amazon S3. V tématu [funkce pro vytváření dat a systémové proměnné](data-factory-functions-variables.md) seznam podporované funkce a proměnné.

Další informace najdete v tématu [Amazon S3 konektor článku](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete data z Amazonu S3, nastavte **typ zdroje** kopie aktivity na **FileSystemSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, jestli k rekurzivnímu seznamu S3 objekty v adresáři. |hodnotu true nebo false |Ne |


#### <a name="example"></a>Příklad:


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Další informace najdete v tématu [Amazon S3 konektor článku](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Systém souborů


### <a name="linked-service"></a>Propojená služba
Systém souborů na místě můžete propojit s objektem pro vytváření dat Azure s **místní souborový Server** propojené služby. Následující tabulka obsahuje popis elementy JSON, které jsou specifické pro službu propojené místní souborový Server.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Ujistěte se, že je vlastnost Typ nastavena **OnPremisesFileServer**. |Ano |
| hostitel |Určuje cestu kořenové složky, kterou chcete zkopírovat. Použít řídicí znak ' \ ' pro speciální znaky v řetězci. V tématu [ukázka propojené definice služby a datovou sadu](#sample-linked-service-and-dataset-definitions) příklady. |Ano |
| ID uživatele |Zadejte ID uživatele, který má přístup k serveru. |Ne (když zvolíte encryptedCredential) |
| heslo |Zadejte heslo pro uživatele (ID uživatele). |Ne (když zvolíte encryptedCredential |
| encryptedCredential |Zadejte zašifrované přihlašovací údaje, které můžete získat spuštěním rutiny New-AzureRmDataFactoryEncryptValue. |Ne (když zvolíte možnost zadat ID uživatele a heslo ve formátu prostého textu) |
| gatewayName |Určuje název brány, kterou Data Factory měla použít pro připojení k souborovému serveru místně. |Ano |

#### <a name="sample-folder-path-definitions"></a>Ukázka složky cesta definice 
| Scénář | Hostování v definici propojené služby | folderPath v definici datové sady |
| --- | --- | --- |
| Místní složky v počítači brány pro správu dat: <br/><br/>Příklady: D:\\ \* nebo D:\folder\subfolder\\* |D:\\ \\ (pro Data Management Gateway 2.0 nebo novější) <br/><br/> localhost (pro starší verze než Data Management Gateway 2.0) |. \\ \\ nebo složky\\\\podsložky (pro Data Management Gateway 2.0 nebo novější) <br/><br/>D:\\ \\ nebo D:\\\\složky\\\\podsložky (pro brány verzi nižší než 2.0) |
| Vzdálené sdílené složce: <br/><br/>Příklady: \\ \\myserver\\sdílet\\ \* nebo \\ \\myserver\\sdílet\\složky\\podsložky\\* |\\\\\\\\myserver\\\\sdílet |. \\ \\ nebo složky\\\\podsložky |


#### <a name="example-using-username-and-password-in-plain-text"></a>Příklad: Pomocí uživatelského jména a hesla ve formátu prostého textu

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Příklad: Pomocí encryptedcredential

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Další informace najdete v tématu [článku konektoru systému souborů](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu systému souborů, nastavte **typ** datové sady, která **sdílení souborů**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Určuje dílčí cestou ke složce. Použít řídicí znak ' \' pro speciální znaky v řetězci. V tématu [ukázka propojené definice služby a datovou sadu](#sample-linked-service-and-dataset-definitions) příklady.<br/><br/>Tato vlastnost se můžete kombinovat **partitionBy** tak, aby měl složky cesty založené na řez počáteční nebo koncové hodnoty data a času. |Ano |
| fileName |Zadejte název souboru do **folderPath** Pokud chcete, aby v tabulce odkazovat na konkrétní soubor ve složce. Pokud nezadáte žádnou hodnotu pro tuto vlastnost, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud není zadán název souboru pro datovou sadu výstupů, název vygenerovaný soubor je v následujícím formátu: <br/><br/>`Data.<Guid>.txt` (Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Ne |
| fileFilter |Zadejte filtr pro umožňuje vybrat podmnožinu souborů v folderPath, nikoli všech souborů. <br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklad 1: "fileFilter": "* .log"<br/>Příklad 2: "fileFilter": 2016 - 1-?. TXT"<br/><br/>Všimněte si, že fileFilter je použít pro datové sadě služby vstupní sdílení souborů. |Ne |
| partitionedBy |PartitionedBy můžete použít k určení dynamické folderPath nebo název souboru pro data časové řady. Příkladem je folderPath parametry pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**; a jsou podporované úrovně: **Optimal** a **nejrychlejší**. v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

> [!NOTE]
> Název souboru a fileFilter nelze současně použít.

#### <a name="example"></a>Příklad:

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Další informace najdete v tématu [článku konektoru systému souborů](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud jsou kopírování dat systému souborů, nastavte **typ zdroje** kopie aktivity na **FileSystemSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Označuje, zda je data načíst rekurzivně z podsložky nebo pouze do zadané složky. |Hodnota TRUE, False (výchozí) |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Další informace najdete v tématu [článku konektoru systému souborů](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Systém souborů jímky v aktivitě kopírování
Pokud data kopírujete do systému souborů, nastavte **typ jímky** kopie aktivity na **FileSystemSink**a zadejte následující vlastnosti v **podřízený** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopie, pokud je zdroj BlobSource nebo systému souborů. |**PreserveHierarchy:** zachovává hierarchii souborů v cílové složce. To znamená relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cestu k souboru cíl k cílové složce.<br/><br/>**FlattenHierarchy:** všechny soubory ze zdrojové složky jsou vytvořené v první úroveň cílové složce. Cílové soubory jsou vytvořeny pomocí názvu objektu generován automaticky.<br/><br/>**MergeFiles:** slučuje všechny soubory ze zdrojové složky pro jeden soubor. Pokud je zadán název nebo objekt blob název souboru, název souboru sloučené je zadaný název. Jinak je název automaticky generovaný soubor. |Ne |
Auto-

#### <a name="example"></a>Příklad:

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace najdete v tématu [článku konektoru systému souborů](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Propojená služba
K definování k serveru FTP propojené služby, nastavte **typ** propojené služby pro **Server_ftp**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| hostitel |Název nebo IP adresa serveru FTP |Ano |&nbsp; |
| authenticationType. |Zadání typu ověřování |Ano |Anonymní, základní |
| uživatelské jméno |Uživatel, který má přístup k serveru FTP |Ne |&nbsp; |
| heslo |Heslo pro uživatele (username) |Ne |&nbsp; |
| encryptedCredential |Šifrovaný přihlašovací údaje pro přístup k serveru FTP |Ne |&nbsp; |
| gatewayName |Název brány pro správu dat pro připojení k serveru FTP na místě |Ne |&nbsp; |
| port |Port, na kterém naslouchá FTP server |Ne |21 |
| enableSsl |Určete, zda chcete pomocí funkce FTP přes kanál SSL/TLS. |Ne |true (pravda) |
| enableServerCertificateValidation |Určete, zda chcete povolit ověřování certifikátu protokolu SSL serveru, pokud používáte FTP přes kanál SSL/TLS. |Ne |true (pravda) |

#### <a name="example-using-anonymous-authentication"></a>Příklad: Pomocí anonymní ověřování

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Příklad: Pomocí uživatelského jména a hesla ve formátu prostého textu pro základní ověřování

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Příklad: Pomocí portu, enableSsl, enableServerCertificateValidation

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Příklad: Pomocí encryptedCredential pro ověřování a brány

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Další informace najdete v tématu [konektor FTP](data-factory-ftp-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby FTP, nastavte **typ** datové sady, která **sdílení souborů**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Sub – cesta ke složce. Použít řídicí znak ' \ ' pro speciální znaky v řetězci. V tématu [ukázka propojené definice služby a datovou sadu](#sample-linked-service-and-dataset-definitions) příklady.<br/><br/>Tato vlastnost se můžete kombinovat **partitionBy** tak, aby měl složky cesty založené na řez počáteční nebo koncové hodnoty data a času. |Ano 
| fileName |Zadejte název souboru do **folderPath** Pokud chcete, aby v tabulce odkazovat na konkrétní soubor ve složce. Pokud nezadáte žádnou hodnotu pro tuto vlastnost, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud není zadán název souboru pro datovou sadu výstupů, název vygenerovaný soubor bude v následujícím tento formát: <br/><br/>Data. <Guid>.txt (například: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| fileFilter |Zadejte filtr pro umožňuje vybrat podmnožinu souborů v folderPath, nikoli všech souborů.<br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklady 1: `"fileFilter": "*.log"`<br/>Příklad 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter se vztahuje vstupní datové sady sdílení souborů. Tato vlastnost není podporována s HDFS. |Ne |
| partitionedBy |partitionedBy slouží k určení dynamické folderPath, název souboru pro data časové řady. Například folderPath parametry pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**; a jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |
| useBinaryTransfer |Určit, jestli použít režim binární přenosu. Platí pro binárního režimu a false ASCII. Výchozí hodnota: True. Tuto vlastnost lze použít pouze v případě typu přidružené propojené služby typu: Server_ftp. |Ne |

> [!NOTE]
> Název souboru a fileFilter nelze použít současně.

#### <a name="example"></a>Příklad:

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Další informace najdete v tématu [konektor FTP](data-factory-ftp-connector.md#dataset-properties) článku.

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete data ze serveru FTP, nastavte **typ zdroje** kopie aktivity na **FileSystemSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Označuje, zda je data načíst rekurzivně z dílčí složky nebo pouze do zadané složky. |Hodnota TRUE, False (výchozí) |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Další informace najdete v tématu [konektor FTP](data-factory-ftp-connector.md#copy-activity-properties) článku.


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Propojená služba
K definování HDFS propojené služby, nastavte **typ** propojené služby pro **Hdfs**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **Hdfs** |Ano |
| URL |Adresa URL HDFS |Ano |
| authenticationType. |Anonymní, nebo Windows. <br><br> Použít **ověřování protokolem Kerberos** HDFS konektor, najdete v části [v této části](#use-kerberos-authentication-for-hdfs-connector) odpovídajícím způsobem nastavit v místním prostředí. |Ano |
| uživatelské jméno |Ověřování uživatelského jména pro systém Windows. |Ano (pro ověřování systému Windows) |
| heslo |Heslo pro ověřování systému Windows. |Ano (pro ověřování systému Windows) |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k HDFS. |Ano |
| encryptedCredential |[Nové AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) výstup pověření přístup. |Ne |

#### <a name="example-using-anonymous-authentication"></a>Příklad: Pomocí anonymní ověřování

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Příklad: Pomocí ověřování systému Windows

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Další informace najdete v tématu [HDFS konektor](#data-factory-hdfs-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat HDFS datovou sadu, nastavte **typ** datové sady, která **sdílení souborů**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Cesta ke složce. Příklad: `myfolder`<br/><br/>Použít řídicí znak ' \ ' pro speciální znaky v řetězci. Příklad: pro folder\subfolder, určete složku\\\\podsložky a pro d:\samplefolder, zadejte d:\\\\ukázková_složka.<br/><br/>Tato vlastnost se můžete kombinovat **partitionBy** tak, aby měl složky cesty založené na řez počáteční nebo koncové hodnoty data a času. |Ano |
| fileName |Zadejte název souboru do **folderPath** Pokud chcete, aby v tabulce odkazovat na konkrétní soubor ve složce. Pokud nezadáte žádnou hodnotu pro tuto vlastnost, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud není zadán název souboru pro datovou sadu výstupů, název vygenerovaný soubor bude v následujícím tento formát: <br/><br/>Data. <Guid>.txt (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy slouží k určení dynamické folderPath, název souboru pro data časové řady. Příklad: folderPath parametry pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

> [!NOTE]
> Název souboru a fileFilter nelze použít současně.

#### <a name="example"></a>Příklad:

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Další informace najdete v tématu [HDFS konektor](#data-factory-hdfs-connector.md#dataset-properties) článku. 

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete data z HDFS, nastavte **typ zdroje** kopie aktivity na **FileSystemSource**a zadejte následující vlastnosti v **zdroj** části:

**FileSystemSource** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Označuje, zda je data načíst rekurzivně z dílčí složky nebo pouze do zadané složky. |Hodnota TRUE, False (výchozí) |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Další informace najdete v tématu [HDFS konektor](#data-factory-hdfs-connector.md#copy-activity-properties) článku.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Propojená služba
K definování protokolu SFTP propojené služby, nastavte **typ** propojené služby pro **Sftp**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- | --- |
| hostitel | Název nebo IP adresa serveru pomocí protokolu SFTP. |Ano |
| port |Port, na kterém naslouchá server pomocí protokolu SFTP. Výchozí hodnota je: 21 |Ne |
| authenticationType. |Zadejte typ ověřování. Povolené hodnoty: **základní**, **parametru SshPublicKey**. <br><br> Odkazovat na [základní ověřování pomocí](#using-basic-authentication) a [pomocí SSH ověření veřejného klíče](#using-ssh-public-key-authentication) částech na další vlastnosti a ukázky JSON v uvedeném pořadí. |Ano |
| skipHostKeyValidation | Určete, zda chcete přeskočit ověření klíče hostitele. | Ne. Výchozí hodnota: false |
| hostKeyFingerprint | Zadejte prstu klíče hostitele. | Ano, pokud `skipHostKeyValidation` nastaven na hodnotu false.  |
| gatewayName |Název brány pro správu dat pro připojení k serveru pomocí protokolu SFTP místně. | Ano, pokud kopírování dat z místního serveru pomocí protokolu SFTP. |
| encryptedCredential | Šifrovaný přihlašovací údaje pro přístup k serveru pomocí protokolu SFTP. Automaticky generovaný když zadáte v Průvodci kopírovat nebo dialogové okno místní ClickOnce základní ověřování (uživatelské jméno a heslo) nebo ověřování parametru SshPublicKey (uživatelské jméno + cesta privátního klíče nebo obsah). | Ne. Platí jenom v případě, že kopírování dat z místního serveru pomocí protokolu SFTP. |

#### <a name="example-using-basic-authentication"></a>Příklad: Použití základního ověřování

Chcete-li základní ověřování použijte, nastavte `authenticationType` jako `Basic`a zadejte následující vlastnosti kromě konektor SFTP obecné ty, které jsou zavedené v poslední části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- | --- |
| uživatelské jméno | Uživatel, který má přístup k serveru pomocí protokolu SFTP. |Ano |
| heslo | Heslo pro uživatele (uživatelské jméno). | Ano |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Příklad: **základní ověřování s šifrovaném přihlašovacím údaji**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**Pomocí ověření veřejného klíče SSH:**

Chcete-li základní ověřování použijte, nastavte `authenticationType` jako `SshPublicKey`a zadejte následující vlastnosti kromě konektor SFTP obecné ty, které jsou zavedené v poslední části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- | --- |
| uživatelské jméno |Uživatel, který má přístup k serveru pomocí protokolu SFTP |Ano |
| privateKeyPath | Zadejte absolutní cestu k souboru privátního klíče můžete přístup k této brány. | Zadejte buď `privateKeyPath` nebo `privateKeyContent`. <br><br> Platí jenom v případě, že kopírování dat z místního serveru pomocí protokolu SFTP. |
| privateKeyContent | Serializovaná řetězec privátní klíče obsahu. Průvodce kopírováním můžete číst soubor privátního klíče a automaticky extrahování privátní klíče obsahu. Pokud používáte jakékoli jiné nástroje nebo SDK, použijte vlastnost privateKeyPath. | Zadejte buď `privateKeyPath` nebo `privateKeyContent`. |
| přístupové heslo | Zadejte průchodu fráze nebo hesla k dešifrování privátního klíče, pokud soubor klíče je chráněn heslo. | Ano, pokud heslo je chráněný soubor privátního klíče. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Příklad: **parametru SshPublicKey ověřování pomocí privátního klíče obsahu**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Další informace najdete v tématu [SFTP konektor](data-factory-sftp-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby pomocí protokolu SFTP, nastavte **typ** datové sady, která **sdílení souborů**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Sub – cesta ke složce. Použít řídicí znak ' \ ' pro speciální znaky v řetězci. V tématu [ukázka propojené definice služby a datovou sadu](#sample-linked-service-and-dataset-definitions) příklady.<br/><br/>Tato vlastnost se můžete kombinovat **partitionBy** tak, aby měl složky cesty založené na řez počáteční nebo koncové hodnoty data a času. |Ano |
| fileName |Zadejte název souboru do **folderPath** Pokud chcete, aby v tabulce odkazovat na konkrétní soubor ve složce. Pokud nezadáte žádnou hodnotu pro tuto vlastnost, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud není zadán název souboru pro datovou sadu výstupů, název vygenerovaný soubor bude v následujícím tento formát: <br/><br/>Data. <Guid>.txt (například: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| fileFilter |Zadejte filtr pro umožňuje vybrat podmnožinu souborů v folderPath, nikoli všech souborů.<br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklady 1: `"fileFilter": "*.log"`<br/>Příklad 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter se vztahuje vstupní datové sady sdílení souborů. Tato vlastnost není podporována s HDFS. |Ne |
| partitionedBy |partitionedBy slouží k určení dynamické folderPath, název souboru pro data časové řady. Například folderPath parametry pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |
| useBinaryTransfer |Určit, jestli použít režim binární přenosu. Platí pro binárního režimu a false ASCII. Výchozí hodnota: True. Tuto vlastnost lze použít pouze v případě typu přidružené propojené služby typu: Server_ftp. |Ne |

> [!NOTE]
> Název souboru a fileFilter nelze použít současně.

#### <a name="example"></a>Příklad:

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Další informace najdete v tématu [SFTP konektor](data-factory-sftp-connector.md#dataset-properties) článku. 

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete z protokolu SFTP zdroje dat, nastavte **typ zdroje** kopie aktivity na **FileSystemSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Označuje, zda je data načíst rekurzivně z dílčí složky nebo pouze do zadané složky. |Hodnota TRUE, False (výchozí) |Ne |



#### <a name="example"></a>Příklad:

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Další informace najdete v tématu [SFTP konektor](data-factory-sftp-connector.md#copy-activity-properties) článku.


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Propojená služba
K definování HTTP propojené služby, nastavte **typ** propojené služby pro **Http**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| url | Základní adresu URL na webový server | Ano |
| authenticationType. | Určuje typ ověřování. Povolené hodnoty jsou: **anonymní**, **základní**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Naleznete v části dál v této tabulce na další vlastnosti a ukázky JSON pro tyto typy ověřování v uvedeném pořadí. | Ano |
| enableServerCertificateValidation | Určete, zda chcete povolit ověřování certifikátu protokolu SSL serveru, pokud je zdroj HTTPS webového serveru | Ne, výchozí hodnota je true |
| gatewayName | Název brány pro správu dat pro připojení k místnímu zdroji HTTP. | Ano, pokud kopírování dat z místního zdroje HTTP. |
| encryptedCredential | Šifrovaný přihlašovací údaje pro přístup k koncový bod HTTP. Automaticky vygenerované při konfiguraci informace o ověřování v Průvodce kopírováním nebo dialogové okno místní ClickOnce. | Ne. Platí jenom v případě, že kopírování dat z místního serveru HTTP. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Příklad: Použití ověřování Basic, ověřování algoritmem Digest nebo systému Windows
Nastavit `authenticationType` jako `Basic`, `Digest`, nebo `Windows`a zadejte následující vlastnosti kromě konektor HTTP obecné ty, které jsou zavedené výše:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| uživatelské jméno | Uživatelské jméno pro přístup k koncový bod HTTP. | Ano |
| heslo | Heslo pro uživatele (uživatelské jméno). | Ano |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Příklad: Použití ověřování ClientCertificate

Chcete-li základní ověřování použijte, nastavte `authenticationType` jako `ClientCertificate`a zadejte následující vlastnosti kromě konektor HTTP obecné ty, které jsou zavedené výše:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| embeddedCertData | Obsah s kódováním base64, pomocí binárních dat soubor Personal Information Exchange (PFX). | Zadejte buď `embeddedCertData` nebo `certThumbprint`. |
| certThumbprint | Kryptografický otisk certifikátu, který byl nainstalován v úložišti certifikátů počítače brány. Platí jenom v případě, že kopírování dat z místního zdroje HTTP. | Zadejte buď `embeddedCertData` nebo `certThumbprint`. |
| heslo | Heslo přidružené k certifikátu. | Ne |

Pokud používáte `certThumbprint` pro ověřování a certifikát nainstalovaný v osobním úložišti místního počítače, musí udělit oprávnění ke čtení ke službě brány:

1. Spusťte konzolu Microsoft Management Console (MMC). Přidat **certifikáty** modul snap-in zacílený **místního počítače**.
2. Rozbalte položku **certifikáty**, **osobní**a klikněte na tlačítko **certifikáty**.
3. Klikněte pravým tlačítkem na certifikát z osobního úložiště a vyberte **všechny úlohy**->**spravovat privátní klíče...**
3. Na **zabezpečení** přidejte uživatelský účet, pod kterou je spuštěna hostitelská služba brány správy dat s přístupem pro čtení k certifikátu.  

**Příklad: pomocí klientského certifikátu:** Tato propojená služba propojuje datovou továrnu místní webový server HTTP. Používá klientský certifikát, který je nainstalován na počítači s brána správy dat nainstalována.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Příklad: pomocí klientského certifikátu do souboru
Tato propojená služba propojuje datovou továrnu místní webový server HTTP. Používá soubor certifikátu klienta na počítači s brána správy dat nainstalována.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Další informace najdete v tématu [HTTP konektor](data-factory-http-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu protokolu HTTP, nastavte **typ** datové sady, která **Http**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data. Pokud cesta není zadána, je použít jenom adresu URL, zadaný v definici propojené služby. <br><br> Chcete-li vytvořit dynamické adresy URL, můžete použít [funkce pro vytváření dat a systémové proměnné](data-factory-functions-variables.md), například: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Ne |
| requestMethod | Metoda HTTP. Povolené hodnoty jsou **získat** nebo **POST**. | Ne. Výchozí hodnota je `GET`. |
| additionalHeaders | Další hlavičky žádosti HTTP. | Ne |
| requestBody | Text pro požadavek HTTP. | Ne |
| Formát | Pokud chcete jednoduše **načtou data z koncový bod HTTP jako-je** bez analýza ho, přeskočte tento formát nastavení. <br><br> Pokud chcete analyzovat během kopírování obsahu odpovědi HTTP, jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

#### <a name="example-using-the-get-default-method"></a>Příklad: použití metody GET (výchozí)

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Příklad: pomocí metody POST

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Další informace najdete v tématu [HTTP konektor](data-factory-http-connector.md#dataset-properties) článku.

### <a name="http-source-in-copy-activity"></a>Zdroj protokolu HTTP v aktivitě kopírování
Pokud kopírujete data ze zdroje HTTP, nastavte **typ zdroje** kopie aktivity na **HttpSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| httpRequestTimeout | Časový limit (TimeSpan) pro získání odezvy požadavku HTTP. Získání odezvy, není časový limit číst data odpovědi je časový limit. | Ne. Výchozí hodnota: 00:01:40 |


#### <a name="example"></a>Příklad:

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace najdete v tématu [HTTP konektor](data-factory-http-connector.md#copy-activity-properties) článku.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Propojená služba
K definování OData propojené služby, nastavte **typ** propojené služby pro **OData**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| url |Adresa URL služby OData. |Ano |
| authenticationType. |Typ ověřování používaný pro připojení ke zdroji OData. <br/><br/> Možné hodnoty pro cloudové prostředí OData, jsou anonymní, základní a OAuth (Upozorňujeme, že Azure Active Directory na základě OAuth aktuálně jedinou podpory Azure Data Factory). <br/><br/> Pro místní OData možné hodnoty jsou anonymní, Basic a Windows. |Ano |
| uživatelské jméno |Pokud používáte základní ověřování, zadejte uživatelské jméno. |Ano (jenom Pokud používáte základní ověřování) |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ano (jenom Pokud používáte základní ověřování) |
| authorizedCredential |Pokud používáte OAuth, klikněte na tlačítko **Autorizovat** tlačítko Průvodce kopírováním služby Data Factory nebo editoru a zadejte svoje přihlašovací údaje, pak bude automaticky generovaný hodnota této vlastnosti. |Ano (jenom Pokud používáte ověřování OAuth) |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení ke službě OData na místě. Zadejte, pokud jsou kopírování dat z místní zdroj OData. |Ne |

#### <a name="example---using-basic-authentication"></a>Příklad: použití základního ověřování
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Příklad: pomocí anonymní ověřování

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Příklad: přístup k ověřování pomocí Windows místní zdroj OData

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Příklad - použití ověřování OAuth přístup ke cloudu zdroj OData
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Další informace najdete v tématu [OData konektor](data-factory-odata-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby OData, nastavte **typ** datové sady, která **ODataResource**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| path |Cesta k prostředku OData |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Další informace najdete v tématu [OData konektor](data-factory-odata-connector.md#dataset-properties) článku.

### <a name="relational-source-in-copy-activity"></a>Relačního zdroje v aktivitě kopírování
Pokud kopírujete z OData zdroje dat, nastavte **typ zdroje** kopie aktivity na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Příklad: | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |"? $select = název, popis a $top = 5" |Ne |

#### <a name="example"></a>Příklad:

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Další informace najdete v tématu [OData konektor](data-factory-odata-connector.md#copy-activity-properties) článku.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Propojená služba
K definování ODBC propojené služby, nastavte **typ** propojené služby pro **OnPremisesOdbc**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| připojovací řetězec |Přístup k pověření část připojovací řetězec a volitelné šifrovat přihlašovací údaje. Příklady v následujících částech. |Ano |
| pověření |Část přístup přihlašovacích údajů z připojovacího řetězce zadaného ve formátu ovladačem vlastnost hodnota. Příklad: "Uid =<user ID>; PWD =<password>; RefreshToken =<secret refresh token>; ". |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k úložišti dat ODBC. Možné hodnoty jsou: anonymní a Basic. |Ano |
| uživatelské jméno |Pokud používáte základní ověřování, zadejte uživatelské jméno. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k úložišti dat ODBC. |Ano |

#### <a name="example---using-basic-authentication"></a>Příklad: použití základního ověřování

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Příklad: základní ověřování pomocí zašifrované přihlašovací údaje
Můžete šifrovat přihlašovací údaje pomocí [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) rutiny (1.0 verzi prostředí Azure PowerShell) nebo [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9 nebo starší verzi Azure Prostředí PowerShell).  

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Příklad: Pomocí anonymní ověřování

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Další informace najdete v tématu [ODBC konektor](data-factory-odbc-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datové sadě služby ODBC, nastavte **typ** datové sady, která **RelationalTable**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v úložišti dat ODBC. |Ano |


#### <a name="example"></a>Příklad:

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Další informace najdete v tématu [ODBC konektor](data-factory-odbc-connector.md#dataset-properties) článku. 

### <a name="relational-source-in-copy-activity"></a>Relačního zdroje v aktivitě kopírování
Pokud jsou kopírování dat z úložiště dat rozhraní ODBC, nastavte **typ zdroje** kopie aktivity na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části :

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
``` 

Další informace najdete v tématu [ODBC konektor](data-factory-odbc-connector.md#copy-activity-properties) článku.

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Propojená služba
K definování Salesforce propojené služby, nastavte **typ** propojené služby pro **Salesforce**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| environmentUrl | Zadejte adresu URL služby Salesforce instanci. <br><br> -Výchozí hodnota je "https://login.salesforce.com". <br> -Ke zkopírování dat z izolovaného prostoru, zadejte "https://test.salesforce.com". <br> -Ke zkopírování dat z vlastní domény, zadejte, například "https://[domain].my.salesforce.com". |Ne |
| uživatelské jméno |Zadejte uživatelské jméno pro uživatelský účet. |Ano |
| heslo |Zadejte heslo pro uživatelský účet. |Ano |
| securityToken |Zadejte token zabezpečení pro uživatelský účet. V tématu [získal token zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) pokyny o tom, jak resetování nebo získat token zabezpečení. Obecné informace o tokeny zabezpečení najdete v tématu [zabezpečení a rozhraní API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Další informace najdete v tématu [konektor služby Salesforce](data-factory-salesforce-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datová sada služby Salesforce, nastavte **typ** datové sady, která **RelationalTable**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v Salesforce. |Ne (Pokud **dotazu** z **RelationalSource** je zadána) |

#### <a name="example"></a>Příklad:

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Další informace najdete v tématu [konektor služby Salesforce](data-factory-salesforce-connector.md#dataset-properties) článku. 

### <a name="relational-source-in-copy-activity"></a>Relačního zdroje v aktivitě kopírování
Pokud kopírujete data ze služby Salesforce, nastavte **typ zdroje** kopie aktivity na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Dotaz SQL 92 nebo [Salesforce objektu dotazu jazyka (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) dotazu. Například `select * from MyTable__c`. |Ne (Pokud **tableName** z **datovou sadu** je zadána) |

#### <a name="example"></a>Příklad:  



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> Část "__c" název rozhraní API je potřeba pro všechny vlastní objekt.

Další informace najdete v tématu [konektor služby Salesforce](data-factory-salesforce-connector.md#copy-activity-properties) článku. 

## <a name="web-data"></a>Data webové 

### <a name="linked-service"></a>Propojená služba
K definování webové propojené služby, nastavte **typ** propojené služby pro **webové**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| URL |Adresa URL pro webové zdroje |Ano |
| authenticationType. |Anonymní. |Ano |
 

#### <a name="example"></a>Příklad:


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Další informace najdete v tématu [webové tabulce konektor](data-factory-web-table-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat webové datovou sadu, nastavte **typ** datové sady, která **WebTable**a zadejte následující vlastnosti v **rámci typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Typ datové sady. musí být nastavena na **WebTable** |Ano |
| path |Relativní adresa URL prostředek, který obsahuje tabulku. |Ne. Pokud cesta není zadána, je použít jenom adresu URL, zadaný v definici propojené služby. |
| index |Index tabulky v prostředku. V tématu [Get index tabulky v stránku HTML](#get-index-of-a-table-in-an-html-page) části Postup získání index tabulky v stránku HTML. |Ano |

#### <a name="example"></a>Příklad:

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Další informace najdete v tématu [webové tabulce konektor](data-factory-web-table-connector.md#dataset-properties) článku. 

### <a name="web-source-in-copy-activity"></a>Webové zdroje v aktivitě kopírování
Pokud jsou kopírování dat z tabulky webové, nastavte **typ zdroje** kopie aktivity na **WebSource**. V současné době po zdroji v aktivitě kopírování typu **WebSource**, jsou podporovány žádné další vlastnosti.

#### <a name="example"></a>Příklad:

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Další informace najdete v tématu [webové tabulce konektor](data-factory-web-table-connector.md#copy-activity-properties) článku. 

## <a name="compute-environments"></a>VÝPOČETNÍ PROSTŘEDÍ
Následující tabulka uvádí výpočetních prostředích nepodporuje objekt pro vytváření dat a aktivit transformace, které můžou běžet na ně. Kliknutím na odkaz pro výpočty, které vás zajímají zobrazíte schémata JSON pro propojenou službu propojení s služby data factory. 

| Výpočetní prostředí | Aktivity |
| --- | --- |
| [Cluster HDInsight na vyžádání](#on-demand-azure-hdinsight-cluster) nebo [vlastní cluster HDInsight](#existing-azure-hdinsight-cluster) |[Vlastní aktivity .NET](#net-custom-activity), [aktivitu Hivu](#hdinsight-hive-activity), [vepřových aktivity] (#-pig – aktivita hdinsight, [činnost MapReduce](#hdinsight-mapreduce-activity), [streamování aktivity Hadoop](#hdinsight-streaming-activityd), [ Spark aktivity](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[Vlastní aktivita .NET](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Strojového učení aktivita provedení dávky](#machine-learning-batch-execution-activity), [strojového učení aktivita prostředku aktualizace](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[U-SQL Data Lake Analytics](#data-lake-analytics-u-sql-activity) |
| [Databáze Azure SQL](#azure-sql-database-1), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-1), [systému SQL Server](#sql-server-1) |[Uložená procedura](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Cluster Azure HDInsight na vyžádání
Služba Azure Data Factory můžete automaticky vytvoření clusteru HDInsight se systémem Windows nebo Linux na vyžádání pro zpracování dat. Cluster se vytvoří ve stejné oblasti jako účet úložiště (vlastnost linkedServiceName v kódu JSON) přidružen ke clusteru. Můžete spustit následující aktivit transformace na tato propojená služba: [vlastní aktivity .NET](#net-custom-activity), [aktivitu Hivu](#hdinsight-hive-activity), [vepřových aktivity] (#-pig – aktivita hdinsight, [činnost MapReduce ](#hdinsight-mapreduce-activity), [Streamování aktivity Hadoop](#hdinsight-streaming-activityd), [Spark aktivity](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Propojená služba 
Následující tabulka obsahuje popis vlastností použitých v definici Azure JSON HDInsight propojené služby na vyžádání.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavená na **HDInsightOnDemand**. |Ano |
| clusterSize |Počet uzlů pracovního procesu nebo data v clusteru. Vytvoření clusteru HDInsight s 2 hlavních uzlech spolu s počtem uzlů pracovního procesu, který jste zadali pro tuto vlastnost. Uzly jsou velikosti Standard_D3, který má 4 jádra, 4 pracovní uzly clusteru trvá 24 jader (4\*4 = 16 jader pro uzly pracovního procesu, plus 2\*4 = 8 jader pro head uzly). V tématu [vytvořit systémem Linux Hadoop clusterů v HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) podrobnosti o Standard_D3 vrstvy. |Ano |
| TimeToLive |Povolené doby nečinnosti pro cluster HDInsight na vyžádání. Určuje, jak dlouho clusteru HDInsight na vyžádání zůstane aktivní po dokončení činnosti spustit, pokud nejsou žádné aktivní úlohy v clusteru.<br/><br/>Například pokud spuštění aktivity trvá 6 minut a timetolive nastavena na 5 minut, clusteru zůstává aktivní po dobu 5 minut po spuštění 6 minut zpracování aktivity. Pokud se okno 6 minut proveden jiné aktivity při spuštění, je zpracován stejného clusteru.<br/><br/>Vytvoření clusteru HDInsight na vyžádání je náročná operace (může trvat), takže použití tohoto nastavení podle potřeby ke zlepšení výkonu služby data factory pomocí opakovaného použití clusteru HDInsight na vyžádání.<br/><br/>Pokud hodnota timetolive nastavíte na 0, odstranění clusteru hned, jak aktivita běžet v zpracovaná. Na druhé straně, pokud jste nastavili na vysokou hodnotu, může přerušit clusteru nečinnosti zbytečně výsledkem vysoké náklady. Proto je důležité nastavit odpovídající hodnotu na základě potřeb.<br/><br/>Více kanálů můžete sdílet stejnou instanci clusteru HDInsight na vyžádání, pokud je hodnota vlastnosti timetolive správně nastavena |Ano |
| verze |Verze clusteru HDInsight. Podrobnosti najdete v tématu [podporované verze HDInsight v Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Ne |
| linkedServiceName |Propojená služba má být používána clusteru na vyžádání pro ukládání a zpracování dat Azure Storage. <p>V současné době nelze vytvořit cluster HDInsight na vyžádání, který používá jako úložiště Azure Data Lake Store. Pokud chcete uložit výsledek data z HDInsight zpracování v Azure Data Lake Store, pomocí aktivity kopírování zkopírovat data z Azure Blob Storage do Azure Data Lake Store.</p>  | Ano |
| additionalLinkedServiceNames |Určuje, že další účty úložiště pro HDInsight propojená služba tak, aby služba Data Factory můžete zaregistrovat vaším jménem. |Ne |
| osType |Typ operačního systému. Povolené hodnoty jsou: systému Windows (výchozí) a Linux |Ne |
| hcatalogLinkedServiceName |Název Azure SQL propojené služby, které odkazují na databázi HCatalog. Cluster HDInsight na vyžádání je vytvořená pomocí Azure SQL database jako metaúložiště. |Ne |

### <a name="json-example"></a>Příklad JSON
Následující kód JSON určuje základě Linux na vyžádání propojené služby HDInsight. Služba Data Factory automaticky vytvoří **systémem Linux** clusteru HDInsight se při zpracování dat řezu. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Další informace najdete v tématu [výpočetní propojené služby](data-factory-compute-linked-services.md) článku. 

## <a name="existing-azure-hdinsight-cluster"></a>Existující cluster Azure HDInsight
Můžete vytvořit propojené služby Azure HDInsight k registraci vlastní cluster HDInsight s Data Factory. Můžete spustit následující aktivit transformace dat na tato propojená služba: [vlastní aktivity .NET](#net-custom-activity), [aktivitu Hivu](#hdinsight-hive-activity), [vepřových aktivity] (#-pig – aktivita hdinsight, [MapReduce aktivita](#hdinsight-mapreduce-activity), [streamování aktivity Hadoop](#hdinsight-streaming-activityd), [Spark aktivity](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Propojená služba
Následující tabulka obsahuje popis vlastností použitých v definici Azure JSON propojené služby Azure HDInsight.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavená na **HDInsight**. |Ano |
| clusterUri |Identifikátor URI clusteru HDInsight. |Ano |
| uživatelské jméno |Zadejte jméno uživatele, který se má použít pro připojení k existujícímu clusteru HDInsight. |Ano |
| heslo |Zadejte heslo pro uživatelský účet. |Ano |
| linkedServiceName | Název úložiště Azure, propojené služby, která odkazuje na Azure blob storage používaný v clusteru HDInsight. <p>V současné době nelze zadat, že Azure Data Lake Store propojené služby pro tuto vlastnost. Může přistupovat k datům v Azure Data Lake Store z skriptů Hive nebo Pig Pokud HDInsight cluster má přístup do Data Lake Store. </p>  |Ano |

Verzích clusterů HDInsight, které jsou podporovány, naleznete v části [podporované HDInsight verze](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). 

#### <a name="json-example"></a>Příklad JSON

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
Služby Azure Batch propojené můžete zaregistrovat fondu služby Batch virtuálních počítačů (VM) můžete vytvořit pomocí služby data factory. Můžete spustit pomocí Azure Batch nebo Azure HDInsight vlastní aktivity .NET. Můžete spustit [vlastní aktivity .NET](#net-custom-activity) na toto propojenou službu. 

### <a name="linked-service"></a>Propojená služba
Následující tabulka obsahuje popis vlastností použitých v definici Azure JSON služby Azure Batch propojený.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavená na **AzureBatch**. |Ano |
| název účtu |Název účtu Azure Batch. |Ano |
| accessKey |Přístupový klíč pro účet Azure Batch. |Ano |
| poolName |Název fondu virtuálních počítačů. |Ano |
| linkedServiceName |Název úložiště Azure propojená služba přidruženého k této službě Azure Batch propojený. Tato propojená služba se používá pro pracovní soubory potřebné ke spuštění aktivity a ukládání protokoly spuštění aktivity. |Ano |


#### <a name="json-example"></a>Příklad JSON

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning"></a>Azure Machine Learning
Vytváření služby Azure Machine Learning propojené k registraci Machine Learning dávkového vyhodnocování koncový bod s služby data factory. Aktivity transformace dat dvě, které můžou běžet v této propojené službě: [aktivita provedení dávky Machine Learning](#machine-learning-batch-execution-activity), [aktivita prostředku aktualizace Machine Learning](#machine-learning-update-resource-activity). 

### <a name="linked-service"></a>Propojená služba
Následující tabulka obsahuje popis vlastností použitých v definici Azure JSON služby Azure Machine Learning propojený.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| Typ |Vlastnost typu musí být nastavená na: **AzureML**. |Ano |
| mlEndpoint |Adresu URL dávkového vyhodnocování. |Ano |
| apiKey |Rozhraní API modelu publikované prostoru. |Ano |

#### <a name="json-example"></a>Příklad JSON

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Vytvoříte **Azure Data Lake Analytics** propojená služba Azure Data Lake Analytics výpočetní služby s objektem pro vytváření dat Azure před použitím [aktivita Data Lake Analytics U-SQL](data-factory-usql-activity.md) v kanálu.

### <a name="linked-service"></a>Propojená služba

Následující tabulka obsahuje popis vlastností použitých v definici JSON služby Azure Data Lake Analytics propojený. 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| Typ |Vlastnost typu musí být nastavená na: **AzureDataLakeAnalytics**. |Ano |
| název účtu |Název účtu Azure Data Lake Analytics. |Ano |
| dataLakeAnalyticsUri |Identifikátor URI služby Azure Data Lake Analytics. |Ne |
| Autorizace |Autorizační kód se načte automaticky po kliknutí na **Autorizovat** tlačítko v editoru služby Data Factory a dokončí se přihlášení OAuth. |Ano |
| subscriptionId |Id předplatného Azure |Ne (když není určeno, předplatné objektu pro vytváření dat se používá). |
| resourceGroupName |Název skupiny prostředků Azure |Ne (když není určeno, skupinu prostředků objektu pro vytváření dat se používá). |
| ID relace |id relace z autorizační relace OAuth. Každé id relace je jedinečné a může být použit pouze jednou. Při použití editoru služby Data Factory toto ID se generuje automaticky. |Ano |


#### <a name="json-example"></a>Příklad JSON
Následující příklad uvádí definici JSON pro služby Azure Data Lake Analytics propojený.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="azure-sql-database"></a>Azure SQL Database
Vytvoření služby Azure SQL propojené a použít je s [aktivity uložené procedury](#stored-procedure-activity) k vyvolání uložené procedury z objektu pro vytváření dat kanál. 

### <a name="linked-service"></a>Propojená služba
K definování Azure SQL Database propojené služby, nastavte **typ** propojené služby pro **azuresqldatabase**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci databáze SQL Azure pro vlastnost connectionString. |Ano |

#### <a name="json-example"></a>Příklad JSON

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

V tématu [konektor služby Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties) článku podrobnosti o této propojené službě.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Vytvoření služby Azure SQL Data Warehouse propojené a použít je s [aktivity uložené procedury](data-factory-stored-proc-activity.md) k vyvolání uložené procedury z objektu pro vytváření dat kanál. 

### <a name="linked-service"></a>Propojená služba
K definování Azure SQL Data Warehouse propojené služby, nastavte **typ** propojené služby pro **AzureSqlDW**a zadejte následující vlastnosti v **rámci typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k Azure SQL Data Warehouse instance pro vlastnost connectionString. |Ano |

#### <a name="json-example"></a>Příklad JSON

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Další informace najdete v tématu [konektor Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) článku. 

## <a name="sql-server"></a>SQL Server 
Vytvoření služby SQL serveru propojená a použít je s [aktivity uložené procedury](data-factory-stored-proc-activity.md) k vyvolání uložené procedury z objektu pro vytváření dat kanál. 

### <a name="linked-service"></a>Propojená služba
Vytvoření propojené služby typu **onpremisessqlserver** propojit místní databázi systému SQL Server do služby data factory. Následující tabulka obsahuje popis specifické pro službu SQL serveru propojená místní elementy JSON.

Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro SQL Server propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavená na: **onpremisessqlserver**. |Ano |
| připojovací řetězec |Zadejte připojovací řetězec informace potřebné pro připojení k místní databázi systému SQL Server pomocí ověřování SQL nebo ověřování systému Windows. |Ano |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k místní databázi systému SQL Server. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud používáte ověřování systému Windows. Příklad: **domainname\\uživatelské jméno**. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |

Můžete šifrovat přihlašovací údaje pomocí **New-AzureRmDataFactoryEncryptValue** rutiny a jak je znázorněno v následujícím příkladu je využít v připojovacím řetězci (**EncryptedCredential** vlastnost):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Příklad: JSON pro pomocí ověřování SQL.

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Příklad: JSON pro použití ověřování systému Windows

Pokud jsou zadané uživatelské jméno a heslo, brána je používá k zosobnění zadaný uživatelský účet pro připojení k místní databázi systému SQL Server. Jinak brána připojí k systému SQL Server přímo s kontextem zabezpečení brány (jeho účet spuštění).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Další informace najdete v tématu [systému SQL Server konektoru](data-factory-sqlserver-connector.md#linked-service-properties) článku.

## <a name="data-transformation-activities"></a>AKTIVITY TRANSFORMACE DAT

Aktivita | Popis
-------- | -----------
[Aktivitu HDInsight Hive](#hdinsight-hive-activity) | Aktivity HDInsight Hive v kanálu pro vytváření dat provede dotazů Hive sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. 
[Aktivita HDInsight Pig](#hdinsight-pig-activity) | HDInsight Pig aktivity v kanálu pro vytváření dat provede Pig dotazy na vlastní nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání.
[Aktivita MapReduce služby HDInsight](#hdinsight-mapreduce-activity) | Činnost HDInsight MapReduce v objektu pro vytváření dat kanál provede MapReduce programy sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání.
[Aktivita Streamování služby HDInsight](#hdinsight-streaming-activity) | HDInsight streamované aktivitě v objektu pro vytváření dat kanál provede streamování Hadoop programy sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání.
[Aktivita Sparku služby HDInsight](#hdinsight-spark-activity) | Aktivity HDInsight Spark v objektu pro vytváření dat kanál provede na clusteru HDInsight Spark programy. 
[Aktivita Provedení dávky služby Machine Learning](#machine-learning-batch-execution-activity) | Azure Data Factory můžete snadno vytvořit kanály, které používají publikované webové služby Azure Machine Learning pro prediktivní analýzy. Aktivita provedení dávky v kanál služby Azure Data Factory, můžete vyvolat webové služby Machine Learning k provádět předpovědi na datech v dávce. 
[Aktivita Aktualizace prostředků služby Machine Learning](#machine-learning-update-resource-activity) | V průběhu času prediktivní modely v Machine Learning vyhodnocování experimentů muset být retrained pomocí nové vstupní datové sady. Jakmile jste hotovi s přeučení chcete aktualizovat webovou službu vyhodnocování s modelem retrained Machine Learning. Aktivita prostředku aktualizace můžete aktualizovat webovou službu s nově naučeného modelu.
[Aktivita Uložená procedura](#stored-procedure-activity) | Aktivity uložené procedury v kanálu pro vytváření dat můžete použít k vyvolání uložené procedury v jednom z následujících úložišť dat: databáze SQL Azure, Azure SQL Data Warehouse, databáze SQL serveru ve vašem podniku nebo virtuální počítač Azure. 
[Aktivita data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL aktivity spouští skript U-SQL na clusteru služby Azure Data Lake Analytics.  
[Vlastní aktivita .NET](#net-custom-activity) | Pokud potřebujete transformovat data způsobem, který není podporován službou Data Factory, můžete vytvořit vlastní aktivity s logika zpracování dat a použijte aktivitu v kanálu. Můžete nakonfigurovat vlastní .NET aktivity ke spuštění pomocí služby Azure Batch nebo clusteru Azure HDInsight. 

     
## <a name="hdinsight-hive-activity"></a>Aktivita Hivu služby HDInsight
V definici JSON aktivity Hive můžete zadat následující vlastnosti. Musí být vlastnost typu aktivity: **HDInsightHive**. Musíte nejprve vytvořit propojené služby HDInsight a zadejte jako hodnotu pro název je **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **rámci typeProperties** části při nastavení typu aktivity na HDInsightHive:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| skript |Zadejte vloženého skriptu Hive |Ne |
| cestu ke skriptu |Uložení skriptu Hive v Azure blob storage a zadejte cestu k souboru. Pomocí vlastnosti 'skript' nebo 'scriptPath'. Obě nelze použít společně. Název souboru je malá a velká písmena. |Ne |
| definuje |Zadejte parametry dvojic klíč/hodnota pro odkazování v rámci skriptu Hive pomocí 'hiveconf. |Ne |

Tyto vlastnosti typu jsou specifická pro aktivitu Hive. Ostatní vlastnosti (mimo v rámci typeProperties části) jsou podporovány pro všechny aktivity.   

### <a name="json-example"></a>Příklad JSON
Následující kód JSON určuje aktivitu HDInsight Hive v kanálu.  

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

Další informace najdete v tématu [Hive aktivity](data-factory-hive-activity.md) článku. 

## <a name="hdinsight-pig-activity"></a>Aktivita Pig služby HDInsight
V definici JSON aktivity Pig můžete zadat následující vlastnosti. Musí být vlastnost typu aktivity: **HDInsightPig**. Musíte nejprve vytvořit propojené služby HDInsight a zadejte jako hodnotu pro název je **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **rámci typeProperties** části při nastavení typu aktivity na HDInsightPig: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| skript |Zadejte vložený skript Pig |Ne |
| cestu ke skriptu |Uložte skript Pig v Azure blob storage a zadejte cestu k souboru. Pomocí vlastnosti 'skript' nebo 'scriptPath'. Obě nelze použít společně. Název souboru je malá a velká písmena. |Ne |
| definuje |Zadejte parametry dvojic klíč/hodnota pro odkazování v rámci skript Pig |Ne |

Tyto vlastnosti typu jsou specifická pro aktivitu Pig. Ostatní vlastnosti (mimo v rámci typeProperties části) jsou podporovány pro všechny aktivity.   

### <a name="json-example"></a>Příklad JSON

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

Další informace najdete v tématu [Pig aktivity](#data-factory-pig-activity.md) článku. 

## <a name="hdinsight-mapreduce-activity"></a>Aktivita MapReduce služby HDInsight
V definici JSON aktivity MapReduce, můžete zadat následující vlastnosti. Musí být vlastnost typu aktivity: **HDInsightMapReduce**. Musíte nejprve vytvořit propojené služby HDInsight a zadejte jako hodnotu pro název je **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **rámci typeProperties** části při nastavení typu aktivity na HDInsightMapReduce: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jarLinkedService | Název propojené služby pro Azure Storage, který obsahuje soubor JAR. | Ano |
| jarFilePath | Cesta k souboru JAR ve službě Azure Storage. | Ano | 
| Název třídy | Název hlavní třídy v souboru JAR. | Ano | 
| argumenty | Seznam argumentů programu MapReduce, oddělených čárkami. V době běhu zobrazí několik další argumenty (například: mapreduce.job.tags) z rozhraní MapReduce. Chcete-li rozlišit vaší argumenty s argumenty MapReduce, zvažte, pomocí možnosti a hodnoty jako argumenty, jak je znázorněno v následujícím příkladu (- s, – vstup, – výstupní atd., jsou možnosti bezprostředně následované jejich hodnoty) | Ne | 

### <a name="json-example"></a>Příklad JSON

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Další informace najdete v tématu [činnost MapReduce](data-factory-map-reduce.md) článku. 

## <a name="hdinsight-streaming-activity"></a>Aktivita Streamování služby HDInsight
V definici JSON aktivity streamování Hadoop, můžete zadat následující vlastnosti. Musí být vlastnost typu aktivity: **HDInsightStreaming**. Musíte nejprve vytvořit propojené služby HDInsight a zadejte jako hodnotu pro název je **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **rámci typeProperties** části při nastavení typu aktivity na HDInsightStreaming: 

| Vlastnost | Popis | 
| --- | --- |
| Mapper | Název spustitelného souboru mapper. V příkladu je cat.exe mapper spustitelný soubor.| 
| reduktorem | Název spustitelného souboru reduktorem. V příkladu je wc.exe reduktorem spustitelný soubor. | 
| vstup | Vstupní soubor (včetně umístění) pro mapper. Příklad: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample je kontejner objektů blob, například/data/Gutenberg je složka, a davinci.txt je objekt blob. |
| output | Ve výstupním souboru (včetně umístění) reduktorem. Výstup úlohy streamování Hadoop je zapsán do umístění zadané pro tuto vlastnost. |
| filePaths | Cesty pro spustitelné soubory mapper a reduktorem. Příklad: "adfsample/example/apps/wc.exe" adfsample je kontejner objektů blob, příklad nebo aplikací je složka a wc.exe je spustitelný soubor. | 
| fileLinkedService | Propojená služba, která představuje úložiště Azure, který obsahuje soubory zadané v části filePaths Azure Storage. | 
| argumenty | Seznam argumentů programu MapReduce, oddělených čárkami. V době běhu zobrazí několik další argumenty (například: mapreduce.job.tags) z rozhraní MapReduce. Chcete-li rozlišit vaší argumenty s argumenty MapReduce, zvažte, pomocí možnosti a hodnoty jako argumenty, jak je znázorněno v následujícím příkladu (- s, – vstup, – výstupní atd., jsou možnosti bezprostředně následované jejich hodnoty) | 
| getdebuginfo – | Element volitelné. Pokud je nastavena k chybě, protokoly se stáhnou pouze při selhání. Pokud je nastavena pro všechny, protokoly budou staženy vždy bez ohledu na stav spuštění. | 

> [!NOTE]
> Je nutno zadat pro streamované aktivitě Hadoop pro datovou sadu výstupů **výstupy** vlastnost. Tato datová sada může být právě fiktivní datovou sadu, která je potřeba jednotka plán kanálu (hodinový, denní, atd.). Pokud aktivita neberou vstup, můžete přeskočit vstupní datové sady pro aktivitu pro určení **vstupy** vlastnost.  

## <a name="json-example"></a>Příklad JSON

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Další informace najdete v tématu [streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md) článku. 

## <a name="hdinsight-spark-activity"></a>Aktivita Spark služby HDInsight
V definici JSON aktivity Spark můžete zadat následující vlastnosti. Musí být vlastnost typu aktivity: **HDInsightSpark**. Musíte nejprve vytvořit propojené služby HDInsight a zadejte jako hodnotu pro název je **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **rámci typeProperties** části při nastavení typu aktivity na HDInsightSpark: 

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| rootPath | Kontejner objektů Blob v Azure a složky, která obsahuje soubor Spark. Název souboru je malá a velká písmena. | Ano |
| entryFilePath | Relativní cesta ke kořenové složce Spark kódu nebo balíčku. | Ano |
| Název třídy | Hlavní třídy aplikace Java/Spark | Ne | 
| argumenty | Seznam argumentů příkazového řádku pro Spark program. | Ne | 
| proxyUser | Uživatelský účet zosobnění spuštění programu Spark | Ne | 
| sparkConfig | Vlastnosti konfigurace Spark. | Ne | 
| getdebuginfo – | Určuje, kdy soubory protokolu Spark se zkopírují do úložiště Azure používá HDInsight cluster (nebo) zadaný ve sparkJobLinkedService. Povolené hodnoty: None, vždy nebo selhání. Výchozí hodnota: žádné. | Ne | 
| sparkJobLinkedService | Azure Storage propojená služba, která obsahuje Spark soubor úlohy, závislosti a protokoly.  Pokud hodnotu pro tuto vlastnost nezadáte, použije se úložiště přidružený k clusteru HDInsight. | Ne |

### <a name="json-example"></a>Příklad JSON

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Je třeba počítat s následujícím: 

- **Typ** je nastavena na **HDInsightSpark**.
- **RootPath** je nastaven na **adfspark\\pyFiles** kde adfspark je kontejner objektů Blob v Azure a pyFiles je dobře složku v daném kontejneru. V tomto příkladu úložiště objektů Blob Azure je ten, který je přidružen Spark cluster. Můžete nahrát soubor do jiného úložiště Azure. Pokud tak učiníte, vytvoření služby Azure Storage, propojené propojení objektu pro vytváření dat. Tento účet úložiště. Potom zadejte název propojené služby, jako hodnotu **sparkJobLinkedService** vlastnost. V tématu [vlastnosti aktivity Spark](#spark-activity-properties) podrobnosti o této vlastnosti a dalších vlastností podporované aktivitou Spark.
- **EntryFilePath** je nastaven na **test.py**, což je soubor python. 
- **Getdebuginfo –** je nastavena na **vždy**, tzn., soubory protokolů jsou vždy vygeneruje (úspěch nebo neúspěch).  

    > [!IMPORTANT]
    > Doporučujeme vám, že nenastavíte tuto vlastnost na vždy v produkčním prostředí Pokud řešíte problém. 
- **Výstupy** obsahuje jednu výstupní datovou sadu. Je třeba zadat datovou sadu výstupů i v případě, že spark program nevytváří žádný výstup. Výstupní datovou sadu jednotky plán pro kanál (hodinový, denní, atd.).

Další informace o aktivitě najdete v tématu [Spark aktivity](data-factory-spark.md) článku.  

## <a name="machine-learning-batch-execution-activity"></a>Aktivita Provedení dávky služby Machine Learning
V definici Azure ML dávky spuštění aktivity JSON, můžete zadat následující vlastnosti. Musí být vlastnost typu aktivity: **AzureMLBatchExecution**. Musíte vytvořit Azure Machine Learning nejprve propojené služby a zadejte název ji jako hodnotu **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **rámci typeProperties** části při nastavení typu aktivity na AzureMLBatchExecution:

Vlastnost | Popis | Požaduje se 
-------- | ----------- | --------
webServiceInput | Datové sady mají být předány jako vstup pro webovou službu Azure ML. Tato datová sada musí být součástí vstupy pro aktivitu. |Použijte webServiceInput nebo webServiceInputs. | 
webServiceInputs | Zadejte datové sady, které mají být předány jako vstupy pro webovou službu Azure ML. Pokud webová služba přijímá více vstupů, použijte vlastnost webServiceInputs místo pomocí vlastnosti webServiceInput. Datové sady, které odkazují **webServiceInputs** musí také obsahovat aktivity **vstupy**. | Použijte webServiceInput nebo webServiceInputs. | 
webServiceOutputs | Datové sady, které jsou přiřazeny jako výstup pro webovou službu Azure ML. Webová služba vrátí výstupní data v této datové sadě. | Ano | 
globalParameters | Zadejte hodnoty pro parametry webové služby v této části. | Ne | 

### <a name="json-example"></a>Příklad JSON
V tomto příkladu aktivity obsahuje datovou sadu **MLSqlInput** jako vstup a **MLSqlOutput** jako výstup. **MLSqlInput** předaným jako vstup k webové službě pomocí pomocí **webServiceInput** vlastnost JSON. **MLSqlOutput** předaný jako výstup webové služby s použitím **webServiceOutputs** vlastnost JSON. 

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }              
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

V příkladu JSON nasazené služby Azure Machine Learning Web používá čtečka čipových karet a modul zapisovače pro čtení a zápis dat z/do Azure SQL Database. Tato webová služba zveřejňuje následující čtyři parametry: název serveru, název databáze, název serveru uživatelského účtu a heslo uživatelského účtu serveru databáze.

> [!NOTE]
> Pouze vstupy a výstupy aktivity AzureMLBatchExecution lze předat jako parametry webové služby. Například ve výše uvedeném fragmentu JSON je MLSqlInput vstup AzureMLBatchExecution aktivity, která se předá jako vstup k webové službě pomocí parametru webServiceInput.

## <a name="machine-learning-update-resource-activity"></a>Aktivita aktualizace prostředku služby Machine Learning
V definici Azure ML aktualizace prostředků aktivity JSON, můžete zadat následující vlastnosti. Musí být vlastnost typu aktivity: **AzureMLUpdateResource**. Musíte vytvořit Azure Machine Learning nejprve propojené služby a zadejte název ji jako hodnotu **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **rámci typeProperties** části Pokud nastavíte typ aktivity AzureMLUpdateResource:

Vlastnost | Popis | Požaduje se 
-------- | ----------- | --------
trainedModelName | Název retrained modelu. | Ano |  
trainedModelDatasetName | Datová sada odkazuje na soubor iLearner vrácené retraining operací. | Ano | 

### <a name="json-example"></a>Příklad JSON
Kanál má dvě aktivity: **AzureMLBatchExecution** a **AzureMLUpdateResource**. Aktivita provedení dávky Azure ML trvá Cvičná data jako vstup a vytvoří soubor iLearner jako výstup. Aktivity vyvolá webovou službu školení (výukový experiment zveřejněné jako webovou službu) se vstupní Cvičná data a přijímá reprezentuje soubor ilearner z webovou službu. PlaceholderBlob je právě fiktivní výstupní datovou sadu, která je vyžadovaná službou Azure Data Factory ke spuštění kanálu.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita U-SQL služby Data Lake Analytics
V definici JSON aktivity U-SQL můžete zadat následující vlastnosti. Musí být vlastnost typu aktivity: **DataLakeAnalyticsU SQL**. Musíte vytvořit služby Azure Data Lake Analytics propojené a zadejte název ji jako hodnotu **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **rámci typeProperties** části při nastavení typu aktivity DataLakeAnalyticsU-SQL: 

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| scriptPath |Cesta ke složce, který obsahuje skript U-SQL. Název souboru je malá a velká písmena. |Ne (když používáte skript) |
| scriptLinkedService |Propojené služby, který odkazuje úložiště, který obsahuje skript pro vytváření dat. |Ne (když používáte skript) |
| skript |Zadejte místo zadání scriptPath a scriptLinkedService zpracování vloženého skriptu. Například: "skript": "Vytvořit databázi test". |Ne (když používáte scriptPath a scriptLinkedService) |
| degreeOfParallelism |Maximální počet uzlů současně slouží ke spuštění úlohy. |Ne |
| priorita |Určuje, jaké úlohy mimo všechny, které jsou zařazeny do fronty, měla by být vybrána má spustit jako první. Čím nižší je číslo, tím vyšší je priorita. |Ne |
| parameters |Parametry pro skript U-SQL |Ne |

### <a name="json-example"></a>Příklad JSON

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Další informace najdete v tématu [Data Lake Analytics U-SQL aktivity](data-factory-usql-activity.md). 

## <a name="stored-procedure-activity"></a>Aktivita Uložená procedura
V definici uložené procedury aktivity JSON, můžete zadat následující vlastnosti. Musí být vlastnost typu aktivity: **SqlServerStoredProcedure**. Musíte vytvořit některého z následujících propojených služeb a zadejte jako hodnotu pro název propojené služby **linkedServiceName** vlastnost:

- SQL Server 
- Azure SQL Database
- Azure SQL Data Warehouse

Následující vlastnosti jsou podporovány v **rámci typeProperties** části při nastavení typu aktivity na SqlServerStoredProcedure:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| storedProcedureName |Zadejte název uložené procedury v databázi Azure SQL nebo Azure SQL Data Warehouse, která je reprezentována propojené služby, která používá výstupní tabulka. |Ano |
| storedProcedureParameters |Zadejte hodnoty pro parametry uložené procedury. Pokud potřebujete předat hodnotu null pro parametr, použijte syntaxi: "param1": null (všechny malá písmena). Viz následující ukázka Další informace o používání této vlastnosti. |Ne |

Pokud zadáte vstupní datové sady, musí být k dispozici (v 'Připravený' stav) se spouští aktivita uložené procedury. Vstupní datové sady nelze zpracovat v uložené proceduře jako parametr. Používá se pouze ke kontrole závislost před zahájením aktivity uložené procedury. Je nutné zadat výstupní datovou sadu aktivity uložené procedury. 

Určuje výstupní datovou sadu **plán** aktivity uložené procedury (každou hodinu, týdně, měsíčně, atd.). Musíte použít výstupní datovou sadu **propojená služba** který odkazuje na databázi SQL Azure nebo Azure SQL Data Warehouse nebo databázi SQL Server, který chcete spustit uloženou proceduru. Výstupní datovou sadu může sloužit jako způsob, jak předat výsledek uložené procedury pro následné zpracování pomocí jiné aktivity ([řetězení aktivity](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) v kanálu. Ale objekt pro vytváření dat nelze zapsat automaticky výstup uložené procedury pro tuto datovou sadu. Je uložené procedury, která zapisuje do tabulky SQL, odkazující na výstupní datovou sadu. V některých případech může být výstupní datovou sadu **fiktivní datovou sadu**, který slouží pouze k určení plánu pro spuštěnou aktivity uložené procedury.  

### <a name="json-example"></a>Příklad JSON

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
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Další informace najdete v tématu [aktivity uložené procedury](data-factory-stored-proc-activity.md) článku. 

## <a name="net-custom-activity"></a>Vlastní aktivita .NET
V rozhraní .NET vlastní aktivity definici JSON, můžete zadat následující vlastnosti. Musí být vlastnost typu aktivity: **DotNetActivity**. Musíte vytvořit propojené služby Azure HDInsight nebo Azure Batch propojené služby a zadejte jako hodnotu pro název propojené služby **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **rámci typeProperties** části při nastavení typu aktivity na DotNetActivity:
 
| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| AssemblyName | Název sestavení. V příkladu je: **MyDotnetActivity.dll**. | Ano |
| Vstupní bod |Název třídy, která implementuje rozhraní IDotNetActivity. V příkladu je: **MyDotNetActivityNS.MyDotNetActivity** kde MyDotNetActivityNS je obor názvů a MyDotNetActivity je třída.  | Ano | 
| PackageLinkedService | Název úložiště Azure, propojené služby, která odkazuje na úložiště objektů blob, který obsahuje soubor zip vlastní aktivity. V příkladu je: **AzureStorageLinkedService**.| Ano |
| PackageFile | Název souboru zip. V příkladu je: **customactivitycontainer/MyDotNetActivity.zip**. | Ano |
| extendedProperties | Rozšířené vlastnosti, které můžete definovat a předat kód .NET. V tomto příkladu **SliceStart** proměnná je nastavená na hodnotu podle proměnnou SliceStart systému. | Ne | 

### <a name="json-example"></a>Příklad JSON

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Podrobné informace najdete v tématu [použít vlastní aktivity v datové továrně](data-factory-use-custom-activities.md) článku. 

## <a name="next-steps"></a>Další kroky
Projděte si tyto kurzy: 

- [Kurz: vytvoření kanálu s aktivitou kopírování](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Kurz: vytvoření kanálu s aktivitou hive](data-factory-build-your-first-pipeline-using-editor.md)
