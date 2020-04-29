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
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 58cf3358a9e908070ce9003d05dd0b576b1d2d3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429691"
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
|Podpůrný software nCipher|Výrobce|<ul><li>Hardwarového nShield rodina HSM</li></ul>|[Použít starší metodu BYOK](hsm-protected-keys-legacy.md)|
|Thales|Výrobce|<ul><li>SafeNet Luna HSM 7 Family s firmwarem verze 7,3 nebo novější</li></ul>| [Použít novou metodu BYOK (Preview)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix|HSM jako služba|<ul><li>Služba správy klíčů (SDKMS) pro samoobslužné naobranu</li></ul>|[Použít novou metodu BYOK (Preview)](hsm-protected-keys-vendor-agnostic-byok.md)|


## <a name="next-steps"></a>Další kroky

Dodržujte [Key Vault osvědčené postupy](../general/best-practices.md) , abyste zajistili zabezpečení, odolnost a monitorování vašich klíčů.
