---
title: Kopírování dat z Xero pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data z Xero do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: 1f6404da163e075b63a99a1d8474cdba4e064b06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930883"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Kopírování dat z Xero pomocí Azure Data Factory

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z Xero. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Xero je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data z Xero do libovolného úložiště dat podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor Xero podporuje:

- [Soukromá aplikace](https://developer.xero.com/documentation/getting-started/api-application-types) Xero, ale ne veřejná aplikace.
- Všechny xero tabulky (koncové body rozhraní API) s výjimkou "Sestavy". 

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, proto není nutné ručně instalovat žádný ovladač pomocí tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor Xero.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Xero jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **Xero** | Ano |
| host | Koncový bod serveru Xero`api.xero.com`( ).  | Ano |
| consumerKey | Klíč příjemce přidružený k aplikaci Xero. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| privátní klíč | Soukromý klíč ze souboru PEM, který byl vygenerován pro vaši soukromou aplikaci Xero, najdete [v tématu Vytvoření páru veřejného a soukromého klíče](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Poznámka: **chcete-li generovat privatekey.pem s numbits 512** pomocí `openssl genrsa -out privatekey.pem 512`; 1024 není podporován. Zahrňte veškerý text ze souboru .pem včetně zakončení unixového řádku(\n), viz ukázka níže.<br/><br/>Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| použitíEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovány pomocí protokolu HTTPS. Výchozí hodnotou je hodnota true.  | Ne |
| useHostVerification | Určuje, zda je v certifikátu serveru vyžadován název hostitele, aby odpovídal názvu hostitele serveru při připojování přes SSL. Výchozí hodnotou je hodnota true.  | Ne |
| usePeerVerification | Určuje, zda se má ověřit identita serveru při připojování přes SSL. Výchozí hodnotou je hodnota true.  | Ne |

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

**Ukázková hodnota soukromého klíče:**

Zahrňte veškerý text ze souboru .pem včetně zakončení unixového řádku(\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Xero.

Chcete-li kopírovat data z Xero, nastavte vlastnost type datové sady na **XeroObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **XeroObject.** | Ano |
| tableName | Název tabulky. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem Xero.

### <a name="xero-as-source"></a>Xero jako zdroj

Chcete-li kopírovat data z Xero, nastavte typ zdroje v aktivitě kopírování na **XeroSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na: **XeroSource.** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM Contacts"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

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

Při zadávání dotazu Xero si všimněte následujícího:

- Tabulky se složitými položkami budou rozděleny do více tabulek. Například bankovní transakce mají složitou datovou strukturu "LineItems", takže data `Bank_Transaction` `Bank_Transaction_Line_Items`bankovní `Bank_Transaction_ID` transakce jsou mapována na tabulka a jako cizí klíč, aby je spojili.

- Data Xero jsou k dispozici prostřednictvím dvou `Minimal` `Complete`schémat: (výchozí) a . Úplné schéma obsahuje požadované tabulky volání, které vyžadují další data (např. sloupec ID) před provedením požadovaného dotazu.

Následující tabulky mají stejné informace ve schématu Minimální a Dokončeno. Chcete-li snížit počet volání rozhraní API, použijte minimální schéma (výchozí).

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
- Invoices_ zálohy 
- Invoices_Overpayments 
- Manual_Journals 
- Přeplatky 
- Overpayments_Allocations 
- Zálohy 
- Prepayments_Allocations 
- Příjmy 
- Receipt_Validation_Errors 
- Tracking_Categories

Následující tabulky mohou být dotazovány pouze s úplným schématem:

- Kompletní.Bank_Transaction_Line_Items 
- Kompletní.Bank_Transaction_Line_Item_Tracking 
- Kompletní.Contact_Group_Contacts 
- Complete.Contacts_Contact_ osoby 
- Kompletní.Credit_Note_Line_Items 
- Kompletní.Credit_Notes_Line_Items_Tracking 
- Platby complete.Expense_Claim_ 
- Dokončit.Expense_Claim_Receipts 
- Kompletní.Invoice_Line_Items 
- Kompletní.Invoices_Line_Items_Tracking
- Kompletní.Manual_Journal_Lines 
- Kompletní.Manual_Journal_Line_Tracking 
- Kompletní.Overpayment_Line_Items 
- Kompletní.Overpayment_Line_Items_Tracking 
- Kompletní.Prepayment_Line_Items 
- Kompletní.Prepayment_Line_Item_Tracking 
- Kompletní.Receipt_Line_Items 
- Kompletní.Receipt_Line_Item_Tracking 
- Kompletní.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam podporovaných úložišť dat podle aktivity kopírování naleznete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
