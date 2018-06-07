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
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: jingwang
ms.openlocfilehash: f12c3a2a1193adc08c408292a4e0eca2e2458085
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620662"
---
# <a name="store-credential-in-azure-key-vault"></a>Uložení přihlašovacích údajů v Azure Key Vault

Můžete uložit přihlašovací údaje pro úložiště dat a výpočtů v [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory načte přihlašovací údaje při provádění aktivity, která používá úložiště dat/výpočetní.

V současné době tuto funkci podporovat všechny typy aktivit s výjimkou vlastní aktivity. Pro konfiguraci konektoru konkrétně, zkontrolujte v části "vazbu vlastnosti služby" v [každého tématu konektor](copy-activity-overview.md#supported-data-stores-and-formats) podrobnosti.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [dokumentace pro objekt pro vytváření dat version1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>Požadavky

Tato funkce závisí na identitu služby data factory. Zjistěte, jak to funguje z [identita služby Data factory](data-factory-service-identity.md) a zajistěte, aby objekt pro vytváření dat mají přidružený jeden.

>[!TIP]
>V Azure Key Vault, když vytvoříte tajný klíč **put celou hodnotu vlastnosti tajný, že ADF propojená služba požádá o (např připojovací řetězce, hesla nebo služby hlavní klíč nebo atd.)**. Například pro Azure Storage propojené služby, přidat `DefaultEndpointsProtocol=http;AccountName=myAccount;AccountKey=myKey;` jako tajný klíč službou AZURE a pak odkaz v poli "connectionString" z ADF; propojené služby Dynamics, put `myPassword` jako tajný klíč službou AZURE, pak odkazovat v poli "paassword" z ADF. Naleznete každý konektor nebo výpočetní článku na podrobnosti o podporovaných vlastností.

## <a name="steps"></a>Kroky

Chcete-li přihlašovací údaje uložené v Azure Key Vault, budete muset:

1. **Načtení identita služby data factory** hodnotu "Služba IDENTITY ID aplikace" generované společně s vaší objekt pro vytváření. Pokud používáte ADF vytváření uživatelského rozhraní, ID identity služby se zobrazí v okně vytvoření propojené služby Azure Key Vault; Můžete také načíst ho z portálu Azure odkazovat na [načíst identitu služby data factory](data-factory-service-identity.md#retrieve-service-identity).
2. **Udělte přístup identity služby Azure Key Vault.** V trezoru klíčů -> zásady -> přístup přidat nový -> hledání tuto aplikaci služby identity ID udělit **získat** oprávnění v rozevírací nabídce tajný oprávnění. To umožňuje toto určený objekt pro vytváření pro přístup k tajný klíč v trezoru klíčů.
3. **Vytvoření propojené služby odkazující na Azure Key Vault.** Odkazovat na [propojená služba Azure Key Vault](#azure-key-vault-linked-service).
4. **Vytvořte propojenou službu úložiště dat, ve které odkaz odpovídající tajného klíče uložené v trezoru.** Odkazovat na [tajný klíč odkaz uloženého v trezoru klíčů](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Služba Azure Key Vault propojené

Pro Azure Key Vault propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **AzureKeyVault**. | Ano |
| BaseUrl | Zadejte adresu URL Azure Key Vault. | Ano |

**Používání vytváření uživatelského rozhraní:**

Klikněte na tlačítko **připojení** -> **propojené služby** -> **+ nový** -> vyhledejte "Azure Key Vault":

![Hledání službou AZURE](media/store-credentials-in-key-vault/search-akv.png)

Vyberte zřízené Azure Key Vault kde jsou uložené přihlašovací údaje. Můžete provést **Test připojení** zajistit vaší službou AZURE připojení je neplatný. 

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

## <a name="reference-secret-stored-in-key-vault"></a>Tajný klíč odkaz uloženého v trezoru klíčů

Když konfigurujete pole v propojené službě odkazující na tajný klíč trezoru klíčů, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu pole musí být nastavena na: **AzureKeyVaultSecret**. | Ano |
| secretName | Název tajný klíč v azure trezoru klíčů. | Ano |
| secretVersion | Verze tajný klíč v azure trezoru klíčů.<br/>Pokud není zadaný, vždy používá nejnovější verze tajný klíč.<br/>-Li zadána, pak se přilepí na danou verzi.| Ne |
| úložiště | Odkazuje na služby Azure Key Vault propojené, který použijete k uložení pověření. | Ano |

**Používání vytváření uživatelského rozhraní:**

Vyberte **Azure Key Vault** tajný polí při vytváření připojení k úložišti dat nebo výpočetní. Vyberte zřízené Azure Key Vault propojené služby a zadejte **tajný název**. Volitelně můžete zadat verzi tajného klíče také. 

![Konfigurace službou AZURE tajný klíč](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Příklad JSON: (viz část "password")**

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
