---
title: Vytvoření datových sad v Azure Data Factory | Microsoft Docs
description: Postup vytvoření datových sad v Azure Data Factory s příklady, které používají vlastnosti, například posun a anchorDateTime.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 6a3401f620f7dfe8b42bad9ed1a3981325b2ce1e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620475"
---
# <a name="datasets-in-azure-data-factory"></a>Datové sady ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-create-datasets.md)
> * [Verze 2 – Preview](../concepts-datasets-linked-services.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [datové sady v V2](../concepts-datasets-linked-services.md).

Tento článek popisuje, jaké datové sady se, jak jsou definovány ve formátu JSON, a způsobu jejich použití v Azure Data Factory kanálů. Poskytuje podrobnosti o jednotlivých částech (například struktura, dostupnost a zásad) v definici JSON datové sady. Tento článek také poskytuje příklady pro použití **posun**, **anchorDateTime**, a **styl** vlastnosti v definici JSON datové sady.

> [!NOTE]
> Pokud jste nový objekt pro vytváření dat, najdete v části [Úvod do Azure Data Factory](data-factory-introduction.md) Přehled. Pokud nemáte praktických zkušeností s vytvářením objektů pro vytváření dat, vám může pomoci lépe porozumět načtením [kurzu transformaci dat](data-factory-build-your-first-pipeline.md) a [kurzu přesun dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. A **kanálu** je logické seskupení **aktivity** společně provádějí úlohy. Aktivity v kanálu definují akce, které se mají provést s vašimi daty. Může například pomocí aktivity kopírování zkopírovat data z místního serveru SQL do úložiště objektů Blob v Azure. Pak můžete použít aktivitu Hive, která se spouští skript Hive v clusteru Azure HDInsight pro zpracování dat z úložiště objektů Blob nevytvořila výstupní data. Nakonec můžete použít druhý aktivity kopírování zkopírovat výstupní data do Azure SQL Data Warehouse, na které business intelligence (BI), vytváření sestav jsou integrované řešení. Další informace o kanály a aktivity, najdete v části [kanály a aktivity v Azure Data Factory](data-factory-create-pipelines.md).

Aktivita může trvat vstup nula nebo více **datové sady**a vytvoří výstupní datové sady. Představuje vstupní datová sada vstupem pro aktivitu v kanálu a výstupní datové reprezentuje výstup aktivity. Datové sady identifikují data v rámci různých úložišť dat, jako jsou tabulky, soubory, složky a dokumenty. Například datové sadě služby Azure Blob Určuje kontejner objektů blob a složky v úložišti objektů Blob, ze kterého by měl kanálu číst data. 

Než vytvoříte datové sady, vytvořit **propojená služba** propojit data store k objektu pro vytváření dat. Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Datové sady identifikují dat v rámci propojených úložištích dat, jako jsou tabulky SQL, souborů, složek a dokumentů. Například Azure Storage propojená služba propojuje účet úložiště do služby data factory. Datové sadě služby Azure Blob představuje kontejner objektů blob a složky, která obsahuje vstupní objekty BLOB ke zpracování. 

Tady je ukázkový scénář. Ke zkopírování dat z úložiště objektů Blob k databázi SQL, vytvoříte dvě propojené služby: úložiště Azure a Azure SQL Database. Pak vytvořte dvě datové sady: datovou sadu objektu Blob Azure, (který odkazuje na službu Azure Storage, propojené) a tabulky SQL Azure datovou sadu (odkazuje na službu Azure SQL Database propojené). Azure Storage a Azure SQL Database propojené služby obsahovat připojovací řetězce, které objekt pro vytváření dat používá za běhu k připojení k Azure Storage a Azure SQL Database, v uvedeném pořadí. Datovou sadu objektu Blob Azure Určuje kontejner objektů blob a objektů blob složku, která obsahuje vstupní objekty BLOB ve službě Blob storage. Datová sada tabulky SQL Azure Určuje tabulku SQL ve vaší databázi SQL, na které má zkopírovat data.

Následující obrázek znázorňuje vztahy mezi kanálu, aktivity, datové sady a propojené služby objektu pro vytváření dat: 

![Vztah mezi kanálu, aktivity, datové sady, propojených služeb](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>JSON datové sady
Datové sady ve službě Data Factory je definováno ve formátu JSON následujícím způsobem:

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
| jméno |Název datové sady. V tématu [Azure Data Factory - pravidla po pojmenování](data-factory-naming-rules.md) pravidla pojmenování. |Ano |Není k dispozici |
| type |Typ datové sady. Zadejte jeden z typů podporovaných službou Data Factory (například: AzureBlob, AzureSqlTable). <br/><br/>Podrobnosti najdete v tématu [typ sady](#Type). |Ano |Není k dispozici |
| Struktura |Schéma datové sady.<br/><br/>Podrobnosti najdete v tématu [strukturu datové sady](#Structure). |Ne |Není k dispozici |
| typeProperties | Vlastnosti typu se liší pro jednotlivé typy (například: Azure Blob, tabulka Azure SQL). Podrobnosti o svých vlastnostech a podporované typy najdete v tématu [typ sady](#Type). |Ano |Není k dispozici |
| external | Logický příznak k určení, zda datové sady je explicitně produkovaný kanálu objekt pro vytváření dat nebo ne. Není-li vstupní datové sady pro aktivitu v kanálu aktuální, tento příznak nastavte na hodnotu true. Tento příznak nastavte na hodnotu true pro vstupní datové sady první aktivitu v kanálu.  |Ne |false (nepravda) |
| dostupnosti | Definuje okna pro zpracování (například hodinové nebo denní) nebo řezů model pro produkční datovou sadu. Jednotlivé jednotky data využívat a vyprodukované spuštění aktivity se nazývá datový řez. Pokud dostupnosti výstupní datové je nastavena na hodnotu denně (frekvenci - den, interval - 1), řez se vytvoří každý den. <br/><br/>Podrobnosti najdete v tématu [datovou sadu dostupnosti](#Availability). <br/><br/>Podrobnosti na datovou sadu řezů modelu najdete v tématu [plánování a provádění](data-factory-scheduling-and-execution.md) článku. |Ano |Není k dispozici |
| policy |Definuje kritéria nebo podmínku, musíte splnit řezy datovou sadu. <br/><br/>Podrobnosti najdete v tématu [datovou sadu zásad](#Policy) části. |Ne |Není k dispozici |

## <a name="dataset-example"></a>Příklad datové sady
V následujícím příkladu představuje datovou sadu tabulku s názvem **MyTable** v databázi SQL.

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

* **typ** je nastaven na AzureSqlTable.
* **Název tabulky** typu (specifické pro typ AzureSqlTable) je nastavena na MyTable.
* **linkedServiceName** odkazuje na propojené služby typu azuresqldatabase., která je definována v další fragmentu kódu JSON. 
* **frekvence dostupnosti** je nastaven na den a **interval** je nastaven na hodnotu 1. To znamená, že je řez datovou sadu vytváří denně.  

**AzureSqlLinkedService** je definován následujícím způsobem:

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

V předchozím fragmentu kódu JSON:

* **typ** je nastaven na azuresqldatabase.
* **connectionString** vlastnost typu Určuje informace pro připojení k databázi SQL.  

Jak vidíte, propojené služby definuje, jak se připojit k databázi SQL. Datová sada definuje, jaký tabulka je použít jako vstup a výstup aktivity v kanálu.   

> [!IMPORTANT]
> Pokud se vytváří datové sady v kanálu, by měl být označen jako **externí**. Toto nastavení obecně platí pro vstupy první aktivitu v kanálu.   


## <a name="Type"></a> Typ sady
Typ datové sady závisí na úložiště dat, které používáte. Najdete v následující tabulce najdete seznam úložišť dat podporovaných službou Data Factory. Klikněte na úložiště dat pro informace o vytvoření propojené služby a sadu dat pro toto datové úložiště.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Úložiště dat, s * může být místní nebo v Azure infrastruktura jako služba (IaaS). Tyto úložiště dat vyžaduje instalaci [Brána pro správu dat](data-factory-data-management-gateway.md).

V příkladu v předchozí části, typ datové sady je nastavený na **AzureSqlTable**. Pro datové sadě služby Azure Blob podobně typ datové sady je nastaven na **AzureBlob**, jak je znázorněno v následujícím kódu JSON:

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

## <a name="Structure"></a>Struktura datové sady
**Struktura** část je nepovinná. Definuje schéma datové sady ve obsahující kolekci názvů a typy dat sloupců. V části struktura použijete k poskytování informací o typu, který se používá k převést typy a mapování sloupců ze zdroje do cíle. V následujícím příkladu, datová sada má tři sloupce: `slicetimestamp`, `projectname`, a `pageviews`. Jsou typu řetězec, řetězec a Decimal, v uvedeném pořadí.

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
| Jazyková verze |. Na základě NET jazykovou verzi, která se použije, když je typ typ formátu .NET: `Datetime` nebo `Datetimeoffset`. Výchozí hodnota je `en-us`. |Ne |
| Formát |Řetězec, který se má použít, když je typ typ formátu .NET formátu: `Datetime` nebo `Datetimeoffset`. |Ne |

Následující pokyny vám pomohou určit, kdy se mají zahrnout informace o struktuře a co mají být zahrnuty **struktura** části.

* **Pro strukturovaná data zdroje**, zadejte v části struktura pouze v případě, že chcete namapovat zdrojové sloupce na jímky sloupců a jejich názvy nejsou stejné. Tento druh zdroj strukturovaných dat ukládá informace schématu a typu dat společně s samotná data. Příklady strukturovaných dat zdroje: SQL Server, Oracle a tabulky Azure. 
  
    Protože je již k dispozici pro strukturovaná data zdroje informací o typu, by neměla zahrnovat informace o typu, pokud obsahovat části struktura.
* **Pro schéma pro zdroje dat pro čtení (konkrétně úložiště objektů Blob)**, můžete k ukládání dat bez ukládání žádné schéma nebo typ informace s daty. Pro tyto typy zdrojů dat zahrnovat chcete namapovat zdrojové sloupce na jímky sloupce struktury. Také zahrnovat struktura, když je datová sada vstupem pro aktivitu kopírování a datové typy sady zdroje dat mají být převedeny na nativní typy pro jímky. 
    
    Objekt pro vytváření dat podporuje následující hodnoty pro poskytnutí informací o typu ve struktuře: **Int16, Int32, Int64, jedním, Double, Decimal, Byte [], logická hodnota, řetězec, Guid, Datetime, Datetimeoffset a časový interval**. Tyto hodnoty jsou specifikace CLS (Common Language)-vyhovující,. Na základě NET typ hodnoty.

Objekt pro vytváření dat automaticky provede převody typů, při přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky. 
  

## <a name="dataset-availability"></a>Datovou sadu dostupnosti
**Dostupnosti** oddíl v datové sadě definuje okna zpracování (například hodinový, denní, nebo každý týden) pro datovou sadu. Další informace o aktivity windows najdete v tématu [plánování a provádění](data-factory-scheduling-and-execution.md).

V následující části dostupnosti Určuje, že výstupní datovou sadu se vytvářejí buď hodinu nebo vstupní datové sady každou hodinu je k dispozici:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

Pokud kanálu má následující počáteční a koncový čas:  

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Výstupní sada vytváří každou hodinu v rámci kanálu spuštění a ukončení. Proto jsou pět řezy datové sady vyprodukované tímto kanálem, jeden pro každou aktivitu okno (12: 00 - 1 AM, 1: 00 - 2 AM, 2: 00 - 3 AM, 3: 00 - 4 AM, 4: 00 - 5: 00). 

Následující tabulka popisuje vlastnosti, které můžete použít v části dostupnosti:

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| frequency |Určuje časovou jednotku pro produkční řez datovou sadu.<br/><br/><b>Podporované frekvence</b>: minutu, hodinu, den, týden, měsíc |Ano |Není k dispozici |
| interval |Určuje multiplikátor pro četnost.<br/><br/>"Frekvence x interval" Určuje, jak často se vytvářejí řez. Například pokud budete potřebovat datovou sadu, která se rozříznut hodinu, nastavíte <b>frekvence</b> k <b>hodinu</b>, a <b>interval</b> k <b>1</b>.<br/><br/>Všimněte si, že pokud zadáte **frekvence** jako **minutu**, měli byste nastavit interval hodnotu menší než 15. |Ano |Není k dispozici |
| Styl |Určuje, zda by měl být řez vytváří na začátku nebo na konci interval.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Pokud **frekvence** je nastaven na **měsíc**, a **styl** je nastaven na **EndOfInterval**, řez se vytváří poslední den v měsíci. Pokud **styl** je nastaven na **StartOfInterval**, řez se vytváří první den v měsíci.<br/><br/>Pokud **frekvence** je nastaven na **den**, a **styl** je nastaven na **EndOfInterval**, řez se vytváří za poslední hodinu dne.<br/><br/>Pokud **frekvence** je nastaven na **hodinu**, a **styl** je nastaven na **EndOfInterval**, řez se vytvářejí na konci za hodinu. Například pro řez dobu 13: 00 – 14: 00, je řez vytvořeného ve 2. |Ne |EndOfInterval |
| anchorDateTime |Definuje absolutní pozici v čase plánovačem slouží k výpočtu hranice řez datovou sadu. <br/><br/>Všimněte si, že pokud tento propoerty částí data, která jsou podrobnější než je zadaná četnost, se ignorují podrobnější částí. Například pokud **interval** je **každou hodinu** (frekvence: hodin a interval: 1) a **anchorDateTime** obsahuje **minuty a sekundy**, pak části minuty a sekundy **anchorDateTime** jsou ignorovány. |Ne |01/01/0001 |
| Posun |Časový interval, ve kterém jsou zapuštěno počáteční a koncová všech řezech datovou sadu. <br/><br/>Všimněte si, že pokud obě **anchorDateTime** a **posun** jsou nastaveny, výsledkem je kombinovaná shift. |Ne |Není k dispozici |

### <a name="offset-example"></a>Příklad posunutí
Ve výchozím nastavení, každý den (`"frequency": "Day", "interval": 1`) řezy začnou ve 12: 00 (půlnoc) koordinovaný světový čas (UTC). Pokud chcete, aby čas spuštění jako čas UTC 6: 00, nastavte posun, jak je znázorněno v následujícím fragmentu kódu: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Příklad anchorDateTime
V následujícím příkladu se sada vytváří jednou za 23 hodin. První řez spustí v době určeného **anchorDateTime**, který je nastaven na `2017-04-19T08:00:00` (UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Posun nebo styl příklad
Tyto datové sady je každý měsíc a vytváří 3. v každém měsíci v 8:00 AM (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Datovou sadu zásad
**Zásad** oddíl v definici datové sady definuje kritéria nebo podmínku, musíte splnit řezy datovou sadu.

### <a name="validation-policies"></a>Zásady ověřování
| Název zásady | Popis | Použít | Požaduje se | Výchozí |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Ověří, jestli data v **úložiště objektů Azure Blob** splňuje požadavky na minimální velikost (v megabajtech). |Azure Blob Storage |Ne |Není k dispozici |
| minimumRows |Ověří, jestli data v **Azure SQL database** nebo **tabulky Azure** obsahuje minimální počet řádků. |<ul><li>Databáze SQL Azure</li><li>Tabulka Azure</li></ul> |Ne |Není k dispozici |

#### <a name="examples"></a>Příklady
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Externích datových sad
Externích datových sad, jsou ty, které nejsou spuštěné kanálu v datové továrně. Pokud se datová sada je označena jako **externí**, **ExternalData** zásad může být definována, jež ovlivňují chování řez dostupnost datové sady.

Pokud datové sady je vytvářen službou Data Factory, by měl být označen jako **externí**. Toto nastavení se obvykle platí pro vstupy první aktivitu v kanálu, pokud se používá aktivitu nebo řetězení kanálu.

| Název | Popis | Požaduje se | Výchozí hodnota |
| --- | --- | --- | --- |
| dataDelay |Doba zpoždění kontroly na dostupnost externích dat pro danou řez. Můžete například zpoždění hodinové kontroly pomocí tohoto nastavení.<br/><br/>Toto nastavení platí jenom pro aktuální čas.  Například pokud je 1:00 PM hned teď a tato hodnota je 10 minut, ověření se spustí: 10: 00.<br/><br/>Všimněte si, že toto nastavení neovlivňuje řezy v minulosti. Řezy s **řez koncový čas** + **dataDelay** < **nyní** jsou zpracovávány bez jakéhokoli zpoždění.<br/><br/>Časy větší než 23:59 hodin by měl být určena pomocí `day.hours:minutes:seconds` formátu. Například pokud chcete zadat 24 hodin, nepoužívejte 24:00:00. Místo toho použijte 1.00:00:00. Pokud používáte 24:00:00, bude považován za 24 dní (24.00:00:00). 1 den a 4 hodiny zadejte 1:04:00:00. |Ne |0 |
| RetryInterval |Doba čekání mezi selhání a další pokus. Toto nastavení platí pro aktuální čas. Pokud předchozí zkuste se nezdařila, je dalším pokusu o po **retryInterval** období. <br/><br/>Pokud je 1:00 PM nyní, můžeme začít prvního pokusu. Pokud doba trvání dokončení první kontrola ověření je 1 minuta a operace se nezdařila, další pokus proběhne v 1:00 + 1 min (doba trvání) + 1min (interval opakování) = 1:02 PM. <br/><br/>Řezy v minulosti není k dispozici žádné zpoždění není. Opakovaném dojde okamžitě. |Ne |00:01:00 (1 min) |
| retryTimeout |Časový limit pro jednotlivé pokusy o opakování.<br/><br/>Pokud je tato vlastnost nastavená na 10 minut, by se během deseti minut dokončit ověření. Pokud trvá déle než 10 minut, aby k ověření, opakovaném časového limitu.<br/><br/>Pokud všechny pokusy o ověření časový limit řez je označen jako **TimedOut**. |Ne |00:10:00 (10 minut) |
| maximumRetry |Stanovený počet zkontrolujte dostupnost externí data. Maximální povolená hodnota je 10. |Ne |3 |


## <a name="create-datasets"></a>Vytvoření datových sad
Datové sady můžete vytvořit pomocí jedné z těchto nástrojů nebo sady SDK: 

- Průvodce kopírováním 
- Azure Portal
- Visual Studio
- PowerShell
- Šablona Azure Resource Manageru
- REST API
- .NET API

Najdete v následujících kurzech podrobné pokyny pro vytváření kanálů a datové sady pomocí jedné z těchto nástrojů nebo sady SDK:
 
- [Vytvoření kanálu s aktivitou transformace dat](data-factory-build-your-first-pipeline.md)
- [Vytvoření kanálu s aktivitou přesun dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Po kanálu se vytvoří a nasadí, můžete spravovat a monitorovat kanály pomocí oken Azure portal nebo aplikace, monitorování a správu. Najdete v následujících tématech podrobné pokyny: 

- [Monitorování a Správa kanálů pomocí oken webu Azure portal](data-factory-monitor-manage-pipelines.md)
- [Monitorování a Správa kanálů pomocí monitorování a správy aplikace](data-factory-monitor-manage-app.md)


## <a name="scoped-datasets"></a>Oboru datové sady
Můžete vytvořit datové sady, které jsou omezená na kanálu pomocí **datové sady** vlastnost. Tyto datové sady lze použít pouze aktivity v rámci tohoto kanálu, nikoli aktivity v jiné kanály. V následujícím příkladu definuje kanál pomocí dvě datové sady (InputDataset rdc a OutputDataset rdc) má být použit v rámci kanálu.  

> [!IMPORTANT]
> Oboru datové sady jsou podporovány pouze s jednorázové kanály (kde **pipelineMode** je nastaven na **OneTime**). V tématu [Onetime kanálu](data-factory-create-pipelines.md#onetime-pipeline) podrobnosti.
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

## <a name="next-steps"></a>Další postup
- Další informace o kanály najdete v tématu [vytvořit kanály](data-factory-create-pipelines.md). 
- Další informace o tom, jak jsou naplánované kanálů a jsou prováděny najdete v tématu [plánování a provádění v Azure Data Factory](data-factory-scheduling-and-execution.md). 
