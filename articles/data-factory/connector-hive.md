---
title: Kopírování dat z Hive pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak ke zkopírování dat z Hive úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 5088d1d4f5b80e4de3dca1d8d41ee48feea12a46
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019737"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Kopírování dat z Hive pomocí Azure Data Factory 

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat z Hive ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z Hive do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Poskytuje integrované ovladače chcete umožnit připojení k Azure Data Factory, proto není nutné ručně nainstalovat všechny ovladače používání tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Hive.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Hive propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **Hive** | Ano |
| hostitel | IP adresa nebo název hostitele serveru Hive, oddělených pomocí ';' u více hostitelů (pouze v případě serviceDiscoveryMode povolit).  | Ano |
| port | Port TCP, který používá Hive server k naslouchání pro připojení klientů. Pokud se připojíte k Azure HDInsights, zadejte port 443. | Ano |
| serverType | Typ serveru Hive. <br/>Povolené hodnoty jsou: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Ne |
| thriftTransportProtocol | Protokol přenos, který se má použít ve vrstvě Thrift. <br/>Povolené hodnoty jsou: **Binární**, **SASL**, **HTTP** | Ne |
| authenticationType. | Metoda ověřování pro přístup k serveru Hive. <br/>Povolené hodnoty jsou: **Anonymní**, **uživatelské jméno**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Ano |
| serviceDiscoveryMode | Hodnota TRUE označuje pomocí služby ZooKeeper false není.  | Ne |
| zooKeeperNameSpace | Obor názvů na pod které Hive Server 2 jsou přidány uzly ZooKeeper.  | Ne |
| useNativeQuery | Určuje, jestli používá nativní dotazy HiveQL ovladače, nebo převede na ekvivalentní formulář v nástrojích pro HiveQL.  | Ne |
| uživatelské jméno | Uživatelské jméno, který používáte pro přístup k serveru Hive.  | Ne |
| heslo | Heslo, odpovídající uživatel. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| httpPath | Částečné adresa URL odpovídající serveru Hive.  | Ne |
| enableSsl | Určuje, zda jsou šifrované připojení k serveru pomocí SSL. Výchozí hodnota je false.  | Ne |
| trustedCertPath | Úplná cesta soubor .pem obsahující certifikáty důvěryhodné CA pro ověření serveru, při připojení přes protokol SSL. Tuto vlastnost lze nastavit pouze při použití protokolu SSL v místním prostředí IR. Výchozí hodnota je soubor cacerts.pem součástí IR.  | Ne |
| useSystemTrustStore | Určuje, jestli se má použít certifikát certifikační Autority ze systémového úložiště důvěryhodnosti nebo ze zadaného souboru PEM. Výchozí hodnota je false.  | Ne |
| allowHostNameCNMismatch | Určuje, jestli se vyžaduje název certifikátu SSL vydaný certifikační Autority tak, aby odpovídaly názvu hostitele serveru při připojení přes protokol SSL. Výchozí hodnota je false.  | Ne |
| allowSelfSignedServerCert | Určuje, jestli se má povolit certifikáty podepsané svým držitelem ze serveru. Výchozí hodnota je false.  | Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

**Příklad:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje Hive datové sady.

Ke zkopírování dat z Hive, nastavte vlastnost typ datové sady na **HiveObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **HiveObject** | Ano |
| tableName | Název tabulky. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem Hive.

### <a name="hivesource-as-source"></a>HiveSource jako zdroj

Ke zkopírování dat z Hive, nastavte typ zdroje v aktivitě kopírování do **HiveSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **HiveSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM MyTable"`. | Ne (když je "tableName" v datové sadě zadán) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
