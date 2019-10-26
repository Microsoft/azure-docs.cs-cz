---
title: Kopírování dat z Impala pomocí Azure Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data z Impala do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu služby Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 54f46c09cfab64d53e8f5f503ca46004289f18c2
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935570"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Kopírování dat z Impala pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z Impala. Vytvoří se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Impala je podporován pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data z Impala můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje nebo jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Data Factory poskytuje integrovaný ovladač, který umožňuje připojení. Proto nemusíte ručně instalovat ovladač pro použití tohoto konektoru.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začít

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor Impala.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Impala jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **Impala**. | Ano |
| Provoz | IP adresa nebo název hostitele serveru Impala (tj. 192.168.222.160).  | Ano |
| port | Port TCP, který server Impala používá k naslouchání klientským připojením. Výchozí hodnota je 21050.  | Ne |
| authenticationType | Typ ověřování, který se má použít. <br/>Povolené hodnoty jsou **anonymní**, **SASLUsername**a **UsernameAndPassword**. | Ano |
| uživatelské jméno | Uživatelské jméno, které se používá pro přístup k serveru Impala. Výchozí hodnota je anonymní, pokud používáte SASLUsername.  | Ne |
| heslo | Heslo, které odpovídá uživatelskému jménu při použití UsernameAndPassword. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| enableSsl | Určuje, zda jsou připojení k serveru šifrována pomocí protokolu SSL. Výchozí hodnota je **false (NEPRAVDA**).  | Ne |
| trustedCertPath | Úplná cesta k souboru. pem, který obsahuje certifikáty důvěryhodné certifikační autority, které se používají k ověření serveru při připojení přes protokol SSL. Tuto vlastnost lze nastavit pouze v případě, že používáte protokol SSL v Integration Runtime v místním prostředí. Výchozí hodnota je soubor cacerts. pem nainstalovaný v prostředí Integration runtime.  | Ne |
| useSystemTrustStore | Určuje, jestli se má použít certifikát certifikační autority z úložiště důvěryhodnosti systému nebo ze zadaného souboru PEM. Výchozí hodnota je **false (NEPRAVDA**).  | Ne |
| allowHostNameCNMismatch | Určuje, jestli se má při připojení přes SSL vyžadovat, aby název certifikátu SSL vydaný certifikační autoritou odpovídal názvu hostitele serveru. Výchozí hodnota je **false (NEPRAVDA**).  | Ne |
| allowSelfSignedServerCert | Určuje, jestli se mají na serveru udělit certifikáty podepsané svým držitelem. Výchozí hodnota je **false (NEPRAVDA**).  | Ne |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadaný, použije se výchozí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou Impala.

Chcete-li kopírovat data z Impala, nastavte vlastnost Type datové sady na **ImpalaObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **ImpalaObject** . | Ano |
| XSD | Název schématu. |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| Stolní | Název tabulky |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové zatížení použijte `schema` a `table`. | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných Impala typem zdroje.

### <a name="impala-as-a-source-type"></a>Impala jako typ zdroje

Chcete-li kopírovat data z Impala, nastavte typ zdroje v aktivitě kopírování na **ImpalaSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **ImpalaSource**. | Ano |
| query | Pro čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

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

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
