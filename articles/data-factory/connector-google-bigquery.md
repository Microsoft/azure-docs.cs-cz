---
title: Kopírování dat z Google BigQuery pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data z Google BigQuery do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu datové továrny.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: c0eb043ce040f154050ef4c3675f165dad326e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929431"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Kopírování dat z Google BigQuery pomocí Azure Data Factory

Tento článek popisuje, jak pomocí kopírovat aktivitu v Azure Data Factory ke kopírování dat z Google BigQuery. Vychází z článku [přehled aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Google BigQuery je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z Google BigQuery můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivity kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Data Factory poskytuje integrovaný ovladač pro povolení připojení. Proto není nutné ručně nainstalovat ovladač používat tento konektor.

>[!NOTE]
>Tento konektor Google BigQuery je postaven na rozhraní chaue BigQuery. Uvědomte si, že BigQuery omezuje maximální rychlost příchozích požadavků a vynucuje příslušné kvóty na základě projektu, naleznete [kvóty & limity - požadavky rozhraní API](https://cloud.google.com/bigquery/quotas#api_requests). Ujistěte se, že neaktivujete příliš mnoho souběžných požadavků na účet.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Datové továrny specifických pro konektor Google BigQuery.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Google BigQuery jsou podporovány následující služby.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na **GoogleBigQuery**. | Ano |
| projekt | ID projektu výchozího projektu BigQuery, proti které se má dotazovat.  | Ano |
| další projekty | Seznam ID projektu veřejných projektů BigQuery oddělených čárkami pro přístup.  | Ne |
| requestGoogleDriveScope | Zda požádat o přístup k Disku Google. Povolení přístupu na Disk Google umožňuje podporu federovaných tabulek, které kombinují data BigQuery s daty z Disku Google. Výchozí hodnota je **false** (nepravda).  | Ne |
| authenticationType | Mechanismus ověřování OAuth 2.0 používaný pro ověřování. ServiceAuthentication lze použít pouze na prostředí Runtime integrace s vlastním hostitelem. <br/>Povolené hodnoty jsou **Ověřování uživatele** a **Ověřování služeb**. Naleznete v částech pod touto tabulkou na více vlastností a JSON ukázky pro tyto typy ověřování v uvedeném pořadí. | Ano |

### <a name="using-user-authentication"></a>Použití ověřování uživatele

Nastavte vlastnost AuthenticationType na **UserAuthentication**a zadejte následující vlastnosti spolu s obecnými vlastnostmi popsanými v předchozí části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| clientId | ID aplikace použité ke generování obnovovacího tokenu. | Ne |
| clientSecret | Tajný klíč aplikace, která slouží ke generování obnovovacího tokenu. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |
| refreshToken | Obnovovací token získaný od Googlu slouží k autorizaci přístupu k BigQuery. Naučte se, jak získat jeden z [Získání OAuth 2.0 přístup tokeny](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) a [tento komunitní blog](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |

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

### <a name="using-service-authentication"></a>Použití ověřování služby

Nastavte vlastnost authenticationType na **ServiceAuthentication**a zadejte následující vlastnosti spolu s obecnými vlastnostmi popsanými v předchozí části. Tento typ ověřování lze použít pouze na prostředí Runtime integrace s vlastním hostitelem.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| e-mail | ID e-mailu účtu služby, který se používá pro ServiceAuthentication. Lze jej použít pouze v prostředí Runtime integrace s vlastním hostitelem.  | Ne |
| keyFilePath | Úplná cesta k souboru klíče .p12, který se používá k ověření e-mailové adresy účtu služby. | Ne |
| trustedCertPath | Úplná cesta k souboru PEM, který obsahuje důvěryhodné certifikáty certifikační autority používané k ověření serveru při připojení přes ssl. Tuto vlastnost lze nastavit pouze v případě, že používáte SSL na vlastní hostované integrační runtime. Výchozí hodnota je soubor cacerts.pem nainstalovaný s integračním runtime.  | Ne |
| useSystemTrustStore | Určuje, zda se má použít certifikát certifikační autority z úložiště důvěryhodných certifikátů systému nebo ze zadaného souboru PEM. Výchozí hodnota je **false** (nepravda).  | Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Google BigQuery.

Chcete-li kopírovat data z Google BigQuery, nastavte vlastnost typu datové sady na **GoogleBigQueryObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **GoogleBigQueryObject.** | Ano |
| Dataset | Název datové sady Google BigQuery. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky. Tato vlastnost je podporována pro zpětnou kompatibilitu. Pro nové pracovní `dataset` `table`vytížení, použití a . | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných typem zdroje Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource jako zdrojový typ

Chcete-li kopírovat data z Google BigQuery, nastavte typ zdroje v aktivitě kopírování na **GoogleBigQuerySource**. Následující vlastnosti jsou podporovány v části **zdroje aktivity** kopírování.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu zdroje aktivity kopírování musí být nastavena na **GoogleBigQuerySource**. | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

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

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v datové továrně naleznete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
