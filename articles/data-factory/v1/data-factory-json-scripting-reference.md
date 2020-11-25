---
title: Azure Data Factory – reference skriptování JSON
description: Poskytuje schémata JSON pro Data Factory entit.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 6372976d85c7fdce2a729047d3ae36911412ecf1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019663"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory – reference skriptování JSON
> [!NOTE]
> Tento článek platí pro Data Factory verze 1.


Tento článek poskytuje schémata a příklady JSON pro definování entit Azure Data Factory (kanál, aktivita, datová sada a propojená služba).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Kanál
Struktura vysoké úrovně pro definici kanálu je následující:

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

Následující tabulka popisuje vlastnosti v rámci definice JSON kanálu:

| Vlastnost | Popis | Povinné
-------- | ----------- | --------
| name | Název kanálu. Zadejte název, který představuje akci, na kterou je nastavená aktivita nebo kanál.<br/><ul><li>Maximální počet znaků: 260.</li><li>Musí začínat číslem písmenem nebo podtržítkem ( \_ ).</li><li>Nejsou povoleny následující znaky: ".", "+", "?", "/", "<", ">", "*", "%", "&" \\ , ":", ""</li></ul> |Yes |
| description |Text popisující, k čemu se aktivita nebo kanál používá | No |
| activities | Obsahuje seznam aktivit. | Yes |
| start |Počáteční datum a čas pro kanál. Musí být ve [formátu ISO](https://en.wikipedia.org/wiki/ISO_8601). Příklad: 2014-10-14T16:32:41. <br/><br/>Je možné zadat místní čas, například čas EST. Tady je příklad: `2016-02-27T06:00:00**-05:00` , který je 6 am EST.<br/><br/>Vlastnosti Start a end společně určují aktivní období pro daný kanál. Výstupní řezy se vytvářejí jenom v tomto aktivním období. |No<br/><br/>Pokud zadáte hodnotu vlastnosti end, je nutné zadat hodnotu vlastnosti Start.<br/><br/>Časy zahájení a ukončení můžou být pro vytvoření kanálu prázdné. Chcete-li nastavit aktivní období pro spuštění kanálu, je nutné zadat obě hodnoty. Pokud při vytváření kanálu nezadáte počáteční a koncový čas, můžete je pomocí rutiny Set-AzDataFactoryPipelineActivePeriod nastavit později. |
| end |Koncové datum a čas kanálu. Je-li parametr zadán, musí být ve formátu ISO. Příklad: 2014-10-14T17:32:41 <br/><br/>Je možné zadat místní čas, například čas EST. Tady je příklad: `2016-02-27T06:00:00**-05:00` , který je 6 am EST.<br/><br/>Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti end hodnotu 9999-09-09. |No <br/><br/>Pokud zadáte hodnotu vlastnosti Start, je nutné zadat hodnotu vlastnosti end.<br/><br/>Viz poznámky pro vlastnost **Start** . |
| isPaused |Pokud je nastavená hodnota true, kanál se nespustí. Výchozí hodnota = false. Tuto vlastnost můžete použít k povolení nebo zakázání. |No |
| pipelineMode |Metoda pro plánování běhu pro kanál. Povolené hodnoty jsou: naplánované (výchozí), jednorázová.<br/><br/>Možnost naplánované znamená, že se kanál spouští v zadaném časovém intervalu podle jeho aktivního období (počáteční a koncový čas). Jednorázová označuje, že se kanál spouští jenom jednou. Jednorázová kanály se po vytvoření nedají změnit nebo aktualizovat aktuálně. Podrobnosti o nastavení jednorázová najdete v tématu [jednorázová Pipeline](data-factory-create-pipelines.md#onetime-pipeline) . |No |
| expirationTime |Doba po vytvoření, pro kterou je kanál platný a měl by zůstat zřízen. Pokud nemá žádná aktivní, neúspěšná nebo nedokončená spuštění, kanál se odstraní automaticky po dosažení doby vypršení platnosti. |No |


## <a name="activity"></a>Aktivita
Struktura vysoké úrovně pro aktivitu v rámci definice kanálu (element Activities) je následující:

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
    },
    "scheduler":
    {
    }
}
```

Následující tabulka popisuje vlastnosti v rámci definice JSON aktivity:

| Značka | Popis | Povinné |
| --- | --- | --- |
| name |Název aktivity. Zadejte název, který představuje akci, na kterou je aktivita nakonfigurována.<br/><ul><li>Maximální počet znaků: 260.</li><li>Musí začínat číslem písmenem nebo podtržítkem ( \_ ).</li><li>Nejsou povoleny následující znaky: ".", "+", "?", "/", "<", ">", "*", "%", "&" \\ , ":", ""</li></ul> |Yes |
| description |Text popisující, k čemu se aktivita používá |No |
| typ |Určuje typ aktivity. Různé typy aktivit najdete v částech [datové úložiště](#data-stores) a [aktivity transformace dat](#data-transformation-activities) . |Yes |
| vztahují |Vstupní tabulky používané aktivitou<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Ne pro aktivity HDInsightStreaming a SqlServerStoredProcedure <br/> <br/> Ano pro všechny ostatní |
| činnosti |Výstupní tabulky používané aktivitou.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Yes |
| linkedServiceName |Název propojené služby používané aktivitou. <br/><br/>Aktivita může vyžadovat zadání propojené služby, která odkazuje na požadované výpočetní prostředí. |Ano pro aktivity HDInsight, aktivity Azure Machine Learning Studio (Classic) a aktivita uložených procedur <br/><br/>Ne ve všech ostatních případech |
| typeProperties |Vlastnosti v části typeProperties závisí na typu aktivity. |No |
| policy |Zásady, které ovlivňují chování aktivity za běhu. Pokud není zadaný, použijí se výchozí zásady. |No |
| scheduler |vlastnost Scheduler slouží k definování požadovaného plánování aktivity. Jeho podvlastnosti jsou stejné jako ty ve [vlastnosti Availability v datové sadě](data-factory-create-datasets.md#dataset-availability). |No |

### <a name="policies"></a>Zásady
Zásady ovlivňují chování aktivity za běhu, konkrétně při zpracování řezu tabulky. Podrobnosti jsou uvedeny v následující tabulce.

| Vlastnost | Povolené hodnoty | Výchozí hodnota | Description |
| --- | --- | --- | --- |
| souběžnost |Integer <br/><br/>Maximální hodnota: 10 |1 |Počet souběžných spuštění aktivity.<br/><br/>Určuje počet paralelních spuštění aktivit, ke kterým může dojít v různých řezech. Například pokud aktivita potřebuje projít velkou sadou dostupných dat, větší hodnota souběžnosti zrychluje zpracování dat. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Určuje pořadí datových řezů, které jsou zpracovávány.<br/><br/>Například pokud máte 2 řezy (jedna se děje na 16:00 a druhý na 17:00), a obě jsou vyřízeny. Pokud nastavíte executionPriorityOrder na NewestFirst, řez se nejprve zpracuje v 5 odp. Podobně pokud nastavíte executionPriorityORder na OldestFIrst, bude zpracován řez ve 4 PM. |
| retry |Integer<br/><br/>Maximální hodnota může být 10. |0 |Počet opakovaných pokusů, než se zpracování dat pro řez označí jako selhání. Pokus o spuštění aktivity pro datový řez se opakuje do zadaného počtu opakování. Opakování se provede co nejdříve po selhání. |
| timeout |TimeSpan |00:00:00 |Časový limit aktivity Příklad: 00:10:00 (implikuje časový limit 10 minut)<br/><br/>Pokud hodnota není zadaná nebo je 0, časový limit je nekonečný.<br/><br/>Pokud doba zpracování dat v řezu překročí hodnotu časového limitu, je zrušena a systém se pokusí o opakované zpracování. Počet opakování závisí na vlastnosti opakování. Pokud dojde k vypršení časového limitu, je stav nastavený na vypršel časový limit. |
| způsobené |TimeSpan |00:00:00 |Určete zpoždění před spuštěním zpracování dat řezu.<br/><br/>Spuštění aktivity pro datový řez se spustí, jakmile je zpoždění po očekávanou dobu provádění.<br/><br/>Příklad: 00:10:00 (implikuje zpoždění po 10 minutách) |
| Má |Integer<br/><br/>Maximální hodnota: 10 |1 |Počet dlouhých pokusů o opakování před neúspěšným provedením řezu<br/><br/>Má pokusy jsou rozmístěny pomocí longRetryInterval. Takže pokud potřebujete zadat čas mezi opakovanými pokusy, použijte má. Pokud jsou zadány parametry Retry a má, každý má pokus obsahuje pokusy o opakování a maximální počet pokusů o opakování * má.<br/><br/>Například pokud máme v zásadách aktivity následující nastavení:<br/>Opakovat: 3<br/>Má: 2<br/>longRetryInterval: 01:00:00<br/><br/>Předpokládejme, že je možné provést pouze jeden řez (stav čeká na vyřízení) a spuštění aktivity pokaždé neproběhne úspěšně. Zpočátku by se mělo 3 po sobě jdoucích pokusů o spuštění. Po každém pokusu se stav řezu opakuje. Po prvním 3 pokusech se bude stav řezu má.<br/><br/>Po hodinu (tj. hodnotě longRetryInteval) by existovala další sada 3 po sobě jdoucích pokusů o spuštění. Po této operaci se stav řezu nezdařil a nebude proveden pokus o další opakování. Proto byly provedeny celkem 6 pokusů.<br/><br/>Pokud je jakékoli spuštění úspěšné, bude stav řezu připravený a nebude proveden pokus o další opakované pokusy.<br/><br/>Má se můžou použít v situacích, kdy se závislá data dostanou v nedeterministických časech, nebo v celkovém prostředí je vločka, pod kterou se zpracování dat děje. V takových případech s tím, že se opakuje jeden po jiné, to nemusí pomáhat a provádět po uplynutí časového intervalu požadovaného výstupu.<br/><br/>Upozornění: nenastavujte vysoké hodnoty pro má nebo longRetryInterval. Vyšší hodnoty obvykle implikují jiné systémové problémy. |
| longRetryInterval |TimeSpan |00:00:00 |Prodleva mezi dlouhými pokusy o opakování |

### <a name="typeproperties-section"></a>oddíl typeProperties
Oddíl typeProperties se pro každou aktivitu liší. Transformační aktivity mají pouze vlastnosti typu. Ukázky JSON, které definují aktivity transformace v kanálu, najdete v části [aktivity transformace dat](#data-transformation-activities) v tomto článku.

**Aktivita kopírování** má dvě pododdíly v části typeProperties: **zdroj** a **jímka**. Ukázky JSON, které ukazují, jak používat úložiště dat jako zdroj nebo jímku, najdete v části [úložiště dat](#data-stores) v tomto článku.

### <a name="sample-copy-pipeline"></a>Ukázkový kanál kopírování
V následujícím ukázkovém kanálu je v části **activities** jedna aktivita typu **Kopírování**. V této ukázce [aktivita kopírování](data-factory-data-movement-activities.md) kopíruje data z úložiště objektů BLOB v Azure do Azure SQL Database.

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

Ukázky JSON, které ukazují, jak používat úložiště dat jako zdroj nebo jímku, najdete v části [úložiště dat](#data-stores) v tomto článku.

Úplný návod k vytváření tohoto kanálu najdete v tématu [kurz: kopírování dat z BLOB Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

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
* Oddíl DEFINEs **Určuje** nastavení běhového prostředí, které se předávají skriptu podregistr jako hodnoty konfigurace podregistru (např `${hiveconf:inputtable}` `${hiveconf:partitionedtable}` .).

Ukázky JSON, které definují aktivity transformace v kanálu, najdete v části [aktivity transformace dat](#data-transformation-activities) v tomto článku.

Úplný návod k vytváření tohoto kanálu najdete v tématu [kurz: sestavení prvního kanálu pro zpracování dat pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="linked-service"></a>Propojená služba
Struktura vysoké úrovně pro definici propojené služby je následující:

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

Následující tabulka popisuje vlastnosti v rámci definice JSON aktivity:

| Vlastnost | Popis | Povinné |
| -------- | ----------- | -------- |
| name | Název propojené služby. | Yes |
| vlastnosti – typ | Typ propojené služby. Například: Azure Storage, Azure SQL Database. |
| typeProperties | Oddíl typeProperties obsahuje prvky, které jsou pro každé úložiště dat nebo výpočetní prostředí odlišné. Viz část úložiště dat pro všechna propojená služby a [výpočetní prostředí](#compute-environments) datového úložiště pro všechny odkazované služby Compute. |

## <a name="dataset"></a>Datová sada
Datová sada v Azure Data Factory je definována takto:

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

Následující tabulka obsahuje popis vlastností ve výše uvedeném formátu JSON:

| Vlastnost | Popis | Povinné | Výchozí |
| --- | --- | --- | --- |
| name | Název datové sady Pravidla pro pojmenování najdete v tématu [pravidla pro Pojmenovávání Azure Data Factory](data-factory-naming-rules.md) . |Yes |Není k dispozici |
| typ | Typ datové sady Zadejte jeden z typů, které podporuje Azure Data Factory (například: Azureblobu, AzureSqlTable). V části [úložiště dat](#data-stores) najdete všechna úložiště dat a typy datových sad podporované nástrojem Data Factory. |
| – struktura | Schéma datové sady Obsahuje sloupce, jejich typy atd. | No |Není k dispozici |
| typeProperties | Vlastnosti odpovídající vybranému typu. Podporované typy a jejich vlastnosti najdete v části [úložiště dat](#data-stores) . |Yes |Není k dispozici |
| external | Příznak Boolean pro určení, zda je datová sada explicitně vytvořena kanálem datové továrny. |No |false (nepravda) |
| dostupnosti | Definuje okno zpracování nebo model průřezu pro produkci sady dat. Podrobnosti o modelu dělení datové sady najdete v článku [plánování a provádění](data-factory-scheduling-and-execution.md) . |Yes |Není k dispozici |
| policy |Definuje kritéria nebo podmínku, které musí řezy datové sady splňovat. <br/><br/>Podrobnosti najdete v části zásady datové sady. |No |Není k dispozici |

Každý sloupec v části **Struktura** obsahuje následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| name |Název sloupce |Yes |
| typ |Datový typ sloupce  |No |
| jazyková verze |Jazyková verze založená na rozhraní .NET, která se má použít, pokud je zadán typ a je typu .NET `Datetime` nebo `Datetimeoffset` . Výchozí je `en-us`. |No |
| formát |Řetězec formátu, který má být použit, pokud je zadán typ a je typu .NET `Datetime` nebo `Datetimeoffset` . |No |

V následujícím příkladu má datová sada tři sloupce `slicetimestamp` , `projectname` a `pageviews` a jsou typu: String, String a Decimal.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Následující tabulka obsahuje popis vlastností, které můžete použít v části **dostupnost** :

| Vlastnost | Popis | Povinné | Výchozí |
| --- | --- | --- | --- |
| frequency |Určuje časovou jednotku pro produkci řezu datové sady.<br/><br/><b>Podporovaná frekvence</b>: minuta, hodina, den, týden, měsíc |Yes |Není k dispozici |
| interval |Určuje násobitel pro frekvenci.<br/><br/>Frekvence x interval určuje, jak často se řez vytvoří.<br/><br/>Pokud potřebujete datovou sadu rozdělit na každou hodinu, nastavte <b>četnost</b> na <b>hodinu</b>a <b>interval</b> na <b>1</b>.<br/><br/><b>Poznámka</b>: Pokud zadáte frekvenci jako minutu, doporučujeme nastavit interval na ne méně než 15. |Yes |Není k dispozici |
| style |Určuje, zda má být řez vytvořen na začátku nebo konci intervalu.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Pokud je frekvence nastavená na month (měsíc) a Style je nastavená na EndOfInterval, řez se vytvoří během posledního dne v měsíci. Pokud je styl nastaven na StartOfInterval, řez se vytvoří první den v měsíci.<br/><br/>Pokud je frekvence nastavená na den a styl je nastavený na EndOfInterval, řez se vytvoří za poslední hodinu dne.<br/><br/>Pokud je frekvence nastavená na Hour (hodina) a Style je nastavená na EndOfInterval, řez se vytvoří na konci hodiny. Například pro řez v období 1 PM – 2 se vytvoří řez na 2 ODP. |No |EndOfInterval |
| anchorDateTime |Definuje absolutní pozici v čase využívané schedulerem k výpočtu hranic řezu datové sady. <br/><br/><b>Poznámka</b>: Pokud AnchorDateTime obsahuje části s daty, které jsou lépe podrobnější než frekvence, budou podrobnější části ignorovány. <br/><br/>Například pokud je <b>interval</b> <b>každou hodinu</b> (četnost: hodina a interval: 1) a <b>AnchorDateTime</b> obsahuje <b>minuty a sekundy</b> , budou části <b>minut a sekund</b> v AnchorDateTime ignorovány. |No |01/01/0001 |
| posun |Časový interval, podle kterého se posune začátek a konec všech řezů datové sady <br/><br/><b>Poznámka</b>: Pokud jsou zadány oba anchorDateTime i offset, výsledkem je kombinovaný posun. |No |Není k dispozici |

Následující oddíl dostupnosti určuje, že výstupní datová sada je buď vytvořená hodinovou (nebo) vstupní datová sada je dostupná každou hodinu:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Oddíl **Policy** v definici datové sady definuje kritéria nebo podmínku, kterou musí řezy datové sady splňovat.

| Název zásady | Popis | Použito pro | Vyžadováno | Výchozí |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Ověří, jestli data v **objektu blob Azure** splňují požadavky na minimální velikost (v megabajtech). |Azure Blob |No |Není k dispozici |
| minimumRows |Ověří, jestli data v **Azure SQL Database** nebo **tabulce Azure** obsahují minimální počet řádků. |<ul><li>Azure SQL Database</li><li>Tabulka Azure</li></ul> |No |Není k dispozici |

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

Pokud není datová sada vytvářena Azure Data Factory, měla by být označena jako **externí**. Toto nastavení se obecně vztahuje na vstupy první aktivity v kanálu, pokud se nepoužívá aktivita nebo řetězení kanálu.

| Název | Popis | Povinné | Výchozí hodnota |
| --- | --- | --- | --- |
| Zpoždění datadelay |Čas pro zpoždění kontroly dostupnosti externích dat pro daný řez. Například pokud jsou data k dispozici každou hodinu, zkontrolujte, že je k dispozici externí data a že odpovídající řez je připravený, a to pomocí datadelay.<br/><br/>Platí pouze pro aktuální čas.  Například pokud je 1:00 PM hned teď a tato hodnota je 10 minut, ověření začíná na 1:10 odp.<br/><br/>Toto nastavení nemá vliv na řezy v minulosti (řezy s koncovým časem řezu + zpoždění < nyní) jsou zpracovávány bez jakéhokoli zpoždění.<br/><br/>Čas delší než 23:59 hodin musí být zadán pomocí `day.hours:minutes:seconds` formátu. Pokud například chcete zadat 24 hodin, nepoužívejte 24:00:00; místo toho použijte 1,00:00:00. Pokud používáte 24:00:00, je zpracována jako 24 dní (24.00:00:00). 1 den a 4 hodiny zadejte 1:04:00:00. |No |0 |
| retryInterval |Čekací doba mezi selháním a dalším pokusem o opakování. Pokud se pokus nezdařil, další pokus je po retryInterval. <br/><br/>Pokud teď 1:00 odp., zahájíme první pokus. Pokud je doba, po kterou má být první kontrola ověření dokončená, 1 minutu a operace se nezdařila, příští opakování bude 1:00 + 1 min (doba trvání) + 1 min (interval opakování) = 1:02 ODP. <br/><br/>U řezů v minulosti nedochází k žádnému zpoždění. Opakování proběhne okamžitě. |No |00:01:00 (1 minuta) |
| retryTimeout |Časový limit pro každý pokus o opakování.<br/><br/>Pokud je tato vlastnost nastavená na 10 minut, musí se ověření dokončit do 10 minut. Pokud ověření proběhne déle než 10 minut, vyprší časový limit opakování.<br/><br/>Pokud se všechny pokusy o ověření vyprší, řez se označí jako časový limit. |No |00:10:00 (10 minut) |
| maximumRetry |Počet, kolikrát chcete ověřit dostupnost externích dat. Maximální povolená hodnota je 10. |No |3 |


## <a name="data-stores"></a>ÚLOŽIŠTĚ DAT
V části [propojené služby](#linked-service) jsou uvedeny popisy pro prvky JSON, které jsou společné pro všechny typy propojených služeb. V této části jsou uvedeny podrobnosti o elementech JSON, které jsou specifické pro jednotlivá úložiště dat.

V části [DataSet](#dataset) jsou uvedeny popisy pro prvky JSON, které jsou společné pro všechny typy datových sad. V této části jsou uvedeny podrobnosti o elementech JSON, které jsou specifické pro jednotlivá úložiště dat.

Část [Activity (aktivita](#activity) ) poskytuje popisy pro prvky JSON, které jsou společné pro všechny typy aktivit. V této části jsou uvedeny podrobnosti o prvcích JSON, které jsou specifické pro jednotlivá úložiště dat, když se používají jako zdroj nebo jímka v aktivitě kopírování.

Kliknutím na odkaz pro úložiště, které vás zajímáte, uvidíte schémata JSON pro propojenou službu, datovou sadu a zdroj/jímku aktivity kopírování.

| Kategorie | Úložiště dat
|:--- |:--- |
| **Azure** |[Azure Blob Storage](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure Synapse Analytics (dříve SQL Data Warehouse)](#azure-synapse-analytics) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
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
| &nbsp; |Webová tabulka |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Propojená služba
Existují dva typy propojených služeb: propojená služba Azure Storage propojená s Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Pokud chcete účet Azure Storage propojit s datovou továrnou pomocí **klíče účtu**, vytvořte propojenou službu Azure Storage. Pro definování propojené služby Azure Storage nastavte **typ** propojené služby na **AzureStorage**. Pak můžete zadat následující vlastnosti v části **typeProperties** :

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k úložišti Azure pro vlastnost connectionString. |Yes |

##### <a name="example"></a>Příklad

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

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage propojená služba SAS
Propojená služba Azure Storage SAS umožňuje propojit účet Azure Storage s datovou továrnou Azure pomocí sdíleného přístupového podpisu (SAS). Poskytuje datovou továrnu s omezeným/časově vázaným přístupem ke všem/konkrétním prostředkům (objektům blob nebo kontejneru) v úložišti. Pokud chcete účet Azure Storage propojit s datovou továrnou pomocí sdíleného přístupového podpisu, vytvořte propojenou službu Azure Storage SAS. Pro definování propojené služby Azure Storage SAS nastavte **typ** propojené služby na **AzureStorageSas**. Pak můžete zadat následující vlastnosti v části **typeProperties** :

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| sasUri |Zadejte identifikátor URI sdíleného přístupového podpisu k prostředkům Azure Storage, jako je například objekt blob, kontejner nebo tabulka. |Yes |

##### <a name="example"></a>Příklad

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

Další informace o těchto propojených službách najdete v článku [konektor Azure Blob Storage](data-factory-azure-blob-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Pokud chcete definovat datovou sadu objektů BLOB v Azure, nastavte **typ** datové sady na **azureblobu**. Pak v části **typeProperties** zadejte následující vlastnosti specifické pro objekt blob Azure:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| folderPath |Cesta ke kontejneru a složce v úložišti objektů BLOB. Příklad: myblobcontainer\myblobfolder\ |Yes |
| fileName |Název objektu BLOB Název souboru je nepovinný a rozlišuje velká a malá písmena.<br/><br/>Pokud zadáte název souboru, bude aktivita (včetně kopírování) fungovat na konkrétním objektu BLOB.<br/><br/>Pokud není zadán název souboru, příkaz Kopírovat zahrnuje všechny objekty BLOB v folderPath pro vstupní datovou sadu.<br/><br/>Pokud pro výstupní datovou sadu není zadán název souboru, bude název vygenerovaného souboru v následujícím formátu: `Data.<Guid>.txt` (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy je volitelná vlastnost. Můžete ji použít k určení dynamického folderPath a názvu souboru pro data časových řad. Například folderPath může být Parametrizovaná za každou hodinu dat. |No |
| formát | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formátování [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Pokud chcete **Kopírovat soubory** mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady. |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**. Podporované úrovně: **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

#### <a name="example"></a>Příklad

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


Další informace najdete v článku [konektor Azure Blob](data-factory-azure-blob-connector.md#dataset-properties) .

### <a name="blobsource-in-copy-activity"></a>BlobSource v aktivitě kopírování
Pokud kopírujete data z Blob Storage Azure, nastavte **typ zdroje** aktivity kopírování na **BlobSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| zahrnout |Určuje, zda mají být data rekurzivně čtena z dílčích složek nebo pouze ze zadané složky. |True (výchozí hodnota), false |No |

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
Pokud kopírujete data do Blob Storage Azure, nastavte **typ jímky** aktivity kopírování na **BlobSink** a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopírování, pokud je zdroj BlobSource nebo FileSystem. |<b>PreserveHierarchy</b>: zachová hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru se zdrojovou složkou je shodná s relativní cestou cílového souboru do cílové složky.<br/><br/><b>FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generovaný název. <br/><br/><b>MergeFiles (výchozí):</b> sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadaný název souboru nebo objektu blob, bude název sloučeného souboru zadaný název. jinak by byl automaticky vygenerovaný název souboru. |No |

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

Další informace najdete v článku [konektor Azure Blob](data-factory-azure-blob-connector.md#copy-activity-properties) .

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Propojená služba
Pro definování propojené služby Azure Data Lake Store nastavte typ propojené služby na **AzureDataLakeStore** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **AzureDataLakeStore** . | Yes |
| dataLakeStoreUri | Zadejte informace o účtu Azure Data Lake Store. Má následující formát: `https://[accountname].azuredatalakestore.net/webhdfs/v1` nebo `adl://[accountname].azuredatalakestore.net/` . | Yes |
| subscriptionId | ID předplatného Azure, ke kterému patří Data Lake Store. | Vyžadováno pro jímku |
| resourceGroupName | Název skupiny prostředků Azure, do které patří Data Lake Store. | Vyžadováno pro jímku |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano (pro ověřování instančního objektu) |
| servicePrincipalKey | Zadejte klíč aplikace. | Ano (pro ověřování instančního objektu) |
| tenant | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Můžete ho načíst tak, že najedete myší v pravém horním rohu Azure Portal. | Ano (pro ověřování instančního objektu) |
| autorizace | V **editoru Data Factory** klikněte na tlačítko **autorizovat** a zadejte své přihlašovací údaje, které přiřadí automaticky vygenerované autorizační URL k této vlastnosti. | Ano (pro ověření přihlašovacích údajů uživatele)|
| sessionId | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečné a dá se použít jenom jednou. Toto nastavení se generuje automaticky, když použijete Editor Data Factory. | Ano (pro ověření přihlašovacích údajů uživatele) |

#### <a name="example-using-service-principal-authentication"></a>Příklad: použití ověřování instančního objektu
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

#### <a name="example-using-user-credential-authentication"></a>Příklad: použití ověření přihlašovacích údajů uživatele
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

Další informace najdete v článku [konektor Azure Data Lake Store](data-factory-azure-datalake-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat Azure Data Lake Store datovou sadu, nastavte **typ** datové sady na **AzureDataLakeStore** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| folderPath |Cesta ke kontejneru a složce v Azure Data Lake Storu |Yes |
| fileName |Název souboru v úložišti Azure Data Lake. Název souboru je nepovinný a rozlišuje velká a malá písmena. <br/><br/>Pokud zadáte název souboru, bude aktivita (včetně kopie) fungovat na konkrétním souboru.<br/><br/>Pokud není zadán název souboru, příkaz Kopírovat zahrnuje všechny soubory v folderPath pro vstupní datovou sadu.<br/><br/>Pokud pro výstupní datovou sadu není zadán název souboru, bude název vygenerovaného souboru v následujícím formátu: `Data.<Guid>.txt` (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy je volitelná vlastnost. Můžete ji použít k určení dynamického folderPath a názvu souboru pro data časových řad. Například folderPath může být Parametrizovaná za každou hodinu dat. |No |
| formát | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formátování [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Pokud chcete **Kopírovat soubory** mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady. |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**. Podporované úrovně: **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

#### <a name="example"></a>Příklad
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

Další informace najdete v článku [konektor Azure Data Lake Store](data-factory-azure-datalake-connector.md#dataset-properties) .

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Azure Data Lake Store zdroj v aktivitě kopírování
Pokud kopírujete data z Azure Data Lake Store, nastavte **typ zdroje** aktivity kopírování na **AzureDataLakeStoreSource** a v části **zdroj** zadejte následující vlastnosti:

**AzureDataLakeStoreSource** podporuje následující vlastnosti oddílu **typeProperties** :

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| zahrnout |Určuje, zda mají být data rekurzivně čtena z dílčích složek nebo pouze ze zadané složky. |True (výchozí hodnota), false |No |

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

Další informace najdete v článku [konektor Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) .

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store jímka v aktivitě kopírování
Pokud kopírujete data do Azure Data Lake Store, nastavte **typ jímky** aktivity kopírování na **AzureDataLakeStoreSink** a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| copyBehavior |Určuje chování při kopírování. |<b>PreserveHierarchy</b>: zachová hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru se zdrojovou složkou je shodná s relativní cestou cílového souboru do cílové složky.<br/><br/><b>FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou vytvořeny v první úrovni cílové složky. Cílové soubory jsou vytvořeny s automaticky generovaným názvem.<br/><br/><b>MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadaný název souboru nebo objektu blob, bude název sloučeného souboru zadaný název. jinak by byl automaticky vygenerovaný název souboru. |No |

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

Další informace najdete v článku [konektor Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) .

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Propojená služba
Pro definování propojené služby Azure Cosmos DB nastavte **typ** propojené služby na **DocumentDb** a v části **typeProperties** zadejte následující vlastnosti:

| **Vlastnost** | **Popis** | **Povinné** |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k databázi Azure Cosmos DB. |Yes |

#### <a name="example"></a>Příklad

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
Další informace najdete v článku [konektor Azure Cosmos DB](data-factory-azure-documentdb-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat Azure Cosmos DB datovou sadu, nastavte **typ** datové sady na **DocumentDbCollection** a v části **typeProperties** zadejte následující vlastnosti:

| **Vlastnost** | **Popis** | **Povinné** |
| --- | --- | --- |
| collectionName |Název kolekce Azure Cosmos DB |Yes |

#### <a name="example"></a>Příklad

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
Další informace najdete v článku [konektor Azure Cosmos DB](data-factory-azure-documentdb-connector.md#dataset-properties) .

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Zdroj kolekce Azure Cosmos DB v aktivitě kopírování
Pokud kopírujete data z Azure Cosmos DB, nastavte **typ zdroje** aktivity kopírování na **DocumentDbCollectionSource** a v části **zdroj** zadejte následující vlastnosti:


| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Povinné** |
| --- | --- | --- | --- |
| query |Zadejte dotaz pro čtení dat. |Řetězec dotazu podporovaný Azure Cosmos DB. <br/><br/>Příklad: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Pokud není zadaný, provede se příkaz SQL: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciální znak označující, že je dokument vnořený |Libovolný znak. <br/><br/>Azure Cosmos DB je úložiště NoSQL pro dokumenty JSON, kde jsou povoleny vnořené struktury. Azure Data Factory umožňuje uživateli používat hierarchii prostřednictvím nestingSeparator, což je "." ve výše uvedených příkladech. U oddělovače aktivita kopírování vygeneruje objekt "Name" se třemi podřízenými elementy First, uprostřed a Last, podle typu "název. First", "Name. middle" a "Name. Last" v definici tabulky. |No |

#### <a name="example"></a>Příklad

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

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Jímka kolekce Azure Cosmos DB v aktivitě kopírování
Pokud kopírujete data do Azure Cosmos DB, nastavte **typ jímky** aktivity kopírování na **DocumentDbCollectionSink** a v části **jímky** zadejte následující vlastnosti:

| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Povinné** |
| --- | --- | --- | --- |
| nestingSeparator |Speciální znak v názvu zdrojového sloupce, který označuje, že je nutné vnořený dokument. <br/><br/>Příklad: `Name.First` ve výstupní tabulce se v Cosmos DB dokumentu vytvoří následující struktura JSON:<br/><br/>"Název": {<br/>    "First": "Jan"<br/>}, |Znak, který se používá k oddělení úrovní vnoření.<br/><br/>Výchozí hodnota je `.` (tečka). |Znak, který se používá k oddělení úrovní vnoření. <br/><br/>Výchozí hodnota je `.` (tečka). |
| writeBatchSize |Počet paralelních požadavků na službu Azure Cosmos DB pro vytváření dokumentů.<br/><br/>Pomocí této vlastnosti můžete vyladit výkon při kopírování dat do nebo z Azure Cosmos DB. Při zvýšení writeBatchSize můžete očekávat lepší výkon, protože se odesílají další paralelní požadavky na Azure Cosmos DB. Je ale potřeba se vyhnout omezování, které může vyvolat chybovou zprávu: "frekvence požadavků je velká".<br/><br/>Omezování je určeno řadou faktorů, včetně velikosti dokumentů, počtu podmínek v dokumentech, indexováním zásad cílové kolekce atd. Pro operace kopírování můžete použít lepší kolekci (například S3), abyste měli k dispozici největší propustnost (2 500 jednotek žádostí za sekundu). |Integer |Ne (výchozí: 5) |
| writeBatchTimeout |Počkejte, než se operace dokončí, než vyprší časový limit. |timespan<br/><br/> Příklad: "00:30:00" (30 minut). |No |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Azure Cosmos DB](data-factory-azure-documentdb-connector.md#copy-activity-properties) .

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Propojená služba
Pro definování propojené služby Azure SQL Database nastavte **typ** propojené služby na **AzureSqlDatabase** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci Azure SQL Database pro vlastnost connectionString. |Yes |

#### <a name="example"></a>Příklad
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

Další informace najdete v článku [konektor Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat Azure SQL Database datovou sadu, nastavte **typ** datové sady na **AzureSqlTable** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v instanci Azure SQL Database, na kterou odkazuje propojená služba. |Yes |

#### <a name="example"></a>Příklad

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
Další informace najdete v článku [konektor Azure SQL](data-factory-azure-sql-connector.md#dataset-properties) .

### <a name="sql-source-in-copy-activity"></a>Zdroj SQL v aktivitě kopírování
Pokud kopírujete data z Azure SQL Database, nastavte **typ zdroje** aktivity kopírování na **SqlSource** a v části **zdroj** zadejte následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| sqlReaderQuery |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury |No |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |No |

#### <a name="example"></a>Příklad

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
Další informace najdete v článku [konektor Azure SQL](data-factory-azure-sql-connector.md#copy-activity-properties) .

### <a name="sql-sink-in-copy-activity"></a>Jímka SQL v aktivitě kopírování
Pokud kopírujete data do Azure SQL Database, nastavte **typ jímky** aktivity kopírování na **SqlSink** a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte, než se operace dávkového vložení dokončí předtím, než vyprší časový limit. |timespan<br/><br/> Příklad: "00:30:00" (30 minut). |No |
| writeBatchSize |Když velikost vyrovnávací paměti dosáhne writeBatchSize, vloží data do tabulky SQL. |Integer (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, která se má provést, aby se vyčistila data konkrétního řezu. |Příkaz dotazu. |No |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování, která se má vyplnit automaticky generovaným identifikátorem řezu, který se použije k vyčištění dat určitého řezu při opakovaném spuštění. |Název sloupce sloupce s datovým typem Binary (32). |No |
| sqlWriterStoredProcedureName |Název uložené procedury, kterou data upsertuje (aktualizuje/vloží) do cílové tabulky. |Název uložené procedury |No |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |No |
| sqlWriterTableType |Zadejte název typu tabulky, který se použije v uložené proceduře. Aktivita kopírování zpřístupňuje data, která jsou k dispozici v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit data zkopírovaná se stávajícími daty. |Název typu tabulky |No |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Azure SQL](data-factory-azure-sql-connector.md#copy-activity-properties) .

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu Azure synapse Analytics, nastavte **typ** propojené služby na **AzureSqlDW** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení ke službě Azure synapse Analytics instance pro vlastnost connectionString. |Yes |



#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Azure synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Pokud chcete definovat datovou sadu Azure synapse Analytics, nastavte **typ** datové sady na **AzureSqlDWTable** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v databázi Azure synapse Analytics, na kterou odkazuje propojená služba |Yes |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Azure synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) .

### <a name="azure-synapse-analytics-source-in-copy-activity"></a>Zdroj analýzy Azure synapse v aktivitě kopírování
Pokud kopírujete data ze služby Azure synapse Analytics, nastavte **typ zdroje** aktivity kopírování na **SqlDWSource** a v části **zdroj** zadejte následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| sqlReaderQuery |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury |No |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |No |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Azure synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) .

### <a name="azure-synapse-analytics-sink-in-copy-activity"></a>Jímka služby Azure synapse Analytics v aktivitě kopírování
Pokud kopírujete data do služby Azure synapse Analytics, nastavte **typ jímky** aktivity kopírování na **SqlDWSink** a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, která se má provést, aby se vyčistila data konkrétního řezu. |Příkaz dotazu. |No |
| allowPolyBase |Označuje, zda použít základ (je-li k dispozici) místo mechanismu BULKINSERT. <br/><br/> **Použití základny je doporučeným způsobem, jak načíst data do synapse Analytics.** |Ano <br/>False (výchozí) |No |
| polyBaseSettings |Skupina vlastností, které lze zadat, je-li vlastnost **allowPolybase** nastavena na **hodnotu true**. |&nbsp; |No |
| rejectValue |Určuje počet nebo procento řádků, které lze odmítnout před tím, než se dotaz nezdařil. <br/><br/>Další informace o možnostech odmítnutí základní třídy najdete v části **argumenty** v tématu [vytvoření externí tabulky (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) . |0 (výchozí), 1, 2,... |No |
| rejectType |Určuje, zda je možnost rejectValue zadána jako hodnota literálu nebo jako procento. |Hodnota (výchozí), procenta |No |
| Rejecttype rejectsamplevalue |Určuje počet řádků, které se mají načíst před tím, než základ přepočítá procento odmítnutých řádků. |1, 2,... |Ano, pokud **rejectType** je **procento** |
| useTypeDefault |Určuje, jak se mají zpracovat chybějící hodnoty v textových souborech s oddělovači, když základ dat načte data z textového souboru.<br/><br/>Přečtěte si další informace o této vlastnosti z oddílu argumenty v tématu [Create External File Format (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql). |True, false (výchozí) |No |
| writeBatchSize |Když velikost vyrovnávací paměti dosáhne writeBatchSize, vloží data do tabulky SQL. |Integer (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Počkejte, než se operace dávkového vložení dokončí předtím, než vyprší časový limit. |timespan<br/><br/> Příklad: "00:30:00" (30 minut). |No |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Azure synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) .

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>Propojená služba
K definování propojené služby Azure Kognitivní hledání nastavte **typ** propojené služby na **AzureSearch** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| -------- | ----------- | -------- |
| url | Adresa URL služby vyhledávání | Yes |
| key | Klíč správce pro vyhledávací službu. | Yes |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Azure kognitivní hledání](data-factory-azure-search-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Pokud chcete definovat datovou sadu Azure Kognitivní hledání, nastavte **typ** datové sady na **AzureSearchIndex** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| -------- | ----------- | -------- |
| typ | Vlastnost Type musí být nastavená na **AzureSearchIndex**.| Yes |
| indexName | Název indexu hledání Data Factory nevytváří index. Index musí existovat v Azure Kognitivní hledání. | Yes |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Azure kognitivní hledání](data-factory-azure-search-connector.md#dataset-properties) .

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Jímka indexu služby Azure Kognitivní hledání v aktivitě kopírování
Pokud kopírujete data do indexu hledání, nastavte **typ jímky** aktivity kopírování na **AzureSearchIndexSink** a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Určuje, zda se má sloučit nebo nahradit, když dokument v indexu již existuje. | Sloučení (výchozí)<br/>Nahrávání| No |
| WriteBatchSize | Když velikost vyrovnávací paměti dosáhne writeBatchSize, nahraje data do indexu vyhledávání. | 1 až 1 000. Výchozí hodnota je 1000. | No |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Azure kognitivní hledání](data-factory-azure-search-connector.md#copy-activity-properties) .

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Propojená služba
Existují dva typy propojených služeb: propojená služba Azure Storage propojená s Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Pokud chcete účet Azure Storage propojit s datovou továrnou pomocí **klíče účtu**, vytvořte propojenou službu Azure Storage. Pro definování propojené služby Azure Storage nastavte **typ** propojené služby na **AzureStorage**. Pak můžete zadat následující vlastnosti v části **typeProperties** :

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ |Vlastnost Type musí být nastavená na: **AzureStorage** . |Yes |
| připojovací řetězec |Zadejte informace potřebné pro připojení k úložišti Azure pro vlastnost connectionString. |Yes |

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

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage propojená služba SAS
Propojená služba Azure Storage SAS umožňuje propojit účet Azure Storage s datovou továrnou Azure pomocí sdíleného přístupového podpisu (SAS). Poskytuje datovou továrnu s omezeným/časově vázaným přístupem ke všem/konkrétním prostředkům (objektům blob nebo kontejneru) v úložišti. Pokud chcete účet Azure Storage propojit s datovou továrnou pomocí sdíleného přístupového podpisu, vytvořte propojenou službu Azure Storage SAS. Pro definování propojené služby Azure Storage SAS nastavte **typ** propojené služby na **AzureStorageSas**. Pak můžete zadat následující vlastnosti v části **typeProperties** :

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ |Vlastnost Type musí být nastavená na: **AzureStorageSas** . |Yes |
| sasUri |Zadejte identifikátor URI sdíleného přístupového podpisu k prostředkům Azure Storage, jako je například objekt blob, kontejner nebo tabulka. |Yes |

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

Další informace o těchto propojených službách najdete v článku [konektor Azure Table Storage](data-factory-azure-table-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Pokud chcete definovat datovou sadu tabulky Azure, nastavte **typ** datové sady na **Azure** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky instance databáze Azure Table, na kterou odkazuje propojená služba |Ano. Když je zadán tableName bez azureTableSourceQuery, zkopírují se všechny záznamy z tabulky do cílového umístění. Pokud je zadána také azureTableSourceQuery, záznamy z tabulky, které splňují dotaz, se zkopírují do cíle. |

#### <a name="example"></a>Příklad

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

Další informace o těchto propojených službách najdete v článku [konektor Azure Table Storage](data-factory-azure-table-connector.md#dataset-properties) .

### <a name="azure-table-source-in-copy-activity"></a>Zdroj tabulky Azure v aktivitě kopírování
Pokud kopírujete data z Azure Table Storage, nastavte **typ zdroje** aktivity kopírování na **AzureTableSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| azureTableSourceQuery |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu tabulky Azure Podívejte se na příklady v následující části. |Ne. Když je zadán tableName bez azureTableSourceQuery, zkopírují se všechny záznamy z tabulky do cílového umístění. Pokud je zadána také azureTableSourceQuery, záznamy z tabulky, které splňují dotaz, se zkopírují do cíle. |
| azureTableSourceIgnoreTableNotFound |Určuje, zda požití neexistuje výjimka tabulky. |TRUE<br/>FALSE |No |

#### <a name="example"></a>Příklad

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

Další informace o těchto propojených službách najdete v článku [konektor Azure Table Storage](data-factory-azure-table-connector.md#copy-activity-properties) .

### <a name="azure-table-sink-in-copy-activity"></a>Jímka tabulky Azure v aktivitě kopírování
Pokud kopírujete data do služby Azure Table Storage, nastavte **typ jímky** aktivity kopírování na **AzureTableSink** a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Výchozí hodnota klíče oddílu, kterou může jímka použít. |Řetězcová hodnota. |No |
| azureTablePartitionKeyName |Zadejte název sloupce, jehož hodnoty se používají jako klíče oddílů. Pokud není zadaný, použije se jako klíč oddílu AzureTableDefaultPartitionKeyValue. |Název sloupce |No |
| azureTableRowKeyName |Zadejte název sloupce, jehož hodnoty sloupce jsou používány jako klíč řádku. Není-li tento parametr zadán, použijte pro každý řádek identifikátor GUID. |Název sloupce |No |
| azureTableInsertType |Režim pro vložení dat do tabulky Azure.<br/><br/>Tato vlastnost určuje, zda mají být existující řádky ve výstupní tabulce se shodnými klíči oddílů a řádky nahrazeny nebo sloučeny. <br/><br/>Další informace o tom, jak tato nastavení (sloučit a nahradit) fungují, najdete v tématech [vložení nebo sloučení entit](/rest/api/storageservices/Insert-Or-Merge-Entity) a [vložení nebo nahrazení entit](/rest/api/storageservices/Insert-Or-Replace-Entity) . <br/><br> Toto nastavení se vztahuje na úrovni řádků, nikoli na úrovni tabulky a ani možnost neodstraní řádky ve výstupní tabulce, které ve vstupu neexistují. |sloučení (výchozí)<br/>náhrady |No |
| writeBatchSize |Vloží data do tabulky Azure, když je dosaženo writeBatchSize nebo writeBatchTimeout. |Integer (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Vloží data do tabulky Azure, když je dosaženo writeBatchSize nebo writeBatchTimeout. |timespan<br/><br/>Příklad: "00:20:00" (20 minut) |Ne (výchozí hodnota pro výchozí nastavení klienta úložiště hodnota časového limitu 90 s) |

#### <a name="example"></a>Příklad

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
Další informace o těchto propojených službách najdete v článku [konektor Azure Table Storage](data-factory-azure-table-connector.md#copy-activity-properties) .

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu Amazon RedShift, nastavte **typ** propojené služby na **AmazonRedshift** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| server |IP adresa nebo název hostitele serveru Amazon RedShift Server. |Yes |
| port |Číslo portu TCP, který server Amazon RedShift používá k naslouchání klientským připojením. |Ne, výchozí hodnota: 5439 |
| database |Název databáze Amazon RedShift. |Yes |
| username |Jméno uživatele, který má přístup k databázi. |Yes |
| heslo |Heslo pro uživatelský účet. |Yes |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku o konektoru Amazon RedShift.

### <a name="dataset"></a>Datová sada
Chcete-li definovat sadu Amazon RedShift DataSet, nastavte **typ** datové sady na **relační** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky v databázi Amazon RedShift, na kterou odkazuje propojená služba |Ne (Pokud je zadán **dotaz** na **RelationalSource** ) |


#### <a name="example"></a>Příklad

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
Další informace najdete v článku o konektoru Amazon RedShift.

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z Amazon RedShift, nastavte **typ zdroje** aktivity kopírování na **RelationalSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |Ne (Pokud je zadaný **TableName** **objektu DataSet** ) |

#### <a name="example"></a>Příklad

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
Další informace najdete v článku o konektoru Amazon RedShift.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu IBM DB2, nastavte **typ** propojené služby na **OnPremisesDB2** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| server |Název serveru DB2. |Yes |
| database |Název databáze DB2 |Yes |
| schema |Název schématu v databázi. V názvu schématu se rozlišují velká a malá písmena. |No |
| authenticationType |Typ ověřování, který se používá pro připojení k databázi DB2. Možné hodnoty jsou: anonymní, základní a Windows. |Yes |
| username |Pokud používáte základní ověřování nebo ověřování systému Windows, zadejte uživatelské jméno. |No |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |No |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k místní databázi DB2. |Yes |

#### <a name="example"></a>Příklad
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
Další informace najdete v článku konektor IBM DB2.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu DB2, nastavte **typ** datové sady na **relační** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky instance databáze DB2, na kterou odkazuje propojená služba Vlastnost tableName rozlišuje velká a malá písmena. |Ne (Pokud je zadán **dotaz** na **RelationalSource** )

#### <a name="example"></a>Příklad
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

Další informace najdete v článku konektor IBM DB2.

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z IBM DB2, nastavte **typ zdroje** aktivity kopírování na **RelationalSource** a v části **zdroj** zadejte následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: `"query": "select * from "MySchema"."MyTable""`. |Ne (Pokud je zadaný **TableName** **objektu DataSet** ) |

#### <a name="example"></a>Příklad
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
Další informace najdete v článku konektor IBM DB2.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu MySQL, nastavte **typ** propojené služby na **OnPremisesMySql** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| server |Název serveru MySQL |Yes |
| database |Název databáze MySQL |Yes |
| schema |Název schématu v databázi. |No |
| authenticationType |Typ ověřování, který se používá pro připojení k databázi MySQL. Možné hodnoty jsou: `Basic` . |Yes |
| userName |Zadejte uživatelské jméno pro připojení k databázi MySQL. |Yes |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali. |Yes |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k místní databázi MySQL. |Yes |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor MySQL](data-factory-onprem-mysql-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu MySQL, nastavte **typ** datové sady na **relační** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky v instanci databáze MySQL, na kterou odkazuje propojená služba |Ne (Pokud je zadán **dotaz** na **RelationalSource** ) |

#### <a name="example"></a>Příklad

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
Další informace najdete v článku [konektor MySQL](data-factory-onprem-mysql-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z databáze MySQL, nastavte **typ zdroje** aktivity kopírování na **RelationalSource** a v části **zdroj** zadejte následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |Ne (Pokud je zadaný **TableName** **objektu DataSet** ) |


#### <a name="example"></a>Příklad
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

Další informace najdete v článku [konektor MySQL](data-factory-onprem-mysql-connector.md#copy-activity-properties) .

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu Oracle, nastavte **typ** propojené služby na **OnPremisesOracle** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| driverType | Určete, který ovladač se má použít ke kopírování dat z/do Oracle Database. Povolené hodnoty jsou **Microsoft** nebo **ODP** (výchozí). Viz část podporovaná verze a instalace v podrobnostech o ovladači. | No |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci Oracle Database pro vlastnost connectionString. | Yes |
| gatewayName | Název brány, která se používá pro připojení k místnímu serveru Oracle |Yes |

#### <a name="example"></a>Příklad
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

Další informace najdete v článku [konektor Oracle](data-factory-onprem-oracle-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Pokud chcete definovat datovou sadu Oracle, nastavte **typ** datové sady na **Oracle** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky v Oracle Database, na kterou odkazuje propojená služba |Ne (Pokud je zadáno **oracleReaderQuery** of **OracleSource** ) |

#### <a name="example"></a>Příklad

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
Další informace najdete v článku [konektor Oracle](data-factory-onprem-oracle-connector.md#dataset-properties) .

### <a name="oracle-source-in-copy-activity"></a>Zdroj Oracle v aktivitě kopírování
Pokud kopírujete data z databáze Oracle, nastavte **typ zdroje** aktivity kopírování na **OracleSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| oracleReaderQuery |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: `select * from MyTable` <br/><br/>Pokud není zadaný, provede se příkaz SQL: `select * from MyTable` |Ne (Pokud je zadaný **TableName** **objektu DataSet** ) |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) .

### <a name="oracle-sink-in-copy-activity"></a>Jímka Oracle v aktivitě kopírování
Pokud kopírujete data do databáze Oracle, nastavte **typ jímky** aktivity kopírování na **OracleSink** a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte, než se operace dávkového vložení dokončí předtím, než vyprší časový limit. |timespan<br/><br/> Příklad: 00:30:00 (30 minut). |No |
| writeBatchSize |Když velikost vyrovnávací paměti dosáhne writeBatchSize, vloží data do tabulky SQL. |Integer (počet řádků) |Ne (výchozí: 100) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, která se má provést, aby se vyčistila data konkrétního řezu. |Příkaz dotazu. |No |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování, která se má vyplnit automaticky generovaným identifikátorem řezu, který se použije k vyčištění dat určitého řezu při opakovaném spuštění. |Název sloupce sloupce s datovým typem Binary (32). |No |

#### <a name="example"></a>Příklad
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
Další informace najdete v článku [konektor Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) .

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu PostgreSQL, nastavte **typ** propojené služby na **OnPremisesPostgreSql** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| server |Název serveru PostgreSQL. |Yes |
| database |Název databáze PostgreSQL |Yes |
| schema |Název schématu v databázi. V názvu schématu se rozlišují velká a malá písmena. |No |
| authenticationType |Typ ověřování, který se používá pro připojení k databázi PostgreSQL. Možné hodnoty jsou: anonymní, základní a Windows. |Yes |
| username |Pokud používáte základní ověřování nebo ověřování systému Windows, zadejte uživatelské jméno. |No |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |No |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k místní databázi PostgreSQL. |Yes |

#### <a name="example"></a>Příklad

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
Další informace najdete v článku [konektor PostgreSQL](data-factory-onprem-postgresql-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu PostgreSQL, nastavte **typ** datové sady na **relační** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky instance databáze PostgreSQL, na kterou odkazuje propojená služba Vlastnost tableName rozlišuje velká a malá písmena. |Ne (Pokud je zadán **dotaz** na **RelationalSource** ) |

#### <a name="example"></a>Příklad
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
Další informace najdete v článku [konektor PostgreSQL](data-factory-onprem-postgresql-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z databáze PostgreSQL, nastavte **typ zdroje** aktivity kopírování na **RelationalSource** a v části **zdroj** zadejte následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Například: "dotaz": "vybrat * z \" MySchema \" . \" MyTable \" ". |Ne (Pokud je zadaný **TableName** **objektu DataSet** ) |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor PostgreSQL](data-factory-onprem-postgresql-connector.md#copy-activity-properties) .

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu SAP Business Warehouse (ČERNOBÍLý), nastavte **typ** propojené služby na **SapBw** a v části **typeProperties** zadejte následující vlastnosti:

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém se nachází instance SAP BW. | řetězec | Yes
systemNumber | Číslo systému SAP BW systému | Desítkové číslo se dvěma číslicemi reprezentované jako řetězec. | Yes
clientId | ID klienta klienta v systému SAP W. | Desítkové číslo se třemi číslicemi reprezentované jako řetězec. | Yes
username | Jméno uživatele, který má přístup k serveru SAP | řetězec | Yes
heslo | Heslo pro tohoto uživatele. | řetězec | Yes
gatewayName | Název brány, kterou by služba Data Factory měla použít pro připojení k místní instanci SAP BW | řetězec | Yes
encryptedCredential | Šifrovaný řetězec přihlašovacích údajů. | řetězec | No

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat SAP BW datovou sadu, nastavte **typ** datové sady na **relační**. Pro SAP BW datovou sadu **relačních** objektů typu není podporována žádná vlastnost specifická pro typ.

#### <a name="example"></a>Příklad

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
Další informace najdete v článku [konektor SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data ze SAP Business Warehouse, nastavte **typ zdroje** aktivity kopírování na **RelationalSource** a v části **zdroj** zadejte následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query | Určuje dotaz MDX pro čtení dat z instance SAP BW. | Dotaz MDX. | Yes |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) .

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat SAP HANA propojenou službu, nastavte **typ** propojené služby na **SapHana** a v části **typeProperties** zadejte následující vlastnosti:

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém se nachází instance SAP HANA. Pokud váš server používá přizpůsobený port, zadejte `server:port` . | řetězec | Yes
authenticationType | Typ ověřování. | . "Basic" nebo "Windows" | Yes
username | Jméno uživatele, který má přístup k serveru SAP | řetězec | Yes
heslo | Heslo pro tohoto uživatele. | řetězec | Yes
gatewayName | Název brány, kterou by služba Data Factory měla použít pro připojení k místní instanci SAP HANA | řetězec | Yes
encryptedCredential | Šifrovaný řetězec přihlašovacích údajů. | řetězec | No

#### <a name="example"></a>Příklad

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
Další informace najdete v článku [konektor SAP HANA](data-factory-sap-hana-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat SAP HANA datovou sadu, nastavte **typ** datové sady na **relační**. Pro SAP HANA datovou sadu **relačních** objektů typu není podporována žádná vlastnost specifická pro typ.

#### <a name="example"></a>Příklad

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
Další informace najdete v článku [konektor SAP HANA](data-factory-sap-hana-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z SAP HANA úložiště dat, nastavte **typ zdroje** aktivity kopírování na **RelationalSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query | Určuje dotaz SQL pro čtení dat z instance SAP HANA. | Dotaz SQL. | Yes |


#### <a name="example"></a>Příklad


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

Další informace najdete v článku [konektor SAP HANA](data-factory-sap-hana-connector.md#copy-activity-properties) .


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Propojená služba
Vytvoříte propojenou službu typu **OnPremisesSqlServer** , která bude propojit databázi SQL Server s datovou továrnou. Následující tabulka uvádí popis pro prvky JSON specifické pro SQL Server propojenou službu.

Následující tabulka uvádí popis pro prvky JSON specifické pro SQL Server propojenou službu.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type by měla být nastavená na: **OnPremisesSqlServer**. |Yes |
| připojovací řetězec |Zadejte informace připojovacího řetězce potřebné pro připojení k databázi SQL Server pomocí ověřování SQL nebo ověřování systému Windows. |Yes |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k databázi SQL Server. |Yes |
| username |Pokud používáte ověřování systému Windows, zadejte uživatelské jméno. Příklad: **DomainName \\ uživatelské_jméno**. |No |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |No |

Přihlašovací údaje můžete šifrovat pomocí rutiny **New-AzDataFactoryEncryptValue** a použít je v připojovacím řetězci, jak je znázorněno v následujícím příkladu (vlastnost **EncryptedCredential** ):

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
#### <a name="example-json-for-using-windows-authentication"></a>Příklad: JSON pro použití ověřování systému Windows

Pokud je zadané uživatelské jméno a heslo, brána je použije k zosobnění zadaného uživatelského účtu pro připojení k SQL Server databázi. V opačném případě se brána připojí k SQL Server přímo s kontextem zabezpečení brány (spouštěcí účet).

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

Další informace najdete v článku [konektor SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat SQL Server datovou sadu, nastavte **typ** datové sady na **SQLServer** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v instanci databáze SQL Server, na kterou odkazuje propojená služba |Yes |

#### <a name="example"></a>Příklad
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

Další informace najdete v článku [konektor SQL Server](data-factory-sqlserver-connector.md#dataset-properties) .

### <a name="sql-source-in-copy-activity"></a>Zdroj SQL v aktivitě kopírování
Pokud kopírujete data z databáze SQL Server, nastavte **typ zdroje** aktivity kopírování na **SqlSource** a v části **zdroj** zadejte následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| sqlReaderQuery |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. Může odkazovat na více tabulek z databáze, na kterou odkazuje vstupní datová sada. Pokud není zadaný, provede se příkaz SQL: vyberte z MyTable. |No |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury |No |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |No |

Pokud je pro SqlSource určena **sqlReaderQuery** , aktivita kopírování spustí tento dotaz proti SQL Server zdroji databáze, aby získala data.

Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přijímá parametry).

Pokud nezadáte buď sqlReaderQuery nebo sqlReaderStoredProcedureName, jsou sloupce definované v oddílu Structure použity k vytvoření dotazu SELECT pro spuštění v databázi SQL Server. Pokud definice datové sady nemá strukturu, všechny sloupce jsou vybrány z tabulky.

> [!NOTE]
> Při použití **sqlReaderStoredProcedureName** je stále nutné zadat hodnotu pro vlastnost **TableName** v datové sadě JSON. V této tabulce neexistují žádná ověření, která by byla provedena.


#### <a name="example"></a>Příklad
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

V tomto příkladu je **sqlReaderQuery** určena pro SqlSource. Aktivita kopírování spustí tento dotaz proti SQL Server zdroji databáze, aby získala data. Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přijímá parametry). SqlReaderQuery může odkazovat na více tabulek v rámci databáze, na kterou odkazuje vstupní datová sada. Není omezena pouze na sadu tabulek, jako je typeProperty tableName sady dat.

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, použijí se sloupce definované v oddílu Structure k vytvoření dotazu SELECT pro spuštění v databázi SQL Server. Pokud definice datové sady nemá strukturu, všechny sloupce jsou vybrány z tabulky.

Další informace najdete v článku [konektor SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) .

### <a name="sql-sink-in-copy-activity"></a>Jímka SQL v aktivitě kopírování
Pokud kopírujete data do databáze SQL Server, nastavte **typ jímky** aktivity kopírování na **SqlSink** a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte, než se operace dávkového vložení dokončí předtím, než vyprší časový limit. |timespan<br/><br/> Příklad: "00:30:00" (30 minut). |No |
| writeBatchSize |Když velikost vyrovnávací paměti dosáhne writeBatchSize, vloží data do tabulky SQL. |Integer (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, která se má provést, aby se vyčistila data konkrétního řezu. Další informace najdete v části s možností opakování. |Příkaz dotazu. |No |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování, která se má vyplnit automaticky generovaným identifikátorem řezu, který se použije k vyčištění dat určitého řezu při opakovaném spuštění. Další informace najdete v části s možností opakování. |Název sloupce sloupce s datovým typem Binary (32). |No |
| sqlWriterStoredProcedureName |Název uložené procedury, kterou data upsertuje (aktualizuje/vloží) do cílové tabulky. |Název uložené procedury |No |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |No |
| sqlWriterTableType |Zadejte název typu tabulky, který se použije v uložené proceduře. Aktivita kopírování zpřístupňuje data, která jsou k dispozici v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit data zkopírovaná se stávajícími daty. |Název typu tabulky |No |

#### <a name="example"></a>Příklad
Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala tyto vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **BlobSource** a typ **jímky** je nastavený na **SqlSink**.

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

Další informace najdete v článku [konektor SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) .

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu Sybase, nastavte **typ** propojené služby na **OnPremisesSybase** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| server |Název serveru Sybase. |Yes |
| database |Název databáze Sybase |Yes |
| schema |Název schématu v databázi. |No |
| authenticationType |Typ ověřování, který se používá pro připojení k databázi Sybase. Možné hodnoty jsou: anonymní, základní a Windows. |Yes |
| username |Pokud používáte základní ověřování nebo ověřování systému Windows, zadejte uživatelské jméno. |No |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |No |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k místní databázi Sybase. |Yes |

#### <a name="example"></a>Příklad
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

Další informace najdete v článku [konektor Sybase](data-factory-onprem-sybase-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Sybase, nastavte **typ** datové sady na **relační** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky instance databáze Sybase, na kterou odkazuje propojená služba |Ne (Pokud je zadán **dotaz** na **RelationalSource** ) |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Sybase](data-factory-onprem-sybase-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z databáze Sybase, nastavte **typ zdroje** aktivity kopírování na **RelationalSource** a v části **zdroj** zadejte následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |Ne (Pokud je zadaný **TableName** **objektu DataSet** ) |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Sybase](data-factory-onprem-sybase-connector.md#copy-activity-properties) .

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu Teradata, nastavte **typ** propojené služby na **OnPremisesTeradata** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| server |Název serveru Teradata. |Yes |
| authenticationType |Typ ověřování, který se používá pro připojení k databázi Teradata. Možné hodnoty jsou: anonymní, základní a Windows. |Yes |
| username |Pokud používáte základní ověřování nebo ověřování systému Windows, zadejte uživatelské jméno. |No |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |No |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k místní databázi Teradata. |Yes |

#### <a name="example"></a>Příklad
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

Další informace najdete v článku [konektor Teradata](data-factory-onprem-teradata-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu objektů BLOB Teradata, nastavte **typ** datové sady na **relační**. V současné době nejsou podporovány žádné vlastnosti typu pro datovou sadu Teradata.

#### <a name="example"></a>Příklad
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

Další informace najdete v článku [konektor Teradata](data-factory-onprem-teradata-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z databáze Teradata, nastavte **typ zdroje** aktivity kopírování na **RelationalSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |Yes |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Teradata](data-factory-onprem-teradata-connector.md#copy-activity-properties) .

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu Cassandra, nastavte **typ** propojené služby na **OnPremisesCassandra** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| Hostitel |Jedna nebo víc IP adres nebo názvů hostitelů Cassandra serverů.<br/><br/>Zadejte čárkami oddělený seznam IP adres nebo názvů hostitelů pro připojení ke všem serverům současně. |Yes |
| port |Port TCP, který server Cassandra používá k naslouchání klientským připojením. |Ne, výchozí hodnota: 9042 |
| authenticationType |Basic nebo Anonymous |Yes |
| username |Zadejte uživatelské jméno pro uživatelský účet. |Ano, pokud je authenticationType nastaveno na Basic. |
| heslo |Zadejte heslo pro uživatelský účet. |Ano, pokud je authenticationType nastaveno na Basic. |
| gatewayName |Název brány, který se používá pro připojení k místní databázi Cassandra. |Yes |
| encryptedCredential |Přihlašovací údaje zašifrované bránou |No |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Cassandra](data-factory-onprem-cassandra-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Cassandra, nastavte **typ** datové sady na **CassandraTable** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| prostor klíčů |Název prostoru klíčů nebo schématu v databázi Cassandra. |Ano (Pokud není definován **dotaz** pro **CassandraSource** ). |
| tableName |Název tabulky v databázi Cassandra |Ano (Pokud není definován **dotaz** pro **CassandraSource** ). |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Cassandra](data-factory-onprem-cassandra-connector.md#dataset-properties) .

### <a name="cassandra-source-in-copy-activity"></a>Cassandra zdroj v aktivitě kopírování
Pokud kopírujete data z Cassandra, nastavte **typ zdroje** aktivity kopírování na **CassandraSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Dotaz SQL-92 nebo dotaz CQL Viz [odkaz na CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Při použití dotazu SQL zadejte **název prostoru klíčů. název tabulky** , který bude představovat tabulku, kterou chcete dotazovat. |Ne (Pokud je definováno pole tableName a prostor pro datovou sadu). |
| consistencyLevel |Úroveň konzistence určuje, kolik replik musí odpovídat žádosti o čtení před vrácením dat do klientské aplikace. Cassandra zkontroluje zadaný počet replik dat, aby splňovaly požadavky na čtení. |JEDNA, DVĚ, TŘI, KVORA, VŠE, LOCAL_QUORUM, EACH_QUORUM LOCAL_ONE. Podrobnosti najdete v tématu [Konfigurace konzistence dat](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) . |Ne. Výchozí hodnota je jedna. |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Cassandra](data-factory-onprem-cassandra-connector.md#copy-activity-properties) .

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu MongoDB, nastavte **typ** propojené služby na **OnPremisesMongoDB** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| server |IP adresa nebo název hostitele serveru MongoDB |Yes |
| port |Port TCP, který server MongoDB používá k naslouchání klientským připojením. |Volitelná výchozí hodnota: 27017 |
| authenticationType |Basic nebo Anonymous. |Yes |
| username |Uživatelský účet pro přístup k MongoDB. |Ano (Pokud se používá základní ověřování). |
| heslo |Heslo pro tohoto uživatele. |Ano (Pokud se používá základní ověřování). |
| authSource |Název databáze MongoDB, kterou chcete použít ke kontrole vašich přihlašovacích údajů pro ověřování. |Volitelné (Pokud se používá základní ověřování). výchozí: používá účet správce a databázi určenou pomocí vlastnosti databaseName. |
| Databáze |Název databáze MongoDB, ke které chcete získat přístup. |Yes |
| gatewayName |Název brány, která přistupuje k úložišti dat. |Yes |
| encryptedCredential |Přihlašovací údaje zašifrované bránou |Volitelné |

#### <a name="example"></a>Příklad

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

Další informace najdete v [článku konektor MongoDB](data-factory-on-premises-mongodb-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu MongoDB, nastavte **typ** datové sady na **MongoDbCollection** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| collectionName |Název kolekce v databázi MongoDB |Yes |

#### <a name="example"></a>Příklad

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

Další informace najdete v [článku konektor MongoDB](data-factory-on-premises-mongodb-connector.md#dataset-properties) .

#### <a name="mongodb-source-in-copy-activity"></a>MongoDB zdroj v aktivitě kopírování
Pokud kopírujete data z MongoDB, nastavte **typ zdroje** aktivity kopírování na **MongoDbSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL-92 Příklad: `select * from MyTable`. |Ne (Pokud je zadán parametr **CollectionName** pro **sadu dat** ) |

#### <a name="example"></a>Příklad

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

Další informace najdete v [článku konektor MongoDB](data-factory-on-premises-mongodb-connector.md#copy-activity-properties) .

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu Amazon S3, nastavte **typ** propojené služby na **AwsAccessKey** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| accessKeyID |ID tajného přístupového klíče |řetězec |Yes |
| secretAccessKey |Tajný přístupový klíč sám o sobě. |Šifrovaný tajný řetězec |Yes |

#### <a name="example"></a>Příklad
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

Další informace najdete v [článku konektoru Amazon S3](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Datová sada
Pokud chcete definovat datovou sadu Amazon S3, nastavte **typ** datové sady na **AmazonS3** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| interval intervalu |Název intervalu S3. |Řetězec |Yes |
| key |Klíč objektu S3. |Řetězec |No |
| směr |Prefix pro klíč objektu S3 Jsou vybrány objekty, jejichž klíče začínají touto předponou. Platí pouze v případě, že klíč je prázdný. |Řetězec |No |
| verze |Verze objektu S3, pokud je povolená Správa verzí S3 |Řetězec |No |
| formát | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formátování [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Pokud chcete **Kopírovat soubory** mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady. |No | |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**. Podporované úrovně jsou: **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No | |


> [!NOTE]
> interval intervalu + klíč určuje umístění objektu S3, kde je kontejner kořenovým kontejnerem pro objekty S3 a klíč je úplná cesta k objektu S3.

#### <a name="example-sample-dataset-with-prefix"></a>Příklad: Ukázková datová sada s předponou

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
#### <a name="example-sample-data-set-with-version"></a>Příklad: sada ukázkových dat (s verzí)

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

#### <a name="example-dynamic-paths-for-s3"></a>Příklad: dynamické cesty pro S3
V ukázce používáme v datové sadě Amazon S3 pevné hodnoty pro klíčová a vlastnost vlastností.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Pomocí systémových proměnných, jako je vlastnosti slicestart, je Data Factory možné dynamicky vypočítat klíč a interval dynamicky za běhu.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Můžete to samé udělat pro vlastnost prefix datové sady Amazon S3. Seznam podporovaných funkcí a proměnných naleznete v tématu [Data Factory functions a System Variables](data-factory-functions-variables.md) .

Další informace najdete v [článku konektoru Amazon S3](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete data z Amazon S3, nastavte **typ zdroje** aktivity kopírování na **FileSystemSource** a v části **zdroj** zadejte následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| zahrnout |Určuje, jestli se mají rekurzivně zobrazovat objekty S3 v rámci adresáře. |true nebo false |No |


#### <a name="example"></a>Příklad


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

Další informace najdete v [článku konektoru Amazon S3](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Systém souborů


### <a name="linked-service"></a>Propojená služba
Místní systém souborů můžete propojit s objektem pro vytváření dat Azure pomocí místní propojené služby **souborového serveru** . Následující tabulka uvádí popisy pro prvky JSON, které jsou specifické pro propojenou službu místního souborového serveru.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Ujistěte se, že vlastnost Type je nastavená na **OnPremisesFileServer**. |Yes |
| Hostitel |Určuje kořenovou cestu ke složce, kterou chcete zkopírovat. Pro speciální znaky v řetězci použijte řídicí znak ' \ '. Příklady najdete v tématu Ukázka propojené služby a definice datových sad. |Yes |
| UserID |Zadejte ID uživatele, který má přístup k serveru. |Ne (Pokud zvolíte encryptedCredential) |
| heslo |Zadejte heslo uživatele (UserID). |Ne (Pokud zvolíte encryptedCredential |
| encryptedCredential |Zadejte šifrované přihlašovací údaje, které můžete získat spuštěním rutiny New-AzDataFactoryEncryptValue. |Ne (Pokud se rozhodnete zadat ID uživatele a heslo jako prostý text) |
| gatewayName |Určuje název brány, kterou Data Factory použít pro připojení k místnímu souborovému serveru. |Yes |

#### <a name="sample-folder-path-definitions"></a>Definice cesty k ukázkovým složkám

| Scénář | Hostitel v definici propojené služby | folderPath v definici datové sady |
| --- | --- | --- |
| Místní složka na počítači Správa dat brány: <br/><br/>Příklady: D: \\ \* nebo D:\folder\subfolder\\* |D: \\ \\ (pro Správa dat Gateway 2,0 a novější verze) <br/><br/> localhost (pro starší verze než Správa dat brána 2,0) |.\\\\ nebo \\ \\ podsložek složky (pro Správa dat bránu 2,0 a novější verze) <br/><br/>D: \\ \\ nebo d: \\ \\ \\ \\ podsložka složky (pro verzi brány nižší než 2,0) |
| Vzdálená sdílená složka: <br/><br/>Příklady: \\ \\ MyServer \\ share nebo sdílená \\ \* \\ \\ \\ Složka MyServer sdílené \\ složky \\\\* |\\\\\\\\MyServer \\ \\ sdílená složka |.\\\\ nebo \\ \\ podsložek složky |


#### <a name="example-using-username-and-password-in-plain-text"></a>Příklad: použití uživatelského jména a hesla v prostém textu

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

#### <a name="example-using-encryptedcredential"></a>Příklad: použití encryptedcredential

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

Další informace najdete v [článku konektor systému souborů](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu systému souborů, nastavte **typ** datové sady na **Shared** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| folderPath |Určuje podcestu ke složce. Pro speciální znaky v řetězci použijte řídicí znak ' \ '. Příklady najdete v tématu Ukázka propojené služby a definice datových sad.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** a mít tak cesty ke složkám na základě data a času začátku a konce řezu. |Yes |
| fileName |Pokud chcete, aby tabulka odkazovala na konkrétní soubor ve složce, zadejte název souboru do **FolderPath** . Pokud pro tuto vlastnost nezadáte žádnou hodnotu, odkazuje tabulka na všechny soubory ve složce.<br/><br/>Pokud není zadán název souboru pro výstupní datovou sadu, je název vygenerovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt` (Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| fileFilter |Určete filtr, který se použije k výběru podmnožiny souborů v folderPath, nikoli všech souborů. <br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklad 1: "FileFilter": "*. log"<br/>Příklad 2: "FileFilter": 2016-1-?. txt<br/><br/>Všimněte si, že tento filtr souborů je použitelný pro sadu vstupních souborů Shared. |No |
| partitionedBy |Pomocí partitionedBy můžete zadat dynamický folderPath/fileName pro data časových řad. Příkladem je folderPath parametrizované pro každou hodinu dat. |No |
| formát | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formátování [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Pokud chcete **Kopírovat soubory** mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady. |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**; a podporované úrovně jsou: **optimální** a **nejrychlejší**. zobrazení [souborů a kompresních formátů v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> Nemůžete současně použít fileName a FileFilter.

#### <a name="example"></a>Příklad

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

Další informace najdete v [článku konektor systému souborů](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete data ze systému souborů, nastavte **typ zdroje** aktivity kopírování na **FileSystemSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| zahrnout |Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. |True, false (výchozí) |No |

#### <a name="example"></a>Příklad

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
Další informace najdete v [článku konektor systému souborů](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Jímka systému souborů v aktivitě kopírování
Pokud kopírujete data do systému souborů, nastavte **typ jímky** aktivity kopírování na **FileSystemSink** a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopírování, pokud je zdroj BlobSource nebo FileSystem. |**PreserveHierarchy:** Zachová hierarchii souborů v cílové složce. To znamená, že relativní cesta ke zdrojovému souboru ke zdrojové složce je stejná jako relativní cesta cílového souboru k cílové složce.<br/><br/>**FlattenHierarchy:** Všechny soubory ze zdrojové složky se vytvoří v první úrovni cílové složky. Cílové soubory se vytvoří s automaticky generovaným názvem.<br/><br/>**MergeFiles:** Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadaný název souboru nebo název objektu blob, Název sloučeného souboru je zadaný název. V opačném případě se jedná o automaticky generovaný název souboru. |No |

automaticky

#### <a name="example"></a>Příklad

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

Další informace najdete v [článku konektor systému souborů](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu FTP, nastavte **typ** propojené služby na **FTPserver** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné | Výchozí |
| --- | --- | --- | --- |
| Hostitel |Název nebo IP adresa serveru FTP |Yes |&nbsp; |
| authenticationType |Zadání typu ověřování |Yes |Základní, anonymní |
| username |Uživatel, který má přístup k serveru FTP |No |&nbsp; |
| heslo |Heslo pro uživatele (uživatelské jméno) |No |&nbsp; |
| encryptedCredential |Šifrované přihlašovací údaje pro přístup k serveru FTP |No |&nbsp; |
| gatewayName |Název Správa dat brány pro připojení k místnímu serveru FTP |No |&nbsp; |
| port |Port, na kterém naslouchá server FTP |No |21 |
| enableSsl |Určete, jestli se má používat FTP přes SSL/TLS kanál. |No |true |
| enableServerCertificateValidation |Určete, jestli se při použití kanálu FTP přes SSL/TLS povolí ověřování certifikátu TLS/SSL serveru. |No |true |

#### <a name="example-using-anonymous-authentication"></a>Příklad: použití anonymního ověřování

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

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Příklad: použití uživatelského jména a hesla v prostém textu pro základní ověřování

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

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Příklad: použití portu, enableSsl, enableServerCertificateValidation

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

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Příklad: použití encryptedCredential pro ověřování a bránu

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

Další informace najdete v článku [konektor FTP](data-factory-ftp-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu serveru FTP, nastavte **typ** datové sady na **Shared** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| folderPath |Dílčí cesta ke složce Pro speciální znaky v řetězci použijte řídicí znak ' \ '. Příklady najdete v tématu Ukázka propojené služby a definice datových sad.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** a mít tak cesty ke složkám na základě data a času začátku a konce řezu. |Yes
| fileName |Pokud chcete, aby tabulka odkazovala na konkrétní soubor ve složce, zadejte název souboru do **FolderPath** . Pokud pro tuto vlastnost nezadáte žádnou hodnotu, odkazuje tabulka na všechny soubory ve složce.<br/><br/>Pokud pro výstupní datovou sadu není zadán název souboru, bude název vygenerovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt` (Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| fileFilter |Určete filtr, který se použije k výběru podmnožiny souborů v folderPath, nikoli všech souborů.<br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklady 1: `"fileFilter": "*.log"`<br/>Příklad 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> pro sadu vstupních dat Shared je použitelný filtr. Tato vlastnost není u HDFS podporována. |No |
| partitionedBy |partitionedBy lze použít k zadání dynamického názvu souboru folderPath pro data časové řady. Například folderPath parametrizované pro každou hodinu dat. |No |
| formát | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formátování [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Pokud chcete **Kopírovat soubory** mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady. |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**; a podporované úrovně jsou: **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useBinaryTransfer |Určete, zda se má použít režim binárního přenosu. Hodnota true pro binární režim a hodnotu false ASCII. Výchozí hodnota: true. Tato vlastnost se dá použít jedině v případě, že typ přidružené propojené služby je typu: FtpServer. |No |

> [!NOTE]
> filename a FileFilter nelze použít současně.

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor FTP](data-factory-ftp-connector.md#dataset-properties) .

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete data ze serveru FTP, nastavte **typ zdroje** aktivity kopírování na **FileSystemSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| zahrnout |Určuje, zda mají být data rekurzivně čtena z dílčích složek nebo pouze ze zadané složky. |True, false (výchozí) |No |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor FTP](data-factory-ftp-connector.md#copy-activity-properties) .


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu HDFS, nastavte **typ** propojené služby na **HDFS** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **HDFS** . |Yes |
| URL |Adresa URL k HDFS |Yes |
| authenticationType |Anonymní nebo Windows. <br><br> Pokud chcete pro konektor HDFS použít **ověřování pomocí protokolu Kerberos** , v této části si odpovídajícím způsobem nastavte místní prostředí. |Yes |
| userName |Uživatelské jméno pro ověřování systému Windows. |Ano (pro ověřování systému Windows) |
| heslo |Heslo pro ověřování systému Windows. |Ano (pro ověřování systému Windows) |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení ke HDFS. |Yes |
| encryptedCredential |Výstup [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) přihlašovacích údajů pro přístup |No |

#### <a name="example-using-anonymous-authentication"></a>Příklad: použití anonymního ověřování

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

#### <a name="example-using-windows-authentication"></a>Příklad: použití ověřování systému Windows

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

Další informace najdete v článku o konektoru HDFS.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu HDFS, nastavte **typ** datové sady na **Shared** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| folderPath |Cesta ke složce Příklad: `myfolder`<br/><br/>Pro speciální znaky v řetězci použijte řídicí znak ' \ '. Například: pro folder\subfolder, zadejte podsložek složky \\ \\ a pro d:\samplefolder zadejte d: \\ \\ samplefolder.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** a mít tak cesty ke složkám na základě data a času začátku a konce řezu. |Yes |
| fileName |Pokud chcete, aby tabulka odkazovala na konkrétní soubor ve složce, zadejte název souboru do **FolderPath** . Pokud pro tuto vlastnost nezadáte žádnou hodnotu, odkazuje tabulka na všechny soubory ve složce.<br/><br/>Pokud pro výstupní datovou sadu není zadán název souboru, bude název vygenerovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt` (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy lze použít k zadání dynamického názvu souboru folderPath pro data časové řady. Příklad: folderPath parametrizované pro každou hodinu dat. |No |
| formát | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formátování [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Pokud chcete **Kopírovat soubory** mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady. |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**. Podporované úrovně: **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> filename a FileFilter nelze použít současně.

#### <a name="example"></a>Příklad

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

Další informace najdete v článku o konektoru HDFS.

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete data ze HDFS, nastavte **typ zdroje** aktivity kopírování na **FileSystemSource** a v části **zdroj** zadejte následující vlastnosti:

**FileSystemSource** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| zahrnout |Určuje, zda mají být data rekurzivně čtena z dílčích složek nebo pouze ze zadané složky. |True, false (výchozí) |No |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku o konektoru HDFS.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu SFTP, nastavte **typ** propojené služby na **SFTP** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| Hostitel | Název nebo IP adresa serveru SFTP. |Yes |
| port |Port, na kterém naslouchá server SFTP. Výchozí hodnota je: 21 |No |
| authenticationType |Zadejte typ ověřování. Povolené hodnoty: **Basic**, **SshPublicKey**. <br><br> Přečtěte si téma použití základního ověřování a části [ověřování pomocí veřejného klíče SSH](#using-ssh-public-key-authentication) na dalších vlastnostech a ukázkách JSON. |Yes |
| skipHostKeyValidation | Určete, zda se má přeskočit ověření klíče hostitele. | Ne. Výchozí hodnota: false |
| hostKeyFingerprint | Zadejte prst pro tisk klíče hostitele. | Ano, pokud `skipHostKeyValidation` je hodnota nastavena na false.  |
| gatewayName |Název Správa dat brány pro připojení k místnímu serveru SFTP. | Ano, pokud se kopírují data z místního serveru SFTP. |
| encryptedCredential | Šifrované přihlašovací údaje pro přístup k serveru SFTP. Automaticky generováno při zadání základního ověřování (uživatelské jméno + heslo) nebo ověřování SshPublicKey (uživatelské jméno + cesta k privátnímu klíči nebo obsah) v průvodci kopírováním nebo v překryvném okně ClickOnce. | Ne. Platí jenom při kopírování dat z místního serveru SFTP. |

#### <a name="example-using-basic-authentication"></a>Příklad: použití základního ověřování

Chcete-li použít základní ověřování, nastavte `authenticationType` jako `Basic` a zadejte následující vlastnosti kromě obecných typů KONEKTORů SFTP zavedených v poslední části:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| username | Uživatel, který má přístup k serveru SFTP. |Yes |
| heslo | Heslo pro uživatele (uživatelské jméno). | Yes |

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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Příklad: **základní ověřování pomocí šifrovaných přihlašovacích údajů**

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

Chcete-li použít základní ověřování, nastavte `authenticationType` jako `SshPublicKey` a zadejte následující vlastnosti kromě obecných typů KONEKTORů SFTP zavedených v poslední části:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| username |Uživatel, který má přístup k serveru SFTP |Yes |
| privateKeyPath | Zadejte absolutní cestu k souboru privátního klíče, ke kterému má brána přístup. | Zadejte buď `privateKeyPath` nebo `privateKeyContent` . <br><br> Platí jenom při kopírování dat z místního serveru SFTP. |
| privateKeyContent | Serializovaný řetězec obsahu privátního klíče. Průvodce kopírováním může přečíst soubor privátního klíče a automaticky extrahovat obsah privátního klíče. Pokud používáte jiný nástroj nebo sadu SDK, použijte místo toho vlastnost privateKeyPath. | Zadejte buď `privateKeyPath` nebo `privateKeyContent` . |
| Hesel | Zadejte heslo a heslo pro dešifrování privátního klíče, je-li soubor klíče chráněn pomocí hesla. | Ano, pokud je soubor privátního klíče chráněn pomocí fráze Pass. |

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Příklad: **ověřování SshPublicKey pomocí obsahu privátního klíče**

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

Další informace najdete v článku [konektor SFTP](data-factory-sftp-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu SFTP, nastavte **typ** datové sady na **Shared** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| folderPath |Dílčí cesta ke složce Pro speciální znaky v řetězci použijte řídicí znak ' \ '. Příklady najdete v tématu Ukázka propojené služby a definice datových sad.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** a mít tak cesty ke složkám na základě data a času začátku a konce řezu. |Yes |
| fileName |Pokud chcete, aby tabulka odkazovala na konkrétní soubor ve složce, zadejte název souboru do **FolderPath** . Pokud pro tuto vlastnost nezadáte žádnou hodnotu, odkazuje tabulka na všechny soubory ve složce.<br/><br/>Pokud pro výstupní datovou sadu není zadán název souboru, bude název vygenerovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt` (Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| fileFilter |Určete filtr, který se použije k výběru podmnožiny souborů v folderPath, nikoli všech souborů.<br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklady 1: `"fileFilter": "*.log"`<br/>Příklad 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> pro sadu vstupních dat Shared je použitelný filtr. Tato vlastnost není u HDFS podporována. |No |
| partitionedBy |partitionedBy lze použít k zadání dynamického názvu souboru folderPath pro data časové řady. Například folderPath parametrizované pro každou hodinu dat. |No |
| formát | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formátování [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Pokud chcete **Kopírovat soubory** mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady. |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**. Podporované úrovně: **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useBinaryTransfer |Určete, zda se má použít režim binárního přenosu. Hodnota true pro binární režim a hodnotu false ASCII. Výchozí hodnota: true. Tato vlastnost se dá použít jedině v případě, že typ přidružené propojené služby je typu: FtpServer. |No |

> [!NOTE]
> filename a FileFilter nelze použít současně.

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor SFTP](data-factory-sftp-connector.md#dataset-properties) .

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete data ze zdroje SFTP, nastavte **typ zdroje** aktivity kopírování na **FileSystemSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| zahrnout |Určuje, zda mají být data rekurzivně čtena z dílčích složek nebo pouze ze zadané složky. |True, false (výchozí) |No |



#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor SFTP](data-factory-sftp-connector.md#copy-activity-properties) .


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu HTTP, nastavte **typ** propojené služby na **http** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| url | Základní adresa URL webového serveru | Yes |
| authenticationType | Určuje typ ověřování. Povolené hodnoty jsou: **anonymní**, **základní**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Další vlastnosti a ukázky JSON pro tyto typy ověřování najdete v částech níže v této tabulce. | Yes |
| enableServerCertificateValidation | Určete, jestli se má povolit ověřování certifikátu TLS/SSL serveru, pokud je zdrojem webový server HTTPS. | Ne, výchozí hodnota je true. |
| gatewayName | Název Správa dat brány pro připojení k místnímu zdroji HTTP | Ano, pokud se kopírují data z místního zdroje HTTP |
| encryptedCredential | Šifrované přihlašovací údaje pro přístup ke koncovému bodu HTTP Automaticky generováno při konfiguraci informací o ověřování v průvodci kopírováním nebo v překryvném okně ClickOnce. | Ne. Platí jenom při kopírování dat z místního serveru HTTP. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Příklad: použití ověřování Basic, Digest nebo Windows
Nastavte `authenticationType` jako `Basic` , `Digest` nebo `Windows` a zadejte následující vlastnosti kromě obecných typů konektoru protokolu HTTP, které jsou zavedené výše:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| username | Uživatelské jméno pro přístup ke koncovému bodu HTTP. | Yes |
| heslo | Heslo pro uživatele (uživatelské jméno). | Yes |

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

#### <a name="example-using-clientcertificate-authentication"></a>Příklad: použití ověřování ClientCertificate

Pokud chcete použít základní ověřování, nastavte `authenticationType` jako `ClientCertificate` a zadejte následující vlastnosti, kromě výše uvedených obecných konektorů http:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| embeddedCertData | Obsah binárních dat v souboru PFX (Personal Information Exchange) kódovaný v kódování Base64. | Zadejte buď `embeddedCertData` nebo `certThumbprint` . |
| certThumbprint | Kryptografický otisk certifikátu, který byl nainstalován v úložišti certifikátů počítače brány. Platí pouze při kopírování dat z místního zdroje HTTP. | Zadejte buď `embeddedCertData` nebo `certThumbprint` . |
| heslo | Heslo přidružené k certifikátu | No |

Pokud používáte aplikaci `certThumbprint` pro ověřování a certifikát je nainstalován v osobním úložišti místního počítače, je třeba udělit oprávnění ke čtení pro službu brány:

1. Spusťte konzolu MMC (Microsoft Management Console). Přidejte modul snap-in **certifikáty** , který cílí na **místní počítač**.
2. Rozbalte **certifikáty**, **osobní** a klikněte na **certifikáty**.
3. Pravým tlačítkem myši klikněte na certifikát z osobního úložiště a vyberte **všechny úlohy** -> **spravovat soukromé klíče...**
3. Na kartě **zabezpečení** přidejte uživatelský účet, pod kterým je spuštěná služba hostitel Správa dat brány s přístupem pro čtení k certifikátu.

**Příklad: použití klientského certifikátu:** Tato propojená služba propojuje vaši datovou továrnu s místním webovým serverem HTTP. Používá klientský certifikát, který je nainstalovaný na počítači s nainstalovanou Správa datovou bránou.

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

#### <a name="example-using-client-certificate-in-a-file"></a>Příklad: použití klientského certifikátu v souboru
Tato propojená služba propojuje vaši datovou továrnu s místním webovým serverem HTTP. Používá soubor certifikátu klienta na počítači s nainstalovanou bránou Správa dat.

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

Další informace najdete v článku [konektor http](data-factory-http-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu HTTP, nastavte **typ** datové sady na **http** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data. Pokud cesta není zadaná, použije se jenom adresa URL zadaná v definici propojené služby. <br><br> Chcete-li vytvořit dynamickou adresu URL, můžete použít [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md), například: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"` . | No |
| requestMethod | Metoda HTTP. Povolené hodnoty jsou **Get** nebo **post**. | Ne. Výchozí je `GET`. |
| additionalHeaders | Další hlavičky požadavku HTTP | No |
| částmi | Tělo požadavku HTTP | No |
| formát | Pokud chcete jednoduše **načíst data z koncového bodu http, jak je** bez analýzy, přeskočte toto nastavení formátu. <br><br> Pokud chcete analyzovat obsah odpovědi HTTP během kopírování, podporují se tyto typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Další informace najdete v částech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formátování [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**. Podporované úrovně: **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

#### <a name="example-using-the-get-default-method"></a>Příklad: použití metody GET (default)

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
Další informace najdete v článku [konektor http](data-factory-http-connector.md#dataset-properties) .

### <a name="http-source-in-copy-activity"></a>Zdroj HTTP v aktivitě kopírování
Pokud kopírujete data ze zdroje HTTP, nastavte **typ zdroje** aktivity kopírování na **HttpSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| -------- | ----------- | -------- |
| httpRequestTimeout | Časový limit (TimeSpan) požadavku HTTP získat odpověď. Je časový limit pro získání odpovědi, nikoli časový limit pro čtení dat odpovědi. | Ne. Výchozí hodnota: 00:01:40 |


#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor http](data-factory-http-connector.md#copy-activity-properties) .

## <a name="odata"></a>OData

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu OData, nastavte **typ** propojené služby na **OData** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| url |Adresa URL služby OData. |Yes |
| authenticationType |Typ ověřování, který se používá pro připojení ke zdroji OData <br/><br/> Pro Cloud OData jsou možné hodnoty anonymní, základní a OAuth (Poznámka Azure Data Factory aktuálně podporuje jenom Azure Active Directory OAuth). <br/><br/> Pro místní OData jsou možné hodnoty anonymní, základní a Windows. |Yes |
| username |Pokud používáte základní ověřování, zadejte uživatelské jméno. |Ano (pouze pokud používáte základní ověřování) |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ano (pouze pokud používáte základní ověřování) |
| authorizedCredential |Pokud používáte OAuth, klikněte na tlačítko **autorizovat** v průvodci kopírováním Data Factory nebo v editoru a zadejte své přihlašovací údaje. hodnota této vlastnosti se vygeneruje automaticky. |Ano (jenom v případě, že používáte ověřování OAuth) |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k místní službě OData. Zadejte pouze v případě, že kopírujete data z místního zdroje OData. |No |

#### <a name="example---using-basic-authentication"></a>Příklad: použití základního ověřování
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Příklad: použití anonymního ověřování

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Příklad: použití ověřování systému Windows při přístupu k místnímu zdroji OData

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

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Příklad: použití ověřování OAuth přístup k cloudu ve zdroji OData
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

Další informace najdete v článku [konektor OData](data-factory-odata-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu OData, nastavte **typ** datové sady na **ODataResource** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| program |Cesta k prostředku OData |No |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor OData](data-factory-odata-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data ze zdroje OData, nastavte **typ zdroje** aktivity kopírování na **RelationalSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Příklad | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |"? $select = název, popis&$top = 5" |No |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor OData](data-factory-odata-connector.md#copy-activity-properties) .


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu ODBC, nastavte **typ** propojené služby na **OnPremisesOdbc** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| připojovací řetězec |Část připojovacího řetězce bez přístupu k přístupu a volitelné šifrované přihlašovací údaje. Podívejte se na příklady v následujících oddílech. |Yes |
| pověření |Část přístupového pověření v připojovacím řetězci, kterou jste zadali ve formátu hodnoty vlastnosti specifické pro ovladač. Příklad: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |No |
| authenticationType |Typ ověřování, který se používá pro připojení k úložišti dat ODBC. Možné hodnoty jsou: Anonymous a Basic. |Yes |
| username |Pokud používáte základní ověřování, zadejte uživatelské jméno. |No |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |No |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k úložišti dat ODBC. |Yes |

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
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Příklad: použití základního ověřování s šifrovanými přihlašovacími údaji
Přihlašovací údaje můžete šifrovat pomocí rutiny [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) .

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

#### <a name="example-using-anonymous-authentication"></a>Příklad: použití anonymního ověřování

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

Další informace najdete v článku [konektor ODBC](data-factory-odbc-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu rozhraní ODBC, nastavte **typ** datové sady na **relační** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky v úložišti dat rozhraní ODBC. |Yes |


#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor ODBC](data-factory-odbc-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z úložiště dat ODBC, nastavte **typ zdroje** aktivity kopírování na **RelationalSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |Yes |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor ODBC](data-factory-odbc-connector.md#copy-activity-properties) .

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Propojená služba
Pokud chcete definovat propojenou službu Salesforce, nastavte **typ** propojené služby na **Salesforce** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| environmentUrl | Zadejte adresu URL instance Salesforce. <br><br> -Výchozí hodnota je https: \/ /Login.Salesforce.com. <br> – Chcete-li kopírovat data z izolovaného prostoru, zadejte " https://test.salesforce.com ". <br> – Chcete-li kopírovat data z vlastní domény, zadejte například "https://[doména]. my. Salesforce. com". |No |
| username |Zadejte uživatelské jméno pro uživatelský účet. |Yes |
| heslo |Zadejte heslo pro uživatelský účet. |Yes |
| Element |Zadejte token zabezpečení pro uživatelský účet. Pokyny, jak resetovat nebo získat token zabezpečení, najdete v tématu [získání tokenu zabezpečení](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) . Obecné informace o tokenech zabezpečení najdete v tématu [zabezpečení a rozhraní API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Yes |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Salesforce](data-factory-salesforce-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Salesforce, nastavte **typ** datové sady na **relační** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky v Salesforce. |Ne (Pokud je zadán **dotaz** na **RelationalSource** ) |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor Salesforce](data-factory-salesforce-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data ze služby Salesforce, nastavte **typ zdroje** aktivity kopírování na **RelationalSource** a v části **zdroj** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Dotaz SQL-92 nebo dotaz na [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) . Například `select * from MyTable__c`. |Ne (Pokud je zadaný **TableName** **objektu DataSet** ) |

#### <a name="example"></a>Příklad



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
> Část "__c" názvu rozhraní API je nutná pro libovolný vlastní objekt.

Další informace najdete v článku [konektor Salesforce](data-factory-salesforce-connector.md#copy-activity-properties) .

## <a name="web-data"></a>Webová data

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat webovou propojenou službu, nastavte **typ** propojené služby na **Web** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| URL |Adresa URL webového zdroje |Yes |
| authenticationType |Anonymous. |Yes |


#### <a name="example"></a>Příklad


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

Další informace najdete v článku [konektor pro web Table](data-factory-web-table-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datová sada
Chcete-li definovat webovou datovou sadu, nastavte **typ** datové sady na **webtable** a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ |Typ datové sady musí být nastavené na **Webtable** . |Yes |
| program |Relativní adresa URL k prostředku, který obsahuje tabulku. |Ne. Pokud cesta není zadaná, použije se jenom adresa URL zadaná v definici propojené služby. |
| index |Index tabulky v prostředku Postup pro získání indexu tabulky na stránce HTML najdete v části získání indexu tabulky v oddílu stránky HTML. |Yes |

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor pro web Table](data-factory-web-table-connector.md#dataset-properties) .

### <a name="web-source-in-copy-activity"></a>Webový zdroj v aktivitě kopírování
Pokud kopírujete data z webové tabulky, nastavte **typ zdroje** aktivity kopírování na **websource**. V současné době platí, že pokud je zdroj v aktivitě kopírování typu **websource**, nejsou podporovány žádné další vlastnosti.

#### <a name="example"></a>Příklad

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

Další informace najdete v článku [konektor pro web Table](data-factory-web-table-connector.md#copy-activity-properties) .

## <a name="compute-environments"></a>VÝPOČETNÍ PROSTŘEDÍ
Následující tabulka uvádí výpočetní prostředí podporovaná nástrojem Data Factory a aktivity transformace, které se na nich můžou spouštět. Kliknutím na odkaz pro výpočetní prostředky, které vás zajímáte, uvidíte schémata JSON pro propojenou službu, která je propojí s datovou továrnou.

| Výpočetní prostředí | Aktivity |
| --- | --- |
| [Cluster HDInsight na vyžádání](#on-demand-azure-hdinsight-cluster) nebo [vlastní cluster HDInsight](#existing-azure-hdinsight-cluster) |[Vlastní aktivita rozhraní .NET](#net-custom-activity), [aktivita v podregistru](#hdinsight-hive-activity), aktivita [prasete](#hdinsight-pig-activity), aktivita [MapReduce](#hdinsight-mapreduce-activity), aktivita streamování Hadoop, [aktivita Sparku](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[Vlastní aktivita .NET](#net-custom-activity) |
| [Azure Machine Learning Studio (klasický)](#azure-machine-learning-studio-classic) | Aktivita [provedení dávkového zpracování Azure Machine Learning Studio (Classic)](#azure-machine-learning-studio-classic-batch-execution-activity), [Azure Machine Learning Studio (Classic) aktivita aktualizace prostředku](#azure-machine-learning-studio-classic-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[U-SQL Data Lake Analytics](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database), [Analýza Azure synapse](#azure-synapse-analytics), [SQL Server](#sql-server-stored-procedure) |[Uložená procedura](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Cluster Azure HDInsight na vyžádání
Služba Azure Data Factory může automaticky vytvořit cluster HDInsight na vyžádání založený na Windows/Linux pro zpracování dat. Cluster se vytvoří ve stejné oblasti jako účet úložiště (vlastnost linkedServiceName ve formátu JSON) přidružený ke clusteru. V této propojené službě můžete spustit následující aktivity transformace: [vlastní aktivita rozhraní .NET](#net-custom-activity), [aktivita v podregistru](#hdinsight-hive-activity), aktivita [prasete](#hdinsight-pig-activity), aktivita [MapReduce](#hdinsight-mapreduce-activity), aktivita streamování Hadoop, [aktivita Sparku](#hdinsight-spark-activity).

### <a name="linked-service"></a>Propojená služba
V následující tabulce najdete popis vlastností použitých v definici Azure JSON pro propojenou službu HDInsight na vyžádání.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type by měla být nastavená na **HDInsightOnDemand**. |Yes |
| clusterSize |Počet uzlů pracovních procesů nebo datových uzlů v clusteru. Cluster HDInsight se vytvoří s 2 hlavními uzly spolu s počtem pracovních uzlů, které pro tuto vlastnost zadáte. Uzly mají velikost Standard_D3 se 4 jádry, takže cluster se čtyřmi pracovními uzly má 24 jader (4 \* 4 = 16 jader pro pracovní uzly a 2 \* 4 = 8 jader pro hlavní uzly). Podrobnosti o Standard_D3 vrstvě najdete [v tématu Vytvoření clusterů Hadoop se systémem Linux v HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) . |Yes |
| TimeToLive |Povolený čas nečinnosti pro cluster HDInsight na vyžádání. Určuje, jak dlouho zůstane cluster HDInsight na vyžádání aktivní po dokončení spuštění aktivity, pokud v clusteru nejsou žádné další aktivní úlohy.<br/><br/>Pokud například spuštění aktivity trvá 6 minut a TimeToLive je nastaveno na 5 minut, zůstane cluster aktivní po dobu 5 minut po 6 minutách zpracování spuštění aktivity. Pokud se spustí další spuštění aktivity s oknem o 6 minutách, zpracuje se stejným clusterem.<br/><br/>Vytvoření clusteru HDInsight na vyžádání je náročná operace (může chvíli trvat), proto toto nastavení použijte, pokud chcete zlepšit výkon objektu pro vytváření dat, a to tak, že znovu použijete cluster HDInsight na vyžádání.<br/><br/>Pokud nastavíte hodnotu TimeToLive na 0, cluster se odstraní, jakmile se aktivita spustí ve zpracování. Na druhé straně platí, že pokud nastavíte vysokou hodnotu, cluster může být nečinný zbytečně, což bude mít za následek vysoké náklady. Proto je důležité nastavit odpovídající hodnotu podle svých potřeb.<br/><br/>Pokud je hodnota vlastnosti TimeToLive správně nastavená, může více kanálů sdílet stejnou instanci clusteru HDInsight na vyžádání. |Yes |
| verze |Verze clusteru HDInsight. Podrobnosti najdete v tématu [podporované verze služby HDInsight v Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |No |
| linkedServiceName |Azure Storage propojená služba, kterou má cluster na vyžádání použít k ukládání a zpracování dat. <p>V současné době nemůžete vytvořit cluster HDInsight na vyžádání, který jako úložiště používá Azure Data Lake Store. Pokud chcete uložit výsledná data ze zpracování HDInsight ve Azure Data Lake Store, pomocí aktivity kopírování zkopírujte data z Azure Blob Storage do Azure Data Lake Store.</p>  | Yes |
| additionalLinkedServiceNames |Určuje další účty úložiště pro propojenou službu HDInsight, aby je služba Data Factory mohla zaregistrovat vaším jménem. |No |
| osType |Typ operačního systému. Povolené hodnoty jsou: Windows (výchozí) a Linux |No |
| hcatalogLinkedServiceName |Název propojené služby Azure SQL, která odkazuje na databázi HCatalog. Cluster HDInsight na vyžádání se vytvoří pomocí Azure SQL Database jako metastore. |No |

### <a name="json-example"></a>Příklad JSON
Následující JSON definuje propojenou službu HDInsight na vyžádání v systému Linux. Služba Data Factory při zpracování datového řezu automaticky vytvoří cluster HDInsight se **systémem Linux** .

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

Další informace najdete v článku věnovaném [COMPUTE propojeným službám](data-factory-compute-linked-services.md) .

## <a name="existing-azure-hdinsight-cluster"></a>Existující cluster Azure HDInsight
Můžete vytvořit propojenou službu Azure HDInsight a zaregistrovat si vlastní cluster HDInsight s Data Factory. V této propojené službě můžete spustit následující aktivity transformace dat: [vlastní aktivita rozhraní .NET](#net-custom-activity), [aktivita v podregistru](#hdinsight-hive-activity), aktivita [prasete](#hdinsight-pig-activity), [aktivita MapReduce](#hdinsight-mapreduce-activity), aktivita streamování Hadoop, [aktivita Sparku](#hdinsight-spark-activity).

### <a name="linked-service"></a>Propojená služba
V následující tabulce najdete popis vlastností použitých v definici Azure JSON propojené služby Azure HDInsight.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type by měla být nastavená na **HDInsight**. |Yes |
| clusterUri |Identifikátor URI clusteru HDInsight. |Yes |
| username |Zadejte jméno uživatele, který se má použít pro připojení k existujícímu clusteru HDInsight. |Yes |
| heslo |Zadejte heslo pro uživatelský účet. |Yes |
| linkedServiceName | Název propojené služby Azure Storage, která odkazuje na úložiště objektů BLOB v Azure používané clusterem HDInsight. <p>V současné době nelze pro tuto vlastnost zadat propojenou službu Azure Data Lake Store. Pokud má cluster HDInsight přístup k Data Lake Store, můžete k datům v Azure Data Lake Store přistupovat ze skriptů z podregistru nebo vepřového prasete. </p>  |Yes |

Verze podporovaných clusterů HDInsight najdete v tématu [podporované verze HDInsight](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

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
Můžete vytvořit propojenou službu Azure Batch k registraci fondu virtuálních počítačů (VM) s datovou továrnou. Vlastní aktivity rozhraní .NET můžete spustit buď pomocí Azure Batch nebo Azure HDInsight. V této propojené službě můžete spustit [vlastní aktivitu rozhraní .NET](#net-custom-activity) .

### <a name="linked-service"></a>Propojená služba
Následující tabulka uvádí popis vlastností použitých v definici Azure JSON propojené služby Azure Batch.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type by měla být nastavená na **AzureBatch**. |Yes |
| accountName |Název Azure Batch účtu |Yes |
| accessKey |Přístupový klíč pro účet Azure Batch |Yes |
| poolName |Název fondu virtuálních počítačů. |Yes |
| linkedServiceName |Název propojené služby Azure Storage přidružené k této Azure Batch propojené službě. Tato propojená služba se používá pro pracovní soubory potřebné ke spuštění aktivity a uložení protokolů spuštění aktivit. |Yes |


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

## <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasický)
Vytvoříte propojenou službu Azure Machine Learning Studio (Classic) pro registraci koncového bodu dávkového vyhodnocování (klasického) studia s datovou továrnou. Dvě aktivity transformace dat, které lze spustit v této propojené službě: [Azure Machine Learning Studio (klasický) aktivita spuštění dávky](#azure-machine-learning-studio-classic-batch-execution-activity), [Azure Machine Learning Studio (Classic) aktivita aktualizace prostředku](#azure-machine-learning-studio-classic-update-resource-activity).

### <a name="linked-service"></a>Propojená služba
Následující tabulka uvádí popis vlastností použitých v definici Azure JSON propojené služby pro Studio (Classic).

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| Typ |Vlastnost Type by měla být nastavená na: **AzureML**. |Yes |
| mlEndpoint |Adresa URL dávkového vyhodnocování |Yes |
| apiKey |Rozhraní API modelu publikovaného pracovního prostoru. |Yes |

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
Vytvoříte propojenou službu **Azure Data Lake Analytics** , abyste propojili výpočetní službu Azure Data Lake Analytics s datovou továrnou Azure předtím, než v kanálu použijete [aktivitu Data Lake Analytics U-SQL](data-factory-usql-activity.md) .

### <a name="linked-service"></a>Propojená služba

Následující tabulka uvádí popis vlastností použitých v definici JSON propojené služby Azure Data Lake Analytics.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| Typ |Vlastnost Type by měla být nastavená na: **AzureDataLakeAnalytics**. |Yes |
| accountName |Azure Data Lake Analytics název účtu. |Yes |
| dataLakeAnalyticsUri |Azure Data Lake Analytics identifikátor URI. |No |
| autorizace |Autorizační kód se načte automaticky po kliknutí na tlačítko **autorizovat** v editoru Data Factory a dokončuje se přihlašovací jméno OAuth. |Yes |
| subscriptionId |ID předplatného Azure |Ne (Pokud není zadaný, použije se předplatné datové továrny). |
| resourceGroupName |Název skupiny prostředků Azure |Ne (Pokud není zadaný, použije se skupina prostředků objektu pro vytváření dat). |
| sessionId |ID relace z autorizační relace OAuth. Každé ID relace je jedinečné a dá se použít jenom jednou. Když použijete Editor Data Factory, toto ID se automaticky vygeneruje. |Yes |


#### <a name="json-example"></a>Příklad JSON
Následující příklad poskytuje definici JSON pro propojenou službu Azure Data Lake Analytics.

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

## <a name="sql-server-stored-procedure"></a>SQL Server uloženou proceduru

Vytvoříte propojenou službu SQL Server a použijete ji s [aktivitou uložené procedury](data-factory-stored-proc-activity.md) k vyvolání uložené procedury z kanálu Data Factory.

### <a name="linked-service"></a>Propojená služba
Vytvoříte propojenou službu typu **OnPremisesSqlServer** , která bude propojit databázi SQL Server s datovou továrnou. Následující tabulka uvádí popis pro prvky JSON specifické pro SQL Server propojenou službu.

Následující tabulka uvádí popis pro prvky JSON specifické pro SQL Server propojenou službu.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type by měla být nastavená na: **OnPremisesSqlServer**. |Yes |
| připojovací řetězec |Zadejte informace připojovacího řetězce potřebné pro připojení k databázi SQL Server pomocí ověřování SQL nebo ověřování systému Windows. |Yes |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k databázi SQL Server. |Yes |
| username |Pokud používáte ověřování systému Windows, zadejte uživatelské jméno. Příklad: **DomainName \\ uživatelské_jméno**. |No |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |No |

Přihlašovací údaje můžete šifrovat pomocí rutiny **New-AzDataFactoryEncryptValue** a použít je v připojovacím řetězci, jak je znázorněno v následujícím příkladu (vlastnost **EncryptedCredential** ):

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
#### <a name="example-json-for-using-windows-authentication"></a>Příklad: JSON pro použití ověřování systému Windows

Pokud je zadané uživatelské jméno a heslo, brána je použije k zosobnění zadaného uživatelského účtu pro připojení k SQL Server databázi. V opačném případě se brána připojí k SQL Server přímo s kontextem zabezpečení brány (spouštěcí účet).

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

Další informace najdete v článku [konektor SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) .

## <a name="data-transformation-activities"></a>AKTIVITY TRANSFORMACE DAT

Aktivita | Description
-------- | -----------
[Aktivita v podregistru HDInsight](#hdinsight-hive-activity) | Aktivita podregistru HDInsight v Data Factoryovém kanálu spouští dotazy na podregistry ve vašem clusteru HDInsight založeném na systému Windows/Linux nebo na vyžádání.
[Aktivita prasete v HDInsight](#hdinsight-pig-activity) | Aktivita prasete v HDInsight v kanálu Data Factory spouští dotazy na prasaty na vašem clusteru HDInsight založeném na systému Windows/Linux nebo na vyžádání.
[Aktivita MapReduce služby HDInsight](#hdinsight-mapreduce-activity) | Aktivita MapReduce služby HDInsight v kanálu Data Factory spouští programy MapReduce na vašem clusteru HDInsight založeném na systému Windows nebo Linux na vyžádání.
[Aktivita Streamování služby HDInsight](#hdinsight-streaming-activity) | Aktivita streamování HDInsight v kanálu Data Factory spouští programy pro streamování Hadoop na vašem clusteru HDInsight založeném na systému Windows/Linux nebo na vyžádání.
[Aktivita Spark služby HDInsight](#hdinsight-spark-activity) | Aktivita HDInsight Spark v kanálu Data Factory spouští programy Spark ve vlastním clusteru HDInsight.
[Aktivita Provedení dávky služby Azure Machine Learning Studio (klasická verze)](#azure-machine-learning-studio-classic-batch-execution-activity) | Azure Data Factory vám umožní snadno vytvářet kanály, které používají publikovanou webovou službu studia (Classic) pro prediktivní analýzy. Pomocí aktivity dávkového spuštění v kanálu Azure Data Factory můžete vyvolat webovou službu studia (Classic), která předpovědi data ve službě Batch.
[Aktivita Aktualizace prostředků služby Azure Machine Learning Studio (klasická verze)](#azure-machine-learning-studio-classic-update-resource-activity) | Prediktivní modely v Azure Machine Learning Studio (klasický) experimenty bodování se v průběhu času musí přenášet pomocí nových vstupních datových sad. Až budete s rekurzem hotovi, chcete aktualizovat webovou službu bodování pomocí převýukového modelu Machine Learning. Aktivitu aktualizovat prostředek můžete použít k aktualizaci webové služby pomocí nově vyučeného modelu.
[Aktivita Uložená procedura](#stored-procedure-activity) | Pomocí aktivity uložená procedura v Data Factoryovém kanálu můžete vyvolat uloženou proceduru v jednom z následujících úložišť dat: Azure SQL Database, Azure synapse Analytics, SQL Server databáze ve vašem podniku nebo virtuálním počítači Azure.
[Aktivita Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) | Aktivita Data Lake Analytics U-SQL spouští skript U-SQL v clusteru Azure Data Lake Analytics.
[Vlastní aktivita .NET](#net-custom-activity) | Pokud potřebujete transformovat data způsobem, který není podporován Data Factory, můžete vytvořit vlastní aktivitu s vlastní logikou zpracování dat a použít aktivitu v kanálu. Vlastní aktivitu rozhraní .NET můžete nakonfigurovat tak, aby běžela buď pomocí služby Azure Batch, nebo clusteru Azure HDInsight.


## <a name="hdinsight-hive-activity"></a>Aktivita Hivu služby HDInsight
V definici JSON aktivity podregistru můžete zadat následující vlastnosti. Vlastnost Type aktivity musí být: **HDInsightHive**. Nejprve musíte vytvořit propojenou službu HDInsight a zadat její název jako hodnotu pro vlastnost **linkedServiceName** . Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na HDInsightHive:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| script |Zadat vložený skript podregistru |No |
| Cesta ke skriptu |Uložte skript podregistru do úložiště objektů BLOB v Azure a zadejte cestu k souboru. Použijte vlastnost Script nebo scriptPath. Nelze použít současně. V názvu souboru se rozlišují malá a velká písmena. |No |
| definuje |Zadejte parametry jako páry klíč/hodnota pro odkazování v rámci skriptu pro podregistr pomocí příkazu ' hiveconf '. |No |

Tyto vlastnosti typu jsou specifické pro aktivitu podregistru. Další vlastnosti (mimo oddíl typeProperties) jsou podporovány pro všechny aktivity.

### <a name="json-example"></a>Příklad JSON
Následující JSON definuje aktivitu podregistru HDInsight v kanálu.

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

Další informace najdete v článku o [aktivitě podregistru](data-factory-hive-activity.md) .

## <a name="hdinsight-pig-activity"></a>Aktivita Pig služby HDInsight
V definici JSON aktivity prasete můžete zadat následující vlastnosti. Vlastnost Type aktivity musí být: **HDInsightPig**. Nejprve musíte vytvořit propojenou službu HDInsight a zadat její název jako hodnotu pro vlastnost **linkedServiceName** . Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na HDInsightPig:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| script |Zadat vložený skript prasete |No |
| Cesta ke skriptu |Uložte skript prasete do úložiště objektů BLOB v Azure a zadejte cestu k souboru. Použijte vlastnost Script nebo scriptPath. Nelze použít současně. V názvu souboru se rozlišují malá a velká písmena. |No |
| definuje |Zadejte parametry jako páry klíč/hodnota pro odkazování v rámci skriptu pro vepřové prostředí. |No |

Tyto vlastnosti typu jsou specifické pro aktivitu prasete. Další vlastnosti (mimo oddíl typeProperties) jsou podporovány pro všechny aktivity.

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

Další informace najdete v článku o aktivitě prasete.

## <a name="hdinsight-mapreduce-activity"></a>Aktivita MapReduce služby HDInsight
V definici JSON aktivity MapReduce můžete zadat následující vlastnosti. Vlastnost Type aktivity musí být: **HDInsightMapReduce**. Nejprve musíte vytvořit propojenou službu HDInsight a zadat její název jako hodnotu pro vlastnost **linkedServiceName** . Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na HDInsightMapReduce:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| jarLinkedService | Název propojené služby pro Azure Storage, která obsahuje soubor JAR. | Yes |
| jarFilePath | Cesta k souboru JAR v Azure Storage. | Yes |
| NázevTřídy | Název hlavní třídy v souboru JAR | Yes |
| náhodné | Seznam argumentů oddělených čárkou pro program MapReduce Za běhu vidíte několik dalších argumentů (například: MapReduce. job. Tags) z rozhraní MapReduce. Chcete-li odlišit argumenty pomocí argumentů MapReduce, zvažte použití možnosti i hodnoty jako argumentů, jak je znázorněno v následujícím příkladu (-s,--Input,--Output atd.). Jedná se o možnosti ihned po jejich hodnotách.) | No |

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

Další informace najdete v článku o [aktivitě MapReduce](data-factory-map-reduce.md) .

## <a name="hdinsight-streaming-activity"></a>Aktivita Streamování služby HDInsight
V definici JSON aktivity streamování Hadoop můžete zadat následující vlastnosti. Vlastnost Type aktivity musí být: **HDInsightStreaming**. Nejprve musíte vytvořit propojenou službu HDInsight a zadat její název jako hodnotu pro vlastnost **linkedServiceName** . Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na HDInsightStreaming:

| Vlastnost | Popis |
| --- | --- |
| Mapper | Název spustitelného souboru mapovače. V tomto příkladu cat.exe je spustitelný soubor mapovače.|
| redukce | Název spustitelného souboru nástroje pro zmenšení. V tomto příkladu je wc.exe spustitelný soubor pro snížení. |
| vstup | Vstupní soubor (včetně umístění) pro mapovač. V příkladu: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"` : adfsample je kontejnerem objektů blob, příkladem/data/Gutenberg je složka a davinci.txt je objekt BLOB. |
| output | Výstupní soubor (včetně umístění) pro redukci. Výstup úlohy služby streamování Hadoop se zapisuje do umístění zadaného pro tuto vlastnost. |
| filePaths | Cesty pro spustitelné soubory Mapper a zmenšení. V příkladu: "adfsample/example/Apps/wc.exe", adfsample je kontejnerem objektů blob, příkladem/aplikacemi je složka a wc.exe je spustitelný soubor. |
| fileLinkedService | Azure Storage propojená služba, která představuje úložiště Azure, které obsahuje soubory zadané v části cesty souborů. |
| náhodné | Seznam argumentů oddělených čárkou pro program MapReduce Za běhu vidíte několik dalších argumentů (například: MapReduce. job. Tags) z rozhraní MapReduce. Chcete-li odlišit argumenty pomocí argumentů MapReduce, zvažte použití možnosti i hodnoty jako argumentů, jak je znázorněno v následujícím příkladu (-s,--Input,--Output atd.). Jedná se o možnosti ihned po jejich hodnotách.) |
| GetDebugInfo – | Volitelný element. Pokud je nastavená chyba, protokoly se stáhnou pouze při selhání. Pokud je nastavené na vše, protokoly se stáhnou vždycky bez ohledu na stav spuštění. |

> [!NOTE]
> Pro aktivitu streamování Hadoop **pro vlastnost Outputs je nutné** zadat výstupní datovou sadu. Tato datová sada může být pouze fiktivní datová sada, která je požadována pro řízení plánu kanálu (každou hodinu, každý den atd.). Pokud aktivita nepřijímá vstup, můžete přeskočit určení vstupní datové sady pro aktivitu pro vlastnost **Inputs** .

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

Další informace najdete v článku o [aktivitě streamování Hadoop](data-factory-hadoop-streaming-activity.md) .

## <a name="hdinsight-spark-activity"></a>Aktivita Spark služby HDInsight
V definici JSON aktivity Sparku můžete zadat následující vlastnosti. Vlastnost Type aktivity musí být: **HDInsightSpark**. Nejprve musíte vytvořit propojenou službu HDInsight a zadat její název jako hodnotu pro vlastnost **linkedServiceName** . Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na HDInsightSpark:

| Vlastnost | Popis | Povinné |
| -------- | ----------- | -------- |
| rootPath | Kontejner a složka Azure Blob, které obsahují soubor Spark. V názvu souboru se rozlišují malá a velká písmena. | Yes |
| entryFilePath | Relativní cesta ke kořenové složce kódu nebo balíčku Spark | Yes |
| NázevTřídy | Hlavní třída Java/Spark aplikace | No |
| náhodné | Seznam argumentů příkazového řádku pro program Spark. | No |
| proxyUser | Uživatelský účet, který se má zosobnit pro spuštění programu Spark | No |
| sparkConfig | Vlastnosti konfigurace Sparku | No |
| GetDebugInfo – | Určuje, kdy se soubory protokolu Spark zkopírují do úložiště Azure používaného clusterem HDInsight (nebo), který určuje sparkJobLinkedService. Povolené hodnoty: žádné, vždy nebo chyba. Výchozí hodnota: Žádný. | No |
| sparkJobLinkedService | Propojená služba Azure Storage, která obsahuje soubor úlohy Spark, závislosti a protokoly.  Pokud pro tuto vlastnost nezadáte hodnotu, použije se úložiště přidružené ke clusteru HDInsight. | No |

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

- Vlastnost **Type** je nastavená na **HDInsightSpark**.
- **RootPath** je nastavená na **adfspark \\ pyFiles** , kde Adfspark je kontejnerem objektů BLOB v Azure a pyFiles je v tomto kontejneru v dobré složce. V tomto příkladu je Blob Storage Azure ta, která je přidružená k clusteru Spark. Soubor můžete nahrát do jiného Azure Storage. Pokud to uděláte, vytvořte propojenou službu Azure Storage k propojení tohoto účtu úložiště s datovou továrnou. Pak zadejte název propojené služby jako hodnotu vlastnosti **sparkJobLinkedService** . Podrobnosti o této vlastnosti a dalších vlastnostech podporovaných aktivitou Spark najdete v tématu vlastnosti aktivity Spark.
- **EntryFilePath** je nastaven na **test.py**, což je soubor Python.
- Vlastnost **GetDebugInfo –** je nastavena na hodnotu **vždy**, což znamená, že soubory protokolu jsou vždy generovány (úspěch nebo neúspěch).

    > [!IMPORTANT]
    > Doporučujeme, abyste tuto vlastnost nestavili na hodnotu vždy v produkčním prostředí, pokud neřešíte problém.
- Oddíl **výstupy** obsahuje jednu výstupní datovou sadu. Výstupní datovou sadu musíte zadat i v případě, že program Spark nevytváří žádný výstup. Výstupní datová sada nařídí plán pro kanál (každou hodinu, každý den atd.).

Další informace o aktivitě najdete v článku o [aktivitě Sparku](data-factory-spark.md) .

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Aktivita Provedení dávky služby Azure Machine Learning Studio (klasická verze)
V definici JSON aktivity spuštění dávky Azure Machine Learning Studio (Classic) můžete zadat následující vlastnosti. Vlastnost Type aktivity musí být: **AzureMLBatchExecution**. Nejprve musíte vytvořit propojenou službu studia (Classic) a zadat její název jako hodnotu vlastnosti **linkedServiceName** . Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na AzureMLBatchExecution:

Vlastnost | Popis | Povinné
-------- | ----------- | --------
webServiceInput | Datová sada, která má být předána jako vstup pro webovou službu Studio (Classic). Tato datová sada musí být také obsažena ve vstupech aktivity. |Použijte buď webServiceInput nebo webServiceInputs. |
webServiceInputs | Zadejte datové sady, které mají být předány jako vstupy pro webovou službu Studio (Classic). Pokud webová služba používá více vstupů, místo použití vlastnosti webServiceInput použijte vlastnost webServiceInputs. Datové sady, na které odkazuje **webServiceInputs** , musí být zahrnuté i do **vstupů** aktivity. | Použijte buď webServiceInput nebo webServiceInputs. |
webServiceOutputs | Datové sady, které jsou přiřazeny jako výstupy pro webovou službu Studio (Classic). Webová služba vrátí výstupní data v této datové sadě. | Yes |
globalParameters | Zadejte hodnoty pro parametry webové služby v této části. | No |

### <a name="json-example"></a>Příklad JSON
V tomto příkladu má aktivita datovou sadu **MLSqlInput** jako vstup a **MLSqlOutput** jako výstup. **MLSqlInput** je předán jako vstup do webové služby pomocí vlastnosti **webServiceInput** JSON. **MLSqlOutput** se předává jako výstup do webové služby pomocí vlastnosti **webServiceOutputs** JSON.

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

V příkladu JSON používá webová služba nasazený Studio (Classic) čtenář a modul zapisovače ke čtení a zápisu dat z a do Azure SQL Database. Tato webová služba zpřístupňuje následující čtyři parametry: název databázového serveru, název databáze, název uživatelského účtu serveru a heslo uživatelského účtu serveru.

> [!NOTE]
> Jako parametry webové služby lze předat pouze vstupy a výstupy aktivity AzureMLBatchExecution. Například ve výše uvedeném fragmentu kódu JSON je MLSqlInput jako vstup aktivity AzureMLBatchExecution, která se předává jako vstup do webové služby prostřednictvím parametru webServiceInput.

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Aktivita Aktualizace prostředků služby Azure Machine Learning Studio (klasická verze)
V Azure Machine Learning Studio (Classic) aktualizace definice JSON aktivity prostředku můžete zadat následující vlastnosti. Vlastnost Type aktivity musí být: **povinná**. Nejprve musíte vytvořit propojenou službu studia (Classic) a zadat její název jako hodnotu vlastnosti **linkedServiceName** . Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na povinná:

Vlastnost | Popis | Povinné
-------- | ----------- | --------
trainedModelName | Název převýukového modelu. | Yes |
trainedModelDatasetName | Datová sada ukazující na soubor iLearner vrácený operací retraining. | Yes |

### <a name="json-example"></a>Příklad JSON
Kanál má dvě aktivity: **AzureMLBatchExecution** a **povinná**. Aktivita spuštění dávky studia (Classic) vezme data školení jako vstup a vytvoří soubor iLearner jako výstup. Tato aktivita vyvolá webovou službu školení (zkušební experiment vydaný jako webovou službu) se vstupními školicími daty a přijme soubor ilearner z webové služby. PlaceholderBlob je pouze fiktivní výstupní datová sada, kterou služba Azure Data Factory vyžaduje ke spuštění kanálu.


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
V definici JSON aktivity U-SQL můžete zadat následující vlastnosti. Vlastnost Type pro aktivitu musí být: **DataLakeAnalyticsU-SQL**. Je nutné vytvořit propojenou službu Azure Data Lake Analytics a zadat její název jako hodnotu pro vlastnost **linkedServiceName** . Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na DATALAKEANALYTICSU-SQL:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| scriptPath |Cesta ke složce, která obsahuje skript U-SQL. Název souboru rozlišuje velká a malá písmena. |Ne (Pokud používáte skript) |
| scriptLinkedService |Propojená služba, která propojuje úložiště obsahující skript s datovou továrnou |Ne (Pokud používáte skript) |
| script |Místo zadání scriptPath a scriptLinkedService zadejte vložený skript. Například: "skript": "vytvořit databázi test". |Ne (Pokud používáte scriptPath a scriptLinkedService) |
| degreeOfParallelism |Maximální počet uzlů současně použitých ke spuštění úlohy. |No |
| upřednostněn |Určuje, které úlohy ze všech, které jsou zařazené do fronty, by měly být vybrány pro první spuštění. Čím nižší číslo, tím vyšší Priorita. |No |
| parameters |Parametry pro skript U-SQL |No |

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

Další informace najdete v tématu [Data Lake Analytics aktivity U-SQL](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Aktivita Uložená procedura
V definici JSON aktivity uložené procedury můžete zadat následující vlastnosti. Vlastnost Type aktivity musí být: **SqlServerStoredProcedure**. Musíte vytvořit jednu z následujících propojených služeb a zadat název propojené služby jako hodnotu pro vlastnost **linkedServiceName** :

- SQL Server
- Azure SQL Database
- Azure Synapse Analytics

Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na SqlServerStoredProcedure:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| storedProcedureName |Zadejte název uložené procedury v Azure SQL Database nebo Azure synapse Analytics reprezentované propojenou službou, kterou používá výstupní tabulka. |Yes |
| storedProcedureParameters |Zadejte hodnoty pro parametry uložené procedury. Pokud pro parametr potřebujete předat hodnotu null, použijte syntaxi: "param1": null (bez velkých malých písmen). V následující ukázce se dozvíte, jak tuto vlastnost použít. |No |

Pokud zadáte vstupní datovou sadu, musí být k dispozici (ve stavu "připraveno") pro spuštění aktivity uložená procedura. Vstupní datovou sadu nelze v uložené proceduře jako parametr spotřebovat. Slouží pouze ke kontrole závislosti před spuštěním aktivity uložené procedury. Pro aktivitu uložené procedury musíte zadat výstupní datovou sadu.

Výstupní datová sada určuje **plán** aktivity uložené procedury (každou hodinu, týdně, měsíčně atd.). Výstupní datová sada musí používat **propojenou službu** , která odkazuje na Azure SQL Database nebo Azure synapse Analytics nebo databáze SQL Server, ve které chcete uloženou proceduru spustit. Výstupní datová sada může sloužit jako způsob, jak předat výsledek uložené procedury pro následné zpracování jinou aktivitou ([řetězení aktivit](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) v kanálu. Data Factory však do této datové sady automaticky nezapisuje výstup uložené procedury. Jedná se o uloženou proceduru, která zapisuje do tabulky SQL, na kterou odkazuje výstupní datová sada. V některých případech může být výstupní datovou sadou **fiktivní datová** sada, která se používá pouze k zadání plánu pro spuštění aktivity uložené procedury.

### <a name="json-example"></a>Příklad JSON

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

Další informace najdete v článku [aktivita uložených procedur](data-factory-stored-proc-activity.md) .

## <a name="net-custom-activity"></a>Vlastní aktivita .NET
V definici JSON vlastní aktivity rozhraní .NET můžete zadat následující vlastnosti. Vlastnost Type aktivity musí být: **DotNetActivity**. Musíte vytvořit propojenou službu Azure HDInsight nebo propojenou službu Azure Batch a jako hodnotu pro vlastnost **linkedServiceName** zadat název propojené služby. Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na DotNetActivity:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| Doplňk | Název sestavení V tomto příkladu je to: **MyDotnetActivity.dll**. | Yes |
| EntryPoint |Název třídy, která implementuje rozhraní IDotNetActivity. V tomto příkladu je to: **MyDotNetActivityNS. MyDotNetActivity** , kde MyDotNetActivityNS je obor názvů a MyDotNetActivity je třída.  | Yes |
| PackageLinkedService | Název propojené služby Azure Storage, která odkazuje na úložiště objektů blob, které obsahuje soubor zip vlastní aktivity. V tomto příkladu je to: **AzureStorageLinkedService**.| Yes |
| PackageFile | Název souboru ZIP V tomto příkladu je to: **customactivitycontainer/MyDotNetActivity.zip**. | Yes |
| extendedProperties | Rozšířené vlastnosti, které lze definovat a předat kódu .NET. V tomto příkladu je proměnná **vlastnosti slicestart** nastavena na hodnotu založenou na systémové proměnné vlastnosti slicestart. | No |

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

Podrobné informace najdete v tématu [použití vlastních aktivit v Data Factory](data-factory-use-custom-activities.md) článku.

## <a name="next-steps"></a>Další kroky
Projděte si tyto kurzy:

- [Kurz: vytvoření kanálu s aktivitou kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Kurz: vytvoření kanálu s aktivitou podregistru](data-factory-build-your-first-pipeline.md)