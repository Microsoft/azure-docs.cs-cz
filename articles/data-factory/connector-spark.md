---
title: Kopírování dat ze Sparku
description: Zjistěte, jak kopírovat data ze Sparku do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: c15241a2508a5d35f8eb84339cc584a651fcd5f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415172"
---
# <a name="copy-data-from-spark-using-azure-data-factory"></a>Kopírování dat ze Sparku pomocí Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat ze Sparku. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Spark je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data ze Sparku můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, proto není nutné ručně instalovat žádný ovladač pomocí tohoto konektoru.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro konektor Spark.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Spark jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **Spark** | Ano |
| host | Ip adresa nebo název hostitele serveru Spark  | Ano |
| port | Port TCP, který server Spark používá k naslouchání klientským připojením. Pokud se připojíte k Azure HDInsights, zadejte port jako 443. | Ano |
| serverTyp | Typ serveru Spark. <br/>Povolené hodnoty jsou: **SharkServer**, **SharkServer2**, **SparkThriftServer** | Ne |
| protokol thriftTransportProtocol | Transportní protokol, který se má použít ve vrstvě Šetrnost. <br/>Povolené hodnoty jsou: **Binární**, **SASL**, **HTTP** | Ne |
| authenticationType | Metoda ověřování používaná pro přístup k serveru Spark. <br/>Povolené hodnoty jsou: **Anonymní**, **Uživatelské jméno**, **Uživatelské jménoAndPassword**, **WindowsAzureHDInsightService** | Ano |
| uživatelské jméno | Uživatelské jméno, které používáte pro přístup k serveru Spark Server.  | Ne |
| heslo | Heslo odpovídající uživateli. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |
| httpPath | Částečná adresa URL odpovídající serveru Spark.  | Ne |
| enableSsl | Určuje, zda jsou připojení k serveru šifrována pomocí systému TLS. Výchozí hodnota je False.  | Ne |
| trustedCertPath | Úplná cesta k souboru PEM obsahujícího důvěryhodné certifikáty certifikační autority pro ověření serveru při připojování přes TLS. Tuto vlastnost lze nastavit pouze při použití TLS na samostatně hostované infračervené ovládání. Výchozí hodnota je soubor cacerts.pem nainstalovaný s infračerveným přenosem.  | Ne |
| useSystemTrustStore | Určuje, zda se má použít certifikát certifikační autority z úložiště důvěryhodných certifikátů systému nebo ze zadaného souboru PEM. Výchozí hodnota je False.  | Ne |
| allowHostNameCNMismatch | Určuje, zda má být při připojování přes TLS vyžadován název certifikátu TLS/SSL vydaný certifikační autoritou. Výchozí hodnota je False.  | Ne |
| allowSelfSignedServerCert | Určuje, zda mají být ze serveru povoleny certifikáty podepsané svým držitelem. Výchozí hodnota je False.  | Ne |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

**Příklad:**

```json
{
    "name": "SparkLinkedService",
    "properties": {
        "type": "Spark",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Spark.

Chcete-li kopírovat data ze Sparku, nastavte vlastnost type datové sady na **SparkObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **SparkObject.** | Ano |
| Schématu | Název schématu. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Použití `schema` `table` a pro nové pracovní zatížení. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "SparkDataset",
    "properties": {
        "type": "SparkObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Spark linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem Spark.

### <a name="spark-as-source"></a>Jiskra jako zdroj

Chcete-li kopírovat data ze Sparku, nastavte typ zdroje v aktivitě kopírování na **SparkSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: SparkSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSpark",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Spark input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SparkSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
