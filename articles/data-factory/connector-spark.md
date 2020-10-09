---
title: Kopírování dat ze Sparku
description: Naučte se, jak kopírovat data ze Sparku do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81415172"
---
# <a name="copy-data-from-spark-using-azure-data-factory"></a>Kopírování dat ze Sparku pomocí Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z Sparku. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Spark se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z Sparku můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, takže nemusíte ručně instalovat žádné ovladače pomocí tohoto konektoru.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor Spark.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Spark jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **Spark** . | Ano |
| Hostitel | IP adresa nebo název hostitele Spark serveru  | Ano |
| port | Port TCP, který server Spark používá k naslouchání klientským připojením. Pokud se připojíte k Azure HDInsights, zadejte port jako 443. | Ano |
| serverType | Typ Spark serveru. <br/>Povolené hodnoty jsou: **SharkServer**, **SharkServer2**, **SparkThriftServer** | No |
| thriftTransportProtocol | Transportní protokol, který se má použít ve vrstvě Thrift. <br/>Povolené hodnoty jsou: **Binary**, **SASL**, **http** . | No |
| authenticationType | Metoda ověřování pro přístup k serveru Spark <br/>Povolené hodnoty jsou: **anonymní**, **username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Ano |
| username | Uživatelské jméno, které používáte pro přístup k Spark serveru.  | No |
| heslo | Heslo odpovídající uživateli Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | No |
| httpPath | Částečná adresa URL odpovídající serveru Spark.  | No |
| enableSsl | Určuje, jestli se připojení k serveru šifrují pomocí protokolu TLS. Výchozí hodnota je False.  | No |
| trustedCertPath | Úplná cesta k souboru. pem, který obsahuje certifikáty důvěryhodné certifikační autority pro ověření serveru při připojení přes protokol TLS. Tuto vlastnost lze nastavit pouze při použití protokolu TLS v místním prostředí IR. Výchozí hodnota je soubor cacerts. pem nainstalovaný s IR.  | No |
| useSystemTrustStore | Určuje, jestli se má použít certifikát certifikační autority z úložiště důvěryhodnosti systému nebo ze zadaného souboru PEM. Výchozí hodnota je False.  | No |
| allowHostNameCNMismatch | Určuje, jestli se má při připojování přes protokol TLS vyžadovat, aby název certifikátu TLS/SSL vydaný certifikační autoritou odpovídal názvu hostitele serveru. Výchozí hodnota je False.  | No |
| allowSelfSignedServerCert | Určuje, jestli se mají na serveru udělit certifikáty podepsané svým držitelem. Výchozí hodnota je False.  | No |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadaný, použije se výchozí Azure Integration Runtime. |No |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou Spark.

Chcete-li kopírovat data z Sparku, nastavte vlastnost Type datové sady na **SparkObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **SparkObject** . | Ano |
| schema | Název schématu. |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| stolu | Název tabulky |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem Tato vlastnost je podporována z důvodu zpětné kompatibility. `schema` `table` Pro nové zatížení použijte a. | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem Spark.

### <a name="spark-as-source"></a>Spark as source

Chcete-li kopírovat data ze Sparku, nastavte typ zdroje v aktivitě kopírování na **SparkSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **SparkSource** . | Ano |
| query | Pro čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

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

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
