---
title: Ukládat přihlašovací údaje v Azure Key Vault | Microsoft Docs
description: Zjistěte, jak ukládat přihlašovací údaje pro úložiště dat používá v Azure trezoru klíčů, který Azure Data Factory můžete automaticky načíst za běhu.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jingwang
ms.openlocfilehash: 9a71a455ac4f406695edf722bc83604539eccaa9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="store-credential-in-azure-key-vault"></a>Uložení přihlašovacích údajů v Azure Key Vault

Můžete uložit přihlašovací údaje pro úložiště dat a výpočtů v [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory načte přihlašovací údaje při provádění aktivity, která používá úložiště dat/výpočetní.

V současné době tuto funkci podporovat všechny typy aktivit s výjimkou vlastní aktivity. Pro konfiguraci konektoru konkrétně, zkontrolujte v části "vazbu vlastnosti služby" v [každého tématu konektor](copy-activity-overview.md#supported-data-stores-and-formats) podrobnosti.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [dokumentace pro objekt pro vytváření dat version1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>Požadavky

Tato funkce závisí na identitu služby data factory. Zjistěte, jak to funguje z [identita služby Data factory](data-factory-service-identity.md) a zajistěte, aby objekt pro vytváření dat mají přidružený jeden.

## <a name="steps"></a>Kroky

Chcete-li přihlašovací údaje uložené v Azure Key Vault, budete muset:

1. **[Načtení identita služby data factory](data-factory-service-identity.md#retrieve-service-identity)**  hodnotu "Služba IDENTITY ID aplikace" generované společně s vaší objekt pro vytváření.
2. **Udělte přístup identity služby Azure Key Vault.** V trezoru klíčů -> zásady -> přístup přidat nový -> hledání tuto aplikaci služby identity ID udělit **získat** oprávnění v rozevírací nabídce tajný oprávnění. To umožňuje toto určený objekt pro vytváření pro přístup k tajný klíč v trezoru klíčů.
3. **Vytvoření propojené služby odkazující na Azure Key Vault.** Odkazovat na [propojená služba Azure Key Vault](#azure-key-vault-linked-service).
4. **Vytvořte propojenou službu úložiště dat, ve které odkaz odpovídající tajného klíče uložené v trezoru.** Odkazovat na [tajný klíč odkaz uloženého v trezoru klíčů](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Služba Azure Key Vault propojené

Pro Azure Key Vault propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **AzureKeyVault**. | Ano |
| BaseUrl | Zadejte adresu URL Azure Key Vault. | Ano |

**Příklad:**

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

## <a name="reference-secret-stored-in-key-vault"></a>Tajný klíč odkaz uloženého v trezoru klíčů

Když konfigurujete pole v propojené službě odkazující na tajný klíč trezoru klíčů, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu pole musí být nastavena na: **AzureKeyVaultSecret**. | Ano |
| secretName | Název tajný klíč v azure trezoru klíčů. | Ano |
| secretVersion | Verze tajný klíč v azure trezoru klíčů.<br/>Pokud není zadaný, vždy používá nejnovější verze tajný klíč.<br/>-Li zadána, pak se přilepí na danou verzi.| Ne |
| obchod | Odkazuje na služby Azure Key Vault propojené, který použijete k uložení pověření. | Ano |

**Příklad: (viz část "password")**

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
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).