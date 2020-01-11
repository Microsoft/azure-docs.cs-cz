---
title: Kopírování dat z Amazon RedShift
description: Přečtěte si, jak kopírovat data z Amazon RedShift na podporovaná úložiště dat jímky pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 4d729a0117c7c409d1a3e0c3fd440aed96153203
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893323"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopírování dat z Amazon RedShift pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-amazon-redshift-connector.md)
> * [Aktuální verze](connector-amazon-redshift.md)


Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z aplikace Amazon RedShift. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Amazon RedShift se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data z Amazon RedShift můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Amazon RedShift podporuje načítání dat z RedShift pomocí dotazů nebo integrované podpory RedShift uvolnění.

> [!TIP]
> Abyste dosáhli nejlepšího výkonu při kopírování velkých objemů dat z RedShift, zvažte použití integrovaného Redshiftu pro uvolnění prostřednictvím Amazon S3. Podrobnosti najdete v tématu věnovaném [kopírování dat z oddílu Amazon RedShift pomocí uvolnění](#use-unload-to-copy-data-from-amazon-redshift) .

## <a name="prerequisites"></a>Požadavky

* Pokud kopírujete data do místního úložiště dat pomocí [Integration runtime](create-self-hosted-integration-runtime.md)v místním prostředí, udělte přístup ke clusteru Amazon RedShift Integration runtime (použijte IP adresu počítače). Pokyny najdete v tématu [autorizace přístupu ke clusteru](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) .
* Pokud kopírujete data do úložiště dat Azure, přečtěte si téma [rozsahy IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653) pro výpočetní IP adresu a rozsahy SQL používané datovými centry Azure.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro službu Amazon RedShift Connector.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Amazon RedShift jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **AmazonRedshift** . | Ano |
| server |IP adresa nebo název hostitele serveru Amazon RedShift Server. |Ano |
| port |Číslo portu TCP, který server Amazon RedShift používá k naslouchání klientským připojením. |Ne, výchozí hodnota je 5439 |
| databáze |Název databáze Amazon RedShift. |Ano |
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných sadou Amazon RedShift DataSet.

Chcete-li kopírovat data z Amazon RedShift, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **AmazonRedshiftTable** . | Ano |
| schema | Název schématu. |Ne (když je zadán zdroj aktivity "query")  |
| table | Název tabulky. |Ne (když je zadán zdroj aktivity "query")  |
| tableName | Název tabulky se schématem Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové zatížení použijte `schema` a `table`. | Ne (když je zadán zdroj aktivity "query") |

**Příklad**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "AmazonRedshiftTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Pokud jste používali `RelationalTable` typovou datovou sadu, je stále podporovaná tak, jak je, a až budete chtít začít používat nové.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností podporovaných zdrojem Amazon RedShift.

### <a name="amazon-redshift-as-source"></a>Amazon RedShift as source

Pokud chcete kopírovat data z Amazon RedShift, nastavte typ zdroje v aktivitě kopírování na **AmazonRedshiftSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **AmazonRedshiftSource** . | Ano |
| query |Pomocí vlastního dotazu můžete číst data. Příklad: select * from MyTable. |Ne (když je "tableName" v datové sadě zadán) |
| redshiftUnloadSettings | Skupina vlastností při použití aplikace Amazon RedShift Unload. | Ne |
| s3LinkedServiceName | Odkazuje na službu Amazon S3, která se používá jako dočasné úložiště, a to zadáním názvu propojené služby typu "AmazonS3". | Ano, pokud se používá uvolnění |
| bucketName | Označuje, že se má v poli S3 ukládat dočasná data. Pokud není zadaný, Služba Data Factory ji automaticky vygeneruje.  | Ano, pokud se používá uvolnění |

**Příklad: Amazon RedShift source v aktivitě kopírování pomocí uvolnění**

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

Přečtěte si další informace o tom, jak pomocí UVOLŇOVÁNí kopírovat data z Amazon RedShift efektivněji z další části.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Použití Unload ke kopírování dat z Amazon RedShift

[Unload](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) je mechanismus poskytovaný službou Amazon RedShift, který může uvolnit výsledky dotazu do jednoho nebo více souborů ve službě Amazon Simple Storage Service (Amazon S3). Tímto způsobem doporučuje Amazon pro kopírování velkých datových sad z RedShift.

**Příklad: kopírování dat z Amazon RedShift do Azure SQL Data Warehouse pomocí Unloaded, dvoufázové kopírování a základu**

Pro tento vzorový případ použití aktivita kopírování uvolní data z Amazon RedShift do Amazon S3, jak je nakonfigurované v "redshiftUnloadSettings", a pak zkopíruje data ze služby Amazon S3 do Azure Blob, jak je uvedeno v "stagingSettings", nakonec použije základnu k načtení dat do SQL data Skladu. Veškerý dočasný formát je zpracováván aktivitou kopírování správně.

![Pracovní postup kopírování RedShift do SQL datového skladu](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapování datových typů pro Amazon RedShift

Při kopírování dat z Amazon RedShift se z datových typů Amazon RedShift používají následující mapování k Azure Data Factory dočasných datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Typ dat Amazon RedShift | Data factory dočasné datový typ |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEAN |Řetězec |
| CHAR |Řetězec |
| DATE (Datum) |Datum a čas |
| NOTACI |Decimal |
| DOUBLE PRECISION |Double |
| INTEGER |Datový typ Int32 |
| REÁLNÉ |Jednoduchá |
| SMALLINT |Int16 |
| TEXT |Řetězec |
| TIMESTAMP |Datum a čas |
| VARCHAR |Řetězec |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
