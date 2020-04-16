---
title: Kopírování dat z Impala pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data z Impala do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu datové továrny.
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
ms.openlocfilehash: b70db03e03ce914ea1d81d94cd2803a36eccfc88
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418214"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Kopírování dat z Impala pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí kopírovat aktivity v Azure Data Factory ke kopírování dat z Impala. Vychází z článku [přehled aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Impala je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data z Impala do libovolného úložiště dat podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivity kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Data Factory poskytuje integrovaný ovladač pro povolení připojení. Proto není nutné ručně nainstalovat ovladač používat tento konektor.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor Impala.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Následující vlastnosti jsou podporovány pro službu propojenou s Impala.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **Impala**. | Ano |
| host | Ip adresa nebo název hostitele serveru Impala (tj. 192.168.222.160).  | Ano |
| port | Port TCP, který server Impala používá k naslouchání pro připojení klientů. Výchozí hodnota je 21050.  | Ne |
| authenticationType | Typ ověřování, který chcete použít. <br/>Povolené hodnoty jsou **Anonymní**, **SASLUsername**a **UsernameAndPassword**. | Ano |
| uživatelské jméno | Uživatelské jméno používané pro přístup k serveru Impala. Výchozí hodnota je anonymní při použití SASLUsername.  | Ne |
| heslo | Heslo, které odpovídá uživatelské jméno při použití UsernameAndPassword. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |
| enableSsl | Určuje, zda jsou připojení k serveru šifrována pomocí tls. Výchozí hodnota je **false** (nepravda).  | Ne |
| trustedCertPath | Úplná cesta k souboru PEM, který obsahuje důvěryhodné certifikáty certifikační autority používané k ověření serveru při připojení přes tls. Tuto vlastnost lze nastavit pouze v případě, že používáte TLS na prostředí integrace s vlastním hostitelem. Výchozí hodnota je soubor cacerts.pem nainstalovaný s integračním runtime.  | Ne |
| useSystemTrustStore | Určuje, zda se má použít certifikát certifikační autority z úložiště důvěryhodných certifikátů systému nebo ze zadaného souboru PEM. Výchozí hodnota je **false** (nepravda).  | Ne |
| allowHostNameCNMismatch | Určuje, zda má být při připojení přes TLS vyžadován název certifikátu TLS/SSL vydaný certifikační autoritou. Výchozí hodnota je **false** (nepravda).  | Ne |
| allowSelfSignedServerCert | Určuje, zda mají být ze serveru povoleny certifikáty podepsané svým držitelem. Výchozí hodnota je **false** (nepravda).  | Ne |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

**Příklad:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Impala.

Chcete-li kopírovat data z Impala, nastavte vlastnost type datové sady na **ImpalaObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **ImpalaObject.** | Ano |
| Schématu | Název schématu. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Použití `schema` `table` a pro nové pracovní zatížení. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných typem zdroje Impala.

### <a name="impala-as-a-source-type"></a>Impala jako zdrojový typ

Chcete-li kopírovat data z Impala, nastavte typ zdroje v aktivitě kopírování na **ImpalaSource**. Následující vlastnosti jsou podporovány v části **zdroje aktivity** kopírování.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na **ImpalaSource**. | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
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
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v datové továrně naleznete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
