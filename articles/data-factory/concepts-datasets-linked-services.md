---
title: Datové sady ve službě Azure Data Factory | Dokumentace Microsoftu
description: Další informace o datové sady ve službě Data Factory. Datové sady představují vstupní a výstupní data.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: shlo
ms.openlocfilehash: 6b74f217d296b5de8886f608b1bc92e908b5d8b4
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866481"
---
# <a name="datasets-in-azure-data-factory"></a>Datové sady ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-create-datasets.md)
> * [Aktuální verze](concepts-datasets-linked-services.md)

Tento článek popisuje, jaké datové sady se, jak jsou definované ve formátu JSON, a jak se používají v kanály Azure Data Factory.

Pokud do služby Data Factory začínáte, přečtěte si téma [Úvod do služby Azure Data Factory](introduction.md) Přehled.

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. A **kanálu** je logické seskupení **aktivity** , které dohromady provádějí určitou úlohu. Aktivity v kanálu definují akce, které se mají provést s vašimi daty. Nyní **datovou sadu** je pojmenované zobrazení dat, která jednoduše body nebo odkazuje na data, kterou chcete použít v vaše **aktivity** jako vstupy a výstupy. Datové sady identifikují data v rámci různých úložišť dat, jako jsou tabulky, soubory, složky a dokumenty. Datová sada objektu blob Azure například určuje kontejner objektů blob a složku v úložišti objektů blob, ze kterých by měla aktivita číst data.

Než vytvoříte datovou sadu, je třeba vytvořit [ **propojená služba** ](concepts-linked-services.md) k propojení vašeho úložiště dat do služby data factory. Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Můžete ho chápat takto; Datová sada reprezentuje strukturu těchto dat v rámci propojených úložištích dat a propojená služba definuje připojení ke zdroji dat. Například Azure Storage propojená služba propojuje účet úložiště do služby data factory. Datová sada služby Azure Blob představuje kontejner objektů blob a složku v rámci tohoto účtu úložiště Azure obsahující vstupní objekty BLOB ke zpracování.

Tady je ukázkový scénář. Ke zkopírování dat z úložiště objektů Blob do služby SQL database, vytvoříte dvě propojené služby: Azure Storage a Azure SQL Database. Vytvořte dvě datové sady: Azure Blob datovou sadu (odkazuje propojenou službu Azure Storage) a datová sada tabulky SQL Azure (což odkazuje na službu Azure SQL Database, která je propojená). Azure Storage a Azure SQL Database propojené služby obsahují připojovací řetězce, které služby Data Factory používá za běhu pro připojení k Azure Storage a Azure SQL Database, v uvedeném pořadí. Datová sada Azure Blob Určuje kontejner objektů blob a složka objektů blob obsahující vstupní objekty BLOB v úložišti objektů Blob. Datová sada tabulky SQL Azure Určuje tabulku SQL ve službě SQL database, ke které se mají zkopírovat data.

Následující diagram znázorňuje vztahy mezi kanálu, aktivit, datové sady a propojené služby ve službě Data Factory:

![Vztah mezi kanálu, aktivita, datové sady, propojené služby](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>JSON datové sady
Datové sady ve službě Data Factory je definován v následujícím formátu JSON:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
Následující tabulka popisuje vlastnosti v výše uvedený text JSON:

Vlastnost | Popis | Požaduje se |
-------- | ----------- | -------- |
jméno | Název datové sady. Zobrazit [Azure Data Factory – pravidla pojmenování](naming-rules.md). |  Ano |
type | Typ datové sady. Zadejte jeden z typů podporovaných službou Data Factory (například: AzureBlob, AzureSqlTable). <br/><br/>Podrobnosti najdete v tématu [typů datových sad](#dataset-type). | Ano |
Struktura | Schéma datové sady. Podrobnosti najdete v tématu [schéma datové sady](#dataset-structure-or-schema). | Ne |
typeProperties | Vlastnosti typu se liší pro každý typ (například: Azure Blob, tabulky Azure SQL). Podrobnosti o podporovaných typech a jejich vlastností najdete v tématu [typ datové sady](#dataset-type). | Ano |

### <a name="data-flow-compatible-dataset"></a>Kompatibilní datové sady toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Zobrazit [podporované typy datovou sadu](#dataset-type) seznam typů datových sad, které jsou [toku dat](concepts-data-flow-overview.md) kompatibilní. Datové sady, které jsou kompatibilní pro tok dat vyžadují definicích podrobných datových sad pro transformace. Definice JSON se díky tomu se mírně liší. Místo _struktura_ mít vlastnosti, datových sad, které se předávají Data kompatibilní _schématu_ vlastnost.

V toku dat datové sady se používají v transformace zdroje a jímky. Datové sady vytvoří definici schémat základní data. Pokud vaše data bez schémat, můžete pro zdroje a jímky odchylek schématu. Schéma v datové sadě představuje fyzický datový typ a tvar.

Definujete schéma z datové sady, získáte souvisejících datových typů, formáty dat, umístění souboru a informace o připojení z přidružené propojené služby. Metadata z datové sady se zobrazí ve vaší zdrojové transformace jako zdroj *projekce*. Projekce v transformaci zdroj představuje data toku dat s definované názvy a typy.

Při importu schématu datové sady se předávají Data, vyberte **importovat schéma** tlačítko a zvolte možnost importovat ze zdroje nebo z místního souboru. Ve většině případů budete importovat schéma přímo ze zdroje. Ale pokud již máte místní schéma souboru (soubory Parquet nebo CSV se záhlavími), může směrovat Data Factory na základní schéma na tento soubor.


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```

Následující tabulka popisuje vlastnosti v výše uvedený text JSON:

Vlastnost | Popis | Požaduje se |
-------- | ----------- | -------- |
jméno | Název datové sady. Zobrazit [Azure Data Factory – pravidla pojmenování](naming-rules.md). |  Ano |
type | Typ datové sady. Zadejte jeden z typů podporovaných službou Data Factory (například: AzureBlob, AzureSqlTable). <br/><br/>Podrobnosti najdete v tématu [typů datových sad](#dataset-type). | Ano |
schema | Schéma datové sady. Podrobnosti najdete v tématu [toku dat kompatibilní datové sady](#dataset-type). | Ne |
typeProperties | Vlastnosti typu se liší pro každý typ (například: Azure Blob, tabulky Azure SQL). Podrobnosti o podporovaných typech a jejich vlastností najdete v tématu [typ datové sady](#dataset-type). | Ano |


## <a name="dataset-example"></a>Příklad datové sady
V následujícím příkladu datová sada představuje tabulku s názvem MyTable ve službě SQL database.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Je třeba počítat s následujícím:

- Typ je nastaven na AzureSqlTable.
- tableName typ (specifické pro typ AzureSqlTable) je nastavena na MyTable.
- Vlastnost linkedServiceName odkazuje na propojenou službu typu AzureSqlDatabase, která je definována v následující fragment kódu JSON.

## <a name="dataset-type"></a>Typ datové sady
Existuje mnoho různých typů datových sad, v závislosti na úložišti dat, které používáte. Najdete v následující tabulce najdete seznam úložišť dat podporovaných službou Data Factory. Klikněte na úložiště dat se informace o vytvoření propojené služby a datové sady pro toto úložiště dat.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-dataflow.md)]

V příkladu v předchozí části, typ datové sady je nastaven na **AzureSqlTable**. U datové sady objektů Blob v Azure, podobně, typ datové sady je nastaven na **AzureBlob**, jak je znázorněno v následujícím kódu JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```

## <a name="dataset-structure-or-schema"></a>Struktury datové sady nebo schématu
**Struktura** části nebo **schématu** (se předávají Data kompatibilní) části datové sady je volitelný. Definuje schéma datové sady pomocí obsahující kolekci názvů a datové typy sloupců. Pomocí části struktury poskytují informace o typu, který se používá k převodu typů a namapovat sloupce ze zdroje do cíle.

Všechny sloupce struktury obsahují následující vlastnosti:

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
jméno | Název sloupce. | Ano
type | Datový typ sloupce. Data Factory podporuje následující typy dat dočasné jako Povolené hodnoty: **Int16, Int32, Int64, Single, Double, Decimal, bajtů [], datový typ Boolean, řetězec, Guid, data a času, Datetimeoffset a časový interval** | Ne
Jazyková verze | . NET jazykovou verzi na základě používané pro typ je typ .NET: `Datetime` nebo `Datetimeoffset`. Výchozí formát je `en-us`. | Ne
formát | Formátovací řetězec se použije, když typ je typ .NET: `Datetime` nebo `Datetimeoffset`. Odkazovat na [vlastní data a řetězce formátu časových](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) o tom, jak formátovat datum a čas. | Ne

### <a name="example"></a>Příklad:
V následujícím příkladu předpokládejme, že zdroj dat objektu Blob je ve formátu CSV a obsahuje tři sloupce: ID uživatele, název a lastlogindate. Jsou typu Int64, řetězce a datum a čas ve formátu vlastní data a času pomocí zkrácené názvy francouzské pro den v týdnu.

Definování struktury datové sady objektů Blob v následujícím způsobem spolu s definic typů pro sloupce:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Doprovodné materiály

Následující pokyny vám pochopit, kdy se mají zahrnout informace o struktuře a co mají být zahrnuty **struktura** oddílu. Další informace o způsobu, jakým objekt pro vytváření dat mapuje data zdroje do jímky a kdy se má určit informace o struktuře z [mapování typů a schémat](copy-activity-schema-and-type-mapping.md).

- **Pro silné schématu zdroje dat**, zadejte v části struktury jenom v případě, že chcete, aby mapování sloupců zdroje do jímky sloupců a jejich názvy se neshodují. Tento druh zdroje strukturovaná data ukládá informace schématu a typu dat spolu s vlastní data. Příklady zdrojů strukturovaných dat: SQL Server, Oracle a Azure SQL Database.<br/><br/>Informace o typu je již k dispozici pro strukturované datové zdroje, by neměl obsahovat informace o typu, pokud zahrnete oddíl struktury.
- **Č/weak schématu zdroje dat třeba textového souboru v úložišti objektů blob**, patří struktura při datová sada je vstupní hodnota pro aktivitu kopírování a datové typy zdrojové datové sady mají být převedeny na nativní typy pro jímku. A zahrnout strukturu, pokud chcete mapování sloupců zdroje do jímky sloupce...

## <a name="create-datasets"></a>Vytvoření datových sad
Můžete vytvářet datové sady pomocí jednoho z těchto nástrojů nebo sad SDK: [rozhraní .NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [rozhraní REST API](quickstart-create-data-factory-rest-api.md), šablony Azure Resource Manageru a webu Azure portal

## <a name="current-version-vs-version-1-datasets"></a>Aktuální verze a verze 1 datové sady

Tady jsou některé rozdíly mezi objektu pro vytváření dat a datové sady Data Factory verze 1:

- Vlastnost external se nepodporuje v aktuální verzi. Se nahrazuje [aktivační událost](concepts-pipeline-execution-triggers.md).
- Vlastnosti zásad a dostupnosti nejsou podporovány v aktuální verzi. Počáteční čas pro kanál závisí na [triggery](concepts-pipeline-execution-triggers.md).
- Datové sady s vymezeným oborem (datové sady, definované v kanálu) nejsou podporovány v aktuální verzi.

## <a name="next-steps"></a>Další postup
Projděte si následující kurz podrobné pokyny pro vytváření kanálů a datových sad pomocí jedné z těchto nástrojů nebo sad SDK.

- [Rychlý start: Vytvoření datové továrny pomocí rozhraní .NET](quickstart-create-data-factory-dot-net.md)
- [Rychlý start: vytvoření datové továrny pomocí prostředí PowerShell](quickstart-create-data-factory-powershell.md)
- [Rychlý start: vytvoření datové továrny pomocí rozhraní REST API](quickstart-create-data-factory-rest-api.md)
- [Rychlý start: vytvoření datové továrny pomocí webu Azure portal](quickstart-create-data-factory-portal.md)
