---
title: Připojování Microsoft Advanced Threat Analytics k Azure Security Center | Microsoft Docs
description: Zjistěte, jak Azure Security Center umožňuje integraci s Microsoft Advanced Threat Analytics.
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: ''
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: yurid
ms.openlocfilehash: fb32fc55288229fb1d09dce98b6092d38c10abf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Připojování Microsoft Advanced Threat Analytics k Azure Security Center
Tento dokument vám pomůže nakonfigurovat integraci mezi službou Microsoft Advanced Threat Analytics a Azure Security Center.

## <a name="why-add-advanced-threat-analytics-data"></a>Proč přidat data Advanced Threat Analytics?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) je místní platforma, která pomáhá zjišťovat chování podezřelé uživatele. Když se připojí, budete moci zobrazit podezřelé akce, které detekuje ATA ve službě Security Center. Tato integrace umožňuje zobrazit, korelaci a prozkoumejte všechny výstrahy zabezpečení související s hybridní cloudové úlohy ve službě Security Center. 

## <a name="how-do-i-configure-this-integration"></a>Konfigurování této integrace
Za předpokladu, že už máte instalace ATA, a funguje správně místně, postupujte podle těchto kroků nakonfigurujete Tato integrace:

1. Přihlaste se k ATA Center a přístup k portálu ATA.
2. Klikněte na tlačítko **Syslog server** v levém podokně.

    ![Syslog server](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. V **koncový bod serveru Syslog** pole, zadejte 127.0.0.7 (Tato adresa musí být) a zadejte 5114 na portu (doporučeno). Číslo portu je doporučení, by měly fungovat libovolný jedinečný port. Nechte všemi dalšími možnostmi, a klikněte na **Uložit**.
4. Klikněte na tlačítko **oznámení** v levém podokně a povolte všechna oznámení Syslog (doporučeno), jak je znázorněno na následujícím obrázku:

    ![Oznámení](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Klikněte na **Uložit**.
6. Otevřete řídicí panel **Security Center**.
7. V levém podokně klikněte na tlačítko **řešení zabezpečení**.
8. V části **Advanced Threat Analytics**, klikněte na tlačítko **přidat**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. Přejít na poslední krok a klikněte na tlačítko **stažení agenta**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. V **přidat nový počítač mimo Azure** vyberte pracovní prostor.

    ![Mimo službu Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. V **přímé agenta** stránky, stáhněte si příslušnou agenta Windows a poznamenejte **ID pracovního prostoru** a **primární klíč**.

    ![Přímé agenta](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. Nainstalujte tohoto agenta v ATA Center. Během instalace, je nutné vybrat možnost **připojit agenta k analýze protokolů Azure**a zadejte *ID pracovního prostoru*, a *primární klíč* na požádání.


Jakmile dokončíte instalaci, dokončení integrace a bude moci zobrazit nové výstrahy Security Center v odeslaných z ATA **vyhledávání** výsledek. Řešení se zobrazí v **řešení zabezpečení** v části **připojené řešení**. 

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak připojit Microsoft ATA k Security Center. Další informace o službě Security Center najdete v následujících článcích:

* [Propojení Azure Active Directory Identity Protection a Azure Security Center](security-center-aadip-integration.md)
* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro skupiny prostředků a předplatná Azure.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reakce na výstrahy zabezpečení.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
- [Zabezpečení dat v Azure Security Center](security-center-data-security.md) -další způsob správy a zabezpečení ve službě Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější informace zabezpečení Azure a informace.


