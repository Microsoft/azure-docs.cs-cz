---
title: Postup generování a přenosu klíčů chráněných HSM pro Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Tento článek vám může pomáhat při plánování, generování a přenosu vlastních klíčů chráněných HSM pro použití s Azure Key Vault. Označuje se také jako BYOK nebo Přineste si vlastní klíč.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 00d2d38801929454110b41be88d133e3af232af7
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425744"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Import klíčů chráněných HSM do Key Vault

Pro zvýšení zabezpečení při použití Azure Key Vault můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM), které nikdy nezanechají hranici HSM. Tento scénář se často označuje jako *Přineste si vlastní klíč*nebo BYOK. Azure Key Vault používá hardwarového nshieldou rodinu HSM (FIPS 140-2 Level 2) k ochraně vašich klíčů.

Tato funkce není pro Azure Čína 21Vianet k dispozici.

> [!NOTE]
> Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](key-vault-overview.md)  
> Úvodní kurz, který zahrnuje vytvoření trezoru klíčů pro klíče chráněné HSM, najdete v tématu [co je Azure Key Vault?](key-vault-overview.md).

## <a name="supported-hsms"></a>Podporované HSM

Přenos klíčů chráněných modulem HSM do Key Vault se podporuje dvěma různými způsoby v závislosti na HSM, kterou používáte. Pomocí následující tabulky určete, která metoda má být použita pro HSM k vygenerování, a pak přeneste vlastní klíče chráněné HSM pro použití s Azure Key Vault. 

|Název dodavatele HSM|Podporované modely HSM|Podporovaná metoda přenosu klíče HSM|
|---|---|---|
|Thales|<ul><li>SafeNet Luna HSM 7 Family s firmwarem verze 7,3 nebo novější</li></ul>| [Použít novou metodu BYOK (Preview)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Podpůrný software nCipher|<ul><li>Hardwarového nShield rodina HSM</li></ul>|[Použít starší metodu BYOK](hsm-protected-keys-legacy.md)|


## <a name="next-steps"></a>Další kroky

Dodržujte [Key Vault osvědčené postupy](key-vault-best-practices.md) , abyste zajistili zabezpečení, odolnost a monitorování vašich klíčů.
