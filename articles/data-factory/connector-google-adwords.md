---
title: Kopírování dat ze služby Google AdWords
description: Přečtěte si, jak kopírovat data z Google AdWords do podporovaných úložišť dat hloubek pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: b01dcad71747da6b7aa770e3993cb82892ae55fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929438"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Kopírování dat ze služby Google AdWords pomocí Azure Data Factory

Tento článek popisuje, jak pomocí služby Kopírovat aktivitu v Azure Data Factory kopírovat data ze služby Google AdWords. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Google AdWords je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)


Data ze služby Google AdWords můžete kopírovat do libovolného podporovaného úložiště dat pro jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, proto není nutné ručně instalovat žádný ovladač pomocí tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit datové továrny specifických pro konektor Google AdWords.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Google AdWords jsou podporovány následující služby:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **GoogleAdWords** | Ano |
| klientCustomerID | ID zákazníka klienta účtu AdWords, pro který chcete načíst data přehledu.  | Ano |
| developerToken | Vývojářský token přidružený k účtu správce, který používáte k udělení přístupu k rozhraní AdWords API.  Toto pole můžete označit jako securestring pro bezpečné uložení v adf nebo ukládat heslo v trezoru klíčů Azure a nechat adf kopírovat aktivitu vytáhnout odtud při provádění kopírování dat - další informace z [přihlašovacích údajů úložiště v trezoru klíčů](store-credentials-in-key-vault.md). | Ano |
| authenticationType | Mechanismus ověřování OAuth 2.0 používaný pro ověřování. ServiceAuthentication lze použít pouze na samoobslužné infračervené ovládání. <br/>Povolené hodnoty jsou: **ServiceAuthentication**, **UserAuthentication** | Ano |
| refreshToken | Obnovovací token získaný od společnosti Google za autorizaci přístupu k adWords pro ověřování uživatelů. Toto pole můžete označit jako securestring pro bezpečné uložení v adf nebo ukládat heslo v trezoru klíčů Azure a nechat adf kopírovat aktivitu vytáhnout odtud při provádění kopírování dat - další informace z [přihlašovacích údajů úložiště v trezoru klíčů](store-credentials-in-key-vault.md). | Ne |
| clientId | ID klienta aplikace Google použité k získání obnovovacího tokenu. Toto pole můžete označit jako securestring pro bezpečné uložení v adf nebo ukládat heslo v trezoru klíčů Azure a nechat adf kopírovat aktivitu vytáhnout odtud při provádění kopírování dat - další informace z [přihlašovacích údajů úložiště v trezoru klíčů](store-credentials-in-key-vault.md). | Ne |
| clientSecret | Tajný klíč klienta aplikace Google použitý k získání obnovovacího tokenu. Toto pole můžete označit jako securestring pro bezpečné uložení v adf nebo ukládat heslo v trezoru klíčů Azure a nechat adf kopírovat aktivitu vytáhnout odtud při provádění kopírování dat - další informace z [přihlašovacích údajů úložiště v trezoru klíčů](store-credentials-in-key-vault.md). | Ne |
| e-mail | ID e-mailu účtu služby, který se používá pro ServiceAuthentication a lze použít pouze na samostatně hostované infračervené ovládání.  | Ne |
| keyFilePath | Úplnou cestu k souboru klíče .p12, který se používá k ověření e-mailové adresy účtu služby a lze jej použít pouze na samoobslužné infračervené ovládání.  | Ne |
| trustedCertPath | Úplná cesta k souboru PEM obsahujícího důvěryhodné certifikáty certifikační autority pro ověření serveru při připojování přes SSL. Tuto vlastnost lze nastavit pouze při použití ssl na samoobslužné infračervené ovládání. Výchozí hodnota je soubor cacerts.pem nainstalovaný s infračerveným přenosem.  | Ne |
| useSystemTrustStore | Určuje, zda se má použít certifikát certifikační autority z úložiště důvěryhodných certifikátů systému nebo ze zadaného souboru PEM. Výchozí hodnota je False.  | Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam služeb podporovaných datovou sadou Google AdWords.

Chcete-li kopírovat data ze služby Google AdWords, nastavte vlastnost typu datové sady na **googleadWordsobject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **GoogleAdWordsObject.** | Ano |
| tableName | Název tabulky. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam služeb podporovaných zdrojem Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords jako zdroj

Chcete-li kopírovat data ze služby Google AdWords, nastavte typ zdroje v aktivitě kopírování na **GoogleAdWordsSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu zdroje aktivit y kopírování musí být nastavena na: **GoogleAdWordsSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

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

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
