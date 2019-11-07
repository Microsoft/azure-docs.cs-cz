---
title: Kopírování dat z Xero pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z Xero do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: d52e536170c649cbc84b6c6dce92afb76ffe3125
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680011"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Kopírování dat z Xero pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z Xero. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Xero je podporován pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data z Xero můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor Xero podporuje:

- Xero [privátní aplikace](https://developer.xero.com/documentation/getting-started/api-application-types) , ale ne veřejnou aplikaci.
- Všechny tabulky Xero (koncové body rozhraní API) s výjimkou "Reports". 

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, takže nemusíte ručně instalovat žádné ovladače pomocí tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor Xero.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Xero jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **Xero** . | Ano |
| Provoz | Koncový bod serveru Xero (`api.xero.com`).  | Ano |
| consumerKey | Klíč příjemce přidružený k aplikaci Xero Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| privateKey | Privátní klíč ze souboru. pem, který byl vygenerován pro vaši privátní aplikaci Xero, najdete v tématu [Vytvoření páru veřejného a privátního klíče](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Poznámka k **vygenerování PrivateKey. pem s numbits 512** pomocí `openssl genrsa -out privatekey.pem 512`; 1024 není podporován. Zahrňte veškerý text ze souboru. pem, včetně konců řádků systému UNIX (\n), viz ukázka níže.<br/><br/>Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| useEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovány pomocí protokolu HTTPS. Výchozí hodnota je true (pravda).  | Ne |
| useHostVerification | Určuje, jestli se v certifikátu serveru vyžaduje název hostitele, který se bude shodovat s názvem hostitele serveru při připojení přes protokol SSL. Výchozí hodnota je true (pravda).  | Ne |
| usePeerVerification | Určuje, jestli se má při připojování přes SSL ověřit identita serveru. Výchozí hodnota je true (pravda).  | Ne |

**Příklad:**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
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

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **XeroObject** . | Ano |
| tableName | Název tabulky | Ne (když je zadán zdroj aktivity "query") |

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

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **XeroSource** . | Ano |
| query | Pro čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM Contacts"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

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

- Tabulky se složitými položkami budou rozděleny na více tabulek. Například bankovní transakce má složitou datovou strukturu "položky řádku", takže data bankovních transakcí jsou namapována na tabulku `Bank_Transaction` a `Bank_Transaction_Line_Items`s `Bank_Transaction_ID` jako cizí klíč, aby je bylo možné propojit dohromady.

- Xero data jsou dostupná prostřednictvím dvou schémat: `Minimal` (výchozí) a `Complete`. Kompletní schéma obsahuje tabulky volání požadovaných součástí, které před provedením požadovaného dotazu vyžadují další data (například sloupec ID).

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
- Účtenk 
- Receipt_Validation_Errors 
- Tracking_Categories

Dotazování na následující tabulky se dá zadat jenom pomocí kompletního schématu:

- Dokončeno. Bank_Transaction_Line_Items 
- Dokončeno. Bank_Transaction_Line_Item_Tracking 
- Dokončeno. Contact_Group_Contacts 
- Dokončit. Contacts_Contact_ osoby 
- Dokončeno. Credit_Note_Line_Items 
- Dokončeno. Credit_Notes_Line_Items_Tracking 
- Dokončené platby. Expense_Claim_ 
- Dokončeno. Expense_Claim_Receipts 
- Dokončeno. Invoice_Line_Items 
- Dokončeno. Invoices_Line_Items_Tracking
- Dokončeno. Manual_Journal_Lines 
- Dokončeno. Manual_Journal_Line_Tracking 
- Dokončeno. Overpayment_Line_Items 
- Dokončeno. Overpayment_Line_Items_Tracking 
- Dokončeno. Prepayment_Line_Items 
- Dokončeno. Prepayment_Line_Item_Tracking 
- Dokončeno. Receipt_Line_Items 
- Dokončeno. Receipt_Line_Item_Tracking 
- Dokončeno. Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam podporovaných úložišť dat pomocí aktivity kopírování najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
