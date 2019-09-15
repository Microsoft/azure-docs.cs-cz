---
title: Ukládat přihlašovací údaje v Azure Key Vault | Microsoft Docs
description: Naučte se ukládat přihlašovací údaje pro úložiště dat používaná v trezoru klíčů Azure, který Azure Data Factory může automaticky načíst za běhu.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: 3f46c54edff2bc765e75742848f83d30e7aa7c09
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003395"
---
# <a name="store-credential-in-azure-key-vault"></a>Ukládat přihlašovací údaje v Azure Key Vault

Přihlašovací údaje pro úložiště dat a výpočetní služby můžete ukládat [Azure Key Vault](../key-vault/key-vault-overview.md). Azure Data Factory načte pověření při provádění aktivity, která používá úložiště nebo výpočetní prostředky.

V současné době všechny typy aktivit kromě vlastní aktivity podporují tuto funkci. Pro konfiguraci konektoru se konkrétně podívejte na podrobnosti v části Vlastnosti propojené služby v [jednotlivých tématech](copy-activity-overview.md#supported-data-stores-and-formats) .

## <a name="prerequisites"></a>Požadavky

Tato funkce závisí na spravované identitě objektu pro vytváření dat. Naučte se, jak to funguje ze [spravované identity pro datovou továrnu](data-factory-service-identity.md) , a ujistěte se, že je k datové továrně přidružená jedna.

## <a name="steps"></a>Kroky

Pokud chcete odkazovat na přihlašovací údaje uložené v Azure Key Vault, musíte:

1. **Načtěte spravovanou identitu Data Factory** zkopírováním hodnoty "ID aplikace spravované identity", které se vygenerovalo společně s vaší továrnou. Pokud používáte uživatelské rozhraní pro vytváření ADF, zobrazí se ID aplikace spravované identity v okně pro vytvoření propojené služby Azure Key Vault. můžete ho také načíst z Azure Portal, přečtěte si téma [načtení spravované identity Data Factory](data-factory-service-identity.md#retrieve-managed-identity).
2. **Udělte spravované identitě přístup k vašemu Azure Key Vault.** V trezoru klíčů – zásady > přístupu – > Přidat nové > Vyhledat toto ID aplikace spravované identity, aby bylo uděleno oprávnění **Get** v rozevíracím seznamu oprávnění pro přístup v tajnosti. Umožňuje této určené továrně přístup k tajným klíčům v trezoru klíčů.
3. **Vytvořte propojenou službu ukazující na Azure Key Vault.** Přečtěte si téma [Azure Key Vault propojená služba](#azure-key-vault-linked-service).
4. **Vytvořte propojenou službu úložiště dat, ve které se odkazuje na odpovídající tajný kód uložený v trezoru klíčů.** Přečtěte si [odkaz na tajný kód uložený v trezoru klíčů](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Propojená služba Azure Key Vault

Pro Azure Key Vault propojenou službu jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **AzureKeyVault**. | Ano |
| baseUrl | Zadejte adresu URL Azure Key Vault. | Ano |

**Používání uživatelského rozhraní pro vytváření:**

Klikněte na **připojení** -> **propojené služby** ->  **+ New** -> vyhledejte "Azure Key Vault":

![Hledat integrace](media/store-credentials-in-key-vault/search-akv.png)

Vyberte zřízené Azure Key Vault, kde jsou uložené vaše přihlašovací údaje. Můžete provést **Test připojení** , abyste se ujistili, že je připojení integrace platné. 

![Konfigurace integrace](media/store-credentials-in-key-vault/configure-akv.png)

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

## <a name="reference-secret-stored-in-key-vault"></a>Odkaz na tajný kód uložený v trezoru klíčů

Následující vlastnosti jsou podporované při konfiguraci pole v propojené službě odkazujícího na tajný kód trezoru klíčů:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type pole musí být nastavena na: **AzureKeyVaultSecret**. | Ano |
| SecretName | Název tajného klíče v Azure Key Vault. | Ano |
| secretVersion | Verze tajného kódu v Azure Key Vault.<br/>Pokud tento parametr nezadáte, vždy používá nejnovější verzi tajného kódu.<br/>Je-li tento parametr zadán, bude se podávat do dané verze.| Ne |
| store | Odkazuje na Azure Key Vault propojená služba, kterou použijete k uložení přihlašovacích údajů. | Ano |

**Používání uživatelského rozhraní pro vytváření:**

Vyberte možnost **Azure Key Vault** pro tajná pole při vytváření připojení k úložišti dat nebo výpočetnímu prostředí. Vyberte zřízené Azure Key Vault propojenou službu a zadejte **název tajného**kódu. Volitelně můžete také zadat tajnou verzi. 

>[!TIP]
>Pro konektory, které používají připojovací řetězec v propojené službě, jako je SQL Server, úložiště objektů BLOB atd., můžete zvolit, aby se ukládaly jenom tajné pole, třeba heslo v integrace, nebo uložit celý připojovací řetězec do integrace. V uživatelském rozhraní můžete najít obě možnosti.

![Konfigurace tajného klíče integrace](media/store-credentials-in-key-vault/configure-akv-secret.png)

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

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
