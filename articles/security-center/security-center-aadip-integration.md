---
title: Připojení ochrany identit Azure Active Directory do Azure Security Center | Microsoft Docs
description: Zjistěte, jak Azure Security Center umožňuje integraci s Azure Active Directory Identity Protection.
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: b15508acea92c165cba85e39ab907fa524088164
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774178"
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Připojení ochrany identit Azure Active Directory do Azure Security Center
Tento dokument vám pomůže nakonfigurovat integraci mezi službou ochrany identit Azure Active Directory (AD) a Azure Security Center.

## <a name="why-connect-azure-ad-identity-protection"></a>Proč připojovat Azure AD Identity Protection?
[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) pomáhá zjišťovat potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci. Když se připojí, budete moci zobrazit výstrahy služby Azure AD Identity Protection v Security Center. Tato integrace umožňuje zobrazit, korelaci a prozkoumejte všechny výstrahy zabezpečení související s hybridní cloudové úlohy ve službě Security Center. 

## <a name="how-do-i-configure-this-integration"></a>Konfigurování této integrace
Pokud už vaše organizace používá Azure AD Identity Protection, postupujte podle pokynů níže nakonfigurujte integraci:

1. Otevřete řídicí panel **Security Center**.
2. V levém podokně klikněte na tlačítko **řešení zabezpečení**. Security Center automaticky zjistí, zda je povoleno Azure AD Identity Protection ve vaší organizaci.

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. Klikněte na tlačítko **CONNECT**.
4. V **integrovat Azure AD Identity Protection** , posuňte se dolů a vyberte odpovídajícího pracovního prostoru:

    ![Pracovní prostor](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. Klikněte na **Připojit**.

Po dokončení této konfigurace, zobrazí se v Azure AD Identity Protection řešení **řešení zabezpečení** v části **připojené řešení**. 

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Centru zabezpečení Azure AD Identity Protection. Další informace o službě Security Center najdete v následujících článcích:

* [Propojení Microsoft Advanced Threat Analytics a Azure Security Center](security-center-ata-integration.md)
* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro skupiny prostředků a předplatná Azure.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reakce na výstrahy zabezpečení.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
- [Zabezpečení dat v Azure Security Center](security-center-data-security.md) -další způsob správy a zabezpečení ve službě Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější informace zabezpečení Azure a informace.


