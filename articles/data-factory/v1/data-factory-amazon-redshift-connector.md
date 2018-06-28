---
title: Přesun dat z Amazon Redshift pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak pro přesun dat z Amazon Redshift pomocí aktivity kopírování objektu pro vytváření dat Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7ece34809734478ddb52c12d5dbd92291231f439
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045683"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Přesun dat z Amazon Redshift pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-amazon-redshift-connector.md)
> * [Verze 2 (aktuální verze)](../connector-amazon-redshift.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [Amazon Redshift konektoru V2](../connector-amazon-redshift.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z Amazon Redshift. Článek vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování. 

Aktuálně podporuje pouze přesunutí dat z Amazon Redshift k objektu pro vytváření dat [úložiště dat podporovaných podřízený](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Přesun dat z jiných úložišť dat na Amazon Redshift není podporována.

> [!TIP]
> K dosažení nejlepšího výkonu dosáhnete při kopírování velkých objemů dat z Amazon Redshift, zvažte použití předdefinované Redshift **uvolnění** příkaz Amazon jednoduché úložiště pomocí služby (Amazon S3). Podrobnosti najdete v tématu [uvolnění použít ke zkopírování dat z Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Požadavky
* Pokud přesouváte data k úložišti dat na místě, nainstalujte [Brána pro správu dat](data-factory-data-management-gateway.md) na místním počítači. Udělit přístup pro bránu do clusteru Amazon Redshift pomocí IP adresu místního počítače. Pokyny najdete v tématu [autorizovat přístup ke clusteru](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Pro přesun dat do úložiště dat Azure, najdete v článku [výpočetní IP adresy a rozsahy SQL, které jsou používány datová centra služby Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování pro přesun dat z Amazon Redshift zdroje pomocí různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je pomocí Průvodce kopírováním Azure Data Factory. Rychlé návod k vytvoření kanálu pomocí Průvodce kopírováním najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

Kanál můžete vytvořit také pomocí portálu Azure, Visual Studio, prostředí Azure PowerShell nebo jiných nástrojů. Šablony Azure Resource Manageru, rozhraní API .NET nebo REST API můžete také použít k vytvoření kanálu. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [aktivity kopírování kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený: 

1. Vytvoření propojených služeb propojíte vstup a výstup úložiště dat do data factory.
2. Vytvořte datové sady, které představují vstupní a výstupní data pro kopírování. 
3. Vytvoření kanálu s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Pokud použijete Průvodce kopírováním, se automaticky vytvoří definice JSON pro tyto entity služby Data Factory. Pokud používáte rozhraní API (s výjimkou .NET API) nebo nástroje, definujete ve formátu JSON entit služby Data Factory. [JSON příklad: kopírování dat z Amazon Redshift do úložiště objektů Azure Blob](#json-example-copy-data-from-amazon-redshift-to-azure-blob) ukazuje definice JSON entit služby Data Factory, které se používají ke zkopírování dat z úložiště dat Amazon Redshift.

Následující části popisují vlastnosti JSON, které slouží k určení entit služby Data Factory pro Amazon Redshift.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující tabulka obsahuje popis elementy JSON, které jsou specifické pro službu Amazon Redshift propojený.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **type** |Tato vlastnost musí být nastavená na **AmazonRedshift**. |Ano |
| **Server** |IP adresu nebo název hostitele serveru Amazon Redshift. |Ano |
| **port** |Číslo portu TCP, který používá server Amazon Redshift naslouchat pro připojení klientů. |Ne (výchozí hodnota je 5439) |
| **database** |Název databáze Amazon Redshift. |Ano |
| **Uživatelské jméno** |Jméno uživatele, který má přístup k databázi. |Ano |
| **heslo** |Heslo pro uživatelský účet. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady

Seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. **Struktura**, **dostupnosti**, a **zásad** oddíly jsou podobné pro všechny typy datovou sadu. Příklady typů datovou sadu: Azure SQL, úložiště objektů Blob v Azure a Azure Table storage.

**Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace o umístění dat v úložišti. **Rámci typeProperties** části datové sady typu **RelationalTable**, což zahrnuje datová sada Amazon Redshift, má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **tableName** |Název tabulky v databázi Amazon Redshift, který propojená služba odkazuje na. |Ne (Pokud **dotazu** vlastnost aktivity kopírování typu **RelationalSource** je zadána) |

## <a name="copy-activity-properties"></a>Zkopírovat vlastnosti aktivit

Seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md) článku. **Název**, **popis**, **vstupy** tabulky, **výstupy** tabulky, a **zásad** vlastnosti k dispozici pro všechny typy aktivit. Vlastnosti, které jsou k dispozici v **rámci typeProperties** části lišit pro každý typ aktivity. Pro aktivitu kopírování vlastnosti lišit v závislosti na typech zdrojů dat a jímky.

Pro aktivitu kopírování, pokud je zdroj typu **AmazonRedshiftSource**, následující vlastnosti jsou k dispozici v **rámci typeProperties** části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **dotaz** | Pomocí vlastního dotazu přečíst data. |Ne (Pokud **tableName** je zadána vlastnost datové sady) |
| **redshiftUnloadSettings** | Obsahuje vlastnost skupiny při použití Redshift **uvolnění** příkaz. | Ne |
| **s3LinkedServiceName** | Amazon S3 používat jako dočasné úložiště. Propojené služby je zadán pomocí Azure Data Factory název typu **AwsAccessKey**. | Při použití vyžaduje **redshiftUnloadSettings** vlastnost |
| **bucketName** | Označuje sady Amazon S3 používat k uložení dočasné data. Pokud není tato vlastnost k dispozici, aktivity kopírování automaticky generuje blok. | Při použití vyžaduje **redshiftUnloadSettings** vlastnost |

Alternativně můžete použít **RelationalSource** typu, který zahrnuje Amazon Redshift, s následující vlastností v **rámci typeProperties** části. Poznámka: Tento typ zdroje nepodporuje Redshift **uvolnění** příkaz.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **dotaz** |Pomocí vlastního dotazu přečíst data. | Ne (Pokud **tableName** je zadána vlastnost datové sady) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>UVOLNĚNÍ použít ke zkopírování dat z Amazon Redshift

Amazon Redshift [ **uvolnění** ](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) příkaz zrušeno jeho zavedení výsledky dotazu na jeden nebo více souborů na Amazon S3. Tento příkaz se doporučuje Amazon kopírování rozsáhlých datových sad z Redshift.

**Příklad: Kopírování dat z Amazon Redshift do Azure SQL Data Warehouse**

Tento příklad kopíruje data z Amazon Redshift do Azure SQL Data Warehouse. V příkladu se používá Redshift **uvolnění** příkaz, dvoufázové instalace zkopírovat data a PolyBase společnosti Microsoft.

Pro tento případ použití ukázkové, aktivity kopírování nejprve zrušeno jeho zavedení dat z Amazon Redshift do Amazon S3 jako nakonfigurovaný v **redshiftUnloadSettings** možnost. V dalším kroku data budou zkopírována z Amazonu S3 do úložiště objektů Blob v Azure jako zadaný v **stagingSettings** možnost. Nakonec PolyBase načte data do SQL Data Warehouse. Všechny dočasné formáty jsou zpracovávány aktivity kopírování.

![Zkopírování pracovního postupu z Amazon Redshift do SQL Data Warehouse](media\data-factory-amazon-redshift-connector\redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Příklad JSON: kopírování dat z Amazon Redshift do úložiště objektů Blob v Azure
Tento příklad ukazuje, jak zkopírovat data z databáze Amazon Redshift do Azure Blob Storage. Data se dají zkopírovat na žádnou [podporované podřízený](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování.  

Ukázka má následující entity objektu pro vytváření dat:

* Propojené služby typu [AmazonRedshift](#linked-service-properties)
* Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstup [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties)
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) vlastnosti

Ukázka zkopíruje data z výsledku dotazu v Amazon Redshift do objektu blob Azure každou hodinu. Vlastnosti JSON, které se používají v ukázce jsou popsané v následujících definice entity.

**Redshift Amazon propojené služby**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Objekt Blob propojená služba Azure storage**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Vstupní datové sady Amazon Redshift**

**Externí** je nastavena na hodnotu true, k informování služba Data Factory je datová sada k objektu pro vytváření dat externí. Nastavení této vlastnosti označuje, že není datové sady vyprodukované aktivitu v datové továrně. Nastavte vlastnost na hodnotu true vstupní datovou sadu, která není vyprodukované aktivitu v kanálu.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu blob každou hodinu nastavením **frekvence** vlastnost "Hodinu" a **interval** vlastnost na hodnotu 1. **FolderPath** dynamicky vyhodnotí vlastnost pro tento objekt blob. Hodnota vlastnosti vychází čas zahájení řez, který je zpracovávána. Cesta ke složce používá rok, měsíc, den a čas části čas spuštění.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Aktivita kopírování v kanálu se k Azure Redshift zdroji (typu RelationalSource) a jímky objektů Blob v Azure**

Kanál obsahuje aktivitu kopírování, který je nakonfigurován pro použití vstupní a výstupní datové sady. Kanál je naplánována na každou hodinu. V definici JSON pro kanál **zdroj** je typ nastaven na **RelationalSource** a **podřízený** je typ nastaven na **BlobSink**. Zadané pro dotaz SQL **dotazu** vlastnost vybere data zkopírovat z poslední hodinu.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Mapování typu pro Amazon Redshift
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí převody typů automatické z typ zdroje pro typ jímky. Typy jsou převáděny pomocí dvoustupňový přístup:

1. Převést z typu nativní zdroj na typ formátu .NET
2. Převést na typ nativní jímky typ formátu .NET

Aktivita kopírování převádí data z typ Amazon Redshift na typ .NET jsou použity následující mapování:

| Typ Amazon Redshift | Typ formátu .NET |
| --- | --- |
| SMALLINT |Int16 |
| CELÉ ČÍSLO |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimal |
| SKUTEČNÉ |Jednoduchá |
| DVOJITÁ PŘESNOST |Double |
| LOGICKÁ HODNOTA |Řetězec |
| CHAR – |Řetězec |
| VARCHAR |Řetězec |
| DATE (Datum) |DateTime |
| ČASOVÉ RAZÍTKO |DateTime |
| TEXT |Řetězec |

## <a name="map-source-to-sink-columns"></a>Mapování zdroje jímky sloupců
Další postupy k mapování sloupců v datové sadě zdroje na sloupců v datové sadě podřízený najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Opakovatelných čtení z relační zdrojů
Při kopírování dat z relační datové úložiště, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Můžete také nakonfigurovat opakovaném **zásad** pro datovou sadu řez znovu spustit, když dojde k chybě. Ujistěte se, že je pro čtení stejná data, bez ohledu na to, jak často se znovu spustí řez. Také se ujistěte, že je stejná data ke čtení, bez ohledu na to, jak spustit řez znovu. Další informace najdete v tématu [Repeatable čte z relačními zdroji](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
Další informace o klíčových faktorů, které ovlivňují výkon aktivity kopírování a způsoby, jak optimalizovat výkon v [výkonu kopie aktivity a ladění průvodce](data-factory-copy-activity-performance.md). 

## <a name="next-steps"></a>Další postup
Podrobné pokyny pro vytvoření kanálu s aktivitou kopírování najdete v tématu [aktivity kopírování kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
