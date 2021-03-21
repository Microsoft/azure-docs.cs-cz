---
title: Uložení přihlašovacích údajů v Azure Key Vault
description: Naučte se ukládat přihlašovací údaje pro úložiště dat používaná v trezoru klíčů Azure, který Azure Data Factory může automaticky načíst za běhu.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 00c28a9ede01a74f4ae64109d277276050047461
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653760"
---
# <a name="store-credential-in-azure-key-vault"></a>Ukládat přihlašovací údaje v Azure Key Vault

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Přihlašovací údaje pro úložiště dat a výpočetní služby můžete ukládat [Azure Key Vault](../key-vault/general/overview.md). Azure Data Factory načte pověření při provádění aktivity, která používá úložiště nebo výpočetní prostředky.

V současné době všechny typy aktivit kromě vlastní aktivity podporují tuto funkci. Pro konfiguraci konektoru se konkrétně podívejte na podrobnosti v části Vlastnosti propojené služby v [jednotlivých tématech](copy-activity-overview.md#supported-data-stores-and-formats) .

## <a name="prerequisites"></a>Předpoklady

Tato funkce závisí na spravované identitě objektu pro vytváření dat. Naučte se, jak to funguje ze [spravované identity pro datovou továrnu](data-factory-service-identity.md) , a ujistěte se, že je k datové továrně přidružená jedna.

## <a name="steps"></a>Postup

Pokud chcete odkazovat na přihlašovací údaje uložené v Azure Key Vault, musíte:

1. **Načtěte spravovanou identitu Data Factory** zkopírováním hodnoty "ID objektu spravované identity" generovaného společně s vaší továrnou. Pokud použijete uživatelské rozhraní pro vytváření ADF, zobrazí se ID objektu spravované identity v okně pro vytvoření propojené služby Azure Key Vault. můžete ho také načíst z Azure Portal, přečtěte si téma [načtení spravované identity Data Factory](data-factory-service-identity.md#retrieve-managed-identity).
2. **Udělte spravované identitě přístup k vašemu Azure Key Vault.** V trezoru klíčů – > zásady přístupu – > přidat zásady přístupu, vyhledejte v této spravované identitě oprávnění **Get** v rozevíracím seznamu oprávnění pro přístup do tajných kódů. Umožňuje této určené továrně přístup k tajným klíčům v trezoru klíčů.
3. **Vytvořte propojenou službu ukazující na Azure Key Vault.** Přečtěte si téma [Azure Key Vault propojená služba](#azure-key-vault-linked-service).
4. **Vytvořte propojenou službu úložiště dat, ve které se odkazuje na odpovídající tajný kód uložený v trezoru klíčů.** Přečtěte si [odkaz na tajný kód uložený v trezoru klíčů](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Propojená služba Azure Key Vault

Pro Azure Key Vault propojenou službu jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **AzureKeyVault**. | Yes |
| baseUrl | Zadejte adresu URL Azure Key Vault. | Yes |

**Používání uživatelského rozhraní pro vytváření:**

Vyberte **připojení**  ->  **propojené služby**  ->  **nové**. V části Nová propojená služba vyhledejte a vyberte "Azure Key Vault":

![Hledat Azure Key Vault](media/store-credentials-in-key-vault/search-akv.png)

Vyberte zřízené Azure Key Vault, kde jsou uložené vaše přihlašovací údaje. Můžete provést **Test připojení** , abyste se ujistili, že je připojení integrace platné. 

![Konfigurace Azure Key Vaultu](media/store-credentials-in-key-vault/configure-akv.png)

**Příklad JSON:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Odkazování na tajný klíč uložený v trezoru klíčů

Následující vlastnosti jsou podporované při konfiguraci pole v propojené službě odkazujícího na tajný kód trezoru klíčů:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type pole musí být nastavená na: **AzureKeyVaultSecret**. | Yes |
| secretName | Název tajného klíče v Azure Key Vault. | Yes |
| Verzetajnéhoklíče | Verze tajného kódu v Azure Key Vault.<br/>Pokud tento parametr nezadáte, vždy používá nejnovější verzi tajného kódu.<br/>Je-li tento parametr zadán, bude se podávat do dané verze.| No |
| store | Odkazuje na Azure Key Vault propojená služba, kterou použijete k uložení přihlašovacích údajů. | Yes |

**Používání uživatelského rozhraní pro vytváření:**

Vyberte možnost **Azure Key Vault** pro tajná pole při vytváření připojení k úložišti dat nebo výpočetnímu prostředí. Vyberte zřízené Azure Key Vault propojenou službu a zadejte **název tajného** kódu. Volitelně můžete také zadat tajnou verzi. 

>[!TIP]
>Pro konektory, které používají připojovací řetězec v propojené službě, jako je SQL Server, úložiště objektů BLOB atd., můžete zvolit, aby se ukládaly jenom tajné pole, třeba heslo v integrace, nebo uložit celý připojovací řetězec do integrace. V uživatelském rozhraní můžete najít obě možnosti.

![Konfigurace Azure Key Vault tajného klíče](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Příklad JSON: (viz oddíl heslo)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
