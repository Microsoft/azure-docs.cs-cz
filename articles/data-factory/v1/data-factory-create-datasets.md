---
title: Vytváření datových sad ve Službě Azure Data Factory
description: Zjistěte, jak vytvořit datové sady v Azure Data Factory, s příklady, které používají vlastnosti, jako je posun a anchorDateTime.
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
ms.openlocfilehash: 18a5e11d2341fb020fc442d2f9ce7c1d44de9d0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260433"
---
# <a name="datasets-in-azure-data-factory"></a>Datové sady v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-create-datasets.md)
> * [Verze 2 (aktuální verze)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [téma Datové sady ve verzi 2](../concepts-datasets-linked-services.md).

Tento článek popisuje, co jsou datové sady, jak jsou definovány ve formátu JSON a jak se používají v kanálech Azure Data Factory. Poskytuje podrobnosti o jednotlivých oddílech (například struktura, dostupnost a zásady) v definici datové sady JSON. Článek také obsahuje příklady pro použití **vlastností posunu**, **anchorDateTime**a **stylu** v definici JSON datové sady.

> [!NOTE]
> Pokud jste s Toutovou službou pro data tezi te, [přečtěte si přehled v tématu Úvod do Azure Data Factory.](data-factory-introduction.md) Pokud nemáte praktické zkušenosti s vytvářením datových továren, můžete získat lepší porozumění čtením [kurzu transformace dat](data-factory-build-your-first-pipeline.md) a kurzu pohybu [dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. **Kanál** je logické seskupení **aktivit,** které společně provádějí úkol. Aktivity v kanálu definují akce, které se mají provést s vašimi daty. Můžete například použít aktivitu kopírování ke kopírování dat z místního SQL Serveru do úložiště objektů blob Azure. Potom můžete použít aktivitu Hive, která spouští skript Hive v clusteru Azure HDInsight ke zpracování dat z úložiště objektů Blob k vytvoření výstupních dat. Nakonec můžete použít druhou aktivitu kopírování ke kopírování výstupních dat do Azure SQL Data Warehouse, na příkladu, které jsou vytvořeny řešení pro vytváření sestav business intelligence (BI). Další informace o kanálech a aktivitách najdete [v tématu Kanály a aktivity v Azure Data Factory](data-factory-create-pipelines.md).

Aktivita může trvat nula nebo více **vstupních datových sad**a vytvořit jednu nebo více výstupních datových sad. Vstupní datová sada představuje vstup pro aktivitu v kanálu a výstupní datová sada představuje výstup pro aktivitu. Datové sady identifikují data v rámci různých úložišť dat, jako jsou tabulky, soubory, složky a dokumenty. Například datová sada Azure Blob určuje kontejner objektů blob a složku v úložišti objektů Blob, ze kterého by měl kanál číst data.

Před vytvořením datové sady vytvořte **propojenou službu,** která propojí úložiště dat s továrnou na data. Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Datové sady identifikují data v rámci propojených úložišť dat, jako jsou tabulky SQL, soubory, složky a dokumenty. Například propojené služby Azure Storage propojuje účet úložiště do datové továrny. Datová sada Azure Blob představuje kontejner objektů blob a složku, která obsahuje vstupní objekty BLOB, které mají být zpracovány.

Zde je ukázkový scénář. Chcete-li zkopírovat data z úložiště objektů Blob do databáze SQL, vytvořte dvě propojené služby: Azure Storage a Azure SQL Database. Potom vytvořte dvě datové sady: datovou sadu Azure Blob (která odkazuje na propojenou službu Azure Storage) a datovou sadu Azure SQL Table (která odkazuje na propojenou službu Azure SQL Database). Služby propojené s Azure Storage a Azure SQL Database obsahují připojovací řetězce, které Data Factory používá za běhu k připojení k úložišti Azure Storage a Azure SQL Database. Datová sada Objektů blob Azure určuje kontejner objektů blob a složku objektů blob, která obsahuje vstupní objekty BLOB v úložišti objektů Blob. Datová sada Tabulka Azure SQL určuje tabulku SQL v databázi SQL, do které se mají data kopírovat.

Následující diagram znázorňuje vztahy mezi kanálem, aktivitou, datovou sadou a propojenou službou v datové továrně:

![Vztah mezi kanálem, aktivitou, datovou sadou, propojenými službami](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Datová sada JSON
Datová sada v Datové továrně je definována ve formátu JSON takto:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
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
| jméno |Název datové sady. Viz [Azure Data Factory – pravidla pojmenování](data-factory-naming-rules.md) pro pravidla pojmenování. |Ano |Není k dispozici |
| type |Typ datové sady. Zadejte jeden z typů podporovaných data factory (například: AzureBlob, AzureSqlTable). <br/><br/>Podrobnosti naleznete v tématu [Typ datové sady](#Type). |Ano |Není k dispozici |
| – struktura |Schéma datové sady.<br/><br/>Podrobnosti naleznete v tématu [Struktura datové sady](#Structure). |Ne |Není k dispozici |
| typeProperties | Vlastnosti typu se liší pro každý typ (například: Azure Blob, Tabulka Azure SQL). Podrobnosti o podporovaných typech a jejich vlastnostech naleznete v [tématu Typ datové sady](#Type). |Ano |Není k dispozici |
| external | Logický příznak k určení, zda je datová sada explicitně vytvořena kanálem datové továrny nebo ne. Pokud vstupní datová sada pro aktivitu není vytvořena aktuálním kanálem, nastavte tento příznak na hodnotu true. Nastavte tento příznak na hodnotu true pro vstupní datovou sadu první aktivity v kanálu.  |Ne |false (nepravda) |
| dostupnosti | Definuje okno zpracování (například každou hodinu nebo denně) nebo model krájení pro výrobu datové sady. Každá jednotka dat spotřebovaných a vytvořených spuštěním aktivity se nazývá řez dat. Pokud je dostupnost výstupní datové sady nastavena na denní (frekvence - Den, interval - 1), je řez produkován denně. <br/><br/>Podrobnosti naleznete v tématu Dostupnost datové sady. <br/><br/>Podrobnosti o modelu krájení datové sady naleznete v článku [Plánování a spuštění.](data-factory-scheduling-and-execution.md) |Ano |Není k dispozici |
| policy |Definuje kritéria nebo podmínku, kterou musí řezy datové sady splňovat. <br/><br/>Podrobnosti naleznete v části [Zásady datové sady.](#Policy) |Ne |Není k dispozici |

## <a name="dataset-example"></a>Příklad datové sady
V následujícím příkladu představuje datová sada tabulku s názvem **MyTable** v databázi SQL.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Je třeba počítat s následujícím:

* **typ** je nastavena na AzureSqlTable.
* vlastnost **tableName** type (specifická pro typ AzureSqlTable) je nastavena na hodnotu MyTable.
* **linkedServiceName** odkazuje na propojenou službu typu AzureSqlDatabase, která je definována v dalším fragmentu JSON.
* **dostupnost je** nastavena na Den a **interval** je nastaven na 1. To znamená, že řez datové sady se vytváří denně.

**AzureSqlLinkedService** je definována takto:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

V předchozím fragmentu JSON:

* **typ** je nastavena na AzureSqlDatabase.
* Vlastnost **connectionString** type určuje informace pro připojení k databázi SQL.

Jak můžete vidět, propojená služba definuje, jak se připojit k databázi SQL. Datová sada definuje, jaká tabulka se používá jako vstup a výstup pro aktivitu v kanálu.

> [!IMPORTANT]
> Pokud není datová sada vytvářena kanálem, měla by být označena jako **externí**. Toto nastavení obecně platí pro vstupy první aktivity v kanálu.

## <a name="dataset-type"></a><a name="Type"></a>Typ datové sady
Typ datové sady závisí na úložišti dat, které používáte. Seznam úložišť dat podporovaných datovou touzi naleznete v následující tabulce. Kliknutím na úložiště dat se dozvíte, jak vytvořit propojenou službu a datovou sadu pro toto úložiště dat.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Úložiště dat s * může být místní nebo na infrastruktuře Azure jako služba (IaaS). Tato úložiště dat vyžadují instalaci [brány pro správu dat](data-factory-data-management-gateway.md).

V příkladu v předchozí části je typ datové sady nastaven na **AzureSqlTable**. Podobně pro datovou sadu Azure Blob je typ datové sady nastaven na **AzureBlob**, jak je znázorněno v následujícím JSON:

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

## <a name="dataset-structure"></a><a name="Structure"></a>Struktura datové sady
Část **struktury** je nepovinná. Definuje schéma datové sady obsahující kolekci názvů a datových typů sloupců. Oddíl struktura slouží k zadání informací o typu, které se používají k převodu typů a mapování sloupců ze zdroje do cíle. V následujícím příkladu má datová `slicetimestamp`sada `projectname`tři `pageviews`sloupce: , a . Jsou typu String, String a Decimal.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Každý sloupec ve struktuře obsahuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název sloupce. |Ano |
| type |Datový typ sloupce.  |Ne |
| jazyková verze |. Jazyková verze založená na NET, která se `Datetime` `Datetimeoffset`má použít, pokud je typ typu .NET: nebo . Výchozí formát je `en-us`. |Ne |
| formát |Formátovací řetězec, který se použije v `Datetime` případě, že se jedná o typ .NET: nebo `Datetimeoffset`. |Ne |

Následující pokyny vám pomohou určit, kdy zahrnout informace o struktuře a co zahrnout do části **struktura.**

* **U strukturovaných zdrojů dat**zadejte oddíl struktury pouze v případě, že chcete, aby se sloupce zdroje mapy utajily a jejich názvy nejsou stejné. Tento druh strukturovaného zdroje dat ukládá schéma dat a informace o typu spolu se samotnými daty. Příklady strukturovaných zdrojů dat zahrnují SQL Server, Oracle a Azure tabulka.
  
    Vzhledem k tomu, že informace o typu jsou již k dispozici pro strukturované zdroje dat, neměli byste při zahrnutí oddílu struktury zahrnout informace o typu.
* **Pro schéma na zdroje dat pro čtení (konkrétně úložiště objektů Blob)** můžete ukládat data bez ukládání schématu nebo zadejte informace s daty. Pro tyto typy zdrojů dat zahrnout strukturu, pokud chcete mapovat zdrojové sloupce do jímací sloupce. Zahrnout také strukturu, když datová sada je vstup pro aktivitu kopírování a datové typy zdrojové datové sady by měly být převedeny na nativní typy pro jímky.
    
    Data Factory podporuje následující hodnoty pro poskytování informací o typu ve struktuře: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Logická, String, Guid, Datetime, Datetimeoffset a Timespan**. Tyto hodnoty jsou kompatibilní se specifikací COMMON Language Specification (CLS), . Hodnoty typu založené na NET.

Data Factory automaticky provádí převody typů při přesunu dat ze zdrojového úložiště dat do úložiště dat jímky.

## <a name="dataset-availability"></a>Dostupnost datové sady
Část **dostupnosti** v datové sadě definuje okno zpracování (například hodinové, denní nebo týdenní) pro datovou sadu. Další informace o oknech aktivit naleznete v [tématu Plánování a provádění](data-factory-scheduling-and-execution.md).

Následující část dostupnosti určuje, že výstupní datová sada je vyráběna každou hodinu nebo je vstupní datová sada k dispozici každou hodinu:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Pokud má potrubí následující počáteční a koncový čas:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Výstupní datová sada se vytváří každou hodinu v rámci počátečního a koncového času kanálu. Proto existuje pět řezy datové sady vytvořené tímto kanálem, jeden pro každé okno aktivity (12:00 - 1:00, 1:00 - 2:00, 2:00 - 3:00, 3:00 - 4:00, 4:00 - 5:00).

Následující tabulka popisuje vlastnosti, které můžete použít v části dostupnosti:

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| frequency |Určuje časovou jednotku pro výrobu řezů datové sady.<br/><br/><b>Podporovaná frekvence</b>: Minuta, Hodina, Den, Týden, Měsíc |Ano |Není k dispozici |
| interval |Určuje násobitel pro frekvenci.<br/><br/>"Interval frekvence x" určuje, jak často se řez vyrábí. Pokud například potřebujete, aby byla datová sada rozdělena na základě hodin, nastavte <b>frekvenci</b> na <b>Hodina</b>a <b>interval</b> na <b>1</b>.<br/><br/>Všimněte si, že pokud zadáte **frekvenci** jako **Minute**, měli byste nastavit interval na nejméně 15. |Ano |Není k dispozici |
|  – styl |Určuje, zda má být řez vytvořen na začátku nebo na konci intervalu.<ul><li>Začátekintervalu</li><li>EndOfInterval</li></ul>Pokud je **frekvence** nastavena na **Month**a **styl** je nastaven na **EndOfInterval**, řez se vyrobí poslední den v měsíci. Pokud je **styl** nastaven na **StartOfInterval**, řez se vyrobí první den v měsíci.<br/><br/>Pokud je **frekvence** nastavena na **možnost Den**a **styl** je nastaven na **EndOfInterval**, řez se vyrobí v poslední hodině dne.<br/><br/>Pokud je **frekvence** nastavena na **hodinu**a **styl** je nastaven na **EndOfInterval**, řez se vyrobí na konci hodiny. Například pro řez pro období 1 PM - 14 HODIN se řez vytvoří ve 14:00. |Ne |EndOfInterval |
| anchorDateTime |Definuje absolutní časovou polohu, kterou plánovač používá k výpočtu hranic datových sad. <br/><br/>Všimněte si, že pokud tato vlastnost má části data, které jsou podrobnější než zadaná frekvence, podrobnější části jsou ignorovány. Například pokud **interval** je **hodinová** (frekvence: hodina a interval: 1) a **anchorDateTime** obsahuje **minuty a sekundy**, pak minuty a sekundy části **anchorDateTime** jsou ignorovány. |Ne |01/01/0001 |
| posun |Časový posun, o které jsou posunuty počáteční a koncové všechny řezy datové sady. <br/><br/>Všimněte si, že pokud jsou zadány **anchorDateTime** a **posun,** výsledkem je kombinovaný posun. |Ne |Není k dispozici |

### <a name="offset-example"></a>příklad odsazení
Ve výchozím nastavení`"frequency": "Day", "interval": 1`začínají denní řezy ( ) ve 12:00 (půlnoci) koordinovaný světový čas (UTC). Pokud chcete, aby čas zahájení byl čas 6 AM UTC místo, nastavte posun, jak je znázorněno v následujícím fragmentu:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime příklad
V následujícím příkladu je datová sada vytvářena jednou za 23 hodin. První řez začíná v čase určeném **anchorDateTime** `2017-04-19T08:00:00` , který je nastaven na (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>příklad posunu/stylu
Následující datová sada je měsíční a je vyráběna třetí měsíc v 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="dataset-policy"></a><a name="Policy"></a>Zásady datové sady
Oddíl **zásad** v definici datové sady definuje kritéria nebo podmínku, kterou musí řezy datové sady splňovat.

### <a name="validation-policies"></a>Ověřovací zásady
| Název zásad | Popis | Použito na | Požaduje se | Výchozí |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Ověří, že data v **úložišti objektů blob Azure** splňují požadavky na minimální velikost (v megabajtech). |Azure Blob Storage |Ne |Není k dispozici |
| minimumRows |Ověří, zda data v **databázi Azure SQL** nebo **v tabulce Azure** obsahují minimální počet řádků. |<ul><li>Databáze Azure SQL</li><li>Tabulka Azure</li></ul> |Ne |Není k dispozici |

#### <a name="examples"></a>Příklady
**minimumVelikostMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumŘádky:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Externí datové sady
Externí datové sady jsou ty, které nejsou vytvářeny spuštěným kanálem v datové továrně. Pokud je datová sada označena jako **externí**, může být definována zásada **ExternalData,** která ovlivní chování dostupnosti řezu datové sady.

Pokud datová sada je vytvářena Data Factory, by měla být označena jako **externí**. Toto nastavení obecně platí pro vstupy první aktivity v kanálu, pokud aktivita nebo řetězení potrubí se používá.

| Name (Název) | Popis | Požaduje se | Výchozí hodnota |
| --- | --- | --- | --- |
| dataDelay |Čas zpoždění kontroly dostupnosti externích dat pro daný řez. Pomocí tohoto nastavení můžete například zpozdit hodinovou kontrolu.<br/><br/>Nastavení platí pouze pro aktuální čas. Například pokud je 1:00 PM právě teď a tato hodnota je 10 minut, ověření začíná 1:10 PM.<br/><br/>Všimněte si, že toto nastavení nemá vliv na řezy v minulosti. Řezy s daty čas +  **ukončení řezuZpoždění****dataDelay** < **Nyní** jsou zpracovány bez zpoždění.<br/><br/>Časy větší než 23:59 hodin by `day.hours:minutes:seconds` měly být určeny pomocí formátu. Chcete-li například zadat 24 hodin, nepoužívejte 24:00:00. Místo toho použijte 1.00:00:00. Pokud používáte 24:00:00, je považován za 24 dní (24:00:00). Pro 1 den a 4 hodiny zadejte 1:04:00:00. |Ne |0 |
| retryInterval |Čekací doba mezi selháním a dalším pokusem. Toto nastavení platí pro aktuální čas. Pokud předchozí pokus se nezdařilo, další pokus je po období **retryInterval.** <br/><br/>Pokud je to 1:00 pm právě teď, začneme první pokus. Pokud je doba trvání dokončení první kontroly ověření 1 minuta a operace se nezdařila, další opakování je v 1:00 + 1min (doba trvání) + 1min (interval opakování) = 1:02 PM. <br/><br/>U řezů v minulosti nedošlo k žádnému zpoždění. Opakování se stane okamžitě. |Ne |00:01:00 (1 min) |
| retryTimeout |Časový čas pro každý pokus o opakování.<br/><br/>Pokud je tato vlastnost nastavena na 10 minut, ověření by měla být dokončena do 10 minut. Pokud trvá déle než 10 minut k provedení ověření, opakování časový mzda.<br/><br/>Pokud všechny pokusy o ověření časový mzda, řez je označen jako **TimedOut**. |Ne |00:10:00 (10 minut) |
| maximumOpakovat |Počet, kolikrát zkontrolovat dostupnost externích dat. Maximální povolená hodnota je 10. |Ne |3 |


## <a name="create-datasets"></a>Vytvoření datových sad
Datové sady můžete vytvořit pomocí jednoho z těchto nástrojů nebo sad SDK:

- Průvodce kopírováním
- Visual Studio
- PowerShell
- Šablona Azure Resource Manageru
- REST API
- .NET API

Podrobné pokyny pro vytváření kanálů a datových sad pomocí jednoho z těchto nástrojů nebo sad SDK naleznete v následujících kurzech:

- [Vytvoření kanálu s aktivitou transformace dat](data-factory-build-your-first-pipeline.md)
- [Vytvoření kanálu s aktivitou přesunu dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Po vytvoření a nasazení kanálu můžete spravovat a monitorovat vaše kanály pomocí blades portálu Azure nebo aplikace monitorování a správa. Podrobné pokyny naleznete v následujících tématech:

- [Monitorování a správa kanálů pomocí blades portálu Azure](data-factory-monitor-manage-pipelines.md)
- [Sledování a správa kanálů pomocí aplikace Monitorování a správa](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Datové sady s vymezenou oborem
Můžete vytvořit datové sady, které jsou vymezeny na kanál pomocí **vlastnosti datové sady.** Tyto datové sady lze použít pouze aktivity v rámci tohoto kanálu, nikoli aktivity v jiných kanálech. Následující příklad definuje kanál se dvěma datovými sadami (InputDataset-rdc a OutputDataset-rdc), které mají být použity v rámci kanálu.

> [!IMPORTANT]
> Oborové datové sady jsou podporovány pouze s jednorázovými kanály (kde **pipelineMode** je nastavena na **OneTime).** Podrobnosti najdete [v tématu Onetime pipeline.](data-factory-create-pipelines.md#onetime-pipeline)
>
>

```json
{
    "name": "CopyPipeline-rdc",
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
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Další kroky
- Další informace o kanálech naleznete v [tématu Vytváření kanálů](data-factory-create-pipelines.md).
- Další informace o tom, jak jsou naplánovány a spouštěny kanály, najdete [v tématu Plánování a provádění v Azure Data Factory](data-factory-scheduling-and-execution.md).
