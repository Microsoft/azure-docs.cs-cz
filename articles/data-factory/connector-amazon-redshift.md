---
title: Kopírování dat z Amazon RedShift
description: Přečtěte si, jak kopírovat data z Amazon RedShift na podporovaná úložiště dat jímky pomocí Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 9441885766dad97dfc237ab81a59710245bf13ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100364252"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopírování dat z Amazon RedShift pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-amazon-redshift-connector.md)
> * [Aktuální verze](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z aplikace Amazon RedShift. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Amazon RedShift se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z Amazon RedShift můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor Amazon RedShift podporuje načítání dat z RedShift pomocí dotazů nebo integrované podpory RedShift uvolnění.

> [!TIP]
> Abyste dosáhli nejlepšího výkonu při kopírování velkých objemů dat z RedShift, zvažte použití integrovaného Redshiftu pro uvolnění prostřednictvím Amazon S3. Podrobnosti najdete v tématu věnovaném [kopírování dat z oddílu Amazon RedShift pomocí uvolnění](#use-unload-to-copy-data-from-amazon-redshift) .

## <a name="prerequisites"></a>Předpoklady

* Pokud kopírujete data do místního úložiště dat pomocí [Integration runtime](create-self-hosted-integration-runtime.md)v místním prostředí, udělte přístup ke clusteru Amazon RedShift Integration runtime (použijte IP adresu počítače). Pokyny najdete v tématu [autorizace přístupu ke clusteru](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) .
* Pokud kopírujete data do úložiště dat Azure, přečtěte si téma [rozsahy IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653) pro výpočetní IP adresu a rozsahy SQL používané datovými centry Azure.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro službu Amazon RedShift Connector.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Amazon RedShift jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **AmazonRedshift** . | Yes |
| server |IP adresa nebo název hostitele serveru Amazon RedShift Server. |Yes |
| port |Číslo portu TCP, který server Amazon RedShift používá k naslouchání klientským připojením. |Ne, výchozí hodnota je 5439 |
| database |Název databáze Amazon RedShift. |Yes |
| username |Jméno uživatele, který má přístup k databázi. |Yes |
| heslo |Heslo pro uživatelský účet. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Můžete použít Azure Integration Runtime nebo místní Integration Runtime (Pokud je úložiště dat umístěné v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. |No |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných sadou Amazon RedShift DataSet.

Chcete-li kopírovat data z Amazon RedShift, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **AmazonRedshiftTable** . | Yes |
| schema | Název schématu. |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem Tato vlastnost je podporována z důvodu zpětné kompatibility. `schema` `table` Pro nové zatížení použijte a. | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

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

Pokud jste používali `RelationalTable` typovou datovou sadu, je stále podporovaná tak, jak je, a až budete chtít začít používat novinku dál.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem Amazon RedShift.

### <a name="amazon-redshift-as-source"></a>Amazon RedShift as source

Pokud chcete kopírovat data z Amazon RedShift, nastavte typ zdroje v aktivitě kopírování na **AmazonRedshiftSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **AmazonRedshiftSource** . | Yes |
| query |Pomocí vlastního dotazu můžete číst data. Příklad: SELECT * FROM MyTable. |Ne (Pokud je zadáno "tableName" v datové sadě |
| redshiftUnloadSettings | Skupina vlastností při použití aplikace Amazon RedShift Unload. | No |
| s3LinkedServiceName | Odkazuje na službu Amazon S3, která se používá jako dočasné úložiště, a to zadáním názvu propojené služby typu "AmazonS3". | Ano, pokud se používá uvolnění |
| interval intervalu | Označuje, že se má v poli S3 ukládat dočasná data. Pokud není zadaný, Služba Data Factory ji automaticky vygeneruje.  | Ano, pokud se používá uvolnění |

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

**Příklad: kopírování dat z Amazon RedShift do Azure synapse Analytics pomocí Unloaded, dvoufázové kopírování a základu**

Pro tento vzorový případ použití aktivita kopírování uvolní data z Amazon RedShift do Amazon S3, jak je nakonfigurované v "redshiftUnloadSettings", a pak zkopíruje data ze služby Amazon S3 do Azure Blob, jak je uvedeno v "stagingSettings", a nakonec použije základnu k načtení dat do Azure synapse Analytics. Veškerý dočasný formát je zpracováván aktivitou kopírování správně.

![Pracovní postup kopírování RedShift do Azure synapse Analytics](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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

Při kopírování dat z Amazon RedShift se z datových typů Amazon RedShift používají následující mapování k Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování typů schématu a dat](copy-activity-schema-and-type-mapping.md) .

| Typ dat Amazon RedShift | Typ dat interim Data Factory |
|:--- |:--- |
| BIGINT |Int64 |
| DATOVÉHO |Řetězec |
| CHAR |Řetězec |
| DATE |DateTime |
| NOTACI |Decimal |
| DVOJITÁ PŘESNOST |dvojité |
| CELÉ ČÍSLO |Int32 |
| REÁLNÉ |Jednoduché |
| SMALLINT |Int16 |
| TEXT |Řetězec |
| ČASOVÉ RAZÍTKO |DateTime |
| VARCHAR |Řetězec |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
