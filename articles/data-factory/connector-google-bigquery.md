---
title: Kopírování dat z Google BigQuery pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z Google BigQuery do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu služby Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: e3fcaa6c1542578d983461623da743724a3114d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389684"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Kopírování dat z Google BigQuery pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z Google BigQuery. Vytvoří se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Google BigQuery se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z Google BigQuery můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje nebo jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Data Factory poskytuje integrovaný ovladač, který umožňuje připojení. Proto nemusíte ručně instalovat ovladač pro použití tohoto konektoru.

>[!NOTE]
>Tento konektor Google BigQuery je postaven nad rozhraními API BigQuery. Počítejte s tím, že BigQuery omezuje maximální rychlost příchozích požadavků a vynutila příslušné kvóty pro jednotlivé projekty. Přečtěte si o [kvótách & limity – požadavky rozhraní API](https://cloud.google.com/bigquery/quotas#api_requests). Ujistěte se, že se k účtu neaktivuje příliš mnoho souběžných žádostí.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor Google BigQuery.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Google BigQuery jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na **GoogleBigQuery**. | Yes |
| projekt | ID projektu výchozího projektu BigQuery, proti kterému se má dotazovat  | Yes |
| additionalProjects | Čárkami oddělený seznam ID projektů veřejných projektů BigQuery pro přístup.  | No |
| requestGoogleDriveScope | Bez ohledu na to, jestli se má vyžádat přístup k disku Google Povolení přístupu k disku Google umožňuje podporu pro federované tabulky, které kombinují BigQuery data s daty z disku Google. Výchozí hodnota je **false** (nepravda).  | No |
| authenticationType | Ověřovací mechanismus OAuth 2,0, který se používá k ověřování. ServiceAuthentication se dá použít jenom pro Integration Runtime v místním prostředí. <br/>Povolené hodnoty jsou **UserAuthentication** a **ServiceAuthentication**. Další vlastnosti a ukázky JSON pro tyto typy ověřování najdete v částech níže v této tabulce. | Yes |

### <a name="using-user-authentication"></a>Použití ověřování uživatelů

Nastavte vlastnost "authenticationType" na **UserAuthentication** a zadejte následující vlastnosti spolu s obecnými vlastnostmi popsanými v předchozí části:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| clientId | ID aplikace použité k vygenerování obnovovacího tokenu | No |
| clientSecret | Tajný kód aplikace použitý k vygenerování obnovovacího tokenu Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | No |
| Refreshtoken kontextového tokenu | Obnovovací token získaný z Google, který slouží k autorizaci přístupu k BigQuery. Přečtěte si, jak získat jednu z informací o [získání přístupových tokenů OAuth 2,0](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) a [tohoto blogu komunity](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | No |

**Příklad:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Ověřování pomocí služby

Nastavte vlastnost "authenticationType" na **ServiceAuthentication** a zadejte následující vlastnosti spolu s obecnými vlastnostmi popsanými v předchozí části. Tento typ ověřování se dá použít jenom pro Integration Runtime v místním prostředí.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| e-mail | ID e-mailu účtu služby, který se používá pro ServiceAuthentication. Dá se použít jenom pro Integration Runtime v místním prostředí.  | No |
| Cesta k souboru | Úplná cesta k souboru klíče. p12, který se používá k ověření e-mailové adresy účtu služby. | No |
| trustedCertPath | Úplná cesta k souboru. pem, který obsahuje certifikáty důvěryhodné certifikační autority, které se používají k ověření serveru při připojení přes protokol TLS. Tuto vlastnost lze nastavit pouze v případě, že používáte protokol TLS při Integration Runtime v místním prostředí. Výchozí hodnota je soubor cacerts. pem nainstalovaný v prostředí Integration runtime.  | No |
| useSystemTrustStore | Určuje, jestli se má použít certifikát certifikační autority z úložiště důvěryhodnosti systému nebo ze zadaného souboru. pem. Výchozí hodnota je **false** (nepravda).  | No |

**Příklad:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou Google BigQuery.

Pokud chcete kopírovat data z Google BigQuery, nastavte vlastnost Type datové sady na **GoogleBigQueryObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **GoogleBigQueryObject** . | Yes |
| integrován | Název datové sady Google BigQuery |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `dataset` a `table` . | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností, které podporuje typ zdroje Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource jako typ zdroje

Pokud chcete kopírovat data z Google BigQuery, nastavte typ zdroje v aktivitě kopírování na **GoogleBigQuerySource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **GoogleBigQuerySource**. | Yes |
| query | Pro čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
