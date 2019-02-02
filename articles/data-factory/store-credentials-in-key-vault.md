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
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: ff070adbda2a36261ca24eb0cc993ca22eada1c7
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55661237"
---
# <a name="store-credential-in-azure-key-vault"></a>Store přihlašovacích údajů ve službě Azure Key Vault

Můžete ukládat přihlašovací údaje pro úložiště dat a výpočetní prostředí v [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory načte přihlašovací údaje při provádění aktivity, která využívá úložiště dat/výpočetní.

V současné době tuto funkci podporovat všechny typy aktivit s výjimkou vlastní aktivity. Konfigurace konektoru konkrétně, najdete v části "vlastnostem propojených služeb" [každého tématu konektor](copy-activity-overview.md#supported-data-stores-and-formats) podrobnosti.

## <a name="prerequisites"></a>Požadavky

Tato funkce využívá identita služeb datové továrny. Zjistěte, jak to funguje z [identita služeb datové továrny](data-factory-service-identity.md) a ujistěte se, že svou datovou továrnu mít některý z přidružené.

## <a name="steps"></a>Kroky

Chcete-li odkazovat přihlašovací údaje uložené ve službě Azure Key Vault, budete muset:

1. **Načíst identita služeb datové továrny** tak, že zkopírujete hodnotu "ID aplikace IDENTITY služby" generované spolu se svým objektem pro vytváření. Pokud používáte ADF vytváření uživatelského rozhraní, v okně vytvoření propojené služby Azure Key Vault; se zobrazí ID identity služby Můžete také načíst ji z webu Azure portal najdete [načíst identita služeb datové továrny](data-factory-service-identity.md#retrieve-service-identity).
2. **Udělte přístup identit do služby Azure Key Vault.** V trezoru klíčů -> zásady -> přístup přidat nový -> vyhledávání ID této aplikace identity služby udělit **získat** oprávnění v rozevírací nabídce oprávnění tajného klíče. To umožňuje tento určený objekt pro vytváření pro přístup k tajným kódem v trezoru klíčů.
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
