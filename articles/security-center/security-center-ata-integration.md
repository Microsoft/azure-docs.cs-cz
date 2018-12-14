---
title: Propojení Microsoft Advanced Threat Analytics a Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak Azure Security Center umožňuje integrací s Microsoft Advanced Threat Analytics.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2018
ms.author: rkarlin
ms.openlocfilehash: 49d6e2ded2ad4b2e5bb0ee30c323b1f459422437
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338392"
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Propojení Microsoft Advanced Threat Analytics a Azure Security Center
Tento dokument vám umožní nakonfigurovat integraci Microsoft Advanced Threat Analytics a Azure Security Center.

## <a name="why-add-advanced-threat-analytics-data"></a>Proč přidat Advanced Threat Analytics data?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) je místní platforma, která pomáhá odhalit podezřelé uživatele chování. Při připojení, budete moct zobrazit podezřelé akce zjištěná službou ATA ve službě Security Center. Tato integrace umožňuje zobrazení, korelovat a prošetřování výstrahy zabezpečení související s vašimi hybridními cloudovými úlohami ve službě Security Center.

## <a name="how-do-i-configure-this-integration"></a>Jak nakonfigurovat tuto integraci?
Za předpokladu, že už máte instalace ATA, a funguje správně v místním, postupujte podle těchto kroků a nakonfigurujte tato integrační:

1. Přihlaste se k ATA Center a přístup k portálu ATA.
2. Klikněte na tlačítko **Syslog server** v levém podokně.

    ![Syslog server](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. V **koncový bod serveru Syslog** pole a zadejte 127.0.0.7 (musí být tato adresa), zadejte 5114 na portu (doporučeno). Číslo portu je doporučení, by měla fungovat jakýkoli jedinečný port. Všechny další možnosti, a klikněte na opustit **Uložit**.
4. Klikněte na tlačítko **oznámení** v levém podokně a povolte všechna oznámení Syslog (doporučeno), jak je znázorněno na následujícím obrázku:

    ![Oznámení](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Klikněte na **Uložit**.
6. Otevřete řídicí panel **Security Center**.
7. V levém podokně klikněte na tlačítko **řešení zabezpečení**.
8. V části **Advanced Threat Analytics**, klikněte na tlačítko **přidat**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)

9. Přejít na poslední krok a klikněte na tlačítko **stáhnout agenta**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. V **přidat nový počítač mimo Azure** stránky, vyberte pracovní prostor.

    ![Mimo službu Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. V **přímý Agent** stránce, stáhněte si odpovídajícího agenta služby Windows a dělat poznámky o **ID pracovního prostoru** a **primární klíč**.

    ![Přímý agent](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. Instalace tohoto agenta ve službě ATA Center. Během instalace, je nutné vybrat možnost **připojit agenta k Azure Log Analytics**a poskytují *ID pracovního prostoru*, a *primární klíč* na požádání.


Po dokončení instalace, integrace se dokončí a budete moci zobrazit nové výstrahy odeslané z ATA ke službě Security Center v **hledání** výsledek. Řešení se zobrazí v **řešení zabezpečení** stránce v části **připojená řešení**.

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit ke službě Security Center Microsoft ATA. Další informace o službě Security Center najdete v následujících článcích:

* [Propojení Azure Active Directory Identity Protection a Azure Security Center](security-center-aadip-integration.md)
* [Nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reagovat na výstrahy zabezpečení.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
- [Zabezpečení dat ve službě Azure Security Center](security-center-data-security.md) – zjistěte, jak data správy a ochrany ve službě Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější zprávy zabezpečení Azure a informace.
