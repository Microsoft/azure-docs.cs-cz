---
title: Uložení přihlašovacích údajů v Azure Key Vault
description: Zjistěte, jak ukládat přihlašovací údaje pro úložiště dat používaná v trezoru klíčů Azure, který může Azure Data Factory automaticky načítat za běhu.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 6720a018cdc3fff95192b0956b3d1040be263ab2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261846"
---
# <a name="store-credential-in-azure-key-vault"></a>Přihlašovací údaje pro ukládání v trezoru klíčů Azure

Můžete uložit přihlašovací údaje pro úložiště dat a výpočetní prostředky v [trezoru klíčů Azure](../key-vault/key-vault-overview.md). Azure Data Factory načte přihlašovací údaje při provádění aktivity, která používá úložiště dat nebo výpočetní prostředky.

V současné době tuto funkci podporují všechny typy aktivit s výjimkou vlastní chod. Podrobnosti naleznete konkrétně v části "Vlastnosti propojené služby" v [jednotlivých tématech konektoru.](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="prerequisites"></a>Požadavky

Tato funkce závisí na identitě spravované z datové továrny. Zjistěte, jak funguje ze [spravované identity pro data factory](data-factory-service-identity.md) a ujistěte se, že vaše data factory mají přidružené.

## <a name="steps"></a>Kroky

Chcete-li odkazovat na přihlašovací údaje uložené v úložišti klíčů Azure, musíte:

1. **Načtěte identitu spravovanou z továrny** z kopírováním hodnoty "ID spravovaného objektu identity" generovaného spolu s vaší továrnou. Pokud používáte vývojové ui ADF, id spravovaného objektu identity se zobrazí v okně vytvoření propojené služby Azure Key Vault; můžete také načíst z webu Azure Portal, odkazovat na [načíst identitu spravovanou z datové továrny](data-factory-service-identity.md#retrieve-managed-identity).
2. **Udělte spravované identitě přístup k úložišti klíčů Azure.** V trezoru klíčů -> Zásady přístupu -> Přidat zásady přístupu, vyhledejte tuto spravovanou identitu a **udělte vám** v rozevíracím seznamu Získat oprávnění v tajném oprávnění. Umožňuje této určené továrně přístup k tajným klíčům v trezoru klíčů.
3. **Vytvořte propojenou službu směřující na trezor klíčů Azure.** Odkazovat na [azure key vault propojené služby](#azure-key-vault-linked-service).
4. **Vytvořte propojenou službu úložiště dat, ve které se odkazuje na odpovídající tajný klíč uložený v trezoru klíčů.** Viz [referenční tajný klíč uložený v trezoru klíčů](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Propojená služba Azure Key Vault

Pro propojenou službu Azure Key Vault jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **AzureKeyVault**. | Ano |
| základní adresa Url | Zadejte adresu URL trezoru klíčů Azure. | Ano |

**Použití vývojového ui:**

Vyberte **Možnost Připojení** -> **propojených služeb** -> **Nová**. V nové propojené službě vyhledejte a vyberte "Azure Key Vault":

![Hledání trezoru klíčů Azure](media/store-credentials-in-key-vault/search-akv.png)

Vyberte zřízený trezor klíčů Azure, kde jsou uloženy vaše přihlašovací údaje. Můžete provést **test připojení,** abyste se ujistili, že vaše připojení AKV je platné. 

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

Při konfiguraci pole v propojené službě odkazující na tajný klíč trezoru klíčů jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type pole musí být nastavena na: **AzureKeyVaultSecret**. | Ano |
| tajný_název | Název tajného klíče v azure key vault. | Ano |
| secretVersion | Verze tajného klíče v azure key vault.<br/>Pokud není zadán, vždy používá nejnovější verzi tajného klíče.<br/>Pokud je zadán, pak se drží dané verze.| Ne |
| store | Odkazuje na propojené služby Azure Key Vault, které slouží k ukládání přihlašovacích údajů. | Ano |

**Použití vývojového ui:**

Vyberte **Azure Key Vault** pro tajná pole při vytváření připojení k úložišti dat nebo výpočetním prostředkům. Vyberte zřízenou službu Azure Key Vault Linked Service a zadejte **tajný název**. Volitelně můžete také poskytnout tajnou verzi. 

>[!TIP]
>Pro konektory pomocí připojovacího řetězce v propojené službě, jako je SQL Server, úložiště objektů Blob atd., můžete buď uložit pouze tajné pole, například heslo v AKV, nebo uložit celý připojovací řetězec v AKV. Obě možnosti najdete v ui.

![Konfigurace tajného klíče Azure Key Vault](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Příklad JSON: (viz část "heslo")**

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
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
