---
title: Vytvořit nebo naplánovat kanály, řetězové aktivity v továrně dat
description: Naučte se vytvořit datový kanál v Azure Data Factory pro přesun a transformaci dat. Vytvořte pracovní postup řízený daty, který vytvoří informace připravené k použití.
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
ms.openlocfilehash: f93bea240ee3f139c9be84199d116f9f3f231261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281519"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Kanály a aktivity v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-create-pipelines.md)
> * [Verze 2 (aktuální verze)](../concepts-pipelines-activities.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Kanály ve Verzi 2](../concepts-pipelines-activities.md).

Tento článek vám pomůže pochopit kanály a aktivity ve službě Azure Data Factory a naučit se je používat k sestavení kompletních pracovních postupů založených na datech pro potřeby přesunů a zpracování dat.

> [!NOTE]
> Tento článek předpokládá, že jste prošli [úvod do Azure Data Factory](data-factory-introduction.md). Pokud nemáte praktické zkušenosti s vytvářením datových továren, procházení [kurzu transformace dat](data-factory-build-your-first-pipeline.md) nebo kurz pro přesun [dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vám pomůže lépe porozumět tomuto článku.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit, které dohromady provádějí určitou úlohu. Aktivity v kanálu definují akce, které se mají provést s vašimi daty. Například můžete použít aktivitu kopírování ke zkopírování dat z místního SQL Serveru do služby Azure Blob Storage. Pak použijete aktivitu Hive, která spouští skript Hive v clusteru Azure HDInsight, aby zpracovala/transformovala data z úložiště objektů blob za účelem vytvoření výstupních dat. Nakonec použijte druhou aktivitu kopírování, která zkopíruje výstupní data do služby Azure SQL Data Warehouse, které jsou postavena řešení tvorby sestav business intelligence (BI).

Každá aktivita může mít nula nebo více vstupních [datových sad](data-factory-create-datasets.md) a může generovat jednu nebo více výstupních [datových sad](data-factory-create-datasets.md). Následující diagram znázorňuje vztah mezi kanálem, aktivitou a datovou sadou v Data Factory:

![Vztah mezi kanálem, aktivitou a datovou sadou](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Kanál umožňuje spravovat aktivity jako sadu namísto každé z nich jednotlivě. Můžete například nasadit, naplánovat, pozastavit a obnovit kanál, namísto samostatného zpracování aktivit v kanálu.

Data Factory podporuje dva typy aktivit: aktivity přesunu dat a transformace dat. Každá aktivita může mít nula nebo více [vstupních datových sad](data-factory-create-datasets.md) a vytvořit jednu nebo více výstupních datových sad.

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
Pokud potřebujete přesunout data do nebo z úložiště dat, které aktivita kopírování nepodporuje, nebo transformovat data pomocí vlastní logiky, vytvořte **vlastní aktivitu .NET**. Podrobnosti o vytvoření a používání vlastní aktivity najdete v tématu [Použití vlastních aktivit v kanálu služby Azure Data Factory](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Plánování kanálů
Kanál je aktivní pouze mezi jeho **čas zahájení** a **čas ukončení.** Není proveden před časem zahájení nebo po koncovém čase. Pokud je kanál pozastaven, neprovede se bez ohledu na jeho počáteční a koncový čas. Pro spuštění kanálu by neměl být pozastaven. Najdete [v tématu Plánování a provádění,](data-factory-scheduling-and-execution.md) kde najdete informace o tom, jak plánování a spouštění v Azure Data Factory funguje.

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
| jméno |Název kanálu. Určuje název, který představuje akci prováděnou kanálem. <br/><ul><li>Maximální počet znaků: 260.</li><li>Musí začínat písmenem nebo\_podtržítkem ( ).</li><li>Následující znaky nejsou povoleny: ".", "+", "?", "/",\*"<",">"," "%","&","","","","","","","","",""\\"</li></ul> |Ano |
| description | Určuje text popisující, k čemu se kanál používá. |Ano |
| activities | Část **activities** může obsahovat definici jedné nebo více aktivit. Podrobnosti o aktivitách prvku JSON naleznete v další části. | Ano |
| start | Počáteční datum a čas pro kanál. Musí být ve [formátu ISO](https://en.wikipedia.org/wiki/ISO_8601). Například: `2016-10-14T16:32:41Z`. <br/><br/>Je možné zadat místní čas, například čas EST. Zde je příklad: `2016-02-27T06:00:00-05:00`", což je 6 AM EST.<br/><br/>Počáteční a koncové vlastnosti společně určují aktivní období pro kanál. Výstupní řezy jsou vyráběny pouze v tomto aktivním období. |Ne<br/><br/>Pokud zadáte hodnotu pro koncovou vlastnost, musíte zadat hodnotu pro vlastnost start.<br/><br/>Počáteční a koncový čas může být prázdný a vytvořit tak kanál. Je nutné zadat obě hodnoty pro nastavení aktivního období pro spuštění kanálu. Pokud při vytváření kanálu nezadáte počáteční a koncový čas, můžete je později nastavit pomocí rutiny Set-AzDataFactoryPipelineActivePeriod. |
| end | Koncové datum a čas pro kanál. Pokud je zadán, musí být ve formátu ISO. Příklad: `2016-10-14T17:32:41Z` <br/><br/>Je možné zadat místní čas, například čas EST. Zde je příklad: `2016-02-27T06:00:00-05:00`, což je 6 AM EST.<br/><br/>Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti end hodnotu 9999-09-09. <br/><br/> Kanál je aktivní pouze mezi jeho čas zahájení a čas ukončení. Není proveden před časem zahájení nebo po koncovém čase. Pokud je kanál pozastaven, neprovede se bez ohledu na jeho počáteční a koncový čas. Pro spuštění kanálu by neměl být pozastaven. Najdete [v tématu Plánování a provádění,](data-factory-scheduling-and-execution.md) kde najdete informace o tom, jak plánování a spouštění v Azure Data Factory funguje. |Ne <br/><br/>Pokud zadáte hodnotu vlastnosti start, musíte zadat hodnotu pro koncovou vlastnost.<br/><br/>Viz poznámky pro **vlastnost start.** |
| jePaused | Pokud je nastavena na hodnotu true, kanál nelze spustit. Je to v pozastaveném stavu. Výchozí hodnota = false. Tuto vlastnost můžete použít k povolení nebo zakázání kanálu. |Ne |
| pipelineMode | Metoda plánování běží pro kanál. Povolené hodnoty jsou: naplánované (výchozí), jednorázové.<br/><br/>"Naplánované" označuje, že potrubí běží v zadaném časovém intervalu podle aktivního období (počáteční a koncový čas). "Onetime" označuje, že potrubí běží pouze jednou. Jednorázové kanály po vytvoření nelze aktuálně změnit nebo aktualizovat. Podrobnosti o jednorázovém nastavení najdete v [tématu Onetime pipeline.](#onetime-pipeline) |Ne |
| expiraceČas | Doba po vytvoření, pro kterou je jednorázový [kanál](#onetime-pipeline) platný a měla by zůstat zřízena. Pokud nemá žádné aktivní, neúspěšné nebo čekající spuštění, kanál se automaticky odstraní, jakmile dosáhne doby vypršení platnosti. Výchozí hodnota:`"expirationTime": "3.00:00:00"`|Ne |
| datové sady |Seznam datových sad, které mají být použity aktivitami definovanými v kanálu. Tuto vlastnost lze použít k definování datových sad, které jsou specifické pro tento kanál a nejsou definovány v rámci datové továrny. Datové sady definované v rámci tohoto kanálu lze použít pouze tento kanál a nelze je sdílet. Podrobnosti najdete [v části Datové sady s oborem.](data-factory-create-datasets.md#scoped-datasets) |Ne |

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

| Značka | Popis | Požaduje se |
| --- | --- | --- |
| jméno | Název aktivity. Určuje název, který představuje akci prováděnou danou aktivitou. <br/><ul><li>Maximální počet znaků: 260.</li><li>Musí začínat písmenem nebo\_podtržítkem ( ).</li><li>Následující znaky nejsou povoleny: ".", "+", "?", "/", "<",">","*","%","&","","","","","&","","",""""""""""\\</li></ul> |Ano |
| description | Text popisující, k čemu aktivita slouží. |Ano |
| type | Typ aktivity. Různé typy aktivit naleznete v částech [Aktivity přesunu dat](#data-movement-activities) a [Aktivity transformace dat.](#data-transformation-activities) |Ano |
| Vstupy |Vstupní tabulky používané aktivitou<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Ano |
| Výstupy |Výstupní tabulky používané aktivitou.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Ano |
| linkedServiceName |Název propojené služby používané aktivitou. <br/><br/>Aktivita může vyžadovat zadání propojené služby, která odkazuje na požadované výpočetní prostředí. |Ano pro aktivity HDInsight a aktivity dávkového bodování Azure Machine Learning <br/><br/>Ne ve všech ostatních případech |
| typeProperties |Vlastnosti v části **typeProperties** závisí na typu aktivity. Pokud chcete zobrazit vlastnosti typu určité aktivity, klikněte na odkaz na aktivitu v předchozí části. | Ne |
| policy |Zásady, které ovlivňují chování aktivity za běhu. Pokud není zadán, jsou použity výchozí zásady. |Ne |
| scheduler | Vlastnost "plánovač" se používá k definování požadovaného plánování aktivity. Jeho podvlastnosti jsou stejné jako ve [vlastnosti dostupnosti v datové sadě](data-factory-create-datasets.md#dataset-availability). |Ne |

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

## <a name="sample-copy-pipeline"></a>Ukázkový kanál kopírování
V následujícím ukázkovém kanálu je v části **activities** jedna aktivita typu **Kopírování**. V této ukázce [aktivita kopírování](data-factory-data-movement-activities.md) kopíruje data ze služby Azure Blob Storage do databáze Azure SQL.

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
* V části **typeProperties** je jako typ zdroje určen **BlobSource** a jako typ jímky **SqlSink**. V části [Aktivity přesunu dat](#data-movement-activities) klikněte na úložiště dat, které chcete použít jako zdroj nebo jímku, abyste se dozvěděli další informace o přesouvání dat do nebo z tohoto úložiště dat.

Úplný návod k vytvoření tohoto kanálu najdete [v tématu Výuka: Kopírování dat z úložiště objektů Blob do databáze SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

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
* Oddíl `defines` slouží k určení nastavení za běhu, které jsou předány do skriptu `${hiveconf:inputtable}`podregistru jako hodnoty konfigurace Hive (např. ). `${hiveconf:partitionedtable}`

Část **typeProperties** je u každé aktivity transformace odlišná. Chcete-li se dozvědět o vlastnostech typu podporovaných pro aktivitu transformace, klikněte na aktivitu transformace v tabulce [Aktivity transformace dat.](#data-transformation-activities)

Úplný návod k vytvoření tohoto kanálu najdete [v tématu Výuka: Vytvoření prvního kanálu pro zpracování dat pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="multiple-activities-in-a-pipeline"></a>Více aktivit v kanálu
Oba předchozí ukázkové kanály obsahovaly jenom jednu aktivitu. Kanál může obsahovat víc než jednu aktivitu.

Pokud máte více aktivit v kanálu a výstup aktivity není vstup emitovat jinou aktivitu, aktivity může běžet paralelně, pokud jsou připraveny řezy vstupních dat pro aktivity.

Můžete zřetězit dvě aktivity tím, že výstupní datovou sadu jedné aktivity jako vstupní datovou sadu druhé aktivity. Druhá aktivita se provede pouze po úspěšném dokončení první aktivity.

![Řetězení aktivit ve stejném potrubí](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

V této ukázce má kanál dvě aktivity: Activity1 a Activity2. Activity1 trvá Dataset1 jako vstup a vytváří výstup Dataset2. Aktivita přebírá Dataset2 jako vstup a vytváří výstupní Dataset3. Vzhledem k tomu, že výstup Activity1 (Dataset2) je vstup Activity2, Activity2 spustí pouze po Aktivita úspěšně dokončí a vytvoří dataset2 řez. Pokud Activity1 selže z nějakého důvodu a nevytváří dataset2 řez, aktivita 2 nespustí pro tento řez (například: 9:00 až 10:00).

Můžete také řetězové aktivity, které jsou v různých kanálech.

![Řetězení ve dvou potrubích](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

V této ukázce Pipeline1 má pouze jednu aktivitu, která přebírá Dataset1 jako vstup a vytváří Dataset2 jako výstup. Pipeline2 má také pouze jednu aktivitu, která přebírá Dataset2 jako vstup a Dataset3 jako výstup.

Další informace naleznete v [tématu plánování a provádění](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>Vytváření a monitorování kanálů
Kanály můžete vytvořit pomocí jednoho z těchto nástrojů nebo sad SDK.

- Průvodce kopírováním
- Visual Studio
- Azure PowerShell
- Šablona Azure Resource Manageru
- REST API
- .NET API

Podrobné pokyny pro vytváření kanálů pomocí jednoho z těchto nástrojů nebo sad SDK naleznete v následujících kurzech.

- [Vytvoření kanálu s aktivitou transformace dat](data-factory-build-your-first-pipeline.md)
- [Vytvoření kanálu s aktivitou přesunu dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Po vytvoření nebo nasazení kanálu můžete spravovat a monitorovat vaše kanály pomocí blades portálu Azure nebo monitorovat a spravovat aplikaci. Podrobné pokyny naleznete v následujících tématech.

- [Monitorujte a spravujte kanály pomocí blades portálu Azure](data-factory-monitor-manage-pipelines.md).
- [Sledování a správa kanálů pomocí monitoru a správy aplikací](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Jednorázový ropovod
Můžete vytvořit a naplánovat pravidelné spouštění kanálu (například: každou hodinu nebo denně) v rámci počátečního a koncového času, který zadáte v definici kanálu. Podrobnosti najdete v tématu Plánování aktivit. Můžete také vytvořit kanál, který běží pouze jednou. Chcete-li tak učinit, nastavte **vlastnost pipelineMode** v definici kanálu **na jeden čas,** jak je znázorněno v následujícím vzorku JSON. Výchozí hodnota pro tuto vlastnost je **naplánována**.

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

Je třeba počítat s následujícím:

* **Počáteční** a **koncový** čas kanálu nejsou určeny.
* **Dostupnost** vstupních a výstupních datových sad je zadána (**frekvence** a **interval**), i když Data Factory nepoužívá hodnoty.
* Zobrazení diagramu nezobrazuje jednorázové kanály. Toto chování je záměrné.
* Jednorázové kanály nelze aktualizovat. Můžete klonovat jednorázový kanál, přejmenovat jej, aktualizovat vlastnosti a nasadit jej a vytvořit další.

## <a name="next-steps"></a>Další kroky
- Další informace o datových sadách najdete v tématu Vytvoření článku [datových sad.](data-factory-create-datasets.md)
- Další informace o tom, jak se plánují a spouštějí kanály, najdete [v článku Plánování a spouštění v Azure Data Factory.](data-factory-scheduling-and-execution.md)
