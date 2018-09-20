---
title: Kopírování dat z Phoenixu s využitím Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z Phoenix úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: jingwang
ms.openlocfilehash: 78e432bf526ad270ae8543ad1be40727ed560d4b
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367895"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Kopírování dat z Phoenixu s využitím Azure Data Factory 

Tento článek ukazuje, jak použít aktivitu kopírování ke zkopírování dat z Phoenix ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z Phoenix do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Poskytuje integrované ovladače chcete umožnit připojení k Azure Data Factory, proto není nutné ručně nainstalovat všechny ovladače používání tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní Phoenix konektoru.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Phoenix propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **Phoenix** | Ano |
| hostitel | IP adresu nebo název hostitele serveru Phoenix. (to znamená 192.168.222.160)  | Ano |
| port | Port TCP, který Phoenix server používá k naslouchání pro připojení klientů. Výchozí hodnota je 8765. Pokud se připojíte k Azure HDInsights, zadejte port 443. | Ne |
| httpPath | Částečné adresa URL odpovídající Phoenix serveru. (to znamená /gateway/sandbox/phoenix/version). Zadejte `/hbasephoenix0` při použití HDInsights clusteru.  | Ne |
| authenticationType. | Mechanismus ověřování používaný pro připojení k serveru Phoenix. <br/>Povolené hodnoty jsou: **anonymní**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Ano |
| uživatelské jméno | Uživatelské jméno pro připojení k serveru Phoenix.  | Ne |
| heslo | Heslo odpovídající uživatelskému jménu. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| enableSsl | Určuje, zda jsou šifrované připojení k serveru pomocí SSL. Výchozí hodnota je false.  | Ne |
| trustedCertPath | Úplná cesta soubor .pem obsahující certifikáty důvěryhodné CA pro ověření serveru, při připojení přes protokol SSL. Tuto vlastnost lze nastavit pouze při použití protokolu SSL v místním prostředí IR. Výchozí hodnota je soubor cacerts.pem součástí IR.  | Ne |
| useSystemTrustStore | Určuje, jestli se má použít certifikát certifikační Autority ze systémového úložiště důvěryhodnosti nebo ze zadaného souboru PEM. Výchozí hodnota je false.  | Ne |
| allowHostNameCNMismatch | Určuje, jestli se vyžaduje název certifikátu SSL vydaný certifikační Autority tak, aby odpovídaly názvu hostitele serveru při připojení přes protokol SSL. Výchozí hodnota je false.  | Ne |
| allowSelfSignedServerCert | Určuje, jestli se má povolit certifikáty podepsané svým držitelem ze serveru. Výchozí hodnota je false.  | Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!NOTE]
>Pokud váš cluster nepodporuje rychlé relace například HDInsight, explicitně přidat index uzlu na konci nastavení cesty k protokolu http, zadejte třeba znak `/hbasephoenix0` místo `/hbasephoenix`.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje Phoenix datové sady.

Ke zkopírování dat z Phoenix, nastavte vlastnost typ datové sady na **PhoenixObject**. Neexistuje žádné další vlastnosti specifické pro typ. v tomto typu datové sady.

**Příklad**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Phoenix zdroje.

### <a name="phoenixsource-as-source"></a>PhoenixSource jako zdroj

Ke zkopírování dat z Phoenix, nastavte typ zdroje v aktivitě kopírování do **PhoenixSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu zdroje aktivity kopírování musí být nastavena na: **PhoenixSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM MyTable"`. | Ano |

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

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
