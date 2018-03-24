---
title: Kopírování dat z Amazon Redshift pomocí Azure Data Factory | Microsoft Docs
description: Další informace o tom, jak zkopírovat data z Amazon Redshift do úložiště podporované jímku dat pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 7dc4af39a659a9c3c910c835265886b79ad5cf62
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopírování dat z Amazon Redshift pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-amazon-redshift-connector.md)
> * [Verze 2 – Preview](connector-amazon-redshift.md)


Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z Redshift Amazon. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [connnector Amazon Redshift v V1](v1/data-factory-amazon-redshift-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Data můžete zkopírovat z Amazon Redshift do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Amazon Redshift podporuje načítání dat z Redshift pomocí dotazu nebo integrovanou podporu Redshift uvolnění.

> [!TIP]
> K dosažení nejlepšího výkonu dosáhnete při kopírování velkých objemů dat z Redshift, zvažte použití předdefinované uvolnění Redshift prostřednictvím Amazon S3. V tématu [uvolnění použít ke zkopírování dat z Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) podrobnosti.

## <a name="prerequisites"></a>Požadavky

* Pokud kopírujete úložiště dat k místním datům pomocí [Self-hosted integrace Runtime](create-self-hosted-integration-runtime.md), zajistit přístup do clusteru Amazon Redshift integrace Runtime (použijte IP adresu počítače). V tématu [autorizovat přístup ke clusteru](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) pokyny.
* Pokud data kopírujete k úložišti dat Azure, najdete v části [rozsahy IP Center dat Azure](https://www.microsoft.com/download/details.aspx?id=41653) pro výpočetní IP adresy a rozsahy SQL používaných dat Azure centra.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru Amazon Redshift.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro službu Amazon Redshift propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **AmazonRedshift** | Ano |
| server |IP adresa nebo název hostitele serveru Amazon Redshift. |Ano |
| port |Číslo portu TCP, který používá server Amazon Redshift naslouchat pro připojení klientů. |Ne, výchozí hodnota je 5439 |
| databáze |Název databáze Amazon Redshift. |Ano |
| uživatelské jméno |Jméno uživatele, který má přístup k databázi. |Ano |
| heslo |Heslo pro uživatelský účet. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

**Příklad:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje Amazon Redshift datovou sadu.

Ke zkopírování dat z Amazon Redshift, nastavte vlastnost typu datové sady, která **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **RelationalTable** | Ano |
| tableName | Název tabulky v Amazon Redshift. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje Amazon Redshift zdroje.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift jako zdroj

Ke zkopírování dat z Amazon Redshift, nastavte typ zdroje v aktivitě kopírování do **AmazonRedshiftSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **AmazonRedshiftSource** | Ano |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Příklad: vybrat * z MyTable. |Ne (když je určena "tableName" v datové sadě) |
| redshiftUnloadSettings | Skupina vlastností při použití Amazon Redshift uvolnění. | Ne |
| s3LinkedServiceName | Odkazuje Amazon S3 k-be používané jako dočasné úložiště tak, že zadáte název propojené služby typu "AmazonS3". | Ano, pokud používáte uvolnění |
| bucketName | Uveďte sady S3 k uložení dat o dočasné. Pokud není zadaná, služba Data Factory ho generuje automaticky.  | Ano, pokud používáte uvolnění |

**Příklad: Amazon Redshift zdroj v aktivitou kopírování pomocí uvolnění**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Další informace o tom, jak používat uvolnění ke zkopírování dat z Amazon Redshift efektivně z další části.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>UVOLNĚNÍ použít ke zkopírování dat z Amazon Redshift

[UVOLNĚNÍ](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) mechanismus poskytované Redshift Amazon, což může mít za následek uvolnění výsledky dotazu na jeden nebo více souborů na služby úložiště jednoduché Amazon (Amazon S3). Je způsob, jak doporučení Amazon kopírování velké datové sady z Redshift.

**Příklad: kopírování dat z Amazon Redshift do Azure SQL Data Warehouse pomocí uvolnění, dvoufázové instalace kopírování a PolyBase**

Pro tuto ukázku případy použití, zkopírujte data aktivity uvolní z Amazon Redshift do Amazon S3 jako nakonfigurovaný v "redshiftUnloadSettings" a kopírování dat z Amazon S3 do objektu Blob Azure zadané v "stagingSettings", nakonec použijte PolyBase k načtení dat do SQL Data Skladu. Všechny dočasné formát se zpracovává souborem aktivity kopírování správně.

![Redshift postup kopírování datového skladu SQL](media\copy-data-from-amazon-redshift\redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapování datového typu pro Amazon Redshift

Při kopírování dat z Amazon Redshift, se používají následující mapování Amazon Redshift datových typů k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| Amazon Redshift datový typ | Typ průběžných dat objektu pro vytváření dat |
|:--- |:--- |
| BIGINT |Int64 |
| LOGICKÁ HODNOTA |Řetězec |
| CHAR – |Řetězec |
| DATE (Datum) |DateTime |
| DECIMAL |Decimal |
| DVOJITÁ PŘESNOST |Dvojitý |
| CELÉ ČÍSLO |Int32 |
| SKUTEČNÉ |Svobodný/svobodná |
| SMALLINT |Int16 |
| TEXT |Řetězec |
| ČASOVÉ RAZÍTKO |DateTime |
| VARCHAR |Řetězec |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
