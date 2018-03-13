---
title: "Kopírování dat z Google BigQuery pomocí Azure Data Factory | Microsoft Docs"
description: "Postup kopírování dat z Google BigQuery do úložiště dat podporovaných podřízený pomocí aktivity kopírování v kanálu data factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: jingwang
ms.openlocfilehash: 955de6cf4b17f1df1e545ccc196856c7c898edfe
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Kopírování dat z Google BigQuery pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z Google BigQuery. Vychází [aktivity kopírování přehled](copy-activity-overview.md) článek, který představuje obecný přehled o aktivitě kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, která je obecně k dispozici, najdete v části [aktivity kopírování v verze 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Data můžete zkopírovat z Google BigQuery do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

 Data Factory poskytuje integrované ovladače pro umožnění připojení. Proto nemusíte ručně nainstalovat ovladač, který chcete použít tento konektor.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru Google BigQuery.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro Google BigQuery propojené služby.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavená na **GoogleBigQuery**. | Ano |
| Projekt | ID projektu výchozí projekt BigQuery k dotazu vůči.  | Ano |
| additionalProjects | Čárkami oddělený seznam ID projektů z veřejné BigQuery projektů přístup.  | Ne |
| requestGoogleDriveScope | Určuje, zda chcete požadovat přístup k Google Drive. Povolení přístupu Google Drive umožňuje podporu pro federované tabulky, které spojují BigQuery dat s daty z Google Drive. Výchozí hodnota je **false**.  | Ne |
| authenticationType. | Metoda ověřování OAuth 2.0, používá k ověřování. ServiceAuthentication lze použít pouze v Self-hosted integrace Runtime. <br/>Povolené hodnoty jsou **UserAuthentication** a **ServiceAuthentication**. Naleznete v části dál v této tabulce na další vlastnosti a ukázky JSON pro tyto typy ověřování v uvedeném pořadí. | Ano |

### <a name="using-user-authentication"></a>Pomocí ověřování uživatelů

Nastavte vlastnost "authenticationType" na **UserAuthentication**a zadejte následující vlastnosti společně s obecné vlastnosti, které jsou popsané v předchozí části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| clientId | ID aplikace sloužící k vygenerování tokenu obnovení. | Ne |
| clientSecret | Tajný klíč aplikace použít k vygenerování tokenu obnovení. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| refreshToken | Aktualizace tokenu získaného z Google použitý k autorizaci přístupu k BigQuery. Další informace o získání jednoho z [přístupových tokenů OAuth 2.0 získání](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens). Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ne |

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

### <a name="using-service-authentication"></a>Pomocí ověřování služby

Nastavte vlastnost "authenticationType" na **ServiceAuthentication**a zadejte následující vlastnosti společně s obecné vlastnosti, které jsou popsané v předchozí části. Tento typ ověřování lze použít pouze v Self-hosted integrace Runtime.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| e-mail | ID s e-mailu účtu služby, který se používá pro ServiceAuthentication. Je možné použít pouze s Self-hosted integrace Runtime.  | Ne |
| keyFilePath | Úplná cesta k souboru klíče .p12, který se používá k ověření e-mailovou adresu účtu služby. | Ne |
| trustedCertPath | Úplná cesta soubor .pem, který obsahuje certifikáty důvěryhodné certifikační Autority používají k ověření serveru, jakmile se připojíte přes protokol SSL. Tuto vlastnost lze nastavit pouze při použití protokolu SSL na Self-hosted integrace Runtime. Výchozí hodnota je soubor cacerts.pem nainstalovaný s modulem runtime integrace.  | Ne |
| useSystemTrustStore | Určuje, jestli chcete použít certifikát Certifikační autority z obchodu důvěryhodnosti systému nebo ze souboru zadané .pem. Výchozí hodnota je **false**.  | Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datové sady](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností nepodporuje datovou sadu Google BigQuery.

Ke zkopírování dat z Google BigQuery, nastavte vlastnost typu datové sady, která **GoogleBigQueryObject**. Není k dispozici žádné další vlastnosti specifické pro typ v tomto typu datové sady.

**Příklad**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje typ zdroje Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource jako typ zdroje

Ke zkopírování dat z Google BigQuery, nastavte typ zdroje v aktivitě kopírování do **GoogleBigQuerySource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na **GoogleBigQuerySource**. | Ano |
| query | Čtení dat pomocí vlastního dotazu SQL. Příklad: `"SELECT * FROM MyTable"`. | Ano |

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

## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v objektu pro vytváření dat najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
