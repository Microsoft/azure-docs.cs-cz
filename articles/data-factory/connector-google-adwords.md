---
title: Kopírování dat z Google AdWords pomocí Azure Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data z Google AdWords na podporovaná úložiště dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: 65bf1fa37ecb8d9e862b0e5042bed29470d750e1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935642"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Kopírování dat z Google AdWords pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z Google AdWords. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Google AdWords se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)


Data z Google AdWords můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, takže nemusíte ručně instalovat žádné ovladače pomocí tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor Google AdWords.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Google AdWords jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **GoogleAdWords** . | Ano |
| clientCustomerID | ID zákazníka klienta účtu AdWords, pro který chcete načíst data sestavy.  | Ano |
| developerToken | Token pro vývojáře přidružený k účtu manažera, který slouží k udělení přístupu k rozhraní AdWords API.  Toto pole můžete označit jako SecureString, abyste ho bezpečně ukládali do podavače ADF, nebo Uložit heslo v Azure Key Vault a nechat si z něj při kopírování dat získat z přihlašovacích údajů možnost z [přihlašovacích údajů v Key Vault Store](store-credentials-in-key-vault.md). | Ano |
| authenticationType | Ověřovací mechanismus OAuth 2,0, který se používá k ověřování. ServiceAuthentication se dá použít jenom pro prostředí IR v místním prostředí. <br/>Povolené hodnoty jsou: **ServiceAuthentication**, **UserAuthentication** | Ano |
| Refreshtoken kontextového tokenu | Obnovovací token získaný z Google pro autorizaci přístupu k AdWords pro UserAuthentication. Toto pole můžete označit jako SecureString, abyste ho bezpečně ukládali do podavače ADF, nebo Uložit heslo v Azure Key Vault a nechat si z něj při kopírování dat získat z přihlašovacích údajů možnost z [přihlašovacích údajů v Key Vault Store](store-credentials-in-key-vault.md). | Ne |
| clientId | ID klienta aplikace Google použité k získání obnovovacího tokenu. Toto pole můžete označit jako SecureString, abyste ho bezpečně ukládali do podavače ADF, nebo Uložit heslo v Azure Key Vault a nechat si z něj při kopírování dat získat z přihlašovacích údajů možnost z [přihlašovacích údajů v Key Vault Store](store-credentials-in-key-vault.md). | Ne |
| clientSecret | Tajný kód klienta aplikace Google, který se používá k získání obnovovacího tokenu. Toto pole můžete označit jako SecureString, abyste ho bezpečně ukládali do podavače ADF, nebo Uložit heslo v Azure Key Vault a nechat si z něj při kopírování dat získat z přihlašovacích údajů možnost z [přihlašovacích údajů v Key Vault Store](store-credentials-in-key-vault.md). | Ne |
| e-mail | ID e-mailu účtu služby, který se používá pro ServiceAuthentication a dá se použít jenom pro prostředí IR v místním prostředí.  | Ne |
| Cesta k souboru | Úplná cesta k souboru klíče. p12, který se používá k ověření e-mailové adresy účtu služby a dá se použít jenom na místním hostiteli IR.  | Ne |
| trustedCertPath | Úplná cesta k souboru. pem, který obsahuje certifikáty důvěryhodné certifikační autority pro ověření serveru při připojení přes protokol SSL. Tuto vlastnost lze nastavit pouze při použití protokolu SSL v místním prostředí IR. Výchozí hodnota je soubor cacerts. pem nainstalovaný s IR.  | Ne |
| useSystemTrustStore | Určuje, jestli se má použít certifikát certifikační autority z úložiště důvěryhodnosti systému nebo ze zadaného souboru PEM. Výchozí hodnota je false (NEPRAVDA).  | Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných sadou Google AdWords DataSet.

Pokud chcete kopírovat data z Google AdWords, nastavte vlastnost Type datové sady na **GoogleAdWordsObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **GoogleAdWordsObject** . | Ano |
| tableName | Název tabulky | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords as source

Pokud chcete kopírovat data z Google AdWords, nastavte typ zdroje v aktivitě kopírování na **GoogleAdWordsSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **GoogleAdWordsSource** . | Ano |
| query | Pro čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

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

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
