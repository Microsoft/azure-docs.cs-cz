---
title: Přesun dat z PostgreSQL pomocí Azure Data Factory | Microsoft Docs
description: Další informace o tom, jak přesunout data z databáze PostgreSQL pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 189adf27795172bb08b52af1a9e3428d854a50a0
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046726"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Přesun dat z PostgreSQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-onprem-postgresql-connector.md)
> * [Verze 2 (aktuální verze)](../connector-postgresql.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [PostgreSQL konektor v V2](../connector-postgresql.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z místní databázi PostgreSQL. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

Z úložiště dat PostgreSQL místní může kopírovat data do úložiště dat žádné podporované jímky. Seznam úložišť dat jako jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Objekt pro vytváření dat aktuálně podporuje přesunutí dat z databáze PostgreSQL k jiným úložištím dat, ale ne pro přesun dat z jiných úložišť dat k databázi PostgreSQL. 

## <a name="prerequisites"></a>Požadavky

Služba data Factory podporuje připojení k místním zdrojům PostgreSQL pomocí Brána pro správu dat. V tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku se dozvíte o Brána pro správu dat a podrobné pokyny o nastavení brány.

Vyžaduje se brána, i když je databáze PostgreSQL hostována v virtuálního počítače Azure IaaS. Brány můžete nainstalovat na stejný virtuální počítač IaaS jako úložiště dat nebo na jiný virtuální počítač, dokud brána se může připojit k databázi.

> [!NOTE]
> V tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení nebo brány související s problémy.

## <a name="supported-versions-and-installation"></a>Podporované verze a instalaci
Brána pro správu dat pro připojení k databázi PostgreSQL, nainstalujte [Ngpsql zprostředkovatele dat pro PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) s verzí mezi 2.0.12 a 3.1.9 ve stejném systému jako brána pro správu dat. PostgreSQL verze 7.4 a vyšší je podporovaná.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště místní PostgreSQL data pomocí různých nástrojů nebo rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data. 
- Tyto nástroje můžete taky vytvořit kanál: 
    - Azure Portal
    - Visual Studio
    - Azure PowerShell
    - Šablona Azure Resource Manageru
    - .NET API
    - REST API

     V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Příklad s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat z úložiště dat PostgreSQL místní, naleznete v tématu [JSON příklad: kopírování dat z PostgreSQL do objektu Blob Azure](#json-example-copy-data-from-postgresql-to-azure-blob) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení konkrétní entity služby Data Factory k úložišti dat PostgreSQL:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro PostgreSQL propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **OnPremisesPostgreSql** |Ano |
| server |Název serveru PostgreSQL. |Ano |
| databáze |Název databáze PostgreSQL. |Ano |
| Schéma |Název schématu v databázi. Název schématu rozlišuje velká a malá písmena. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k databázi PostgreSQL. Možné hodnoty jsou: anonymní, základní a systému Windows. |Ano |
| uživatelské jméno |Pokud používáte ověřování Basic nebo Windows, zadejte uživatelské jméno. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k místní databázi PostgreSQL. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu.

V rámci typeProperties části se liší pro jednotlivé typy datovou sadu a poskytuje informace o umístění dat v úložišti. Rámci typeProperties část datové sady typ **RelationalTable** (což zahrnuje datová sada PostgreSQL) má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze PostgreSQL, kterou propojená služba odkazuje. TableName rozlišuje velká a malá písmena. |Ne (Pokud **dotazu** z **RelationalSource** je zadána) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti dostupné v rámci typeProperties části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

Pokud je zdroj typu **RelationalSource** (která zahrnuje PostgreSQL), následující vlastnosti jsou k dispozici v rámci typeProperties části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Například: `"query": "select * from \"MySchema\".\"MyTable\""`. |Ne (Pokud **tableName** z **datovou sadu** je zadána) |

> [!NOTE]
> Schéma a tabulku názvy rozlišují malá a velká písmena. Uzavřete je do `""` (dvojité uvozovky) v dotazu.  

**Příklad:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>Příklad JSON: kopírování dat z PostgreSQL do objektu Blob Azure
Tento příklad obsahuje ukázkové JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se ukazují, jak zkopírovat data z databáze PostgreSQL do Azure Blob Storage. Však lze zkopírovat data do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.   

> [!IMPORTANT]
> Tato ukázka obsahuje fragmenty kódu JSON. Podrobné pokyny pro vytvoření objektu pro vytváření dat neobsahuje. V tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) podrobné pokyny najdete v článku.

Ukázka má následující entity objektu pro vytváření dat:

1. Propojené služby typu [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z výsledku dotazu v databázi PostgreSQL do objektu blob každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

Jako první krok nastavte Brána pro správu dat. Tyto pokyny jsou v [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

**PostgreSQL propojené služby:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
**Propojená služba Azure Blob storage:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
    }
    }
}
```
**PostgreSQL vstupní datové sady:**

Příkladu se předpokládá, jste vytvořili tabulku "MyTable" v PostgreSQL a obsahuje sloupec s názvem "časové razítko" pro data časové řady.

Nastavení `"external": true` služba Data Factory informuje, že datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure Blob výstupní datovou sadu:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Název složky a cesta k souboru pro tento objekt blob se vyhodnocují dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kanál s aktivitou kopírování:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **RelationalSource** a **podřízený** je typ nastaven na **BlobSink**. Zadané pro dotaz SQL **dotazu** vlastnost vybere data z public.usstates tabulky v databázi PostgreSQL.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>Mapování typu pro PostgreSQL
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup krok 2:

1. Převést na typ .NET typy nativní zdrojů
2. Převést na typ jímky nativní typ formátu .NET

Při přesunu dat na PostgreSQL, se používají následující mapování z typu PostgreSQL na typ .NET.

| Typ databáze PostgreSQL | PostgresSQL aliasy | Typ rozhraní .NET framework |
| --- | --- | --- |
| abstime | |Datum a čas | &nbsp;
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bit [(ne)] | |Byte [] řetězec | &nbsp;
| bit různých [(ne)] |varbit |Byte [] řetězec |
| Boolean |BOOL |Logická hodnota |
| Pole | |Byte [] řetězec |&nbsp;
| bytea | |Byte [] řetězec |&nbsp;
| znak [(ne)] |char [(ne)] |Řetězec |
| znak různých [(ne)] |varchar [(ne)] |Řetězec |
| CID | |Řetězec |&nbsp;
| CIDR | |Řetězec |&nbsp;
| kruhu. | |Byte [] řetězec |&nbsp;
| datum | |Datum a čas |&nbsp;
| DateRange | |Řetězec |&nbsp;
| Dvojitá přesnost |FLOAT8 |Double |
| inet | |Byte [] řetězec |&nbsp;
| intarry | |Řetězec |&nbsp;
| int4range | |Řetězec |&nbsp;
| int8range | |Řetězec |&nbsp;
| integer |int, int4 |Int32 |
| Interval [pole] [(p)] | |Časový interval |&nbsp;
| json | |Řetězec |&nbsp;
| jsonb | |Byte] |&nbsp;
| Řádek | |Byte [] řetězec |&nbsp;
| lseg | |Byte [] řetězec |&nbsp;
| macaddr | |Byte [] řetězec |&nbsp;
| peníze | |Decimal |&nbsp;
| číselný [(p, s)] |Decimal [(p, s)] |Decimal |
| numrange | |Řetězec |&nbsp;
| OID | |Int32 |&nbsp;
| path | |Byte [] řetězec |&nbsp;
| pg_lsn | |Int64 |&nbsp;
| Bod | |Byte [] řetězec |&nbsp;
| mnohoúhelníku | |Byte [] řetězec |&nbsp;
| skutečné |FLOAT4 |Jednoduchá |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| sériové |serial4 |Int32 |
| text | |Řetězec |&nbsp;

## <a name="map-source-to-sink-columns"></a>Mapování zdroje jímky sloupců
Další informace o mapování sloupců v datové sadě zdrojového sloupce v datové sadě podřízený najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelných číst z relační zdrojů
Při kopírování dat z relačních dat ukládá, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Zásady opakovaných pokusů pro datovou sadu můžete také nakonfigurovat tak, aby řez se znovu spustí, když dojde k chybě. Řez se znovu spustí, buď způsobem, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát řez je spustit. V tématu [Repeatable číst z relačními zdroji](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
