---
title: Azure Defender pro Key Vault – výhody a funkce
description: Přečtěte si o výhodách a funkcích Azure Defenderu pro Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 06818e443568918e2ee87bbfbec81836ea85648b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936411"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Seznámení s Azure Defenderem pro Key Vault

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné kódy, jako jsou certifikáty, připojovací řetězce a hesla. 

Povolit **Azure Defender pro Key Vault** pro Azure – nativní a rozšířenou ochranu před internetovými útoky pro Azure Key Vault, která poskytuje další vrstvu Security Intelligence. 

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|**Azure Defender pro Key Vault** se účtuje tak, jak je znázorněno na [stránce s cenami](security-center-pricing.md) .|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Jaké jsou výhody Azure Defenderu pro Key Vault?

Azure Defender detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Key Vault nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby bez nutnosti provádět odborníky na zabezpečení a bez nutnosti spravovat systémy monitorování zabezpečení třetích stran.  

Když dojde k neobvyklé aktivitám, Azure Defender zobrazí výstrahy a případně je pošle prostřednictvím e-mailu relevantním členům vaší organizace. Tyto výstrahy obsahují podrobnosti o podezřelé aktivitě a doporučení ohledně toho, jak tyto hrozby prozkoumat a opravit. 

## <a name="azure-defender-for-key-vault-alerts"></a>Azure Defender pro výstrahy Key Vault
Když obdržíte výstrahu od Azure Defenderu pro Key Vault, doporučujeme, abyste si vyšetřili a odpověděli na výstrahu, jak je popsáno v tématu [reakce na Azure Defender pro Key Vault](defender-for-key-vault-usage.md). Azure Defender pro Key Vault chrání aplikace a přihlašovací údaje, takže i v případě, že jste obeznámeni s aplikací nebo uživatelem, který výstrahu aktivoval, je důležité kontrolovat situaci, kdy se všechna upozornění spustila.

Výstrahy se zobrazí na stránce **zabezpečení** Key Vault, na řídicím panelu Azure Defender a na stránce s výstrahami Security Center.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Stránka zabezpečení Azure Key Vault":::

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Defenderu pro Key Vault.

Související materiály najdete v následujících článcích: 

- [Key Vault výstrahy zabezpečení](alerts-reference.md#alerts-azurekv)– oddíl Key Vault referenční tabulky pro všechny Azure Security Center výstrahy
- [Export výstrah do SIEM](continuous-export.md)
- [Potlačit výstrahy z Azure Defenderu](alerts-suppression-rules.md)