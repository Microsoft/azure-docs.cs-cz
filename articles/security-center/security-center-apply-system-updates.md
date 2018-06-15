---
title: Použít aktualizace systému v Azure Security Center | Microsoft Docs
description: Tento dokument ukazuje, jak implementovat Azure Security Center doporučení ** použít aktualizace systému ** a ** po systému aktualizace ** restartuje.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 9f7924f3f0975dc32fdf5b8e1b89a1fb8e9b7d57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866426"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Použít aktualizace systému v Azure Security Center
Azure Security Center monitoruje denní Windows a Linux virtuální počítače (VM) a počítače pro chybějící aktualizace operačního systému. Security Center načte seznam dostupných zabezpečení a důležité aktualizace ze služby Windows Update nebo Windows Server Update Services (WSUS), podle toho, která je nakonfigurovaná služba počítači se systémem Windows. Security Center také zkontroluje nejnovější aktualizace v systémech Linux. Pokud virtuální počítač nebo počítač chybí aktualizace systému, bude Security Center doporučujeme použít aktualizace systému.

## <a name="implement-the-recommendation"></a>Implementace doporučení
Použití systému se aktualizace zobrazí jako doporučení ve službě Security Center. Pokud virtuální počítač nebo počítač chybí aktualizace systému, toto doporučení se zobrazí v části **doporučení** a v části **výpočetní**.  Vyberete doporučení, otevře se **aktualizace systému** řídicího panelu.

V tomto příkladu použijeme **výpočetní**.

1. Vyberte **výpočetní** v hlavní nabídce Security Center.

   ![Vyberte výpočetní][1]

2. V části **výpočetní**, vyberte **chybějící aktualizace systému**. **Aktualizace systému** otevře řídicí panel.

   ![Použít aktualizace systému řídicí panel][2]

   Horní části řídicí panel poskytuje:

    - Celkový počet Windows a virtuální počítače s Linuxem a počítače s chybějícími aktualizacemi systému.
    - Celkový počet kritické aktualizace chybí napříč virtuálních počítačů a počítačů.
    - Celkový počet aktualizací zabezpečení chybí napříč virtuálních počítačů a počítačů.

  Dolní části řídicího panelu zobrazí všechny chybějící aktualizace napříč virtuálních počítačů a počítačů a závažnost chybějící aktualizace.  Seznam obsahuje:

    - Název: Název chybějící aktualizace.
    - NE. Virtuální počítače a počítače: Celkový počet virtuálních počítačů a počítačů, které chybí této aktualizace.
    - Stav: Aktuální stav doporučení:

      - Otevřete: Doporučení dosud nebylo řešeno.
      - V průběhu: Doporučení se aktuálně na tyto prostředky a není třeba žádné akce.
      - Přeložit: Doporučení byla již dokončena. (Pokud byl problém vyřešen, položka je vyšedlá.)

    - ZÁVAŽNOST: Popisuje závažnost tohoto konkrétního doporučení:

      - Vysoká: Ohrožení zabezpečení existuje u významného prostředku (aplikace, virtuální počítač nebo skupinu zabezpečení sítě) a vyžaduje pozornost.
      - Střední: Nekritické nebo další kroky jsou nutné k dokončení procesu nebo odstranění ohrožení.
      - Nízká: Ohrožení zabezpečení mělo by se řešit, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení nejsou doporučení s nízkou závažností uváděny, ale pokud je chcete zobrazit, je možné je vyfiltrovat.)

3. Chybějící aktualizace vyberte v seznamu zobrazíte podrobnosti.

   ![Chybějící aktualizace zabezpečení][3]

4. Vyberte **vyhledávání** ikona na pásu karet nejvyšší.  Vyhledávací dotaz analýzy protokolů otevře filtrované počítačům chybí aktualizace.

   ![Hledání analýzy protokolů][4]

5. Vyberte počítač ze seznamu pro další informace. Další výsledek hledání otevře s informacemi, které jsou filtrovány pouze pro tento počítač.

    ![Hledání analýzy protokolů][5]

## <a name="reboot-after-system-updates"></a>Restartovat po aktualizacích systému
1. Vraťte se na **doporučení** okno. Nový záznam vygenerovalo po použití aktualizací systému, nazývá **restartovat po aktualizacích systému**. Tato položka umožňuje vědět, že je potřeba restartovat virtuální počítač pro dokončení procesu použití aktualizace systému.

   ![Restartovat po aktualizacích systému][6]
2. Vyberte **restartovat po aktualizacích systému**. Tím se otevře **restart čeká na dokončení aktualizací systému** procesu aktualizací okno se seznamem virtuálních počítačů, které potřebujete k dokončení použít systém restartovat.

   ![Čeká se na restartování][7]

Restartujte virtuální počítač z Azure do dokončení procesu.

## <a name="next-steps"></a>Další kroky
Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png
