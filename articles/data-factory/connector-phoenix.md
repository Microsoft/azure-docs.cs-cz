---
title: Kopírování dat z Phoenixu pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data z Phoenixu do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: d236b9f8800b644a0aa51597d01df1c1442475ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416779"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Kopírování dat z Phoenixu pomocí Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z Phoenixu. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Phoenix je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data z Phoenix u libovolného úložiště dat podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, proto není nutné ručně instalovat žádný ovladač pomocí tohoto konektoru.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Datové továrny specifické pro konektor Phoenix.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Phoenix jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **Phoenix** | Ano |
| host | Adresa IP nebo název hostitele serveru Phoenix. (tedy 192.168.222.160)  | Ano |
| port | Port TCP, který server Phoenix používá k naslouchání připojení klientů. Výchozí hodnota je 8765. Pokud se připojíte k Azure HDInsights, zadejte port jako 443. | Ne |
| httpPath | Částečná adresa URL odpovídající serveru Phoenix. (to znamená /gateway/sandbox/phoenix/version). Určete, zda používáte `/hbasephoenix0` cluster HDInsights.  | Ne |
| authenticationType | Mechanismus ověřování používaný k připojení k serveru Phoenix. <br/>Povolené hodnoty jsou: **Anonymní**, **Uživatelské jménoAndPassword**, **WindowsAzureHDInsightService** | Ano |
| uživatelské jméno | Uživatelské jméno použité pro připojení k serveru Phoenix.  | Ne |
| heslo | Heslo odpovídající uživatelskému jménu. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |
| enableSsl | Určuje, zda jsou připojení k serveru šifrována pomocí systému TLS. Výchozí hodnota je False.  | Ne |
| trustedCertPath | Úplná cesta k souboru PEM obsahujícího důvěryhodné certifikáty certifikační autority pro ověření serveru při připojování přes TLS. Tuto vlastnost lze nastavit pouze při použití TLS na samostatně hostované infračervené ovládání. Výchozí hodnota je soubor cacerts.pem nainstalovaný s infračerveným přenosem.  | Ne |
| useSystemTrustStore | Určuje, zda se má použít certifikát certifikační autority z úložiště důvěryhodných certifikátů systému nebo ze zadaného souboru PEM. Výchozí hodnota je False.  | Ne |
| allowHostNameCNMismatch | Určuje, zda má být při připojování přes TLS vyžadován název certifikátu TLS/SSL vydaný certifikační autoritou. Výchozí hodnota je False.  | Ne |
| allowSelfSignedServerCert | Určuje, zda mají být ze serveru povoleny certifikáty podepsané svým držitelem. Výchozí hodnota je False.  | Ne |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

>[!NOTE]
>Pokud váš cluster nepodporuje neschůdnou relaci, například HDInsight, explicitně přidejte index uzlu `/hbasephoenix0` na `/hbasephoenix`konci nastavení cesty http, například zadejte místo .

**Příklad:**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "/hbasephoenix0",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Phoenix.

Chcete-li zkopírovat data z Phoenix, nastavte vlastnost type datové sady **phoenixobject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **PhoenixObject.** | Ano |
| Schématu | Název schématu. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Použití `schema` `table` a pro nové pracovní zatížení. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem Phoenix.

### <a name="phoenix-as-source"></a>Phoenix jako zdroj

Chcete-li zkopírovat data z Phoenixu, nastavte typ zdroje v aktivitě kopírování na **PhoenixSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na: **PhoenixSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
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
