---
title: Vytvořit nebo plán kanály, řetězu aktivit ve službě Data Factory | Microsoft Docs
description: Naučte se vytvořit kanál dat v Azure Data Factory přesunout a transformovat data. Vytvoření pracovního postupu řízených daty k vytvoření připravené k použití informací.
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
ms.openlocfilehash: f80a22c39608a9d9c67977f2d0493af7300f475b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622702"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Kanály a aktivity v Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-create-pipelines.md)
> * [Verze 2 – Preview](../concepts-pipelines-activities.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [kanály v V2](../concepts-pipelines-activities.md).

Tento článek vám pomůže pochopit kanály a aktivity ve službě Azure Data Factory a naučit se je používat k sestavení kompletních pracovních postupů založených na datech pro potřeby přesunů a zpracování dat.  

> [!NOTE]
> Tento článek předpokládá, že jste prošli [Úvod do Azure Data Factory](data-factory-introduction.md). Pokud nemáte hands-na-zkušenosti s vytvářením objektů pro vytváření dat, projít [kurzu transformaci dat](data-factory-build-your-first-pipeline.md) nebo [kurzu přesun dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) by vám pomohou pochopit, v tomto článku lépe.  

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit, které dohromady provádějí určitou úlohu. Aktivity v kanálu definují akce, které se mají provést s vašimi daty. Například můžete použít aktivitu kopírování ke zkopírování dat z místního SQL Serveru do služby Azure Blob Storage. Pak použijete aktivitu Hive, která spouští skript Hive v clusteru Azure HDInsight, aby zpracovala/transformovala data z úložiště objektů blob za účelem vytvoření výstupních dat. Nakonec použijte druhou aktivitu kopírování, která zkopíruje výstupní data do služby Azure SQL Data Warehouse, které jsou postavena řešení tvorby sestav business intelligence (BI). 

Každá aktivita může mít nula nebo více vstupních [datových sad](data-factory-create-datasets.md) a může generovat jednu nebo více výstupních [datových sad](data-factory-create-datasets.md). Následující diagram znázorňuje vztah mezi kanálem, aktivitou a datovou sadou v Data Factory: 

![Vztah mezi kanálu, aktivity a datové sady](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Kanál umožňuje spravovat aktivity jednotlivě jako sada místo každé z nich. Můžete nasadit, plánovat, pozastavit a obnovit kanál, místo nezávisle zabývají aktivity v kanálu.

Data Factory podporuje dva typy aktivit: aktivity přesunu dat a transformace dat. Každá aktivita může mít vstup nula nebo více [datové sady](data-factory-create-datasets.md) a vytvoří výstupní datové sady.

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
Pokud potřebujete přesunout úložiště dat do nebo z dat, aktivitě kopírování nebude podporovat, nebo transformace dat pomocí vlastní logiky, vytvořte **vlastní aktivity .NET**. Podrobnosti o vytvoření a používání vlastní aktivity najdete v tématu [Použití vlastních aktivit v kanálu služby Azure Data Factory](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Plán kanálů
Kanál je aktivní jenom mezi jeho **spustit** čas a **end** čas. Nebude provedena před časem zahájení nebo po koncovém čase. Když je pozastavená kanálu, se nebudou provedeny bez ohledu na jeho počáteční a koncový čas. Pro kanál ke spuštění by neměl být pozastavena. V tématu [plánování a provádění](data-factory-scheduling-and-execution.md) pochopit, jak funguje plánování a provádění v Azure Data Factory.

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
| activities | Část **activities** může obsahovat definici jedné nebo více aktivit. Najdete v části Další informace o elementu, JSON aktivity. | Ano |  
| start | Počáteční datum a čas pro kanál. Musí být v [formátu ISO](http://en.wikipedia.org/wiki/ISO_8601). Například: `2016-10-14T16:32:41Z`. <br/><br/>Je možné zadat místní čas, například Odhadovaný čas. Tady je příklad: `2016-02-27T06:00:00-05:00`", což je odhadované AM 6<br/><br/>Počáteční a koncové vlastnosti společně zadejte aktivní období kanálu. Výstup řezy jenom vytváří se v tomto aktivní období. |Ne<br/><br/>Pokud zadáte hodnotu pro vlastnost end, zadejte hodnotu pro vlastnost start.<br/><br/>Počáteční a koncový čas i lze vytvořit kanál prázdný. Musíte zadat obě hodnoty se nastavit aktivní období pro kanál ke spuštění. Pokud nezadáte počáteční a koncový čas při vytváření kanálu, můžete nastavit pomocí rutiny Set-AzureRmDataFactoryPipelineActivePeriod později. |
| konec | Koncové datum a čas pro kanál. Pokud zadaný, musí být ve formátu ISO. Příklad: `2016-10-14T17:32:41Z` <br/><br/>Je možné zadat místní čas, například Odhadovaný čas. Tady je příklad: `2016-02-27T06:00:00-05:00`, což je odhadované AM 6<br/><br/>Chcete-li kanál spouštět bez omezení, zadejte jako hodnotu pro vlastnost end 9999-09-09. <br/><br/> Kanál je aktivní jenom mezi její počáteční čas a koncový čas. Nebude provedena před časem zahájení nebo po koncovém čase. Když je pozastavená kanálu, se nebudou provedeny bez ohledu na jeho počáteční a koncový čas. Pro kanál ke spuštění by neměl být pozastavena. V tématu [plánování a provádění](data-factory-scheduling-and-execution.md) pochopit, jak funguje plánování a provádění v Azure Data Factory. |Ne <br/><br/>Pokud zadáte hodnotu pro vlastnost spustit, musíte zadat hodnotu pro vlastnost end.<br/><br/>Naleznete v poznámkách k **spustit** vlastnost. |
| isPaused | Pokud je nastaven na hodnotu true, kanál nejde spustit. Je v pozastaveném stavu. Výchozí hodnota = false. Tato vlastnost slouží k povolení nebo zakázání kanálu. |Ne |
| pipelineMode | Metoda pro naplánování spuštění pro kanál. Povolené hodnoty jsou: naplánované (výchozí), jednorázově.<br/><br/>"Pravidelnou" udává, že kanál spouští v zadaném časovém intervalu podle jeho aktivní období (počáteční a koncový čas). 'Jednorázově' udává, že kanál spouští jenom jednou. Po vytvoření jednorázově kanály nelze aktuálně upravit nebo aktualizovat. V tématu [Onetime kanálu](#onetime-pipeline) podrobnosti o jednorázově nastavení. |Ne |
| ExpirationTime | Doba, po vytvoření, pro kterou [jednorázového kanálu](#onetime-pipeline) je platná a by měla zůstat zřízené. Pokud nemá žádné aktivní, se nezdařilo, nebo až spuštění kanálu je automaticky odstraněna po dorazí čas vypršení platnosti. Výchozí hodnota: `"expirationTime": "3.00:00:00"`|Ne |
| Datové sady |Seznam datových sad má být používána aktivity definované v kanálu. Tuto vlastnost lze použít k definování datových sad, které jsou specifické pro tento kanál a není definován v rámci služby data factory. Datové sady definované v rámci tohoto kanálu lze použít pouze tento kanál a nesmí se sdílet. V tématu [obor datové sady](data-factory-create-datasets.md#scoped-datasets) podrobnosti. |Ne |

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
| type | Typ aktivity. Najdete v článku [aktivity přesunu dat](#data-movement-activities) a [aktivit transformace dat](#data-transformation-activities) oddíly pro různé typy aktivit. |Ano |
| vstupy |Vstupní tabulky použité aktivitou<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Ano |
| výstupy |Výstupní tabulky použité aktivitou.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Ano |
| linkedServiceName |Název propojené služby používané aktivitou. <br/><br/>Aktivita může vyžadovat zadání propojené služby, která odkazuje na požadované výpočetní prostředí. |Ano pro aktivita HDInsight a Azure Machine Learning aktivita dávkového vyhodnocování <br/><br/>Ne ve všech ostatních případech |
| typeProperties |Vlastnosti v **rámci typeProperties** části závisí na typu aktivity. Pokud chcete zobrazit vlastnosti typu určité aktivity, klikněte na odkaz na aktivitu v předchozí části. | Ne |
| policy |Zásady, které ovlivňují chování aktivity za běhu. Pokud není zadaný, použijí se výchozí zásady. |Ne |
| Scheduler | Vlastnost "scheduler" se používá k definování požadované plánování pro aktivitu. Jeho podvlastnosti jsou stejné jako ty, které jsou v [vlastnost availability v datové sadě](data-factory-create-datasets.md#dataset-availability). |Ne |


### <a name="policies"></a>Zásady
Zásady ovlivňují chování běhu aktivity, konkrétně při zpracování řezu tabulky. Následující tabulka obsahuje podrobnosti.

| Vlastnost | Povolené hodnoty | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| Souběžnosti |Integer <br/><br/>Maximální hodnota: 10 |1 |Počet souběžných spuštění aktivity.<br/><br/>Určuje počet spuštění paralelní aktivity, které se může stát při jiné řezy. Například pokud aktivitu musí projít, velké sady dostupných dat, mají větší hodnotu souběžnosti urychluje zpracování dat. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Určuje pořadí datové řezy, které jsou zpracovávány.<br/><br/>Pokud máte 2 řezy (jeden situaci ve 4 a další v 17: 00) a jsou obě čekající na zpracování. Pokud jste nastavili executionPriorityOrder být NewestFirst, je nejprve zpracování řezu v 17: 00. Podobně pokud nastavíte executionPriorityORder být OldestFIrst, pak ve 4 zpracování řezu se. |
| retry |Integer<br/><br/>Maximální hodnota může být 10 |0 |Počet opakování, než se zpracování dat pro řez je označen jako selhání. Provedení aktivity pro datový řez je opakovat až zadaný počet. Opakovaném provádí co nejdříve po selhání. |
| timeout |Časový interval |00:00:00 |Časový limit aktivity. Příklad: 00:10:00 (znamená časový limit 10 minut)<br/><br/>Pokud hodnota není zadána nebo je 0, časový limit je nekonečno.<br/><br/>Pokud bude čas zpracování dat na řez překročí hodnota časového limitu, se zruší a systém se pokusí opakujte zpracování. Počet pokusů, závisí na vlastnost opakování. Když dojde k vypršení časového limitu, je stav nastaven na TimedOut. |
| Zpoždění |Časový interval |00:00:00 |Zadejte zpoždění před zpracování dat řezu spustí.<br/><br/>Provádění aktivity pro datový řez se spustí po zpoždění očekávaný čas spuštění.<br/><br/>Příklad: 00:10:00 (znamená zpoždění 10 minut) |
| opakování po delší době |Integer<br/><br/>Maximální hodnota: 10 |1 |Počet dlouho opakování pokusů, než řez spuštění se nezdařilo.<br/><br/>pokusy o opakování po delší době jsou rozmístěny ve longRetryInterval. Takže pokud je třeba zadat čas mezi pokusy o opakování, použijte opakování po delší době. Pokud jsou zadané opakování a opakování po delší době, jednotlivé pokusy o opakování po delší době zahrnuje opakovaných pokusů a je maximální počet pokusů o opakování * opakování po delší době.<br/><br/>Například, pokud bychom měli následující nastavení v zásadách aktivit:<br/>Opakujte: 3<br/>opakování po delší době: 2<br/>longRetryInterval: 01:00:00<br/><br/>Předpokládá se jenom jeden řez provést (stav Čeká) a provedení aktivity pokaždé, když dojde k chybě. Nejdřív by 3 provádění po sobě jdoucích pokusů. Po každém pokusu o stav řezu bude opakovat. Po první 3 pokusy jsou přes, bude stav řezu opakování po delší době.<br/><br/>Po hodině (který je na longRetryInteval hodnota) bude další sadu 3 provádění po sobě jdoucích pokusů. Poté stav řezu by se nezdařilo a by se pokus o žádné další opakování. Proto celkové 6 pokusy byly provedeny.<br/><br/>Pokud žádné spuštění úspěšné, stav řezu by mít připravené a jsou pokus o žádné další opakování.<br/><br/>opakování po delší době je možné použít situace, kdy závislé data dorazí na Nedeterministický časy nebo je v nestabilním stavu v rámci které zpracování dat dojde celém prostředí. V takových případech to, které opakování, jedna po druhé nemusí být úspěšná a díky tomu v intervalech čas má za následek požadované výstup.<br/><br/>Word varování: nenastavujte vysoké hodnoty pro opakování po delší době nebo longRetryInterval. Vyšší hodnoty obvykle implikují dalších systémových otázek. |
| longRetryInterval |Časový interval |00:00:00 |Prodleva mezi pokusy o opakování dlouho |

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
* V části **typeProperties** je jako typ zdroje určen **BlobSource** a jako typ jímky **SqlSink**. V [aktivity přesunu dat](#data-movement-activities) oddíl, klikněte na ukládání dat, kterou chcete použít jako zdroj nebo jímka Další informace o přesun dat do nebo z tohoto úložiště dat. 

Kompletní a podrobný postup vytváření tohoto kanálu, najdete v části [kurz: kopírování dat z úložiště objektů Blob do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

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
* `defines` Části slouží k určení nastavení běhového prostředí, které se předávají skriptu hive jako konfigurační hodnoty Hive (např `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Část **typeProperties** je u každé aktivity transformace odlišná. Další informace o vlastnostech typu podporovaných aktivity transformace, klikněte na aktivitu transformaci v [aktivit transformace dat](#data-transformation-activities) tabulky. 

Kompletní a podrobný postup vytváření tohoto kanálu, najdete v části [kurz: sestavit svůj první kanál pro zpracování dat pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="multiple-activities-in-a-pipeline"></a>Více aktivit v kanálu
Oba předchozí ukázkové kanály obsahovaly jenom jednu aktivitu. Kanál může obsahovat víc než jednu aktivitu.  

Pokud máte více aktivit v kanálu a výstup aktivity není vstup jinou aktivitu, může paralelně spustit aktivity, pokud připravení řezy vstupní data pro aktivity. 

Dvě aktivity můžete zřetězené tak, že výstupní datovou sadu jednu aktivitu jako vstupní datové sady z jiné aktivity. Druhá aktivita se spustí, pouze pokud první z nich úspěšně dokončena.

![Řetězení aktivity v kanálu stejné](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

V této ukázce kanálu má dvě aktivity: aktivity "activity1" a "activity2". Aktivity "activity1" trvá Dataset1 jako vstup a výstup vytváří Dataset2. Aktivity trvá Dataset2 jako vstup a výstup vytváří Dataset3. Od výstup aktivity "activity1" vstup "activity2", "activity2" spustí až po úspěšném dokončení aktivity a produkuje řez Dataset2 je (Dataset2). Pokud aktivity "activity1" z nějakého důvodu selže a nevytváří Dataset2 řez, Activity 2 se nespustí pro tento řez (například: 9: 00 do 10 AM). 

Můžete také zřetězené aktivity, které jsou v jiné kanály.

![Řetězení aktivity ve dvou kanálů](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

V této ukázce má Pipeline1 jenom jedna aktivita, která přebírá Dataset1 jako vstup a vytváří Dataset2 jako výstup. Pipeline2 má také jenom jedna aktivita, která přebírá Dataset2 jako vstup a Dataset3 jako výstup. 

Další informace najdete v tématu [plánování a provádění](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

## <a name="create-and-monitor-pipelines"></a>Vytvoření a monitorování kanálů
Kanály můžete vytvořit pomocí jedné z těchto nástrojů nebo sady SDK. 

- Průvodce kopírováním. 
- Azure Portal
- Visual Studio
- Azure PowerShell
- Šablona Azure Resource Manageru
- REST API
- .NET API

Najdete v následujících kurzech podrobné pokyny pro vytváření kanálů pomocí jedné z těchto nástrojů nebo sady SDK.
 
- [Vytvoření kanálu s aktivitou transformace dat](data-factory-build-your-first-pipeline.md)
- [Vytvoření kanálu s aktivitou přesun dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Jakmile kanál vytvořit nasazení, můžete spravovat a monitorovat kanály pomocí oken webu Azure portal nebo monitorování a správě aplikací. Najdete v následujících tématech podrobné pokyny. 

- [Monitorování a Správa kanálů pomocí oken webu Azure portal](data-factory-monitor-manage-pipelines.md).
- [Monitorování a Správa kanálů pomocí monitorování a Správa aplikací](data-factory-monitor-manage-app.md)


## <a name="onetime-pipeline"></a>Jednorázově kanálu
Můžete vytvořit a naplánovat kanál spouštět pravidelně (například: hodinové nebo denní) v rámci počáteční a koncový čas zadáte v definici kanálu. V tématu [plánování aktivit](#scheduling-and-execution) podrobnosti. Můžete také vytvořit kanál, který se spustí jenom jednou. Uděláte to tak, nastavíte **pipelineMode** vlastnost v definici kanálu pro **jednorázově** jak znázorňuje následující ukázka JSON. Výchozí hodnota pro tuto vlastnost je **naplánované**.

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

* **Spustit** a **end** nejsou zadány časy pro kanál.
* **Dostupnost** vstup a výstup je zadán datové sady (**frekvence** a **interval**), i když Data Factory nepoužívá hodnoty.  
* Zobrazení diagramu nezobrazuje jednorázové kanály. Toto chování je záměrné.
* Nelze aktualizovat, jednorázové kanály. Můžete klonovat jednorázového kanálu, přejmenujte ji, aktualizovat vlastnosti a nasadit ji vytvořte jiný.


## <a name="next-steps"></a>Další kroky
- Další informace o datových sadách najdete v tématu [vytvoření datových sad](data-factory-create-datasets.md) článku. 
- Další informace o tom, jak jsou naplánované kanálů a jsou prováděny najdete v tématu [plánování a provádění v Azure Data Factory](data-factory-scheduling-and-execution.md) článku. 
  

