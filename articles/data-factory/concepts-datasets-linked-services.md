---
title: Datové sady
description: Další informace o datových sadách v Datové továrně. Datové sady představují vstupní/výstupní data.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/25/2019
ms.openlocfilehash: 33b2ca8db75acff1ce423aa50087961cce6092b2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418401"
---
# <a name="datasets-in-azure-data-factory"></a>Datové sady v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-create-datasets.md)
> * [Aktuální verze](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Tento článek popisuje, co jsou datové sady, jak jsou definovány ve formátu JSON a jak se používají v kanálech Azure Data Factory.

Pokud jste s Toutovou službou pro data tezi te, [přečtěte si přehled v tématu Úvod do Azure Data Factory.](introduction.md)

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. **Kanál** je logické seskupení **aktivit,** které společně provádějí úkol. Aktivity v kanálu definují akce, které se mají provést s vašimi daty. **Datová sada** je nyní pojmenované zobrazení dat, které jednoduše odkazuje nebo odkazuje na data, která chcete použít ve svých **aktivitách** jako vstupy a výstupy. Datové sady identifikují data v rámci různých úložišť dat, jako jsou tabulky, soubory, složky a dokumenty. Datová sada objektu blob Azure například určuje kontejner objektů blob a složku v úložišti objektů blob, ze kterých by měla aktivita číst data.

Před vytvořením datové sady je nutné vytvořit [**propojenou službu,**](concepts-linked-services.md) která propojí úložiště dat s továrnou na data. Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Přemýšlejte o tom tímto způsobem; datová sada představuje strukturu dat v rámci propojených úložišť dat a propojená služba definuje připojení ke zdroji dat. Například propojené služby Azure Storage propojuje účet úložiště do datové továrny. Datová sada Azure Blob představuje kontejner objektů blob a složku v rámci tohoto účtu úložiště Azure, který obsahuje vstupní objekty BLOB, které mají být zpracovány.

Zde je ukázkový scénář. Chcete-li zkopírovat data z úložiště objektů Blob do databáze SQL, vytvořte dvě propojené služby: Azure Storage a Azure SQL Database. Potom vytvořte dvě datové sady: datovou sadu Azure Blob (která odkazuje na propojenou službu Azure Storage) a datovou sadu Azure SQL Table (která odkazuje na propojenou službu Azure SQL Database). Služby propojené s Azure Storage a Azure SQL Database obsahují připojovací řetězce, které Data Factory používá za běhu k připojení k úložišti Azure Storage a Azure SQL Database. Datová sada Objektů blob Azure určuje kontejner objektů blob a složku objektů blob, která obsahuje vstupní objekty BLOB v úložišti objektů Blob. Datová sada Tabulka Azure SQL určuje tabulku SQL v databázi SQL, do které se mají data kopírovat.

Následující diagram znázorňuje vztahy mezi kanálem, aktivitou, datovou sadou a propojenou službou v datové továrně:

![Vztah mezi kanálem, aktivitou, datovou sadou, propojenými službami](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Datová sada JSON
Datová sada v datové továrně je definována v následujícím formátu JSON:

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
Následující tabulka popisuje vlastnosti ve výše uvedeném json:

Vlastnost | Popis | Požaduje se |
-------- | ----------- | -------- |
jméno | Název datové sady. Viz [Azure Data Factory – pravidla pojmenování](naming-rules.md). |  Ano |
type | Typ datové sady. Zadejte jeden z typů podporovaných data factory (například: AzureBlob, AzureSqlTable). <br/><br/>Podrobnosti naleznete v tématu [Typy datových sad](#dataset-type). | Ano |
– struktura | Schéma datové sady. Podrobnosti naleznete v [tématu Schéma datové sady](#dataset-structure-or-schema). | Ne |
typeProperties | Vlastnosti typu se liší pro každý typ (například: Azure Blob, Tabulka Azure SQL). Podrobnosti o podporovaných typech a jejich vlastnostech naleznete v [tématu Typ datové sady](#dataset-type). | Ano |

### <a name="data-flow-compatible-dataset"></a>Datová sada kompatibilní s tokem dat



Seznam typů datových sad, které jsou kompatibilní [s tokem dat,](concepts-data-flow-overview.md) najdete v seznamu podporovaných [typů datových](#dataset-type) sad. Datové sady, které jsou kompatibilní pro tok dat, vyžadují pro transformace jemně odstupňované definice datových sad. Definice JSON se tedy mírně liší. Místo vlastnosti _structure_ mají datové sady, které jsou kompatibilní s tokem dat, vlastnost _schématu._

V toku dat se datové sady používají ve zdrojových a jímacích transformacích. Datové sady definují základní schémata dat. Pokud data nemá žádné schéma, můžete použít posun schématu pro zdroj a jímky. Schéma v datové sadě představuje fyzický datový typ a obrazec.

Definováním schématu z datové sady získáte související datové typy, formáty dat, umístění souboru a informace o připojení z přidružené propojené služby. Metadata z datových sad se zobrazí ve zdrojové transformaci jako zdrojová *projekce*. Projekce ve zdrojové transformaci představuje data toku dat s definovanými názvy a typy.

Při importu schématu datové sady Tok dat vyberte tlačítko **Importovat schéma** a zvolte import ze zdroje nebo z místního souboru. Ve většině případů budete importovat schéma přímo ze zdroje. Ale pokud již máte místní soubor schématu (parketový soubor nebo CSV se záhlavími), můžete nasměrovat Data Factory založit schéma na tomto souboru.


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

Následující tabulka popisuje vlastnosti ve výše uvedeném json:

Vlastnost | Popis | Požaduje se |
-------- | ----------- | -------- |
jméno | Název datové sady. Viz [Azure Data Factory – pravidla pojmenování](naming-rules.md). |  Ano |
type | Typ datové sady. Zadejte jeden z typů podporovaných data factory (například: AzureBlob, AzureSqlTable). <br/><br/>Podrobnosti naleznete v tématu [Typy datových sad](#dataset-type). | Ano |
Schématu | Schéma datové sady. Podrobnosti naleznete v [tématu datové sady kompatibilní s tokem dat](#dataset-type). | Ne |
typeProperties | Vlastnosti typu se liší pro každý typ (například: Azure Blob, Tabulka Azure SQL). Podrobnosti o podporovaných typech a jejich vlastnostech naleznete v [tématu Typ datové sady](#dataset-type). | Ano |


## <a name="dataset-example"></a>Příklad datové sady
V následujícím příkladu představuje datová sada tabulku s názvem MyTable v databázi SQL.

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

- typ je nastavena na AzureSqlTable.
- vlastnost tableName type (specifická pro typ AzureSqlTable) je nastavena na hodnotu MyTable.
- linkedServiceName odkazuje na propojenou službu typu AzureSqlDatabase, která je definována v dalším fragmentu JSON.

## <a name="dataset-type"></a>Typ datové sady
Existuje mnoho různých typů datových sad v závislosti na úložišti dat, které používáte. Seznam dat uložených podporovanou totoovou továrnou dat najdete v článku [přehled konektorů.](connector-overview.md) Kliknutím na úložiště dat se dozvíte, jak vytvořit propojenou službu a datovou sadu pro toto úložiště dat.

V příkladu v předchozí části je typ datové sady nastaven na **AzureSqlTable**. Podobně pro datovou sadu Azure Blob je typ datové sady nastaven na **AzureBlob**, jak je znázorněno v následujícím JSON:

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

## <a name="dataset-structure-or-schema"></a>Struktura datové sady nebo schéma
Datové sady **oddílu** **struktury** nebo schématu (kompatibilní s tokem dat) jsou volitelné. Definuje schéma datové sady obsahující kolekci názvů a datových typů sloupců. Oddíl struktura slouží k zadání informací o typu, které se používají k převodu typů a mapování sloupců ze zdroje do cíle.

Každý sloupec ve struktuře obsahuje následující vlastnosti:

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
jméno | Název sloupce. | Ano
type | Datový typ sloupce. Data Factory podporuje následující dočasné datové typy jako povolené hodnoty: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset a Timespan** | Ne
jazyková verze | . Jazyková verze založená na NET, která se `Datetime` `Datetimeoffset`má použít, pokud je typ typu .NET: nebo . Výchozí formát je `en-us`. | Ne
formát | Formátovací řetězec, který se použije v `Datetime` případě, že se jedná o typ .NET: nebo `Datetimeoffset`. Informace o formátování data a času naleznete v [podrobnostech](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) o formátování data. | Ne

### <a name="example"></a>Příklad
V následujícím příkladu předpokládejme, že zdrojová data objektu Blob jsou ve formátu CSV a obsahují tři sloupce: userid, name a lastlogindate. Jsou typu Int64, String a Datetime s vlastním datetime formát pomocí zkrácené francouzské názvy pro den v týdnu.

Definujte strukturu datové sady objektu Blob takto spolu s definicemi typů pro sloupce:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Doprovodné materiály

Následující pokyny vám pomohou pochopit, kdy zahrnout informace o struktuře a co zahrnout do části **struktura.** Další informace o tom, jak data factory mapuje zdrojová data k jímce a kdy zadat informace o struktuře ze [schématu a mapování typů](copy-activity-schema-and-type-mapping.md).

- **U zdrojů dat silného schématu**zadejte oddíl struktury pouze v případě, že chcete, aby se sloupce zdroje mapy utajily a jejich názvy nejsou stejné. Tento druh strukturovaného zdroje dat ukládá schéma dat a informace o typu spolu se samotnými daty. Příklady strukturovaných zdrojů dat zahrnují SQL Server, Oracle a Azure SQL Database.<br/><br/>Vzhledem k tomu, že informace o typu jsou již k dispozici pro strukturované zdroje dat, neměli byste při zahrnutí oddílu struktury zahrnout informace o typu.
- **Pro žádné/slabé zdroje dat schématu, například textový soubor v úložišti objektů blob**, zahrnout strukturu, když datová sada je vstup pro aktivitu kopírování a datové typy zdrojové datové sady by měly být převedeny na nativní typy pro jímky. A zahrnout strukturu, pokud chcete mapovat zdrojové sloupce do jímací sloupce..

## <a name="create-datasets"></a>Vytvoření datových sad
Datové sady můžete vytvořit pomocí jednoho z těchto nástrojů nebo sad SDK: [rozhraní .NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [ROZHRANÍ REST API](quickstart-create-data-factory-rest-api.md), Šablona Správce prostředků Azure a Portál Azure.

## <a name="current-version-vs-version-1-datasets"></a>Aktuální verze vs. datové sady verze 1

Zde jsou některé rozdíly mezi datovými sadami Data Factory a Data Factory verze 1:

- Externí vlastnost není v aktuální verzi podporována. Je nahrazen [spoušť](concepts-pipeline-execution-triggers.md).
- Vlastnosti zásad a dostupnosti nejsou v aktuální verzi podporovány. Čas spuštění kanálu závisí na [aktivačních událostech](concepts-pipeline-execution-triggers.md).
- Datové sady s vymezeným oborem (datové sady definované v kanálu) nejsou v aktuální verzi podporovány.

## <a name="next-steps"></a>Další kroky
V následujícím kurzu naleznete podrobné pokyny pro vytváření kanálů a datových sad pomocí jednoho z těchto nástrojů nebo sad SDK.

- [Rychlý start: Vytvoření datové továrny pomocí rozhraní .NET](quickstart-create-data-factory-dot-net.md)
- [Úvodní příručka: vytvoření datové továrny pomocí PowerShellu](quickstart-create-data-factory-powershell.md)
- [Úvodní příručka: vytvoření datové továrny pomocí rozhraní REST API](quickstart-create-data-factory-rest-api.md)
- [Úvodní příručka: vytvoření datové továrny pomocí portálu Azure](quickstart-create-data-factory-portal.md)
