---
title: Kopírování dat z Presto pomocí Azure Data Factory (Preview)
description: Naučte se, jak kopírovat data z Presto do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: 15f4133b03c1fe77548425500445937e86ed5a8e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87372497"
---
# <a name="copy-data-from-presto-using-azure-data-factory-preview"></a>Kopírování dat z Presto pomocí Azure Data Factory (Preview)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z Presto. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

> [!IMPORTANT]
> Tento konektor je momentálně ve verzi Preview. Můžete si to vyzkoušet a sdělit nám svůj názor. Pokud do svého řešení chcete zavést závislost na konektorech ve verzi Preview, kontaktujte [podporu Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Presto je podporován pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z Presto můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, takže nemusíte ručně instalovat žádné ovladače pomocí tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor Presto.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Presto jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **Presto** . | Yes |
| Hostitel | IP adresa nebo název hostitele serveru Presto (např. 192.168.222.160)  | Yes |
| serverVersion | Verze serveru Presto (např. 0,148-t)  | Yes |
| zařazení | Kontext katalogu pro všechny požadavky na serveru.  | Yes |
| port | Port TCP, který server Presto používá k naslouchání klientským připojením. Výchozí hodnota je 8080.  | No |
| authenticationType | Ověřovací mechanismus, který se používá pro připojení k serveru Presto. <br/>Povolené hodnoty jsou: **anonymní**, **LDAP** | Yes |
| username | Uživatelské jméno použité pro připojení k serveru Presto.  | No |
| heslo | Heslo odpovídající uživatelskému jménu. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | No |
| enableSsl | Určuje, jestli se připojení k serveru šifrují pomocí protokolu TLS. Výchozí hodnota je False.  | No |
| trustedCertPath | Úplná cesta k souboru. pem, který obsahuje certifikáty důvěryhodné certifikační autority pro ověření serveru při připojení přes protokol TLS. Tuto vlastnost lze nastavit pouze při použití protokolu TLS v místním prostředí IR. Výchozí hodnota je soubor cacerts. pem nainstalovaný s IR.  | No |
| useSystemTrustStore | Určuje, jestli se má použít certifikát certifikační autority z úložiště důvěryhodnosti systému nebo ze zadaného souboru PEM. Výchozí hodnota je False.  | No |
| allowHostNameCNMismatch | Určuje, jestli se má při připojování přes protokol TLS vyžadovat, aby název certifikátu TLS/SSL vydaný certifikační autoritou odpovídal názvu hostitele serveru. Výchozí hodnota je False.  | No |
| allowSelfSignedServerCert | Určuje, jestli se mají na serveru udělit certifikáty podepsané svým držitelem. Výchozí hodnota je False.  | No |
| timeZoneID | Místní časové pásmo používané připojením. Platné hodnoty pro tuto možnost se zadává v databázi časového pásma IANA. Výchozí hodnota je systémové časové pásmo.  | No |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných Presto DataSet.

Chcete-li kopírovat data z Presto, nastavte vlastnost Type datové sady na **PrestoObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **PrestoObject** . | Yes |
| schema | Název schématu. |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| stolu | Název tabulky |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem Tato vlastnost je podporována z důvodu zpětné kompatibility. `schema` `table` Pro nové zatížení použijte a. | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných Presto zdrojem.

### <a name="presto-as-source"></a>Presto as source

Chcete-li kopírovat data z Presto, nastavte typ zdroje v aktivitě kopírování na **PrestoSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **PrestoSource** . | Yes |
| query | Pro čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

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
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
