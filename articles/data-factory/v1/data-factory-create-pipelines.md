---
title: Vytváření, plánování kanálů a řetězení aktivit v Data Factory
description: Naučte se vytvářet datový kanál v Azure Data Factory pro přesun a transformaci dat. Vytvořte pracovní postup založený na datech, který vytvoří připravené k použití informací.
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
ms.openlocfilehash: e3f9735a712a1302624b1ed88f462ca62138b883
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461505"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Kanály a aktivity v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-create-pipelines.md)
> * [Verze 2 (aktuální verze)](../concepts-pipelines-activities.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [kanály v v2](../concepts-pipelines-activities.md).

Tento článek vám pomůže pochopit kanály a aktivity ve službě Azure Data Factory a naučit se je používat k sestavení kompletních pracovních postupů založených na datech pro potřeby přesunů a zpracování dat.

> [!NOTE]
> V tomto článku se předpokládá, že jste prošli [úvodem do Azure Data Factory](data-factory-introduction.md). Pokud nemáte praktickou zkušenost s vytvářením datových továren, Projděte si [kurz transformace dat](data-factory-build-your-first-pipeline.md) nebo [kurz přesunu dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) , který vám pomůže lépe porozumět tomuto článku.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit, které společně provádějí úlohu. Aktivity v kanálu definují akce, které se mají s daty provádět. Aktivitu kopírování můžete například použít ke kopírování dat z databáze SQL Server do Azure Blob Storage. Pak použijete aktivitu Hive, která spouští skript Hive v clusteru Azure HDInsight, aby zpracovala/transformovala data z úložiště objektů blob za účelem vytvoření výstupních dat. Nakonec použijte druhou aktivitu kopírování ke zkopírování výstupních dat do analýzy Azure synapse, kde jsou vytvářena řešení pro vytváření sestav business intelligence (BI).

Každá aktivita může mít nula nebo více vstupních [datových sad](data-factory-create-datasets.md) a může generovat jednu nebo více výstupních [datových sad](data-factory-create-datasets.md). Následující diagram znázorňuje vztah mezi kanálem, aktivitou a datovou sadou v Data Factory:

![Vztah mezi kanálem, aktivitou a datovou sadou](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Kanál umožňuje spravovat aktivity jako sadu, a ne jednotlivě. Můžete třeba nasadit, naplánovat, pozastavit a obnovit kanál a nezávisle na aktivitách v kanálu.

Data Factory podporuje dva typy aktivit: aktivity přesunu dat a transformace dat. Každá aktivita může mít nula nebo více vstupních [datových sad](data-factory-create-datasets.md) a vytvoří jednu nebo více výstupních datových sad.

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
Pokud potřebujete přesunout data do nebo z úložiště dat, které aktivita kopírování nepodporuje, nebo transformovat data pomocí vlastní logiky, vytvořte **vlastní aktivitu rozhraní .NET**. Podrobnosti o vytvoření a používání vlastní aktivity najdete v tématu [Použití vlastních aktivit v kanálu služby Azure Data Factory](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Plánování kanálů
Kanál je aktivní jenom mezi časem **spuštění** a časem **ukončení** . Není prováděna před časem spuštění nebo po ukončení. Pokud je kanál pozastaven, nebude proveden bez ohledu na jeho počáteční a koncový čas. Pro spuštění kanálu by neměl být pozastaven. V tématu [plánování a spouštění](data-factory-scheduling-and-execution.md) můžete pochopit, jak plánování a provádění funguje v Azure Data Factory.

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

| Značka | Popis | Povinné |
| --- | --- | --- |
| name |Název kanálu. Určuje název, který představuje akci prováděnou kanálem. <br/><ul><li>Maximální počet znaků: 260.</li><li>Musí začínat číslem písmenem nebo podtržítkem ( \_ ).</li><li>Nejsou povoleny následující znaky: ".", "+", "?", "/", "<", ">", " \* ", "%", "&", ":", "" \\</li></ul> |Yes |
| description | Určuje text popisující, k čemu se kanál používá. |Yes |
| activities | Část **activities** může obsahovat definici jedné nebo více aktivit. Podrobnosti o elementu JSON aktivit naleznete v další části. | Yes |
| start | Počáteční datum a čas pro kanál. Musí být ve [formátu ISO](https://en.wikipedia.org/wiki/ISO_8601). Příklad: `2016-10-14T16:32:41Z`. <br/><br/>Je možné zadat místní čas, například čas EST. Tady je příklad: `2016-02-27T06:00:00-05:00` ", což je 6 am EST.<br/><br/>Vlastnosti Start a end společně určují aktivní období pro daný kanál. Výstupní řezy se vytvářejí jenom v tomto aktivním období. |No<br/><br/>Pokud zadáte hodnotu vlastnosti end, je nutné zadat hodnotu vlastnosti Start.<br/><br/>Časy zahájení a ukončení můžou být pro vytvoření kanálu prázdné. Chcete-li nastavit aktivní období pro spuštění kanálu, je nutné zadat obě hodnoty. Pokud při vytváření kanálu nezadáte počáteční a koncový čas, můžete je pomocí rutiny Set-AzDataFactoryPipelineActivePeriod nastavit později. |
| end | Koncové datum a čas kanálu. Je-li parametr zadán, musí být ve formátu ISO. Příklad: `2016-10-14T17:32:41Z` <br/><br/>Je možné zadat místní čas, například čas EST. Tady je příklad: `2016-02-27T06:00:00-05:00` , který je 6 am EST.<br/><br/>Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti end hodnotu 9999-09-09. <br/><br/> Kanál je aktivní jenom mezi časem spuštění a časem ukončení. Není prováděna před časem spuštění nebo po ukončení. Pokud je kanál pozastaven, nebude proveden bez ohledu na jeho počáteční a koncový čas. Pro spuštění kanálu by neměl být pozastaven. V tématu [plánování a spouštění](data-factory-scheduling-and-execution.md) můžete pochopit, jak plánování a provádění funguje v Azure Data Factory. |No <br/><br/>Pokud zadáte hodnotu vlastnosti Start, je nutné zadat hodnotu vlastnosti end.<br/><br/>Viz poznámky pro vlastnost **Start** . |
| isPaused | Pokud je nastavená hodnota true, kanál se nespustí. Je ve stavu pozastaveno. Výchozí hodnota = false. Tuto vlastnost můžete použít k povolení nebo zakázání kanálu. |No |
| pipelineMode | Metoda pro plánování běhu pro kanál. Povolené hodnoty jsou: naplánované (výchozí), jednorázová.<br/><br/>Možnost naplánované znamená, že se kanál spouští v zadaném časovém intervalu podle jeho aktivního období (počáteční a koncový čas). Jednorázová označuje, že se kanál spouští jenom jednou. Jednorázová kanály se po vytvoření nedají změnit nebo aktualizovat aktuálně. Podrobnosti o nastavení jednorázová najdete v tématu [jednorázová Pipeline](#onetime-pipeline) . |No |
| expirationTime | Doba po vytvoření, pro kterou je [jednorázový kanál](#onetime-pipeline) platný a měl by zůstat zřízen. Pokud nemá žádná aktivní, neúspěšná nebo nedokončená spuštění, kanál se po dosažení doby vypršení platnosti automaticky odstraní. Výchozí hodnota: `"expirationTime": "3.00:00:00"`|No |
| datové sady |Seznam datových sad, které budou použity aktivitami, které jsou definovány v kanálu. Tato vlastnost se dá použít k definování datových sad, které jsou specifické pro tento kanál a nejsou definované v rámci objektu pro vytváření dat. Datové sady definované v tomto kanálu můžou použít jenom tento kanál a nedá se sdílet. Podrobnosti najdete v tématu [vymezené datové sady](data-factory-create-datasets.md#scoped-datasets) . |No |

## <a name="activity-json"></a>Zápis JSON aktivity
Část **activities** může obsahovat definici jedné nebo více aktivit. Každá aktivita má následující strukturu nejvyšší úrovně:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
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

| Značka | Popis | Povinné |
| --- | --- | --- |
| name | Název aktivity. Určuje název, který představuje akci prováděnou danou aktivitou. <br/><ul><li>Maximální počet znaků: 260.</li><li>Musí začínat číslem písmenem nebo podtržítkem ( \_ ).</li><li>Nejsou povoleny následující znaky: ".", "+", "?", "/", "<", ">", "*", "%", "&" \\ , ":", ""</li></ul> |Yes |
| description | Text popisující, k čemu aktivita slouží. |Yes |
| typ | Typ aktivity. Různé typy aktivit najdete v částech aktivity [přesunu dat](#data-movement-activities) a [aktivity transformace dat](#data-transformation-activities) . |Yes |
| vztahují |Vstupní tabulky používané aktivitou<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Yes |
| činnosti |Výstupní tabulky používané aktivitou.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Yes |
| linkedServiceName |Název propojené služby používané aktivitou. <br/><br/>Aktivita může vyžadovat zadání propojené služby, která odkazuje na požadované výpočetní prostředí. |Ano pro aktivitu s aktivitou dávkového vyhodnocování aktivity služby HDInsight a Azure Machine Learning Studio (Classic) <br/><br/>Ne ve všech ostatních případech |
| typeProperties |Vlastnosti v části **typeProperties** závisí na typu aktivity. Pokud chcete zobrazit vlastnosti typu určité aktivity, klikněte na odkaz na aktivitu v předchozí části. | No |
| policy |Zásady, které ovlivňují chování aktivity za běhu. Pokud není zadaný, použijí se výchozí zásady. |No |
| scheduler | vlastnost Scheduler slouží k definování požadovaného plánování aktivity. Jeho podvlastnosti jsou stejné jako ty ve [vlastnosti Availability v datové sadě](data-factory-create-datasets.md#dataset-availability). |No |

### <a name="policies"></a>Zásady
Zásady ovlivňují chování aktivity za běhu, konkrétně při zpracování řezu tabulky. Podrobnosti jsou uvedeny v následující tabulce.

| Vlastnost | Povolené hodnoty | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| souběžnost |Integer <br/><br/>Maximální hodnota: 10 |1 |Počet souběžných spuštění aktivity.<br/><br/>Určuje počet paralelních spuštění aktivit, ke kterým může dojít v různých řezech. Například pokud aktivita potřebuje projít velkou sadou dostupných dat, větší hodnota souběžnosti zrychluje zpracování dat. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Určuje pořadí datových řezů, které jsou zpracovávány.<br/><br/>Například pokud máte 2 řezy (jedna se děje na 16:00 a druhý na 17:00), a obě jsou vyřízeny. Pokud nastavíte executionPriorityOrder na NewestFirst, řez se nejprve zpracuje v 5 odp. Podobně pokud nastavíte executionPriorityORder na OldestFIrst, bude zpracován řez ve 4 PM. |
| retry |Integer<br/><br/>Maximální hodnota může být 10. |0 |Počet opakovaných pokusů, než se zpracování dat pro řez označí jako selhání. Pokus o spuštění aktivity pro datový řez se opakuje do zadaného počtu opakování. Opakování se provede co nejdříve po selhání. |
| timeout |TimeSpan |00:00:00 |Časový limit aktivity Příklad: 00:10:00 (implikuje časový limit 10 minut)<br/><br/>Pokud hodnota není zadaná nebo je 0, časový limit je nekonečný.<br/><br/>Pokud doba zpracování dat v řezu překročí hodnotu časového limitu, je zrušena a systém se pokusí o opakované zpracování. Počet opakování závisí na vlastnosti opakování. Pokud dojde k vypršení časového limitu, je stav nastavený na vypršel časový limit. |
| způsobené |TimeSpan |00:00:00 |Určete zpoždění před spuštěním zpracování dat řezu.<br/><br/>Spuštění aktivity pro datový řez se spustí, jakmile je zpoždění po očekávanou dobu provádění.<br/><br/>Příklad: 00:10:00 (implikuje zpoždění po 10 minutách) |
| Má |Integer<br/><br/>Maximální hodnota: 10 |1 |Počet dlouhých pokusů o opakování před neúspěšným provedením řezu<br/><br/>Má pokusy jsou rozmístěny pomocí longRetryInterval. Takže pokud potřebujete zadat čas mezi opakovanými pokusy, použijte má. Pokud jsou zadány parametry Retry a má, každý má pokus obsahuje pokusy o opakování a maximální počet pokusů o opakování * má.<br/><br/>Například pokud máme v zásadách aktivity následující nastavení:<br/>Opakovat: 3<br/>Má: 2<br/>longRetryInterval: 01:00:00<br/><br/>Předpokládejme, že je možné provést pouze jeden řez (stav čeká na vyřízení) a spuštění aktivity pokaždé neproběhne úspěšně. Zpočátku by se mělo 3 po sobě jdoucích pokusů o spuštění. Po každém pokusu se stav řezu opakuje. Po prvním 3 pokusech se bude stav řezu má.<br/><br/>Po hodinu (tj. hodnotě longRetryInteval) by existovala další sada 3 po sobě jdoucích pokusů o spuštění. Po této operaci se stav řezu nezdařil a nebude proveden pokus o další opakování. Proto byly provedeny celkem 6 pokusů.<br/><br/>Pokud je jakékoli spuštění úspěšné, bude stav řezu připravený a nebude proveden pokus o další opakované pokusy.<br/><br/>Má se můžou použít v situacích, kdy se závislá data dostanou v nedeterministických časech, nebo v celkovém prostředí je vločka, pod kterou se zpracování dat děje. V takových případech s tím, že se opakuje jeden po jiné, to nemusí pomáhat a provádět po uplynutí časového intervalu požadovaného výstupu.<br/><br/>Upozornění: nenastavujte vysoké hodnoty pro má nebo longRetryInterval. Vyšší hodnoty obvykle implikují jiné systémové problémy. |
| longRetryInterval |TimeSpan |00:00:00 |Prodleva mezi dlouhými pokusy o opakování |

## <a name="sample-copy-pipeline"></a>Ukázkový kanál kopírování
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
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```

Je třeba počítat s následujícím:

* V části aktivit je jenom jedna aktivita, jejíž vlastnost **type** je nastavená na **Copy**.
* Vstup aktivity je nastavený na **InputDataset** a výstup aktivity je nastavený na **OutputDataset**. Informace o definicích datových sad ve formátu JSON najdete v článku [Datové sady](data-factory-create-datasets.md).
* V části **typeProperties** je jako typ zdroje určen **BlobSource** a jako typ jímky **SqlSink**. V části [aktivity přesunu dat](#data-movement-activities) klikněte na úložiště dat, které chcete použít jako zdroj nebo jímku, abyste se dozvěděli víc o přesouvání dat do a z tohoto úložiště dat.

Úplný návod k vytváření tohoto kanálu najdete v tématu [kurz: kopírování dat z BLOB Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

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
* `defines`Oddíl slouží k určení nastavení modulu runtime, která jsou předána skriptu podregistr jako hodnoty konfigurace podregistru (např `${hiveconf:inputtable}` `${hiveconf:partitionedtable}` .).

Část **typeProperties** je u každé aktivity transformace odlišná. Chcete-li získat informace o vlastnostech typu podporovaných pro aktivitu transformace, klikněte na aktivitu transformace v tabulce [aktivity transformace dat](#data-transformation-activities) .

Úplný návod k vytváření tohoto kanálu najdete v tématu [kurz: sestavení prvního kanálu pro zpracování dat pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="multiple-activities-in-a-pipeline"></a>Více aktivit v kanálu
Oba předchozí ukázkové kanály obsahovaly jenom jednu aktivitu. Kanál může obsahovat víc než jednu aktivitu.

Pokud máte v kanálu více aktivit a výstupem aktivity není vstup jiné aktivity, aktivity mohou běžet paralelně, pokud jsou vstupní datové řezy pro aktivity připravené.

Můžete zřetězit dvě aktivity pomocí výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Druhá aktivita se spustí jenom v případě, že se první úspěšně dokončí.

![Řetězení aktivit ve stejném kanálu](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

V této ukázce má kanál dvě aktivity: Activity1 a "Activity2". Activity1 přebírá DataSet1. jako vstup a vytváří výstupní Dataset2. Aktivita přijímá Dataset2 jako vstup a vytváří výstupní Dataset3. Vzhledem k tomu, že výstup Activity1 (Dataset2) je vstupem "Activity2", "Activity2" se spustí až po úspěšném dokončení aktivity a vytvoří řez Dataset2. Pokud se Activity1 z nějakého důvodu nepovede a nevytvoří Dataset2 řez, aktivita 2 se pro tento řez nespustí (například 9 AM až 10 DOP.).

Můžete také zřetězit aktivity, které jsou v různých kanálech.

![Řetězení aktivit ve dvou kanálech](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

V této ukázce má Pipeline1 pouze jednu aktivitu, která přijímá DataSet1. jako vstup a vytváří Dataset2 jako výstup. Pipeline2 má také pouze jednu aktivitu, která přijímá Dataset2 jako vstup a Dataset3 jako výstup.

Další informace najdete v tématu [plánování a provádění](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>Vytváření a monitorování kanálů
Kanály můžete vytvořit pomocí některého z těchto nástrojů nebo sad SDK.

- Průvodce kopírováním
- Visual Studio
- Azure PowerShell
- Šablona Azure Resource Manageru
- REST API
- .NET API

V následujících kurzech najdete podrobné pokyny k vytváření kanálů pomocí některého z těchto nástrojů nebo sad SDK.

- [Vytvoření kanálu s aktivitou transformace dat](data-factory-build-your-first-pipeline.md)
- [Vytvoření kanálu s aktivitou přesunu dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Po vytvoření nebo nasazení kanálu můžete své kanály spravovat a monitorovat pomocí oken Azure Portal nebo monitorovat a spravovat aplikace. Podrobné pokyny najdete v následujících tématech.

- [Monitorujte a spravujte kanály pomocí Azure Portalch](data-factory-monitor-manage-pipelines.md)oken.
- [Monitorování a Správa kanálů pomocí monitorování a správy aplikací](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Kanál jednorázová
Můžete vytvořit a naplánovat pravidelné spouštění kanálu (například každou hodinu nebo každý den) v rámci počátečního a koncového času, který zadáte v definici kanálu. Podrobnosti najdete v tématu plánování aktivit. Můžete také vytvořit kanál, který bude spuštěn pouze jednou. K tomu je třeba nastavit vlastnost **pipelineMode** v definici kanálu na **jednorázová** , jak je znázorněno v následující ukázce JSON. Výchozí hodnota této vlastnosti je **plánována**.

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
                ],
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

Všimněte si, že:

* **Počáteční** a **koncový** časy pro kanál nejsou zadány.
* Je zadaná **dostupnost** vstupních a výstupních datových sad (**frekvence** a **interval**), i když Data Factory nepoužívá hodnoty.
* Zobrazení diagramu nezobrazuje jednorázové kanály. Toto chování je záměrné.
* Jednorázové kanály nelze aktualizovat. Můžete naklonovat jednorázový kanál, přejmenovat ho, aktualizovat vlastnosti a nasazovat ho, aby se vytvořil nějaký jiný.

## <a name="next-steps"></a>Další kroky
- Další informace o datových sadách naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) .
- Další informace o tom, jak se naplánují a spouštějí kanály, najdete [v tématu plánování a spouštění v Azure Data Factory](data-factory-scheduling-and-execution.md) článku.
