---
title: Použití aktualizací systému v Azure Security Center | Dokumenty společnosti Microsoft
description: Tento dokument ukazuje, jak implementovat doporučení Azure Security Center **použít aktualizace systému** a **restartovat po aktualizacích systému**.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 3f27753b0775f44cbdf9d4c478a19e423b8e1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604551"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Použití systémových aktualizací v Azure Security Center
Azure Security Center každodenně kontroluje virtuální počítače s Windows a Linuxem z hlediska chybějících aktualizací operačního systému. Také denně načítá seznam dostupných aktualizací zabezpečení a kritických aktualizací ze služby Windows Update nebo WSUS (Windows Server Update Services) – podle toho, která služba je na počítači s Windows nakonfigurovaná. Security Center také kontroluje nejnovější aktualizace u systémů Linux. Pokud na vašem virtuálním nebo fyzickém počítači aktualizace systému chybí, Security Center doporučí, abyste si ji nainstalovali.

## <a name="implement-the-recommendation"></a>Provést doporučení
Aktualizace systému Apply se zobrazí jako doporučení v Centru zabezpečení. Pokud ve vašem virtuálním počítači nebo počítači chybí aktualizace systému, toto doporučení se zobrazí v části **Doporučení** a v části **Výpočetní výkon**.  Výběrem doporučení se otevře řídicí panel **Použít aktualizace systému.**

V tomto příkladu použijeme **compute**.

1. V hlavní nabídce Centra zabezpečení vyberte **Vypočítat.**

   ![Vybrat výpočetní výkon][1]

2. V části **Compute**vyberte **Chybějící aktualizace systému**. Otevře se řídicí panel **Použít aktualizace systému.**

   ![Panel Apply system updates][2]

   Horní část řídicího panelu poskytuje:

    - Celkový počet virtuálních počítačů se systémem Windows a Linux a počítačů, které postrádají aktualizace systému.
    - Celkový počet důležitých aktualizací, které ve vašich virtuálních počítačích a počítačích chybí.
    - Celkový počet aktualizací zabezpečení chybí ve vašich virtuálních počítačích a počítačích.

   V dolní části řídicího panelu jsou uvedeny všechny chybějící aktualizace ve virtuálních počítačích a počítačích a závažnost chybějící aktualizace.  Seznam obsahuje:

    - NÁZEV: Název chybějící aktualizace.
    - Ne. Virtuálnípočítače & počítače: Celkový počet virtuálních počítačů a počítačů, které chybí tato aktualizace.
    - STATE (Stav): Aktuální stav doporučení:

      - Otevřeno: Doporučení ještě nebylo vyřešeno.
      - Probíhá: Doporučení se aktuálně na tyto prostředky používá a není třeba provádět žádnou akci.
      - Vyřešeno: Doporučení už je dokončené. (Pokud byl problém vyřešen, položka je vyšedlá.)

    - SEVERITY (Závažnost): Popisuje závažnost tohoto konkrétního doporučení:

      - Vysoká: Ohrožení zabezpečení existuje u významného prostředku (aplikace, virtuální počítač nebo skupina zabezpečení sítě) a vyžaduje pozornost.
      - Střední: Nekritické nebo další kroky jsou potřebné k dokončení procesu nebo odstranění ohrožení.
      - Low (Nízká): Ohrožení zabezpečení by se mělo řešit, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení nejsou doporučení s nízkou závažností uváděny, ale pokud je chcete zobrazit, je možné je vyfiltrovat.)

3. Vyberte chybějící aktualizaci v seznamu, chcete-li zobrazit podrobnosti.

   ![Chybí aktualizace zabezpečení][3]

4. Na horním pásu karet vyberte ikonu **Hledat.**  Vyhledávací dotaz azure monitoru se otevře filtrovaný do počítačů, které aktualizují.

   ![Hledání protokolů Azure Monitor][4]

5. Další informace získáte výběrem počítače ze seznamu. Otevře se další výsledek hledání s informacemi filtrovanými pouze pro daný počítač.

    ![Hledání protokolů Azure Monitor][5]

## <a name="next-steps"></a>Další kroky
Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Monitorování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak sledovat stav prostředků Azure.
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – zjistěte, jak sledovat stav partnerských řešení.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Najděte blogové příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
