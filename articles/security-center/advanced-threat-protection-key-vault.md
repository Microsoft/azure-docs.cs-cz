---
title: Ochrana před hrozbami pro Azure Key Vault
description: Tento článek vysvětluje, jak nastavit rozšířenou ochranu před internetovými útoky pro Azure Key Vault v Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914802"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Ochrana před hrozbami pro Azure Key Vault (Preview)

Rozšířená ochrana před internetovými útoky pro Azure Key Vault poskytuje další vrstvu funkce Security Intelligence. Tento nástroj detekuje potenciálně nebezpečné pokusy o přístup k účtům Key Vault a jejich zneužití. Pomocí nativní rozšířené ochrany před internetovými útoky v Azure Security Center můžete řešit hrozby, aniž by se jedná o odborníka na zabezpečení, a bez učení dalších systémů monitorování zabezpečení.

Když Security Center detekuje aktivitu neobvyklé, zobrazí výstrahy. Také pošle e-mailem správce předplatného s podrobnostmi o podezřelé aktivitě a doporučeních k tomu, jak zjistit a opravit zjištěné hrozby.

## <a name="configuring-threat-protection-from-security-center"></a>Konfigurace ochrany před hrozbami z Security Center

Ve výchozím nastavení je rozšířená ochrana před internetovými útoky povolená pro všechny vaše Key Vault účty, když se přihlásíte k odběru standardní cenové úrovně Security Center. Další informace najdete v tématu [ceny](security-center-pricing.md).

Povolení nebo zakázání ochrany určitého předplatného:

1. V levém podokně Security Center vyberte **cenové & nastavení**.

1. Vyberte předplatné s účty úložiště, pro které chcete povolit nebo zakázat ochranu před hrozbami.

1. Vyberte **Cenová úroveň**.

1. V poli **Vybrat cenovou úroveň podle typu prostředku** Najděte řádek **trezory klíčů** a vyberte **povoleno** nebo **zakázáno**.

    [![povolení nebo zakázání rozšířené ochrany před internetovými útoky pro Key Vault v Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Vyberte **Save** (Uložit).


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak povolit a zakázat rozšířenou ochranu před internetovými útoky pro Azure Key Vault. 

Související materiály najdete v následujících článcích:

- [Ochrana před hrozbami v Azure Security Center](threat-protection.md)– Tento článek popisuje zdroje výstrah zabezpečení v Azure Security Center.
- [Key Vault výstrahy zabezpečení](alerts-reference.md#alerts-azurekv)– oddíl Key Vault referenční tabulky pro všechny Azure Security Center výstrahy