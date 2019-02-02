---
title: Kopírování dat z Impala pomocí služby Azure Data Factory (Preview) | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z Impala úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu datové továrny.
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
ms.openlocfilehash: f86931aad4eab697e4a0d2dfc47a6d4ff5bfc256
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565679"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory-preview"></a>Kopírování dat z Impala pomocí služby Azure Data Factory (Preview)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z Impala. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

> [!IMPORTANT]
> Tento konektor je aktuálně ve verzi preview. Můžete vyzkoušet a poskytnout zpětnou vazbu. Pokud do svého řešení chcete zavést závislost na konektorech ve verzi Preview, kontaktujte [podporu Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z Impala do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

 Data Factory poskytuje integrovaný ovladač umožňující připojení. Proto není nutné ručně nainstalovat ovladač pro tento konektor použít.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Impala.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro aplikaci Impala propojenou službu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **Impala**. | Ano |
| hostitel | IP adresu nebo název hostitele serveru Impala (192.168.222.160).  | Ano |
| port | Port TCP, který Impala server používá k naslouchání pro připojení klientů. Výchozí hodnota je 21050.  | Ne |
| authenticationType. | Typ ověřování, který se má použít. <br/>Povolené hodnoty jsou **anonymní**, **SASLUsername**, a **UsernameAndPassword**. | Ano |
| uživatelské jméno | Uživatelské jméno pro přístup k serveru Impala. Výchozí hodnota je anonymní, když použijete SASLUsername.  | Ne |
| heslo | Heslo, které odpovídá uživatelskému jménu, při použití UsernameAndPassword. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| enableSsl | Určuje, zda jsou šifrovaná připojení k serveru pomocí SSL. Výchozí hodnota je **false**.  | Ne |
| trustedCertPath | Úplná cesta soubor .pem, který obsahuje certifikáty důvěryhodné certifikační Autority používaného k ověřování serveru, když se připojíte přes protokol SSL. Tuto vlastnost lze nastavit pouze při použití protokolu SSL na modul Integration Runtime. Výchozí hodnota je soubor cacerts.pem nainstalované s modulem runtime integrace.  | Ne |
| useSystemTrustStore | Určuje, jestli se má použít certifikát certifikační Autority ze systémového úložiště důvěryhodnosti nebo ze zadaného souboru PEM. Výchozí hodnota je **false**.  | Ne |
| allowHostNameCNMismatch | Určuje, jestli se vyžaduje název certifikátu SSL vydaný certifikační Autority tak, aby odpovídaly názvu hostitele serveru, když se připojíte přes protokol SSL. Výchozí hodnota je **false**.  | Ne |
| allowSelfSignedServerCert | Určuje, jestli se má povolit certifikáty podepsané svým držitelem ze serveru. Výchozí hodnota je **false**.  | Ne |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností podporovaných datovou sadu Impala.

Ke zkopírování dat z Impala, nastavte vlastnost typ datové sady na **ImpalaObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **ImpalaObject** | Ano |
| tableName | Název tabulky. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaný typ zdroje Impala.

### <a name="impala-as-a-source-type"></a>Impala jako typ zdroje

Ke zkopírování dat z Impala, nastavte typ zdroje v aktivitě kopírování do **ImpalaSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na **ImpalaSource**. | Ano |
| query | Použijte vlastní dotaz SQL číst data. Příklad: `"SELECT * FROM MyTable"`. | Ne (když je "tableName" v datové sadě zadán) |

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

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
