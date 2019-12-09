---
title: Kopírování dat z Presto pomocí Azure Data Factory (Preview)
description: Zjistěte, jak kopírovat data z Presto úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: 71bff5e3761d72236e6896733b96bd6e01460e52
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927808"
---
# <a name="copy-data-from-presto-using-azure-data-factory-preview"></a>Kopírování dat z Presto pomocí Azure Data Factory (Preview)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z Presto. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

> [!IMPORTANT]
> Tento konektor je aktuálně ve verzi preview. Můžete vyzkoušet a sdělte nám svůj názor. Pokud do svého řešení chcete zavést závislost na konektorech ve verzi Preview, kontaktujte [podporu Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Presto je podporován pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Kopírování dat z Presto do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Poskytuje integrované ovladače chcete umožnit připojení k Azure Data Factory, proto není nutné ručně nainstalovat všechny ovladače používání tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní Presto konektoru.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Presto propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **Presto** | Ano |
| hostitel | IP adresu nebo název hostitele serveru, Presto. (to znamená 192.168.222.160)  | Ano |
| serverVersion | Verze Presto serveru. (to znamená 0.148-t)  | Ano |
| catalog | Kontext pro všechny žádosti na serveru, který katalogu.  | Ano |
| port | Port TCP, Presto server používá pro naslouchání pro připojení klientů. Výchozí hodnota je 8080.  | Ne |
| authenticationType. | Mechanismus ověřování používaný pro připojení k Presto serveru. <br/>Povolené hodnoty jsou: **anonymní**, **LDAP** | Ano |
| uživatelské jméno | Uživatelské jméno pro připojení k serveru Presto.  | Ne |
| heslo | Heslo odpovídající uživatelskému jménu. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| enableSsl | Určuje, zda jsou šifrované připojení k serveru pomocí SSL. Výchozí hodnota je false.  | Ne |
| trustedCertPath | Úplná cesta soubor .pem obsahující certifikáty důvěryhodné CA pro ověření serveru, při připojení přes protokol SSL. Tuto vlastnost lze nastavit pouze při použití protokolu SSL v místním prostředí IR. Výchozí hodnota je soubor cacerts.pem součástí IR.  | Ne |
| useSystemTrustStore | Určuje, jestli se má použít certifikát certifikační Autority ze systémového úložiště důvěryhodnosti nebo ze zadaného souboru PEM. Výchozí hodnota je false.  | Ne |
| allowHostNameCNMismatch | Určuje, jestli se vyžaduje název certifikátu SSL vydaný certifikační Autority tak, aby odpovídaly názvu hostitele serveru při připojení přes protokol SSL. Výchozí hodnota je false.  | Ne |
| allowSelfSignedServerCert | Určuje, jestli se má povolit certifikáty podepsané svým držitelem ze serveru. Výchozí hodnota je false.  | Ne |
| timeZoneID | Místní časové pásmo používaný pro připojení. Platné hodnoty pro tuto možnost, jsou určené v databázi IANA časové pásmo. Výchozí hodnota je časové pásmo systému.  | Ne |

**Příklad:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje Presto datové sady.

Ke zkopírování dat z Presto, nastavte vlastnost typ datové sady na **PrestoObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ datové sady, musí být nastavena na: **PrestoObject** | Ano |
| schema | Název schématu. |Ne (když je zadán zdroj aktivity "query")  |
| table | Název tabulky. |Ne (když je zadán zdroj aktivity "query")  |
| tableName | Název tabulky se schématem Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové zatížení použijte `schema` a `table`. | Ne (když je zadán zdroj aktivity "query") |

**Příklad**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Presto zdroje.

### <a name="presto-as-source"></a>Presto jako zdroj

Ke zkopírování dat z Presto, nastavte typ zdroje v aktivitě kopírování do **PrestoSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu zdroje aktivity kopírování musí být nastavena na: **PrestoSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM MyTable"`. | Ne (když je "tableName" v datové sadě zadán) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
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
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
