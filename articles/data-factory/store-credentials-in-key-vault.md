---
title: Store přihlašovacích údajů ve službě Azure Key Vault | Dokumentace Microsoftu
description: Zjistěte, jak ukládat přihlašovací údaje pro úložiště dat v Azure key vault, který Azure Data Factory můžete automaticky načíst za běhu.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: jingwang
ms.openlocfilehash: fcd3af6c000debb8da6200205a9aa2ae61feac58
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675411"
---
# <a name="store-credential-in-azure-key-vault"></a>Store přihlašovacích údajů ve službě Azure Key Vault

Můžete ukládat přihlašovací údaje pro úložiště dat a výpočetní prostředí v [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory načte přihlašovací údaje při provádění aktivity, která využívá úložiště dat/výpočetní.

V současné době tuto funkci podporovat všechny typy aktivit s výjimkou vlastní aktivity. Konfigurace konektoru konkrétně, najdete v části "vlastnostem propojených služeb" [každého tématu konektor](copy-activity-overview.md#supported-data-stores-and-formats) podrobnosti.

## <a name="prerequisites"></a>Požadavky

Tato funkce spoléhá na objekt pro vytváření spravované identity data. Zjistěte, jak to funguje z [spravovaných identit pro službu Data factory](data-factory-service-identity.md) a ujistěte se, že svou datovou továrnu mít některý z přidružené.

## <a name="steps"></a>Kroky

Chcete-li odkazovat přihlašovací údaje uložené ve službě Azure Key Vault, budete muset:

1. **Načíst data factory spravované identity** tak, že zkopírujete hodnotu "ID aplikace IDENTITY služby" generované spolu se svým objektem pro vytváření. Pokud používáte ADF vytváření uživatelského rozhraní, ID aplikace spravovaná identita zobrazí v okně vytvoření propojené služby Azure Key Vault; Můžete také načíst ji z webu Azure portal najdete [identita spravované služby data factory načtení](data-factory-service-identity.md#retrieve-managed-identity).
2. **Udělení přístupu spravovaných identit do služby Azure Key Vault.** V trezoru klíčů -> zásady -> přístup přidat nový -> Toto ID aplikace identity udělit spravované hledání **získat** oprávnění v rozevírací nabídce oprávnění tajného klíče. To umožňuje tento určený objekt pro vytváření pro přístup k tajným kódem v trezoru klíčů.
3. **Vytvoření propojené služby odkazuje na službě Azure Key Vault.** Odkazovat na [propojená služba Azure Key Vault](#azure-key-vault-linked-service).
4. **Vytvořte propojenou službu úložiště dat, uvnitř které odkaz odpovídající tajného klíče uložené v trezoru.** Odkazovat na [odkaz tajného klíče uložené ve službě key vault](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault propojené služby

Pro Azure Key Vault propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **AzureKeyVault**. | Ano |
| baseUrl | Zadejte adresu URL služby Azure Key Vault. | Ano |

**Použití pro vytváření uživatelského rozhraní:**

Klikněte na tlačítko **připojení** -> **propojené služby** -> **+ nová** -> vyhledejte "Azure Key Vault":

![Službou AZURE Search](media/store-credentials-in-key-vault/search-akv.png)

Vyberte zřízené Azure Key Vault ukládat svoje přihlašovací údaje. Můžete provést **Test připojení** Ujistěte se, že vaše AKV spojení není přerušeno. 

![Konfigurace službou AZURE](media/store-credentials-in-key-vault/configure-akv.png)

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

## <a name="reference-secret-stored-in-key-vault"></a>Tajný kód odkazu ukládají ve službě key vault

Při konfiguraci pole v propojené službě odkazující na tajný kód trezoru klíčů, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu pole musí být nastavena: **AzureKeyVaultSecret**. | Ano |
| secretName | Název tajného kódu ve službě azure key vault. | Ano |
| secretVersion | Verze tajného klíče ve službě azure key vault.<br/>Pokud není zadán, vždy používá nejnovější verzi tajného kódu.<br/>Je-li zadán, pak připevní na danou verzi.| Ne |
| úložiště | Odkazuje na službu, který použijete k uložení přihlašovacích údajů propojené služby Azure Key Vault. | Ano |

**Použití pro vytváření uživatelského rozhraní:**

Vyberte **Azure Key Vault** pro skryté pole při vytváření připojení k úložišti dat/výpočetní. Vyberte zřízené Azure Key Vault propojenou službu a poskytnout **název tajného kódu**. Volitelně můžete zadat tajná verze. 

![Konfigurace tajného kódu službou AZURE](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Příklad JSON: (viz oddíl "heslo")**

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
