---
title: Přesun dat z Amazon Redshift pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak přesunout data pomocí aktivity kopírování objekt pro vytváření dat Azure z Amazon Redshift.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eafc5dbd89d5727fac0040816785dbfcdf123b62
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563611"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Přesun dat z Amazon Redshift pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-amazon-redshift-connector.md)
> * [Verze 2 (aktuální verze)](../connector-amazon-redshift.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Amazon Redshift v V2](../connector-amazon-redshift.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování ve službě Azure Data Factory k přesunu dat z Amazon Redshift. Tento článek vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

Data Factory v současné době podporuje pouze přesouvá data z Amazon Redshift k [podporovaného úložiště dat jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Přesun dat z jiných úložišť dat k databázi Amazon Redshift není podporován.

> [!TIP]
> K dosažení nejlepšího výkonu při kopírování velkých objemů dat z Amazon Redshift, zvažte použití předdefinovaných Redshift **uvolnění** příkaz prostřednictvím Amazon Simple Storage Service (Amazon S3). Podrobnosti najdete v tématu [uvolnění použít ke zkopírování dat z Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Požadavky
* Pokud přesouváte data v místním úložišti dat, nainstalujte [brána správy dat](data-factory-data-management-gateway.md) na místním počítači. Udělit přístup pro bránu do clusteru Amazon Redshift s použitím IP adresy v místním počítači. Pokyny najdete v tématu [povolit přístup ke clusteru](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Pro přesun dat do úložiště dat Azure, najdete v článku [Compute IP adres a rozsahů SQL, které používají datová centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování pro přesun dat z zdroje Amazon Redshift pomocí různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použití Průvodce kopírováním služby Azure Data Factory. Rychlý postup k vytvoření kanálu pomocí Průvodce kopírováním, najdete v části [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

Kanál můžete vytvořit také pomocí webu Azure portal, sady Visual Studio, prostředí Azure PowerShell nebo jiných nástrojů. Šablony Azure Resource Manageru, rozhraní API pro .NET nebo REST API můžete také použít k vytvoření kanálu. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření propojené služby, které spojí vstupní a výstupní úložiště dat do služby data factory.
2. Vytvoření datové sady, které představují vstupní a výstupní data pro operaci kopírování.
3. Vytvoření kanálu s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup.

Při použití Průvodce kopírováním definice JSON pro tyto entity služby Data Factory se automaticky vytvoří. Při použití rozhraní API (s výjimkou rozhraní .NET API) nebo nástroje pro definování entit služby Data Factory ve formátu JSON. [Příklad JSON: Kopírování dat do úložiště objektů Blob v Azure z Amazon Redshift](#json-example-copy-data-from-amazon-redshift-to-azure-blob) ukazuje definice JSON entit služby Data Factory, které se používají ke kopírování dat z úložiště dat Amazon Redshift.

Následující části popisují vlastnosti JSON, které se používají k definování entit služby Data Factory pro Amazon Redshift.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro služby Amazon Redshift propojený.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **type** |Tato vlastnost musí být nastavená na **AmazonRedshift**. |Ano |
| **server** |IP adresu nebo název hostitele serveru Amazon Redshift. |Ano |
| **Port** |Číslo portu TCP, které server Amazon Redshift používá k naslouchání pro připojení klientů. |Ne (výchozí hodnota je 5439) |
| **database** |Název databáze Amazon Redshift. |Ano |
| **uživatelské jméno** |Jméno uživatele, který má přístup k databázi. |Ano |
| **Heslo** |Heslo pro uživatelský účet. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady

Seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. **Struktura**, **dostupnosti**, a **zásady** oddíly jsou podobné pro všechny typy datové sady. Příklady typů datových sad: Azure SQL, Azure Blob storage a Azure Table storage.

**TypeProperties** oddílu se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. **TypeProperties** části datové sady typu **RelationalTable**, což zahrnuje datová sada Amazon Redshift má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **tableName** |Název tabulky v databázi Amazon Redshift, která odkazuje propojenou službu. |Ne (Pokud **dotazu** vlastnost aktivity kopírování typu **RelationalSource** určena) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md) článku. **Název**, **popis**, **vstupy** tabulky, **výstupy** tabulky, a **zásady** jsou vlastnosti k dispozici pro všechny typy aktivit. Vlastnosti, které jsou k dispozici v **typeProperties** oddílu se liší pro každý typ aktivity. Pro aktivitu kopírování vlastností lišit v závislosti na typy dat zdroje a jímky.

Pro aktivitu kopírování, pokud je zdroj typu **AmazonRedshiftSource**, následující vlastnosti jsou k dispozici v **typeProperties** části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **Dotaz** | Použijte vlastní dotaz číst data. |Ne (Pokud **tableName** je zadána vlastnost datové sady) |
| **redshiftUnloadSettings** | Při použití Redshift obsahuje skupiny vlastností **uvolnění** příkazu. | Ne |
| **s3LinkedServiceName** | Amazon S3, který se použije jako dočasné úložiště. Propojené služby je určen pomocí Azure Data Factory název typu **typu AwsAccessKey**. | Požaduje se při použití **redshiftunloadsettings, taková služba** vlastnost |
| **bucketName** | Označuje kbelíku Amazon S3 používat k ukládání dat dočasné. Pokud tuto vlastnost nezadáte, aktivita kopírování automaticky vygeneruje kbelík. | Požaduje se při použití **redshiftunloadsettings, taková služba** vlastnost |

Alternativně můžete použít **RelationalSource** typ, který zahrnuje Amazon Redshift, s následující vlastností v **typeProperties** oddílu. Poznámka: Tento typ zdroje nepodporuje Redshift **uvolnění** příkazu.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **Dotaz** |Použijte vlastní dotaz číst data. | Ne (Pokud **tableName** je zadána vlastnost datové sady) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Použití uvolnění pro kopírování dat z Amazon Redshift

Amazon Redshift [ **uvolnění** ](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) příkaz uvolní výsledky dotazu na jeden nebo více souborů na Amazon S3. Tento příkaz se doporučuje pro kopírování velkých datových sad z Redshift Amazon.

**Příklad: Kopírování dat z Amazon Redshift do Azure SQL Data Warehouse**

V tomto příkladu kopíruje data z Amazon Redshift do Azure SQL Data Warehouse. V příkladu se používá Redshift **uvolnění** příkaz dvoufázové instalace kopírování dat a Microsoft PolyBase.

Pro tento případ použití ukázkové aktivity kopírování nejprve uvolní data z Amazon Redshift Amazon S3 podle konfigurace v **redshiftunloadsettings, taková služba** možnost. V dalším kroku se kopírují data z Amazonu S3 do úložiště objektů Blob v Azure, jak je uvedeno v **stagingSettings** možnost. Nakonec PolyBase načte data do SQL Data Warehouse. Všechny dočasné formáty jsou zpracovány pomocí aktivity kopírování.

![Kopírování z Amazon Redshift pracovního postupu do SQL Data Warehouse](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Příklad JSON: Kopírování dat do úložiště objektů Blob v Azure z Amazon Redshift
Tato ukázka předvádí, jak kopírovat data ze služby Amazon Redshift database do Azure Blob Storage. Data se dají zkopírovat přímo k libovolnému [jímky podporované](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování.

Ukázka obsahuje následující entit datové továrny:

* Propojené služby typu [AmazonRedshift](#linked-service-properties)
* Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties)
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [Azureblobu](data-factory-azure-blob-connector.md#dataset-properties)
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která se používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) vlastnosti

Ukázce kopíruje data z výsledků dotazu v Amazon Redshift do objektu blob Azure každou hodinu. Vlastnostech JSON použitých v příkladu jsou popsány v následující části definice entity.

**Amazon Redshift propojené služby**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
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
**Vstupní datová sada Amazon Redshift**

**Externí** je nastavena na "true" informovat službu Data Factory, že tato datová sada je externí s datovou továrnou. Nastavení této vlastnosti znamená, že není datové sady produkované aktivity ve službě data factory. Nastavte vlastnost na hodnotu true pro vstupní datovou sadu, která není vytvořena podle aktivity v kanálu.

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

Data se zapisují do nového objektu blob každou hodinu tak, že nastavíte **frekvence** vlastnost "Hodina" a **interval** vlastnost na hodnotu 1. **FolderPath** vlastností pro objekt blob se dynamicky vyhodnocuje. Hodnota vlastnosti je založena na počáteční čas řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

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

**Aktivita kopírování v kanálu pomocí Azure Redshift zdroj (typu RelationalSource) a jímky objektů Blob v Azure**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad. Kanálu je naplánované spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **RelationalSource** a **jímky** je typ nastaven na **BlobSink**. Zadaná pro dotaz SQL **dotazu** vlastnost vybere data ke kopírování ze uplynulou hodinu.

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
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku, aktivita kopírování provádí převody automatické typ z typ zdroje do jímky typu. Typy jsou převedeny pomocí dvoukrokový přístup:

1. Nativní zdrojový typ převést na typ .NET
2. Převést z typu .NET native jímky typu

Aktivitu kopírování, která převádí data z Amazon Redshift typu na typ .NET jsou použity následující mapování:

| Typ Amazon Redshift | Typ formátu .NET |
| --- | --- |
| SMALLINT |Int16 |
| CELÉ ČÍSLO |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimal |
| REAL |Single |
| DVOJITOU PŘESNOSTÍ |Double |
| DATOVÝ TYP BOOLEAN |String |
| CHAR |String |
| VARCHAR |String |
| DATE (Datum) |DateTime |
| ČASOVÉ RAZÍTKO |DateTime |
| TEXT |String |

## <a name="map-source-to-sink-columns"></a>Mapování zdroje do jímky sloupce
Další postup mapování sloupců v datové sadě zdroje do sloupců v datové sadě jímky najdete v tématu [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Opakovatelné operace čtení z relačních zdrojů
Při kopírování dat z relační datové úložiště, mějte opakovatelnosti aby se zabránilo neúmyslnému výsledků. Ve službě Azure Data Factory můžete znovu spustit řezu ručně. Můžete také nakonfigurovat opakování **zásady** pro datovou sadu znovu spustit určitý řez, pokud dojde k chybě. Ujistěte se, že je pro čtení na stejná data, bez ohledu na to, kolikrát se znovu spustí podle řez. Také se ujistěte, že je stejná data čtení bez ohledu na to, jak spustit řez znovu. Další informace najdete v tématu [Repeatable čte z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
Další informace o klíčových faktorů, které ovlivňují výkon aktivitu kopírování a způsoby, jak optimalizovat výkon [průvodci laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Další postup
Podrobné pokyny pro vytvoření kanálu s aktivitou kopírování najdete v tématu [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
