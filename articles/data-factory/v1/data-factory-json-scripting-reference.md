---
title: Azure Data Factory - JSON Scripting Reference | Dokumentace Microsoftu
description: Poskytuje schémat JSON entit služby Data Factory.
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
ms.openlocfilehash: c5909c1f511d3a7816ebafc3ea8b326edb7f14e3
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629493"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory - JSON Scripting Reference
> [!NOTE]
> Tento článek platí pro Data Factory verze 1.


Tento článek obsahuje příklady a schémata JSON pro definování entit služby Azure Data Factory (kanál, aktivit, datové sady a propojené služby).  

## <a name="pipeline"></a>Kanál 
Vysoké úrovně struktury kanálu je následujícím způsobem: 

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
| description |Text popisující, k čemu aktivita nebo kanál slouží | Ne |
| activities | Obsahuje seznam aktivit. | Ano |
| start |Počáteční datum a čas pro kanál. Musí být v [formátu ISO](http://en.wikipedia.org/wiki/ISO_8601). Příklad: 2014-10-14T16:32:41. <br/><br/>Je možné zadat místního času, například Odhadovaný čas. Tady je příklad: `2016-02-27T06:00:00**-05:00`, což je odhad AM 6<br/><br/>Počáteční a koncové vlastnosti definujte aktivní období kanálu. Výstupní řezy se tvoří pouze s v tomto aktivní období. |Ne<br/><br/>Pokud zadáte hodnotu pro vlastnost end, musíte zadat hodnotu pro vlastnost start.<br/><br/>Počáteční a koncový čas může být oba prázdné k vytvoření kanálu. Je nutné zadat obě hodnoty se nastavit aktivní období kanálu ke spuštění. Pokud nezadáte počáteční a koncový čas při vytváření kanálu, můžete nastavit pomocí rutiny Set-AzureRmDataFactoryPipelineActivePeriod později. |
| konec |Koncové datum a čas pro kanál. Je-li zadána, musí být ve formátu ISO. Příklad: 2014-10-14T17:32:41 <br/><br/>Je možné zadat místního času, například Odhadovaný čas. Tady je příklad: `2016-02-27T06:00:00**-05:00`, což je odhad AM 6<br/><br/>Kanál spouštět bez omezení, zadejte jako hodnotu pro vlastnost end 9999-09-09. |Ne <br/><br/>Pokud zadáte hodnotu pro vlastnost start, musíte zadat hodnotu pro vlastnost end.<br/><br/>Naleznete v poznámkách k **start** vlastnost. |
| isPaused |Pokud je nastavený na hodnotu true kanál se nespustí. Výchozí hodnota = false. Tato vlastnost slouží k povolení nebo zakázání. |Ne |
| pipelineMode |Metoda pro naplánování spuštění pro kanál. Povolené hodnoty jsou: naplánované (výchozí), jednorázové.<br/><br/>"Naplánované" označuje, že se kanál spustí v zadaný časový interval podle jeho aktivního období (počáteční a koncový čas). "Jednorázové" označuje, že se kanál spustí pouze jednou. Jednorázová kanály po vytvoření se nedají upravit/aktualizovat aktuálně. Zobrazit [Onetime kanálu](data-factory-create-pipelines.md#onetime-pipeline) podrobnosti o jednorázové nastavení. |Ne |
| expirationTime |Dobu trvání, po vytvoření, pro kterou je platný a zůstanou zřízené kanálu. Pokud nemá žádná aktivní, se nezdařilo, nebo čekající spuštění kanálu se odstraní automaticky jednou dosáhne čas vypršení platnosti. |Ne |


## <a name="activity"></a>Aktivita 
Vysoké úrovně struktury pro aktivitu v rámci definice kanálu (aktivity element) je následujícím způsobem:

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
| jméno |Název aktivity. Zadejte název, který představuje akci, který je nakonfigurovaný na aktivitu<br/><ul><li>Maximální počet znaků: 260.</li><li>Musí začínat písmenem, číslicí nebo podtržítkem (_).</li><li>Nejsou povolené tyto znaky: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Ano |
| description |Text popisující, k čemu aktivita slouží. |Ne |
| type |Určuje typ aktivity. Zobrazit [ÚLOŽIŠŤ dat](#data-stores) a [aktivity TRANSFORMACE dat](#data-transformation-activities) oddíly pro různé typy aktivit. |Ano |
| vstupy |Vstupní tabulky použité aktivitou<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Ne pro HDInsightStreaming a SqlServerStoredProcedure aktivity <br/> <br/> Ano, pro všechny ostatní |
| výstupy |Výstupní tabulky použité aktivitou.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Ano |
| linkedServiceName |Název propojené služby používané aktivitou. <br/><br/>Aktivita může vyžadovat zadání propojené služby, která odkazuje na požadované výpočetní prostředí. |Ano pro aktivity HDInsight, Azure Machine Learning, aktivity a aktivity uložené procedury. <br/><br/>Ne ve všech ostatních případech |
| typeProperties |Vlastnosti v části typeProperties závisí na typu aktivity. |Ne |
| policy |Zásady, které ovlivňují chování aktivity za běhu. Pokud není zadaný, použijí se výchozí zásady. |Ne |
| Scheduler |Vlastnost "scheduler" se používá k definování požadované plánování aktivity. Jeho objektu třídy subproperties jsou stejné jako ty v [vlastnost availability v datové sadě](data-factory-create-datasets.md#dataset-availability). |Ne |

### <a name="policies"></a>Zásady
Zásady ovlivňují chování za běhu aktivity, konkrétně v případě, že zpracování řezu tabulku. Následující tabulka obsahuje podrobnosti.

| Vlastnost | Povolené hodnoty | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| souběžnost |Integer <br/><br/>Maximální hodnota: 10 |1 |Počet souběžných spuštění aktivity.<br/><br/>Určuje počet spuštění paralelní aktivity, které mohou probíhat na jiné kolekce obsahuje nějaké řezy. Například pokud aktivitu musí projít velké sady dostupných dat, mají větší hodnotu souběžnosti urychlí dat zpracovává. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Určuje pořadí datové řezy, které jsou zpracovávány.<br/><br/>Pokud máte 2 řezy (jeden situaci v 16: 00 a jiný v 17: 00) a oba jsou čeká na provedení. Pokud jste nastavili executionPriorityOrder bude NewestFirst, řez v 17: 00, je zpracován jako první. Podobně pokud nastavíte executionPriorityORder bude OldestFIrst, pak v 16: 00 zpracování řezu se. |
| retry |Integer<br/><br/>Maximální hodnota je 10 |0 |Počet opakování, než se zpracování dat pro řez je označen jako selhání. Spuštění aktivit pro datový řez je opakovat maximálně určený počet opakování. Opakování se provádí co nejdříve po selhání. |
| timeout |Časový interval |00:00:00 |Časový limit aktivity. Příklad: 00:10:00 (implikuje časový limit 10 minut)<br/><br/>Pokud hodnotu nezadáte, nebo je 0, je neomezený časový limit.<br/><br/>Pokud doba zpracování dat na určitý řez překročí hodnota časového limitu, bude zrušen a se systém pokusí o opakování zpracování. Počet opakovaných pokusů závisí na vlastnosti opakování. Pokud dojde k vypršení časového limitu, je stav nastaven na vypršel časový limit. |
| zpoždění |Časový interval |00:00:00 |Zadejte zpoždění před zpracování dat řezu.<br/><br/>Spuštění aktivit pro datový řez se spustí po zpoždění je za očekávanou dobu spuštění.<br/><br/>Příklad: 00:10:00 (implikuje prodlevě o délce 10 minut) |
| opakování po delší době |Integer<br/><br/>Maximální hodnota: 10 |1 |Počet opakování po delší době pokusů, než se řez spuštění se nezdařilo.<br/><br/>pokusy o opakování po delší době jsou rozmístěné ve longRetryInterval. Proto pokud je třeba zadat dobu mezi opakovanými pokusy, použijte opakování po delší době. Pokud je určen jak opakování, longRetry, jednotlivé pokusy o opakování po delší době zahrnuje opakovaných pokusů a je maximální počet pokusů o opakování * opakování po delší době.<br/><br/>Například, pokud budeme mít následující nastavení v zásadách aktivit:<br/>Zkuste: 3<br/>opakování po delší době: 2<br/>longRetryInterval: 01:00:00<br/><br/>Předpokládá se jenom jeden řez ke spuštění (stav Čeká) a pokaždé, když se nezdaří spuštění aktivity. Zpočátku bude pokusy o 3 po sobě jdoucích provedení. Po každý pokus o stav řezu bude opakovat. Po první 3 pokusy přes stav řezu bude LongRetry.<br/><br/>Po hodině (to znamená, longRetryInteval hodnota) bude další sadu 3 pokusy po sobě jdoucích spuštění. Potom by se stav řezu a byste se pokusit žádné další pokusy. Proto celkové 6 byly provedeny pokusy.<br/><br/>Pokud žádné spuštění úspěšné, stav řezu bude připravená a nedochází k pokusům o žádné další pokusy.<br/><br/>opakování po delší době může použít v situacích, kdy závislá data přibývají Nedeterministický časy nebo celkové prostředí je v nestabilním stavu za zpracování dat dojde k. V takových případech to uděláte, opakované pokusy sebou nemusí pomáhá a tím po uplynutí čas výsledky v požadované výstupu.<br/><br/>Word upozornění: není nastavený pro opakování po delší době nebo longRetryInterval vysoké hodnoty. Obvykle vyšší hodnoty znamenají další systémové problémy. |
| longRetryInterval |Časový interval |00:00:00 |Prodleva mezi pokusy o opakování po delší době |

### <a name="typeproperties-section"></a>části typeProperties
V části typeProperties se liší pro každou aktivitu. Aktivity transformace mají pouze vlastnosti typu. Zobrazit [aktivity TRANSFORMACE dat](#data-transformation-activities) části v tomto článku pro ukázky JSON, které definují transformace aktivity v kanálu. 

**Aktivita kopírování** má dvě témata v části typeProperties: **zdroj** a **jímky**. Zobrazit [ÚLOŽIŠŤ dat](#data-stores) části v tomto článku pro ukázky JSON, které ukazují, jak pomocí dat ukládat jako zdroje a jímky. 

### <a name="sample-copy-pipeline"></a>Ukázkový kanál kopírování
V následujícím ukázkovém kanálu je v části **activities** jedna aktivita typu **Kopírování**. V této ukázce [aktivita kopírování](data-factory-data-movement-activities.md) kopíruje data ze služby Azure Blob storage do služby Azure SQL database. 

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

Zobrazit [ÚLOŽIŠŤ dat](#data-stores) části v tomto článku pro ukázky JSON, které ukazují, jak pomocí dat ukládat jako zdroje a jímky.    

Kompletní postup vytváření tohoto kanálu najdete v části [kurz: kopírování dat z Blob Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

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
* **Definuje** oddíl se používá k určení nastavení modulu runtime, které se předá skriptu hive jako konfigurační hodnoty Hive (např. `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Zobrazit [aktivity TRANSFORMACE dat](#data-transformation-activities) části v tomto článku pro ukázky JSON, které definují transformace aktivity v kanálu.

Kompletní postup vytváření tohoto kanálu najdete v části [kurz: vytvoření prvního kanálu pro zpracování dat pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="linked-service"></a>Propojená služba
Základní struktura pro definici propojené služby je následujícím způsobem:

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
| vlastnosti – typ | Typ propojené služby. Příklad: Azure Storage, Azure SQL Database. |
| typeProperties | V části typeProperties má elementy, které se liší pro jednotlivé obchody dat nebo výpočetní prostředí. V tématu [úložišť dat](#datastores) části pro všechna data propojené služby úložiště a [výpočetní prostředí](#compute-environments) všechny výpočty propojené služby |   

## <a name="dataset"></a>Datová sada 
Datové sady ve službě Azure Data Factory je definovaná následujícím způsobem:

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

Následující tabulka popisuje vlastnosti v výše uvedený text JSON:   

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| jméno | Název datové sady. Zobrazit [Azure Data Factory – pravidla pojmenování](data-factory-naming-rules.md) pravidla pojmenování. |Ano |Není k dispozici |
| type | Typ datové sady. Zadejte jeden z typů podporovaných službou Azure Data Factory (například: AzureBlob, AzureSqlTable). Zobrazit [ÚLOŽIŠŤ dat](#data-stores) oddíl úložiště dat a typů sad dat podporovaných službou Data Factory. | 
| Struktura | Schéma datové sady. Obsahuje sloupce, jejich typy, atd. | Ne |Není k dispozici |
| typeProperties | Vlastnosti odpovídající vybraného typu. Zobrazit [ÚLOŽIŠŤ dat](#data-stores) části Podporované typy a jejich vlastnosti. |Ano |Není k dispozici |
| external | Logický příznak k určení, zda datové sady je explicitně vytvořen kanál datové továrny nebo ne. |Ne |false (nepravda) |
| dostupnosti | Definuje okno zpracování nebo řezů model pro produkční prostředí datové sady. Podrobnosti o datové sady, model dělení časového najdete [plánování a provádění](data-factory-scheduling-and-execution.md) článku. |Ano |Není k dispozici |
| policy |Definuje kritéria nebo podmínky, které musí splnit řezy datové sady. <br/><br/>Podrobnosti najdete v tématu [datovou sadu zásad](#Policy) oddílu. |Ne |Není k dispozici |

Každý sloupec v **struktura** oddíl obsahuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název sloupce. |Ano |
| type |Datový typ sloupce.  |Ne |
| Jazyková verze |.NET na základě jazykové verze se použije, když typ je zadán a je typ formátu .NET `Datetime` nebo `Datetimeoffset`. Výchozí hodnota je `en-us`. |Ne |
| Formát |Formátovací řetězec se používá, když typ je zadána a je typ formátu .NET `Datetime` nebo `Datetimeoffset`. |Ne |

V následujícím příkladu datová sada obsahuje tři sloupce `slicetimestamp`, `projectname`, a `pageviews` a jsou typu: řetězec, řetězec a desetinných v uvedeném pořadí.

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
| frequency |Určuje časovou jednotku pro produkční prostředí řez datové sady.<br/><br/><b>Podporované frekvence</b>: minuta, hodina, dne, týdne, měsíce |Ano |Není k dispozici |
| interval |Určuje multiplikátor pro četnost<br/><br/>"Interval četnosti x" Určuje, jak často se řez.<br/><br/>Pokud potřebujete datové sady na průřezem podle počtu hodin, nastavíte <b>frekvence</b> k <b>hodinu</b>, a <b>interval</b> k <b>1</b>.<br/><br/><b>Poznámka:</b>: Pokud chcete zadat frekvenci jako minutu, doporučujeme nastavit interval na menší než 15 |Ano |Není k dispozici |
| Styl |Určuje, zda by měl být řez na začátek/konec intervalu.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Pokud je nastaven styl EndOfInterval Frequency je nastavená na měsíc, řez na poslední den v měsíci. Pokud je nastaven styl StartOfInterval, řez v první den v měsíci.<br/><br/>Pokud je nastaven styl EndOfInterval Frequency je nastavená na den, řez za poslední hodinu dne.<br/><br/>Je-li Frequency je nastavená na Hour a je nastaven styl EndOfInterval, řez je vytvořen na konec hodiny. Například pro určitý řez pro dobu 13: 00 – 2 hodin řez ve 14. |Ne |EndOfInterval |
| anchorDateTime |Definuje absolutní pozici v čase plánovačem slouží k výpočtu hranice řez datové sady. <br/><br/><b>Poznámka:</b>: Pokud AnchorDateTime má částí data, která jsou podrobnější než je četnost pak podrobnější částí se ignorují. <br/><br/>Například pokud <b>interval</b> je <b>každou hodinu</b> (frekvence: hour a interval: 1) a <b>AnchorDateTime</b> obsahuje <b>minuty a sekundy</b>pak bude <b>minuty a sekundy</b> části AnchorDateTime jsou ignorovány. |Ne |01/01/0001 |
| Posun |Interval TimeSpan, podle kterého se posune začátku a konce všechny řezy datové sady. <br/><br/><b>Poznámka:</b>: Pokud nejsou zadány anchorDateTime a posun, výsledek je kombinované posun. |Ne |Není k dispozici |

Následující části Dostupnost určuje, zda výstupní datové sady vyprodukované každou hodinu (nebo) vstupní datová sada je k dispozici po hodinách:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

**Zásady** oddíl v definici datové sady definuje kritéria nebo podmínky, které musí splnit řezy datové sady.

| Název zásady | Popis | Použít na | Požaduje se | Výchozí |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Ověří, jestli data **objektů blob v Azure** splňuje požadavky na minimální velikost (v megabajtech). |Azure Blob |Ne |Není k dispozici |
| minimumRows |Ověří, jestli data v **Azure SQL database** nebo **tabulek v Azure** obsahuje minimální počet řádků. |<ul><li>Azure SQL Database</li><li>Tabulka Azure</li></ul> |Ne |Není k dispozici |

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

Pokud datová sada je vytvořených službou Azure Data Factory, by měla být označena jako **externí**. Toto nastavení je obecně platí pro vstupy první aktivitu v kanálu, pokud používá aktivitu nebo řetězení kanálu.

| Název | Popis | Požaduje se | Výchozí hodnota |
| --- | --- | --- | --- |
| dataDelay |Doba zpoždění kontroly dostupnosti externích dat pro danou řez. Například pokud jsou data dostupná po hodinách, kontrola ověří, externích dat je k dispozici a odpovídající řez je připravený může zpozdit s použitím dataDelay.<br/><br/>Platí pouze pro aktuální čas.  Například pokud je 1:00 PM hned teď a tato hodnota je 10 minut, ověření se spustí v 13:10.<br/><br/>Toto nastavení nemá vliv na kolekce obsahuje nějaké řezy v minulosti (řezy s koncový čas řezu + dataDelay < teď) bez jakéhokoli zpoždění zpracování.<br/><br/>Čas větší než 23:59 hodin muset zadat pomocí `day.hours:minutes:seconds` formátu. Například pokud chcete zadat 24 hodin, nepoužívejte 24:00:00 Místo toho použijte 1.00:00:00. Pokud používáte 24:00:00, je považován za 24 dní (24.00:00:00). 1 den a 4 hodiny zadejte 1:04:00:00. |Ne |0 |
| retryInterval |Doba čekání mezi selhání a dalších opakovaný pokus. Pokud se nezdaří zkuste je dalším pokusu o po retryInterval. <br/><br/>Pokud je 1:00 PM teď začneme první pokus. Pokud doba trvání dokončení první ověření je 1 minuta a operace se nezdařila, další opakování je v 1:00 + 1 min (doba trvání) + 1 minuta (interval opakování) = 1:02 odp. <br/><br/>Řezy v minulosti neexistuje žádné zpoždění. Opakování dojde okamžitě. |Ne |00:01:00 (1 minuta) |
| retryTimeout |Časový limit pro každý opakovaný pokus.<br/><br/>Pokud je tato vlastnost nastavená na 10 minut, ověřování je potřeba dokončit během 10 minut. Pokud to trvá déle než 10 minut, než se provést ověření, opakování vyprší časový limit.<br/><br/>Pokud všechny pokusy o ověření vyprší časový limit, řez je označena jako vypršel časový limit. |Ne |00:10:00 (10 minut) |
| maximumRetry |Počet přístupů ke kontrole dostupnosti externí data. Povolená maximální hodnota je 10. |Ne |3 |


## <a name="data-stores"></a>ÚLOŽIŠTĚ DAT
[Propojená služba](#linked-service) oddílu k dispozici popisy elementů JSON, které jsou společné pro všechny typy propojené služby. Tato část obsahuje podrobnosti o JSON prvky, které jsou specifické pro každé úložiště.

[Datovou sadu](#dataset) oddílu k dispozici popisy elementů JSON, které jsou společné pro všechny typy datových sad. Tato část obsahuje podrobnosti o JSON prvky, které jsou specifické pro každé úložiště.

[Aktivity](#activity) oddílu k dispozici popisy elementů JSON, které jsou společné pro všechny typy aktivit. Tato část obsahuje podrobnosti o JSON elementy, které jsou specifické pro každé úložiště dat, když se používá jako zdroj/jímka v aktivitě kopírování.  

Kliknutím na odkaz úložiště, které vás zajímají zobrazíte schémat JSON propojené služby, datové sady a zdroje/jímky pro aktivitu kopírování.

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
| &nbsp; |[Webová tabulka](#web-table) |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Propojená služba
Existují dva typy propojené služby: propojenou službu Azure Storage a propojená služba Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
K propojení účtu Azure storage do služby data factory pomocí **klíč účtu**, vytvořte propojenou službu Azure Storage. K definování služby Azure Storage propojenou službu, nastavte **typ** propojené služby pro **AzureStorage**. Potom můžete zadat následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k Azure storage pro vlastnost připojovací řetězec. |Ano |

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
Služby Azure Storage SAS propojené umožňuje propojit účet úložiště Azure do služby Azure data factory pomocí sdílený přístupový podpis (SAS). Poskytuje objekt pro vytváření dat s omezením pomocí specifikátoru/časově omezenou přístup k prostředkům all/konkrétní (objektu blob nebo kontejneru) ve službě storage. Propojte svůj účet úložiště Azure do služby data factory pomocí sdíleného přístupového podpisu, vytvoření Azure Storage SAS propojenou službu. K definování Azure Storage SAS propojenou službu, nastavte **typ** propojené služby pro **AzureStorageSas**. Potom můžete zadat následující vlastnosti v **typeProperties** části:   

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| sasUri |Zadejte identifikátor URI podpis sdíleného přístupu k prostředkům služby Azure Storage jako objekt blob, kontejneru nebo tabulky. |Ano |

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

Další informace o těchto propojené služby, najdete v části [konektor Azure Blob Storage](data-factory-azure-blob-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
K definování datové sady objektů Blob v Azure, nastavte **typ** datové sady na **AzureBlob**. Zadejte následující specifické vlastnosti objektů Blob v Azure v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Cesta k kontejner a složku v úložišti objektů blob. Příklad: myblobcontainer\myblobfolder\ |Ano |
| fileName |Název objektu blob. Název souboru je volitelný a malá a velká písmena.<br/><br/>Pokud zadáte filename, aktivity (včetně kopie) funguje na konkrétní objekt Blob.<br/><br/>Pokud není zadán název souboru, zahrnuje kopírování všech objektů BLOB v folderPath pro vstupní datovou sadu.<br/><br/>Pokud není zadán název souboru pro výstupní datovou sadu, název generovaného souboru by měl být v následujícím tento formát: Data. <Guid>.txt (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy vlastnost je volitelná. Slouží k určení dynamické folderPath a název souboru pro data časových řad. Například může být parametrizován folderPath pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, ** ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formát](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

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
Pokud se kopírování dat ze služby Azure Blob Storage, nastavte **typ zdroje** aktivity kopírování na **BlobSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. |True, False (výchozí hodnota) |Ne |

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
Pokud se kopírování dat do služby Azure Blob Storage, nastavte **jímky typu** aktivity kopírování na **BlobSink**a zadejte následující vlastnosti v **jímky** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopírování, pokud je zdroj BlobSource nebo systému souborů. |<b>PreserveHierarchy</b>: zachová hierarchií souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cesta cílový soubor do cílové složky.<br/><br/><b>FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úroveň cílové složky. Cílové soubory mají název automaticky generovány. <br/><br/><b>MergeFiles (výchozí):</b> sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadaný název souboru nebo objekt Blob, název sloučený soubor by měl být zadaný název; v opačném případě bude název automaticky generovaného souboru. |Ne |

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
K definování Azure Data Lake Store propojenou službu, nastavte typ propojenou službu, která **AzureDataLakeStore**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **AzureDataLakeStore** | Ano |
| dataLakeStoreUri | Zadejte informace o účtu Azure Data Lake Store. Má následující formát: `https://[accountname].azuredatalakestore.net/webhdfs/v1` nebo `adl://[accountname].azuredatalakestore.net/`. | Ano |
| subscriptionId | Id předplatného Azure, ke kterému patří Data Lake Store. | Vyžaduje se pro jímku |
| resourceGroupName | Název skupiny prostředků Azure, ke kterému patří Data Lake Store. | Vyžaduje se pro jímku |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano (pro ověřování instančních objektů) |
| servicePrincipalKey | Zadejte klíč aplikace. | Ano (pro ověřování instančních objektů) |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Podržením ukazatele myši v pravém horním rohu webu Azure portal můžete načíst ji. | Ano (pro ověřování instančních objektů) |
| Autorizace | Klikněte na tlačítko **Authorize** tlačítko **editoru služby Data Factory** a zadejte svoje přihlašovací údaje, které přiřadí tuto vlastnost adresa URL automaticky generované autorizace. | Ano (pro ověření pověření uživatele)|
| ID relace | Id relace OAuth z autorizační relace OAuth. Každé id relace je jedinečný a může být použit pouze jednou. Toto nastavení není automaticky vygenerován při použití editoru služby Data Factory. | Ano (pro ověření pověření uživatele) |

#### <a name="example-using-service-principal-authentication"></a>Příklad: použití ověřování instančních objektů
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

#### <a name="example-using-user-credential-authentication"></a>Příklad: používání ověřování pověření uživatele
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
Chcete-li definovat datová sada služby Azure Data Lake Store, nastavte **typ** datové sady na **AzureDataLakeStore**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| folderPath |Uložit cestu pro kontejner a složku ve službě Azure Data Lake. |Ano |
| fileName |Název souboru ve službě Azure Data Lake store. Název souboru je volitelný a malá a velká písmena. <br/><br/>Pokud zadáte filename, aktivity (včetně kopie) pracuje v konkrétní soubor.<br/><br/>Pokud není zadán název souboru, kopie zahrne všechny soubory v folderPath pro vstupní datovou sadu.<br/><br/>Pokud není zadán název souboru pro výstupní datovou sadu, název generovaného souboru by měl být v následujícím tento formát: Data. <Guid>.txt (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy vlastnost je volitelná. Slouží k určení dynamické folderPath a název souboru pro data časových řad. Například může být parametrizován folderPath pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, ** ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formát](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

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

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Azure Data Lake Store zdroje v aktivitě kopírování
Pokud se kopírování dat ze Azure Data Lake Store, nastavte **typ zdroje** aktivity kopírování na **AzureDataLakeStoreSource**a zadejte následující vlastnosti v **zdroj**části:

**AzureDataLakeStoreSource** podporuje následující vlastnosti **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. |True, False (výchozí hodnota) |Ne |

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

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store jímky v aktivitě kopírování
Pokud se kopírování dat do Azure Data Lake Store, nastavte **jímky typu** aktivity kopírování na **AzureDataLakeStoreSink**a zadejte následující vlastnosti v **jímky** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Určuje chování kopírování. |<b>PreserveHierarchy</b>: zachová hierarchií souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cesta cílový soubor do cílové složky.<br/><br/><b>FlattenHierarchy</b>: všechny soubory ze zdrojové složky vytvořené v první úroveň cílové složky. Cílové soubory jsou vytvořeny s názvem automaticky generovány.<br/><br/><b>MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadaný název souboru nebo objekt Blob, název sloučený soubor by měl být zadaný název; v opačném případě bude název automaticky generovaného souboru. |Ne |

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
K definování služby Azure Cosmos DB propojenou službu, nastavte **typ** propojené služby pro **DocumentDb**a zadejte následující vlastnosti v **typeProperties** části:  

| **Vlastnost** | **Popis** | **Vyžaduje** |
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
Další informace najdete v tématu [konektor služby Azure Cosmos DB](data-factory-azure-documentdb-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datová sada služby Azure Cosmos DB, nastavte **typ** datové sady na **DocumentDbCollection**a zadejte následující vlastnosti v **typeProperties** části: 

| **Vlastnost** | **Popis** | **Vyžaduje** |
| --- | --- | --- |
| collectionName |Název kolekce Azure Cosmos DB. |Ano |

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
Další informace najdete v tématu [konektor služby Azure Cosmos DB](data-factory-azure-documentdb-connector.md#dataset-properties) článku.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Zdroj kolekce Azure Cosmos DB v aktivitě kopírování
Pokud se kopírování dat ze služby Azure Cosmos DB, nastavte **typ zdroje** aktivity kopírování na **DocumentDbCollectionSource**a zadejte následující vlastnosti v **zdroj**části:


| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Vyžaduje** |
| --- | --- | --- | --- |
| query |Zadejte dotaz, který bude číst data. |Řetězce podporovány službou Azure Cosmos DB dotazu. <br/><br/>Příklad: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Ne <br/><br/>Pokud není zadán, příkaz SQL, která se spustí: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciální znakem k označení, že je vnořená dokumentu |Libovolný znak. <br/><br/>Azure Cosmos DB je úložiště NoSQL pro dokumenty JSON, ve kterém jsou povolené vnořené struktury. Azure Data Factory umožňuje uživateli k označení hierarchie prostřednictvím nestingSeparator, což je "." v předchozích příkladech. Oddělovačem, aktivita kopírování bude generovat objekt "Name" s tři podřízené prvky nejprve, střední a příjmení podle "Name.First", "Name.Middle" a "Name.Last" v definici tabulky. |Ne |

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

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Azure Cosmos DB kolekce jímky v aktivitě kopírování
Pokud se kopírování dat do služby Azure Cosmos DB, nastavte **jímky typu** aktivity kopírování na **DocumentDbCollectionSink**a zadejte následující vlastnosti v **jímky** oddílu :

| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Vyžaduje** |
| --- | --- | --- | --- |
| nestingSeparator |Je potřeba speciálního znaku v název zdrojového sloupce označíte daný vnořený dokument. <br/><br/>Například výše: `Name.First` ve výstupu tabulky vytvoří následující strukturu JSON v dokumentu databáze Cosmos DB:<br/><br/>"Name": {<br/>    "First": "John"<br/>}, |Znak, který se používá k oddělení úrovní vnoření.<br/><br/>Výchozí hodnota je `.` (tečka). |Znak, který se používá k oddělení úrovní vnoření. <br/><br/>Výchozí hodnota je `.` (tečka). |
| WriteBatchSize |Počet paralelních žádostí do služby Azure Cosmos DB k vytváření dokumentů.<br/><br/>Při kopírování dat ze služby Azure Cosmos DB pomocí této vlastnosti můžete optimalizovat výkon. Lepšího výkonu můžete očekávat, když zvýšíte writeBatchSize, protože se odesílají další paralelní požadavky na službu Azure Cosmos DB. Ale budete muset vyhnout omezení, která může vyvolat chybovou zprávu: "Žádosti je velká míra".<br/><br/>Omezení je určeno pomocí několika faktory, včetně velikosti dokumentů, počet podmínek v dokumentech, indexování zásad cílovou kolekci, atd. Pro operace kopírování, vám pomůže lépe kolekce (například S3) mají největší propustnost, které jsou k dispozici (požadavek 2 500 jednotek za sekundu). |Integer |Ne (výchozí: 5) |
| writeBatchTimeout |Čekací doba dokončení před vypršením časového limitu operace. |Časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |

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

Další informace najdete v tématu [konektor služby Azure Cosmos DB](data-factory-azure-documentdb-connector.md#copy-activity-properties) článku.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Propojená služba
Definovat službu Azure SQL Database propojenou službu, nastavte **typ** propojené služby pro **AzureSqlDatabase**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci Azure SQL Database pro vlastnost připojovací řetězec. |Ano |

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
Chcete-li definovat datová sada služby Azure SQL Database, nastavte **typ** datové sady na **AzureSqlTable**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v instanci Azure SQL Database, která je propojená služba odkazuje na. |Ano |

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
Pokud se kopírování dat ze služby Azure SQL Database, nastavte **typ zdroje** aktivity kopírování na **SqlSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |

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

### <a name="sql-sink-in-copy-activity"></a>SQL jímky v aktivitě kopírování
Pokud kopírujete data do služby Azure SQL Database, nastavte **jímky typu** aktivity kopírování na **SqlSink**a zadejte následující vlastnosti v **jímky** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu. |Časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |
| WriteBatchSize |Vloží data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování ke spuštění tak, že po vyčištění dat určitý řez. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování k vyplnění s identifikátorem automaticky generovány řez, který se používá k vyčištění dat určitý řez, kdy se znovu spustit. |Název sloupce pro sloupec s datovým typem binary(32). |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury upsertuje (aktualizace/vkládání) data do cílové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název tabulky typu použitého v uložené proceduře. Aktivitu kopírování, která zpřístupňuje data přesouvá do dočasné tabulky s tímto typem tabulky. Uloženou proceduru kód pak sloučit data kopírovaná s existujícími daty. |Zadejte název tabulky. |Ne |

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
Definovat službu Azure SQL Data Warehouse propojenou službu, nastavte **typ** propojené služby pro **AzureSqlDW**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci Azure SQL Data Warehouse pro vlastnost připojovací řetězec. |Ano |



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
Chcete-li definovat datová sada služby Azure SQL Data Warehouse, nastavte **typ** datové sady na **AzureSqlDWTable**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v databázi Azure SQL Data Warehouse, která odkazuje propojenou službu. |Ano |

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

### <a name="sql-dw-source-in-copy-activity"></a>SQL data Warehouse zdroje v aktivitě kopírování
Pokud se kopírování dat z Azure SQL Data Warehouse, nastavte **typ zdroje** aktivity kopírování na **SqlDWSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |

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

### <a name="sql-dw-sink-in-copy-activity"></a>SQL data Warehouse jímky v aktivitě kopírování
Pokud kopírujete data do služby Azure SQL Data Warehouse, nastavte **jímky typu** aktivity kopírování na **SqlDWSink**a zadejte následující vlastnosti v **jímky** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování ke spuštění tak, že po vyčištění dat určitý řez. |Příkaz dotazu. |Ne |
| allowPolyBase |Označuje, zda místo mechanismus hromadné vložení pomocí PolyBase (Pokud se používá). <br/><br/> **Při použití technologie PolyBase je doporučeným způsobem, jak načíst data do SQL Data Warehouse.** |True <br/>False (výchozí) |Ne |
| polyBaseSettings |Skupina vlastností, které může být zadán při **allowPolybase** je nastavena na **true**. |&nbsp; |Ne |
| rejectValue |Určuje číslo nebo procentuální podíl řádků, které mohou být odmítnuty předtím, než se dotaz nezdaří. <br/><br/>Další informace o možnosti odmítnutí PolyBase v **argumenty** část [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tématu. |0 (výchozí), 1, 2... |Ne |
| rejectType |Určuje, zda je rejectValue možnost zadat hodnotu literálu nebo jako procento. |Hodnota (výchozí), procenta |Ne |
| rejectSampleValue |Určuje počet řádků, načtěte před PolyBase přepočítá procento pozice zamítnutých řádků. |1, 2, … |Ano, pokud **rejectType** je **procento** |
| useTypeDefault |Určuje způsob zpracování chybějící hodnoty v textových souborů s oddělovači, když PolyBase načte data z textového souboru.<br/><br/>Další informace o této vlastnosti v části argumenty [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Hodnota TRUE, False (výchozí) |Ne |
| WriteBatchSize |Vloží data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu. |Časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |

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
K definování Azure Search propojenou službu, nastavte **typ** propojené služby pro **AzureSearch**a zadejte následující vlastnosti v **typeProperties** části:  

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
Chcete-li definovat datová sada služby Azure Search, nastavte **typ** datové sady na **AzureSearchIndex**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| type | Vlastnost type musí být nastavená na **AzureSearchIndex**.| Ano |
| indexName | Název indexu Azure Search. Objekt pro vytváření dat, nevytvoří index. Index musí existovat ve službě Azure Search. | Ano |

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

### <a name="azure-search-index-sink-in-copy-activity"></a>Služba Azure Search Index jímky v aktivitě kopírování
Pokud se kopírování dat do indexu Azure Search, nastavte **jímky typu** aktivity kopírování na **AzureSearchIndexSink**a zadejte následující vlastnosti v **jímky** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Určuje, jestli se má sloučit nebo nahradit, již existuje dokument v indexu. | Sloučit (výchozí)<br/>Odeslat| Ne |
| WriteBatchSize | Nahrání dat do indexu Azure Search writeBatchSize dosáhne velikosti vyrovnávací paměti. | 1 až 1 000. Výchozí hodnota je 1000. | Ne |

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
Existují dva typy propojené služby: propojenou službu Azure Storage a propojená služba Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
K propojení účtu Azure storage do služby data factory pomocí **klíč účtu**, vytvořte propojenou službu Azure Storage. K definování služby Azure Storage propojenou službu, nastavte **typ** propojené služby pro **AzureStorage**. Potom můžete zadat následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavená na: **AzureStorage** |Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k Azure storage pro vlastnost připojovací řetězec. |Ano |

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
Služby Azure Storage SAS propojené umožňuje propojit účet úložiště Azure do služby Azure data factory pomocí sdílený přístupový podpis (SAS). Poskytuje objekt pro vytváření dat s omezením pomocí specifikátoru/časově omezenou přístup k prostředkům all/konkrétní (objektu blob nebo kontejneru) ve službě storage. Propojte svůj účet úložiště Azure do služby data factory pomocí sdíleného přístupového podpisu, vytvoření Azure Storage SAS propojenou službu. K definování Azure Storage SAS propojenou službu, nastavte **typ** propojené služby pro **AzureStorageSas**. Potom můžete zadat následující vlastnosti v **typeProperties** části:   

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavená na: **AzureStorageSas** |Ano |
| sasUri |Zadejte identifikátor URI podpis sdíleného přístupu k prostředkům služby Azure Storage jako objekt blob, kontejneru nebo tabulky. |Ano |

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

Další informace o těchto propojené služby, najdete v části [konektor Azure Table Storage](data-factory-azure-table-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datová sada služby Azure Table, nastavte **typ** datové sady na **AzureTable**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze tabulky Azure, který odkazuje propojenou službu. |Ano. Pokud tableName je zadán bez azureTableSourceQuery, všechny záznamy z tabulky se zkopírují do cíle. Pokud je určena azureTableSourceQuery také záznamy z tabulky, která odpovídá dotazu se zkopírují do cíle. |

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

Další informace o těchto propojené služby, najdete v části [konektor Azure Table Storage](data-factory-azure-table-connector.md#dataset-properties) článku. 

### <a name="azure-table-source-in-copy-activity"></a>Azure Table zdroje v aktivitě kopírování
Pokud se kopírování dat z Azure Table Storage, nastavte **typ zdroje** aktivity kopírování na **AzureTableSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| azureTableSourceQuery |Použijte vlastní dotaz číst data. |Řetězec dotazu, tabulky Azure. Příklady naleznete v další části. |Ne. Pokud tableName je zadán bez azureTableSourceQuery, všechny záznamy z tabulky se zkopírují do cíle. Pokud je určena azureTableSourceQuery také záznamy z tabulky, která odpovídá dotazu se zkopírují do cíle. |
| azureTableSourceIgnoreTableNotFound |Označuje, zda spolknout výjimka tabulka neexistuje. |HODNOTA TRUE<br/>FALSE |Ne |

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

Další informace o těchto propojené služby, najdete v části [konektor Azure Table Storage](data-factory-azure-table-connector.md#copy-activity-properties) článku. 

### <a name="azure-table-sink-in-copy-activity"></a>Tabulka Azure jímky v aktivitě kopírování
Pokud se kopírování dat do Azure Table Storage, nastavte **jímky typu** aktivity kopírování na **AzureTableSink**a zadejte následující vlastnosti v **jímky** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Výchozí hodnota klíče oddílu, který je možné jímkou. |Hodnotu řetězce. |Ne |
| azureTablePartitionKeyName |Zadejte název sloupce, jejichž hodnoty se používají jako klíče oddílu. Pokud není zadán, AzureTableDefaultPartitionKeyValue slouží jako klíč oddílu. |Název sloupce. |Ne |
| azureTableRowKeyName |Zadejte název sloupce, jejichž hodnoty sloupce se používají jako klíč řádku. Pokud není zadán, použijte identifikátor GUID pro každý řádek. |Název sloupce. |Ne |
| azureTableInsertType |Režim vložení dat do tabulek v Azure.<br/><br/>Tato vlastnost určuje, zda existující řádky v tabulce výstup s odpovídajícími klíče oddílu a řádku mají jejich hodnoty, nahrazení nebo sloučení. <br/><br/>Další informace o tom, jak fungují tato nastavení (sloučení a nahradit), najdete v článku [vložení nebo Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) a [vložení nebo nahrazení Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx) témata. <br/><br> Toto nastavení se vztahuje na úrovni řádků, nikoli úrovni tabulky a ani možnost odstranění řádků ve výstupní tabulce, které neexistují ve vstupu. |Sloučit (výchozí)<br/>nahradit |Ne |
| WriteBatchSize |Při dosažení writeBatchSize nebo writeBatchTimeout vloží data do tabulek v Azure. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Při dosažení writeBatchSize nebo writeBatchTimeout vloží data do tabulek v Azure |Časový interval<br/><br/>Příklad: "00:20:00" (20 minut) |Ne (výchozí nastavení časového limitu úložiště klienta. výchozí hodnota 90 sekundu) |

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
Další informace o těchto propojené služby, najdete v části [konektor Azure Table Storage](data-factory-azure-table-connector.md#copy-activity-properties) článku. 

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Propojená služba
K definování Amazon Redshift propojenou službu, nastavte **typ** propojené služby pro **AmazonRedshift**a zadejte následující vlastnosti v **typeProperties** oddílu :  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |IP adresa nebo název hostitele serveru Amazon Redshift. |Ano |
| port |Číslo portu TCP, které server Amazon Redshift používá k naslouchání pro připojení klientů. |Ne, výchozí hodnota: 5439 |
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

Další informace najdete v tématu [konektor Amazon Redshift](#data-factory-amazon-redshift-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datová sada služby Amazon Redshift, nastavte **typ** datové sady na **RelationalTable**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v databázi Amazon Redshift, propojená služba odkazuje na. |Ne (Pokud **dotazu** z **RelationalSource** určena) |


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
Další informace najdete v tématu [konektor Amazon Redshift](#data-factory-amazon-redshift-connector.md#dataset-properties) článku.

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování 
Pokud data kopírujete text z Amazon Redshift, nastavte **typ zdroje** aktivity kopírování na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ne (Pokud **tableName** z **datovou sadu** určena) |

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
Další informace najdete v tématu [konektor Amazon Redshift](#data-factory-amazon-redshift-connector.md#copy-activity-properties) článku.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Propojená služba
K definování IBM DB2 propojenou službu, nastavte **typ** propojené služby pro **OnPremisesDB2**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru DB2. |Ano |
| databáze |Název databáze DB2. |Ano |
| schéma |Název schématu databáze. Název schématu je velká a malá písmena. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k databázi DB2. Možné hodnoty jsou: Anonymous, Basic a Windows. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud se používá ověřování Basic nebo Windows. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení k místní databázi DB2. |Ano |

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
Další informace najdete v tématu [konektor IBM DB2](#data-factory-onprem-db2-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
K definování datové sady DB2, nastavte **typ** datové sady na **RelationalTable**a zadejte následující vlastnosti v **typeProperties** části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze DB2, propojená služba odkazuje na. TableName je velká a malá písmena. |Ne (Pokud **dotazu** z **RelationalSource** určena) 

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

Další informace najdete v tématu [konektor IBM DB2](#data-factory-onprem-db2-connector.md#dataset-properties) článku.

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud se kopírování dat z databáze IBM DB2, nastavte **typ zdroje** aktivity kopírování na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Například: `"query": "select * from "MySchema"."MyTable""`. |Ne (Pokud **tableName** z **datovou sadu** určena) |

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
Další informace najdete v tématu [konektor IBM DB2](#data-factory-onprem-db2-connector.md#copy-activity-properties) článku.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Propojená služba
K definování MySQL propojenou službu, nastavte **typ** propojené služby pro **OnPremisesMySql**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru MySQL. |Ano |
| databáze |Název databáze MySQL. |Ano |
| schéma |Název schématu databáze. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k databázi MySQL. Možné hodnoty jsou: `Basic`. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno pro připojení k databázi MySQL. |Ano |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali. |Ano |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení k místní databázi MySQL. |Ano |

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

Další informace najdete v tématu [konektor MySQL](data-factory-onprem-mysql-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
K definování datové sady MySQL, nastavte **typ** datové sady na **RelationalTable**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze MySQL, propojená služba odkazuje na. |Ne (Pokud **dotazu** z **RelationalSource** určena) |

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
Další informace najdete v tématu [konektor MySQL](data-factory-onprem-mysql-connector.md#dataset-properties) článku. 

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud se kopírování dat z databáze MySQL, nastavte **typ zdroje** aktivity kopírování na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ne (Pokud **tableName** z **datovou sadu** určena) |


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

Další informace najdete v tématu [konektor MySQL](data-factory-onprem-mysql-connector.md#copy-activity-properties) článku. 

## <a name="oracle"></a>Oracle 

### <a name="linked-service"></a>Propojená služba
K definování Oracle propojenou službu, nastavte **typ** propojené služby pro **OnPremisesOracle**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| driverType | Určete, který ovladač určený ke kopírování dat z/do databáze Oracle. Povolené hodnoty jsou **Microsoft** nebo **ODP** (výchozí). Zobrazit [podporované verze a instalace](#supported-versions-and-installation) části na podrobnosti o ovladači. | Ne |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci databáze Oracle pro vlastnost připojovací řetězec. | Ano |
| Název brány | Název brány, který se používá pro připojení k místnímu serveru Oracle |Ano |

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

Další informace najdete v tématu [konektor Oracle](data-factory-onprem-oracle-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
K definování datové sady Oracle, nastavte **typ** datové sady na **OracleTable**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v databázi Oracle, který odkazuje propojenou službu. |Ne (Pokud **oracleReaderQuery** z **OracleSource** určena) |

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
Další informace najdete v tématu [konektor Oracle](data-factory-onprem-oracle-connector.md#dataset-properties) článku.

### <a name="oracle-source-in-copy-activity"></a>Oracle zdroje v aktivitě kopírování
Pokud se kopírování dat z databáze Oracle, nastavte **typ zdroje** aktivity kopírování na **OracleSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| oracleReaderQuery |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Příklad: `select * from MyTable` <br/><br/>Pokud není zadán, příkaz SQL, která se spustí: `select * from MyTable` |Ne (Pokud **tableName** z **datovou sadu** určena) |

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

Další informace najdete v tématu [konektor Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) článku.

### <a name="oracle-sink-in-copy-activity"></a>Oracle jímky v aktivitě kopírování
Pokud data kopírujete do am databáze Oracle, nastavte **jímky typu** aktivity kopírování na **třídě OracleSink**a zadejte následující vlastnosti v **jímky** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu. |Časový interval<br/><br/> Příklad: 00:30:00 (30 minut). |Ne |
| WriteBatchSize |Vloží data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti. |Celé číslo (počet řádků) |Ne (výchozí: 100) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování ke spuštění tak, že po vyčištění dat určitý řez. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování k vyplnění s identifikátorem automaticky generovány řez, který se používá k vyčištění dat určitý řez, kdy se znovu spustit. |Název sloupce pro sloupec s datovým typem binary(32). |Ne |

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
Další informace najdete v tématu [konektor Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) článku.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Propojená služba
K definování PostgreSQL propojenou službu, nastavte **typ** propojené služby pro **OnPremisesPostgreSql**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru PostgreSQL. |Ano |
| databáze |Název databáze PostgreSQL. |Ano |
| schéma |Název schématu databáze. Název schématu je velká a malá písmena. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k databázi PostgreSQL. Možné hodnoty jsou: Anonymous, Basic a Windows. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud se používá ověřování Basic nebo Windows. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení k místní databázi PostgreSQL. |Ano |

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
K definování datové sady PostgreSQL, nastavte **typ** datové sady na **RelationalTable**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze PostgreSQL, propojená služba odkazuje na. TableName je velká a malá písmena. |Ne (Pokud **dotazu** z **RelationalSource** určena) |

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

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud se kopírování dat z databáze PostgreSQL, nastavte **typ zdroje** aktivity kopírování na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Příklad: "dotaz": "vybrat * z \"MySchema\".\" MyTable\"". |Ne (Pokud **tableName** z **datovou sadu** určena) |

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
K definování SAP Business Warehouse (BW) propojenou službu, nastavte **typ** propojené služby pro **SapBw**a zadejte následující vlastnosti v **typeProperties** oddílu :  

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém se nachází instance SAP BW. | řetězec | Ano
systemNumber | Číslo systému systému SAP BW. | Dvěma číslicemi desetinné číslo reprezentované jako řetězec. | Ano
ID klienta | ID klienta v systému SAP W klienta. | Tři číslice desetinné číslo reprezentované jako řetězec. | Ano
uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP | řetězec | Ano
heslo | Heslo pro uživatele. | řetězec | Ano
Název brány | Název brány, který služba Data Factory měla použít pro připojení k místní instanci SAP BW. | řetězec | Ano
encryptedCredential | Řetězec, který šifrované přihlašovací údaje. | řetězec | Ne

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

Další informace najdete v tématu [konektoru SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
K definování datové sady SAP BW, nastavte **typ** datové sady na **RelationalTable**. Nejsou žádné vlastnosti specifické pro typ. podporované pro SAP BW datové sady typu **RelationalTable**.  

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
Další informace najdete v tématu [konektoru SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#dataset-properties) článku. 

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud data kopírujete text z SAP Business Warehouse, nastavte **typ zdroje** aktivity kopírování na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:


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

Další informace najdete v tématu [konektoru SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) článku. 

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Propojená služba
K definování SAP HANA propojenou službu, nastavte **typ** propojené služby pro **SapHana**a zadejte následující vlastnosti v **typeProperties** části:  

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém se nachází instance SAP HANA. Pokud váš server používá vlastní port, zadejte `server:port`. | řetězec | Ano
authenticationType. | Typ ověřování. | řetězec. "Základní" nebo "Windows" | Ano 
uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP | řetězec | Ano
heslo | Heslo pro uživatele. | řetězec | Ano
Název brány | Název brány, který služba Data Factory měla použít pro připojení k místní instanci SAP HANA. | řetězec | Ano
encryptedCredential | Řetězec, který šifrované přihlašovací údaje. | řetězec | Ne

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
Další informace najdete v tématu [konektor SAP HANA](data-factory-sap-hana-connector.md#linked-service-properties) článku.
 
### <a name="dataset"></a>Datová sada
K definování datové sady SAP HANA, nastavte **typ** datové sady na **RelationalTable**. Nejsou žádné vlastnosti specifické pro typ. podporované pro SAP HANA datové sady typu **RelationalTable**. 

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
Další informace najdete v tématu [konektor SAP HANA](data-factory-sap-hana-connector.md#dataset-properties) článku. 

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud se kopírování dat z úložiště dat SAP HANA, nastavte **typ zdroje** aktivity kopírování na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query | Určuje dotaz SQL na čtení dat z instance SAP HANA. | Dotaz SQL. | Ano |


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

Další informace najdete v tématu [konektor SAP HANA](data-factory-sap-hana-connector.md#copy-activity-properties) článku.


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Propojená služba
Vytvoření propojené služby typu **OnPremisesSqlServer** propojení k místní databázi SQL serveru do služby data factory. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro místní propojené služby SQL serveru.

Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro propojenou službu SQL serveru.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type by měla být nastavená na: **OnPremisesSqlServer**. |Ano |
| připojovací řetězec |Zadejte připojovací řetězec informace potřebné pro připojení k místní databázi SQL serveru pomocí ověřování SQL nebo ověřování Windows. |Ano |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení k místní databázi SQL serveru. |Ano |
| uživatelské jméno |Pokud používáte ověřování Windows, zadejte uživatelské jméno. Příklad: **domainname\\uživatelské jméno**. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |

Můžete k šifrování přihlašovacích údajů a **New-AzureRmDataFactoryEncryptValue** rutiny a jejich použití v připojovacím řetězci, jak je znázorněno v následujícím příkladu (**EncryptedCredential** vlastnost):  

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Příklad: JSON pro použití ověřování SQL

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
#### <a name="example-json-for-using-windows-authentication"></a>Příklad: JSON pro použití ověřování Windows

Pokud uživatelské jméno a heslo nejsou zadány, budou brána používá zosobnění zadaný uživatelský účet pro připojení k místní databázi systému SQL Server. Jinak brána připojí k serveru SQL Server přímo s kontextem zabezpečení brány (jeho účet po spuštění).

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

Další informace najdete v tématu [konektor SQL serveru](data-factory-sqlserver-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
K definování datové sady SQL Server, nastavte **typ** datové sady na **SqlServerTable**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v instanci databáze SQL serveru, která je propojená služba odkazuje na. |Ano |

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

Další informace najdete v tématu [konektor SQL serveru](data-factory-sqlserver-connector.md#dataset-properties) článku. 

### <a name="sql-source-in-copy-activity"></a>Zdroje SQL v aktivitě kopírování
Pokud se kopírování dat z databáze serveru SQL Server, nastavte **typ zdroje** aktivity kopírování na **SqlSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Například: `select * from MyTable`. Může odkazovat více tabulek z databáze odkazuje vstupní datové sady. Pokud není zadán, příkaz SQL, která se spustí: Vyberte MyTable. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |

Pokud **sqlReaderQuery** je určená pro SqlSource, spustí aktivita kopírování tohoto dotazu na zdroji databázi systému SQL Server se mají získat data.

Alternativně můžete zadat uložené procedury tak, že zadáte **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura slouží k sestavení dotazu select pro spuštění na databázi SQL serveru. Pokud struktura nemá žádné definice datové sady, vyberou se všechny sloupce z tabulky.

> [!NOTE]
> Při použití **sqlReaderStoredProcedureName**, je stále potřeba zadat hodnotu **tableName** vlastnost v datové sadě JSON. Neexistují žádné ověření, ale provedeny v této tabulce.


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

V tomto příkladu **sqlReaderQuery** je určená pro SqlSource. Tento dotaz spustí aktivita kopírování na zdroji databázi systému SQL Server se mají získat data. Alternativně můžete zadat uložené procedury tak, že zadáte **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura parametry). SqlReaderQuery může odkazovat více tabulek v databázi odkazují vstupní datové sady. Není omezena pouze tabulky jako typeProperty tableName datové sady.

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura slouží k sestavení dotazu select pro spuštění na databázi SQL serveru. Pokud struktura nemá žádné definice datové sady, vyberou se všechny sloupce z tabulky.

Další informace najdete v tématu [konektor SQL serveru](data-factory-sqlserver-connector.md#copy-activity-properties) článku. 

### <a name="sql-sink-in-copy-activity"></a>SQL jímky v aktivitě kopírování
Pokud se kopírování dat do databáze SQL serveru, nastavte **jímky typu** aktivity kopírování na **SqlSink**a zadejte následující vlastnosti v **jímky** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu. |Časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |
| WriteBatchSize |Vloží data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování ke spuštění tak, že po vyčištění dat určitý řez. Další informace najdete v tématu [opakovatelnosti](#repeatability-during-copy) oddílu. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování k vyplnění s identifikátorem automaticky generovány řez, který se používá k vyčištění dat určitý řez, kdy se znovu spustit. Další informace najdete v tématu [opakovatelnosti](#repeatability-during-copy) oddílu. |Název sloupce pro sloupec s datovým typem binary(32). |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury upsertuje (aktualizace/vkládání) data do cílové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název tabulky typu použitého v uložené proceduře. Aktivitu kopírování, která zpřístupňuje data přesouvá do dočasné tabulky s tímto typem tabulky. Uloženou proceduru kód pak sloučit data kopírovaná s existujícími daty. |Zadejte název tabulky. |Ne |

#### <a name="example"></a>Příklad:
Kanálu obsahujícího aktivitu kopírování, kterou Pokud chcete použít tyto vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **jímky** je typ nastaven na **SqlSink**.

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

Další informace najdete v tématu [konektor SQL serveru](data-factory-sqlserver-connector.md#copy-activity-properties) článku. 

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Propojená služba
K definování Sybase propojenou službu, nastavte **typ** propojené služby pro **OnPremisesSybase**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru Sybase. |Ano |
| databáze |Název databáze Sybase. |Ano |
| schéma |Název schématu databáze. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k databázi Sybase. Možné hodnoty jsou: Anonymous, Basic a Windows. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud se používá ověřování Basic nebo Windows. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení k místní databázi Sybase. |Ano |

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
K definování datové sady Sybase, nastavte **typ** datové sady na **RelationalTable**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze Sybase, propojená služba odkazuje na. |Ne (Pokud **dotazu** z **RelationalSource** určena) |

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

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud se kopírování dat z databáze Sybase, nastavte **typ zdroje** aktivity kopírování na **RelationalSource**a zadejte následující vlastnosti v **zdroj** oddílu :


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ne (Pokud **tableName** z **datovou sadu** určena) |

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
K definování Teradata propojenou službu, nastavte **typ** propojené služby pro **OnPremisesTeradata**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru Teradata. |Ano |
| authenticationType. |Typ ověřování používaný pro připojení k databázi Teradata. Možné hodnoty jsou: Anonymous, Basic a Windows. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud se používá ověřování Basic nebo Windows. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení k místní databázi Teradata. |Ano |

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
K definování datové sady objektů Teradata Blob, nastavte **typ** datové sady na **RelationalTable**. V současné době nejsou žádné vlastnosti typu pro datovou sadu Teradata nepodporuje. 

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

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud se kopírování dat z databáze Teradata, nastavte **typ zdroje** aktivity kopírování na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ano |

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
Chcete-li definovat Cassandra propojené služby, nastavte **typ** propojené služby pro **OnPremisesCassandra**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| hostitel |Jeden nebo více IP adres nebo názvů hostitele serveru Cassandra.<br/><br/>Zadejte seznam IP adres nebo názvů hostitele pro připojení ke všem serverům současně. |Ano |
| port |Port TCP, který Cassandra server používá k naslouchání pro připojení klientů. |Ne, výchozí hodnota: 9042 |
| authenticationType. |Základní nebo anonymní |Ano |
| uživatelské jméno |Zadejte uživatelské jméno uživatelského účtu. |Ano, pokud typ ověřování je nastavena na Basic. |
| heslo |Zadejte heslo pro uživatelský účet. |Ano, pokud typ ověřování je nastavena na Basic. |
| Název brány |Název brány, který se používá pro připojení k místní databázi Cassandra. |Ano |
| encryptedCredential |Přihlašovací údaje zašifrované pomocí brány. |Ne |

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
K definování datové sady Cassandra, nastavte **typ** datové sady na **CassandraTable**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| prostor klíčů |Název prostor klíčů nebo schéma databáze Cassandra. |Ano (Pokud **dotazu** pro **CassandraSource** není definován). |
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
Pokud se kopírování dat z Cassandry, nastavte **typ zdroje** aktivity kopírování na **CassandraSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |SQL-92 nebo dotazu CQL. Zobrazit [CQL odkaz](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Při použití jazyka SQL, zadejte **keyspace name.table název** představující tabulku, kterou dotaz. |Ne (pokud jsou definovány tableName a prostor klíčů pro datovou sadu). |
| consistencyLevel |Úrovně konzistentnosti Určuje, kolik repliky musí odpovědět na požadavek čtení před vrácením data do klientské aplikace. Cassandra ověří zadaný počet replik pro data splňují požadavek na čtení. |JEDNA, DVĚ, TŘI, KVORA, VŠECHNY, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Zobrazit [konfigurace konzistentnosti dat](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) podrobnosti. |Ne. Výchozí hodnota je 1. |

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
K definování MongoDB propojenou službu, nastavte **typ** propojené služby pro **OnPremisesMongoDB**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |IP adresa nebo název hostitele serveru MongoDB. |Ano |
| port |Port TCP, který MongoDB server používá k naslouchání pro připojení klientů. |Volitelné, výchozí hodnota: 27017 |
| authenticationType. |Basic nebo Anonymous. |Ano |
| uživatelské jméno |Uživatelský účet pro přístup k MongoDB. |Ano (Pokud se používá základní ověřování). |
| heslo |Heslo pro uživatele. |Ano (Pokud se používá základní ověřování). |
| authSource |Název databáze MongoDB, kterou chcete použít ke kontrole přihlašovacích údajů pro ověřování. |Nepovinné (Pokud se používá základní ověřování). Výchozí: používá účet správce a databáze určená vlastnost databaseName. |
| databaseName |Název databáze MongoDB, které chcete získat přístup. |Ano |
| Název brány |Název brány, který přistupuje k úložišti. |Ano |
| encryptedCredential |Přihlašovací údaje zašifrované pomocí brány. |Nepovinné |

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

Další informace najdete v tématu [článku konektoru MongoDB](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
K definování datové sady MongoDB, nastavte **typ** datové sady na **MongoDbCollection**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| collectionName |Název kolekce v databázi MongoDB. |Ano |

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

Další informace najdete v tématu [článku konektoru MongoDB](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>Zdroj MongoDB v aktivitě kopírování
Pokud data kopírujete text z MongoDB, nastavte **typ zdroje** aktivity kopírování na **MongoDbSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL 92. Například: `select * from MyTable`. |Ne (Pokud **collectionName** z **datovou sadu** určena) |

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

Další informace najdete v tématu [článku konektoru MongoDB](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Propojená služba
K definování Amazon S3 propojenou službu, nastavte **typ** propojené služby pro **typu AwsAccessKey**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| accessKeyID |ID tajný přístupový klíč. |řetězec |Ano |
| secretAccessKey |Vlastního klíče přístupu k tajným klíčům. |Zašifrovaný řetězec tajného kódu |Ano |

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

Další informace najdete v tématu [článku konektor Amazon S3](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Datová sada
Chcete-li definovat datová sada služby Amazon S3, nastavte **typ** datové sady na **AmazonS3**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| bucketName |Název sektoru S3. |Řetězec |Ano |
| key |Klíč objektu S3. |Řetězec |Ne |
| Předpona |Předpona klíče objektu S3. Objekty, jejichž klíče začínat touto předponou vybráno. Platí pouze v případě, klíč je prázdný. |Řetězec |Ne |
| verze |Verze objektu S3, pokud je povolená Správa verzí S3. |Řetězec |Ne |
| Formát | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, ** ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formát](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady. |Ne | |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne | |


> [!NOTE]
> bucketName + klávesy Určuje umístění objektu S3, pokud blok je kořenový kontejner pro objekty S3 a klíč je úplná cesta k objektu S3.

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
#### <a name="example-sample-data-set-with-version"></a>Příklad: Ukázkové datové sadě (verze)

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
V ukázce používáme pevnými hodnotami vlastností klíč a bucketName v datovou sadu Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Můžete mít Data Factory vypočítat klíč a bucketName dynamicky za běhu pomocí systémových proměnných, například vlastnosti SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Můžete provést stejný pro vlastnost prefix datová sada služby Amazon S3. Zobrazit [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md) seznam podporovaných funkcí a proměnných.

Další informace najdete v tématu [článku konektor Amazon S3](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud se kopírují data z Amazonu S3, nastavte **typ zdroje** aktivity kopírování na **FileSystemSource**a zadejte následující vlastnosti v **zdroj** části:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, zda rekurzivně seznamu S3 objekty v adresáři. |True nebo false |Ne |


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

Další informace najdete v tématu [článku konektor Amazon S3](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Systém souborů


### <a name="linked-service"></a>Propojená služba
Systém souborů v místním můžete propojit s Azure data factory s **s místními souborového serveru** propojenou službu. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro souborový Server On-Premises propojenou službu.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Zkontrolujte, že vlastnost type je nastavená na **OnPremisesFileServer**. |Ano |
| hostitel |Určuje kořenová cesta ke složce, která chcete kopírovat. Použijte řídicí znak "\" pro zvláštní znaky v řetězci. Zobrazit [ukázka propojené služby a datové sady definice](#sample-linked-service-and-dataset-definitions) příklady. |Ano |
| ID uživatele |Zadejte ID uživatele, který má přístup k serveru. |Ne (když zvolíte encryptedCredential) |
| heslo |Zadejte heslo pro uživatele (ID uživatele). |Ne (když zvolíte encryptedCredential |
| encryptedCredential |Zadejte zašifrované přihlašovací údaje, které můžete získat spuštěním rutiny New-AzureRmDataFactoryEncryptValue. |Ne (když se rozhodnete zadat ID uživatele a heslo jako prostý text) |
| Název brány |Určuje název brány, které služby Data Factory měla použít pro připojení k serveru v místním souboru. |Ano |

#### <a name="sample-folder-path-definitions"></a>Ukázka složky cesta definice 
| Scénář | Hostování v definici propojené služby | folderPath v definici datové sady |
| --- | --- | --- |
| Místní složka na počítači brány pro správu dat: <br/><br/>Příklady: D:\\ \* nebo D:\folder\subfolder\\* |D:\\ \\ (pro Data Management Gateway 2.0 a novější) <br/><br/> místního hostitele (pro starší verze než Data Management Gateway 2.0) |. \\ \\ nebo složky\\\\podsložku (pro Data Management Gateway 2.0 a novější) <br/><br/>D:\\ \\ nebo D:\\\\složky\\\\podsložku (pro brány verze nižší než 2.0) |
| Vzdálené sdílené složky: <br/><br/>Příklady: \\ \\myserver\\sdílet\\ \* nebo \\ \\myserver\\sdílet\\složky\\podsložky\\* |\\\\\\\\myserver\\\\sdílet |. \\ \\ nebo složky\\\\podsložky |


#### <a name="example-using-username-and-password-in-plain-text"></a>Příklad: Pomocí uživatelského jména a hesla v prostém textu

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

#### <a name="example-using-encryptedcredential"></a>Příklad: Použití encryptedcredential

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
K definování datové sady systému souborů, nastavte **typ** datové sady na **sdílení souborů**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Určuje dílčí cestou ke složce. Použijte řídicí znak "\" pro zvláštní znaky v řetězci. Zobrazit [ukázka propojené služby a datové sady definice](#sample-linked-service-and-dataset-definitions) příklady.<br/><br/>Můžete zkombinovat tato vlastnost se **partitionBy** mít složku cesty založené na řez počátečním/koncovém data a časy. |Ano |
| fileName |Zadejte název souboru **folderPath** Pokud má tabulka, která má odkazovat na konkrétní soubor ve složce. Pokud je nezadávejte žádnou hodnotu pro tuto vlastnost, v tabulce odkazuje na všechny soubory ve složce.<br/><br/>Pokud není zadán název souboru pro výstupní datovou sadu, název generovaného souboru je v následujícím formátu: <br/><br/>`Data.<Guid>.txt` (Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Ne |
| fileFilter |Určete filtr, který slouží k výběru podmnožinu souborů v cestě folderPath, nikoli všech souborů. <br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklad 1: "fileFilter": "* .log"<br/>Příklad 2: "fileFilter": 2016 - 1-?. TXT"<br/><br/>Všimněte si, že fileFilter se dá použít pro vstupní datovou sadu sdílení souborů. |Ne |
| partitionedBy |PartitionedBy můžete použít k určení dynamické folderPath/fileName pro data časových řad. Příkladem je folderPath s parametry pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, ** ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formát](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**; a jsou podporované úrovně: **Optimal** a **nejrychlejší**. Zobrazit [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

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
Pokud se kopírování dat ze systému souborů, nastavte **typ zdroje** aktivity kopírování na **FileSystemSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. |Hodnota TRUE, False (výchozí) |Ne |

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
Pokud data kopírujete do systému souborů, nastavte **jímky typu** aktivity kopírování na **FileSystemSink**a zadejte následující vlastnosti v **jímky** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopírování, pokud je zdroj BlobSource nebo systému souborů. |**PreserveHierarchy:** zachová hierarchií souborů v cílové složce. To znamená relativní cestu ke zdrojové složce zdrojový soubor je stejný jako relativní cesta cílový soubor do cílové složky.<br/><br/>**FlattenHierarchy:** všechny soubory ze zdrojové složky vytvořené v první úroveň cílové složky. Cílové soubory se vytvoří s automaticky generovaným názvem.<br/><br/>**MergeFiles:** sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název nebo objekt blob název souboru, název sloučený soubor je zadaný název. V opačném případě je název automaticky generovaného souboru. |Ne |
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
K definování FTP propojenou službu, nastavte **typ** propojené služby pro **Server_ftp**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| hostitel |Název nebo IP adresa serveru FTP |Ano |&nbsp; |
| authenticationType. |Zadání typu ověřování |Ano |Basic, anonymní |
| uživatelské jméno |Uživatel, který má přístup k serveru FTP |Ne |&nbsp; |
| heslo |Heslo pro uživatele (uživatelské jméno) |Ne |&nbsp; |
| encryptedCredential |Šifrované přihlašovací údaje pro přístup k serveru FTP |Ne |&nbsp; |
| Název brány |Název brány správy dat pro připojení k serveru FTP na místě |Ne |&nbsp; |
| port |Port, na kterém naslouchá FTP server |Ne |21 |
| enableSsl |Určete, jestli chcete pomocí funkce FTP přes kanál SSL/TLS. |Ne |true (pravda) |
| enableServerCertificateValidation |Určuje, zda povolit ověření certifikátu serveru SSL při použití FTP přes kanál SSL/TLS. |Ne |true (pravda) |

#### <a name="example-using-anonymous-authentication"></a>Příklad: Použití anonymní ověřování

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

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Příklad: Pomocí uživatelského jména a hesla v prostém textu pro základní ověřování

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

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Příklad: Použití portu, enableSsl, enableServerCertificateValidation

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

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Příklad: Použití encryptedCredential pro ověřování a brány

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
Chcete-li definovat datová sada služby FTP, nastavte **typ** datové sady na **sdílení souborů**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Podřízená cesta ke složce. Použijte řídicí znak "\" pro zvláštní znaky v řetězci. Zobrazit [ukázka propojené služby a datové sady definice](#sample-linked-service-and-dataset-definitions) příklady.<br/><br/>Můžete zkombinovat tato vlastnost se **partitionBy** mít složku cesty založené na řez počátečním/koncovém data a časy. |Ano 
| fileName |Zadejte název souboru **folderPath** Pokud má tabulka, která má odkazovat na konkrétní soubor ve složce. Pokud je nezadávejte žádnou hodnotu pro tuto vlastnost, v tabulce odkazuje na všechny soubory ve složce.<br/><br/>Pokud není zadán název souboru pro výstupní datovou sadu, název generovaného souboru by měl být v následujícím tento formát: <br/><br/>Data. <Guid>.txt (například: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| fileFilter |Určete filtr, který slouží k výběru podmnožinu souborů v cestě folderPath, nikoli všech souborů.<br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklady 1: `"fileFilter": "*.log"`<br/>Příklad 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter platí pro vstupní datovou sadu sdílení souborů. Tato vlastnost není podporována s HDFS. |Ne |
| partitionedBy |partitionedBy slouží k určení dynamické folderPath, název souboru pro data časových řad. Například folderPath s parametry pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, ** ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formát](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**; a jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |
| useBinaryTransfer |Určete, jestli použít režim binární přenos. Hodnota true pro binárním režimu a false ASCII. Výchozí hodnota: True. Tuto vlastnost lze použít pouze v případě typu přidružené propojené služby typu: Server_ftp. |Ne |

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
Pokud se kopírování dat ze serveru FTP, nastavte **typ zdroje** aktivity kopírování na **FileSystemSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. |Hodnota TRUE, False (výchozí) |Ne |

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
K definování HDFS propojenou službu, nastavte **typ** propojené služby pro **Hdfs**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavená na: **Hdfs** |Ano |
| URL |Adresa URL HDFS |Ano |
| authenticationType. |Anonymní, nebo Windows. <br><br> Použití **ověřování protokolem Kerberos** konektor HDFS najdete v tématu [v této části](#use-kerberos-authentication-for-hdfs-connector) odpovídajícím způsobem nastavit v místním prostředí. |Ano |
| uživatelské jméno |Ověřování uživatelského jména pro Windows. |Ano (pro ověřování systému Windows) |
| heslo |Heslo pro ověřování Windows. |Ano (pro ověřování systému Windows) |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení k HDFS. |Ano |
| encryptedCredential |[Nové AzureRMDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactoryencryptvalue) výstup přihlašovacích údajů pro přístup. |Ne |

#### <a name="example-using-anonymous-authentication"></a>Příklad: Použití anonymní ověřování

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

#### <a name="example-using-windows-authentication"></a>Příklad: Použití ověřování Windows

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
K definování datové sady HDFS, nastavte **typ** datové sady na **sdílení souborů**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Cesta ke složce. Příklad: `myfolder`<br/><br/>Použijte řídicí znak "\" pro zvláštní znaky v řetězci. Příklad: folder\subfolder, určete složku\\\\podsložky a d:\samplefolder, zadejte d:\\\\ukázková_složka.<br/><br/>Můžete zkombinovat tato vlastnost se **partitionBy** mít složku cesty založené na řez počátečním/koncovém data a časy. |Ano |
| fileName |Zadejte název souboru **folderPath** Pokud má tabulka, která má odkazovat na konkrétní soubor ve složce. Pokud je nezadávejte žádnou hodnotu pro tuto vlastnost, v tabulce odkazuje na všechny soubory ve složce.<br/><br/>Pokud není zadán název souboru pro výstupní datovou sadu, název generovaného souboru by měl být v následujícím tento formát: <br/><br/>Data. <Guid>.txt (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy slouží k určení dynamické folderPath, název souboru pro data časových řad. Příklad: folderPath s parametry pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, ** ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formát](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

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
Pokud se kopírují data z HDFS, nastavte **typ zdroje** aktivity kopírování na **FileSystemSource**a zadejte následující vlastnosti v **zdroj** části:

**FileSystemSource** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. |Hodnota TRUE, False (výchozí) |Ne |

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
K definování protokolu SFTP propojenou službu, nastavte **typ** propojené služby pro **Sftp**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- | --- |
| hostitel | Název nebo IP adresa serveru SFTP. |Ano |
| port |Port, na kterém naslouchá SFTP server. Výchozí hodnota je: 21 |Ne |
| authenticationType. |Zadejte typ ověřování. Povolené hodnoty: **základní**, **SshPublicKey**. <br><br> Odkazovat na [použití základního ověřování](#using-basic-authentication) a [pomocí SSH ověření veřejného klíče](#using-ssh-public-key-authentication) oddíly na více vlastností a ukázky JSON v uvedeném pořadí. |Ano |
| skipHostKeyValidation | Určete, jestli chcete přeskočit ověřování klíče hostitele. | Ne. Výchozí hodnota: NEPRAVDA |
| Mělo | Zadejte hlas klíče hostitele. | Ano, pokud `skipHostKeyValidation` je nastavena na hodnotu false.  |
| Název brány |Název brány správy dat pro připojení místnímu serveru SFTP. | Ano, pokud se kopírování dat z místní server SFTP. |
| encryptedCredential | Šifrované přihlašovací údaje pro přístup k serveru SFTP. Automaticky generované při zadání základní ověřování (uživatelské jméno a heslo) nebo SshPublicKey ověřování (uživatelské jméno + privátní cesta ke klíči nebo obsah) v Průvodci kopírovat nebo dialogové okno automaticky otevírané okno ClickOnce. | Ne. Platí pouze při kopírování dat z místní server SFTP. |

#### <a name="example-using-basic-authentication"></a>Příklad: Použití základního ověřování

Chcete-li použít základní ověřování, nastavte `authenticationType` jako `Basic`a zadejte následující požadované vlastnosti kromě konektoru SFTP obecných představíme v poslední části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- | --- |
| uživatelské jméno | Uživatel, který má přístup k serveru SFTP. |Ano |
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

#### <a name="using-ssh-public-key-authentication"></a>**Použití ověřování pomocí veřejného klíče SSH:**

Chcete-li použít základní ověřování, nastavte `authenticationType` jako `SshPublicKey`a zadejte následující požadované vlastnosti kromě konektoru SFTP obecných představíme v poslední části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- | --- |
| uživatelské jméno |Uživatel, který má přístup k serveru SFTP |Ano |
| privateKeyPath | Zadejte absolutní cestu k souboru privátního klíče můžete přístup k této brány. | Zadejte, jestli `privateKeyPath` nebo `privateKeyContent`. <br><br> Platí pouze při kopírování dat z místní server SFTP. |
| privateKeyContent | Serializovaný řetězec soukromého klíče obsahu. Průvodce kopírováním může číst soubor privátního klíče a automaticky extrahovat obsah privátního klíče. Pokud používáte žádné další nástroj nebo sadu SDK, použijte vlastnost privateKeyPath. | Zadejte, jestli `privateKeyPath` nebo `privateKeyContent`. |
| přístupové heslo | Zadejte pass frázi/heslo k dešifrování privátního klíče, pokud soubor klíče je chráněn heslo. | Ano, pokud se soubor privátního klíče je chráněn heslo. |

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Příklad: **SshPublicKey ověřování pomocí privátního klíče obsahu**

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

Další informace najdete v tématu [konektor SFTP](data-factory-sftp-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
K definování datové sady SFTP, nastavte **typ** datové sady na **sdílení souborů**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Podřízená cesta ke složce. Použijte řídicí znak "\" pro zvláštní znaky v řetězci. Zobrazit [ukázka propojené služby a datové sady definice](#sample-linked-service-and-dataset-definitions) příklady.<br/><br/>Můžete zkombinovat tato vlastnost se **partitionBy** mít složku cesty založené na řez počátečním/koncovém data a časy. |Ano |
| fileName |Zadejte název souboru **folderPath** Pokud má tabulka, která má odkazovat na konkrétní soubor ve složce. Pokud je nezadávejte žádnou hodnotu pro tuto vlastnost, v tabulce odkazuje na všechny soubory ve složce.<br/><br/>Pokud není zadán název souboru pro výstupní datovou sadu, název generovaného souboru by měl být v následujícím tento formát: <br/><br/>Data. <Guid>.txt (například: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| fileFilter |Určete filtr, který slouží k výběru podmnožinu souborů v cestě folderPath, nikoli všech souborů.<br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklady 1: `"fileFilter": "*.log"`<br/>Příklad 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter platí pro vstupní datovou sadu sdílení souborů. Tato vlastnost není podporována s HDFS. |Ne |
| partitionedBy |partitionedBy slouží k určení dynamické folderPath, název souboru pro data časových řad. Například folderPath s parametry pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, ** ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formát](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |
| useBinaryTransfer |Určete, jestli použít režim binární přenos. Hodnota true pro binárním režimu a false ASCII. Výchozí hodnota: True. Tuto vlastnost lze použít pouze v případě typu přidružené propojené služby typu: Server_ftp. |Ne |

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

Další informace najdete v tématu [konektor SFTP](data-factory-sftp-connector.md#dataset-properties) článku. 

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud se kopírování dat z protokolu SFTP zdroje, nastavte **typ zdroje** aktivity kopírování na **FileSystemSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. |Hodnota TRUE, False (výchozí) |Ne |



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

Další informace najdete v tématu [konektor SFTP](data-factory-sftp-connector.md#copy-activity-properties) článku.


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Propojená služba
K definování HTTP propojenou službu, nastavte **typ** propojené služby pro **Http**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| url | Základní adresa URL webového serveru | Ano |
| authenticationType. | Určuje typ ověřování. Povolené hodnoty jsou: **anonymní**, **základní**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Naleznete v oddílech dál v této tabulce na další vlastnosti a ukázky JSON pro typy ověřování v uvedeném pořadí. | Ano |
| enableServerCertificateValidation | Určuje, zda povolit ověření certifikátu serveru SSL, pokud zdrojem je protokol HTTPS webového serveru | Ne, výchozí hodnota je true |
| Název brány | Název brány správy dat pro připojení ke zdroji v místním HTTP. | Ano, pokud se kopírování dat z místních zdroje HTTP. |
| encryptedCredential | Šifrované přihlašovací údaje pro přístup ke koncovému bodu HTTP. Automaticky generované při konfiguraci ověřovací údaje v Průvodci kopírovat nebo dialogové okno automaticky otevírané okno ClickOnce. | Ne. Platí pouze při kopírování dat z místní server HTTP. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Příklad: Používá ověřování Basic, Digest nebo Windows
Nastavte `authenticationType` jako `Basic`, `Digest`, nebo `Windows`a zadejte následující požadované vlastnosti kromě konektor HTTP obecných zavedené výše:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| uživatelské jméno | Uživatelské jméno pro přístup ke koncovému bodu HTTP. | Ano |
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

Chcete-li použít základní ověřování, nastavte `authenticationType` jako `ClientCertificate`a zadejte následující požadované vlastnosti kromě konektor HTTP obecných zavedené výše:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| embeddedCertData | Obsah s kódováním Base64 binární data souboru Personal Information Exchange (PFX). | Zadejte, jestli `embeddedCertData` nebo `certThumbprint`. |
| certThumbprint | Kryptografický otisk certifikátu, který byl nainstalován na počítači brány úložiště certifikátů. Platí pouze při kopírování dat z místních zdroje HTTP. | Zadejte, jestli `embeddedCertData` nebo `certThumbprint`. |
| heslo | Hesla přidruženého k certifikátu. | Ne |

Pokud používáte `certThumbprint` pro ověřování a certifikát, který je nainstalován v osobním úložišti místního počítače, je nutné udělit oprávnění pro čtení službě brány:

1. Spusťte konzolu Microsoft Management Console (MMC). Přidat **certifikáty** modul snap-in, který se zaměřuje **místního počítače**.
2. Rozbalte **certifikáty**, **osobní**a klikněte na tlačítko **certifikáty**.
3. Klikněte pravým tlačítkem na certifikát z osobního úložiště a vyberte **všechny úkoly**->**spravovat soukromé klíče...**
3. Na **zabezpečení** kartu, přidejte uživatelský účet, pod kterým je spuštěna služba hostitele brány správy dat s přístupem pro čtení k certifikátu.  

**Příklad: použití klientského certifikátu:** Tato propojená služba propojuje svou datovou továrnu místní webový server HTTP. Klientský certifikát, který je nainstalován na počítači s nainstalovanou bránu správy dat využívá.

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

#### <a name="example-using-client-certificate-in-a-file"></a>Příklad: použití klientský certifikát v souboru
Tato propojená služba propojuje svou datovou továrnu místní webový server HTTP. Používá soubor certifikátu klienta na počítači s brána správy dat nainstalovaná.

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

Další informace najdete v tématu [konektor HTTP](data-factory-http-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datová sada HTTP, nastavte **typ** datové sady na **Http**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data. Když není zadána cesta, použije se pouze adresu URL, které jsou určené v definici propojené služby. <br><br> K vytvoření dynamické adresy URL, můžete použít [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md), příklad: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Ne |
| requestMethod | Metoda protokolu HTTP. Povolené hodnoty jsou **získat** nebo **příspěvek**. | Ne. Výchozí hodnota je `GET`. |
| additionalHeaders | Další hlavičky požadavků HTTP. | Ne |
| Includesearchresults: true | Obsah žádosti protokolu HTTP. | Ne |
| Formát | Pokud chcete jednoduše **načtení dat z koncového bodu HTTP jako-je** bez parsování ho, přeskočte tento formát nastavení. <br><br> Pokud chcete analyzovat obsah odpovědi HTTP při kopírování, jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, ** OrcFormat**, **ParquetFormat**. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formát](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

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

#### <a name="example-using-the-post-method"></a>Příklad: použití metody POST

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
Další informace najdete v tématu [konektor HTTP](data-factory-http-connector.md#dataset-properties) článku.

### <a name="http-source-in-copy-activity"></a>Zdroj HTTP v aktivitě kopírování
Pokud se kopírování dat ze zdroje HTTP, nastavte **typ zdroje** aktivity kopírování na **HttpSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| httpRequestTimeout | Časový limit (TimeSpan) pro požadavek HTTP získat odpověď. Časový limit je získat odpověď, nevypršel časový limit pro čtení dat odpovědi. | Ne. Výchozí hodnota: 00:01:40 |


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

Další informace najdete v tématu [konektor HTTP](data-factory-http-connector.md#copy-activity-properties) článku.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Propojená služba
K definování OData propojenou službu, nastavte **typ** propojené služby pro **OData**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| url |Adresa URL služby OData. |Ano |
| authenticationType. |Typ ověřování používaný pro připojení ke zdroji OData. <br/><br/> Pro cloudové prostředí OData možné hodnoty jsou Anonymous, Basic a protokolem OAuth (Poznámka: momentálně se podporuje jenom podpory Azure Data Factory, Azure Active Directory na základě OAuth). <br/><br/> V místním prostředí OData možné hodnoty jsou Anonymous, Basic a Windows. |Ano |
| uživatelské jméno |Pokud používáte základní ověřování, zadejte uživatelské jméno. |Ano (pouze v případě, že používáte základní ověřování) |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ano (pouze v případě, že používáte základní ověřování) |
| authorizedCredential |Pokud používáte OAuth, klikněte na tlačítko **Authorize** tlačítko Průvodce kopírováním služby Data Factory nebo editoru a zadejte svoje přihlašovací údaje, pak hodnota této vlastnosti bude možné automaticky vygenerovat. |Ano (pouze v případě, že používáte ověřování OAuth) |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení ke službě OData s místními. Zadejte, pokud se kopírování dat ze zdroje v místním prostředí OData. |Ne |

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

#### <a name="example---using-anonymous-authentication"></a>Příklad: použití anonymní ověřování

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

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Příklad: použití ověřování OAuth přístup ke cloudovým zdroj OData
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

Další informace najdete v tématu [konektor OData](data-factory-odata-connector.md#linked-service-properties) článku.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datová sada služby OData, nastavte **typ** datové sady na **ODataResource**a zadejte následující vlastnosti v **typeProperties** části: 

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

Další informace najdete v tématu [konektor OData](data-factory-odata-connector.md#dataset-properties) článku.

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud se kopírování dat ze zdroje OData, nastavte **typ zdroje** aktivity kopírování na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Příklad: | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |"? $select = název, popis a $top = 5" |Ne |

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

Další informace najdete v tématu [konektor OData](data-factory-odata-connector.md#copy-activity-properties) článku.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Propojená služba
K definování ODBC propojenou službu, nastavte **typ** propojené služby pro **OnPremisesOdbc**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| připojovací řetězec |– Přístup k pověření část připojovací řetězec a případně zašifrované přihlašovací údaje. Příklady v následujících částech. |Ano |
| pověření |Část přístup přihlašovacích údajů z připojovacího řetězce zadaného ve vlastnosti specifické pro ovladač formátu. Příklad: "Uid =<user ID>; PWD =<password>; RefreshToken =<secret refresh token>; ". |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k úložišti dat rozhraní ODBC. Možné hodnoty jsou: anonymní a Basic. |Ano |
| uživatelské jméno |Pokud používáte základní ověřování, zadejte uživatelské jméno. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení k úložišti dat rozhraní ODBC. |Ano |

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
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Příklad: použití základního ověřování se zašifrovanými přihlašovacími údaji
Můžete šifrovat přihlašovací údaje pomocí [New-AzureRMDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactoryencryptvalue) rutiny (1.0 verzi prostředí Azure PowerShell) nebo [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9 nebo starší verzi nástroje Azure Prostředí PowerShell).  

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

#### <a name="example-using-anonymous-authentication"></a>Příklad: Použití anonymní ověřování

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

Další informace najdete v tématu [konektor ODBC](data-factory-odbc-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
K definování datové sady rozhraní ODBC, nastavte **typ** datové sady na **RelationalTable**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v úložišti dat rozhraní ODBC. |Ano |


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

Další informace najdete v tématu [konektor ODBC](data-factory-odbc-connector.md#dataset-properties) článku. 

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud se kopírování dat z úložiště dat rozhraní ODBC, nastavte **typ zdroje** aktivity kopírování na **RelationalSource**a zadejte následující vlastnosti v **zdroj** oddílu :

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ano |

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

Další informace najdete v tématu [konektor ODBC](data-factory-odbc-connector.md#copy-activity-properties) článku.

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Propojená služba
K definování Salesforce propojenou službu, nastavte **typ** propojené služby pro **Salesforce**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| environmentUrl | Zadejte adresu URL služby Salesforce instanci. <br><br> – Výchozí hodnota je "https://login.salesforce.com". <br> – Chcete-li kopírovat data z karantény, zadejte "https://test.salesforce.com". <br> -Ke zkopírování dat z vlastní domény, zadejte, například "https://[domain].my.salesforce.com". |Ne |
| uživatelské jméno |Zadejte uživatelské jméno uživatelského účtu. |Ano |
| heslo |Zadejte heslo pro uživatelský účet. |Ano |
| Token securityToken |Zadejte token zabezpečení pro uživatelský účet. Zobrazit [získal token zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) pokyny o tom, jak resetování/get token zabezpečení. Obecné informace o tokeny zabezpečení najdete v tématu [zabezpečení a rozhraní API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ano |

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

Další informace najdete v tématu [konektor Salesforce](data-factory-salesforce-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
Chcete-li definovat datová sada Salesforce, nastavte **typ** datové sady na **RelationalTable**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v Salesforce. |Ne (Pokud **dotazu** z **RelationalSource** určena) |

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

Další informace najdete v tématu [konektor Salesforce](data-factory-salesforce-connector.md#dataset-properties) článku. 

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud se kopírování dat ze služby Salesforce, nastavte **typ zdroje** aktivity kopírování na **RelationalSource**a zadejte následující vlastnosti v **zdroj** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |Dotaz SQL 92 nebo [Salesforce objektu dotazu jazyka (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) dotazu. Například `select * from MyTable__c`. |Ne (Pokud **tableName** z **datovou sadu** určena) |

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

Další informace najdete v tématu [konektor Salesforce](data-factory-salesforce-connector.md#copy-activity-properties) článku. 

## <a name="web-data"></a>Data webů 

### <a name="linked-service"></a>Propojená služba
K definování webové propojené služby, nastavte **typ** propojené služby pro **webové**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| URL |Adresa URL k webovému zdroji |Ano |
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

Další informace najdete v tématu [Webová tabulka konektor](data-factory-web-table-connector.md#linked-service-properties) článku. 

### <a name="dataset"></a>Datová sada
K definování datové sady Web, nastavte **typ** datové sady na **WebTable**a zadejte následující vlastnosti v **typeProperties** části: 

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Typ datové sady. musí být nastaveno na **WebTable** |Ano |
| path |Relativní adresa URL k prostředku, který obsahuje tabulku. |Ne. Když není zadána cesta, použije se pouze adresu URL, které jsou určené v definici propojené služby. |
| index |Index tabulky ve zdroji. Zobrazit [Get index tabulky na stránce HTML](#get-index-of-a-table-in-an-html-page) najdete kroky pro získání indexu tabulky na stránce HTML. |Ano |

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

Další informace najdete v tématu [Webová tabulka konektor](data-factory-web-table-connector.md#dataset-properties) článku. 

### <a name="web-source-in-copy-activity"></a>Webový zdroj v aktivitě kopírování
Pokud data kopírujete text z Webová tabulka, nastavte **typ zdroje** aktivity kopírování na **WebSource**. Pokud v současné době zdroje v aktivitě kopírování je typu **WebSource**, jsou podporovány žádné další vlastnosti.

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

Další informace najdete v tématu [Webová tabulka konektor](data-factory-web-table-connector.md#copy-activity-properties) článku. 

## <a name="compute-environments"></a>VÝPOČETNÍ PROSTŘEDÍ
Následující tabulka uvádí podporované objektu pro vytváření dat a aktivity transformace, které v nich dají spustit výpočetní prostředí. Klikněte na odkaz pro výpočetní prostředky, které vás zajímají zobrazíte schémat JSON pro propojenou službu, která ho propojit s datovou továrnou. 

| Výpočetní prostředí | Aktivity |
| --- | --- |
| [Cluster HDInsight na vyžádání](#on-demand-azure-hdinsight-cluster) nebo [vlastní cluster HDInsight](#existing-azure-hdinsight-cluster) |[Vlastní aktivita .NET](#net-custom-activity), [aktivitu Hivu](#hdinsight-hive-activity), [aktivita Pig](#hdinsight-pig-activity), [aktivita MapReduce](#hdinsight-mapreduce-activity), [Hadoop, streamování aktivity](#hdinsight-streaming-activityd), [Aktivity spark](#hdinsight-spark-activity) |
| [Služba Azure Batch](#azure-batch) |[Vlastní aktivita .NET](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Aktivita provedení dávky služby Machine Learning](#machine-learning-batch-execution-activity), [aktivita aktualizace prostředku služby Machine Learning](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[U-SQL Data Lake Analytics](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database-1), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-1), [systému SQL Server](#sql-server-1) |[Uložená procedura](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Cluster Azure HDInsight na vyžádání
Služba Azure Data Factory můžete automaticky vytvořit Windows/linuxem clusteru HDInsight na vyžádání zpracovávat data. Vytvoření clusteru ve stejné oblasti jako účet úložiště (vlastnost linkedServiceName v kódu JSON) přidružené ke clusteru. Můžete spustit následující aktivity transformace na tuto propojenou službu: [vlastní aktivita .NET](#net-custom-activity), [aktivitu Hivu](#hdinsight-hive-activity), [aktivita Pig](#hdinsight-pig-activity), [MapReduce aktivita](#hdinsight-mapreduce-activity), [Hadoop, streamování aktivity](#hdinsight-streaming-activityd), [aktivitu Spark](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Propojená služba 
Následující tabulka obsahuje popis vlastností použitých v definici JSON pro Azure HDInsight, propojené služby na vyžádání.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type by měla být nastavená na **HDInsightOnDemand**. |Ano |
| clusterSize |Počet pracovních procesů a datových uzlů do clusteru. Vytvoření clusteru HDInsight s 2 hlavní uzly spolu s počtem uzlů pracovního procesu, kterou zadáte pro tuto vlastnost. Uzly mají velikost Standard_D3, který má 4 jádra, 4 pracovní uzel clusteru trvá 24 jader (4\*4 = 16 jader pro pracovní uzly a navíc 2\*4 = 8 jader pro hlavní uzly). Zobrazit [založených na Linuxu vytvoření Hadoop clusterů v HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) podrobnosti na úrovni Standard_D3. |Ano |
| TimeToLive |Povolené prodlevy pro cluster HDInsight na vyžádání. Určuje, jak dlouho zůstane aktivní cluster HDInsight na vyžádání po dokončení aktivity spustit, pokud v clusteru nejsou žádné aktivní úlohy.<br/><br/>Například pokud spuštění aktivit trvá 6 minut a timetolive nastavena na 5 minut, clusteru zůstává aktivní po 5 minutách od 6 minut výpočetního aktivity spustit. Pokud se spuštění další aktivity provádí s oknem 6 minut, je zpracován programovacím modelem stejného clusteru.<br/><br/>Vytváření clusteru HDInsight na vyžádání je náročná operace (akce může trvat), takže použití tohoto nastavení jako potřebné ke zlepšení výkonu služby data factory pomocí opakovaného použití clusteru služby HDInsight na vyžádání.<br/><br/>Pokud hodnota timetolive nastavíte na 0, cluster odstraní jako aktivita spustit zpracované. Na druhé straně Pokud nastavíte vysokou hodnotu, clusteru může zůstat nečinná, zbytečně výsledkem vysoké náklady. Proto je důležité, že nastavíte příslušnou hodnotu na základě vašich potřeb.<br/><br/>Více kanálů můžou sdílet stejnou instanci clusteru HDInsight na vyžádání, pokud je odpovídajícím způsobem nastavit hodnoty vlastnosti timetolive |Ano |
| verze |Verze clusteru HDInsight. Podrobnosti najdete v tématu [podporované verze HDInsight ve službě Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Ne |
| linkedServiceName |Propojená služba clusteru na vyžádání používané pro ukládání a zpracování dat v Azure Storage. <p>V současné době nelze vytvořit cluster HDInsight na vyžádání, která používá Azure Data Lake Store jako úložiště. Pokud chcete uložit výsledných dat z HDInsight zpracování do Azure Data Lake Store, použijte aktivitu kopírování ke zkopírování dat z Azure Blob Storage do Azure Data Lake Store.</p>  | Ano |
| additionalLinkedServiceNames |Určuje další účty úložiště pro HDInsight propojenou službu tak, aby služba Data Factory můžete zaregistrovat vaším jménem. |Ne |
| osType |Typ operačního systému. Povolené hodnoty jsou: (výchozí) Windows a Linux |Ne |
| hcatalogLinkedServiceName |Název serveru Azure SQL propojenou službu, které odkazují na databázi HCatalog. Cluster HDInsight na vyžádání se vytvoří s využitím Azure SQL database jako metastore. |Ne |

### <a name="json-example"></a>Příklad JSON
Následující kód JSON určuje HDInsight propojené služby na vyžádání založené na Linuxu. Služba Data Factory automaticky vytvoří **založených na Linuxu** clusteru HDInsight při zpracování datový řez. 

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

Další informace najdete v tématu [propojené služby Compute](data-factory-compute-linked-services.md) článku. 

## <a name="existing-azure-hdinsight-cluster"></a>Existující cluster Azure HDInsight
Můžete vytvořit služby Azure HDInsight propojené můžete zaregistrovat vlastní cluster HDInsight s Data Factory. Můžete spustit následující aktivity transformace dat v této propojené službě: [vlastní aktivita .NET](#net-custom-activity), [aktivitu Hivu](#hdinsight-hive-activity), [aktivita Pig](#hdinsight-pig-activity), [ Aktivita MapReduce](#hdinsight-mapreduce-activity), [Hadoop, streamování aktivity](#hdinsight-streaming-activityd), [aktivitu Spark](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Propojená služba
Následující tabulka obsahuje popis vlastností použitých v definici Azure JSON HDInsight Azure propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type by měla být nastavená na **HDInsight**. |Ano |
| clusterUri |Identifikátor URI clusteru HDInsight. |Ano |
| uživatelské jméno |Zadejte jméno uživatele, který se má použít pro připojení do existujícího clusteru HDInsight. |Ano |
| heslo |Zadejte heslo pro uživatelský účet. |Ano |
| linkedServiceName | Název propojenou službu Azure Storage, který odkazuje na úložiště objektů blob v Azure používá HDInsight cluster. <p>V současné době nelze zadat, že že Azure Data Lake Store propojené služby pro tuto vlastnost. Získávají přístup k datům v Azure Data Lake Store pomocí skriptů Hive a Pig Pokud HDInsight cluster má přístup k Data Lake Store. </p>  |Ano |

Verze podporováno clusterů HDInsight najdete v tématu [podporované verze HDInsight](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). 

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
Můžete vytvořit služby propojené služby Azure Batch můžete zaregistrovat fondu služby Batch virtuálních počítačů (VM) pomocí služby data factory. Můžete spouštět vlastní aktivity .NET pomocí Azure HDInsight nebo Azure Batch. Můžete spustit [vlastní aktivita .NET](#net-custom-activity) v této propojené službě. 

### <a name="linked-service"></a>Propojená služba
Následující tabulka obsahuje popis vlastností použitých v definici Azure JSON služby propojené služby Azure Batch.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type by měla být nastavená na **AzureBatch**. |Ano |
| název účtu |Název účtu služby Azure Batch. |Ano |
| accessKey |Přístupový klíč pro účet Azure Batch. |Ano |
| poolName |Název fondu virtuálních počítačů. |Ano |
| linkedServiceName |Název služby Azure Storage propojená služba spojené s touto službou propojené služby Azure Batch. Tato propojená služba se používá pro pracovní soubory potřebné ke spuštění aktivity a ukládání protokolů spouštění aktivit. |Ano |


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
Vytvoření služby Azure Machine Learning propojený zaregistrovat Machine Learning dávkového vyhodnocování koncového bodu pomocí služby data factory. Aktivity transformace dat dvě, které můžou běžet na této propojené službě: [aktivita provedení dávky služby Machine Learning](#machine-learning-batch-execution-activity), [aktivita prostředku aktualizace Machine Learning](#machine-learning-update-resource-activity). 

### <a name="linked-service"></a>Propojená služba
Následující tabulka obsahuje popis vlastností použitých v definici Azure JSON ze služby Azure Machine Learning propojený.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| Typ |Vlastnost type by měla být nastavená na: **AzureML**. |Ano |
| mlEndpoint |Adresu URL dávkového bodování. |Ano |
| ApiKey |Rozhraní API publikované pracovního prostoru modelu. |Ano |

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
Vytváření **Azure Data Lake Analytics** propojená služba Azure Data Lake Analytics výpočetní služby Azure data Factory teprve pak [aktivita Data Lake Analytics U-SQL](data-factory-usql-activity.md) v kanálu.

### <a name="linked-service"></a>Propojená služba

Následující tabulka obsahuje popis vlastností použitých v definici JSON služby Azure Data Lake Analytics propojený. 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| Typ |Vlastnost type by měla být nastavená na: **AzureDataLakeAnalytics**. |Ano |
| název účtu |Název účtu Azure Data Lake Analytics. |Ano |
| dataLakeAnalyticsUri |Identifikátor URI, Azure Data Lake Analytics. |Ne |
| Autorizace |Autorizační kód se načte automaticky po kliknutí na tlačítko **Authorize** tlačítko v editoru služby Data Factory a dokončí se přihlášení OAuth. |Ano |
| subscriptionId |Id předplatného Azure |Ne (když není určeno, předplatné objektu pro vytváření dat se používá). |
| resourceGroupName |Název skupiny prostředků Azure |Ne (když není určeno, skupina prostředků objektu pro vytváření dat se používá). |
| ID relace |id relace z autorizační relace OAuth. Každé id relace je jedinečný a může být použit pouze jednou. Při použití editoru služby Data Factory toto ID se generuje automaticky. |Ano |


#### <a name="json-example"></a>Příklad JSON
Následující příklad uvádí definici JSON pro Azure Data Lake Analytics propojenou službu.

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
Vytvoření propojené služby Azure SQL a použít je s [aktivity uložené procedury](#stored-procedure-activity) vyvolat uloženou proceduru z kanálu Data Factory. 

### <a name="linked-service"></a>Propojená služba
Definovat službu Azure SQL Database propojenou službu, nastavte **typ** propojené služby pro **AzureSqlDatabase**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci Azure SQL Database pro vlastnost připojovací řetězec. |Ano |

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

Zobrazit [konektor služby Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties) , kde najdete podrobnosti o této propojené službě.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Vytvoření služby Azure SQL Data Warehouse propojené a použít je s [aktivity uložené procedury](data-factory-stored-proc-activity.md) vyvolat uloženou proceduru z kanálu Data Factory. 

### <a name="linked-service"></a>Propojená služba
Definovat službu Azure SQL Data Warehouse propojenou službu, nastavte **typ** propojené služby pro **AzureSqlDW**a zadejte následující vlastnosti v **typeProperties** části:  

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci Azure SQL Data Warehouse pro vlastnost připojovací řetězec. |Ano |

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
Vytvoření propojené služby SQL serveru a použít je s [aktivity uložené procedury](data-factory-stored-proc-activity.md) vyvolat uloženou proceduru z kanálu Data Factory. 

### <a name="linked-service"></a>Propojená služba
Vytvoření propojené služby typu **OnPremisesSqlServer** propojení k místní databázi SQL serveru do služby data factory. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro místní propojené služby SQL serveru.

Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro propojenou službu SQL serveru.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type by měla být nastavená na: **OnPremisesSqlServer**. |Ano |
| připojovací řetězec |Zadejte připojovací řetězec informace potřebné pro připojení k místní databázi SQL serveru pomocí ověřování SQL nebo ověřování Windows. |Ano |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení k místní databázi SQL serveru. |Ano |
| uživatelské jméno |Pokud používáte ověřování Windows, zadejte uživatelské jméno. Příklad: **domainname\\uživatelské jméno**. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |

Můžete k šifrování přihlašovacích údajů a **New-AzureRmDataFactoryEncryptValue** rutiny a jejich použití v připojovacím řetězci, jak je znázorněno v následujícím příkladu (**EncryptedCredential** vlastnost):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Příklad: JSON pro použití ověřování SQL

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
#### <a name="example-json-for-using-windows-authentication"></a>Příklad: JSON pro použití ověřování Windows

Pokud uživatelské jméno a heslo nejsou zadány, budou brána používá zosobnění zadaný uživatelský účet pro připojení k místní databázi systému SQL Server. Jinak brána připojí k serveru SQL Server přímo s kontextem zabezpečení brány (jeho účet po spuštění).

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

Další informace najdete v tématu [konektor SQL serveru](data-factory-sqlserver-connector.md#linked-service-properties) článku.

## <a name="data-transformation-activities"></a>AKTIVITY TRANSFORMACE DAT

Aktivita | Popis
-------- | -----------
[Aktivita HDInsight Hive](#hdinsight-hive-activity) | Aktivita HDInsight Hive v kanálu služby Data Factory provádí dotazy Hive sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. 
[Aktivita Pig s HDInsight](#hdinsight-pig-activity) | Aktivita Pig s HDInsight v kanálech Data Factory spustí dotazy Pig sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání.
[Aktivita MapReduce služby HDInsight](#hdinsight-mapreduce-activity) | Aktivita HDInsight MapReduce v kanálech Data Factory provádí programů MapReduce sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání.
[Aktivita Streamování služby HDInsight](#hdinsight-streaming-activity) | Aktivita HDInsight streamování v kanálech Data Factory provádí streamování Hadoop programy sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání.
[Aktivita Sparku služby HDInsight](#hdinsight-spark-activity) | HDInsight Spark aktivitu v kanálu služby Data Factory provádí programů Spark v clusteru HDInsight. 
[Aktivita Provedení dávky služby Machine Learning](#machine-learning-batch-execution-activity) | Azure Data Factory umožňuje snadno vytvářet kanály, které používají publikované webové služby Azure Machine Learning pro prediktivní analýzy. Aktivita provedení dávky služby v kanálu Azure Data Factory, můžete vyvolat webové služby Machine Learning k vytváření predikcí na data ve službě batch. 
[Aktivita Aktualizace prostředků služby Machine Learning](#machine-learning-update-resource-activity) | V průběhu času prediktivních modelů ve službě Machine Learning, experimenty vyhodnocování musí být retrained pomocí nové vstupní datové sady. Jakmile budete hotovi s přetrénování, budete chtít aktualizovat hodnoticí webové služby s modelem Machine Learning retrained. Aktivita aktualizace prostředku můžete použít k aktualizaci webové služby s nově trénovaného modelu.
[Aktivita Uložená procedura](#stored-procedure-activity) | Aktivity uložené procedury v kanálech Data Factory můžete použít k vyvolání uložené procedury v jedné z následujících úložiště dat: Azure SQL Database, Azure SQL Data Warehouse, databáze SQL serveru ve vašem podniku nebo Virtuálním počítači Azure. 
[Aktivita data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) | Aktivita U-SQL služby data Lake Analytics spouští skript U-SQL v clusteru služby Azure Data Lake Analytics.  
[Vlastní aktivita .NET](#net-custom-activity) | Pokud potřebujete transformovat data způsobem, který není podporován službou Data Factory, můžete vytvořit vlastní aktivitu s vlastní logiku zpracování dat a použijte aktivitu v kanálu. Můžete vytvořit vlastní aktivitu .NET pro spuštění pomocí služby Azure Batch nebo cluster Azure HDInsight. 

     
## <a name="hdinsight-hive-activity"></a>Aktivita Hivu služby HDInsight
V definici JSON aktivity Hive můžete zadat následující vlastnosti. Vlastnost typu aktivity musí být: **HDInsightHive**. Musíte nejprve vytvořit HDInsight propojené služby a zadejte název ji jako hodnotu **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **typeProperties** části Pokud nastavíte typ aktivity HDInsightHive:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| skript |Zadejte vložený skript Hive |Ne |
| Cesta ke skriptu |Store skriptu Hivu ve službě Azure blob storage a zadejte cestu k souboru. Vlastnost 'script' nebo "scriptPath". Obě nelze použít společně. Název souboru je velká a malá písmena. |Ne |
| definuje |Zadejte parametry jako páry klíč/hodnota pro odkazování v rámci skriptu Hive pomocí "hiveconf. |Ne |

Tyto vlastnosti typu jsou specifická pro aktivitu Hive. Další vlastnosti (mimo části typeProperties) jsou podporované pro všechny aktivity.   

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

Další informace najdete v tématu [aktivita Hive](data-factory-hive-activity.md) článku. 

## <a name="hdinsight-pig-activity"></a>Aktivita Pig služby HDInsight
Zadejte následující vlastnosti v definici JSON aktivity Pig. Vlastnost typu aktivity musí být: **HDInsightPig**. Musíte nejprve vytvořit HDInsight propojené služby a zadejte název ji jako hodnotu **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **typeProperties** oddílu typu aktivity nastavená na HDInsightPig: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| skript |Zadejte vložený skript Pig |Ne |
| Cesta ke skriptu |Skript Pig Store ve službě Azure blob storage a zadejte cestu k souboru. Vlastnost 'script' nebo "scriptPath". Obě nelze použít společně. Název souboru je velká a malá písmena. |Ne |
| definuje |Zadejte parametry jako páry klíč/hodnota pro odkazování v rámci skript Pig |Ne |

Tyto vlastnosti typu jsou specifická pro aktivitu Pig. Další vlastnosti (mimo části typeProperties) jsou podporované pro všechny aktivity.   

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

Další informace najdete v tématu [aktivita Pig](#data-factory-pig-activity.md) článku. 

## <a name="hdinsight-mapreduce-activity"></a>Aktivita MapReduce služby HDInsight
V definici JSON aktivity MapReduce můžete zadat následující vlastnosti. Vlastnost typu aktivity musí být: **HDInsightMapReduce**. Musíte nejprve vytvořit HDInsight propojené služby a zadejte název ji jako hodnotu **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **typeProperties** oddílu typu aktivity nastavená na HDInsightMapReduce: 

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jarLinkedService | Název propojené služby pro službu Azure Storage, který obsahuje soubor JAR. | Ano |
| jarFilePath | Cesta k souboru JAR ve službě Azure Storage. | Ano | 
| Název třídy | Název hlavní třída v souboru JAR. | Ano | 
| argumenty | Seznam argumentů pro MapReduce program s hodnotami oddělenými čárkou. Za běhu, se zobrazí několik dalších argumentů (například: mapreduce.job.tags) z rozhraní MapReduce. K rozlišení vaše argumenty s argumenty MapReduce, zvažte použití možnosti a hodnoty jako argumenty, jak je znázorněno v následujícím příkladu (- s, – vstup, – výstup atd., jsou možnosti bezprostředně následovat jejich hodnoty) | Ne | 

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

Další informace najdete v tématu [aktivita MapReduce](data-factory-map-reduce.md) článku. 

## <a name="hdinsight-streaming-activity"></a>Aktivita Streamování služby HDInsight
V definici JSON aktivity streamování Hadoop můžete zadat následující vlastnosti. Vlastnost typu aktivity musí být: **HDInsightStreaming**. Musíte nejprve vytvořit HDInsight propojené služby a zadejte název ji jako hodnotu **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **typeProperties** oddílu typu aktivity nastavená na HDInsightStreaming: 

| Vlastnost | Popis | 
| --- | --- |
| mapování | Název spustitelného souboru mapování. V tomto příkladu je cat.exe Mapovač spustitelný soubor.| 
| redukční funkci | Název spustitelného souboru redukční funkci. V tomto příkladu je wc.exe redukční funkci spustitelný soubor. | 
| vstup | Vstupní soubor (včetně umístění) pro mapovač. Příklad: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample je kontejner objektů blob, příklad/data/Gutenberg je složka, a davinci.txt objektu blob. |
| output | Výstupní soubor (včetně umístění) pro redukční funkci. Výstup úlohy streamování Hadoop je zapsán do umístění určeného pro tuto vlastnost. |
| filePaths | Cesty pro spustitelné soubory mapovací a redukční funkci. Příklad: "adfsample/example/apps/wc.exe" adfsample je kontejner objektů blob, příklad/aplikace je složka, a wc.exe spustitelný soubor. | 
| fileLinkedService | Propojená služba Azure Storage, který představuje úložiště Azure, který obsahuje soubory zadané v části filePaths. | 
| argumenty | Seznam argumentů pro MapReduce program s hodnotami oddělenými čárkou. Za běhu, se zobrazí několik dalších argumentů (například: mapreduce.job.tags) z rozhraní MapReduce. K rozlišení vaše argumenty s argumenty MapReduce, zvažte použití možnosti a hodnoty jako argumenty, jak je znázorněno v následujícím příkladu (- s, – vstup, – výstup atd., jsou možnosti bezprostředně následovat jejich hodnoty) | 
| getDebugInfo | Volitelný prvek. Pokud je nastaven na chybu, protokoly se stáhnou pouze při selhání. Pokud je nastavená na všechny, protokoly budou staženy vždy bez ohledu na stav spuštění. | 

> [!NOTE]
> Je nutné zadat pro streamované aktivitě Hadoop pro výstupní datovou sadu **výstupy** vlastnost. Tato datová sada může být jen fiktivní datovou sadu, která je nutná Centrum umožňující prosazovat kanálu plánu (každou hodinu, každý den atd.). Pokud aktivita nemá vstupní, můžete přeskočit zadávání vstupní datovou sadu aktivity pro **vstupy** vlastnost.  

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
Zadejte následující vlastnosti v definici JSON aktivity Spark. Vlastnost typu aktivity musí být: **HDInsightSpark**. Musíte nejprve vytvořit HDInsight propojené služby a zadejte název ji jako hodnotu **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **typeProperties** oddílu typu aktivity nastavená na HDInsightSpark: 

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| rootPath | Kontejner objektů Blob v Azure a složku obsahující soubor Spark. Název souboru je velká a malá písmena. | Ano |
| entryFilePath | Relativní cesta ke kořenové složce kódu nebo balíčku, Spark. | Ano |
| Název třídy | Hlavní třída Java/Spark vaší aplikace | Ne | 
| argumenty | Seznam argumentů příkazového řádku pro program Sparku. | Ne | 
| proxyUser | Uživatelský účet zosobnění spuštění programu Sparku | Ne | 
| sparkConfig | Vlastnosti konfigurace Spark. | Ne | 
| getDebugInfo | Určuje, kdy soubory protokolu Spark se zkopírují do služby Azure storage používá HDInsight cluster (nebo) je uvedli v sparkJobLinkedService. Povolené hodnoty: None, vždy nebo selhání. Výchozí hodnota: žádný. | Ne | 
| sparkJobLinkedService | Azure Storage propojenou službu, která obsahuje Spark soubor úlohy, závislosti a protokoly.  Pokud žádnou hodnotu pro tuto vlastnost nezadáte, použije se úložiště spojené s clusterem HDInsight. | Ne |

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
- **RootPath** je nastavena na **adfspark\\pyFiles** kde adfspark je kontejner objektů Blob v Azure a pyFiles je jemné složky v tomto kontejneru. V tomto příkladu Azure Blob Storage je ten, který je spojen s clusterem Spark. Nahrajte soubor do jiné služby Azure Storage. Pokud tak učiníte, vytvořte propojenou službu Azure Storage k propojení účtu úložiště do služby data factory. Zadejte název propojené služby, jako hodnotu **sparkJobLinkedService** vlastnost. Zobrazit [vlastnosti aktivity Spark](#spark-activity-properties) podrobnosti o této vlastnosti a dalších vlastností podporovaných aktivitou Sparku.
- **EntryFilePath** je nastavena na **test.py**, což je soubor pythonu. 
- **GetDebugInfo** je nastavena na **vždy**, což znamená, že soubory protokolu budou vždy generována (úspěch nebo neúspěch).  

    > [!IMPORTANT]
    > Doporučujeme vám, že nenastavíte tato vlastnost vždy v produkčním prostředí Pokud řešíte problém. 
- **Výstupy** oddíl má jednu výstupní datovou sadu. Výstupní datové sady je nutné zadat i v případě, že program sparku nevytváří žádný výstup. Výstupní datovou sadu řídí plán kanálu (každou hodinu, každý den atd.).

Další informace o aktivitě najdete v tématu [aktivitu Spark](data-factory-spark.md) článku.  

## <a name="machine-learning-batch-execution-activity"></a>Aktivita Provedení dávky služby Machine Learning
Zadejte následující vlastnosti v Azure ML Batch spuštění aktivity JSON definice. Vlastnost typu aktivity musí být: **AzureMLBatchExecution**. Musíte vytvořit Azure Machine Learning nejprve propojené služby a zadejte název ji jako hodnotu **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **typeProperties** oddílu typu aktivity nastavená na AzureMLBatchExecution:

Vlastnost | Popis | Požaduje se 
-------- | ----------- | --------
webServiceInput | Datové sady mají být předány jako vstup pro webovou službu Azure ML. Tato datová sada musí být součástí vstupy pro aktivitu. |Použijte webServiceInput nebo webServiceInputs. | 
webServiceInputs | Zadejte datové sady mají být předány jako vstupy pro webovou službu Azure ML. Pokud webová služba přijímá více vstupů, použijte vlastnost webServiceInputs namísto použití vlastnost webServiceInput. Datové sady, které odkazují **webServiceInputs** musí také obsahovat aktivity **vstupy**. | Použijte webServiceInput nebo webServiceInputs. | 
webServiceOutputs | Datové sady, které jsou přiřazené jako výstup pro webovou službu Azure ML. Webová služba vrátí výstupní data v této datové sadě. | Ano | 
globalParameters | Zadejte hodnoty parametrů webové služby v této části. | Ne | 

### <a name="json-example"></a>Příklad JSON
V tomto příkladu má datovou sadu aktivita **MLSqlInput** jako vstup a **MLSqlOutput** jako výstup. **MLSqlInput** je předán jako vstup do webové služby s použitím **webServiceInput** vlastnost JSON. **MLSqlOutput** je předán jako výstup webové služby s použitím **webServiceOutputs** vlastnost JSON. 

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

V tomto příkladu JSON v nasazované službě Azure Machine Learning Web používá čtečky a zapisovače modulu pro čtení a zápis dat z/do Azure SQL Database. Tato webová služba poskytuje následující čtyři parametry: název serveru, název databáze, název serveru uživatelského účtu a heslo uživatelského účtu serveru databáze.

> [!NOTE]
> Pouze vstupů a výstupů aktivity AzureMLBatchExecution lze předat jako parametry webové služby. Například ve výše uvedeném fragmentu JSON MLSqlInput je vstupní hodnota pro AzureMLBatchExecution aktivity, které je předáno jako vstup do webové služby prostřednictvím parametru webServiceInput.

## <a name="machine-learning-update-resource-activity"></a>Aktivita aktualizace prostředku služby Machine Learning
V definici Azure ML aktualizace prostředků zápis JSON aktivity můžete zadat následující vlastnosti. Vlastnost typu aktivity musí být: **AzureMLUpdateResource**. Musíte vytvořit Azure Machine Learning nejprve propojené služby a zadejte název ji jako hodnotu **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **typeProperties** části Pokud nastavíte typ aktivity AzureMLUpdateResource:

Vlastnost | Popis | Požaduje se 
-------- | ----------- | --------
Hodnota trainedModelName | Název retrained modelu. | Ano |  
trainedModelDatasetName | Datová sada odkazuje na soubor iLearner, který vrátil retraining operací. | Ano | 

### <a name="json-example"></a>Příklad JSON
Kanál má dvě aktivity: **AzureMLBatchExecution** a **AzureMLUpdateResource**. Aktivita provedení dávky služby Azure ML trvá trénovací data jako vstup a vytvoří soubor iLearner jako výstup. Aktivita vyvolá školení webové služby (výukového experimentu vystavena jako webové služby) se vstupní trénovacích dat a soubor ilearner, který obdrží z webové služby. PlaceholderBlob je jenom fiktivní výstupní datovou sadu, která požaduje služba Azure Data Factory ke spuštění kanálu.


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
Zadejte následující vlastnosti v definici JSON aktivity U-SQL. Vlastnost typu aktivity musí být: **DataLakeAnalyticsU SQL**. Musíte vytvořit službu Azure Data Lake Analytics propojené a zadejte název ji jako hodnotu **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **typeProperties** oddílu typu aktivity nastavená DataLakeAnalyticsU-SQL: 

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| ScriptPath |Cesta ke složce, která obsahuje skript U-SQL. Název souboru je velká a malá písmena. |Ne (když je pomocí skriptu) |
| scriptLinkedService |Propojené služby, která propojuje úložiště, který obsahuje skript do služby data factory |Ne (když je pomocí skriptu) |
| skript |Zadejte místo určení scriptPath a scriptLinkedService zpracování vloženého skriptu. Příklad: "skript": "Vytvořit databázi test". |Ne (když použijete scriptPath a scriptLinkedService) |
| z degreeOfParallelism |Maximální počet uzlů najednou použitý ke spuštění úlohy. |Ne |
| priorita |Určuje, které z uložených ve frontě úloh by měl být vybrané ke spuštění první. Čím nižší je číslo, tím vyšší je priorita. |Ne |
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

Další informace najdete v tématu [aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md). 

## <a name="stored-procedure-activity"></a>Aktivita Uložená procedura
Zadejte následující vlastnosti v definici zápis JSON aktivity uložené procedury. Vlastnost typu aktivity musí být: **SqlServerStoredProcedure**. Musíte vytvořit některý z následujících propojených služeb a jako hodnotu uvést název propojené služby **linkedServiceName** vlastnost:

- SQL Server 
- Azure SQL Database
- Azure SQL Data Warehouse

Následující vlastnosti jsou podporovány v **typeProperties** oddílu typu aktivity nastavená na SqlServerStoredProcedure:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| storedProcedureName |Zadejte název uložené procedury v databázi Azure SQL nebo Azure SQL Data Warehouse, která je reprezentována propojené služby, který používá výstupní tabulky. |Ano |
| storedProcedureParameters |Zadejte hodnoty parametrů uložené procedury. Pokud je potřeba pro parametr předat hodnotu null, použijte syntaxi: "param1": null (malými písmeny). Najdete v následující ukázce se dozvíte o použití této vlastnosti. |Ne |

Pokud chcete zadat vstupní datovou sadu, musí být k dispozici (ve stavu "Připraveno") pro spuštění aktivity uložené procedury. Vstupní datovou sadu nejde použít v uložené proceduře jako parametr. Používá se pouze ke kontrole závislost před spuštěním aktivity uložených procedur. Je nutné zadat výstupní datovou sadu aktivity uložené procedury. 

Výstupní datová sada Určuje **plán** pro aktivitu uložené procedury (každou hodinu, každý týden, měsíc, atd.). Výstupní datová sada musí používat **propojená služba** , který odkazuje na službu Azure SQL Database nebo Azure SQL Data Warehouse nebo databázi SQL Server, ve kterém chcete spustit uloženou proceduru. Výstupní datová sada může sloužit jako způsob, jak předat výsledek úložnou proceduru pro následné zpracování pomocí další aktivity ([řetězení aktivit](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) v kanálu. Ale Data Factory nezapisuje automaticky výstup z uložené procedury do tohoto objektu dataset. Je uložená procedura, která zapisuje do výstupní datové sady odkazující na tabulku SQL. V některých případech může být výstupní datovou sadu **fiktivní datovou sadu**, který se používá jenom k určení plánu pro spuštěnou aktivitu uložené procedury.  

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
Zadejte následující vlastnosti v vlastní aktivitu .NET definice JSON. Vlastnost typu aktivity musí být: **DotNetActivity**. Je nutné vytvořit službu Azure HDInsight, propojené nebo Azure Batch, propojené služby a jako hodnotu uvést název propojené služby **linkedServiceName** vlastnost. Následující vlastnosti jsou podporovány v **typeProperties** oddílu typu aktivity nastavená na DotNetActivity:
 
| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| AssemblyName | Název sestavení. V tomto příkladu je: **MyDotnetActivity.dll**. | Ano |
| Vstupní bod |Název třídy, která implementuje rozhraní IDotNetActivity. V tomto příkladu je: **MyDotNetActivityNS.MyDotNetActivity** kde MyDotNetActivityNS je obor názvů a MyDotNetActivity je třída.  | Ano | 
| PackageLinkedService | Název propojenou službu Azure Storage, který odkazuje na úložiště objektů blob, který obsahuje příslušný soubor .zip pro vlastní aktivity. V tomto příkladu je: **AzureStorageLinkedService**.| Ano |
| PackageFile | Název souboru zip. V tomto příkladu je: **customactivitycontainer/MyDotNetActivity.zip**. | Ano |
| ExtendedProperties | Rozšířené vlastnosti, které můžete definovat a předat kód .NET. V tomto příkladu **SliceStart** proměnná je nastavená na hodnotu podle vlastnosti SliceStart systémové proměnné. | Ne | 

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

Podrobné informace najdete v tématu [použití vlastních aktivit ve službě Data Factory](data-factory-use-custom-activities.md) článku. 

## <a name="next-steps"></a>Další kroky
Projděte si tyto kurzy: 

- [Kurz: vytvoření kanálu s aktivitou kopírování](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Kurz: vytvoření kanálu s aktivitou hivu](data-factory-build-your-first-pipeline-using-editor.md)
