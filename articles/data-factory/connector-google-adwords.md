---
title: Kopírování dat z Google AdWords pomocí Azure Data Factory (Preview) | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z Google AdWords úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: b712b576e1dd47698de66889d4edf9dda026a16c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017986"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory-preview"></a>Kopírování dat z Google AdWords pomocí Azure Data Factory (Preview)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z Google AdWords. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

> [!IMPORTANT]
> Tento konektor je aktuálně ve verzi preview. Můžete vyzkoušet a poskytnout zpětnou vazbu. Pokud do svého řešení chcete zavést závislost na konektorech ve verzi Preview, kontaktujte [podporu Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z Google AdWords do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Poskytuje integrované ovladače chcete umožnit připojení k Azure Data Factory, proto není nutné ručně nainstalovat všechny ovladače používání tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Google AdWords.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro Google AdWords propojené služby:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **GoogleAdWords** | Ano |
| clientCustomerID | ID zákazníka klienta AdWords účtu, který chcete načíst data sestavy pro.  | Ano |
| developerToken | Token pro vývojáře spojené s účtem správce, který použijete k udělení přístupu k rozhraní API AdWords.  Můžete označit pole jako SecureString bezpečně uložit ve službě ADF nebo ukládání hesel ve službě Azure Key Vault a umožnit ADF kopírování acitivty o přijetí změn z něj při kopírování dat – Další informace z [Store přihlašovacích údajů ve službě Key Vault](store-credentials-in-key-vault.md). | Ano |
| authenticationType. | Mechanismus ověřování OAuth 2.0 pro ověřování. ServiceAuthentication jde použít jenom v místním prostředí IR. <br/>Povolené hodnoty jsou: **ServiceAuthentication**, **UserAuthentication** | Ano |
| refreshToken | Obnovovací token získaný z Googlu pro autorizaci přístupu ke AdWords pro UserAuthentication. Můžete označit pole jako SecureString bezpečně uložit ve službě ADF nebo ukládání hesel ve službě Azure Key Vault a umožnit ADF kopírování acitivty o přijetí změn z něj při kopírování dat – Další informace z [Store přihlašovacích údajů ve službě Key Vault](store-credentials-in-key-vault.md). | Ne |
| ID klienta | Id klienta aplikace google, slouží k získání tokenu obnovení. Můžete označit pole jako SecureString bezpečně uložit ve službě ADF nebo ukládání hesel ve službě Azure Key Vault a umožnit ADF kopírování acitivty o přijetí změn z něj při kopírování dat – Další informace z [Store přihlašovacích údajů ve službě Key Vault](store-credentials-in-key-vault.md). | Ne |
| ClientSecret | Tajný kód klienta aplikace služby google slouží k získání tokenu obnovení. Můžete označit pole jako SecureString bezpečně uložit ve službě ADF nebo ukládání hesel ve službě Azure Key Vault a umožnit ADF kopírování acitivty o přijetí změn z něj při kopírování dat – Další informace z [Store přihlašovacích údajů ve službě Key Vault](store-credentials-in-key-vault.md). | Ne |
| e-mail | ID e-mailu účtu služby, který se používá pro ServiceAuthentication a jde použít jenom v místním prostředí IR.  | Ne |
| keyFilePath | Úplná cesta k souboru klíče .p12, který se používá k ověření e-mailovou adresu účtu služby a jde použít jenom v místním prostředí IR.  | Ne |
| trustedCertPath | Úplná cesta soubor .pem obsahující certifikáty důvěryhodné CA pro ověření serveru, při připojení přes protokol SSL. Tuto vlastnost lze nastavit pouze při použití protokolu SSL v místním prostředí IR. Výchozí hodnota je soubor cacerts.pem součástí IR.  | Ne |
| useSystemTrustStore | Určuje, jestli se má použít certifikát certifikační Autority ze systémového úložiště důvěryhodnosti nebo ze zadaného souboru PEM. Výchozí hodnota je false.  | Ne |

**Příklad:**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                 "type": "SecureString",
                 "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refreshToken>"
            },
            "clientId": {
                 "type": "SecureString",
                 "value": "<clientId>"
            },
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje Google AdWords datové sady.

Ke zkopírování dat z Google AdWords, nastavte vlastnost typ datové sady na **GoogleAdWordsObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **GoogleAdWordsObject** | Ano |
| tableName | Název tabulky. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}

```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords jako zdroj

Ke zkopírování dat z Google AdWords, nastavte typ zdroje v aktivitě kopírování do **GoogleAdWordsSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **GoogleAdWordsSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM MyTable"`. | Ne (když je "tableName" v datové sadě zadán) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
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
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
