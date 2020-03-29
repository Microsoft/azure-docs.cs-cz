---
title: Ochrana před hrozbami pro Azure Key Vault
description: Tento článek vysvětluje, jak nastavit pokročilou ochranu před hrozbami pro Azure Key Vault v Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914802"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Ochrana před hrozbami pro Azure Key Vault (preview)

Pokročilá ochrana před hrozbami pro Azure Key Vault poskytuje další vrstvu inteligence zabezpečení. Tento nástroj detekuje potenciálně škodlivé pokusy o přístup nebo zneužití účtů trezoru klíčů. Pomocí nativní pokročilé ochrany před hrozbami v Azure Security Center můžete řešit hrozby, aniž byste byli odborníkem na zabezpečení a aniž byste se naučili další systémy monitorování zabezpečení.

Když Security Center detekuje anomální aktivity, zobrazí výstrahy. Také e-maily správce předplatného s podrobnostmi o podezřelé aktivity a doporučení, jak prozkoumat a napravit identifikované hrozby.

## <a name="configuring-threat-protection-from-security-center"></a>Konfigurace ochrany před hrozbami z Centra zabezpečení

Ve výchozím nastavení je rozšířená ochrana před hrozbami povolena pro všechny účty trezoru klíčů, když se přihlásíte k odběru standardní cenové úrovně Centra zabezpečení. Další informace naleznete v [tématu Ceny](security-center-pricing.md).

Povolení nebo zakázání ochrany pro konkrétní předplatné:

1. V levém podokně v Centru zabezpečení vyberte **Nastavení cenového &**.

1. Vyberte předplatné s účty úložiště, pro které chcete povolit nebo zakázat ochranu před hrozbami.

1. Vyberte **Cenová úroveň**.

1. Ve skupině **Vybrat cenovou úroveň podle typu prostředku** vyhledejte řádek **Trezory klíčů** a vyberte **Povoleno** nebo **Zakázáno**.

    [![Povolení nebo zakázání rozšířené ochrany před hrozbami pro trezor klíčů v Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Vyberte **Uložit**.


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak povolit a zakázat pokročilou ochranu před hrozbami pro Azure Key Vault. 

Související materiály naleznete v následujících článcích:

- [Ochrana před hrozbami v Azure Security Center](threat-protection.md)-- Tento článek popisuje zdroje výstrah zabezpečení v Azure Security Center.
- [Výstrahy zabezpečení trezoru klíčů](alerts-reference.md#alerts-azurekv)– část referenční tabulky v referenční tabulce v úložišti klíčů pro všechny výstrahy Centra zabezpečení Azure