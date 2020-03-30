---
title: Azure Data Factory – referenční příručka pro skriptování JSON
description: Poskytuje schémata JSON pro entity factory dat.
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
ms.openlocfilehash: 3492f917be8116d0eed0c7ec03ed8aa9ff506520
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346595"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory – referenční příručka pro skriptování JSON
> [!NOTE]
> Tento článek platí pro Data Factory verze 1.


Tento článek obsahuje schémata JSON a příklady pro definování entit Azure Data Factory (kanál, aktivita, datová sada a propojená služba).

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

| Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
| jméno | Název kanálu. Zadejte název, který představuje akci, kterou je aktivita nebo kanál nakonfigurován<br/><ul><li>Maximální počet znaků: 260.</li><li>Musí začínat písmenem nebo\_podtržítkem ( ).</li><li>Následující znaky nejsou povoleny: ".", "+", "?", "/", "<",">","*","%","&","","","","","&","","",""""""""""\\</li></ul> |Ano |
| description |Text popisující, k čemu se aktivita nebo kanál používá | Ne |
| activities | Obsahuje seznam aktivit. | Ano |
| start |Počáteční datum a čas pro kanál. Musí být ve [formátu ISO](https://en.wikipedia.org/wiki/ISO_8601). Například: 2014-10-14T16:32:41. <br/><br/>Je možné zadat místní čas, například čas EST. Zde je příklad: `2016-02-27T06:00:00**-05:00`, což je 6 AM EST.<br/><br/>Počáteční a koncové vlastnosti společně určují aktivní období pro kanál. Výstupní řezy jsou vyráběny pouze v tomto aktivním období. |Ne<br/><br/>Pokud zadáte hodnotu pro koncovou vlastnost, musíte zadat hodnotu pro vlastnost start.<br/><br/>Počáteční a koncový čas může být prázdný a vytvořit tak kanál. Je nutné zadat obě hodnoty pro nastavení aktivního období pro spuštění kanálu. Pokud při vytváření kanálu nezadáte počáteční a koncový čas, můžete je později nastavit pomocí rutiny Set-AzDataFactoryPipelineActivePeriod. |
| end |Koncové datum a čas pro kanál. Pokud je zadán, musí být ve formátu ISO. Například: 2014-10-14T17:32:41 <br/><br/>Je možné zadat místní čas, například čas EST. Zde je příklad: `2016-02-27T06:00:00**-05:00`, což je 6 AM EST.<br/><br/>Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti end hodnotu 9999-09-09. |Ne <br/><br/>Pokud zadáte hodnotu vlastnosti start, musíte zadat hodnotu pro koncovou vlastnost.<br/><br/>Viz poznámky pro **vlastnost start.** |
| jePaused |Pokud je nastavena na true, kanál se nespustí. Výchozí hodnota = false. Tuto vlastnost můžete použít k povolení nebo zakázání. |Ne |
| pipelineMode |Metoda plánování běží pro kanál. Povolené hodnoty jsou: naplánované (výchozí), jednorázové.<br/><br/>"Naplánované" označuje, že potrubí běží v zadaném časovém intervalu podle aktivního období (počáteční a koncový čas). "Onetime" označuje, že potrubí běží pouze jednou. Jednorázové kanály po vytvoření nelze aktuálně změnit nebo aktualizovat. Podrobnosti o jednorázovém nastavení najdete v [tématu Onetime pipeline.](data-factory-create-pipelines.md#onetime-pipeline) |Ne |
| expiraceČas |Doba po vytvoření, pro kterou je kanál platný a měla by zůstat zřízena. Pokud nemá žádné aktivní, neúspěšné nebo čekající spuštění, kanál se odstraní automaticky, jakmile dosáhne doby vypršení platnosti. |Ne |


## <a name="activity"></a>Aktivita
Struktura vysoké úrovně pro aktivitu v rámci definice kanálu (prvek aktivity) je následující:

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

Následující tabulka popisuje vlastnosti v definici aktivity JSON:

| Značka | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název aktivity. Zadejte název, který představuje akci, kterou je aktivita nakonfigurována.<br/><ul><li>Maximální počet znaků: 260.</li><li>Musí začínat písmenem nebo\_podtržítkem ( ).</li><li>Následující znaky nejsou povoleny: ".", "+", "?", "/", "<",">","*","%","&","","","","","&","","",""""""""""\\</li></ul> |Ano |
| description |Text popisující, k čemu se aktivita používá. |Ne |
| type |Určuje typ aktivity. Různé typy aktivit naleznete v oddílech [ÚLOŽIŠTĚ DAT](#data-stores) a [AKTIVITY TRANSFORMACE DAT.](#data-transformation-activities) |Ano |
| Vstupy |Vstupní tabulky používané aktivitou<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Ne pro aktivity HDInsightStreaming a SqlServerStoredProcedure <br/> <br/> Ano pro všechny ostatní |
| Výstupy |Výstupní tabulky používané aktivitou.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Ano |
| linkedServiceName |Název propojené služby používané aktivitou. <br/><br/>Aktivita může vyžadovat zadání propojené služby, která odkazuje na požadované výpočetní prostředí. |Ano, pro aktivity HDInsight, Aktivity Azure Machine Learning a Aktivita uložené procedury. <br/><br/>Ne ve všech ostatních případech |
| typeProperties |Vlastnosti v části typeProperties závisí na typu aktivity. |Ne |
| policy |Zásady, které ovlivňují chování aktivity za běhu. Pokud není zadán, jsou použity výchozí zásady. |Ne |
| scheduler |Vlastnost "plánovač" se používá k definování požadovaného plánování aktivity. Jeho podvlastnosti jsou stejné jako ve [vlastnosti dostupnosti v datové sadě](data-factory-create-datasets.md#dataset-availability). |Ne |

### <a name="policies"></a>Zásady
Zásady ovlivňují chování aktivity za běhu, konkrétně při zpracování řezu tabulky. V následující tabulce jsou uvedeny podrobnosti.

| Vlastnost | Povolené hodnoty | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| souběžnost |Integer <br/><br/>Maximální hodnota: 10 |1 |Počet souběžných spuštění aktivity.<br/><br/>Určuje počet paralelních spuštění aktivit, ke kterým může dojít na různých řezech. Například pokud aktivita potřebuje projít velkou sadu dostupných dat, s větší hodnota souběžnosti urychluje zpracování dat. |
| executionPriorityOrder |NejnovějšíPrvní<br/><br/>NejstaršíPrvní |NejstaršíPrvní |Určuje pořadí řezů dat, které jsou zpracovávány.<br/><br/>Například pokud máte 2 řezy (jeden se děje v 16:00 a další v 17:00) a oba čekají na spuštění. Pokud nastavíte spuštěníPriorityOrder být NewestFirst, řez v 17:00 je zpracována jako první. Podobně pokud nastavíte spuštěníPriorityORder být OldestFIrst, pak řez na 4 pm je zpracována. |
| retry |Integer<br/><br/>Maximální hodnota může být 10 |0 |Počet opakování před zpracováním dat pro řez je označen jako selhání. Spuštění aktivity pro řez dat je opakován až do zadaného počtu opakování. Opakování se provádí co nejdříve po selhání. |
| timeout |TimeSpan |00:00:00 |Časový čas aktivity. Příklad: 00:10:00 (znamená časový výtažek 10 min)<br/><br/>Pokud hodnota není zadána nebo je 0, časový čas je nekonečný.<br/><br/>Pokud doba zpracování dat na řezu překročí hodnotu časového limitu, je zrušena a systém se pokusí zopakovat zpracování. Počet opakování závisí na vlastnosti opakování. Když dojde k časovému výpadku, stav je nastaven na TimedOut. |
| Zpoždění |TimeSpan |00:00:00 |Určete zpoždění před zahájením zpracování dat řezu.<br/><br/>Provádění aktivity pro řez dat je spuštěn a zpoždění je po očekávané době provádění.<br/><br/>Příklad: 00:10:00 (znamená zpoždění 10 minut) |
| longRetry |Integer<br/><br/>Maximální hodnota: 10 |1 |Počet pokusů o dlouhou opakování před spuštěním řezu se nezdařilo.<br/><br/>LongRetry pokusy jsou rozmístěny longRetryInterval. Takže pokud potřebujete zadat čas mezi pokusy o opakování, použijte longRetry. Pokud jsou zadány Retry a longRetry jsou zadány, každý pokus longRetry zahrnuje pokusy opakovat a maximální počet pokusů je Opakovat * longRetry.<br/><br/>Například pokud máme následující nastavení v zásadách aktivity:<br/>Opakování: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Předpokládejme, že je pouze jeden řez ke spuštění (stav je Čekání) a provádění aktivity selže pokaždé. Zpočátku by došlo ke 3 po sobě jdoucím pokusům o provedení. Po každém pokusu by byl stav řezu Opakovat. Po prvních 3 pokusy jsou u konce, stav řezu by LongRetry.<br/><br/>Po hodině (to znamená longRetryInteval hodnota), by další sadu 3 po sobě jdoucích pokusů o spuštění. Poté by se stav řezu nezdařil a nepokoušeli by se o žádné další pokusy. Proto bylo provedeno celkem 6 pokusů.<br/><br/>Pokud je jakékoli spuštění úspěšné, stav řezu by byl Připraven a žádné další pokusy o opakování.<br/><br/>longRetry lze použít v situacích, kdy závislá data dorazí v nedeterministické době nebo celkové prostředí je šupinatá, za kterých dochází ke zpracování dat. V takových případech opakování jeden po druhém nemusí pomoci a to po časovém intervalu má za následek požadovaný výstup.<br/><br/>Upozornění: nenastavujte vysoké hodnoty pro longRetry nebo longRetryInterval. Vyšší hodnoty obvykle znamenají další systémové problémy. |
| longRetryInterval |TimeSpan |00:00:00 |Prodleva mezi dlouhými pokusy o opakování |

### <a name="typeproperties-section"></a>oddíl typeProperties
Oddíl typeProperties se pro každou aktivitu liší. Transformační aktivity mají pouze vlastnosti typu. Viz [aktivity transformace dat](#data-transformation-activities) v tomto článku pro ukázky JSON, které definují transformační aktivity v kanálu.

**Aktivita kopírování** má v části typeProperties dvě podsekce: **zdroj** a **jímku**. Viz data [stores](#data-stores) části v tomto článku pro ukázky JSON, které ukazují, jak používat úložiště dat jako zdroj nebo jímky.

### <a name="sample-copy-pipeline"></a>Ukázkový kanál kopírování
V následujícím ukázkovém kanálu je v části **activities** jedna aktivita typu **Kopírování**. V této [ukázce zkopíruje aktivitu Copy](data-factory-data-movement-activities.md) data z úložiště objektů blob Azure do databáze Azure SQL.

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

Viz data [stores](#data-stores) části v tomto článku pro ukázky JSON, které ukazují, jak používat úložiště dat jako zdroj nebo jímky.

Úplný návod k vytvoření tohoto kanálu najdete [v tématu Výuka: Kopírování dat z úložiště objektů Blob do databáze SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

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
* Oddíl **definuje** se používá k určení nastavení za běhu, které jsou předány do skriptu podregistru jako hodnoty konfigurace Hive (např `${hiveconf:inputtable}`. `${hiveconf:partitionedtable}`).

Viz [aktivity transformace dat](#data-transformation-activities) v tomto článku pro ukázky JSON, které definují transformační aktivity v kanálu.

Úplný návod k vytvoření tohoto kanálu najdete [v tématu Výuka: Vytvoření prvního kanálu pro zpracování dat pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).

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

Následující tabulka popisuje vlastnosti v definici aktivity JSON:

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| jméno | Název propojené služby. | Ano |
| vlastnosti - typ | Typ propojené služby. Například: Azure Storage, Azure SQL Database. |
| typeProperties | Oddíl typeProperties obsahuje prvky, které se liší pro každé úložiště dat nebo výpočetní prostředí. Viz část úložiště dat pro všechny propojené služby úložiště dat a [výpočetní prostředí](#compute-environments) pro všechny služby propojené s výpočetními prostředky. |

## <a name="dataset"></a>Datová sada
Datová sada ve Službě Azure Data Factory je definována takto:

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

Následující tabulka popisuje vlastnosti ve výše uvedeném json:

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| jméno | Název datové sady. Viz [Azure Data Factory – pravidla pojmenování](data-factory-naming-rules.md) pro pravidla pojmenování. |Ano |Není k dispozici |
| type | Typ datové sady. Zadejte jeden z typů podporovaných Azure Data Factory (například: AzureBlob, AzureSqlTable). Viz data [stores](#data-stores) část pro všechny úložiště dat a typy datových sad podporovaných Data Factory. |
| – struktura | Schéma datové sady. Obsahuje sloupce, jejich typy atd. | Ne |Není k dispozici |
| typeProperties | Vlastnosti odpovídající vybranému typu. Podporované typy a jejich vlastnosti naleznete v části [DATA STORES.](#data-stores) |Ano |Není k dispozici |
| external | Logický příznak k určení, zda je datová sada explicitně vytvořena kanálem datové továrny nebo ne. |Ne |false (nepravda) |
| dostupnosti | Definuje okno zpracování nebo model krájení pro výrobu datové sady. Podrobnosti o modelu krájení datové sady naleznete v článku [Plánování a spuštění.](data-factory-scheduling-and-execution.md) |Ano |Není k dispozici |
| policy |Definuje kritéria nebo podmínku, kterou musí řezy datové sady splňovat. <br/><br/>Podrobnosti naleznete v části Zásady datové sady. |Ne |Není k dispozici |

Každý sloupec v části **struktury** obsahuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název sloupce. |Ano |
| type |Datový typ sloupce.  |Ne |
| jazyková verze |Jazyková verze založená na rozhraní .NET, `Datetime` která `Datetimeoffset`má být použita při zadání typu a je typu .NET nebo . Výchozí je `en-us`. |Ne |
| formát |Formátovací řetězec, který se použije při `Datetime` zadání `Datetimeoffset`typu a je typu .NET nebo . |Ne |

V následujícím příkladu má datová `slicetimestamp` `projectname`sada `pageviews` tři sloupce , a jsou typu: String, String a Decimal.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Následující tabulka popisuje vlastnosti, které můžete použít v části **dostupnosti:**

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| frequency |Určuje časovou jednotku pro výrobu řezů datové sady.<br/><br/><b>Podporovaná frekvence</b>: Minuta, Hodina, Den, Týden, Měsíc |Ano |Není k dispozici |
| interval |Určuje násobitel pro frekvenci.<br/><br/>"Interval frekvence x" určuje, jak často se řez vyrábí.<br/><br/>Pokud potřebujete, aby byla datová sada rozdělena na základě hodin, nastavte <b>frekvenci</b> na <b>hodinu</b>a <b>interval</b> na <b>1</b>.<br/><br/><b>Poznámka:</b>Pokud zadáte Frekvenci jako minutu, doporučujeme nastavit interval na nejméně 15 |Ano |Není k dispozici |
|  – styl |Určuje, zda má být řez vytvořen na začátku/na konci intervalu.<ul><li>Začátekintervalu</li><li>EndOfInterval</li></ul><br/><br/>Pokud frekvence je nastavena na Měsíc a styl je nastaven na EndOfInterval, řez se vyrábí na poslední den v měsíci. Pokud je styl nastaven na StartOfInterval, řez se vytváří první den v měsíci.<br/><br/>Pokud frekvence je nastavena na Den a styl je nastaven a EndOfInterval, řez se vyrábí v poslední hodině dne.<br/><br/>Pokud Frekvence je nastavena na hodinu a styl je nastaven na EndOfInterval, řez se vytváří na konci hodiny. Například pro řez pro 1 PM – 14 hodin období, řez se vyrábí v 14:00. |Ne |EndOfInterval |
| anchorDateTime |Definuje absolutní časovou polohu používanou plánovačem k výpočtu hranic datových sad. <br/><br/><b>Poznámka:</b>Pokud AnchorDateTime má data části, které jsou podrobnější než frekvence pak podrobnější části jsou ignorovány. <br/><br/>Například pokud <b>interval</b> je <b>hodinová</b> (frekvence: hodina a interval: 1) a <b>AnchorDateTime</b> obsahuje <b>minuty a sekundy</b> pak <b>minuty a sekundy</b> části AnchorDateTime jsou ignorovány. |Ne |01/01/0001 |
| posun |Časový posun, o které jsou posunuty počáteční a koncové všechny řezy datové sady. <br/><br/><b>Poznámka:</b>Pokud jsou zadány anchorDateTime i offset, výsledkem je kombinovaný posun. |Ne |Není k dispozici |

Následující část dostupnosti určuje, že výstupní datová sada je buď vyráběna každou hodinu (nebo) vstupní datová sada je k dispozici každou hodinu:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Oddíl **zásad** v definici datové sady definuje kritéria nebo podmínku, kterou musí řezy datové sady splňovat.

| Název zásady | Popis | Použito | Požaduje se | Výchozí |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Ověří, že data v **objektu blob Azure** splňuje požadavky na minimální velikost (v megabajtech). |Azure Blob |Ne |Není k dispozici |
| minimumRows |Ověří, zda data v **databázi Azure SQL** nebo **v tabulce Azure** obsahují minimální počet řádků. |<ul><li>Azure SQL Database</li><li>Tabulka Azure</li></ul> |Ne |Není k dispozici |

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

Pokud se datová sada nevyrábí službou Azure Data Factory, měla by být označena jako **externí**. Toto nastavení obecně platí pro vstupy první aktivity v kanálu, pokud aktivita nebo řetězení potrubí se používá.

| Name (Název) | Popis | Požaduje se | Výchozí hodnota |
| --- | --- | --- | --- |
| dataDelay |Čas zpoždění kontroly dostupnosti externích dat pro daný řez. Například pokud data jsou k dispozici každou hodinu, kontrola zobrazit externí data je k dispozici a odpovídající řez je Ready může být zpožděn pomocí dataDelay.<br/><br/>Platí pouze pro tuto dobu.  Například pokud je 1:00 PM právě teď a tato hodnota je 10 minut, ověření začíná 1:10 PM.<br/><br/>Toto nastavení nemá vliv na řezy v minulosti (řezy s časem ukončení řezu + dataDelay < Now) jsou zpracovány bez zpoždění.<br/><br/>Čas větší než 23:59 hodin `day.hours:minutes:seconds` je třeba zadat pomocí formátu. Chcete-li například zadat 24 hodin, nepoužívejte 24:00:00; místo toho použijte 1.00:00:00. Pokud používáte 24:00:00, je považován za 24 dní (24:00:00). Pro 1 den a 4 hodiny zadejte 1:04:00:00. |Ne |0 |
| retryInterval |Čekací doba mezi selháním a dalším pokusem o opakování. Pokud se pokus nezdaří, další pokus je po retryInterval. <br/><br/>Pokud je to 1:00 pm právě teď, začneme první pokus. Pokud je doba trvání dokončení první kontroly ověření 1 minuta a operace se nezdařila, další opakování je v 1:00 + 1 min (doba trvání) + 1 min (interval opakování) = 1:02 PM. <br/><br/>U řezů v minulosti nedošlo k žádnému zpoždění. Opakování se stane okamžitě. |Ne |00:01:00 (1 min) |
| retryTimeout |Časový čas pro každý pokus o opakování.<br/><br/>Pokud je tato vlastnost nastavena na 10 minut, ověření musí být dokončena do 10 minut. Pokud trvá déle než 10 minut k provedení ověření, opakování časový mzda.<br/><br/>Pokud všechny pokusy o ověření časový mzda out, řez je označen jako TimedOut. |Ne |00:10:00 (10 minut) |
| maximumOpakovat |Počet, kolikrát zkontrolovat dostupnost externích dat. Povolená maximální hodnota je 10. |Ne |3 |


## <a name="data-stores"></a>ÚLOŽIŠTĚ DAT
Oddíl [Propojená služba](#linked-service) obsahuje popisy prvků JSON, které jsou společné pro všechny typy propojených služeb. Tato část obsahuje podrobnosti o prvcích JSON, které jsou specifické pro každé úložiště dat.

Část [Dataset](#dataset) poskytla popisy prvků JSON, které jsou společné pro všechny typy datových sad. Tato část obsahuje podrobnosti o prvcích JSON, které jsou specifické pro každé úložiště dat.

Část [Aktivita](#activity) obsahuje popisy prvků JSON, které jsou společné pro všechny typy aktivit. Tato část obsahuje podrobnosti o json prvky, které jsou specifické pro každé úložiště dat při použití jako zdroj/jímka v aktivitě kopírování.

Kliknutím na odkaz pro obchod, který vás zajímá, zobrazíte schémata JSON pro propojenou službu, datovou sadu a zdroj/jímku pro aktivitu kopírování.

| Kategorie | Úložiště dat
|:--- |:--- |
| **Azure** |[Úložiště objektů blob Azure](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure SQL Data Warehouse](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
| &nbsp; |[Azure Table storage](#azure-table-storage) |
| **Databáze** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[Mysql](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **Soubor** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Systém souborů](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Ostatní** |[Protokol HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Webová tabulka |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Propojená služba
Propojené služby jsou dva typy propojených služeb: propojené služby Azure Storage a propojené služby Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Chcete-li propojit účet úložiště Azure s totoovou továrnou pomocí **klíče účtu**, vytvořte propojenou službu Azure Storage. Chcete-li definovat propojenou službu Azure Storage, nastavte **typ** propojené služby na **AzureStorage**. Potom můžete v části **typeProperties** zadat následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k úložišti Azure pro vlastnost connectionString. |Ano |

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

#### <a name="azure-storage-sas-linked-service"></a>Propojená služba Azure Storage SAS
Propojená služba Azure Storage SAS umožňuje propojit účet úložiště Azure s totodatovou továrně dat Azure pomocí sdíleného přístupového podpisu (SAS). Poskytuje datové továrny s omezený/časově vázaný přístup ke všem/konkrétní prostředky (objekt blob/kontejner) v úložišti. Pokud chcete propojit svůj účet úložiště Azure s totodatovou továrnou pomocí sdíleného přístupového podpisu, vytvořte propojenou službu Azure Storage SAS. Chcete-li definovat propojenou službu Azure Storage SAS, nastavte **typ** propojené služby na **AzureStorageSas**. Potom můžete v části **typeProperties** zadat následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| sasUri |Zadejte identifikátor URI sdíleného přístupového podpisu pro prostředky úložiště Azure, jako je objekt blob, kontejner nebo tabulka. |Ano |

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

Další informace o těchto propojených službách najdete v článku [konektor úložiště objektů blob Azure.](data-factory-azure-blob-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Objektů blob Azure, nastavte **typ** datové sady na **AzureBlob**. Potom zadejte následující vlastnosti specifické pro objekt Blob Azure v části **typeProperties:**

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Cesta ke kontejneru a složce v úložišti objektů blob. Příklad: myblobcontainer\myblobfolder\ |Ano |
| fileName |Název objektu blob. název_souboru je volitelný a rozlišuje malá a velká písmena.<br/><br/>Pokud zadáte název souboru, aktivita (včetně kopírování) funguje na konkrétním objektu Blob.<br/><br/>Když fileName není zadán, Copy zahrnuje všechny objekty BLOB ve složceCesta pro vstupní datovou sadu.<br/><br/>Pokud není pro výstupní datovou sadu zadán název generovaného souboru, bude název `Data.<Guid>.txt` generovaného souboru v následujícím formátu: (například: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy je volitelná vlastnost. Můžete ji použít k určení dynamické složkyCesta a název souboru pro data časových řad. Například folderPath lze parametrizovat pro každou hodinu dat. |Ne |
| formát | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), Formát [Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), Formát [orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady. |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**. Podporované úrovně jsou: **Optimální** a **nejrychlejší**. Další informace najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

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


Další informace najdete v článku [azure blob konektor.](data-factory-azure-blob-connector.md#dataset-properties)

### <a name="blobsource-in-copy-activity"></a>Zdroj objektu BlobV v aktivitě kopírování
Pokud kopírujete data z úložiště objektů blob Azure, nastavte **zdrojový typ** aktivity kopírování na **Objekt BlobSource**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. |True (výchozí hodnota), False |Ne |

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
Pokud kopírujete data do úložiště objektů blob Azure, nastavte **typ jímky** aktivity kopírování na **BlobSink**a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopírování, pokud je zdrojem Objekt BlobSource nebo FileSystem. |<b>Zachovat hierarchii</b>: zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru ke zdrojové složce je shodná s relativní cestou cílového souboru do cílové složky.<br/><br/><b>Sloučení :</b>všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generovaný název. <br/><br/><b>MergeFiles (výchozí):</b> sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru/objektu blob, sloučený název souboru by byl zadaný název; v opačném případě by byl automaticky generovaný název souboru. |Ne |

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

Další informace najdete v článku [azure blob konektor.](data-factory-azure-blob-connector.md#copy-activity-properties)

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu Azure Data Lake Store, nastavte typ propojené služby do **AzureDataLakeStore**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **AzureDataLakeStore.** | Ano |
| dataLakeStoreUri | Zadejte informace o účtu Azure Data Lake Store. Je v následujícím `https://[accountname].azuredatalakestore.net/webhdfs/v1` formátu: `adl://[accountname].azuredatalakestore.net/`nebo . | Ano |
| subscriptionId | ID předplatného Azure, ke kterému úložiště datových jezer patří. | Vyžadováno pro umyvadlo |
| resourceGroupName | Název skupiny prostředků Azure, do kterého patří úložiště datových jezer. | Vyžadováno pro umyvadlo |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano (pro ověřování instančního objektu) |
| servicePrincipalKey | Zadejte klíč aplikace. | Ano (pro ověřování instančního objektu) |
| Nájemce | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Můžete načíst na jenom myši v pravém horním rohu portálu Azure. | Ano (pro ověřování instančního objektu) |
| autorizace | V **Editoru datové továrny** klikněte na **tlačítko Autorizovat** a zadejte pověření, které této vlastnosti přiřadí automaticky vygenerovanou autorizační adresu URL. | Ano (pro ověřování pověření uživatele)|
| Sessionid | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečné a lze je použít pouze jednou. Toto nastavení se automaticky generuje při použití Editoru datových tocích. | Ano (pro ověřování pověření uživatele) |

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

Další informace najdete v článku [konektor u úložiště dat Azure.](data-factory-azure-datalake-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Azure Data Lake Store, nastavte **typ** datové sady na **AzureDataLakeStore**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| folderPath |Cesta ke kontejneru a složce v úložišti Azure Data Lake. |Ano |
| fileName |Název souboru v úložišti Azure Data Lake. název_souboru je volitelný a rozlišuje malá a velká písmena. <br/><br/>Pokud zadáte název souboru, aktivita (včetně kopírování) funguje na konkrétním souboru.<br/><br/>Pokud není zadán název_souboru, funkce Kopírovat zahrnuje všechny soubory ve složceCesta pro vstupní datovou sadu.<br/><br/>Pokud není pro výstupní datovou sadu zadán název generovaného souboru, bude název `Data.<Guid>.txt` generovaného souboru v následujícím formátu: (například: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy je volitelná vlastnost. Můžete ji použít k určení dynamické složkyCesta a název souboru pro data časových řad. Například folderPath lze parametrizovat pro každou hodinu dat. |Ne |
| formát | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), Formát [Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), Formát [orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady. |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**. Podporované úrovně jsou: **Optimální** a **nejrychlejší**. Další informace najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

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

Další informace najdete v článku [konektor u úložiště dat Azure.](data-factory-azure-datalake-connector.md#dataset-properties)

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Zdroj úložiště datových jezer Azure v aktivitě kopírování
Pokud kopírujete data z úložiště Datových jezer Azure, nastavte **zdrojový typ** aktivity kopírování na **AzureDataLakeStoreSource**a ve **zdrojové** části zadejte následující vlastnosti:

**AzureDataLakeStoreSource** podporuje následující vlastnosti **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. |True (výchozí hodnota), False |Ne |

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

Další informace najdete v článku [konektor u úložiště dat Azure.](data-factory-azure-datalake-connector.md#copy-activity-properties)

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Jímka úložiště datového jezera Azure v aktivitě kopírování
Pokud kopírujete data do úložiště datových jezer Azure, nastavte **typ jímky** aktivity kopírování na **AzureDataLakeStoreSink**a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Určuje chování kopírování. |<b>Zachovat hierarchii</b>: zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru ke zdrojové složce je shodná s relativní cestou cílového souboru do cílové složky.<br/><br/><b>Sloučení :</b>všechny soubory ze zdrojové složky jsou vytvořeny v první úrovni cílové složky. Cílové soubory jsou vytvořeny s automaticky generovaným názvem.<br/><br/><b>MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru/objektu blob, sloučený název souboru by byl zadaný název; v opačném případě by byl automaticky generovaný název souboru. |Ne |

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

Další informace najdete v článku [konektor u úložiště dat Azure.](data-factory-azure-datalake-connector.md#copy-activity-properties)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu Azure Cosmos DB, nastavte **typ** propojené služby na **DocumentDb**a v části **typeProperties** určete následující vlastnosti:

| **Vlastnost** | **Popis** | **Požadováno** |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k databázi Azure Cosmos DB. |Ano |

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
Další informace najdete v článku [konektoru Azure Cosmos DB.](data-factory-azure-documentdb-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Azure Cosmos DB, nastavte **typ** datové sady na **DocumentDbCollection**a v části **typeProperties** zadejte následující vlastnosti:

| **Vlastnost** | **Popis** | **Požadováno** |
| --- | --- | --- |
| název kolekce |Název kolekce Azure Cosmos DB. |Ano |

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
Další informace najdete v článku [konektoru Azure Cosmos DB.](data-factory-azure-documentdb-connector.md#dataset-properties)

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Zdroj kolekce Azure Cosmos DB v aktivitě kopírování
Pokud kopírujete data z databáze Azure Cosmos DB, nastavte **zdrojový typ** aktivity kopírování na **DocumentDbCollectionSource**a ve **zdrojové** části určete následující vlastnosti:


| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Požadováno** |
| --- | --- | --- | --- |
| query |Zadejte dotaz pro čtení dat. |Řetězec dotazu podporovaný službou Azure Cosmos DB. <br/><br/>Příklad: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Ne <br/><br/>Pokud není zadán, příkaz SQL, který je proveden:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Zvláštní znak označující, že dokument je vnořený |Libovolný znak. <br/><br/>Azure Cosmos DB je úložiště NoSQL pro dokumenty JSON, kde jsou povoleny vnořené struktury. Azure Data Factory umožňuje uživateli dedikovat hierarchii pomocí vnořeníSeparator, což je "." ve výše uvedených příkladech. S oddělovačem bude aktivita kopírování generovat objekt "Name" se třemi podřízenými prvky First, Middle a Last podle "Name.First", "Name.Middle" a "Name.Last" v definici tabulky. |Ne |

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

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Jímka kolekce DB Azure Cosmos v aktivitě kopírování
Pokud kopírujete data do azure cosmos db, nastavte **typ jímky** aktivity kopírování na **DocumentDbCollectionSink**a zadejte následující vlastnosti v části **jímky:**

| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Požadováno** |
| --- | --- | --- | --- |
| nestingSeparator |Speciální znak ve název zdrojového sloupce označující, že je potřeba vnořený dokument. <br/><br/>Například výše: `Name.First` ve výstupní tabulce vytvoří následující strukturu JSON v dokumentu Cosmos DB:<br/><br/>"Jméno": {<br/>    "První": "John"<br/>}, |Znak, který se používá k oddělení úrovní vnoření.<br/><br/>Výchozí hodnota `.` je (tečka). |Znak, který se používá k oddělení úrovní vnoření. <br/><br/>Výchozí hodnota `.` je (tečka). |
| writeBatchSize |Počet paralelních požadavků na službu Azure Cosmos DB k vytvoření dokumentů.<br/><br/>Výkon můžete doladit při kopírování dat do/z Azure Cosmos DB pomocí této vlastnosti. Můžete očekávat lepší výkon při zvýšení writeBatchSize, protože jsou odesílány další paralelní požadavky na Azure Cosmos DB. Budete však muset vyhnout omezení, které může vyvolat chybovou zprávu: "Míra požadavků je velká".<br/><br/>Omezení se rozhoduje řadou faktorů, včetně velikosti dokumentů, počtu termínů v dokumentech, indexování zásad cílovékolekce atd. Pro operace kopírování můžete použít lepší kolekci (například S3) mít největší propustnost k dispozici (2 500 jednotek požadavku za sekundu). |Integer |Ne (výchozí: 5) |
| writeBatchTimeout |Počkejte čas na dokončení operace před časovým výpadkem. |Timespan<br/><br/> Příklad: "00:30:00" (30 minut). |Ne |

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

Další informace najdete v článku [konektoru Azure Cosmos DB.](data-factory-azure-documentdb-connector.md#copy-activity-properties)

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu Azure SQL Database, nastavte **typ** propojené služby na **AzureSqlDatabase**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci Azure SQL Database pro vlastnost connectionString. |Ano |

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

Další informace najdete v článku [konektoru Azure SQL.](data-factory-azure-sql-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Azure SQL Database, nastavte **typ** datové sady na **AzureSqlTable**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v instanci Azure SQL Database, na kterou odkazuje propojená služba. |Ano |

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
Další informace najdete v článku [konektoru Azure SQL.](data-factory-azure-sql-connector.md#dataset-properties)

### <a name="sql-source-in-copy-activity"></a>Zdroj SQL v aktivitě kopírování
Pokud kopírujete data z databáze Azure SQL, nastavte **zdrojový typ** aktivity kopírování na **SqlSource**a ve **zdrojové** části určete následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Dvojice název/hodnota. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |

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
Další informace najdete v článku [konektoru Azure SQL.](data-factory-azure-sql-connector.md#copy-activity-properties)

### <a name="sql-sink-in-copy-activity"></a>Jímka SQL v aktivitě kopírování
Pokud kopírujete data do databáze Azure SQL, nastavte **typ jímky** aktivity kopírování na **SqlSink**a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte čas pro operaci dávkové vložení k dokončení před časovým výpadkem. |Timespan<br/><br/> Příklad: "00:30:00" (30 minut). |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, který má být proveden tak, aby byla vyčištěna data určitého řezu. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování, který se má vyplnit automaticky generovaným identifikátorem řezu, který se používá k vyčištění dat určitého řezu při opětovném spuštění. |Název sloupce s datovým typem binárního(32). |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury, která upserts (aktualizace/vloží) data do cílové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Dvojice název/hodnota. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název typu tabulky, který má být použit v uložené proceduře. Aktivita kopírování zpřístupňuje data přesunutá v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit zkopírovaná data s existujícími daty. |Název typu tabulky. |Ne |

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

Další informace najdete v článku [konektoru Azure SQL.](data-factory-azure-sql-connector.md#copy-activity-properties)

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu Azure SQL Data Warehouse, nastavte **typ** propojené služby na **AzureSqlDW**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci datového skladu Azure SQL pro vlastnost connectionString. |Ano |



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

Další informace najdete v článku konektor [u konektoru Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu datového skladu Azure SQL, nastavte **typ** datové sady na **AzureSqlDWTable**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v databázi Azure SQL Data Warehouse, na kterou propojená služba odkazuje. |Ano |

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

Další informace najdete v článku konektor [u konektoru Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties)

### <a name="sql-dw-source-in-copy-activity"></a>Zdroj SQL DW v aktivitě kopírování
Pokud kopírujete data z datového skladu Azure SQL, nastavte **zdrojový typ** aktivity kopírování na **SqlDWSource**a ve **zdrojové** části zadejte následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Dvojice název/hodnota. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |

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

Další informace najdete v článku konektor [u konektoru Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

### <a name="sql-dw-sink-in-copy-activity"></a>Jímka SQL DW v aktivitě kopírování
Pokud kopírujete data do datového skladu Azure SQL, nastavte **typ jímky** aktivity kopírování na **SqlDWSink**a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, který má být proveden tak, aby byla vyčištěna data určitého řezu. |Příkaz dotazu. |Ne |
| allowPolyBase |Označuje, zda použít PolyBase (pokud je to možné) namísto bulkinsert mechanismu. <br/><br/> **Použití PolyBase je doporučený způsob, jak načíst data do datového skladu SQL.** |True <br/>False (výchozí) |Ne |
| polyBaseSettings |Skupina vlastností, které lze zadat při **allowPolybase** vlastnost je nastavena na **hodnotu true**. |&nbsp; |Ne |
| rejectValue |Určuje počet nebo procento řádků, které lze odmítnout před selháním dotazu. <br/><br/>Další informace o možnostech odmítnutí základny PolyBase naleznete v části **Argumenty** v tématu [CREATE EXTERNAL TABLE (Transact-SQL).](https://msdn.microsoft.com/library/dn935021.aspx) |0 (výchozí), 1, 2, ... |Ne |
| rejectType |Určuje, zda je možnost rejectValue zadána jako hodnota literálu nebo procento. |Hodnota (výchozí), procento |Ne |
| rejectSampleValue |Určuje počet řádků, které mají být načteny před přepočtem PolyBase procento odmítnutých řádků. |1, 2, ... |Ano, pokud **rejectType** je **procento** |
| useTypeDefault |Určuje, jak zpracovat chybějící hodnoty v oddělených textových souborech, když PolyBase načte data z textového souboru.<br/><br/>Další informace o této vlastnosti naleznete v části Argumenty v [části CREATE EXTERNAL FILE FORMAT (Transact-SQL).](https://msdn.microsoft.com/library/dn935026.aspx) |Pravda, Nepravda (výchozí) |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Počkejte čas pro operaci dávkové vložení k dokončení před časovým výpadkem. |Timespan<br/><br/> Příklad: "00:30:00" (30 minut). |Ne |

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

Další informace najdete v článku konektor [u konektoru Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu Azure Cognitive Search, nastavte **typ** propojené služby na **AzureSearch**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| url | Adresa URL vyhledávací služby. | Ano |
| key | Klíč správce vyhledávací služby. | Ano |

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

Další informace najdete v článku [konektor azure kognitivní vyhledávání.](data-factory-azure-search-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Azure Cognitive Search, nastavte **typ** datové sady na **AzureSearchIndex**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| type | Vlastnost type musí být nastavena na **AzureSearchIndex**.| Ano |
| název_indexu | Název indexu vyhledávání. Data Factory nevytvoří index. Index musí existovat v Azure Cognitive Search. | Ano |

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

Další informace najdete v článku [konektor azure kognitivní vyhledávání.](data-factory-azure-search-connector.md#dataset-properties)

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Jímka indexu kognitivního vyhledávání Azure v aktivitě kopírování
Pokud kopírujete data do indexu vyhledávání, nastavte **typ jímky** aktivity kopírování na **AzureSearchIndexSink**a zadejte následující vlastnosti v části **jímky:**

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| -------- | ----------- | -------------- | -------- |
| Chování zápisu | Určuje, zda má být sloučen nebo nahrazen, pokud dokument již v indexu existuje. | Sloučení (výchozí)<br/>Odeslat| Ne |
| WriteBatchSize | Nahraje data do indexu vyhledávání, když velikost vyrovnávací paměti dosáhne writeBatchSize. | 1 až 1 000. Výchozí hodnota je 1000. | Ne |

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

Další informace najdete v článku [konektor azure kognitivní vyhledávání.](data-factory-azure-search-connector.md#copy-activity-properties)

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Propojená služba
Propojené služby jsou dva typy propojených služeb: propojené služby Azure Storage a propojené služby Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Chcete-li propojit účet úložiště Azure s totoovou továrnou pomocí **klíče účtu**, vytvořte propojenou službu Azure Storage. Chcete-li definovat propojenou službu Azure Storage, nastavte **typ** propojené služby na **AzureStorage**. Potom můžete v části **typeProperties** zadat následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavena na: **AzureStorage.** |Ano |
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
Propojená služba Azure Storage SAS umožňuje propojit účet úložiště Azure s totodatovou továrně dat Azure pomocí sdíleného přístupového podpisu (SAS). Poskytuje datové továrny s omezený/časově vázaný přístup ke všem/konkrétní prostředky (objekt blob/kontejner) v úložišti. Pokud chcete propojit svůj účet úložiště Azure s totodatovou továrnou pomocí sdíleného přístupového podpisu, vytvořte propojenou službu Azure Storage SAS. Chcete-li definovat propojenou službu Azure Storage SAS, nastavte **typ** propojené služby na **AzureStorageSas**. Potom můžete v části **typeProperties** zadat následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavena na: **AzureStorageSas** |Ano |
| sasUri |Zadejte identifikátor URI sdíleného přístupového podpisu pro prostředky úložiště Azure, jako je objekt blob, kontejner nebo tabulka. |Ano |

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

Další informace o těchto propojených službách najdete v článku [konektor azure table storage.](data-factory-azure-table-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Tabulka Azure, nastavte **typ** datové sady na **AzureTable**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v instanci Azure Table Database, na kterou odkazuje propojená služba. |Ano. Pokud je zadán název_tableName bez azureTableSourceQuery, všechny záznamy z tabulky jsou zkopírovány do cíle. Pokud azureTableSourceQuery je také zadán, záznamy z tabulky, která splňuje dotaz jsou zkopírovány do cíle. |

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

Další informace o těchto propojených službách najdete v článku [konektor azure table storage.](data-factory-azure-table-connector.md#dataset-properties)

### <a name="azure-table-source-in-copy-activity"></a>Zdroj tabulky Azure v aktivitě kopírování
Pokud kopírujete data z Azure Table Storage, nastavte **zdrojový typ** aktivity kopírování na **AzureTableSource**a ve **zdrojové** části zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| azureTableSourceQuery |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu tabulky Azure. Viz příklady v další části. |Ne. Pokud je zadán název_tableName bez azureTableSourceQuery, všechny záznamy z tabulky jsou zkopírovány do cíle. Pokud azureTableSourceQuery je také zadán, záznamy z tabulky, která splňuje dotaz jsou zkopírovány do cíle. |
| azureTableSourceIgnoreTableNotFound |Určete, zda neexistuje výjimka tabulky. |TRUE<br/>FALSE |Ne |

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

Další informace o těchto propojených službách najdete v článku [konektor azure table storage.](data-factory-azure-table-connector.md#copy-activity-properties)

### <a name="azure-table-sink-in-copy-activity"></a>Jímka tabulek Azure v aktivitě kopírování
Pokud kopírujete data do azure table storage, nastavte **typ jímky** aktivity kopírování na **AzureTableSink**a zadejte následující vlastnosti v části **jímky:**

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Výchozí hodnota klíče oddílu, kterou může použít jímka. |Hodnota řetězce. |Ne |
| azureTablePartitionKeyName |Zadejte název sloupce, jehož hodnoty se používají jako klíče oddílu. Pokud není zadán, AzureTableDefaultPartitionKeyValue se používá jako klíč oddílu. |Název sloupce. |Ne |
| azureTableRowKeyName |Zadejte název sloupce, jehož hodnoty sloupců se používají jako klíč řádku. Pokud není zadán, použijte identifikátor GUID pro každý řádek. |Název sloupce. |Ne |
| azureTableInsertType |Režim pro vložení dat do tabulky Azure.<br/><br/>Tato vlastnost určuje, zda existující řádky ve výstupní tabulce s odpovídajícími klíči oddílu a řádků mají své hodnoty nahrazeny nebo sloučeny. <br/><br/>Informace o tom, jak tato nastavení (sloučení a nahrazení) fungují, najdete v tématu [Vložení nebo sloučení entit](https://msdn.microsoft.com/library/azure/hh452241.aspx) a Vložení nebo nahrazení témat [entity.](https://msdn.microsoft.com/library/azure/hh452242.aspx) <br/><br> Toto nastavení platí na úrovni řádku, nikoli na úrovni tabulky a ani jedna z možností neodstraní řádky ve výstupní tabulce, které ve vstupu neexistují. |sloučení (výchozí)<br/>Nahradit |Ne |
| writeBatchSize |Vloží data do tabulky Azure při přístupu na zápisBatchSize nebo writeBatchTimeout. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Vloží data do tabulky Azure, když je přístupů writeBatchSize nebo writeBatchTimeout |Timespan<br/><br/>Příklad: "00:20:00" (20 minut) |Ne (Výchozí výchozí hodnota časového limitu klienta úložiště 90 sekund) |

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
Další informace o těchto propojených službách najdete v článku [konektor azure table storage.](data-factory-azure-table-connector.md#copy-activity-properties)

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu Amazon Redshift, nastavte **typ** propojené služby na **AmazonRedshift**a zadejte následující vlastnosti v části **typeProperties:**

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |IP adresa nebo název hostitele serveru Amazon Redshift. |Ano |
| port |Číslo portu TCP, který server Amazon Redshift používá k naslouchání pro připojení klientů. |Ne, výchozí hodnota: 5439 |
| database |Název databáze Amazon Redshift. |Ano |
| uživatelské jméno |Jméno uživatele, který má přístup k databázi. |Ano |
| heslo |Heslo pro uživatelský účet. |Ano |

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

Další informace naleznete v článku konektoru Amazon Redshift.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Amazon Redshift, nastavte **typ** datové sady na **Relační tabulku**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v databázi Amazon Redshift, na kterou odkazuje propojená služba. |Ne (pokud je zadán **dotaz** **RelationalSource)** |


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
Další informace naleznete v článku konektoru Amazon Redshift.

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z aplikace Amazon Redshift, nastavte **zdrojový typ** aktivity kopírování na **Relační zdroj**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ne (pokud je zadán **název tabulky** **datové sady)** |

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
Další informace naleznete v článku konektoru Amazon Redshift.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu IBM DB2, nastavte **typ** propojené služby **na OnPremisesDB2**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru DB2. |Ano |
| database |Název databáze DB2. |Ano |
| Schématu |Název schématu v databázi. Název schématu rozlišuje malá a velká písmena. |Ne |
| authenticationType |Typ ověřování používaný pro připojení k databázi DB2. Možné hodnoty jsou: Anonymní, Základní a Windows. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud používáte základní ověřování nebo ověřování systému Windows. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k místní databázi DB2. |Ano |

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
Další informace naleznete v článku konektoru IBM DB2.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu DB2, nastavte **typ** datové sady na **Relační tabulku**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v instanci databáze DB2, na kterou odkazuje propojená služba. TableName rozlišuje malá a velká písmena. |Ne (pokud je zadán **dotaz** **RelationalSource)**

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

Další informace naleznete v článku konektoru IBM DB2.

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z IBM DB2, nastavte **zdrojový typ** aktivity kopírování na **RelationalSource**a ve **zdrojové** části určete následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Například: `"query": "select * from "MySchema"."MyTable""`. |Ne (pokud je zadán **název tabulky** **datové sady)** |

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
Další informace naleznete v článku konektoru IBM DB2.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat službu propojenou s MySQL, nastavte **typ** propojené služby **na OnPremisesMySql**a zadejte následující vlastnosti v části **typeProperties:**

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru MySQL. |Ano |
| database |Název databáze MySQL. |Ano |
| Schématu |Název schématu v databázi. |Ne |
| authenticationType |Typ ověřování používaný pro připojení k databázi MySQL. Možné hodnoty `Basic`jsou: . |Ano |
| userName |Zadejte uživatelské jméno pro připojení k databázi MySQL. |Ano |
| heslo |Zadejte heslo pro zadaný uživatelský účet. |Ano |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k místní databázi MySQL. |Ano |

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

Další informace naleznete v článku [konektoru MySQL.](data-factory-onprem-mysql-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu MySQL, nastavte **typ** datové sady na **Relační tabulku**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v instanci Databáze MySQL, na kterou odkazuje propojená služba. |Ne (pokud je zadán **dotaz** **RelationalSource)** |

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
Další informace naleznete v článku [konektoru MySQL.](data-factory-onprem-mysql-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z databáze MySQL, nastavte **zdrojový typ** aktivity kopírování na **RelationalSource**a ve **zdrojové** části určete následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ne (pokud je zadán **název tabulky** **datové sady)** |


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

Další informace naleznete v článku [konektoru MySQL.](data-factory-onprem-mysql-connector.md#copy-activity-properties)

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu Oracle, nastavte **typ** propojené služby na **OnPremisesOracle**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type ovladače | Určete, který ovladač se má použít ke kopírování dat z databáze Oracle Database nebo do databáze Oracle. Povolené hodnoty jsou **Microsoft** nebo **ODP** (výchozí). Podrobnosti o ovladači naleznete v části Podporovaná verze a instalace. | Ne |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci Databáze Oracle pro vlastnost connectionString. | Ano |
| název brány | Název brány, která se používá k připojení k místnímu serveru Oracle |Ano |

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

Další informace naleznete v článku [oracle konektor.](data-factory-onprem-oracle-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Oracle, nastavte **typ** datové sady na **OracleTable**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v databázi Oracle Database, na kterou propojená služba odkazuje. |Ne (pokud je zadán **oracleReaderQuery** společnosti **OracleSource)** |

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
Další informace naleznete v článku [oracle konektor.](data-factory-onprem-oracle-connector.md#dataset-properties)

### <a name="oracle-source-in-copy-activity"></a>Zdroj Oracle v aktivitě kopírování
Pokud kopírujete data z databáze Oracle, nastavte **zdrojový typ** aktivity kopírování na **OracleSource**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| oracleReaderQuery |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Příklad: `select * from MyTable` <br/><br/>Pokud není zadán, příkaz SQL, který je proveden:`select * from MyTable` |Ne (pokud je zadán **název tabulky** **datové sady)** |

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

Další informace naleznete v článku [oracle konektor.](data-factory-onprem-oracle-connector.md#copy-activity-properties)

### <a name="oracle-sink-in-copy-activity"></a>Řešení Oracle Sink v aktivitě kopírování
Pokud kopírujete data do databáze Oracle, nastavte **typ jímky** aktivity kopírování na **OracleSink**a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte čas pro operaci dávkové vložení k dokončení před časovým výpadkem. |Timespan<br/><br/> Příklad: 00:30:00 (30 minut). |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize. |Celé číslo (počet řádků) |Ne (výchozí: 100) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, který má být proveden tak, aby byla vyčištěna data určitého řezu. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování, který se má vyplnit automaticky generovaným identifikátorem řezu, který se používá k vyčištění dat určitého řezu při opětovném spuštění. |Název sloupce s datovým typem binárního(32). |Ne |

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
Další informace naleznete v článku [oracle konektor.](data-factory-onprem-oracle-connector.md#copy-activity-properties)

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu PostgreSQL, nastavte **typ** propojené služby **na OnPremisesPostgreSql**a určete následující vlastnosti v části **typeProperties:**

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název postgresql serveru. |Ano |
| database |Název databáze PostgreSQL. |Ano |
| Schématu |Název schématu v databázi. Název schématu rozlišuje malá a velká písmena. |Ne |
| authenticationType |Typ ověřování používaný pro připojení k databázi PostgreSQL. Možné hodnoty jsou: Anonymní, Základní a Windows. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud používáte základní ověřování nebo ověřování systému Windows. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k místní databázi PostgreSQL. |Ano |

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
Další informace naleznete v článku [postgresql konektor.](data-factory-onprem-postgresql-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu PostgreSQL, nastavte **typ** datové sady na **Relační tabulku**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v instanci Databáze PostgreSQL, na kterou odkazuje propojená služba. TableName rozlišuje malá a velká písmena. |Ne (pokud je zadán **dotaz** **RelationalSource)** |

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
Další informace naleznete v článku [postgresql konektor.](data-factory-onprem-postgresql-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z databáze PostgreSQL, nastavte **zdrojový typ** aktivity kopírování na **RelationalSource**a ve **zdrojové** části určete následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Například: "dotaz": "select \"* from\"MySchema . \"MyTable\"". |Ne (pokud je zadán **název tabulky** **datové sady)** |

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

Další informace naleznete v článku [postgresql konektor.](data-factory-onprem-postgresql-connector.md#copy-activity-properties)

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu SAP Business Warehouse (BW), nastavte **typ** propojené služby na **SapBw**a v části **typeProperties** určete následující vlastnosti:

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém se nachází instance SAP BW. | řetězec | Ano
systemNumber | Systémové číslo systému SAP BW. | Dvoumístné desetinné číslo reprezentované jako řetězec. | Ano
clientId | ID klienta v systému SAP W. | Třímístné desetinné číslo reprezentované jako řetězec. | Ano
uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP | řetězec | Ano
heslo | Heslo pro tohoto uživatele. | řetězec | Ano
název brány | Název brány, kterou by měla služba Data Factory použít k připojení k místní instanci SAP BW. | řetězec | Ano
šifrované pověření | Šifrovaný řetězec pověření. | řetězec | Ne

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

Další informace naleznete v článku [konektoru SAP Business Warehouse.](data-factory-sap-business-warehouse-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu SAP BW, nastavte **typ** datové sady na **Relační tabulka**. Pro datovou sadu SAP BW typu **RelationalTable**nejsou podporovány žádné vlastnosti specifické pro daný typ .

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
Další informace naleznete v článku [konektoru SAP Business Warehouse.](data-factory-sap-business-warehouse-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z obchodního skladu SAP, nastavte **typ zdroje** aktivity kopírování na **RelationalSource**a ve **zdrojové** části určete následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query | Určuje dotaz MDX pro čtení dat z instance SAP BW. | dotaz u MDX. | Ano |

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

Další informace naleznete v článku [konektoru SAP Business Warehouse.](data-factory-sap-business-warehouse-connector.md#copy-activity-properties)

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu SAP HANA, nastavte **typ** propojené služby na **SapHana**a určete následující vlastnosti v části **typeProperties:**

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém je umístěna instance SAP HANA. Pokud server používá vlastní port, `server:port`zadejte . | řetězec | Ano
authenticationType | Typ ověřování. | . "Základní" nebo "Windows" | Ano
uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP | řetězec | Ano
heslo | Heslo pro tohoto uživatele. | řetězec | Ano
název brány | Název brány, kterou by měla služba Data Factory použít k připojení k místní instanci SAP HANA. | řetězec | Ano
šifrované pověření | Šifrovaný řetězec pověření. | řetězec | Ne

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
Další informace naleznete v článku [konektoru SAP HANA.](data-factory-sap-hana-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu SAP HANA, nastavte **typ** datové sady na **Relační tabulka**. Pro datovou sadu SAP HANA typu **RelationalTable**nejsou podporovány žádné vlastnosti specifické pro daný typ .

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
Další informace naleznete v článku [konektoru SAP HANA.](data-factory-sap-hana-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z úložiště dat SAP HANA, nastavte **zdrojový typ** aktivity kopírování na **RelationalSource**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query | Určuje dotaz SQL pro čtení dat z instance SAP HANA. | Dotaz SQL. | Ano |


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

Další informace naleznete v článku [konektoru SAP HANA.](data-factory-sap-hana-connector.md#copy-activity-properties)


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Propojená služba
Vytvoříte propojenou službu typu **OnPremisesSqlServer** pro propojení místní databáze serveru SQL Server s totožnou sítí dat. Následující tabulka obsahuje popis prvků JSON specifických pro místní propojenou službu SQL Server.

Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu SQL Server.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu by měla být nastavena **na: OnPremisesSqlServer**. |Ano |
| připojovací řetězec |Zadejte informace connectionString potřebné pro připojení k místní databázi serveru SQL Server pomocí ověřování SQL nebo ověřování systému Windows. |Ano |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k místní databázi serveru SQL Server. |Ano |
| uživatelské jméno |Pokud používáte ověřování systému Windows, zadejte uživatelské jméno. Příklad: **uživatelské\\jméno domény**. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |

Pověření můžete šifrovat pomocí rutiny **New-AzDataFactoryEncryptValue** a použít je v připojovacím řetězci, jak je znázorněno v následujícím příkladu (**Vlastnost EncryptedCredential):**

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

Pokud uživatelské jméno a heslo jsou zadány, gateway používá je zosobnit zadaný uživatelský účet pro připojení k místní databázi SERVERU SQL Server. V opačném případě se brána připojí k serveru SQL Server přímo s kontextem zabezpečení brány (její spouštěcí účet).

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

Další informace naleznete v článku [konektoru serveru SQL Server.](data-factory-sqlserver-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu serveru SQL Server, nastavte **typ** datové sady na **sqlservertable**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v instanci databáze serveru SQL Server, na které odkazuje propojená služba. |Ano |

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

Další informace naleznete v článku [konektoru serveru SQL Server.](data-factory-sqlserver-connector.md#dataset-properties)

### <a name="sql-source-in-copy-activity"></a>Zdroj SQL v aktivitě kopírování
Pokud kopírujete data z databáze serveru SQL Server, nastavte **zdrojový typ** aktivity kopírování na **SqlSource**a ve **zdrojové** části určete následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Například: `select * from MyTable`. Může odkazovat na více tabulek z databáze, na kterou odkazuje vstupní datová sada. Pokud není zadán, příkaz SQL, který je proveden: vyberte z MyTable. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Dvojice název/hodnota. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |

Pokud je pro sqlSource zadán **sqlReaderQuery,** spustí aktivita kopírování tento dotaz proti zdroji databáze serveru SQL Server, aby získala data.

Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (pokud uložená procedura přebírá parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura se používají k vytvoření výběrového dotazu spustit proti databázi serveru SQL Server. Pokud definice datové sady nemá strukturu, jsou z tabulky vybrány všechny sloupce.

> [!NOTE]
> Při použití **sqlReaderStoredProcedureName**, stále je třeba zadat hodnotu **vlastnosti tableName** v datové sadě JSON. V šaku tabulce nejsou provedena žádná ověření.


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

V tomto příkladu **sqlReaderQuery** je určen pro SqlSource. Aktivita kopírování spustí tento dotaz proti zdroji databáze serveru SQL Server, aby získala data. Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (pokud uložená procedura přebírá parametry). SqlReaderQuery může odkazovat na více tabulek v rámci databáze, na které odkazuje vstupní datová sada. Není omezena pouze na tabulku nastavenou jako vlastnost tableName vlastnosti datové sady.

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura se používají k vytvoření výběrového dotazu spustit proti databázi serveru SQL Server. Pokud definice datové sady nemá strukturu, jsou z tabulky vybrány všechny sloupce.

Další informace naleznete v článku [konektoru serveru SQL Server.](data-factory-sqlserver-connector.md#copy-activity-properties)

### <a name="sql-sink-in-copy-activity"></a>Jímka SQL v aktivitě kopírování
Pokud kopírujete data do databáze serveru SQL Server, nastavte **typ jímky** aktivity kopírování do **aplikace SqlSink**a v části **jímky** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte čas pro operaci dávkové vložení k dokončení před časovým výpadkem. |Timespan<br/><br/> Příklad: "00:30:00" (30 minut). |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, který se má provést tak, aby byla vyčištěna data určitého řezu. Další informace naleznete v části opakovatelnost. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování, který se má vyplnit automaticky generovaným identifikátorem řezu, který se používá k vyčištění dat určitého řezu při opětovném spuštění. Další informace naleznete v části opakovatelnost. |Název sloupce s datovým typem binárního(32). |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury, která upserts (aktualizace/vloží) data do cílové tabulky. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Dvojice název/hodnota. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název typu tabulky, který má být použit v uložené proceduře. Aktivita kopírování zpřístupňuje data přesunutá v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit zkopírovaná data s existujícími daty. |Název typu tabulky. |Ne |

#### <a name="example"></a>Příklad
Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití těchto vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **objekt BlobSource** a typ **jímky** je nastaven na **sqlsink**.

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

Další informace naleznete v článku [konektoru serveru SQL Server.](data-factory-sqlserver-connector.md#copy-activity-properties)

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu Sybase, nastavte **typ** propojené **služby na OnPremisesSybase**a určete následující vlastnosti v části **typeProperties:**

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru Sybase. |Ano |
| database |Název databáze Sybase. |Ano |
| Schématu |Název schématu v databázi. |Ne |
| authenticationType |Typ ověřování používaný pro připojení k databázi Sybase. Možné hodnoty jsou: Anonymní, Základní a Windows. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud používáte základní ověřování nebo ověřování systému Windows. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k místní databázi Sybase. |Ano |

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

Další informace naleznete v článku [konektoru Sybase.](data-factory-onprem-sybase-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Sybase, nastavte **typ** datové sady na **Relační tabulku**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v instanci Databáze Sybase, na kterou odkazuje propojená služba. |Ne (pokud je zadán **dotaz** **RelationalSource)** |

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

Další informace naleznete v článku [konektoru Sybase.](data-factory-onprem-sybase-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z databáze Sybase, nastavte **zdrojový typ** aktivity kopírování na **RelationalSource**a ve **zdrojové** části určete následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ne (pokud je zadán **název tabulky** **datové sady)** |

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

Další informace naleznete v článku [konektoru Sybase.](data-factory-onprem-sybase-connector.md#copy-activity-properties)

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu Teradata, nastavte **typ** propojené služby **na OnPremisesTeradata**a určete následující vlastnosti v části **typeProperties:**

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |Název serveru Teradata. |Ano |
| authenticationType |Typ ověřování používaný pro připojení k databázi Teradata. Možné hodnoty jsou: Anonymní, Základní a Windows. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud používáte základní ověřování nebo ověřování systému Windows. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| název brány |Název brány, kterou by měla služba Data Factory použít k připojení k místní databázi Teradata. |Ano |

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

Další informace naleznete v článku [konektorteradata.](data-factory-onprem-teradata-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu objektů Blob Teradata, nastavte **typ** datové sady na **Relační tabulka**. V současné době nejsou podporovány žádné vlastnosti typu pro datovou sadu Teradata.

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

Další informace naleznete v článku [konektorteradata.](data-factory-onprem-teradata-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z databáze Teradata, nastavte **zdrojový typ** aktivity kopírování na **RelationalSource**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ano |

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

Další informace naleznete v článku [konektorteradata.](data-factory-onprem-teradata-connector.md#copy-activity-properties)

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu Cassandra, nastavte **typ** propojené služby **na OnPremisesCassandra**a určete následující vlastnosti v části **typeProperties:**

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| host |Jedna nebo více IP adres nebo názvy hostitelů serverů Cassandra.<br/><br/>Zadejte seznam adres IP nebo názvů hostitelů oddělených čárkami, který se má současně připojit ke všem serverům. |Ano |
| port |Port TCP, který server Cassandra používá k naslouchání klientským připojením. |Ne, výchozí hodnota: 9042 |
| authenticationType |Základní nebo Anonymní |Ano |
| uživatelské jméno |Zadejte uživatelské jméno uživatelského účtu. |Ano, pokud authenticationType je nastavena na základní. |
| heslo |Zadejte heslo pro uživatelský účet. |Ano, pokud authenticationType je nastavena na základní. |
| název brány |Název brány, která se používá pro připojení k místní databázi Cassandra. |Ano |
| šifrované pověření |Pověření zašifrované bránou. |Ne |

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

Další informace naleznete v článku [konektorcassandra.](data-factory-onprem-cassandra-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Cassandra, nastavte **typ** datové sady na **CassandraTable**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| prostor kláves |Název keyspace nebo schéma v databázi Cassandra. |Ano (Pokud **dotaz** pro **CassandraSource** není definován). |
| tableName |Název tabulky v databázi Cassandra. |Ano (Pokud **dotaz** pro **CassandraSource** není definován). |

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

Další informace naleznete v článku [konektorcassandra.](data-factory-onprem-cassandra-connector.md#dataset-properties)

### <a name="cassandra-source-in-copy-activity"></a>Cassandra Zdroj v aktivitě kopírování
Pokud kopírujete data z Cassandry, nastavte **zdrojový typ** aktivity kopírování na **CassandraSource**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Dotaz SQL-92 nebo cql dotaz. Viz [odkaz na CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Při použití dotazu SQL zadejte **název keyspace name.table,** který bude představovat tabulku, na kterou se chcete dotazovat. |Ne (pokud jsou definovány tableName a keyspace na datové sadě). |
| consistencyLevel |Úroveň konzistence určuje, kolik replik musí reagovat na požadavek na čtení před vrácením dat do klientské aplikace. Cassandra zkontroluje zadaný počet replik pro data ke splnění požadavku na čtení. |JEDNA, DVA, TŘI, KVORum, VŠECHNY, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Podrobnosti [najdete v tématu Konfigurace konzistence dat.](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) |Ne. Výchozí hodnota je ONE. |

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

Další informace naleznete v článku [konektorcassandra.](data-factory-onprem-cassandra-connector.md#copy-activity-properties)

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu MongoDB, nastavte **typ** propojené služby **na OnPremisesMongoDB**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| server |IP adresa nebo název hostitele serveru MongoDB. |Ano |
| port |Tcp port, který server MongoDB používá k naslouchání pro připojení klientů. |Volitelná, výchozí hodnota: 27017 |
| authenticationType |Základní, nebo Anonymní. |Ano |
| uživatelské jméno |Uživatelský účet pro přístup k MongoDB. |Ano (pokud se používá základní ověřování). |
| heslo |Heslo pro tohoto uživatele. |Ano (pokud se používá základní ověřování). |
| authSource |Název databáze MongoDB, kterou chcete použít ke kontrole přihlašovacích údajů pro ověřování. |Volitelné (pokud se používá základní ověřování). výchozí: Používá účet správce a databázi určenou pomocí vlastnosti databaseName. |
| Databasename |Název databáze MongoDB, ke které chcete získat přístup. |Ano |
| název brány |Název brány, která přistupuje k úložišti dat. |Ano |
| šifrované pověření |Pověření zašifrované bránou. |Nepovinné |

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

Další informace naleznete v [článku mongoDB konektor](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu MongoDB, nastavte **typ** datové sady na **MongoDbCollection**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| název kolekce |Název kolekce v databázi MongoDB. |Ano |

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

Další informace naleznete v [článku mongoDB konektor](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>Zdroj MongoDB v aktivitě kopírování
Pokud kopírujete data z MongoDB, nastavte **zdrojový typ** aktivity kopírování na **MongoDbSource**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL-92. Například: `select * from MyTable`. |Ne (pokud je zadán **název kolekce** **datové sady)** |

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

Další informace naleznete v [článku mongoDB konektor](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu Amazon S3, nastavte **typ** propojené služby na **AwsAccessKey**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| accessKeyID |ID tajného přístupového klíče. |řetězec |Ano |
| tajný klíč AccessKey |Samotný tajný přístupový klíč. |Šifrovaný tajný řetězec |Ano |

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

Další informace naleznete v [článku konektoru Amazon S3](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Amazon S3, nastavte **typ** datové sady na **AmazonS3**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| název_bloku |Název bloku S3. |Řetězec |Ano |
| key |Klíč objektu S3. |Řetězec |Ne |
| Předponu |Předpona pro klíč objektu S3. Objekty, jejichž klávesy začínají touto předponou, jsou vybrány. Platí pouze v případě, že klíč je prázdný. |Řetězec |Ne |
| version |Verze Objektu S3, pokud je povolena správa verzí S3. |Řetězec |Ne |
| formát | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), Formát [Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), Formát [orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady. |Ne | |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**. Podporované úrovně jsou: **Optimální** a **nejrychlejší**. Další informace najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne | |


> [!NOTE]
> bucketName + klíč určuje umístění ObjektU S3, kde je objekt kořenového kontejneru pro objekty S3 a klíč je úplná cesta k objektu S3.

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
#### <a name="example-sample-data-set-with-version"></a>Příklad: Ukázková sada dat (s verzí)

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
V ukázce používáme pevné hodnoty pro vlastnosti klíče a bucketName v datové sadě Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Data Factory můžete vypočítat klíč a bucketName dynamicky za běhu pomocí systémových proměnných, jako je Například SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Totéž můžete udělat pro vlastnost předpony datové sady Amazon S3. Seznam podporovaných funkcí a proměnných naleznete v [tématu Funkce datové továrny a systémové proměnné.](data-factory-functions-variables.md)

Další informace naleznete v [článku konektoru Amazon S3](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete data z Amazons3, nastavte **zdrojový typ** aktivity kopírování na **FileSystemSource**a ve **zdrojové** části určete následující vlastnosti:


| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Určuje, zda se mají v adresáři znovu zobrazit seznam objektů S3. |pravda/nepravda |Ne |


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

Další informace naleznete v [článku konektoru Amazon S3](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Systém souborů


### <a name="linked-service"></a>Propojená služba
Místní souborový systém můžete propojit s toovou továrně dat Azure s **propojenou službou Místní souborový server.** Následující tabulka obsahuje popisy prvků JSON, které jsou specifické pro propojenou službu místního souborového serveru.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Ujistěte se, že vlastnost type je nastavena **na OnPremisesFileServer**. |Ano |
| host |Určuje kořenovou cestu složky, kterou chcete zkopírovat. Pro speciální znaky v řetězci použijte řídicí znak \ ' . Viz Ukázka propojené služby a definice datových sad pro příklady. |Ano |
| Userid |Zadejte ID uživatele, který má přístup k serveru. |Ne (pokud zvolíte encryptedCredential) |
| heslo |Zadejte heslo pro uživatele (userid). |Ne (pokud zvolíte encryptedCredential |
| šifrované pověření |Zadejte šifrovaná pověření, která můžete získat spuštěním rutiny New-AzDataFactoryEncryptValue. |Ne (pokud se rozhodnete zadat userid a heslo ve formátu prostého textu) |
| název brány |Určuje název brány, kterou by měla data factory použít pro připojení k místnímu souborovému serveru. |Ano |

#### <a name="sample-folder-path-definitions"></a>Ukázkové definice cest složek

| Scénář | Hostitel v definici propojené služby | folderPath v definici datové sady |
| --- | --- | --- |
| Místní složka v počítači brány pro správu dat: <br/><br/>Příklady: D:\\ \* nebo D:\folder\subfolder\\* |D:\\ \\ (pro bránu pro správu dat 2.0 a novější verze) <br/><br/> localhost (pro starší verze než Brána pro správu dat 2.0) |. nebo\\\\podsložkou složky (pro bránu Pro správu dat 2.0 a novější verze) \\ \\ <br/><br/>D:\\ \\ nebo\\\\D: podsložka (pro\\\\verzi brány pod 2.0) |
| Vzdálená sdílená složka: <br/><br/>Příklady: \\ \\podsložka\\ \* \\ \\myserver\\\\\\share\\nebo myserver share folder\\* |\\\\\\\\myserver\\\\sdílet |. nebo podsložka složky\\\\ \\ \\ |


#### <a name="example-using-username-and-password-in-plain-text"></a>Příklad: Použití uživatelského jména a hesla ve formátu prostého textu

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

#### <a name="example-using-encryptedcredential"></a>Příklad: Použití šifrovaného pověření

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

Další informace naleznete v [článku konektoru systému souborů](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu systému souborů, nastavte **typ** datové sady na **Soubor Share**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Určuje podcestu ke složce. Pro speciální znaky v řetězci použijte řídicí znak \' . Viz Ukázka propojené služby a definice datových sad pro příklady.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** mít cesty ke složkám na základě počátečního a koncového data řezu. |Ano |
| fileName |Zadejte název souboru ve **složceCesta,** pokud chcete, aby tabulka odkazovala na určitý soubor ve složce. Pokud pro tuto vlastnost nezadáte žádnou hodnotu, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud není pro výstupní datovou sadu zadán název souboru file, je název generovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt`(Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Ne |
| filtr souboru |Určete filtr, který se použije k výběru podmnožiny souborů ve složceCesta, nikoli všechny soubory. <br/><br/>Povolené hodnoty `*` jsou: (více `?` znaků) a (jeden znak).<br/><br/>Příklad 1: "fileFilter": "*.log"<br/>Příklad 2: "fileFilter": 2016-1-?. txt"<br/><br/>Všimněte si, že fileFilter je použitelný pro vstupní datovou sadu FileShare. |Ne |
| partitionedBy |Pomocí funkce partitionedBy můžete určit dynamickou složkuCesta/název_souboru pro data časových řad. Příkladem je folderPath parametrizovaný pro každou hodinu dat. |Ne |
| formát | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), Formát [Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), Formát [orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady. |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**; a podporované úrovně jsou: **Optimální** a **nejrychlejší**. viz [Formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

> [!NOTE]
> FileName a fileFilter nelze použít současně.

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

Další informace naleznete v [článku konektoru systému souborů](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete data ze systému souborů, nastavte **zdrojový typ** aktivity kopírování na **FileSystemSource**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. |Pravda, Nepravda (výchozí) |Ne |

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
Další informace naleznete v [článku konektoru systému souborů](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Jímka systému souborů v aktivitě kopírování
Pokud kopírujete data do systému souborů, nastavte **typ jímky** aktivity kopírování na **FileSystemSink**a v části **jímky** určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopírování, pokud je zdrojem Objekt BlobSource nebo FileSystem. |**Zachovat hierarchii:** Zachová hierarchii souborů v cílové složce. To znamená, že relativní cesta zdrojového souboru ke zdrojové složce je stejná jako relativní cesta cílového souboru do cílové složky.<br/><br/>**Sloučení hierarchie:** Všechny soubory ze zdrojové složky jsou vytvořeny v první úrovni cílové složky. Cílové soubory jsou vytvořeny s automaticky generovaným názvem.<br/><br/>**MergeFiles:** Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru nebo objektblou, je zadanýnázev sloučeného souboru. V opačném případě se jedná o automaticky generovaný název souboru. |Ne |

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

Další informace naleznete v [článku konektoru systému souborů](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu FTP, nastavte **typ** propojené služby na **ftpServer**a určete následující vlastnosti v části **typeProperties:**

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| host |Název nebo IP adresa serveru FTP |Ano |&nbsp; |
| authenticationType |Zadání typu ověřování |Ano |Základní, Anonymní |
| uživatelské jméno |Uživatel, který má přístup k serveru FTP |Ne |&nbsp; |
| heslo |Heslo pro uživatele (uživatelské jméno) |Ne |&nbsp; |
| šifrované pověření |Šifrovaná pověření pro přístup k serveru FTP |Ne |&nbsp; |
| název brány |Název brány pro správu dat pro připojení k místnímu serveru FTP |Ne |&nbsp; |
| port |Port, na kterém server FTP naslouchá |Ne |21 |
| enableSsl |Určení, zda se má použít FTP přes kanál SSL/TLS |Ne |true |
| enableServerCertificateValidation |Určete, zda se má povolit ověření certifikátu TLS/SSL serveru při použití ftp přes kanál SSL/TLS |Ne |true |

#### <a name="example-using-anonymous-authentication"></a>Příklad: Použití anonymního ověřování

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

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Příklad: Použití uživatelského jména a hesla ve formátu prostého textu pro základní ověřování

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

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Příklad: Použití encryptedCredential pro ověřování a bránu

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

Další informace naleznete v článku [ftp konektor.](data-factory-ftp-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu FTP, nastavte **typ** datové sady na **FileShare**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Dílčí cesta ke složce. Pro speciální znaky v řetězci použijte řídicí znak \ ' . Viz Ukázka propojené služby a definice datových sad pro příklady.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** mít cesty ke složkám na základě počátečního a koncového data řezu. |Ano
| fileName |Zadejte název souboru ve **složceCesta,** pokud chcete, aby tabulka odkazovala na určitý soubor ve složce. Pokud pro tuto vlastnost nezadáte žádnou hodnotu, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud není pro výstupní datovou sadu zadán název souboru file, bude název generovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt`(Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Ne |
| filtr souboru |Určete filtr, který se použije k výběru podmnožiny souborů ve složceCesta, nikoli všechny soubory.<br/><br/>Povolené hodnoty `*` jsou: (více `?` znaků) a (jeden znak).<br/><br/>Příklady 1:`"fileFilter": "*.log"`<br/>Příklad 2:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter je použitelný pro vstupní datovou sadu FileShare. Tato vlastnost není podporována pomocí hdfs. |Ne |
| partitionedBy |partitionedBy lze použít k určení dynamické složkyPath, název souboru pro data časových řad. Například folderPath parametrizovánpro každou hodinu dat. |Ne |
| formát | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), Formát [Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), Formát [orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady. |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**; a podporované úrovně jsou: **Optimální** a **nejrychlejší**. Další informace najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |
| useBinaryTransfer |Určete, zda je nutné použít režim binárního přenosu. True pro binární režim a false ASCII. Výchozí hodnota: True. Tuto vlastnost lze použít pouze v případě, že přidružený typ propojené služby je typu: FtpServer. |Ne |

> [!NOTE]
> název souboru a fileFilter nelze použít současně.

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

Další informace naleznete v článku [ftp konektor.](data-factory-ftp-connector.md#dataset-properties)

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete data ze serveru FTP, nastavte **zdrojový typ** aktivity kopírování na **FileSystemSource**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. |Pravda, Nepravda (výchozí) |Ne |

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

Další informace naleznete v článku [ftp konektor.](data-factory-ftp-connector.md#copy-activity-properties)


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu HDFS, nastavte **typ** propojené služby na **rozhraní HDFS**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavena na: **Hdfs** |Ano |
| URL |Adresa URL systému HDFS |Ano |
| authenticationType |Anonymní, nebo Windows. <br><br> Chcete-li použít **ověřování protokolem Kerberos** pro konektor HDFS, v této části nastavte místní prostředí odpovídajícím způsobem. |Ano |
| userName |Uživatelské jméno pro ověřování systému Windows. |Ano (pro ověřování systému Windows) |
| heslo |Heslo pro ověřování systému Windows. |Ano (pro ověřování systému Windows) |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k hdfs. |Ano |
| šifrované pověření |[New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) výstup přístupového pověření. |Ne |

#### <a name="example-using-anonymous-authentication"></a>Příklad: Použití anonymního ověřování

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

#### <a name="example-using-windows-authentication"></a>Příklad: Použití ověřování systému Windows

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

Další informace naleznete v článku konektoru HDFS.

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu HDFS, nastavte **typ** datové sady na **FileShare**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Cesta ke složce. Příklad: `myfolder`<br/><br/>Pro speciální znaky v řetězci použijte řídicí znak \ ' . Příklad: pro složku\podsložka zadejte podsložku složky\\\\a pro složku\\\\d:\samplefolder zadejte d: samplefolder.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** mít cesty ke složkám na základě počátečního a koncového data řezu. |Ano |
| fileName |Zadejte název souboru ve **složceCesta,** pokud chcete, aby tabulka odkazovala na určitý soubor ve složce. Pokud pro tuto vlastnost nezadáte žádnou hodnotu, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud není pro výstupní datovou sadu zadán název souboru file, bude název generovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt`(například: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy lze použít k určení dynamické složkyPath, název souboru pro data časových řad. Příklad: folderPath parametrizovaný pro každou hodinu dat. |Ne |
| formát | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), Formát [Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), Formát [orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady. |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**. Podporované úrovně jsou: **Optimální** a **nejrychlejší**. Další informace najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

> [!NOTE]
> název souboru a fileFilter nelze použít současně.

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

Další informace naleznete v článku konektoru HDFS.

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete data z HDFS, nastavte **zdrojový typ** aktivity kopírování na **FileSystemSource**a ve **zdrojové** části určete následující vlastnosti:

**FileSystemSource** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. |Pravda, Nepravda (výchozí) |Ne |

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

Další informace naleznete v článku konektoru HDFS.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu SFTP, nastavte **typ** propojené služby na **Sftp**a určete následující vlastnosti v části **typeProperties:**

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| host | Název nebo IP adresa serveru SFTP. |Ano |
| port |Port, na kterém naslouchá server SFTP. Výchozí hodnota je: 21 |Ne |
| authenticationType |Zadejte typ ověřování. Povolené hodnoty: **Základní**, **SshPublicKey**. <br><br> Odkazovat na použití základní ověřování a [pomocí SSH veřejného klíče ověřování](#using-ssh-public-key-authentication) oddíly na více vlastností a ukázky JSON v uvedeném pořadí. |Ano |
| skipHostKeyValidation | Určete, zda má být ověření klíče hostitele přeskočte. | Ne. Výchozí hodnota: false |
| hostKeyFingerprint | Určete otisk prstu klíče hostitele. | Ano, `skipHostKeyValidation` pokud je nastavena na false.  |
| název brány |Název brány pro správu dat pro připojení k místnímu serveru SFTP. | Ano, pokud kopírování dat z místního serveru SFTP. |
| šifrované pověření | Šifrovaná pověření pro přístup k serveru SFTP. Automaticky generované při zadání základního ověřování (uživatelské jméno + heslo) nebo ověřování SshPublicKey (uživatelské jméno + cesta soukromého klíče nebo obsah) v průvodci kopírováním nebo v dialogovém okně ClickOnce. | Ne. Použít pouze při kopírování dat z místního serveru SFTP. |

#### <a name="example-using-basic-authentication"></a>Příklad: Použití základního ověřování

Chcete-li použít `authenticationType` základní `Basic`ověřování, nastavte jako , a zadejte následující vlastnosti kromě obecných vlastností konektoru SFTP zavedených v poslední části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Příklad: **Základní ověřování se šifrovanými přihlašovacími údaji**

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

#### <a name="using-ssh-public-key-authentication"></a>**Použití ověřování veřejného klíče SSH:**

Chcete-li použít `authenticationType` základní `SshPublicKey`ověřování, nastavte jako , a zadejte následující vlastnosti kromě obecných vlastností konektoru SFTP zavedených v poslední části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| uživatelské jméno |Uživatel, který má přístup k serveru SFTP |Ano |
| privátnícesta | Zadejte absolutní cestu k souboru soukromého klíče, ke kterému má brána přístup. | Zadejte `privateKeyPath` buď `privateKeyContent`nebo . <br><br> Použít pouze při kopírování dat z místního serveru SFTP. |
| privateKeyContent | Serializovaný řetězec obsahu soukromého klíče. Průvodce kopírováním může číst soubor soukromého klíče a automaticky extrahovat obsah soukromého klíče. Pokud používáte jiný nástroj/sadu SDK, použijte místo toho vlastnost privateKeyPath. | Zadejte `privateKeyPath` buď `privateKeyContent`nebo . |
| Heslo | Zadejte heslo/heslo pro dešifrování soukromého klíče, pokud je soubor klíče chráněn přístupovou frází. | Ano, pokud je soubor soukromého klíče chráněn příchytnou frází. |

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Příklad: **Ověřování SshPublicKey pomocí obsahu soukromého klíče**

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

Další informace naleznete v článku [konektoru SFTP.](data-factory-sftp-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu SFTP, nastavte **typ** datové sady na **FileShare**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Dílčí cesta ke složce. Pro speciální znaky v řetězci použijte řídicí znak \ ' . Viz Ukázka propojené služby a definice datových sad pro příklady.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** mít cesty ke složkám na základě počátečního a koncového data řezu. |Ano |
| fileName |Zadejte název souboru ve **složceCesta,** pokud chcete, aby tabulka odkazovala na určitý soubor ve složce. Pokud pro tuto vlastnost nezadáte žádnou hodnotu, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud není pro výstupní datovou sadu zadán název souboru file, bude název generovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt`(Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Ne |
| filtr souboru |Určete filtr, který se použije k výběru podmnožiny souborů ve složceCesta, nikoli všechny soubory.<br/><br/>Povolené hodnoty `*` jsou: (více `?` znaků) a (jeden znak).<br/><br/>Příklady 1:`"fileFilter": "*.log"`<br/>Příklad 2:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter je použitelný pro vstupní datovou sadu FileShare. Tato vlastnost není podporována pomocí hdfs. |Ne |
| partitionedBy |partitionedBy lze použít k určení dynamické složkyPath, název souboru pro data časových řad. Například folderPath parametrizovánpro každou hodinu dat. |Ne |
| formát | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), Formát [Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), Formát [orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady. |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**. Podporované úrovně jsou: **Optimální** a **nejrychlejší**. Další informace najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |
| useBinaryTransfer |Určete, zda je nutné použít režim binárního přenosu. True pro binární režim a false ASCII. Výchozí hodnota: True. Tuto vlastnost lze použít pouze v případě, že přidružený typ propojené služby je typu: FtpServer. |Ne |

> [!NOTE]
> název souboru a fileFilter nelze použít současně.

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

Další informace naleznete v článku [konektoru SFTP.](data-factory-sftp-connector.md#dataset-properties)

### <a name="file-system-source-in-copy-activity"></a>Zdroj systému souborů v aktivitě kopírování
Pokud kopírujete data ze zdroje SFTP, nastavte **typ zdroje** aktivity kopírování na **FileSystemSource**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. |Pravda, Nepravda (výchozí) |Ne |



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

Další informace naleznete v článku [konektoru SFTP.](data-factory-sftp-connector.md#copy-activity-properties)


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu HTTP, nastavte **typ** propojené služby na **protokol Http**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| url | Základní adresa URL webového serveru | Ano |
| authenticationType | Určuje typ ověřování. Povolené hodnoty jsou: **Anonymní**, **Basic**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Naleznete v částech pod touto tabulkou na více vlastností a JSON ukázky pro tyto typy ověřování v uvedeném pořadí. | Ano |
| enableServerCertificateValidation | Určete, zda má být povolení ověření certifikátu TLS/SSL serveru, pokud je zdrojem webový server HTTPS | Ne, výchozí nastavení je pravda |
| název brány | Název brány pro správu dat pro připojení k místnímu zdroji HTTP. | Ano, pokud kopírování dat z místního zdroje HTTP. |
| šifrované pověření | Šifrovaná pověření pro přístup ke koncovému bodu HTTP. Automaticky generované při konfiguraci ověřovacích informací v průvodci kopírováním nebo v dialogovém okně ClickOnce. | Ne. Použít pouze při kopírování dat z místního serveru HTTP. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Příklad: Použití základního ověřování, ověřování algoritmem Digest nebo Systému Windows
Nastavte `authenticationType` `Basic`jako `Digest`, `Windows`, nebo , a zadejte následující vlastnosti kromě obecných vlastností konektoru HTTP, které jsou uvedeny výše:

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

#### <a name="example-using-clientcertificate-authentication"></a>Příklad: Použití ověřování klientského certifikátu

Chcete-li použít `authenticationType` základní `ClientCertificate`ověřování, nastavte jako , a zadejte následující vlastnosti kromě obecných obecných vlastností konektoru HTTP, které jsou uvedeny výše:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| embeddedCertData | Obsah binárních dat souboru PFX (Base64) kódovaný. | Zadejte `embeddedCertData` buď `certThumbprint`nebo . |
| certThumbprint | Kryptografický otisk certifikátu, který byl nainstalován v úložišti certifikátů počítače brány. Použít pouze při kopírování dat z místního zdroje HTTP. | Zadejte `embeddedCertData` buď `certThumbprint`nebo . |
| heslo | Heslo přidružené k certifikátu. | Ne |

Pokud používáte `certThumbprint` pro ověřování a certifikát je nainstalován v osobním úložišti místního počítače, je třeba udělit oprávnění ke čtení služby brány:

1. Spusťte konzolu MMC (Microsoft Management Console). Přidejte modul snap-in **Certifikáty,** který cílí na **místní počítač**.
2. Rozbalte **certifikáty**, **osobní**a klepněte na **položku Certifikáty**.
3. Klikněte pravým tlačítkem myši na certifikát z osobního úložiště a vyberte **všechny úkoly**->**spravovat soukromé klíče...**
3. Na kartě **Zabezpečení** přidejte uživatelský účet, pod kterým je spuštěna hostitelská služba brány správy dat s přístupem pro čtení k certifikátu.

**Příklad: použití klientského certifikátu:** Tato propojená služba propojuje vaši datovou továrnu s místním webovým serverem HTTP. Používá klientský certifikát, který je nainstalován v počítači s nainstalovanou bránou pro správu dat.

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
Tato propojená služba propojuje vaši datovou továrnu s místním webovým serverem HTTP. Používá soubor klientského certifikátu v počítači s nainstalovanou bránou pro správu dat.

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

Další informace naleznete v článku [konektoru HTTP.](data-factory-http-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu HTTP, nastavte **typ** datové sady na **hodnotu Http**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| Relativeurl | Relativní adresa URL prostředku, který obsahuje data. Pokud cesta není zadána, je použita pouze adresa URL zadaná v definici propojené služby. <br><br> Chcete-li vytvořit dynamickou adresu URL, můžete použít [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md), příklad: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Ne |
| requestMethod | Http metoda. Povolené hodnoty jsou **GET** nebo **POST**. | Ne. Výchozí je `GET`. |
| additionalHeaders | Další hlavičky požadavku HTTP. | Ne |
| requestBody | Tělo pro požadavek HTTP. | Ne |
| formát | Pokud chcete jednoduše **načíst data z koncového bodu HTTP** bez analýzy, přeskočte toto nastavení formátu. <br><br> Chcete-li během kopírování analyzovat obsah odpovědi HTTP, jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Další informace naleznete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), Formát [Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), Formát [orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket.](data-factory-supported-file-and-compression-formats.md#parquet-format) |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**. Podporované úrovně jsou: **Optimální** a **nejrychlejší**. Další informace najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

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
Další informace naleznete v článku [konektoru HTTP.](data-factory-http-connector.md#dataset-properties)

### <a name="http-source-in-copy-activity"></a>Zdroj PROTOKOLU HTTP v aktivitě kopírování
Pokud kopírujete data ze zdroje HTTP, nastavte **typ zdroje** aktivity kopírování na **HttpSource**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| httpRequestTimeout | Časový čas (TimeSpan) pro požadavek HTTP získat odpověď. Je časový čas získat odpověď, nikoli časový čas pro čtení dat odpovědi. | Ne. Výchozí hodnota: 00:01:40 |


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

Další informace naleznete v článku [konektoru HTTP.](data-factory-http-connector.md#copy-activity-properties)

## <a name="odata"></a>OData

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu OData, nastavte **typ** propojené služby na **OData**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| url |Adresa URL služby OData. |Ano |
| authenticationType |Typ ověřování používaný pro připojení ke zdroji OData. <br/><br/> Pro cloudOData možné hodnoty jsou anonymní, základní a OAuth (poznámka: Azure Data Factory aktuálně podporují jenom Azure Active Directory na Základě OAuth). <br/><br/> Pro místní OData možné hodnoty jsou Anonymní, Základní a Windows. |Ano |
| uživatelské jméno |Pokud používáte základní ověřování, zadejte uživatelské jméno. |Ano (pouze pokud používáte základní ověřování) |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ano (pouze pokud používáte základní ověřování) |
| autorizované pověření |Pokud používáte OAuth, klepněte na tlačítko **Autorizovat** v Průvodci kopírováním datové továrny nebo v editoru a zadejte své přihlašovací údaje, pak bude hodnota této vlastnosti automaticky generována. |Ano (pouze pokud používáte ověřování OAuth) |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k místní službě OData. Zadejte pouze v případě, že kopírujete data z místního zdroje OData. |Ne |

#### <a name="example---using-basic-authentication"></a>Příklad - Použití základního ověřování
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

#### <a name="example---using-anonymous-authentication"></a>Příklad - Použití anonymního ověřování

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

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Příklad – Použití ověřování systému Windows při přístupu k místnímu zdroji OData

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

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Příklad - Použití ověřování OAuth přistupující ke zdroji OData v cloudu
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

Další informace naleznete v článku [konektoru OData.](data-factory-odata-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu OData, nastavte **typ** datové sady na **ODataResource**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| cesta |Cesta k prostředku OData |Ne |

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

Další informace naleznete v článku [konektoru OData.](data-factory-odata-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data ze zdroje OData, nastavte **typ zdroje** aktivity kopírování na **RelationalSource**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Příklad | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |"?$select=Název, Popis&$top=5" |Ne |

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

Další informace naleznete v článku [konektoru OData.](data-factory-odata-connector.md#copy-activity-properties)


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu ODBC, nastavte **typ** propojené služby **na OnPremisesOdbc**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| připojovací řetězec |Část připojovacího řetězce bez přístupu k pověření a volitelná šifrovaná pověření. Příklady naleznete v následujících částech. |Ano |
| pověření |Část připojovacího řetězce přístupová pověření zadaná ve formátu hodnoty vlastnosti specifické pro ovladač. Příklad: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Ne |
| authenticationType |Typ ověřování používaný pro připojení k úložišti dat ODBC. Možné hodnoty jsou: Anonymní a Základní. |Ano |
| uživatelské jméno |Pokud používáte základní ověřování, zadejte uživatelské jméno. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k úložišti dat ODBC. |Ano |

#### <a name="example---using-basic-authentication"></a>Příklad - Použití základního ověřování

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
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Příklad - Použití základního ověřování se šifrovanými přihlašovacími údaji
Pověření můžete zašifrovat pomocí rutiny [New-AzDataFactoryEncryptValue.](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue)

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

#### <a name="example-using-anonymous-authentication"></a>Příklad: Použití anonymního ověřování

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

Další informace naleznete v článku [konektoru ODBC.](data-factory-odbc-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu ODBC, nastavte **typ** datové sady na **Relační tabulku**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v úložišti dat ODBC. |Ano |


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

Další informace naleznete v článku [konektoru ODBC.](data-factory-odbc-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data z úložiště dat ODBC, nastavte **zdrojový typ** aktivity kopírování na **RelationalSource**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Například: `select * from MyTable`. |Ano |

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

Další informace naleznete v článku [konektoru ODBC.](data-factory-odbc-connector.md#copy-activity-properties)

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Propojená služba
Chcete-li definovat propojenou službu Salesforce, nastavte **typ** propojené služby na **Salesforce**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| prostředíUrl | Zadejte adresu URL instance Salesforce. <br><br> - Výchozí hodnota\/je "https: /login.salesforce.com". <br> - Chcete-li kopírovat data zhttps://test.salesforce.comizolovaného prostoru, zadejte " ". <br> - Chcete-li kopírovat data z vlastní domény, zadejte například "https://[domain].my.salesforce.com". |Ne |
| uživatelské jméno |Zadejte uživatelské jméno uživatelského účtu. |Ano |
| heslo |Zadejte heslo pro uživatelský účet. |Ano |
| token security |Zadejte token zabezpečení pro uživatelský účet. Pokyny k resetování/získání tokenu zabezpečení najdete v tématu [Získání tokenu zabezpečení.](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) Informace o tokenech zabezpečení obecně naleznete v [tématu Zabezpečení a rozhraní API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ano |

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

Další informace naleznete v článku [konektoru Salesforce.](data-factory-salesforce-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat datovou sadu Salesforce, nastavte **typ** datové sady na **Relační tabulku**a v části **typeProperties** zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v Salesforce. |Ne (pokud je zadán **dotaz** **RelationalSource)** |

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

Další informace naleznete v článku [konektoru Salesforce.](data-factory-salesforce-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relační zdroj v aktivitě kopírování
Pokud kopírujete data ze služby Salesforce, nastavte **typ zdroje** aktivity kopírování na **Relační zdroj**a ve **zdrojové** části určete následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Dotaz SQL-92 nebo dotaz [ovacího jazyka objektu Salesforce (SOQL).](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) Například `select * from MyTable__c`. |Ne (pokud je zadán **název tabulky** **datové sady)** |

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
> "__c" část názvu rozhraní API je potřeba pro všechny vlastní objekt.

Další informace naleznete v článku [konektoru Salesforce.](data-factory-salesforce-connector.md#copy-activity-properties)

## <a name="web-data"></a>Webová data

### <a name="linked-service"></a>Propojená služba
Chcete-li definovat webovou propojenou službu, nastavte **typ** propojené služby na **web**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| URL |Adresa URL webového zdroje |Ano |
| authenticationType |Anonymní. |Ano |


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

Další informace naleznete v článku [konektoru webové tabulky.](data-factory-web-table-connector.md#linked-service-properties)

### <a name="dataset"></a>Datová sada
Chcete-li definovat webovou datovou sadu, nastavte **typ** datové sady na **WebTable**a v části **typeProperties** určete následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |typ datové sady. musí být nastavena na **WebTable** |Ano |
| cesta |Relativní adresa URL prostředku, který obsahuje tabulku. |Ne. Pokud cesta není zadána, je použita pouze adresa URL zadaná v definici propojené služby. |
| index |Index tabulky v prostředku. Postup získání indexu tabulky na stránce HTML najdete v tématu Získání indexu tabulky na stránce HTML. |Ano |

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

Další informace naleznete v článku [konektoru webové tabulky.](data-factory-web-table-connector.md#dataset-properties)

### <a name="web-source-in-copy-activity"></a>Webový zdroj v aktivitě kopírování
Pokud kopírujete data z webové tabulky, nastavte **zdrojový typ** aktivity kopírování na **WebSource**. V současné době, pokud zdroj v aktivitě kopírování je typu **WebSource**, nejsou podporovány žádné další vlastnosti.

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

Další informace naleznete v článku [konektoru webové tabulky.](data-factory-web-table-connector.md#copy-activity-properties)

## <a name="compute-environments"></a>VÝPOČETNÍ PROSTŘEDÍ
V následující tabulce jsou uvedeny výpočetní prostředí podporované factory a transformace aktivity, které můžete spustit na nich. Kliknutím na odkaz pro výpočetní prostředky, které vás zajímají, zobrazíte schémata JSON pro propojenou službu, která je propojují s toto datovou továrnou.

| Výpočetní prostředí | Aktivity |
| --- | --- |
| [Cluster HDInsight na vyžádání](#on-demand-azure-hdinsight-cluster) nebo [vlastní cluster HDInsight](#existing-azure-hdinsight-cluster) |[.NET vlastní aktivita](#net-custom-activity), [Hive aktivita](#hdinsight-hive-activity), [Pig aktivita](#hdinsight-pig-activity), [MapReduce aktivita](#hdinsight-mapreduce-activity), Hadoop streaming aktivita, [Spark aktivita](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[Vlastní aktivita .NET](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Aktivita dávkového spuštění strojového učení](#machine-learning-batch-execution-activity), [aktivita zdrojů aktualizace strojového učení](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[U-SQL Data Lake Analytics](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database), [Azure SQL Data Warehouse](#azure-sql-data-warehouse), SQL [Server](#sql-server-stored-procedure) |[Uložená procedura](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Cluster Azure HDInsight na vyžádání
Služba Azure Data Factory může automaticky vytvořit cluster HDInsight založený na Windows/Linuxu pro zpracování dat. Cluster je vytvořen ve stejné oblasti jako účet úložiště (vlastnost linkedServiceName v JSON) přidružený ke clusteru. V této propojené službě můžete spustit následující transformační aktivity: [vlastní aktivitu .NET](#net-custom-activity), [Aktivitu Hive](#hdinsight-hive-activity), [Aktivitu prasete](#hdinsight-pig-activity), [Aktivitu MapReduce](#hdinsight-mapreduce-activity), Aktivitu streamování Hadoop, [Aktivitu Spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Propojená služba
Následující tabulka obsahuje popis vlastností použitých v definici Azure JSON propojené služby HDInsight na vyžádání.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type by měla být nastavena na **HDInsightOnDemand**. |Ano |
| clusterSize |Počet uzlů pracovníka/dat v clusteru. Cluster HDInsight je vytvořen se 2 hlavníu uzly spolu s počtem pracovních uzlů, které zadáte pro tuto vlastnost. Uzly mají velikost Standard_D3, která má 4 jádra, takže cluster pracovních\*uzlů 4 zabírá 24 jader\*(4 4 = 16 jader pro pracovní uzly plus 2 4 = 8 jader pro hlavní uzly). Podrobnosti o úrovni Standard_D3 najdete v tématu [Vytváření clusterů Hadoop založených na Linuxu v HDInsightu.](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) |Ano |
| timetolive |Povolená doba nečinnosti clusteru HDInsight na vyžádání. Určuje, jak dlouho zůstane cluster HDInsight na vyžádání po dokončení spuštění aktivity naživu, pokud v clusteru nejsou žádné další aktivní úlohy.<br/><br/>Například pokud spuštění aktivity trvá 6 minut a timetolive je nastavena na 5 minut, cluster zůstane naživu po dobu 5 minut po 6 minut zpracování aktivity spustit. Pokud je spuštěna jiná aktivita s oknem 6 minut, je zpracována stejným clusterem.<br/><br/>Vytvoření clusteru HDInsight na vyžádání je nákladná operace (může chvíli trvat), takže toto nastavení použijte podle potřeby ke zlepšení výkonu datové továrny opětovným použitím clusteru HDInsight na vyžádání.<br/><br/>Pokud nastavíte hodnotu timetolive na hodnotu 0, cluster se odstraní, jakmile bude aktivita spuštěna ve zpracování. Na druhou stranu, pokud nastavíte vysokou hodnotu, cluster může zůstat nečinný zbytečně vést k vysokým nákladům. Proto je důležité nastavit příslušnou hodnotu na základě vašich potřeb.<br/><br/>Více kanálů může sdílet stejnou instanci clusteru HDInsight na vyžádání, pokud je správně nastavena hodnota vlastnosti timetolive |Ano |
| version |Verze clusteru HDInsight. Podrobnosti najdete [v tématu podporované verze HDInsight u Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Ne |
| linkedServiceName |Propojená služba Azure Storage, kterou má cluster na vyžádání používat pro ukládání a zpracování dat. <p>V současné době nelze vytvořit cluster HDInsight na vyžádání, který používá Azure Data Lake Store jako úložiště. Pokud chcete uložit výsledná data ze zpracování HDInsight v Úložišti datových jezer Azure, použijte aktivitu kopírování ke kopírování dat z úložiště objektů blob Azure do Úložiště datových jezer Azure.</p>  | Ano |
| additionalLinkedServiceNames |Určuje další účty úložiště pro propojenou službu HDInsight, aby je služba Data Factory mohla zaregistrovat vaším jménem. |Ne |
| osTyp |Typ operačního systému. Povolené hodnoty jsou: Windows (výchozí) a Linux |Ne |
| hcatalogLinkedServiceName |Název propojené služby Azure SQL, která přejděte na databázi HCatalog. Cluster HDInsight na vyžádání se vytvoří pomocí databáze Azure SQL jako metastore. |Ne |

### <a name="json-example"></a>Příklad JSON
Následující JSON definuje linuxovou službu propojenou hdinsight na vyžádání. Služba Data Factory automaticky vytvoří cluster HDInsight **založený na Linuxu** při zpracování řezu dat.

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

Další informace najdete v článku [Výpočetní propojené služby.](data-factory-compute-linked-services.md)

## <a name="existing-azure-hdinsight-cluster"></a>Existující cluster Azure HDInsight
Můžete vytvořit propojenou službu Azure HDInsight a zaregistrovat svůj vlastní cluster HDInsight pomocí Data Factory. V této propojené službě můžete spustit následující aktivity transformace dat: [vlastní aktivita .NET](#net-custom-activity), [Aktivita Hive](#hdinsight-hive-activity), [Aktivita prasete](#hdinsight-pig-activity), [Aktivita MapReduce](#hdinsight-mapreduce-activity), Aktivita streamování Hadoop, [Aktivita Sparku](#hdinsight-spark-activity).

### <a name="linked-service"></a>Propojená služba
Následující tabulka obsahuje popis vlastností použitých v definici Azure JSON propojené služby Azure HDInsight.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type by měla být nastavena na **HDInsight**. |Ano |
| clusterUri |Identifikátor URI clusteru HDInsight. |Ano |
| uživatelské jméno |Zadejte jméno uživatele, který se má použít k připojení k existujícímu clusteru HDInsight. |Ano |
| heslo |Zadejte heslo pro uživatelský účet. |Ano |
| linkedServiceName | Název propojené služby Azure Storage, která odkazuje na úložiště objektů blob Azure používané clusterem HDInsight. <p>V současné době nelze zadat propojené služby Azure Data Lake Store pro tuto službu. K datům v úložišti Azure Data Lake Store můžete přistupovat ze skriptů Hive/Pig, pokud má cluster HDInsight přístup k úložišti Data Lake Store. </p>  |Ano |

U podporovaných verzí clusterů HDInsight najdete [v tématu podporované verze HDInsight](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

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
Můžete vytvořit azure batch propojené služby zaregistrovat dávkový fond virtuálních počítačů (VM) s továrně dat. Vlastní aktivity rozhraní .NET můžete spouštět pomocí Azure Batch nebo Azure HDInsight. V této propojené službě můžete spustit [vlastní aktivitu .NET.](#net-custom-activity)

### <a name="linked-service"></a>Propojená služba
Následující tabulka obsahuje popis vlastností použitých v definici Azure JSON propojené služby Azure Batch.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type by měla být nastavena na **AzureBatch**. |Ano |
| accountName |Název účtu Azure Batch. |Ano |
| Accesskey |Přístupový klíč pro účet Azure Batch. |Ano |
| název_fondu |Název fondu virtuálních počítačů. |Ano |
| linkedServiceName |Název propojené služby Azure Storage přidružené k této propojené službě Azure Batch. Tato propojená služba se používá pro pracovní soubory potřebné ke spuštění aktivity a ukládání protokolů provádění aktivit. |Ano |


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
Vytvoříte propojenou službu Azure Machine Learning pro registraci koncového bodu dávkového bodování Machine Learning s továrně dat. Dvě aktivity transformace dat, které lze spustit v této propojené službě: [Aktivita dávkového spuštění strojového učení](#machine-learning-batch-execution-activity), [Aktivita zdroje aktualizace strojového učení](#machine-learning-update-resource-activity).

### <a name="linked-service"></a>Propojená služba
Následující tabulka obsahuje popis vlastností použitých v definici Azure JSON propojené služby Azure Machine Learning.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| Typ |Vlastnost type by měla být nastavena na: **AzureML**. |Ano |
| mlEndpoint |Adresa URL dávkového bodování. |Ano |
| apiKey |Rozhraní API publikovaného modelu pracovního prostoru. |Ano |

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
Vytvoříte propojenou službu **Azure Data Lake Analytics,** která propojí výpočetní službu Azure Data Lake Analytics s toovou továrně dat Azure, než použijete [aktivitu U-SQL analýzy datového jezera](data-factory-usql-activity.md) v kanálu.

### <a name="linked-service"></a>Propojená služba

Následující tabulka obsahuje popis vlastností použitých v definici JSON propojené služby Azure Data Lake Analytics.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| Typ |Vlastnost type by měla být nastavena na: **AzureDataLakeAnalytics**. |Ano |
| accountName |Název účtu Azure Data Lake Analytics. |Ano |
| dataLakeAnalyticsUri |Identifikátor URI analýzy datového jezera Azure. |Ne |
| autorizace |Autorizační kód se automaticky načte po kliknutí na tlačítko **Autorizovat** v Editoru datové továrny a dokončení přihlášení OAuth. |Ano |
| subscriptionId |ID předplatného Azure |Ne (Pokud není zadáno, použije se odběr datové továrny). |
| resourceGroupName |Název skupiny prostředků Azure |Ne (Pokud není zadáno, použije se skupina prostředků datové továrny). |
| Sessionid |ID relace z autorizační relace OAuth. Každé ID relace je jedinečné a lze je použít pouze jednou. Při použití Editoru datových toto ID se automaticky generuje. |Ano |


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

## <a name="sql-server-stored-procedure"></a>Uložená procedura serveru SQL Server

Vytvoříte propojenou službu sql serveru a použijete ji s [aktivitou uložené procedury](data-factory-stored-proc-activity.md) k vyvolání uložené procedury z kanálu data factory.

### <a name="linked-service"></a>Propojená služba
Vytvoříte propojenou službu typu **OnPremisesSqlServer** pro propojení místní databáze serveru SQL Server s totožnou sítí dat. Následující tabulka obsahuje popis prvků JSON specifických pro místní propojenou službu SQL Server.

Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu SQL Server.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu by měla být nastavena **na: OnPremisesSqlServer**. |Ano |
| připojovací řetězec |Zadejte informace connectionString potřebné pro připojení k místní databázi serveru SQL Server pomocí ověřování SQL nebo ověřování systému Windows. |Ano |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k místní databázi serveru SQL Server. |Ano |
| uživatelské jméno |Pokud používáte ověřování systému Windows, zadejte uživatelské jméno. Příklad: **uživatelské\\jméno domény**. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |

Pověření můžete šifrovat pomocí rutiny **New-AzDataFactoryEncryptValue** a použít je v připojovacím řetězci, jak je znázorněno v následujícím příkladu (**Vlastnost EncryptedCredential):**

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

Pokud uživatelské jméno a heslo jsou zadány, gateway používá je zosobnit zadaný uživatelský účet pro připojení k místní databázi SERVERU SQL Server. V opačném případě se brána připojí k serveru SQL Server přímo s kontextem zabezpečení brány (její spouštěcí účet).

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

Další informace naleznete v článku [konektoru serveru SQL Server.](data-factory-sqlserver-connector.md#linked-service-properties)

## <a name="data-transformation-activities"></a>AKTIVITY TRANSFORMACE DAT

Aktivita | Popis
-------- | -----------
[Aktivita hdinsight hive](#hdinsight-hive-activity) | Aktivita HDInsight Hive v kanálu Data Factory provádí dotazy Hive na vašem vlastním nebo na vyžádání windows/linuxového clusteru HDInsight.
[HDInsight Prasečí aktivita](#hdinsight-pig-activity) | Aktivita HDInsight Pig v datové továrně provádí dotazy Pig na vašem vlastním nebo na vyžádání založeném hdinsight clusteru windows/linuxu.
[HdInsight MapReduce aktivity](#hdinsight-mapreduce-activity) | Aktivita HDInsight MapReduce v kanálu Data Factory provádí programy MapReduce na vašem vlastním nebo na vyžádání založeného hdinsight clusteru windows/linux.
[Aktivita streamování HDInsight](#hdinsight-streaming-activity) | Aktivita streamování HDInsight v kanálu Data Factory provádí programy Hadoop Streaming na vašem vlastním nebo na vyžádání windows/linuxového clusteru HDInsight.
[Aktivita hdinsight spark](#hdinsight-spark-activity) | Aktivita HDInsight Spark v kanálu Data Factory spouští programy Spark ve vašem vlastním clusteru HDInsight.
[Aktivita Provedení dávky služby Machine Learning](#machine-learning-batch-execution-activity) | Azure Data Factory umožňuje snadno vytvářet kanály, které používají publikovanou webovou službu Azure Machine Learning pro prediktivní analýzy. Pomocí aktivity dávkového spuštění v kanálu Azure Data Factory můžete vyvolat webovou službu Machine Learning a vytvořit předpovědi na data v dávce.
[Aktivita Aktualizace prostředků služby Machine Learning](#machine-learning-update-resource-activity) | V průběhu času je třeba prediktivní modely v experimentech s hodnocením strojového učení přeškolit pomocí nových vstupních datových sad. Po dokončení rekvalifikace, chcete aktualizovat vyhodnocování webové služby s retrained machine learning modelu. Aktualizovat aktivitu zdrojů můžete použít k aktualizaci webové služby s nově trénovaným modelem.
[Aktivita Uložená procedura](#stored-procedure-activity) | Aktivitu uloženou proceduru v kanálu Data Factory můžete použít k vyvolání uložené procedury v jednom z následujících úložišť dat: Azure SQL Database, Azure SQL Data Warehouse, SQL Server Database ve vašem podniku nebo virtuální počítač Azure.
[Aktivita U-SQL služby Data Lake Analytics](#data-lake-analytics-u-sql-activity) | Aktivita U-SQL služby Data Lake Analytics spouští skript U-SQL v clusteru Azure Data Lake Analytics.
[Vlastní aktivita .NET](#net-custom-activity) | Pokud potřebujete transformovat data způsobem, který není podporován data factory, můžete vytvořit vlastní aktivitu s vlastní logiku zpracování dat a použít aktivitu v kanálu. Vlastní aktivitu rozhraní .NET můžete nakonfigurovat tak, aby se spouštěla pomocí služby Azure Batch nebo clusteru Azure HDInsight.


## <a name="hdinsight-hive-activity"></a>Aktivita Hivu služby HDInsight
V definici JSON aktivity Hive můžete určit následující vlastnosti. Vlastnost type pro aktivitu musí být: **HDInsightHive**. Nejprve musíte vytvořit propojenou službu HDInsight a zadat její název jako hodnotu vlastnosti **linkedServiceName.** Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity hdinsightHive:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| . |Zadání vlože skriptu Hive |Ne |
| cesta skriptu |Uložte skript Hive do úložiště objektů blob Azure a zadejte cestu k souboru. Použijte vlastnost 'script' nebo 'scriptPath'. Obojí nelze použít společně. Název souboru rozlišuje malá a velká písmena. |Ne |
| Definuje |Zadejte parametry jako dvojice klíč/hodnota pro odkazování ve skriptu Hive pomocí "hiveconf" |Ne |

Tyto vlastnosti typu jsou specifické pro aktivitu Hive. Ostatní vlastnosti (mimo oddíl typeProperties) jsou podporovány pro všechny aktivity.

### <a name="json-example"></a>Příklad JSON
Následující JSON definuje hdinsight hive aktivitu v kanálu.

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

Další informace naleznete v článku [Aktivita hive.](data-factory-hive-activity.md)

## <a name="hdinsight-pig-activity"></a>Aktivita Pig služby HDInsight
V definici JSON aktivity prasete můžete zadat následující vlastnosti. Vlastnost typu pro aktivitu musí být: **HDInsightPig**. Nejprve musíte vytvořit propojenou službu HDInsight a zadat její název jako hodnotu vlastnosti **linkedServiceName.** Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na HDInsightPig:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| . |Zadejte vložku skriptu Prase |Ne |
| cesta skriptu |Uložte skript Pig do úložiště objektů blob Azure a zadejte cestu k souboru. Použijte vlastnost 'script' nebo 'scriptPath'. Obojí nelze použít společně. Název souboru rozlišuje malá a velká písmena. |Ne |
| Definuje |Zadejte parametry jako páry klíč/hodnota pro odkazování ve skriptu Pig |Ne |

Tyto vlastnosti typu jsou specifické pro aktivitu prasete. Ostatní vlastnosti (mimo oddíl typeProperties) jsou podporovány pro všechny aktivity.

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

Další informace naleznete v článku Aktivita prasat.

## <a name="hdinsight-mapreduce-activity"></a>Aktivita MapReduce služby HDInsight
V definici MSSnížit aktivitu JSON můžete zadat následující vlastnosti. Vlastnost typu pro aktivitu musí být: **HDInsightMapReduce**. Nejprve musíte vytvořit propojenou službu HDInsight a zadat její název jako hodnotu vlastnosti **linkedServiceName.** Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na HDInsightMapReduce:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jarLinkedService | Název propojené služby pro Azure Storage, který obsahuje soubor JAR. | Ano |
| jarFilePath | Cesta k souboru JAR ve službě Azure Storage. | Ano |
| Classname | Název hlavní třídy v souboru JAR. | Ano |
| Argumenty | Seznam argumentů oddělených čárkami pro program MapReduce. Za běhu se zobrazí několik dalších argumentů (například mapreduce.job.tags) z rozhraní MapReduce. Chcete-li odlišit argumenty s Argumenty MapReduce, zvažte použití možnosti i hodnoty jako argumentů, jak je znázorněno v následujícím příkladu (-s, --input, --output atd., jsou možnosti bezprostředně následované jejich hodnotami) | Ne |

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

Další informace naleznete v článku [MapReduce Activity.](data-factory-map-reduce.md)

## <a name="hdinsight-streaming-activity"></a>Aktivita Streamování služby HDInsight
V definici JSON aktivity streamování Hadoop můžete určit následující vlastnosti. Vlastnost typu aktivity musí být: **HDInsightStreaming**. Nejprve musíte vytvořit propojenou službu HDInsight a zadat její název jako hodnotu vlastnosti **linkedServiceName.** Při nastavení typu aktivity na HDInsightStreaming jsou v části **typeProperties** podporovány následující vlastnosti:

| Vlastnost | Popis |
| --- | --- |
| Mapovač | Název spustitelného souboru mapovače. V příkladu cat.exe je mapper spustitelný soubor.|
| Redukce | Název spustitelného souboru reduktoru. V příkladu wc.exe je spustitelný soubor reduktoru. |
| vstup | Vstupní soubor (včetně umístění) pro mapovač. V příkladu: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"`: adfsample je kontejner objektů blob, example/data/Gutenberg je složka a davinci.txt je objekt blob. |
| output | Výstupní soubor (včetně umístění) pro reduktor. Výstup úlohy streamování Hadoop je zapsán do umístění určeného pro tuto vlastnost. |
| Filepaths | Cesty pro mapovač a redukční spustitelné soubory. V příkladu: "adfsample/example/apps/wc.exe", adfsample je kontejner objektů blob, příklad/aplikace je složka a wc.exe je spustitelný soubor. |
| fileLinkedService | Propojená služba Azure Storage, která představuje úložiště Azure, které obsahuje soubory zadané v části filePaths. |
| Argumenty | Seznam argumentů oddělených čárkami pro program MapReduce. Za běhu se zobrazí několik dalších argumentů (například mapreduce.job.tags) z rozhraní MapReduce. Chcete-li odlišit argumenty s Argumenty MapReduce, zvažte použití možnosti i hodnoty jako argumentů, jak je znázorněno v následujícím příkladu (-s, --input, --output atd., jsou možnosti bezprostředně následované jejich hodnotami) |
| getDebugInfo | Volitelný prvek. Pokud je nastavena na selhání, protokoly jsou staženy pouze při selhání. Pokud je nastavena na všechny, protokoly jsou vždy staženy bez ohledu na stav spuštění. |

> [!NOTE]
> Je nutné zadat výstupní datovou sadu pro aktivitu streamování Hadoop pro vlastnost **výstupy.** Tato datová sada může být pouze fiktivní datová sada, která je vyžadována k řízení plánu kanálu (každou hodinu, denně atd.). Pokud aktivita nepřevezme vstup, můžete přeskočit zadání vstupní datové sady pro aktivitu **vlastnosti inputs.**

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

Další informace naleznete v článku [Aktivita streamování Hadoop.](data-factory-hadoop-streaming-activity.md)

## <a name="hdinsight-spark-activity"></a>Aktivita Spark služby HDInsight
V definici JSON aktivity spark můžete zadat následující vlastnosti. Vlastnost typu aktivity musí být: **HDInsightSpark**. Nejprve musíte vytvořit propojenou službu HDInsight a zadat její název jako hodnotu vlastnosti **linkedServiceName.** Při nastavení typu aktivity na HDInsightSpark jsou v části **typeProperties** podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| rootPath | Kontejner a složka objektu Blob Azure, která obsahuje soubor Spark. Název souboru rozlišuje malá a velká písmena. | Ano |
| entryFilePath | Relativní cesta ke kořenové složce kódu Spark/balíčku. | Ano |
| Classname | Hlavní třída Java/Spark aplikace | Ne |
| Argumenty | Seznam argumentů příkazového řádku do programu Spark. | Ne |
| proxyUživatel | Uživatelský účet, který se má zosobnit za účelem spuštění programu Spark | Ne |
| sparkConfig | Vlastnosti konfigurace spark. | Ne |
| getDebugInfo | Určuje, kdy se soubory protokolu Spark zkopírují do úložiště Azure používaného clusterem HDInsight (nebo) určeného službou sparkJobLinkedService. Povolené hodnoty: Žádné, Vždy nebo Selhání. Výchozí hodnota: Žádný. | Ne |
| sparkJobLinkedService | Propojená služba Azure Storage, která obsahuje soubor úloh, závislostí a protokolů Spark.  Pokud nezadáte hodnotu pro tuto vlastnost, použije se úložiště přidružené ke clusteru HDInsight. | Ne |

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

- Vlastnost **type** je nastavena na **HDInsightSpark**.
- **RootPath** je nastavena na **adfspark\\pyFiles** kde adfspark je kontejner Azure Blob a pyFiles je v pořádku složky v tomto kontejneru. V tomto příkladu azure blob storage je ten, který je přidružený ke clusteru Spark. Soubor můžete nahrát do jiného úložiště Azure. Pokud tak učiníte, vytvořte propojenou službu Azure Storage, která propojí tento účet úložiště s toto datové toky. Potom zadejte název propojené služby jako hodnotu pro **vlastnost sparkJobLinkedService.** Podrobnosti o této vlastnosti a dalších vlastnostech podporovaných aktivitou sparku najdete v tématu Vlastnosti aktivity sparku.
- **EntryFilePath** je nastavena na **test.py**, což je soubor pythonu.
- Vlastnost **getDebugInfo** je nastavena na **always**, což znamená, že soubory protokolu jsou vždy generovány (úspěch nebo neúspěch).

    > [!IMPORTANT]
    > Doporučujeme nenastavovat tuto vlastnost vždy v produkčním prostředí, pokud neřešíte problém.
- Výstupní **outputs** sekce má jednu výstupní datovou sadu. Je nutné zadat výstupní datovou sadu i v případě, že program jiskry nevytváří žádný výstup. Výstupní datová sada řídí plán kanálu (každou hodinu, denně atd.).

Další informace o aktivitě najdete v článku [Aktivita sparku.](data-factory-spark.md)

## <a name="machine-learning-batch-execution-activity"></a>Aktivita Provedení dávky služby Machine Learning
Můžete zadat následující vlastnosti v Azure Machine Learning studio dávkové spuštění aktivity JSON definice. Vlastnost type pro aktivitu musí být: **AzureMLBatchExecution**. Nejprve musíte vytvořit propojenou službu Azure Machine Learning a zadat její název jako hodnotu vlastnosti **linkedServiceName.** Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na AzureMLBatchExecution:

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
webServiceInput | Datová sada, která má být předána jako vstup pro webovou službu Studio Azure Machine Learning. Tato datová sada musí být také zahrnuta do vstupů pro aktivitu. |Použijte webServiceInput nebo webServiceInputs. |
webServiceInputs | Zadejte datové sady, které mají být předány jako vstupy pro webovou službu Studio Azure Machine Learning. Pokud webová služba trvá více vstupů, použijte vlastnost webServiceInputs namísto použití vlastnosti webServiceInput. Datové sady, na které odkazuje **webServiceInputs,** musí být také zahrnuty do **vstupů aktivity**. | Použijte webServiceInput nebo webServiceInputs. |
webServiceOutputs | Datové sady, které jsou přiřazeny jako výstupy pro webovou službu Studio Azure Machine Learning. Webová služba vrátí výstupní data v této datové sadě. | Ano |
globalParameters | V této části zadejte hodnoty parametrů webové služby. | Ne |

### <a name="json-example"></a>Příklad JSON
V tomto příkladu aktivita má datovou sadu **MLSqlInput** jako vstup a **MLSqlOutput** jako výstup. **MLSqlInput** je předán jako vstup do webové služby pomocí **vlastnosti webServiceInput** JSON. **MlSqlOutput** je předán jako výstup webové služby pomocí **vlastnosti webServiceOutputs** JSON.

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

V příkladu JSON nasazené azure machine learning webové služby používá čtečku a modul pro zápis pro čtení a zápis dat z nebo do databáze Azure SQL. Tato webová služba zpřístupňuje následující čtyři parametry: název databázového serveru, název databáze, název uživatelského účtu serveru a heslo uživatelského účtu serveru.

> [!NOTE]
> Pouze vstupy a výstupy aktivity AzureMLBatchExecution mohou být předány jako parametry webové službě. Například ve výše uvedeném fragmentu JSON je MLSqlInput vstupem do aktivity AzureMLBatchExecution, která je předána jako vstup do webové služby prostřednictvím parametru webServiceInput.

## <a name="machine-learning-update-resource-activity"></a>Aktivita aktualizace prostředku služby Machine Learning
Můžete zadat následující vlastnosti v Azure Machine Learning studio Update aktivity prostředků JSON definice. Vlastnost type pro aktivitu musí být: **AzureMLUpdateResource**. Nejprve musíte vytvořit propojenou službu Azure Machine Learning a zadat její název jako hodnotu vlastnosti **linkedServiceName.** Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na AzureMLUpdateResource:

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
trainedModelName | Název přetrénovaného modelu. | Ano |
trainedModelDatasetName | Datová sada směřující na soubor iLearner vrácený retraining operací. | Ano |

### <a name="json-example"></a>Příklad JSON
Kanál má dvě aktivity: **AzureMLBatchExecution** a **AzureMLUpdateResource**. Azure Machine Learning studio Batch Execution aktivity bere trénovací data jako vstup a vytvoří soubor iLearner jako výstup. Aktivita vyvolá trénovací webovou službu (trénovací experiment vystavený jako webová služba) se vstupními trénovacími daty a obdrží soubor ilearner z webové služby. Zástupný objekt Blob je pouze fiktivní výstupní datová sada, kterou služba Azure Data Factory vyžaduje ke spuštění kanálu.


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
V definici JSON aktivity U-SQL můžete zadat následující vlastnosti. Vlastnost type pro aktivitu musí být: **DataLakeAnalyticsU-SQL**. Musíte vytvořit propojenou službu Azure Data Lake Analytics a zadat její název jako hodnotu vlastnosti **linkedServiceName.** Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na DataLakeAnalyticsU-SQL:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| scriptPath |Cesta ke složce, která obsahuje skript U-SQL. Název souboru rozlišuje malá a velká písmena. |Ne (pokud používáte skript) |
| skriptLinkedService |Propojená služba, která propojuje úložiště obsahující skript s továrně dat |Ne (pokud používáte skript) |
| . |Místo zadávání skriptuPath a scriptLinkedService zadejte vložkový skript. Například: "skript": "VYTVOŘIT test databáze". |Ne (pokud používáte scriptPath a scriptLinkedService) |
| degreeOfParallelism |Maximální počet uzlů současně používaných ke spuštění úlohy. |Ne |
| Prioritou |Určuje, které úlohy ze všech, které jsou zařazeny do fronty, by měly být vybrány jako první. Čím nižší číslo, tím vyšší priorita. |Ne |
| parameters |Parametry skriptu U-SQL |Ne |

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

Další informace naleznete v [tématu Data Lake Analytics U-SQL Activity](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Aktivita Uložená procedura
V definici JSON aktivity uložené procedury můžete zadat následující vlastnosti. Vlastnost type pro aktivitu musí být: **SqlServerStoredProcedure**. Je nutné vytvořit jednu z následujících propojených služeb a zadat název propojené služby jako hodnotu vlastnosti **linkedServiceName:**

- SQL Server
- Azure SQL Database
- Azure SQL Data Warehouse

Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na SqlServerStoredProcedure:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| storedProcedureName |Zadejte název uložené procedury v databázi Azure SQL nebo Azure SQL Data Warehouse, který je reprezentován propojenou službou, kterou používá výstupní tabulka. |Ano |
| storedProcedureParameters |Zadejte hodnoty pro parametry uložené procedury. Pokud potřebujete předat hodnotu null pro parametr, použijte syntaxi: "param1": null (všechna malá písmena). Informace o používání této vlastnosti naleznete v následující ukázce. |Ne |

Pokud zadáte vstupní datovou sadu, musí být k dispozici (ve stavu "Připraveno") pro spuštění aktivity uložené procedury. Vstupní datovou sadu nelze spotřebovat v uložené proceduře jako parametr. Používá se pouze ke kontrole závislosti před spuštěním aktivity uložené procedury. Je nutné zadat výstupní datovou sadu pro aktivitu uložené procedury.

Výstupní datová sada určuje **plán** pro aktivitu uložené procedury (hodinové, týdenní, měsíční atd.). Výstupní datová sada musí používat **propojenou službu,** která odkazuje na Azure SQL Database nebo Azure SQL Data Warehouse nebo sql server databáze, ve kterém chcete, aby uložená procedura spustit. Výstupní datová sada může sloužit jako způsob, jak předat výsledek uložené procedury pro následné zpracování jinou aktivitou ([zřetězení činnosti](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) v kanálu. Data Factory však automaticky nezapisuje výstup uložené procedury do této datové sady. Je uložená procedura, která zapisuje do tabulky SQL, na kterou odkazuje výstupní datová sada. V některých případech může být výstupní datovou sadou **fiktivní datová sada**, která se používá pouze k určení plánu pro spuštění aktivity uložené procedury.

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

Další informace naleznete v článku [Aktivita uložené procedury.](data-factory-stored-proc-activity.md)

## <a name="net-custom-activity"></a>Vlastní aktivita .NET
V definici JSON vlastní aktivity .NET můžete zadat následující vlastnosti. Vlastnost typu aktivity musí být: **DotNetActivity**. Musíte vytvořit propojenou službu Azure HDInsight nebo propojenou službu Azure Batch a zadat název propojené služby jako hodnotu vlastnosti **linkedServiceName.** Následující vlastnosti jsou podporovány v části **typeProperties** při nastavení typu aktivity na DotNetActivity:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| Assemblyname | Název sestavení. V příkladu je: **MyDotnetActivity.dll**. | Ano |
| EntryPoint |Název třídy, která implementuje rozhraní IDotNetActivity. V příkladu je: **MyDotNetActivityNS.MyDotNetActivity** kde MyDotNetActivityNS je obor názvů a MyDotNetActivity je třída.  | Ano |
| Služba PackageLinkedService | Název propojené služby Azure Storage, která odkazuje na úložiště objektů blob, které obsahuje soubor zip vlastní aktivity. V příkladu je: **AzureStorageLinkedService**.| Ano |
| Soubor balíčků | Název souboru zip. V příkladu je: **customactivitycontainer/MyDotNetActivity.zip**. | Ano |
| extendedProperties | Rozšířené vlastnosti, které můžete definovat a předat kódu .NET. V tomto příkladu je proměnná **SliceStart** nastavena na hodnotu založenou na systémové proměnné SliceStart. | Ne |

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

Podrobné informace naleznete [v tématu Použití vlastních aktivit v](data-factory-use-custom-activities.md) článku Data Factory.

## <a name="next-steps"></a>Další kroky
Projděte si tyto kurzy:

- [Kurz: vytvoření kanálu s aktivitou kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Kurz: vytvoření kanálu s aktivitou podregistru](data-factory-build-your-first-pipeline.md)
