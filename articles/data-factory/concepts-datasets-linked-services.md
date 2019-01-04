---
title: Datové sady a propojené služby v Azure Data Factory | Dokumentace Microsoftu
description: Další informace o datové sady a propojené služby ve službě Data Factory. Propojené služby propojují úložiště výpočetní/dat do služby data factory. Datové sady představují vstupní a výstupní data.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: d5cf4005ad50c9c75f22b2fa2719925afbe69f26
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38581262"
---
# <a name="datasets-and-linked-services-in-azure-data-factory"></a>Datové sady a propojené služby v Azure Data Factory 
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-create-datasets.md)
> * [Aktuální verze](concepts-datasets-linked-services.md)

Tento článek popisuje, jaké datové sady se, jak jsou definované ve formátu JSON, a jak se používají v kanály Azure Data Factory. 

Pokud do služby Data Factory začínáte, přečtěte si téma [Úvod do služby Azure Data Factory](introduction.md) Přehled. 

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. A **kanálu** je logické seskupení **aktivity** , které dohromady provádějí určitou úlohu. Aktivity v kanálu definují akce, které se mají provést s vašimi daty. Například můžete použít aktivitu kopírování ke kopírování dat z místního SQL serveru do úložiště objektů Blob v Azure. Potom můžete použít aktivitu Hivu, která spouští skript Hive v clusteru Azure HDInsight ke zpracování dat z úložiště objektů Blob a generuje výstupní data. Nakonec můžete použít druhou aktivitu kopírování ke kopírování dat výstup do Azure SQL Data Warehouse, na které business intelligence (BI), vytváření sestav jsou integrované řešení. Další informace o kanálech a aktivitách najdete v tématu [kanály a aktivity](concepts-pipelines-activities.md) ve službě Azure Data Factory.

Nyní **datovou sadu** je pojmenované zobrazení dat, která jednoduše body nebo odkazuje na data, kterou chcete použít v vaše **aktivity** jako vstupy a výstupy. Datové sady identifikují data v rámci různých úložišť dat, jako jsou tabulky, soubory, složky a dokumenty. Datová sada objektu blob Azure například určuje kontejner objektů blob a složku v úložišti objektů blob, ze kterých by měla aktivita číst data.

Než vytvoříte datovou sadu, je třeba vytvořit **propojená služba** k propojení vašeho úložiště dat do služby data factory. Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Můžete ho chápat takto; Datová sada reprezentuje strukturu těchto dat v rámci propojených úložištích dat a propojená služba definuje připojení ke zdroji dat. Například Azure Storage propojená služba propojuje účet úložiště do služby data factory. Datová sada služby Azure Blob představuje kontejner objektů blob a složku v rámci tohoto účtu úložiště Azure obsahující vstupní objekty BLOB ke zpracování.

Tady je ukázkový scénář. Ke zkopírování dat z úložiště objektů Blob do služby SQL database, vytvoříte dvě propojené služby: Azure Storage a Azure SQL Database. Vytvořte dvě datové sady: Datová sada objektů Blob v Azure (což odkazuje propojenou službu Azure Storage) a datová sada tabulky SQL Azure (což odkazuje na službu Azure SQL Database, která je propojená). Azure Storage a Azure SQL Database propojené služby obsahují připojovací řetězce, které služby Data Factory používá za běhu pro připojení k Azure Storage a Azure SQL Database, v uvedeném pořadí. Datová sada Azure Blob Určuje kontejner objektů blob a složka objektů blob obsahující vstupní objekty BLOB v úložišti objektů Blob. Datová sada tabulky SQL Azure Určuje tabulku SQL ve službě SQL database, ke které se mají zkopírovat data.

Následující diagram znázorňuje vztahy mezi kanálu, aktivit, datové sady a propojené služby ve službě Data Factory:

![Vztah mezi kanálu, aktivita, datové sady, propojené služby](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>JSON propojené služby
Ve službě Data Factory propojené služby je definovaná ve formátu JSON následujícím způsobem:

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

Název objektu blob.

Vlastnost | Popis | Požaduje se |
-------- | ----------- | -------- |
jméno | Název propojené služby. Zobrazit [Azure Data Factory – pravidla pojmenování](naming-rules.md). |  Ano |
type | Typ propojené služby. Příklad: AzureStorage (úložiště dat) nebo AzureBatch (compute). Zobrazit popis typeProperties. | Ano |
typeProperties | Vlastnosti typu se liší pro jednotlivé obchody dat nebo výpočetní. <br/><br/> Podporované datové úložiště typy a jejich vlastnosti typu, najdete v článku [typ datové sady](#dataset-type) tabulky v tomto článku. Přejděte na článek konektor úložiště dat. Další informace o typu vlastnosti specifické pro úložiště dat. <br/><br/> Typy podporovaných výpočetních a jejich vlastnosti typu naleznete v tématu [propojené služby Compute](compute-linked-services.md). | Ano |
connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne

## <a name="linked-service-example"></a>Například propojená služba
Tato propojená služba je propojenou službu Azure Storage. Všimněte si, že typ je nastavený na AzureStorage. Typ vlastnosti propojenou službu Azure Storage zahrnují připojovací řetězec. Služba Data Factory používá tento připojovací řetězec pro připojení k úložišti dat za běhu. 

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
Datové sady ve službě Data Factory je definovaná ve formátu JSON následujícím způsobem:

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
Název objektu blob.

Vlastnost | Popis | Požaduje se |
-------- | ----------- | -------- |
jméno | Název souboru je volitelný a malá a velká písmena. Zobrazit [Azure Data Factory – pravidla pojmenování](naming-rules.md). |  Ano |
type | Pokud není zadán název souboru, zahrnuje kopírování všech objektů BLOB v folderPath pro vstupní datovou sadu. Zadejte jeden z typů podporovaných službou Data Factory (například: AzureBlob, AzureSqlTable). <br/><br/>Podrobnosti najdete v tématu [typů datových sad](#dataset-type). | Ano |
Struktura | Slouží k určení dynamické folderPath a název souboru pro data časových řad. Podrobnosti najdete v tématu [struktury datové sady](#dataset-structure). | Ne |
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

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

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
## <a name="dataset-structure"></a>Struktury datové sady
**Struktura** část je nepovinná. Definuje schéma datové sady pomocí obsahující kolekci názvů a datové typy sloupců. Pomocí části struktury poskytují informace o typu, který se používá k převodu typů a namapovat sloupce ze zdroje do cíle.

Všechny sloupce struktury obsahují následující vlastnosti:

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
jméno | Definuje chování kopírování, pokud je zdroj BlobSource nebo systému souborů. | Ano
type | PreserveHierarchy: zachová hierarchií souborů v cílové složce. Data Factory podporuje následující typy dat dočasné jako Povolené hodnoty: **Int16, Int32, Int64, Single, Double, Decimal, bajtů [], datový typ Boolean, řetězec, Guid, data a času, Datetimeoffset a časový interval** | Ne
Jazyková verze | . NET jazykovou verzi na základě používané pro typ je typ .NET: `Datetime` nebo `Datetimeoffset`. Výchozí hodnota je `en-us`. | Ne
Formát | Formátovací řetězec se použije, když typ je typ .NET: `Datetime` nebo `Datetimeoffset`. Odkazovat na [vlastní data a řetězce formátu časových](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) o tom, jak formátovat datum a čas. | Ne

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
- Rychlý start: vytvoření datové továrny pomocí webu Azure portal
