---
title: Datové sady a propojené služby v Azure Data Factory | Microsoft Docs
description: Další informace o datových sad a propojené služby ve službě Data Factory. Propojené služby propojují úložiště výpočetní nebo dat do data factory. Datové sady představují vstupní a výstupní data.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: ''
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: d88e4a068841f68feffb094b2aa07faaf5a6a8cc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="datasets-and-linked-services-in-azure-data-factory"></a>Datové sady a propojené služby v Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-create-datasets.md)
> * [Verze 2 – Preview](concepts-datasets-linked-services.md)

Tento článek popisuje, jaké datové sady se, jak jsou definovány ve formátu JSON, a způsobu jejich použití v Azure Data Factory V2 kanálů. 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [datové sady v V1 objekt pro vytváření dat](v1/data-factory-create-datasets.md).

Pokud jste nový objekt pro vytváření dat, najdete v části [Úvod do Azure Data Factory](introduction.md) Přehled. 

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. A **kanálu** je logické seskupení **aktivity** společně provádějí úlohy. Aktivity v kanálu definují akce, které se mají provést s vašimi daty. Může například pomocí aktivity kopírování zkopírovat data z místního serveru SQL do úložiště objektů Blob v Azure. Pak můžete použít aktivitu Hive, která se spouští skript Hive v clusteru Azure HDInsight pro zpracování dat z úložiště objektů Blob nevytvořila výstupní data. Nakonec můžete použít druhý aktivity kopírování zkopírovat výstupní data do Azure SQL Data Warehouse, na které business intelligence (BI), vytváření sestav jsou integrované řešení. Další informace o kanály a aktivity, najdete v části [kanály a aktivity](concepts-pipelines-activities.md) v Azure Data Factory.

Nyní **datovou sadu** pojmenované zobrazení obsahuje data, která jednoduše bodů nebo odkazuje na data, kterou chcete použít v vaše **aktivity** jako vstupy a výstupy. Datové sady identifikují data v rámci různých úložišť dat, jako jsou tabulky, soubory, složky a dokumenty. Datová sada objektu blob Azure například určuje kontejner objektů blob a složku v úložišti objektů blob, ze kterých by měla aktivita číst data.

Než vytvoříte datové sady, musíte vytvořit **propojená služba** propojit data store k objektu pro vytváření dat. Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Považujte ho tímto způsobem; Datová sada reprezentuje strukturu dat v rámci propojených úložištích dat a propojené služby definuje připojení ke zdroji dat. Například Azure Storage propojená služba propojuje účet úložiště do služby data factory. Datové sadě služby Azure Blob představuje kontejner objektů blob a složky v rámci tohoto účtu úložiště Azure, který obsahuje vstupní objekty BLOB ke zpracování.

Tady je ukázkový scénář. Ke zkopírování dat z úložiště objektů Blob k databázi SQL, vytvoříte dvě propojené služby: úložiště Azure a Azure SQL Database. Pak vytvořte dvě datové sady: datovou sadu objektu Blob Azure, (který odkazuje na službu Azure Storage, propojené) a tabulky SQL Azure datovou sadu (odkazuje na službu Azure SQL Database propojené). Azure Storage a Azure SQL Database propojené služby obsahovat připojovací řetězce, které objekt pro vytváření dat používá za běhu k připojení k Azure Storage a Azure SQL Database, v uvedeném pořadí. Datovou sadu objektu Blob Azure Určuje kontejner objektů blob a objektů blob složku, která obsahuje vstupní objekty BLOB ve službě Blob storage. Datová sada tabulky SQL Azure Určuje tabulku SQL ve vaší databázi SQL, na které má zkopírovat data.

Následující obrázek znázorňuje vztahy mezi kanálu, aktivity, datové sady a propojené služby objektu pro vytváření dat:

![Vztah mezi kanálu, aktivity, datové sady, propojených služeb](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>JSON propojené služby
V datové továrně propojené služby je definováno ve formátu JSON následujícím způsobem:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Následující tabulka popisuje vlastnosti v výše uvedený kód JSON:

Vlastnost | Popis | Požaduje se |
-------- | ----------- | -------- |
jméno | Název propojené služby. V tématu [Azure Data Factory - pravidla po pojmenování](naming-rules.md). |  Ano |
type | Typ propojené služby. Příklad: azurestorage. (úložiště dat) nebo AzureBatch (výpočetní). Najdete popis rámci typeProperties. | Ano |
typeProperties | Vlastnosti typu se liší u každé úložiště dat nebo výpočetní. <br/><br/> Pro podporované data uložit typy a jejich vlastnosti typu, najdete v článku [typ dataset](#dataset-type) tabulky v tomto článku. Přejděte na článek konektor úložiště dat, další informace o typu vlastnosti specifické pro úložiště dat. <br/><br/> Typy podporované výpočtů a jejich vlastnosti typu najdete v tématu [výpočetní propojené služby](compute-linked-services.md). | Ano |
connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. | Ne

## <a name="linked-service-example"></a>Propojenou službu příkladu
Následující propojené služby je propojenou službu úložiště Azure. Všimněte si, že je typ nastaven na azurestorage. Typ vlastnosti pro službu Azure Storage, propojené zahrnují připojovací řetězec. Služba Data Factory používá tento připojovací řetězec k připojení k úložišti dat za běhu. 

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-json"></a>JSON datové sady
Datové sady ve službě Data Factory je definováno ve formátu JSON následujícím způsobem:

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
Následující tabulka popisuje vlastnosti v výše uvedený kód JSON:

Vlastnost | Popis | Požaduje se |
-------- | ----------- | -------- |
jméno | Název datové sady. V tématu [Azure Data Factory - pravidla po pojmenování](naming-rules.md). |  Ano |
type | Typ datové sady. Zadejte jeden z typů podporovaných službou Data Factory (například: AzureBlob, AzureSqlTable). <br/><br/>Podrobnosti najdete v tématu [datovou sadu typy](#dataset-types). | Ano |
Struktura | Schéma datové sady. Podrobnosti najdete v tématu [strukturu datové sady](#dataset-structure). | Ne |
typeProperties | Vlastnosti typu se liší pro jednotlivé typy (například: Azure Blob, tabulka Azure SQL). Podrobnosti o svých vlastnostech a podporované typy najdete v tématu [typ sady](#dataset-type). | Ano |

## <a name="dataset-example"></a>Příklad datové sady
V následujícím příkladu představuje datovou sadu tabulku s názvem MyTable v databázi SQL.

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
- Název tabulky typu (specifické pro typ AzureSqlTable) je nastavena na MyTable.
- linkedServiceName odkazuje na propojené služby typu azuresqldatabase., která je definována v další fragmentu kódu JSON.

## <a name="dataset-type"></a>Typ sady
Existuje mnoho různých typů datových sad, v závislosti na úložiště dat, které používáte. Najdete v následující tabulce najdete seznam úložišť dat podporovaných službou Data Factory. Klikněte na úložiště dat pro informace o vytvoření propojené služby a sadu dat pro toto datové úložiště.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

V příkladu v předchozí části, typ datové sady je nastavený na **AzureSqlTable**. Pro datové sadě služby Azure Blob podobně typ datové sady je nastaven na **AzureBlob**, jak je znázorněno v následujícím kódu JSON:

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
## <a name="dataset-structure"></a>Struktura datové sady
**Struktura** část je nepovinná. Definuje schéma datové sady ve obsahující kolekci názvů a typy dat sloupců. V části struktura použijete k poskytování informací o typu, který se používá k převést typy a mapování sloupců ze zdroje do cíle.

Každý sloupec ve struktuře obsahuje následující vlastnosti:

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
jméno | Název sloupce. | Ano
type | Datový typ sloupce. Objekt pro vytváření dat podporuje následující typy průběžných dat jako Povolené hodnoty: **Int16, Int32, Int64, jedním, Double, Decimal, Byte [], logická hodnota, řetězec, Guid, Datetime, Datetimeoffset a časový interval** | Ne
Jazyková verze | . Na základě NET jazykovou verzi, která se použije, když je typ typ formátu .NET: `Datetime` nebo `Datetimeoffset`. Výchozí hodnota je `en-us`. | Ne
Formát | Řetězec, který se má použít, když je typ typ formátu .NET formátu: `Datetime` nebo `Datetimeoffset`. Odkazovat na [vlastní řetězců data a času formát](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings) k formátování data a času. | Ne

### <a name="example"></a>Příklad:
V následujícím příkladu předpokládejme, že zdroj dat objektů Blob je ve formátu CSV a obsahuje tři sloupce: ID uživatele, název a lastlogindate. Jsou typu Int64, String a datum a čas ve formátu data a času vlastní pomocí zkrácené francouzštině názvy den v týdnu.

Definujte strukturu datové sady objektu Blob takto společně s definic typů pro sloupce:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Doprovodné materiály

Následující pokyny vám pomohou pochopit, kdy se mají zahrnout informace o struktuře a co mají být zahrnuty **struktura** části. Další informace o způsobu mapování objektu pro vytváření dat zdrojových dat za účelem jímky a kdy k zadání informací o struktuře z [schéma a mapování typu](copy-activity-schema-and-type-mapping.md).

- **Zdroje dat silné schématu**, zadejte v části struktura pouze v případě, že chcete namapovat zdrojové sloupce na jímky sloupců a jejich názvy nejsou stejné. Tento druh zdroj strukturovaných dat ukládá informace schématu a typu dat společně s samotná data. Příklady strukturovaných dat zdroje: SQL Server, Oracle a Azure SQL Database.<br/><br/>Protože je již k dispozici pro strukturovaná data zdroje informací o typu, by neměla zahrnovat informace o typu, pokud obsahovat části struktura.
- **Pro žádný/weak schématu zdroje dat například textového souboru v úložišti objektů blob**, zahrnout struktura je datová sada vstupem pro aktivitu kopírování a datové typy sady zdroje dat mají být převedeny na nativní typy pro jímky. A pokud chcete namapovat zdrojové sloupce na jímky sloupce zahrnovat struktura...

## <a name="create-datasets"></a>Vytvoření datových sad
Datové sady můžete vytvořit pomocí jedné z těchto nástrojů nebo sady SDK: [.NET API](quickstart-create-data-factory-dot-net.md), [prostředí PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), šablony Azure Resource Manageru a portálu Azure

## <a name="v1-vs-v2-datasets"></a>V1 vs. Datové sady v2

Zde jsou některé rozdíly mezi datové sady objektu pro vytváření dat v1 a v2: 

- Vlastnost external nepodporuje v2. Je nahrazena [aktivační událost](concepts-pipeline-execution-triggers.md).
- Zásady a dostupnosti vlastnosti nejsou podporovány u V2. Počáteční čas pro kanál závisí na [aktivační události](concepts-pipeline-execution-triggers.md).
- Oboru datové sady (datové sady definované v kanálu) nejsou podporovány u V2. 

## <a name="next-steps"></a>Další postup
Projděte si následující kurz podrobné pokyny pro vytváření kanálů a datové sady pomocí jedné z těchto nástrojů nebo sady SDK. 

- [Rychlý start: Vytvoření datové továrny pomocí rozhraní .NET](quickstart-create-data-factory-dot-net.md)
- [Rychlý úvod: Vytvořte objekt pro vytváření dat pomocí prostředí PowerShell](quickstart-create-data-factory-powershell.md)
- [Rychlý úvod: Vytvořte objekt pro vytváření dat pomocí rozhraní REST API](quickstart-create-data-factory-rest-api.md)
- Rychlý úvod: Vytvořte objekt pro vytváření dat pomocí portálu Azure
