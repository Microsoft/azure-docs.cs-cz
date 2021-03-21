---
title: Azure Defender pro Key Vault – výhody a funkce
description: Přečtěte si o výhodách a funkcích Azure Defenderu pro Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: f127a24bec5567a3868ae77cb8a52f0af2a19603
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100674"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Seznámení s Azure Defenderem pro Key Vault

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné kódy, jako jsou certifikáty, připojovací řetězce a hesla. 

Povolit **Azure Defender pro Key Vault** pro Azure – nativní a rozšířenou ochranu před internetovými útoky pro Azure Key Vault, která poskytuje další vrstvu Security Intelligence. 

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Obecná dostupnost (GA)|
|Stanov|**Azure Defender pro Key Vault** se účtuje tak, jak je znázorněno na [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/) .|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Jaké jsou výhody Azure Defenderu pro Key Vault?

Azure Defender detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Key Vault nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby bez nutnosti provádět odborníky na zabezpečení a bez nutnosti spravovat systémy monitorování zabezpečení třetích stran.  

Když dojde k neobvyklé aktivitám, Azure Defender zobrazí výstrahy a případně je pošle prostřednictvím e-mailu relevantním členům vaší organizace. Tyto výstrahy obsahují podrobnosti o podezřelé aktivitě a doporučení ohledně toho, jak tyto hrozby prozkoumat a opravit. 

## <a name="azure-defender-for-key-vault-alerts"></a>Azure Defender pro výstrahy Key Vault
Když obdržíte výstrahu od Azure Defenderu pro Key Vault, doporučujeme, abyste si vyšetřili a odpověděli na výstrahu, jak je popsáno v tématu [reakce na Azure Defender pro Key Vault](defender-for-key-vault-usage.md). Azure Defender pro Key Vault chrání aplikace a přihlašovací údaje, takže i v případě, že jste obeznámeni s aplikací nebo uživatelem, který výstrahu aktivoval, je důležité kontrolovat situaci, kdy se všechna upozornění spustila.

Výstrahy se zobrazí na stránce **zabezpečení** Key Vault, na řídicím panelu Azure Defender a na stránce s výstrahami Security Center.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Stránka zabezpečení Azure Key Vault":::


> [!TIP]
> Pomocí pokynů v tématu [ověřování Azure Key Vault detekce hrozeb v Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)můžete simulovat Azure Defender pro Key Vault výstrahy.


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Defenderu pro Key Vault.

Související materiály najdete v následujících článcích: 

- [Key Vault výstrahy zabezpečení](alerts-reference.md#alerts-azurekv)– oddíl Key Vault referenční tabulky pro všechny Azure Security Center výstrahy
- [Průběžný export Security Center dat](continuous-export.md)
- [Potlačit výstrahy z Azure Defenderu](alerts-suppression-rules.md)