---
title: Jak nastavit rozšířenou ochranu před internetovými útoky pro Azure Key Vault | Microsoft Docs
description: Tento článek vysvětluje, jak nastavit rozšířenou ochranu před internetovými útoky pro Azure Key Vault v Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: b28f03f0997b6bb2c494c35cee9695a478906c47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521875"
---
# <a name="how-to-set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Nastavení rozšířené ochrany před internetovými útoky pro Azure Key Vault (Preview)

Rozšířená ochrana před internetovými útoky pro Azure Key Vault poskytuje další vrstvu funkce Security Intelligence. Tento nástroj detekuje potenciálně nebezpečné pokusy o přístup k účtům Key Vault a jejich zneužití. Pomocí nativní rozšířené ochrany před internetovými útoky v Security Center můžete řešit hrozby, aniž by se jedná o odborníka na zabezpečení, a bez učení dalších systémů monitorování zabezpečení.

Když Security Center detekuje aktivitu neobvyklé, zobrazí výstrahy. Také pošle e-mailem správce předplatného s podrobnostmi o podezřelé aktivitě a doporučeních k tomu, jak zjistit a opravit zjištěné hrozby. 

> [!NOTE]
> Rozšířená ochrana před internetovými útoky pro Azure Key Vault je v tuto chvíli dostupná jenom v Severní Amerikach oblastech.

## <a name="to-set-up-advanced-threat-protection-from-azure-security-center"></a>Nastavení rozšířené ochrany před internetovými útoky z Azure Security Center

Ve výchozím nastavení je rozšířená ochrana před internetovými útoky povolená pro všechny vaše Key Vault účty, když se přihlásíte k odběru úrovně Standard Security Center (viz [ceny](security-center-pricing.md)). 

Povolení nebo zakázání ochrany určitého předplatného:

1. Na bočním panelu Security Center klikněte na **ceny & nastavení**.
1. Vyberte předplatné s účty úložiště, pro které chcete povolit nebo zakázat ochranu před hrozbami.
1. Klikněte na **cenová úroveň**.
1. V poli **Vybrat cenovou úroveň podle typu prostředku** Najděte řádek trezory klíčů a klikněte na **povoleno** nebo **zakázáno**.
    [![povolení nebo zakázání rozšířené ochrany před internetovými útoky pro Key Vault v Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Klikněte na **Uložit**.


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak povolit a zakázat rozšířenou ochranu před internetovými útoky pro Azure Key Vault. 

Další související materiály najdete v následujícím článku:

- [Detekce hrozeb pro vrstvy služeb Azure v Security Center](security-center-alerts-service-layer.md) – Tento článek popisuje výstrahy týkající se rozšířené ochrany před internetovými útoky pro Azure Key Vault