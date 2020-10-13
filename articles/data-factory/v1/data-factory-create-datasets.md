---
title: Vytvoření datových sad v Azure Data Factory
description: Naučte se, jak vytvořit datové sady v Azure Data Factory, s příklady, které používají vlastnosti, jako je posun a anchorDateTime.
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
ms.openlocfilehash: ddb99fd7a7ce8265a6e9c63555cd6a226caacc4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440724"
---
# <a name="datasets-in-azure-data-factory-version-1"></a>Datové sady v Azure Data Factory (verze 1)
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-create-datasets.md)
> * [Verze 2 (aktuální verze)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [datové sady ve verzi v2](../concepts-datasets-linked-services.md).

Tento článek popisuje, jaké jsou datové sady, jak jsou definovány ve formátu JSON a jak se používají v Azure Data Factorych kanálech. Poskytuje podrobnosti o jednotlivých oddílech (například strukturu, dostupnost a zásady) v definici JSON datové sady. Článek také poskytuje příklady pro použití vlastností **offset**, **anchorDateTime**a **style** v definici JSON datové sady.

> [!NOTE]
> Pokud s Data Factory začínáte, přečtěte si téma [Úvod do Azure Data Factory](data-factory-introduction.md) pro přehled. Pokud nemáte praktické zkušenosti s vytvářením datových továren, můžete získat lepší porozumění pomocí [kurzu transformace dat](data-factory-build-your-first-pipeline.md) a [kurzu přesunu dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. **Kanál** je logické seskupení **aktivit** , které dohromady provádějí úlohu. Aktivity v kanálu definují akce, které se mají provádět s daty. Aktivitu kopírování můžete například použít ke kopírování dat z databáze SQL Server do úložiště objektů BLOB v Azure. Pak můžete použít aktivitu podregistru, která spouští skript podregistru v clusteru Azure HDInsight ke zpracování dat z úložiště objektů blob za účelem vytvoření výstupních dat. Nakonec můžete použít druhou aktivitu kopírování ke zkopírování výstupních dat do služby Azure synapse Analytics (dříve SQL Data Warehouse), a to nad tím, která řešení pro vytváření sestav business intelligence (BI) jsou sestavená. Další informace o kanálech a aktivitách najdete v tématu [kanály a aktivity v Azure Data Factory](data-factory-create-pipelines.md).

Aktivita může mít nula nebo více vstupních **datových sad**a vytvoří jednu nebo více výstupních datových sad. Vstupní datová sada představuje vstup pro aktivitu v kanálu a výstupní datová sada představuje výstup aktivity. Datové sady identifikují data v rámci různých úložišť dat, jako jsou tabulky, soubory, složky a dokumenty. Datová sada objektu blob Azure například určuje kontejner objektů BLOB a složku v úložišti objektů blob, ze kterých by měl kanál číst data.

Před vytvořením datové sady vytvořte **propojenou službu** , která propojí úložiště dat s datovou továrnou. Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Datové sady identifikují data v propojených úložištích dat, jako jsou tabulky SQL, soubory, složky a dokumenty. Například propojená služba Azure Storage propojuje účet úložiště s datovou továrnou. Datová sada objektů BLOB v Azure představuje kontejner objektů BLOB a složku obsahující vstupní objekty blob, které se mají zpracovat.

Tady je ukázkový scénář. Pokud chcete kopírovat data z úložiště objektů blob do SQL Database, vytvoříte dvě propojené služby: Azure Storage a Azure SQL Database. Pak vytvořte dvě datové sady: datovou sadu Azure Blob (která odkazuje na propojenou službu Azure Storage) a datovou sadu tabulky Azure SQL (která odkazuje na propojenou službu Azure SQL Database). Azure Storage a Azure SQL Database propojené služby obsahují připojovací řetězce, které Data Factory používá za běhu, aby se připojily k vašemu Azure Storage a Azure SQL Database. Datová sada Azure Blob určuje kontejner objektů BLOB a složku objektů blob, které obsahují vstupní objekty BLOB v úložišti objektů BLOB. Datová sada tabulky SQL Azure Určuje tabulku SQL ve vaší databázi SQL, do které se zkopírují data.

Následující diagram znázorňuje vztahy mezi kanálem, aktivitou, datovou sadou a propojenou službou v Data Factory:

![Vztah mezi kanálem, aktivitou, datovou sadou, propojenými službami](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Datová sada – JSON
Datová sada v Data Factory je definována ve formátu JSON následujícím způsobem:

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

Následující tabulka obsahuje popis vlastností ve výše uvedeném formátu JSON:

| Vlastnost | Popis | Povinné | Výchozí |
| --- | --- | --- | --- |
| name |Název datové sady Pravidla pro pojmenování najdete v tématu [pravidla pro Pojmenovávání Azure Data Factory](data-factory-naming-rules.md) . |Yes |Není k dispozici |
| typ |Typ datové sady Zadejte jeden z typů, které podporuje Data Factory (například: Azureblobu, AzureSqlTable). <br/><br/>Podrobnosti najdete v tématu [Typ datové sady](#Type). |Yes |Není k dispozici |
| – struktura |Schéma datové sady<br/><br/>Podrobnosti najdete v tématu [Struktura datové sady](#Structure). |No |Není k dispozici |
| typeProperties | Vlastnosti typu se u každého typu liší (například: Azure Blob, tabulka SQL Azure). Podrobnosti o podporovaných typech a jejich vlastnostech naleznete v tématu [Typ datové sady](#Type). |Yes |Není k dispozici |
| external | Příznak Boolean pro určení, zda je datová sada explicitně vytvořena kanálem datové továrny. Pokud vstupní datová sada pro aktivitu není vytvořena aktuálním kanálem, nastavte tento příznak na hodnotu true. Nastavte tento příznak na hodnotu true pro vstupní datovou sadu první aktivity v kanálu.  |No |false (nepravda) |
| dostupnosti | Definuje okno zpracování (například každou hodinu nebo každý den) nebo model průřezu pro produkční datovou sadu. Každá jednotka dat spotřebované a vyráběná spuštěním aktivity se nazývá datový řez. Pokud je dostupnost výstupní datové sady nastavená na hodnotu denně (frekvence-den, interval-1), řez se vytvoří každý den. <br/><br/>Podrobnosti najdete v tématu Dostupnost datové sady. <br/><br/>Podrobnosti o modelu dělení datových sad naleznete v článku [plánování a provádění](data-factory-scheduling-and-execution.md) . |Yes |Není k dispozici |
| policy |Definuje kritéria nebo podmínku, které musí řezy datové sady splňovat. <br/><br/>Podrobnosti najdete v části [zásady datové sady](#Policy) . |No |Není k dispozici |

## <a name="dataset-example"></a>Příklad datové sady
V následujícím příkladu datová sada představuje tabulku s názvem **myTable** v databázi SQL.

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

* **typ** je nastavený na AzureSqlTable.
* vlastnost typu **TableName** (specifická pro typ AzureSqlTable) je nastavená na myTable.
* **linkedServiceName** odkazuje na propojenou službu typu AzureSqlDatabase, která je definována v následujícím fragmentu kódu JSON.
* **frekvence dostupnosti** je nastavená na den a **interval** je nastavená na 1. To znamená, že řez datové sady se vyprodukuje každý den.

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

V předchozím fragmentu kódu JSON:

* **typ** je nastavený na AzureSqlDatabase.
* vlastnost typu **ConnectionString** určuje informace pro připojení k databázi SQL.

Jak vidíte, propojená služba definuje, jak se připojit k databázi SQL. Datová sada definuje, jaká tabulka slouží jako vstup a výstup aktivity v kanálu.

> [!IMPORTANT]
> Pokud není datová sada vytvářena kanálem, měla by být označena jako **externí**. Toto nastavení se obecně vztahuje na vstupy první aktivity v kanálu.

## <a name="dataset-type"></a><a name="Type"></a> Typ datové sady
Typ datové sady závisí na používaném úložišti dat. Seznam úložišť dat podporovaných nástrojem Data Factory najdete v následující tabulce. Kliknutím na úložiště dat se dozvíte, jak vytvořit propojenou službu a datovou sadu pro toto úložiště dat.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Úložiště dat s * můžou být místní nebo v infrastruktuře Azure jako služba (IaaS). Tato úložiště dat vyžadují, abyste nainstalovali [bránu Správa dat](data-factory-data-management-gateway.md).

V příkladu v předchozí části je typ datové sady nastavený na **AzureSqlTable**. Podobně pro datovou sadu objektů BLOB v Azure je typ datové sady nastavený na **azureblobu**, jak je znázorněno v následujícím kódu JSON:

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
Oddíl **struktury** je nepovinný. Definuje schéma datové sady obsahující kolekci názvů a datových typů sloupců. Oddíl Structure (struktura) slouží k poskytnutí informací o typu, které slouží k převodu typů a mapování sloupců ze zdroje do cíle. V následujícím příkladu má datová sada tři sloupce: `slicetimestamp` , `projectname` a `pageviews` . Jsou typu String, String a Decimal, v uvedeném pořadí.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Každý sloupec ve struktuře obsahuje následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| name |Název sloupce |Yes |
| typ |Datový typ sloupce  |No |
| jazyková verze |. Jazyková verze založená na síti, která se má použít, pokud je typem typ .NET: `Datetime` nebo `Datetimeoffset` . Výchozí formát je `en-us`. |No |
| formát |Řetězec formátu, který se má použít, pokud je typem typ .NET: `Datetime` nebo `Datetimeoffset` . |No |

Následující pokyny vám pomohou určit, kdy zahrnout informace o struktuře a co zahrnout do části **Struktura** .

* V případě **strukturovaných zdrojů dat**určete oddíl struktura pouze v případě, že chcete mapovat zdrojové sloupce na sloupce jímky a jejich názvy nejsou stejné. Tento druh strukturovaného zdroje dat ukládá schéma dat a informace o typech spolu s samotnými daty. Příklady strukturovaných zdrojů dat zahrnují SQL Server, Oracle a tabulku Azure.
  
    Jelikož informace o typu jsou již k dispozici pro strukturované zdroje dat, neměli byste při zahrnutí oddílu struktury zahrnovat informace o typu.
* **Pro schéma při čtení zdrojů dat (konkrétně úložiště objektů BLOB)** se můžete rozhodnout ukládat data bez uložení informací o schématu nebo typu s daty. U těchto typů zdrojů dat zahrňte strukturu, pokud chcete namapovat zdrojové sloupce na sloupce jímky. Také zahrnout strukturu, pokud je datová sada vstupem pro aktivitu kopírování a datové typy zdrojové datové sady by měly být převedeny na nativní typy jímky.
    
    Data Factory podporuje následující hodnoty pro poskytování informací o typu ve struktuře: **Int16, Int32, Int64, Single, Double, Decimal, Byte [], Boolean, String, GUID, DateTime, DateTimeOffset a TimeSpan**. Tyto hodnoty jsou kompatibilní se specifikací CLS (Common Language Specification). Hodnoty typů založené na síti.

Data Factory automaticky provádí převody typu při přesunu dat ze zdrojového úložiště dat do úložiště dat jímky.

## <a name="dataset-availability"></a>Dostupnost datové sady
Oddíl **dostupnosti** v datové sadě definuje okno zpracování (například každou hodinu, každý den nebo každý týden) pro datovou sadu. Další informace o oknech aktivit najdete v tématu [plánování a provádění](data-factory-scheduling-and-execution.md).

Následující oddíl dostupnosti určuje, že výstupní datová sada je buď vytvořená každou hodinu, nebo je vstupní datová sada k dispozici každou hodinu:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Pokud má kanál následující počáteční a koncové časy:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Výstupní datová sada se vytvoří každou hodinu v rámci počátečního a koncového času kanálu. Proto je k dispozici pět řezů datových sad vytvořených tímto kanálem, jeden pro každé okno aktivity (v rozmezí od 16 do 1 dop., 1 dop. 2 dop. 2 dop. 3 dop. 4 dop. 4 dop. 5 AM).

Následující tabulka obsahuje popis vlastností, které můžete použít v části Dostupnost:

| Vlastnost | Popis | Povinné | Výchozí |
| --- | --- | --- | --- |
| frequency |Určuje časovou jednotku pro produkci řezu datové sady.<br/><br/><b>Podporovaná frekvence</b>: minuta, hodina, den, týden, měsíc |Yes |Není k dispozici |
| interval |Určuje násobitel pro frekvenci.<br/><br/>Frekvence x interval určuje, jak často se řez vytvoří. Například pokud potřebujete datovou sadu rozdělit každou hodinu, nastavte <b>četnost</b> na <b>hodinu</b>a <b>interval</b> na <b>1</b>.<br/><br/>Všimněte si, že pokud zadáte **frekvenci** jako **minutu**, měli byste nastavit interval na ne méně než 15. |Yes |Není k dispozici |
| style |Určuje, zda má být řez vytvořen na začátku nebo konci intervalu.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Pokud je **frekvence** nastavená na hodnotu **month (měsíc**) a **styl** je nastaven na **EndOfInterval**, řez se vytvoří za poslední den v měsíci. Pokud je **styl** nastaven na **StartOfInterval**, řez se vytvoří první den v měsíci.<br/><br/>Pokud je **frekvence** nastavená na **Day**a **style** je nastavená na **EndOfInterval**, řez se vytvoří za poslední hodinu dne.<br/><br/>Pokud je **frekvence** nastavená na **Hour**a **style** je nastavená na **EndOfInterval**, řez se vytvoří na konci hodiny. Například pro řez v období 1 – 2 PM se řez vytvoří 2. |No |EndOfInterval |
| anchorDateTime |Definuje absolutní pozici v čase, kterou Plánovač používá k výpočtu hranic řezu datové sady. <br/><br/>Všimněte si, že pokud tato vlastnost obsahuje části s daty, které jsou lépe podrobnější než zadaná frekvence, budou podrobnější části ignorovány. Například pokud je **interval** **každou hodinu** (frekvence: hodina a interval: 1) a **anchorDateTime** obsahuje **minuty a sekundy**, budou části minut a sekund **anchorDateTime** ignorovány. |No |01/01/0001 |
| posun |Časový interval, podle kterého se posune začátek a konec všech řezů datové sady <br/><br/>Všimněte si, že pokud jsou zadány současně **anchorDateTime** i **offset** , je výsledkem kombinovaný posun. |No |Není k dispozici |

### <a name="offset-example"></a>Příklad posunutí
Ve výchozím nastavení se každý den ( `"frequency": "Day", "interval": 1` ) řezů začíná v rozmezí od 12:00 (půlnoc) koordinovaný světový čas (UTC). Pokud chcete, aby byl čas zahájení nastavený na hodnotu 6 času UTC, nastavte posun, jak je znázorněno v následujícím fragmentu kódu:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Příklad anchorDateTime
V následujícím příkladu se datová sada vytvoří jednou za 23 hodin. První řez začíná v čase určeném parametrem **anchorDateTime**, který je nastaven na hodnotu `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>Příklad posunutí/stylu
Následující datová sada je měsíčně a je vytvořena na 3. v každém měsíci v 8:00 `3.08:00:00` . ():

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="dataset-policy"></a><a name="Policy"></a>Zásada datové sady
Oddíl **Policy** v definici datové sady definuje kritéria nebo podmínku, kterou musí řezy datové sady splňovat.

### <a name="validation-policies"></a>Zásady ověřování
| Název zásad | Description | Použito pro | Vyžadováno | Výchozí |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Ověří, jestli data v **úložišti objektů BLOB v Azure** splňují požadavky na minimální velikost (v megabajtech). |Azure Blob Storage |No |Není k dispozici |
| minimumRows |Ověří, jestli data v **databázi SQL Azure** nebo **tabulce Azure** obsahují minimální počet řádků. |<ul><li>Azure SQL Database</li><li>Tabulka Azure</li></ul> |No |Není k dispozici |

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

### <a name="external-datasets"></a>Externí datové sady
Externí datové sady jsou ty, které nejsou vytvořené spuštěným kanálem v datové továrně. Pokud je datová sada označená jako **externí**, může být definována zásada **ExternalData** , která má vliv na chování dostupnosti řezu datové sady.

Pokud není datová sada vytvářena Data Factory, měla by být označena jako **externí**. Toto nastavení se obecně vztahuje na vstupy první aktivity v kanálu, pokud se nepoužívá aktivita nebo řetězení kanálu.

| Název | Popis | Povinné | Výchozí hodnota |
| --- | --- | --- | --- |
| Zpoždění datadelay |Čas pro zpoždění kontroly dostupnosti externích dat pro daný řez. Pomocí tohoto nastavení můžete například zpozdit hodinovou kontrolu.<br/><br/>Nastavení platí pouze pro aktuální čas. Například pokud je 1:00 PM hned teď a tato hodnota je 10 minut, ověření začíná na 1:10 odp.<br/><br/>Všimněte si, že toto nastavení nemá vliv na řezy v minulosti. Řezy s **časem ukončení řezu**  +  :**datadelay**  <  se**teď** zpracovávají bez zpoždění.<br/><br/>Čas větší než 23:59 hodin by měl být zadán pomocí `day.hours:minutes:seconds` formátu. Pokud například chcete zadat 24 hodin, nepoužívejte 24:00:00. Místo toho použijte 1,00:00:00. Pokud používáte 24:00:00, je zpracována jako 24 dní (24.00:00:00). 1 den a 4 hodiny zadejte 1:04:00:00. |No |0 |
| retryInterval |Čekací doba mezi selháním a dalším pokusem. Toto nastavení platí pro aktuální čas. Pokud se předchozí pokus nezdařil, další pokus je po **retryInterval** období. <br/><br/>Pokud teď 1:00 odp., zahájíme první pokus. Pokud je doba, po kterou je první kontrola ověření dokončená, 1 minuta a operace se nezdařila, bude příští opakování v 1:00 + 1 minuta (trvání) + 1 minuta (interval opakování) = 1:02 ODP. <br/><br/>U řezů v minulosti nedochází k žádnému zpoždění. Opakování proběhne okamžitě. |No |00:01:00 (1 minuta) |
| retryTimeout |Časový limit pro každý pokus o opakování.<br/><br/>Pokud je tato vlastnost nastavená na 10 minut, mělo by se ověřování dokončit do 10 minut. Pokud ověření proběhne déle než 10 minut, vyprší časový limit opakování.<br/><br/>Pokud se všechny pokusy o vypršení časového limitu ověření vyprší, řez se označí jako **časový limit**. |No |00:10:00 (10 minut) |
| maximumRetry |Počet, kolikrát chcete ověřit dostupnost externích dat. Maximální povolená hodnota je 10. |No |3 |


## <a name="create-datasets"></a>Vytvoření datových sad
Datové sady můžete vytvořit pomocí jednoho z těchto nástrojů nebo sad SDK:

- Průvodce kopírováním
- Visual Studio
- PowerShell
- Šablona Azure Resource Manageru
- REST API
- .NET API

Podrobné pokyny k vytváření kanálů a datových sad pomocí jednoho z těchto nástrojů nebo sad SDK najdete v následujících kurzech:

- [Vytvoření kanálu s aktivitou transformace dat](data-factory-build-your-first-pipeline.md)
- [Vytvoření kanálu s aktivitou přesunu dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Po vytvoření a nasazení kanálu můžete své kanály spravovat a monitorovat pomocí oken Azure Portal nebo aplikace pro monitorování a správu. Podrobné pokyny najdete v následujících tématech:

- [Monitorování a Správa kanálů pomocí Azure Portalch oken](data-factory-monitor-manage-pipelines.md)
- [Monitorování a Správa kanálů pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Datové sady s vymezeným oborem
Můžete vytvořit datové sady, které jsou vymezeny na kanál pomocí vlastnosti **datové sady** . Tyto datové sady můžou používat jenom aktivity v rámci tohoto kanálu, ne aktivity v jiných kanálech. Následující příklad definuje kanál se dvěma datovými sadami (InputDataset-RDC a OutputDataset-RDC), který se použije v rámci kanálu.

> [!IMPORTANT]
> Datové sady s vymezeným oborem jsou podporovány pouze s jednorázovými kanály (kde **pipelineMode** je nastaven na **jednorázová**). Podrobnosti najdete v tématu [jednorázová kanál](data-factory-create-pipelines.md#onetime-pipeline) .
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
- Další informace o kanálech najdete v tématu [vytvoření kanálů](data-factory-create-pipelines.md).
- Další informace o tom, jak se naplánují a spouštějí kanály, najdete [v tématu plánování a spouštění v Azure Data Factory](data-factory-scheduling-and-execution.md).
