---
title: Vytvoření datové sady ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet datové sady ve službě Azure Data Factory s příklady, které používají vlastnosti, jako je posun a anchorDateTime.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 690c2769e129d5661e7d66cb3f9f968643c0dbdb
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746996"
---
# <a name="datasets-in-azure-data-factory"></a>Datové sady ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-create-datasets.md)
> * [Verze 2 (aktuální verze)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [datové sady ve V2](../concepts-datasets-linked-services.md).

Tento článek popisuje, jaké datové sady se, jak jsou definované ve formátu JSON, a jak se používají v kanály Azure Data Factory. Poskytuje podrobnosti o každé části (například struktura, dostupnost a zásady) v definici JSON datové sady. Tento článek také obsahuje příklady pro použití **posun**, **anchorDateTime**, a **styl** vlastnosti v definici JSON datové sady.

> [!NOTE]
> Pokud do služby Data Factory začínáte, přečtěte si téma [Úvod do služby Azure Data Factory](data-factory-introduction.md) Přehled. Pokud nemáte praktické zkušenosti s vytvářením datové továrny, můžete získat lepší pochopení článku [kurzu transformace dat](data-factory-build-your-first-pipeline.md) a [kurzu přesunu dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. A **kanálu** je logické seskupení **aktivity** , které dohromady provádějí určitou úlohu. Aktivity v kanálu definují akce, které se mají provést s vašimi daty. Například můžete použít aktivitu kopírování ke kopírování dat z místního SQL serveru do úložiště objektů Blob v Azure. Potom můžete použít aktivitu Hivu, která spouští skript Hive v clusteru Azure HDInsight ke zpracování dat z úložiště objektů Blob a generuje výstupní data. Nakonec můžete použít druhou aktivitu kopírování ke kopírování dat výstup do Azure SQL Data Warehouse, na které business intelligence (BI), vytváření sestav jsou integrované řešení. Další informace o kanálech a aktivitách najdete v tématu [kanály a aktivity ve službě Azure Data Factory](data-factory-create-pipelines.md).

Každá aktivita může mít nula nebo více vstupních **datových sad**a vytvoří výstupní datové sady. Vstupní datová sada představuje vstup pro aktivitu v kanálu a výstupní datovou sadu představuje výstup pro aktivitu. Datové sady identifikují data v rámci různých úložišť dat, jako jsou tabulky, soubory, složky a dokumenty. Datová sada služby Azure Blob například Určuje kontejner objektů blob a složku v úložišti objektů Blob, ze kterého by měl kanál číst data.

Než vytvoříte datovou sadu, vytvořte **propojená služba** k propojení vašeho úložiště dat do služby data factory. Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Datové sady identifikují data v rámci propojených úložištích dat, jako jsou tabulky SQL, souborů, složek a dokumenty. Například Azure Storage propojená služba propojuje účet úložiště do služby data factory. Datová sada služby Azure Blob představuje kontejner objektů blob a složku obsahující vstupní objekty BLOB ke zpracování.

Tady je ukázkový scénář. Ke zkopírování dat z úložiště objektů Blob do služby SQL database, vytvoříte dvě propojené služby: Azure Storage a Azure SQL Database. Vytvořte dvě datové sady: Azure Blob datovou sadu (odkazuje propojenou službu Azure Storage) a datová sada tabulky SQL Azure (což odkazuje na službu Azure SQL Database, která je propojená). Azure Storage a Azure SQL Database propojené služby obsahují připojovací řetězce, které služby Data Factory používá za běhu pro připojení k Azure Storage a Azure SQL Database, v uvedeném pořadí. Datová sada Azure Blob Určuje kontejner objektů blob a složka objektů blob obsahující vstupní objekty BLOB v úložišti objektů Blob. Datová sada tabulky SQL Azure Určuje tabulku SQL ve službě SQL database, ke které se mají zkopírovat data.

Následující diagram znázorňuje vztahy mezi kanálu, aktivit, datové sady a propojené služby ve službě Data Factory:

![Vztah mezi kanálu, aktivita, datové sady, propojené služby](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>JSON datové sady
Datové sady ve službě Data Factory je definovaná ve formátu JSON následujícím způsobem:

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

Následující tabulka popisuje vlastnosti v výše uvedený text JSON:

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| jméno |Název datové sady. Zobrazit [Azure Data Factory – pravidla pojmenování](data-factory-naming-rules.md) pravidla pojmenování. |Ano |Není k dispozici |
| type |Typ datové sady. Zadejte jeden z typů podporovaných službou Data Factory (například: AzureBlob, AzureSqlTable). <br/><br/>Podrobnosti najdete v tématu [typ datové sady](#Type). |Ano |Není k dispozici |
| Struktura |Schéma datové sady.<br/><br/>Podrobnosti najdete v tématu [struktury datové sady](#Structure). |Ne |Není k dispozici |
| typeProperties | Vlastnosti typu se liší pro každý typ (například: Azure Blob, tabulky Azure SQL). Podrobnosti o podporovaných typech a jejich vlastností najdete v tématu [typ datové sady](#Type). |Ano |Není k dispozici |
| external | Logický příznak k určení, zda datové sady je explicitně vytvořen kanál datové továrny nebo ne. Pokud není aktuální kanál vytvoří vstupní datovou sadu pro aktivitu, můžete tento příznak nastavte na hodnotu true. Tento příznak nastavte na hodnotu true pro vstupní datové sady první aktivity v kanálu.  |Ne |false (nepravda) |
| dostupnosti | Definuje okno zpracování (například každou hodinu nebo každý den) nebo řezů model pro produkční prostředí datové sady. Každá jednotka data využívaná a produkovaná spuštění aktivity se nazývá datový řez. Pokud dostupnost výstupní datovou sadu je nastavena na hodnotu denně (frekvence - den, interval - 1), řez každý den. <br/><br/>Podrobnosti najdete v tématu [dostupnosti datové sady](#Availability). <br/><br/>Podrobnosti o datové sady, model dělení časového, najdete v článku [plánování a provádění](data-factory-scheduling-and-execution.md) článku. |Ano |Není k dispozici |
| policy |Definuje kritéria nebo podmínky, které musí splnit řezy datové sady. <br/><br/>Podrobnosti najdete v tématu [datovou sadu zásad](#Policy) oddílu. |Ne |Není k dispozici |

## <a name="dataset-example"></a>Příklad datové sady
V následujícím příkladu, datová sada představuje tabulku s názvem **MyTable** ve službě SQL database.

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
* **tableName** (specifické pro typ AzureSqlTable) vlastnost type je nastavená na MyTable.
* **Vlastnost linkedServiceName** odkazuje na propojenou službu typu AzureSqlDatabase, která je definována v následující fragment kódu JSON.
* **frekvence dostupnosti** je nastavena na den, a **interval** je nastavená na 1. To znamená, že datová sada řez každý den.

**AzureSqlLinkedService** je definovaná následujícím způsobem:

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
* **připojovací řetězec** informace pro připojení k SQL database určuje vlastnosti typu.

Jak je vidět, propojená služba definuje připojení ke službě SQL database. Datová sada definuje, jaké tabulka se používá jako vstup a výstup aktivity v kanálu.

> [!IMPORTANT]
> Pokud datová sada je právě vytváří kanál, by měla být označena jako **externí**. Toto nastavení platí obecně pro vstupy první aktivitu v kanálu.

## <a name="Type"></a> Typ datové sady
Typ datové sady, která závisí na úložiště dat, které používáte. Najdete v následující tabulce najdete seznam úložišť dat podporovaných službou Data Factory. Klikněte na úložiště dat se informace o vytvoření propojené služby a datové sady pro toto úložiště dat.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Úložiště dat s * mohou být místní nebo v Azure infrastruktury jako služby (IaaS). Tyto úložiště dat vyžadují, abyste si nainstalovali [brána správy dat](data-factory-data-management-gateway.md).

V příkladu v předchozí části, typ datové sady je nastaven na **AzureSqlTable**. U datové sady objektů Blob v Azure, podobně, typ datové sady je nastaven na **AzureBlob**, jak je znázorněno v následujícím kódu JSON:

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

## <a name="Structure"></a>Struktury datové sady
**Struktura** část je nepovinná. Definuje schéma datové sady pomocí obsahující kolekci názvů a datové typy sloupců. Pomocí části struktury poskytují informace o typu, který se používá k převodu typů a namapovat sloupce ze zdroje do cíle. V následujícím příkladu datová sada obsahuje tři sloupce: `slicetimestamp`, `projectname`, a `pageviews`. Jsou typu String, String a desetinné číslo, v uvedeném pořadí.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Všechny sloupce struktury obsahují následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název sloupce. |Ano |
| type |Datový typ sloupce.  |Ne |
| Jazyková verze |. NET jazykovou verzi na základě používané pro typ je typ .NET: `Datetime` nebo `Datetimeoffset`. Výchozí formát je `en-us`. |Ne |
| formát |Formátovací řetězec se použije, když typ je typ .NET: `Datetime` nebo `Datetimeoffset`. |Ne |

Podle následujících pokynů můžete určit, kdy se mají zahrnout informace o struktuře a co mají být zahrnuty **struktura** oddílu.

* **Pro strukturovaná data zdroje**, zadejte v části struktury jenom v případě, že chcete, aby mapování sloupců zdroje do jímky sloupců a jejich názvy se neshodují. Tento druh zdroje strukturovaná data ukládá informace schématu a typu dat spolu s vlastní data. Příklady zdrojů strukturovaných dat: SQL Server, Oracle a tabulek v Azure.
  
    Informace o typu je již k dispozici pro strukturované datové zdroje, by neměl obsahovat informace o typu, pokud zahrnete oddíl struktury.
* **Pro schéma na zdroje dat pro čtení (konkrétně služby Blob storage)**, můžete k ukládání dat bez ukládání žádné schéma nebo typ informací s daty. Pro tyto typy zdrojů dat patří struktura Pokud chcete mapování sloupců zdroje do jímky sloupce. Také zahrnovat struktury datová sada je vstupní hodnota pro aktivitu kopírování a datové typy zdrojové datové sady mají být převedeny na nativní typy pro jímku.
    
    Data Factory podporuje následující hodnoty pro poskytnutí informací o typu struktury: **Int16, Int32, Int64, Single, Double, Decimal, bajtů [], datový typ Boolean, řetězec, Guid, data a času, Datetimeoffset a časový interval**. Tyto hodnoty jsou specifikace CLS (Common Language)-kompatibilní. Na základě NET typ hodnoty.

Data Factory automaticky provádí převody typů, při přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky.

## <a name="dataset-availability"></a>Dostupnost datové sady
**Dostupnosti** oddíl v datové sadě definuje okno zpracování (například každou hodinu, každý den, nebo jednou týdně) pro tuto datovou sadu. Další informace o časových obdobích aktivity, naleznete v tématu [plánování a provádění](data-factory-scheduling-and-execution.md).

Následující části Dostupnost určuje, že výstupní datová sada buď vytváří každou hodinu nebo každou hodinu je k dispozici vstupní datové sady:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Pokud tento kanál obsahuje následující počáteční a koncový čas:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Výstupní datová sada vytváří každou hodinu v rámci kanálu počáteční a koncové časy. Proto je pět řezů datové sady vyprodukované tímto kanálem, jeden pro každý okna aktivity (00: 00 - 1 AM, 1 AM - 2 AM, 2: 00 - 3 AM, 3 AM - 4 AM, 4: 00 - 5: 00).

Následující tabulka popisuje vlastnosti, které můžete použít v části dostupnosti:

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| frequency |Určuje časovou jednotku pro produkční prostředí řez datové sady.<br/><br/><b>Podporované frekvence</b>: Minuta, hodina, den, týden, měsíc |Ano |Není k dispozici |
| interval |Určuje multiplikátor pro četnost.<br/><br/>"Interval četnosti x" Určuje, jak často se řez. Například pokud potřebujete datové sady na průřezem podle počtu hodin, nastavíte <b>frekvence</b> k <b>hodinu</b>, a <b>interval</b> k <b>1</b>.<br/><br/>Všimněte si, že pokud zadáte **frekvence** jako **minutu**, byste měli nastavit interval na menší než 15. |Ano |Není k dispozici |
| Styl |Určuje, zda by měl být řez na začátku nebo konci interval.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Pokud **frekvence** je nastavena na **měsíc**, a **styl** je nastavena na **EndOfInterval**, řez na poslední den v měsíci. Pokud **styl** je nastavena na **StartOfInterval**, řez v první den v měsíci.<br/><br/>Pokud **frekvence** je nastavena na **den**, a **styl** je nastavena na **EndOfInterval**, řez za poslední hodinu dne.<br/><br/>Pokud **frekvence** je nastavena na **hodinu**, a **styl** je nastavena na **EndOfInterval**, řez na konec hodiny. Například pro určitý řez dobu 13: 00 – 2 hodin řez ve 14. |Ne |EndOfInterval |
| anchorDateTime |Definuje absolutní pozici v čase plánovačem slouží k výpočtu hranice řez datové sady. <br/><br/>Všimněte si, že pokud je tato propoerty částí data, která jsou podrobnější než je zadaná četnost, se ignorují podrobnější částí. Například pokud **interval** je **každou hodinu** (frekvence: hour a interval je: 1) a **anchorDateTime** obsahuje **minuty a sekundy**, pak části minuty a sekundy **anchorDateTime** jsou ignorovány. |Ne |01/01/0001 |
| Posun |Interval TimeSpan, podle kterého se posune začátku a konce všechny řezy datové sady. <br/><br/>Všimněte si, že pokud mají oba **anchorDateTime** a **posun** jsou zadána, výsledkem je kombinované shift. |Ne |Není k dispozici |

### <a name="offset-example"></a>Příklad posunutí
Ve výchozím nastavení, každý den (`"frequency": "Day", "interval": 1`) řezy spustit v 12: 00 (půlnoc) koordinovaný univerzální čas (UTC). Pokud chcete čas spuštění jako čas UTC 6: 00, nastavte posun, jak je znázorněno v následujícím fragmentu kódu:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Příklad anchorDateTime
V následujícím příkladu je vytvořen datové sady jednou za 23 hodin. První řez začíná časový limit určený parametrem **anchorDateTime**, který je nastaven na `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>Příklad posun a styl
Následující datová sada je měsíční a je vytvořen na 3. v každém měsíci v 8:00:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Datové sady zásad
**Zásady** oddíl v definici datové sady definuje kritéria nebo podmínky, které musí splnit řezy datové sady.

### <a name="validation-policies"></a>Ověření zásad
| Název zásady | Popis | Použít na | Požaduje se | Výchozí |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Ověří, jestli data v **úložiště objektů Blob v Azure** splňuje požadavky na minimální velikost (v megabajtech). |Azure Blob Storage |Ne |Není k dispozici |
| minimumRows |Ověří, jestli data v **Azure SQL database** nebo **tabulek v Azure** obsahuje minimální počet řádků. |<ul><li>Databáze SQL Azure</li><li>Tabulka Azure</li></ul> |Ne |Není k dispozici |

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
Externích datových sad, jsou ty, které nejsou od spuštění kanálu v datové továrně. Pokud datová sada je označená jako **externí**, **ExternalData** zásad může být definována a ovlivnit chování řez dostupnosti datové sady.

Pokud datové sady se vytvořil objekt pro vytváření dat, by měla být označena jako **externí**. Toto nastavení platí obecně pro vstupy první aktivitu v kanálu, pokud používá aktivitu nebo řetězení kanálu.

| Název | Popis | Požaduje se | Výchozí hodnota |
| --- | --- | --- | --- |
| dataDelay |Doba zpoždění kontroly dostupnosti externích dat pro danou řez. Například můžete kontrolu hodinové pozdržet pomocí tohoto nastavení.<br/><br/>Toto nastavení platí pouze pro aktuální čas. Například pokud je 1:00 PM hned teď a tato hodnota je 10 minut, ověření se spustí v 13:10.<br/><br/>Všimněte si, že toto nastavení nemá vliv na kolekce obsahuje nějaké řezy v minulosti. Řezy s **koncový čas řezu** + **dataDelay** < **nyní** zpracovávají bez jakéhokoli zpoždění.<br/><br/>Krát větší než 23:59 hodin zadat pomocí `day.hours:minutes:seconds` formátu. Například pokud chcete zadat 24 hodin, nepoužívejte 24:00:00. Místo toho použijte 1.00:00:00. Pokud používáte 24:00:00, je považován za 24 dní (24.00:00:00). 1 den a 4 hodiny zadejte 1:04:00:00. |Ne |0 |
| retryInterval |Doba čekání mezi selhání a dalším pokusem. Toto nastavení platí pro aktuální čas. Pokud předchozí akci se nezdařilo, je dalším pokusu o po **retryInterval** období. <br/><br/>Pokud je 1:00 PM teď začneme první pokus. Pokud doba trvání dokončení první ověření je 1 minuta a operace se nezdařila, další opakování je v 1:00 + 1 min (doba trvání) + 1 minuta (interval opakování) = 1:02 odp. <br/><br/>Řezy v minulosti neexistuje žádné zpoždění. Opakování dojde okamžitě. |Ne |00:01:00 (1 minuta) |
| retryTimeout |Časový limit pro každý opakovaný pokus.<br/><br/>Pokud je tato vlastnost nastavená na 10 minut, ověření by měla dokončit během 10 minut. Pokud to trvá déle než 10 minut, než se provést ověření, opakování vyprší časový limit.<br/><br/>Pokud všechny pokusy o vypršení časového limitu ověření řezu se označí jako **vypršel časový limit**. |Ne |00:10:00 (10 minut) |
| maximumRetry |Počet pokusů ke kontrole dostupnosti externí data. Maximální povolená hodnota je 10. |Ne |3 |


## <a name="create-datasets"></a>Vytvoření datových sad
Datové sady můžete vytvořit pomocí některého z těchto nástrojů nebo sad SDK:

- Průvodce kopírováním
- portál Azure
- Visual Studio
- PowerShell
- Šablona Azure Resource Manageru
- REST API
- .NET API

Najdete v následujících kurzech najdete podrobné pokyny pro vytváření kanálů a datových sad pomocí jedné z těchto nástrojů nebo sad SDK:

- [Vytvoření kanálu s aktivitou transformace dat](data-factory-build-your-first-pipeline.md)
- [Vytvoření kanálu s aktivitou přesunu dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Po vytvoření a nasazení kanálu můžete spravovat a monitorování kanálů pomocí oken Azure portal nebo monitorování a Správa aplikací. Naleznete v následujících tématech pro podrobné pokyny:

- [Monitorování a Správa kanálů pomocí oken webu Azure portal](data-factory-monitor-manage-pipelines.md)
- [Monitorování a Správa kanálů pomocí monitorování a Správa aplikací](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Datové sady s vymezeným oborem
Můžete vytvořit datové sady, které jsou omezená na kanálu pomocí **datových sad** vlastnost. Tyto datové sady je možné pouze aktivity v rámci tohoto kanálu, nikoli aktivity v jiných kanálech. Následující příklad definuje kanál s dvě datové sady (InputDataset rdc a OutputDataset-funkci Remote Differential Compression) se dá použít v kanálu.

> [!IMPORTANT]
> Datové sady s vymezeným oborem jsou podporovány pouze s jednorázové kanálů (kde **pipelineMode** je nastavena na **OneTime**). Zobrazit [Onetime kanálu](data-factory-create-pipelines.md#onetime-pipeline) podrobnosti.
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
- Další informace o kanálech najdete v tématu [vytvářet kanály](data-factory-create-pipelines.md).
- Další informace o tom, jak jsou kanály naplánovala a provést, najdete v části [plánování a provádění ve službě Azure Data Factory](data-factory-scheduling-and-execution.md).
