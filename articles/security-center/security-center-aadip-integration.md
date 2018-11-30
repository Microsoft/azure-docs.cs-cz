---
title: Připojení služby Azure Active Directory Identity Protection ke službě Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak Azure Security Center umožňuje integrací s Azure Active Directory Identity Protection.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2018
ms.author: rkarlin
ms.openlocfilehash: 2ed5791b352ee4b19f707e52bc6f8f78e1f0e5b5
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635301"
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Připojení služby Azure Active Directory Identity Protection ke službě Azure Security Center
Tento dokument vám umožní nakonfigurovat integraci Azure Active Directory (AD) Identity Protection a Azure Security Center.

## <a name="why-connect-azure-ad-identity-protection"></a>Proč připojovat aplikaci Azure AD Identity Protection?
[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) pomáhá zjišťovat potenciální ohrožení zabezpečení dopadem na identity vaší organizace. Při připojení, budete moci zobrazit výstrahy služby Azure AD Identity Protection ve službě Security Center. Tato integrace umožňuje zobrazení, korelovat a prošetřování výstrahy zabezpečení související s vašimi hybridními cloudovými úlohami ve službě Security Center.

## <a name="how-do-i-configure-this-integration"></a>Jak nakonfigurovat tuto integraci?
Pokud už vaše organizace používá Azure AD Identity Protection, postupujte podle pokynů níže nakonfigurujte integraci:

1. Otevřete řídicí panel **Security Center**.
2. V levém podokně klikněte na tlačítko **řešení zabezpečení**. Security Center automaticky zjišťuje, pokud Azure AD Identity Protection je povolená pro vaši organizaci.

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. Klikněte na tlačítko **připojit**.
4. V **integrovat Azure AD Identity Protection** přejděte dolů a vyberte odpovídajícího pracovního prostoru:

    ![pracovní prostor](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. Klikněte na **Připojit**.

Po dokončení této konfigurace řešení Azure AD Identity Protection objeví v **řešení zabezpečení** stránce v části **připojená řešení**.

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit ke službě Security Center služby Azure AD Identity Protection. Další informace o službě Security Center najdete v následujících článcích:

* [Propojení Microsoft Advanced Threat Analytics a Azure Security Center](security-center-ata-integration.md)
* [Nastavení zásad zabezpečení ve službě Azure Security Center](security-center-azure-policy.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reagovat na výstrahy zabezpečení.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
- [Zabezpečení dat ve službě Azure Security Center](security-center-data-security.md) – zjistěte, jak data správy a ochrany ve službě Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější zprávy zabezpečení Azure a informace.
