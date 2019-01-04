---
title: Kopírování dat z Amazon Redshift pomocí Azure Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak kopírovat data z Amazon Redshift úložišť dat podporovaných jímky pomocí služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 7c790d03143eece9b0c827a033bdd46bfd1a8f45
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024361"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopírování dat z Amazon Redshift pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-amazon-redshift-connector.md)
> * [Aktuální verze](connector-amazon-redshift.md)


Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z Amazon Redshift. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z Amazon Redshift do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Amazon Redshift podporuje načítání dat z dotazu nebo integrovaná podpora uvolnění Redshift Redshift.

> [!TIP]
> K dosažení nejlepšího výkonu při kopírování velkých objemů dat z Redshift, zvažte použití předdefinovaných uvolnění Redshift prostřednictvím Amazon S3. Zobrazit [uvolnění použít ke zkopírování dat z Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) podrobné informace.

## <a name="prerequisites"></a>Požadavky

* Pokud kopírujete úložiště dat k místním datům pomocí [modul Integration Runtime](create-self-hosted-integration-runtime.md), udělte přístup ke clusteru Amazon Redshift prostředí Integration Runtime (použijte IP adresu počítače). Zobrazit [povolit přístup ke clusteru](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) pokyny.
* Pokud se kopírování dat do úložiště dat Azure, přečtěte si téma [rozsahy IP datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653) Compute IP adres a rozsahů SQL využívají Azure data centra.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Amazon Redshift.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Amazon Redshift propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **AmazonRedshift** | Ano |
| server |IP adresa nebo název hostitele serveru Amazon Redshift. |Ano |
| port |Číslo portu TCP, které server Amazon Redshift používá k naslouchání pro připojení klientů. |Ne, výchozí hodnota je 5439 |
| databáze |Název databáze Amazon Redshift. |Ano |
| uživatelské jméno |Jméno uživatele, který má přístup k databázi. |Ano |
| heslo |Heslo pro uživatelský účet. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje datová sada Amazon Redshift.

Ke zkopírování dat z Amazon Redshift, nastavte vlastnost typ datové sady na **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **RelationalTable** | Ano |
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností zdroje Amazon Redshift podporována.

### <a name="amazon-redshift-as-source"></a>Jako zdroj Amazon Redshift

Ke zkopírování dat z Amazon Redshift, nastavte typ zdroje v aktivitě kopírování do **AmazonRedshiftSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **AmazonRedshiftSource** | Ano |
| query |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Příklad: vybrat * z MyTable. |Ne (když je "tableName" v datové sadě zadán) |
| redshiftunloadsettings, taková služba | Při použití uvolnění Amazon Redshift vlastnosti skupiny. | Ne |
| s3LinkedServiceName | Pokud zadáte název propojené služby typu "AmazonS3" odkazuje na Amazon S3 k-be používané jako dočasné úložiště. | Ano, pokud používáte uvolnění z paměti |
| bucketName | Označuje sadu S3 k uložení dat o dočasné. Pokud se nezadá, služby Data Factory vytvoří jej automaticky.  | Ano, pokud používáte uvolnění z paměti |

**Příklad: Zdroje Amazon Redshift v aktivitou kopírování pomocí uvolnění z paměti**

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

Další informace o tom, jak používat uvolnění pro kopírování dat z Amazon Redshift efektivně v další části.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Použití uvolnění pro kopírování dat z Amazon Redshift

[UVOLNIT](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) virtuálních sítí je mechanismus poskytované Amazon Redshift, které můžete uvolnit výsledky dotazu na jeden nebo více souborů na Amazon Simple Storage Service (Amazon S3). Je to způsob doporučení pro kopírování velkých sadách dat z Redshift Amazon.

**Příklad: kopírování dat do Azure SQL Data Warehouse pomocí UVOLNIT, dvoufázové instalace kopírování a PolyBase z Amazon Redshift**

Pro tuto ukázku případ použití, dat na uvolní aktivity kopírování z Amazon Redshift do Amazon S3 podle konfigurace v redshiftunloadsettings, taková "služba" a zkopírujte data z Amazonu S3 do objektu Blob Azure jako zadané v poli "stagingSettings", a konečně použijte PolyBase k načtení dat do SQL Data Sklad. Všechny dočasné formát se zpracovává souborem aktivitu kopírování, která správně.

![Redshift do SQL data Warehouse Kopírovat pracovní postup](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapování datového typu pro Amazon Redshift

Při kopírování dat z Amazon Redshift, se používají následující mapování z Amazon Redshift datových typů na Azure Data Factory dočasné datové typy. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Datový typ Amazon Redshift | Data factory dočasné datový typ |
|:--- |:--- |
| BIGINT |Int64 |
| DATOVÝ TYP BOOLEAN |Řetězec |
| CHAR |Řetězec |
| DATE (Datum) |DateTime |
| DECIMAL |Desítkově |
| DVOJITOU PŘESNOSTÍ |Double |
| CELÉ ČÍSLO |Datový typ Int32 |
| REAL |Jednoduchá |
| SMALLINT |Int16 |
| TEXT |Řetězec |
| ČASOVÉ RAZÍTKO |DateTime |
| VARCHAR |Řetězec |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
