---
title: Kopírování dat z Hive pomocí Azure Data Factory | Microsoft Docs
description: Postup kopírování dat z podregistru do úložiště dat podporovaných podřízený pomocí aktivity kopírování v kanál služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 0b216b334f07a59f7c159bc804116f2190f3bf6d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Kopírování dat z Hive pomocí Azure Data Factory 

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z Hive. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [aktivitu kopírování v V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Data můžete zkopírovat z podregistru do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Azure Data Factory poskytuje integrované ovladače pro umožnění připojení, proto nemusíte ručně nainstalovat všechny ovladače, používání tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru Hive.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Hive propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **Hive** | Ano |
| hostitel | IP adresa nebo název hostitele serveru Hive, oddělené podle ';' pro více hostitelů (pouze v případě serviceDiscoveryMode je povolit).  | Ano |
| port | Port TCP, který používá Hive server naslouchat pro připojení klientů.  | Ne |
| serverType | Typ serveru Hive. <br/>Povolené hodnoty jsou: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Ne |
| thriftTransportProtocol | Přenosový protokol pro použití v Thrift vrstvy. <br/>Povolené hodnoty jsou: **binární**, **SASL**, **HTTP** | Ne |
| authenticationType. | Metodu ověřování pro přístup k serveru Hive. <br/>Povolené hodnoty jsou: **anonymní**, **uživatelské jméno**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Ano |
| serviceDiscoveryMode | TRUE označuje pomocí služby ZooKeeper false není.  | Ne |
| zooKeeperNameSpace | Obor názvů na ZooKeeper pod které Hive Server 2 jsou přidány uzly.  | Ne |
| useNativeQuery | Určuje, zda používá nativní dotazy HiveQL ovladače, nebo je převede na ekvivalentní formuláře v HiveQL.  | Ne |
| uživatelské jméno | Uživatelské jméno, který používáte pro přístup k serveru Hive.  | Ne |
| heslo | Heslo odpovídající uživateli. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| httpPath | Částečné adresa URL odpovídající serveru Hive.  | Ne |
| enableSsl | Určuje, zda jsou šifrované připojení k serveru pomocí protokolu SSL. Výchozí hodnota je false.  | Ne |
| trustedCertPath | Úplná cesta soubor .pem, který obsahuje certifikáty důvěryhodné certifikační Autority pro ověření serveru při připojení přes protokol SSL. Tuto vlastnost lze nastavit pouze při použití protokolu SSL na vlastním hostováním infračerveného signálu. Výchozí hodnota je soubor cacerts.pem nainstalované s infračerveného signálu.  | Ne |
| useSystemTrustStore | Určuje, jestli se má použít certifikát Certifikační autority z úložiště důvěryhodnosti systému nebo z určeného souboru PEM. Výchozí hodnota je false.  | Ne |
| allowHostNameCNMismatch | Určuje, jestli chcete vyžadovat protokol SSL vydaný certifikační Autoritou název certifikátu shodovat s názvem hostitele serveru při připojení přes protokol SSL. Výchozí hodnota je false.  | Ne |
| allowSelfSignedServerCert | Určuje, jestli se má povolit certifikáty podepsané svým držitelem ze serveru. Výchozí hodnota je false.  | Ne |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je veřejně přístupná data store), můžete použít modul Runtime integrace Self-hosted nebo Runtime integrace Azure. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

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
            },
            "httpPath" : "gateway/sandbox/spark"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datové sady](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností nepodporuje datovou sadu Hive.

Ke zkopírování dat z Hive, nastavte vlastnost typu datové sady, která **HiveObject**. Není k dispozici žádné další vlastnosti specifické pro typ v tomto typu datové sady.

**Příklad**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které jsou podporovány zdrojem Hive.

### <a name="hivesource-as-source"></a>HiveSource jako zdroj

Ke zkopírování dat z Hive, nastavte typ zdroje v aktivitě kopírování do **HiveSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **HiveSource** | Ano |
| query | Čtení dat pomocí vlastního dotazu SQL. Například: `"SELECT * FROM MyTable"`. | Ano |

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
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
