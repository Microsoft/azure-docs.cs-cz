---
title: Kopírování dat z Xero pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z Xero do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: jingwang
ms.openlocfilehash: d795f8355943032751b911423b8aaa93b2df3206
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366904"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Kopírování dat z Xero pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z Xero. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Xero je podporován pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z Xero můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor Xero podporuje:

- Ověřování OAuth 2,0 a OAuth 1,0. Pro OAuth 1,0 konektor podporuje [soukromou aplikaci](https://developer.xero.com/documentation/getting-started/getting-started-guide) Xero, ale ne veřejnou aplikaci.
- Všechny tabulky Xero (koncové body rozhraní API) s výjimkou "Reports".

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor Xero.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Xero jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **Xero** . | Yes |
| connectionProperties | Skupina vlastností, která definuje, jak se připojit k Xero. | Yes |
| ***V části `connectionProperties` :*** | | |
| Hostitel | Koncový bod serveru Xero ( `api.xero.com` ).  | Yes |
| authenticationType | Povolené hodnoty jsou `OAuth_2.0` a `OAuth_1.0` . | Yes |
| consumerKey | Pro OAuth 2,0 zadejte **ID klienta** pro vaši aplikaci Xero.<br>V případě OAuth 1,0 zadejte uživatelský klíč přidružený k aplikaci Xero.<br>Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| privateKey | Pro OAuth 2,0 zadejte **tajný klíč klienta** pro vaši aplikaci Xero.<br>V případě OAuth 1,0 zadejte privátní klíč ze souboru. pem, který jste vygenerovali pro privátní aplikaci Xero, v tématu [Vytvoření páru veřejného a privátního klíče](https://developer.xero.com/documentation/auth-and-limits/create-publicprivate-key). Poznámka: pro **vygenerování PrivateKey. pem s numbits 1024 512** se nepodporuje `openssl genrsa -out privatekey.pem 512` . Zahrňte veškerý text ze souboru. pem, včetně konců řádků systému UNIX (\n), viz ukázka níže.<br/><br>Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenantId | ID tenanta přidružené k vaší aplikaci Xero Platí pro ověřování OAuth 2,0.<br>Zjistěte, jak získat ID tenanta z [tématu zjištění klientů, kterým máte oprávnění k přístupu](https://developer.xero.com/documentation/oauth2/auth-flow). | Ano pro ověřování OAuth 2,0 |
| Refreshtoken kontextového tokenu | Platí pro ověřování OAuth 2,0.<br/>Obnovovací token OAuth 2,0 je přidružen k aplikaci Xero a používá se k aktualizaci přístupového tokenu. platnost přístupového tokenu vyprší po 30 minutách. Přečtěte si, jak funguje autorizační tok Xero a jak získat obnovovací token z [tohoto článku](https://developer.xero.com/documentation/oauth2/auth-flow). Pokud chcete získat obnovovací token, musíte požádat o [obor offline_access](https://developer.xero.com/documentation/oauth2/scopes). <br/>Zjištění **omezení**: Poznámka Xero obnoví obnovovací token po jeho použití pro aktualizaci přístupového tokenu. Pro provozní úlohy před spuštěním každé aktivity kopírování musíte nastavit platný obnovovací token pro použití ADF.<br/>Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano pro ověřování OAuth 2,0 |
| useEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovány pomocí protokolu HTTPS. Výchozí hodnotou je hodnota true.  | No |
| useHostVerification | Určuje, jestli se v certifikátu serveru vyžaduje název hostitele, který se bude shodovat s názvem hostitele serveru při připojení přes protokol TLS. Výchozí hodnotou je hodnota true.  | No |
| usePeerVerification | Určuje, jestli se má při připojování přes protokol TLS ověřit identita serveru. Výchozí hodnotou je hodnota true.  | No |

**Příklad: ověřování OAuth 2,0**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": { 
                "host": "api.xero.com",
                "authenticationType":"OAuth_2.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<client ID>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "tenantId": "<tenant ID>", 
                "refreshToken": {
                    "type": "SecureString",
                    "value": "<refresh token>"
                }, 
                "useEncryptedEndpoints": true, 
                "useHostVerification": true, 
                "usePeerVerification": true
            }            
        }
    }
}
```

**Příklad: ověřování OAuth 1,0**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": {
                "host": "api.xero.com", 
                "authenticationType":"OAuth_1.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                }, 
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}
```

**Ukázka hodnoty privátního klíče:**

Zahrňte veškerý text ze souboru. pem, včetně konců řádků UNIX (\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných Xero DataSet.

Chcete-li kopírovat data z Xero, nastavte vlastnost Type datové sady na **XeroObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **XeroObject** . | Yes |
| tableName | Název tabulky | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných Xero zdrojem.

### <a name="xero-as-source"></a>Xero as source

Chcete-li kopírovat data z Xero, nastavte typ zdroje v aktivitě kopírování na **XeroSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **XeroSource** . | Yes |
| query | Pro čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM Contacts"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Při zadávání dotazu Xero Pamatujte na následující:

- Tabulky se složitými položkami budou rozděleny na více tabulek. Například bankovní transakce má komplexní strukturu dat "položky řádku", takže data bankovních transakcí jsou namapována na tabulku `Bank_Transaction` a s využitím `Bank_Transaction_Line_Items` `Bank_Transaction_ID` cizího klíče, aby je bylo možné propojit dohromady.

- Xero data jsou dostupná prostřednictvím dvou schémat: `Minimal` (výchozí) a `Complete` . Kompletní schéma obsahuje tabulky volání požadovaných součástí, které před provedením požadovaného dotazu vyžadují další data (například sloupec ID).

Následující tabulky obsahují stejné informace v rámci minimálního a úplného schématu. Chcete-li snížit počet volání rozhraní API, použijte minimální schéma (výchozí).

- Bank_Transactions
- Contact_Groups 
- Kontakty 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Faktury 
- Invoices_Credit_Notes
- Invoices_ platby 
- Invoices_Overpayments 
- Manual_Journals 
- Přeplatků 
- Overpayments_Allocations 
- Zálohy 
- Prepayments_Allocations 
- Účtenky 
- Receipt_Validation_Errors 
- Tracking_Categories

Dotazování na následující tabulky se dá zadat jenom pomocí kompletního schématu:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ osoby 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ platby 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam podporovaných úložišť dat pomocí aktivity kopírování najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
