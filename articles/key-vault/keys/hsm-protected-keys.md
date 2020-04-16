---
title: Jak generovat a přenášet klíče chráněné hsm pro Azure Key Vault - Azure Key Vault | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže naplánovat, generovat a pak přenést vlastní klíče chráněné hsm pro použití s Azure Key Vault. Také známý jako BYOK nebo si přineste vlastní klíč.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429691"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Import klíčů chráněných softwarem HSM do trezoru klíčů

Pro větší jistotu, při použití Trezoru klíčů Azure, můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM), které nikdy opustit hranice modulu hardwarového zabezpečení. Tento scénář se často označuje jako *bring your own key * (použití vlastního klíče) nebo BYOK. Azure Key Vault používá nCipher nShield řady hsmů (FIPS 140-2 Úroveň 2 ověřeno) k ochraně vašich klíčů.

Tato funkce není k dispozici pro Azure China 21Vianet.

> [!NOTE]
> Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](../general/overview.md)  
> Kurz začínáme, který zahrnuje vytvoření trezoru klíčů pro klíče chráněné hsm, najdete v [tématu Co je Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>Podporované moduly hesmazívky nebo je v pouz

Přenos klíčů chráněných moduly hesm do trezoru klíčů je podporován dvěma různými způsoby v závislosti na podporovaných modulech hesm. Pomocí následující tabulky určete, která metoda by měla být použita pro vaše hsmy ke generování a pak přenést vlastní klíče chráněné hsm pro použití s Azure Key Vault. 

|Název dodavatele|Typ dodavatele|Podporované modely modulu hsm|Podporovaná metoda přenosu klíče modulu hesm|
|---|---|---|---|
|nCipher|Výrobce|<ul><li>nRodina stehovací sypy</li></ul>|[Použití starší metody BYOK](hsm-protected-keys-legacy.md)|
|Thales|Výrobce|<ul><li>SafeNet Luna HSM 7 rodina s firmwarem verze 7.3 nebo novější</li></ul>| [Použít novou metodu BYOK (náhled)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix|HSM jako služba|<ul><li>Samoobslužná služba správy klíčů (SDKMS)</li></ul>|[Použít novou metodu BYOK (náhled)](hsm-protected-keys-vendor-agnostic-byok.md)|


## <a name="next-steps"></a>Další kroky

Postupujte podle [osvědčených postupů trezoru klíčů](../general/best-practices.md) a zajistěte zabezpečení, odolnost a monitorování klíčů.
