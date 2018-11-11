---
title: Použít aktualizace systému ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje, jak implementovat doporučení Azure Security Center **nainstalovat aktualizace systému** a **restartovat po aktualizacích systému**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7b5f4415351ad5b51b874e5dc17349160f65e9ef
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240371"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Použít aktualizace systému ve službě Azure Security Center
Azure Security Center monitoruje denní Windows a Linuxem a virtuální počítače (VM) pro chybějící aktualizace operačního systému. Security Center načte seznam dostupných zabezpečení a kritických aktualizací ze služby Windows Update nebo Windows Server Update Services (WSUS), podle toho, která služba je nakonfigurována na počítači Windows. Security Center také vyhledává nejnovější aktualizace v systémech Linux. Pokud virtuální počítač nebo počítači chybí aktualizace systému, Security Center vám doporučí použít aktualizace systému.

## <a name="implement-the-recommendation"></a>Implementace doporučení
Použití systému aktualizace se zobrazí jako doporučení ve službě Security Center. Pokud virtuální počítač nebo počítači chybí aktualizace systému, toto doporučení se zobrazí v části **doporučení** a v části **Compute**.  Vyberete doporučení, otevře **nainstalovat aktualizace systému** řídicího panelu.

V tomto příkladu budeme používat **Compute**.

1. Vyberte **Compute** hlavní nabídce služby Security Center.

   ![Zvolte Compute][1]

2. V části **Compute**vyberte **chybějící aktualizace systému**. **Nainstalovat aktualizace systému** otevře se řídicí panel.

   ![Použít řídicí panel aktualizace systému][2]

   Horní části řídicího panelu poskytuje:

    - Celkový počet Windows a virtuální počítače s Linuxem a počítače s chybějícími aktualizacemi systému.
    - Celkový počet důležitých aktualizací chybí ve virtuálních počítačích a počítačích.
    - Celkový počet aktualizací zabezpečení mezi virtuální počítače a počítačů.

  Dolní části řídicího panelu se zobrazí všechny chybějící aktualizace ve vašich virtuálních počítačů a počítačů a závažnost chybějící aktualizace.  Seznam obsahuje:

    - Název: Název chybějící aktualizace.
    - NE. Virtuálních počítačů a počítačů: Celkový počet virtuálních počítačů a počítačů, které chybí daná aktualizace.
    - Stav: Aktuální stav doporučení:

      - Otevřít: Doporučení dosud nebylo řešeno.
      - Probíhá: Doporučení se právě zavádí na tyto prostředky a nevyžaduje žádné akce se.
      - Vyřešeno: Doporučení už je dokončené. (Pokud byl problém vyřešen, položka je vyšedlá.)

    - ZÁVAŽNOST: Popisuje závažnost tohoto konkrétního doporučení:

      - Vysoká: Ohrožení zabezpečení existuje u významného prostředku (aplikace, virtuálního počítače nebo skupiny zabezpečení sítě) a vyžaduje pozornost.
      - Střední: Nekritické nebo další kroky jsou potřebné k dokončení procesu nebo odstranění ohrožení.
      - Nízká: Chybu mělo by se řešit, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení nejsou doporučení s nízkou závažností uváděny, ale pokud je chcete zobrazit, je možné je vyfiltrovat.)

3. Vyberte chybějící aktualizace v seznamu zobrazíte podrobnosti.

   ![Chybějící aktualizace zabezpečení][3]

4. Vyberte **hledání** ikona na horním pásu karet.  Filtrované počítačům chybí aktualizace se otevře vyhledávací dotaz Log Analytics.

   ![Hledání log Analytics][4]

5. Vyberte počítač ze seznamu pro další informace. Další výsledek vyhledávání se otevře s informacemi filtrovat pouze pro daný počítač.

    ![Hledání log Analytics][5]

## <a name="reboot-after-system-updates"></a>Restartovat po aktualizacích systému
1. Vraťte se **doporučení** okno. Po použití aktualizací systému, nazývá se vygeneroval nový záznam **restartovat po aktualizacích systému**. Tato položka vám umožňuje vědět, že je potřeba restartovat virtuální počítač k dokončení procesu instalace aktualizací systému.

   ![Restartovat po aktualizacích systému][6]
2. Vyberte **restartovat po aktualizacích systému**. Tím se otevře **čeká na dokončení aktualizace systému** okno zobrazení seznamu virtuálních počítačů, které je třeba restartovat, aby mohla dokončit použít systém procesu aktualizací.

   ![Čeká na restartování][7]

Restartujte virtuální počítač z Azure na dokončení procesu.

## <a name="next-steps"></a>Další postup
Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – přečtěte si blog příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png
