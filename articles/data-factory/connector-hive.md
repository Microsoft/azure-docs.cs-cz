---
title: Kopírování dat z Hive pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data z Hive do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 3720d917d71fa4e8c5a14bb60fdc7c405be4bfdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410455"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Kopírování dat z Hive pomocí Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z Hive. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Hive je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data z Hive do libovolného úložiště dat podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, proto není nutné ručně instalovat žádný ovladač pomocí tohoto konektoru.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor Hive.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Hive jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **Hive** | Ano |
| host | IP adresa nebo název hostitele serveru Hive, oddělený ';' pro více hostitelů (pouze v případě, že je povolen serviceDiscoveryMode).  | Ano |
| port | Port TCP, který server Hive používá k naslouchání klientským připojením. Pokud se připojíte k Azure HDInsights, zadejte port jako 443. | Ano |
| serverTyp | Typ serveru Hive. <br/>Povolené hodnoty jsou: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Ne |
| protokol thriftTransportProtocol | Transportní protokol, který se má použít ve vrstvě Šetrnost. <br/>Povolené hodnoty jsou: **Binární**, **SASL**, **HTTP** | Ne |
| authenticationType | Metoda ověřování používaná pro přístup k serveru Hive. <br/>Povolené hodnoty jsou: **Anonymní**, **Uživatelské jméno**, **Uživatelské jménoAndPassword**, **WindowsAzureHDInsightService** | Ano |
| serviceDiscoveryMode | true označit pomocí služby ZooKeeper, false not.  | Ne |
| zooKeeperNameSpace | Obor názvů na ZooKeeper, pod kterým hive server 2 uzly jsou přidány.  | Ne |
| useNativeQuery | Určuje, zda ovladač používá nativní dotazy HiveQL, nebo je převede na ekvivalentní formulář v HiveQL.  | Ne |
| uživatelské jméno | Uživatelské jméno, které používáte pro přístup k serveru Hive.  | Ne |
| heslo | Heslo odpovídající uživateli. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |
| httpPath | Částečná adresa URL odpovídající serveru Hive.  | Ne |
| enableSsl | Určuje, zda jsou připojení k serveru šifrována pomocí systému TLS. Výchozí hodnota je False.  | Ne |
| trustedCertPath | Úplná cesta k souboru PEM obsahujícího důvěryhodné certifikáty certifikační autority pro ověření serveru při připojování přes TLS. Tuto vlastnost lze nastavit pouze při použití TLS na samostatně hostované infračervené ovládání. Výchozí hodnota je soubor cacerts.pem nainstalovaný s infračerveným přenosem.  | Ne |
| useSystemTrustStore | Určuje, zda se má použít certifikát certifikační autority z úložiště důvěryhodných certifikátů systému nebo ze zadaného souboru PEM. Výchozí hodnota je False.  | Ne |
| allowHostNameCNMismatch | Určuje, zda má být při připojování přes TLS vyžadován název certifikátu TLS/SSL vydaný certifikační autoritou. Výchozí hodnota je False.  | Ne |
| allowSelfSignedServerCert | Určuje, zda mají být ze serveru povoleny certifikáty podepsané svým držitelem. Výchozí hodnota je False.  | Ne |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Hive.

Chcete-li kopírovat data z Úlu, nastavte vlastnost type datové sady na **HiveObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **HiveObject.** | Ano |
| Schématu | Název schématu. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky včetně části schématu. Tato vlastnost je podporována pro zpětnou kompatibilitu. Pro nové pracovní `schema` `table`vytížení, použití a . | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem Hive.

### <a name="hivesource-as-source"></a>HiveSource jako zdroj

Chcete-li kopírovat data z Úlu, nastavte typ zdroje v aktivitě kopírování na **HiveSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: HiveSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

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

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
