---
title: Přesun dat z PostgreSQL pomocí Azure Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z databáze PostgreSQL pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2f964ac77ade69f14692a337f17011e93f85f68c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025704"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Přesun dat z PostgreSQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-onprem-postgresql-connector.md)
> * [Verze 2 (aktuální verze)](../connector-postgresql.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor PostgreSQL ve V2](../connector-postgresql.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování ve službě Azure Data Factory k přesunu dat z místní databáze PostgreSQL. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

Kopírování dat z úložiště dat v místním PostgreSQL do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data factory podporuje právě přesouvá data z databáze PostgreSQL do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat k databázi PostgreSQL. 

## <a name="prerequisites"></a>Požadavky

Služba data Factory podporuje připojení k místním zdrojům PostgreSQL pomocí brány správy dat. Zobrazit [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku se dozvíte o brána správy dat a podrobné pokyny o nastavení brány.

I v případě, že na Virtuálním počítači Azure IaaS je hostitelem databáze PostgreSQL je potřeba brána. Bránu můžete nainstalovat na stejném virtuálním počítači IaaS jako úložiště dat nebo na jiný virtuální počítač, tak dlouho, dokud brána lze připojit k databázi.

> [!NOTE]
> Naleznete v tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení/bránou související problémy.

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Brána správy dat pro připojení k databázi PostgreSQL, nainstalujte [Ngpsql zprostředkovatel dat pro PostgreSQL](https://go.microsoft.com/fwlink/?linkid=282716) verze 2.0.12 až 3.1.9 ve stejném systému jako brána pro správu dat. PostgreSQL verze 7.4 a vyšší je podporovaná.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště dat v místním PostgreSQL pomocí různých nástrojů a rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data. 
- Tyto nástroje můžete také použít k vytvoření kanálu: 
    - portál Azure
    - Visual Studio
    - Azure PowerShell
    - Šablona Azure Resource Manageru
    - .NET API
    - REST API

     Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory.
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. 

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Tady je příklad s definice JSON entit služby Data Factory, které se používají ke kopírování dat z úložiště dat v místním PostgreSQL, naleznete v tématu [příklad JSON: Kopírování dat z PostgreSQL do objektů Blob v Azure](#json-example-copy-data-from-postgresql-to-azure-blob) části tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory konkrétní for postgresql – úložiště dat:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro PostgreSQL propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavená na: **OnPremisesPostgreSql** |Ano |
| server |Název serveru PostgreSQL. |Ano |
| databáze |Název databáze PostgreSQL. |Ano |
| schéma |Název schématu databáze. Název schématu je velká a malá písmena. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k databázi PostgreSQL. Možné hodnoty: Anonymní, základní a Windows. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud se používá ověřování Basic nebo Windows. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení k místní databázi PostgreSQL. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datové sady.

V části typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. TypeProperties části datové sady typu **RelationalTable** (která zahrnuje PostgreSQL datovou sadu) má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze PostgreSQL, propojená služba odkazuje na. TableName je velká a malá písmena. |Ne (Pokud **dotazu** z **RelationalSource** určena) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti v části typeProperties aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

Pokud je zdroj typu **RelationalSource** (která zahrnuje PostgreSQL), v části typeProperties jsou k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Například: `"query": "select * from \"MySchema\".\"MyTable\""`. |Ne (Pokud **tableName** z **datovou sadu** určena) |

> [!NOTE]
> Schéma a tabulku názvy jsou malá a velká písmena. Vložte je do `""` (dvojité uvozovky) v dotazu.  

**Příklad:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>Příklad JSON: Kopírování dat z PostgreSQL do objektů Blob v Azure
V tomto příkladu obsahuje ukázky JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Zobrazí se kopírování dat z databáze PostgreSQL do služby Azure Blob Storage. Ale data je možné zkopírovat do libovolné jímky uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.   

> [!IMPORTANT]
> Tato ukázka poskytuje fragmenty kódu JSON. Neobsahuje podrobné pokyny pro vytvoření datové továrny. Zobrazit [přesun dat mezi místními umístěními a cloudu](data-factory-move-data-between-onprem-and-cloud.md) najdete podrobné pokyny.

Ukázka obsahuje následující entit datové továrny:

1. Propojené služby typu [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázce kopíruje data z výsledků dotazu v databázi PostgreSQL do objektu blob každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

Jako první krok nastavte si bránu správy dat. Pokyny jsou v [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

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

Ukázka předpokládá vytvoříte tabulku "MyTable" v PostgreSQL a obsahuje sloupec s názvem "časové razítko" pro data časových řad.

Nastavení `"external": true` služby Data Factory informuje, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Název složky a cesta k souboru pro tento objekt blob se dynamicky vyhodnocuje podle času spuštění řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

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

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno na každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **RelationalSource** a **jímky** je typ nastaven na **BlobSink**. Zadaná pro dotaz SQL **dotazu** vlastnost vybere data z public.usstates tabulky v databázi PostgreSQL.

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
## <a name="type-mapping-for-postgresql"></a>Mapování typu for PostgreSQL
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typy s přístupem následující krok 2:

1. Převést na typ formátu .NET typy nativních zdrojů
2. Převést z typu .NET native jímky typu

Při přesouvání dat k PostgreSQL, se používají následující mapování z typu PostgreSQL na typ .NET.

| Typ databáze PostgreSQL | Aliasy PostgresSQL | Typ rozhraní .NET framework |
| --- | --- | --- |
| abstime | |Datum a čas | &nbsp;
| bigint |Int8 |Int64 |
| bigserial |serial8 |Int64 |
| bitové [(n)] | |Byte [], řetězce | &nbsp;
| bit různou [(n)] |varbit |Byte [], řetězce |
| Boolean |BOOL |Logická hodnota |
| Pole | |Byte [], řetězce |&nbsp;
| bytea | |Byte [], řetězce |&nbsp;
| znak [(n)] |char [(n)] |Řetězec |
| znak různé [(n)] |varchar [(n)] |Řetězec |
| CID | |Řetězec |&nbsp;
| CIDR | |Řetězec |&nbsp;
| Kruh | |Byte [], řetězce |&nbsp;
| date | |Datum a čas |&nbsp;
| DateRange | |Řetězec |&nbsp;
| dvojitou přesností |FLOAT8 |Double |
| inet | |Byte [], řetězce |&nbsp;
| intarry | |Řetězec |&nbsp;
| int4range | |Řetězec |&nbsp;
| int8range | |Řetězec |&nbsp;
| integer |int, int4 |Datový typ Int32 |
| Interval [pole] [(p).] | |Časový interval |&nbsp;
| json | |Řetězec |&nbsp;
| jsonb | |Byte] |&nbsp;
| Řádek | |Byte [], řetězce |&nbsp;
| lseg | |Byte [], řetězce |&nbsp;
| macaddr | |Byte [], řetězce |&nbsp;
| peníze | |Desítkově |&nbsp;
| numerické [(p, s)] |desetinné číslo [(p, s)] |Desítkově |
| numrange | |Řetězec |&nbsp;
| identifikátor objektu | |Datový typ Int32 |&nbsp;
| path | |Byte [], řetězce |&nbsp;
| pg_lsn | |Int64 |&nbsp;
| Bod | |Byte [], řetězce |&nbsp;
| Mnohoúhelník | |Byte [], řetězce |&nbsp;
| Real |FLOAT4 |Jednoduchá |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| sériové |serial4 |Datový typ Int32 |
| text | |Řetězec |&nbsp;

## <a name="map-source-to-sink-columns"></a>Mapování zdroje do jímky sloupce
Další informace o mapování sloupců v datové sadě zdroje do sloupců v datové sadě jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení z relačních zdrojů
Při kopírování dat z relačních dat ukládá, mějte opakovatelnosti aby se zabránilo neúmyslnému výsledků. Ve službě Azure Data Factory můžete znovu spustit řezu ručně. Zásady opakování pro datovou sadu můžete také nakonfigurovat tak, aby určitý řez se znovu spustí, když dojde k chybě. V obou případech se znovu spustí určitý řez, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát spustit určitý řez. Zobrazit [Repeatable z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.
