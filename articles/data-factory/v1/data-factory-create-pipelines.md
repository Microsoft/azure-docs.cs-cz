---
title: Vytvořit/plán kanály, řetězit aktivity v Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak ve službě Azure Data Factory pro přesouvání a transformaci dat vytvoříte kanál data. Vytvoření pracovního postupu na základě dat použít informace vytvořit připravený.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 09fd2f38c3746cf92d576325058dc36221ae50cd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38668023"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Kanály a aktivity ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-create-pipelines.md)
> * [Verze 2 (aktuální verze)](../concepts-pipelines-activities.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [kanály ve V2](../concepts-pipelines-activities.md).

Tento článek vám pomůže pochopit kanály a aktivity ve službě Azure Data Factory a naučit se je používat k sestavení kompletních pracovních postupů založených na datech pro potřeby přesunů a zpracování dat.  

> [!NOTE]
> Tento článek předpokládá, že jste prošli [Úvod do služby Azure Data Factory](data-factory-introduction.md). Pokud nemáte praktická-na-zkušeností s vytvořením datové továrny, probíhá [kurzu transformace dat](data-factory-build-your-first-pipeline.md) a/nebo [kurzu přesunu dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) by vám lépe porozumět v tomto článku.  

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit, které dohromady provádějí určitou úlohu. Aktivity v kanálu definují akce, které se mají provést s vašimi daty. Například můžete použít aktivitu kopírování ke zkopírování dat z místního SQL Serveru do služby Azure Blob Storage. Pak použijete aktivitu Hive, která spouští skript Hive v clusteru Azure HDInsight, aby zpracovala/transformovala data z úložiště objektů blob za účelem vytvoření výstupních dat. Nakonec použijte druhou aktivitu kopírování, která zkopíruje výstupní data do služby Azure SQL Data Warehouse, které jsou postavena řešení tvorby sestav business intelligence (BI). 

Každá aktivita může mít nula nebo více vstupních [datových sad](data-factory-create-datasets.md) a může generovat jednu nebo více výstupních [datových sad](data-factory-create-datasets.md). Následující diagram znázorňuje vztah mezi kanálem, aktivitou a datovou sadou v Data Factory: 

![Vztah mezi kanálu, aktivitou a datovou sadu](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Kanál umožňuje spravovat aktivity jako sadu, a ne každou jednotlivě. Můžete nasadit, naplánovat, pozastavit a obnovit kanálu, namísto týkající se aktivity v kanálu nezávisle na sobě.

Data Factory podporuje dva typy aktivit: aktivity přesunu dat a transformace dat. Každá aktivita může mít nula nebo více vstupních [datových sad](data-factory-create-datasets.md) a vygenerovat výstupní datové sady.

Vstupní datová sada představuje vstup pro aktivitu v kanálu a výstupní datová sada představuje výstup pro aktivitu. Datové sady identifikují data v rámci různých úložišť dat, jako jsou tabulky, soubory, složky a dokumenty. Po vytvoření datové sady můžete tuto datovou sadu používat v aktivitách v rámci kanálu. Datová sada například může být vstupní/výstupní datovou sadou aktivity kopírování nebo aktivity HDInsightHive. Další informace o datových sadách najdete v článku [Datové sady v Azure Data Factory](data-factory-create-datasets.md).

### <a name="data-movement-activities"></a>Aktivity přesunu dat
Aktivita kopírování ve službě Data Factory kopíruje data ze zdrojového úložiště dat do úložiště dat jímky. Data Factory podporuje následující typy úložišť dat. Data z libovolného zdroje lze zapsat do libovolné jímky. Kliknutím na úložiště dat se dozvíte, jak kopírovat data z a do daného úložiště.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Úložiště dat s * mohou být místní nebo v Azure IaaS a vyžadují nainstalování [Brány správy dat](data-factory-data-management-gateway.md) na místním počítači nebo na počítači Azure IaaS.

Podrobnosti najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Aktivity transformace dat
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Podrobnosti najdete v článku [Aktivity transformace dat](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Vlastní aktivity .NET 
Pokud potřebujete přesunout data do nebo z datového úložiště, aktivita kopírování nepodporuje, nebo transformovat data pomocí vlastní logiky, vytvoření **vlastní aktivitu .NET**. Podrobnosti o vytvoření a používání vlastní aktivity najdete v tématu [Použití vlastních aktivit v kanálu služby Azure Data Factory](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Plánování kanálů
Kanál je aktivní jenom mezi jeho **start** čas a **end** čas. Nebude provedena před časem spuštění nebo po času ukončení. Pokud kanál je pozastavený, se nebudou provedeny bez ohledu na jejich počáteční a koncový čas. Pro kanál ke spuštění by neměly být pozastaveno. Zobrazit [plánování a provádění](data-factory-scheduling-and-execution.md) pochopit, jak funguje plánování a provádění ve službě Azure Data Factory.

## <a name="pipeline-json"></a>Zápis JSON kanálu
Teď se blíže podíváme na to, jak se kanál definuje ve formátu JSON. Obecná struktura kanálu vypadá takto:

```json
{
    "name": "PipelineName",
    "properties": 
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets": 
        [
        ]
    }
}
```

| Značka | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název kanálu. Určuje název, který představuje akci prováděnou kanálem. <br/><ul><li>Maximální počet znaků: 260.</li><li>Musí začínat písmenem, číslicí nebo podtržítkem (_).</li><li>Nejsou povolené tyto znaky: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Ano |
| description | Určuje text popisující, k čemu se kanál používá. |Ano |
| activities | Část **activities** může obsahovat definici jedné nebo více aktivit. V části Další podrobnosti o elementu activities formátu JSON. | Ano |  
| start | Počáteční datum a čas pro kanál. Musí být v [formátu ISO](http://en.wikipedia.org/wiki/ISO_8601). Například: `2016-10-14T16:32:41Z`. <br/><br/>Je možné zadat místního času, například Odhadovaný čas. Tady je příklad: `2016-02-27T06:00:00-05:00`", což je odhad AM 6<br/><br/>Počáteční a koncové vlastnosti definujte aktivní období kanálu. Výstupní řezy se tvoří pouze s v tomto aktivní období. |Ne<br/><br/>Pokud zadáte hodnotu pro vlastnost end, musíte zadat hodnotu pro vlastnost start.<br/><br/>Počáteční a koncový čas může být oba prázdné k vytvoření kanálu. Je nutné zadat obě hodnoty se nastavit aktivní období kanálu ke spuštění. Pokud nezadáte počáteční a koncový čas při vytváření kanálu, můžete nastavit pomocí rutiny Set-AzureRmDataFactoryPipelineActivePeriod později. |
| konec | Koncové datum a čas pro kanál. Je-li zadána, musí být ve formátu ISO. Příklad: `2016-10-14T17:32:41Z` <br/><br/>Je možné zadat místního času, například Odhadovaný čas. Tady je příklad: `2016-02-27T06:00:00-05:00`, což je odhad AM 6<br/><br/>Kanál spouštět bez omezení, zadejte jako hodnotu pro vlastnost end 9999-09-09. <br/><br/> Kanál je aktivní jenom mezi jeho počáteční a koncový čas. Nebude provedena před časem spuštění nebo po času ukončení. Pokud kanál je pozastavený, se nebudou provedeny bez ohledu na jejich počáteční a koncový čas. Pro kanál ke spuštění by neměly být pozastaveno. Zobrazit [plánování a provádění](data-factory-scheduling-and-execution.md) pochopit, jak funguje plánování a provádění ve službě Azure Data Factory. |Ne <br/><br/>Pokud zadáte hodnotu pro vlastnost start, musíte zadat hodnotu pro vlastnost end.<br/><br/>Naleznete v poznámkách k **start** vlastnost. |
| isPaused | Je-li nastavena hodnota true, kanál se nespustí. Je v pozastaveném stavu. Výchozí hodnota = false. Tato vlastnost slouží k povolení nebo zakázání kanálu. |Ne |
| pipelineMode | Metoda pro naplánování spuštění pro kanál. Povolené hodnoty jsou: naplánované (výchozí), jednorázové.<br/><br/>"Naplánované" označuje, že se kanál spustí v zadaný časový interval podle jeho aktivního období (počáteční a koncový čas). "Jednorázové" označuje, že se kanál spustí pouze jednou. Jednorázová kanály po vytvoření se nedají upravit/aktualizovat aktuálně. Zobrazit [Onetime kanálu](#onetime-pipeline) podrobnosti o jednorázové nastavení. |Ne |
| expirationTime | Dobu trvání, po vytvoření, pro kterou [jednorázového kanálu](#onetime-pipeline) platnost a měly by být zřízené. Pokud nemá žádná aktivní, se nezdařilo, nebo nevyřízených spuštění, zřetězení příkazů je automaticky odstraněna po dosáhne čas vypršení platnosti. Výchozí hodnota: `"expirationTime": "3.00:00:00"`|Ne |
| datové sady |Seznam datových sad pro aktivity definované v kanálu. Tato vlastnost slouží k definování datové sady, které jsou specifické pro tento kanál a není definovaný v rámci služby data factory. Datové sady definovaných v rámci tohoto kanálu jde použít jenom pomocí tohoto kanálu a nelze sdílet. Zobrazit [obor datové sady](data-factory-create-datasets.md#scoped-datasets) podrobnosti. |Ne |

## <a name="activity-json"></a>Zápis JSON aktivity
Část **activities** může obsahovat definici jedné nebo více aktivit. Každá aktivita má následující strukturu nejvyšší úrovně:

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

Následující tabulka obsahuje popis vlastností v definici aktivity ve formátu JSON:

| Značka | Popis | Požaduje se |
| --- | --- | --- |
| jméno | Název aktivity. Určuje název, který představuje akci prováděnou danou aktivitou. <br/><ul><li>Maximální počet znaků: 260.</li><li>Musí začínat písmenem, číslicí nebo podtržítkem (_).</li><li>Nejsou povolené tyto znaky: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Ano |
| description | Text popisující, k čemu aktivita slouží. |Ano |
| type | Typ aktivity. Zobrazit [aktivity přesunu dat](#data-movement-activities) a [aktivity transformace dat](#data-transformation-activities) oddíly pro různé typy aktivit. |Ano |
| vstupy |Vstupní tabulky použité aktivitou<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Ano |
| výstupy |Výstupní tabulky použité aktivitou.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Ano |
| linkedServiceName |Název propojené služby používané aktivitou. <br/><br/>Aktivita může vyžadovat zadání propojené služby, která odkazuje na požadované výpočetní prostředí. |Ano pro aktivitu HDInsight a Azure Machine Learning aktivita dávkového bodování <br/><br/>Ne ve všech ostatních případech |
| typeProperties |Vlastnosti **typeProperties** části závisí na typu aktivity. Pokud chcete zobrazit vlastnosti typu určité aktivity, klikněte na odkaz na aktivitu v předchozí části. | Ne |
| policy |Zásady, které ovlivňují chování aktivity za běhu. Pokud není zadaný, použijí se výchozí zásady. |Ne |
| Scheduler | Vlastnost "scheduler" se používá k definování požadované plánování aktivity. Jeho objektu třídy subproperties jsou stejné jako ty v [vlastnost availability v datové sadě](data-factory-create-datasets.md#dataset-availability). |Ne |


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

## <a name="sample-copy-pipeline"></a>Ukázkový kanál kopírování
V následujícím ukázkovém kanálu je v části **activities** jedna aktivita typu **Kopírování**. V této ukázce [aktivita kopírování](data-factory-data-movement-activities.md) kopíruje data ze služby Azure Blob Storage do služby Azure SQL Database. 

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
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
} 
```

Je třeba počítat s následujícím:

* V části aktivit je jenom jedna aktivita, jejíž vlastnost **type** je nastavená na **Copy**.
* Vstup aktivity je nastavený na **InputDataset** a výstup aktivity je nastavený na **OutputDataset**. Informace o definicích datových sad ve formátu JSON najdete v článku [Datové sady](data-factory-create-datasets.md). 
* V části **typeProperties** je jako typ zdroje určen **BlobSource** a jako typ jímky **SqlSink**. V [aktivity přesunu dat](#data-movement-activities) oddíl, klikněte na úložiště dat, že chcete získat další informace o přesouvání dat do a z tohoto úložiště dat použít jako zdroj nebo jímku. 

Kompletní postup vytváření tohoto kanálu najdete v části [kurz: kopírování dat z Blob Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="sample-transformation-pipeline"></a>Ukázkový kanál transformace
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
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Je třeba počítat s následujícím: 

* V části activities je jenom jedna aktivita, jejíž vlastnost **type** má hodnotu **HDInsightHive**.
* Soubor skriptu Hive **partitionweblogs.hql** je uložený v účtu služby Azure Storage (který určuje služba scriptLinkedService s názvem **AzureStorageLinkedService**) a ve složce **script** v kontejneru **adfgetstarted**.
* `defines` Oddíl se používá k určení nastavení modulu runtime, které se předá skriptu hive jako konfigurační hodnoty Hive (např. `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Část **typeProperties** je u každé aktivity transformace odlišná. Další informace o vlastnostech typu podporovaných u aktivit transformace, klikněte na aktivitu transformace v [aktivity transformace dat](#data-transformation-activities) tabulky. 

Kompletní postup vytváření tohoto kanálu najdete v části [kurz: vytvoření prvního kanálu pro zpracování dat pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="multiple-activities-in-a-pipeline"></a>Více aktivit v kanálu
Oba předchozí ukázkové kanály obsahovaly jenom jednu aktivitu. Kanál může obsahovat víc než jednu aktivitu.  

Pokud máte více aktivit v kanálu a výstup aktivity není vstup jiné aktivity, mohou se aktivity spouštět souběžně Pokud kolekce obsahuje nějaké řezy vstupní data pro aktivity je připravené. 

Dvě aktivity můžete zřetězit tím, že výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Druhá aktivita spustí, pouze pokud první z nich úspěšně dokončí.

![Řetězení aktivit v kanálech stejné](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

V tomto příkladu tento kanál obsahuje dvou aktivit: aktivity "activity1" a "activity2". Vezme Dataset1 jako vstup aktivity "activity1" a vytváří výstupní Dataset2. Aktivita přijímá jako vstup Dataset2 a vytváří výstupní Dataset3. Od výstup aktivity "activity1" vstup "activity2", "activity2" spustí, až po úspěšném dokončení aktivity a produkuje řez Dataset2 je (Dataset2). Pokud aktivity "activity1" z nějakého důvodu nepovede, nevytvoří řez Dataset2 Activity 2 nespustí pro tento řez (například: 9: 00 do 10: 00). 

Můžete také zřetězit aktivity, které jsou v různých kanálech.

![Řetězení aktivit v dva kanály](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

V této ukázce Pipeline1 má pouze jednu aktivitu, která přijímá Dataset1 jako vstup a vytvoří Dataset2 jako výstup. Pipeline2 také má pouze jednu aktivitu, která přebírá Dataset2 jako vstup a Dataset3 jako výstup. 

Další informace najdete v tématu [plánování a provádění](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

## <a name="create-and-monitor-pipelines"></a>Vytvořit a monitorovat kanály
Můžete vytvářet kanály pomocí jedné z těchto nástrojů nebo sad SDK. 

- Průvodce kopírováním. 
- Azure Portal
- Visual Studio
- Azure PowerShell
- Šablona Azure Resource Manageru
- REST API
- .NET API

Najdete v následujících kurzech najdete podrobné pokyny pro vytváření kanálů pomocí jedné z těchto nástrojů nebo sad SDK.
 
- [Vytvoření kanálu s aktivitou transformace dat](data-factory-build-your-first-pipeline.md)
- [Vytvoření kanálu s aktivitou přesunu dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Jakmile kanálu vytvořili a nasadili, můžete spravovat a monitorování kanálů pomocí oken webu Azure portal nebo monitorování a Správa aplikace. V následujících tématech pro podrobné pokyny. 

- [Monitorování a Správa kanálů pomocí oken webu Azure portal](data-factory-monitor-manage-pipelines.md).
- [Monitorování a Správa kanálů pomocí monitorování a Správa aplikace](data-factory-monitor-manage-app.md)


## <a name="onetime-pipeline"></a>Jednorázová kanálu
Můžete vytvářet a plánovat pravidelné spouštění kanálu (třeba: každou hodinu nebo každý den) v rámci počátečním a koncovým časem, zadejte v definici kanálu. Zobrazit [plánování aktivit](#scheduling-and-execution) podrobnosti. Můžete také vytvořit kanál, který se spustí pouze jednou. Uděláte to tak, že nastavíte **pipelineMode** vlastnost v definici kanálu **jednorázově** jak je znázorněno v následujícím příkladu JSON. Výchozí hodnota této vlastnosti je **naplánované**.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ]
                "name": "CopyActivity-0"
            }
        ]
        "pipelineMode": "OneTime"
    }
}
```

Je třeba počítat s následujícím:

* **Spustit** a **end** časem pro kanál nejsou zadány.
* **Dostupnost** vstupní a výstupní datové sady je zadána (**frekvence** a **interval**), i když objekt pro vytváření dat nepoužívá hodnoty.  
* Zobrazení diagramu nezobrazuje jednorázové kanály. Toto chování je záměrné.
* Jednorázové kanály se nedá aktualizovat. Můžete naklonovat jednorázového kanálu, přejmenovat, aktualizovat vlastnosti a nasadit ho chcete vytvořit další.


## <a name="next-steps"></a>Další kroky
- Další informace o datových sadách najdete v tématu [vytvoření datových sad](data-factory-create-datasets.md) článku. 
- Další informace o tom, jak jsou kanály naplánovala a provést, najdete v části [plánování a provádění ve službě Azure Data Factory](data-factory-scheduling-and-execution.md) článku. 
  

