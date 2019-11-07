---
title: Kopírování dat z podregistru pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z podregistru do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: 6bc644c960fdfa24c7ae7e190d5a110cdba41f9c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680851"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Kopírování dat z podregistru pomocí Azure Data Factory 

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z podregistru. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor podregistru se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data z podregistru můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, takže nemusíte ručně instalovat žádné ovladače pomocí tohoto konektoru.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor podregistru.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu podregistru jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **podregistr** . | Ano |
| Provoz | IP adresa nebo název hostitele serveru pro podregistr, oddělený znakem '; ' pro více hostitelů (pouze v případě, že je povoleno serviceDiscoveryMode).  | Ano |
| port | Port TCP, který server podregistru používá k naslouchání klientským připojením. Pokud se připojíte k Azure HDInsights, zadejte port jako 443. | Ano |
| serverType | Typ serveru podregistru <br/>Povolené hodnoty jsou: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Ne |
| thriftTransportProtocol | Transportní protokol, který se má použít ve vrstvě Thrift. <br/>Povolené hodnoty jsou: **Binary**, **SASL**, **http** . | Ne |
| authenticationType | Metoda ověřování pro přístup k serveru podregistru <br/>Povolené hodnoty jsou: **anonymní**, **username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Ano |
| serviceDiscoveryMode | true pro indikaci použití služby ZooKeeper, NEPRAVDA.  | Ne |
| zooKeeperNameSpace | Obor názvů na ZooKeeper, do kterého se přidají uzly pro podregistr Server 2  | Ne |
| useNativeQuery | Určuje, zda ovladač používá nativní dotazy HiveQL, nebo je převede do ekvivalentního formátu v HiveQL.  | Ne |
| uživatelské jméno | Uživatelské jméno, které používáte pro přístup k serveru podregistru.  | Ne |
| heslo | Heslo odpovídající uživateli Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| httpPath | Částečná adresa URL odpovídající serveru podregistru.  | Ne |
| enableSsl | Určuje, zda jsou připojení k serveru šifrována pomocí protokolu SSL. Výchozí hodnota je false (NEPRAVDA).  | Ne |
| trustedCertPath | Úplná cesta k souboru. pem, který obsahuje certifikáty důvěryhodné certifikační autority pro ověření serveru při připojení přes protokol SSL. Tuto vlastnost lze nastavit pouze při použití protokolu SSL v místním prostředí IR. Výchozí hodnota je soubor cacerts. pem nainstalovaný s IR.  | Ne |
| useSystemTrustStore | Určuje, jestli se má použít certifikát certifikační autority z úložiště důvěryhodnosti systému nebo ze zadaného souboru PEM. Výchozí hodnota je false (NEPRAVDA).  | Ne |
| allowHostNameCNMismatch | Určuje, jestli se má při připojování přes protokol SSL vyžadovat, aby název certifikátu SSL vydaný certifikační autoritou odpovídal názvu hostitele serveru. Výchozí hodnota je false (NEPRAVDA).  | Ne |
| allowSelfSignedServerCert | Určuje, jestli se mají na serveru udělit certifikáty podepsané svým držitelem. Výchozí hodnota je false (NEPRAVDA).  | Ne |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadaný, použije se výchozí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou podregistru.

Chcete-li kopírovat data z podregistru, nastavte vlastnost Type datové sady na **HiveObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **HiveObject** . | Ano |
| schema | Název schématu. |Ne (když je zadán zdroj aktivity "query")  |
| stolní | Název tabulky |Ne (když je zadán zdroj aktivity "query")  |
| tableName | Název tabulky včetně části schématu Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `schema` a `table`. | Ne (když je zadán zdroj aktivity "query") |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem podregistru.

### <a name="hivesource-as-source"></a>HiveSource as source

Chcete-li kopírovat data z podregistru, nastavte typ zdroje v aktivitě kopírování na **HiveSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **HiveSource** . | Ano |
| query | Pro čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

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

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
