---
title: Kopírování dat z Amazon Redshift
description: Zjistěte, jak kopírovat data z Amazon Redshift do podporovaných úložišť dat jímky pomocí Azure Data Factory.
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
ms.openlocfilehash: ce63da745fb84ebccd57b246fc934f595dd7cda1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418248"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopírování dat z Amazon Redshift pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-amazon-redshift-connector.md)
> * [Aktuální verze](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z Amazon Redshift. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Amazon Redshift je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data z Amazon Redshift do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor Amazon Redshift podporuje načítání dat z Redshift pomocí dotazu nebo integrované podpory Redshift UNLOAD.

> [!TIP]
> Chcete-li dosáhnout nejlepšího výkonu při kopírování velkého množství dat z Redshift, zvažte použití vestavěného Redshift UNLOAD přes Amazon S3. Podrobnosti najdete [v tématu Kopírování dat z oddílu Amazon Redshift pomocí příkazu UNLOAD.](#use-unload-to-copy-data-from-amazon-redshift)

## <a name="prerequisites"></a>Požadavky

* Pokud kopírujete data do místního úložiště dat pomocí [prostředí Integration Runtime](create-self-hosted-integration-runtime.md)s vlastním hostitelem , udělte integračnímu runtime (použijte IP adresu počítače) přístup ke clusteru Amazon Redshift. Pokyny naleznete [v tématu Autorizace přístupu ke clusteru.](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html)
* Pokud kopírujete data do úložiště dat Azure, přečtěte si informace o [rozsahu IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653) pro výpočetní IP adresu a oblasti SQL používané datovými centry Azure.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit datové továrny specifických pro konektor Amazon Redshift.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Amazon Redshift jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **AmazonRedshift** | Ano |
| server |IP adresa nebo název hostitele serveru Amazon Redshift. |Ano |
| port |Číslo portu TCP, který server Amazon Redshift používá k naslouchání pro připojení klientů. |Ne, výchozí hodnota je 5439 |
| database |Název databáze Amazon Redshift. |Ano |
| uživatelské jméno |Jméno uživatele, který má přístup k databázi. |Ano |
| heslo |Heslo pro uživatelský účet. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime (pokud vaše úložiště dat se nachází v privátní síti). Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Amazon Redshift.

Chcete-li kopírovat data z Amazon Redshift, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **AmazonRedshiftTable** | Ano |
| Schématu | Název schématu. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Použití `schema` `table` a pro nové pracovní zatížení. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

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

Pokud jste `RelationalTable` používali zadaný datový soubor, je stále podporována tak, jak je, zatímco se doporučuje používat novou do budoucna.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem Amazon Redshift.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift jako zdroj

Chcete-li zkopírovat data z Amazon Redshift, nastavte typ zdroje v aktivitě kopírování **na AmazonRedshiftSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na: **AmazonRedshiftSource** | Ano |
| query |Ke čtení dat použijte vlastní dotaz. Příklad: vyberte * z MyTable. |Ne (pokud je v datové sadě zadán "název_tabulky") |
| redshiftUnloadSettings | Property group při použití Amazon Redshift UNLOAD. | Ne |
| s3LinkedServiceName | Odkazuje na Amazon S3, který má být použit jako dočasné úložiště zadáním názvu propojené služby typu "AmazonS3". | Ano, pokud používáte UNLOAD |
| název_bloku | Označte kontejner S3 pro uložení dočasných dat. Pokud není k dispozici, služba Data Factory ji vygeneruje automaticky.  | Ano, pokud používáte UNLOAD |

**Příklad: Amazon Redshift zdroj v kopírování činnosti pomocí UNLOAD**

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

Další informace o tom, jak používat UNLOAD efektivně kopírovat data z Amazon Redshift z další části.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Použití UNLOAD ke kopírování dat z Amazon Redshift

[UNLOAD](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) je mechanismus poskytovaný společností Amazon Redshift, který může uvolnit výsledky dotazu do jednoho nebo více souborů na Amazon Simple Storage Service (Amazon S3). Je to způsob, jakým amazon doporučuje kopírování velkých datových souborů z Redshift.

**Příklad: kopírování dat z Amazon Redshift do Azure SQL Data Warehouse pomocí UNLOAD, fázované kopie a PolyBase**

V tomto případě ukázkového použití zkopíruje aktivita uvolní data z Amazon Redshift na Amazon S3 nakonfigurovaná v "redshiftUnloadSettings" a pak zkopíruje data z AmazonS3 do objektu Blob Azure, jak je uvedeno v "stagingSettings", nakonec použijte PolyBase k načtení dat do datového skladu SQL. Veškerý dočasný formát je správně zpracován aktivitou kopírování.

![Červený posun na pracovní postup kopírování SQL DW](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapování datových typů pro Amazon Redshift

Při kopírování dat z Amazon Redshift se používají následující mapování z datových typů Amazon Redshift do dočasných datových typů Azure Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování schématu a datových typů.](copy-activity-schema-and-type-mapping.md)

| Datový typ Amazon Redshift | Dočasný datový typ datové továrny |
|:--- |:--- |
| Bigint |Int64 |
| Boolean |Řetězec |
| Char |Řetězec |
| DATE (Datum) |DateTime |
| Desetinných |Desetinné číslo |
| DVOJITÁ PŘESNOST |Double |
| CELÉ ČÍSLO |Int32 |
| REÁLNÉ |Single |
| Smallint |Int16 |
| TEXT |Řetězec |
| Časové razítko |DateTime |
| Varchar |Řetězec |

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
