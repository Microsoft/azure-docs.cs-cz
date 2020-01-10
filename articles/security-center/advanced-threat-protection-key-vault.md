---
title: Nastavení rozšířené ochrany před internetovými útoky pro Azure Key Vault | Microsoft Docs
description: Tento článek vysvětluje, jak nastavit rozšířenou ochranu před internetovými útoky pro Azure Key Vault v Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 2375d8ee92d9c04c287b7fca793fcdc236e1e8f4
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720034"
---
# <a name="set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Nastavení rozšířené ochrany před internetovými útoky pro Azure Key Vault (Preview)

Rozšířená ochrana před internetovými útoky pro Azure Key Vault poskytuje další vrstvu funkce Security Intelligence. Tento nástroj detekuje potenciálně nebezpečné pokusy o přístup k účtům Key Vault a jejich zneužití. Pomocí nativní rozšířené ochrany před internetovými útoky v Azure Security Center můžete řešit hrozby, aniž by se jedná o odborníka na zabezpečení, a bez učení dalších systémů monitorování zabezpečení.

Když Security Center detekuje aktivitu neobvyklé, zobrazí výstrahy. Také pošle e-mailem správce předplatného s podrobnostmi o podezřelé aktivitě a doporučeních k tomu, jak zjistit a opravit zjištěné hrozby.

> [!NOTE]
> Rozšířená ochrana před internetovými útoky pro Azure Key Vault je aktuálně dostupná jenom v Severní Amerikach oblastech.

## <a name="set-up-advanced-threat-protection-from-azure-security-center"></a>Nastavení rozšířené ochrany před internetovými útoky z Azure Security Center

Ve výchozím nastavení je rozšířená ochrana před internetovými útoky povolená pro všechny účty Key Vault, když se přihlásíte k odběru Security Center úrovně Standard. Další informace najdete v tématu [ceny](security-center-pricing.md).

Pokud chcete povolit nebo zakázat ochranu určitého předplatného, postupujte podle těchto kroků.

1. V levém podokně Security Center vyberte **cenové & nastavení**.
1. Vyberte předplatné s účty úložiště, pro které chcete povolit nebo zakázat ochranu před hrozbami.
1. Vyberte **Cenová úroveň**.
1. V poli **Vybrat cenovou úroveň podle typu prostředku** Najděte řádek **trezory klíčů** a vyberte **povoleno** nebo **zakázáno**.

    [![povolení nebo zakázání rozšířené ochrany před internetovými útoky pro Key Vault v Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Vyberte **Uložit**.


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak povolit a zakázat rozšířenou ochranu před internetovými útoky pro Azure Key Vault. 

Další související materiály najdete v následujícím článku:

- [Detekce hrozeb pro vrstvy služeb Azure v Security Center](security-center-alerts-service-layer.md): Tento článek popisuje výstrahy týkající se rozšířené ochrany před internetovými útoky pro Azure Key Vault.
