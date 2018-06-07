---
title: Kopírování dat z Impala pomocí Azure Data Factory (beta) | Microsoft Docs
description: Postup kopírování dat z Impala do úložiště dat podporovaných podřízený pomocí aktivity kopírování v kanálu data factory.
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 73987d03cb96fa421d193504fe6eaf6c3b5ddb18
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618765"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory-beta"></a>Kopírování dat z Impala pomocí Azure Data Factory (beta)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z Impala. Vychází [aktivity kopírování přehled](copy-activity-overview.md) článek, který představuje obecný přehled o aktivitě kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 objektu pro vytváření dat, která je obecně k dispozici, najdete v části [aktivity kopírování v verze 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Tento konektor je aktuálně ve verzi beta. Můžete si vyzkoušet a poskytnout zpětnou vazbu. Nepoužívejte ji v produkčním prostředí.

## <a name="supported-capabilities"></a>Podporované možnosti

Data můžete zkopírovat z Impala do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

 Data Factory poskytuje integrované ovladače pro umožnění připojení. Proto nemusíte ručně nainstalovat ovladač, který chcete použít tento konektor.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru Impala.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro Impala propojené služby.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavená na **Impala**. | Ano |
| hostitel | IP adresu nebo název hostitele serveru Impala (192.168.222.160).  | Ano |
| port | Port TCP, který používá Impala server naslouchat pro připojení klientů. Výchozí hodnota je 21050.  | Ne |
| authenticationType. | Typ ověřování používat. <br/>Povolené hodnoty jsou **anonymní**, **SASLUsername**, a **UsernameAndPassword**. | Ano |
| uživatelské jméno | Uživatelské jméno pro přístup k serveru Impala. Výchozí hodnota je anonymní, při použití SASLUsername.  | Ne |
| heslo | Heslo, které odpovídá uživatelskému jménu, při použití UsernameAndPassword. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| enableSsl | Určuje, zda připojení k serveru jsou šifrována pomocí protokolu SSL. Výchozí hodnota je **false**.  | Ne |
| trustedCertPath | Úplná cesta soubor .pem, který obsahuje certifikáty důvěryhodné certifikační Autority používají k ověření serveru, jakmile se připojíte přes protokol SSL. Tuto vlastnost lze nastavit pouze při použití protokolu SSL na Self-hosted integrace Runtime. Výchozí hodnota je soubor cacerts.pem nainstalovaný s modulem runtime integrace.  | Ne |
| useSystemTrustStore | Určuje, jestli se má použít certifikát Certifikační autority z úložiště důvěryhodnosti systému nebo z určeného souboru PEM. Výchozí hodnota je **false**.  | Ne |
| allowHostNameCNMismatch | Určuje, jestli chcete vyžadovat protokol SSL vydaný certifikační Autoritou název certifikátu shodovat s názvem hostitele serveru při připojení přes protokol SSL. Výchozí hodnota je **false**.  | Ne |
| allowSelfSignedServerCert | Určuje, jestli se má povolit certifikáty podepsané svým držitelem ze serveru. Výchozí hodnota je **false**.  | Ne |
| connectVia | [Integrace runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je veřejně přístupná data store), můžete použít modul Runtime integrace Self-hosted nebo Runtime integrace Azure. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datové sady](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností nepodporuje Impala datovou sadu.

Ke zkopírování dat z Impala, nastavte vlastnost typu datové sady, která **ImpalaObject**. Není k dispozici žádné další vlastnosti specifické pro typ v tomto typu datové sady.

**Příklad**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje typ Impala zdroje.

### <a name="impala-as-a-source-type"></a>Impala jako typ zdroje

Ke zkopírování dat z Impala, nastavte typ zdroje v aktivitě kopírování do **ImpalaSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na **ImpalaSource**. | Ano |
| query | Čtení dat pomocí vlastního dotazu SQL. Příklad: `"SELECT * FROM MyTable"`. | Ano |

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
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v objektu pro vytváření dat najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
