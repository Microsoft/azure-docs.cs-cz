---
title: Kopírování dat z Xero pomocí Azure Data Factory (Beta) | Microsoft Docs
description: Postup kopírování dat z Xero do úložiště dat podporovaných podřízený pomocí aktivity kopírování v kanál služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
ms.openlocfilehash: fbfad00956faad49980a431504d991cfde22cafb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619367"
---
# <a name="copy-data-from-xero-using-azure-data-factory-beta"></a>Kopírování dat z Xero pomocí Azure Data Factory (Beta)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z Xero. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [aktivitu kopírování v V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Tento konektor je aktuálně ve verzi Beta. Můžete si vyzkoušet a poskytnout zpětnou vazbu. Nepoužívejte ji v produkčním prostředí.

## <a name="supported-capabilities"></a>Podporované možnosti

Data můžete zkopírovat z Xero do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Xero podporuje:

- Xero [privátní aplikace](https://developer.xero.com/documentation/getting-started/api-application-types) , ale není veřejné aplikace.
- Všechny Xero tabulky (koncové body rozhraní API) s výjimkou "Sestavy". 

Azure Data Factory poskytuje integrované ovladače pro umožnění připojení, proto nemusíte ručně nainstalovat všechny ovladače, používání tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke Xero konektoru.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Xero propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **Xero** | Ano |
| hostitel | Koncový bod serveru Xero (`api.xero.com`).  | Ano |
| consumerKey | Uživatelský klíč přidružené k aplikaci Xero. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| privateKey | Najdete v části privátní klíč z soubor .pem, který byl vytvořen pro vaši aplikaci privátní Xero [vytvoření páru veřejného a privátního klíče RSA](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Poznámka: k **generovat privatekey.pem s numbits 512** pomocí `openssl genrsa -out privatekey.pem 512`; 1024 není podporován. Zahrnout všechny text z soubor .pem, včetně endings(\n) řádku systému Unix, viz následující ukázka.<br/><br/>Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| useEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat jsou šifrované pomocí protokolu HTTPS. Výchozí hodnota je true.  | Ne |
| useHostVerification | Určuje, zda název hostitele je požadováno v certifikátu serveru tak, aby odpovídaly název hostitele serveru při připojení přes protokol SSL. Výchozí hodnota je true.  | Ne |
| usePeerVerification | Určuje, jestli pro ověření totožnosti serveru při připojení přes protokol SSL. Výchozí hodnota je true.  | Ne |

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

**Privátní klíče hodnota vzorku:**

Zahrnout všechny text z soubor .pem, včetně endings(\n) řádku Unix.

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datové sady](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností nepodporuje Xero datovou sadu.

Ke zkopírování dat z Xero, nastavte vlastnost typu datové sady, která **XeroObject**. Není k dispozici žádné další vlastnosti specifické pro typ v tomto typu datové sady.

**Příklad**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje Xero zdroje.

### <a name="xero-as-source"></a>Xero jako zdroj

Ke zkopírování dat z Xero, nastavte typ zdroje v aktivitě kopírování do **XeroSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **XeroSource** | Ano |
| query | Čtení dat pomocí vlastního dotazu SQL. Například: `"SELECT * FROM Contacts"`. | Ano |

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

Při zadání dotazu Xero, pamatujte na tyhle:

- Tabulky s komplexní položky rozdělí k několika tabulkám. Například bankovní transakce obsahuje komplexní datová struktura "LineItems", takže data bankovní transakce je mapována do tabulky `Bank_Transaction` a `Bank_Transaction_Line_Items`, s `Bank_Transaction_ID` jako cizí klíč je propojit dohromady.

- Xero dat je k dispozici prostřednictvím dvou schémata: `Minimal` (výchozí) a `Complete`. Úplné schéma obsahuje tabulky požadovaných volání, které vyžadují další data (např. sloupec ID) před provedením požadovaný dotaz.

Následující tabulky obsahují stejné informace ve minimálního a dokončení schématu. Chcete-li snížit počet volání rozhraní API, použijte minimální schématu (výchozí).

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
- Invoices_ záloh 
- Invoices_Overpayments 
- Manual_Journals 
- Přeplatků 
- Overpayments_Allocations 
- Zálohy 
- Prepayments_Allocations 
- Potvrzení 
- Receipt_Validation_Errors 
- Tracking_Categories

Následující tabulky může být dotazován pouze s kompletní schématu:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ osob 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ plateb 
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

## <a name="next-steps"></a>Další postup
Seznam podporovaných datová úložiště pomocí aktivity kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
