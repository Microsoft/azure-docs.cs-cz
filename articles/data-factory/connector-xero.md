---
title: Kopírování dat z platformy Xero pomocí Azure Data Factory (Preview) | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z platformy Xero úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: 6793fbcc50711e10231b87fa6e1f11f54f90d325
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54018428"
---
# <a name="copy-data-from-xero-using-azure-data-factory-preview"></a>Kopírování dat z platformy Xero pomocí Azure Data Factory (Preview)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z platformy Xero. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

> [!IMPORTANT]
> Tento konektor je aktuálně ve verzi preview. Můžete vyzkoušet a poskytnout zpětnou vazbu. Pokud do svého řešení chcete zavést závislost na konektorech ve verzi Preview, kontaktujte [podporu Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z platformy Xero do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Xero podporuje:

- Xero [privátní aplikace](https://developer.xero.com/documentation/getting-started/api-application-types) , ale aplikaci není veřejné.
- Všechny platformy Xero tabulky (koncových bodů rozhraní API) s výjimkou "Zprávy". 

Poskytuje integrované ovladače chcete umožnit připojení k Azure Data Factory, proto není nutné ručně nainstalovat všechny ovladače používání tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní platformy Xero konektoru.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Xero propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **Xero** | Ano |
| hostitel | Koncový bod serveru Xero (`api.xero.com`).  | Ano |
| consumerKey | Uživatelský klíč přidružený k aplikaci Xero. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| privateKey | Privátní klíč ze souboru .pem, který byl vygenerován pro vaši aplikaci privátní Xero, naleznete v tématu [vytvoření páru veřejného a privátního klíče](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Poznámka: **generovat privatekey.pem s numbits 512** pomocí `openssl genrsa -out privatekey.pem 512`; 1024 se nepodporuje. Zahrnout veškerý text z soubor .pem, včetně endings(\n) řádku Unix, najdete v ukázce níže.<br/><br/>Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| useEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovat pomocí protokolu HTTPS. Výchozí hodnota je true.  | Ne |
| useHostVerification | Určuje, zda je nutný název hostitele v certifikátu serveru tak, aby odpovídaly názvu hostitele serveru při připojení přes protokol SSL. Výchozí hodnota je true.  | Ne |
| usePeerVerification | Určuje, jestli se má ověřit identitu serveru při připojení přes protokol SSL. Výchozí hodnota je true.  | Ne |

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

Zahrnout veškerý text z soubor .pem, včetně endings(\n) řádku Unix.

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje datové sady Xero.

Ke zkopírování dat z platformy Xero, nastavte vlastnost typ datové sady na **XeroObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **XeroObject** | Ano |
| tableName | Název tabulky. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem Xero.

### <a name="xero-as-source"></a>Xero jako zdroj

Ke zkopírování dat z platformy Xero, nastavte typ zdroje v aktivitě kopírování do **XeroSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **XeroSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM Contacts"`. | Ne (když je "tableName" v datové sadě zadán) |

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

Při zadávání dotazu Xero, mějte na paměti následující:

- Tabulky s komplexnějších položek rozdělí k několika tabulkám. Bankovní transakce má například komplexní datová struktura položky "řádku", tak data bankovní transakce je namapována na tabulku `Bank_Transaction` a `Bank_Transaction_Line_Items`, s `Bank_Transaction_ID` jako cizí klíč je propojit dohromady.

- Data platformy Xero je k dispozici prostřednictvím dvou schémat: `Minimal` (výchozí) a `Complete`. Úplné schéma obsahuje požadované volání tabulek, které vyžadují další data (např. sloupec ID) před provedením požadované dotazu.

Následující tabulky obsahují stejné informace ve schématu minimální a kompletní. Pokud chcete snížit počet volání rozhraní API, použijte minimální schémat (výchozí).

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
- Přeplatků 
- Overpayments_Allocations 
- Zálohy 
- Prepayments_Allocations 
- Potvrzení 
- Receipt_Validation_Errors 
- Tracking_Categories

V následujících tabulkách se dá dotazovat jenom s kompletní schématu:

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

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných aktivitou kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
