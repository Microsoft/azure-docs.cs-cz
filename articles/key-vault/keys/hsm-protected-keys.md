---
title: Postup generování a přenosu klíčů chráněných HSM pro Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Tento článek vám může pomáhat při plánování, generování a přenosu vlastních klíčů chráněných HSM pro použití s Azure Key Vault. Označuje se také jako BYOK nebo Přineste si vlastní klíč.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 011ef67e5dbbf5b391e8bdaad20a42688022a0a9
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2020
ms.locfileid: "84296761"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Import klíčů chráněných modulem HSM do služby Key Vault

Pro zvýšení zabezpečení při použití Azure Key Vault můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM), které nikdy nezanechají hranici HSM. Tento scénář se často označuje jako *bring your own key * (použití vlastního klíče) nebo BYOK. Azure Key Vault používá hardwarového nshieldou rodinu HSM (FIPS 140-2 Level 2) k ochraně vašich klíčů.

Tato funkce není pro Azure Čína 21Vianet k dispozici.

> [!NOTE]
> Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../general/overview.md)  
> Úvodní kurz, který zahrnuje vytvoření trezoru klíčů pro klíče chráněné HSM, najdete v tématu [co je Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>Podporované HSM

Přenos klíčů chráněných modulem HSM do Key Vault se podporuje dvěma různými způsoby v závislosti na HSM, kterou používáte. Pomocí následující tabulky určete, která metoda má být použita pro HSM k vygenerování, a pak přeneste vlastní klíče chráněné HSM pro použití s Azure Key Vault. 

|Název dodavatele|Typ dodavatele|Podporované modely HSM|Podporovaná metoda přenosu klíče HSM|
|---|---|---|---|
|[Podpůrný software nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Výrobců<br/>HSM jako služba|<ul><li>Hardwarového nShield rodina HSM</li><li>Hardwarového nShield jako služba</ul>|**Metoda 1:** [podpůrný software nCipher BYOK](hsm-protected-keys-ncipher.md) (se silným ověřením pro import klíče a ověření HSM)<br/>**Metoda 2:** [použití nové metody BYOK](hsm-protected-keys-byok.md) |
|Thales|Výrobce|<ul><li>SafeNet Luna HSM 7 Family s firmwarem verze 7,3 nebo novější</li></ul>| [Použít novou metodu BYOK](hsm-protected-keys-byok.md)|
|Fortanix|Výrobců<br/>HSM jako služba|<ul><li>Služba správy klíčů (SDKMS) pro samoobslužné naobranu</li><li>Equinix SmartKey</li></ul>|[Použít novou metodu BYOK](hsm-protected-keys-byok.md)|
|PERC|Výrobce|Všechny LiquidSecurity HSM s<ul><li>Firmware verze 2.0.4 nebo novější</li><li>Firmware verze 3,2 nebo novější</li></ul>|[Použít novou metodu BYOK](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Enterprise Key Management System)|Několik značek HSM a modelů, včetně<ul><li>Podpůrný software nCipher</li><li>Thales</li><li>Utimaco</li></ul>Podrobnosti najdete v tématu [Cryptomathic web](https://www.cryptomathic.com/azurebyok) .|[Použít novou metodu BYOK](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>Další kroky

* Dodržujte [Key Vault osvědčené postupy](../general/best-practices.md) , abyste zajistili zabezpečení, odolnost a monitorování vašich klíčů.
* Úplný popis nové metody BYOK najdete v tématu [BYOK Specification](https://docs.microsoft.com/azure/key-vault/keys/byok-specification) .
