---
title: Kopírování dat z Presto pomocí Azure Data Factory (Preview)
description: Zjistěte, jak kopírovat data z Presto do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927808"
---
# <a name="copy-data-from-presto-using-azure-data-factory-preview"></a>Kopírování dat z Presto pomocí Azure Data Factory (Preview)

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z Presto. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

> [!IMPORTANT]
> Tento konektor je aktuálně ve verzi Preview. Můžete to vyzkoušet a dát nám zpětnou vazbu. Pokud do svého řešení chcete zavést závislost na konektorech ve verzi Preview, kontaktujte [podporu Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Presto je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z aplikace Presto můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, proto není nutné ručně instalovat žádný ovladač pomocí tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro konektor Presto.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Presto jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **Presto** | Ano |
| host | Adresa IP nebo název hostitele serveru Presto. (tj. 192.168.222.160)  | Ano |
| serverVersion | Verze serveru Presto. (tj. 0,148 t)  | Ano |
| Katalogu | Kontext katalogu pro všechny požadavky proti serveru.  | Ano |
| port | Port TCP, který server Presto používá k naslouchání pro připojení klientů. Výchozí hodnota je 8080.  | Ne |
| authenticationType | Mechanismus ověřování používaný k připojení k serveru Presto. <br/>Povolené hodnoty jsou: **Anonymní**, **LDAP** | Ano |
| uživatelské jméno | Uživatelské jméno použité pro připojení k serveru Presto.  | Ne |
| heslo | Heslo odpovídající uživatelskému jménu. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |
| enableSsl | Určuje, zda jsou připojení k serveru šifrována pomocí ssl. Výchozí hodnota je False.  | Ne |
| trustedCertPath | Úplná cesta k souboru PEM obsahujícího důvěryhodné certifikáty certifikační autority pro ověření serveru při připojování přes SSL. Tuto vlastnost lze nastavit pouze při použití ssl na samoobslužné infračervené ovládání. Výchozí hodnota je soubor cacerts.pem nainstalovaný s infračerveným přenosem.  | Ne |
| useSystemTrustStore | Určuje, zda se má použít certifikát certifikační autority z úložiště důvěryhodných certifikátů systému nebo ze zadaného souboru PEM. Výchozí hodnota je False.  | Ne |
| allowHostNameCNMismatch | Určuje, zda má být při připojování přes SSL vyžadován název certifikátu SSL vydaný certifikační autoritou. Výchozí hodnota je False.  | Ne |
| allowSelfSignedServerCert | Určuje, zda mají být ze serveru povoleny certifikáty podepsané svým držitelem. Výchozí hodnota je False.  | Ne |
| timeZoneID | Místní časové pásmo používané připojením. Platné hodnoty pro tuto možnost jsou určeny v databázi časových pásem IANA. Výchozí hodnota je systémové časové pásmo.  | Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Presto.

Chcete-li kopírovat data z aplikace Presto, nastavte vlastnost type datové sady na **PrestoObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **PrestoObject.** | Ano |
| Schématu | Název schématu. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Použití `schema` `table` a pro nové pracovní zatížení. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem Presto.

### <a name="presto-as-source"></a>Presto jako zdroj

Chcete-li kopírovat data z aplikace Presto, nastavte typ zdroje v aktivitě kopírování na **PrestoSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: PrestoSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

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

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
